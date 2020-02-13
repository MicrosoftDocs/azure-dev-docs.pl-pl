---
title: Wdrażanie aplikacji MicroProfile w chmurze za pomocą platformy Docker i Azure
description: Dowiedz się, jak wdrożyć aplikację MicroProfile w chmurze przy użyciu platformy Docker i usługi Azure Container Instances.
services: container-instances;container-retistry
documentationcenter: java
author: brunoborges
ms.author: brborges
ms.date: 11/21/2018
ms.service: container-instances
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 17630393b5c4a86e23c914dc38b0c6e1a6606ab1
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999872"
---
# <a name="deploy-a-microprofile-application-to-the-cloud-with-docker-and-azure"></a>Wdrażanie aplikacji MicroProfile w chmurze za pomocą platformy Docker i Azure

W tym artykule pokazano, jak spakować aplikację [MicroProfile.io] w kontenerze platformy Docker i uruchomić ją w usłudze Azure Container Instances.

> [!NOTE]
>
> Ta procedura działa z dowolną implementacją aplikacji MicroProfile.io, o ile obraz kontenera platformy Docker jest samowykonywalny (tj. zawiera środowisko uruchomieniowe).

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym samouczku wymagane są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz utworzyć [Bezpłatne konto platformy Azure].
* [Interfejs wiersza polecenia platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji [Maven] programu Apache (wersja 3 lub nowsza).
* Klient usługi [Usługa Git].

## <a name="microprofile-hello-azure-sample"></a>Przykładowa aplikacja MicroProfile Hello Azure

W tym artykule będziemy używać przykładowej aplikacji [MicroProfile Hello Azure](https://github.com/azure-samples/microprofile-hello-azure):

### <a name="clone-build-and-run-locally"></a>Klonowanie, kompilowanie i uruchamianie lokalnie

```bash
$ git clone https://github.com/Azure-Samples/microprofile-hello-azure.git
$ mvn clean package
...
[INFO] BUILD SUCCESS
...
$ mvn payara-micro:start
...
[2018-07-30T13:34:51.553-0700] [] [INFO] [] [PayaraMicro] [tid: _ThreadID=1 _ThreadName=main] [timeMillis: 1532982891553] [levelValue: 800] Payara Micro  5.182 #badassmicrofish (build 303) ready in 10,304 (ms)
...
```

Aby przetestować aplikację, możesz wywołać narzędzie `curl` lub skorzystać z [przeglądarki](http://localhost:8080/api/hello):

```bash
$ curl http://localhost:8080/api/hello
Hello, Azure!
```

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

Teraz przenieśmy tę aplikację do chmury przy użyciu usług [Azure Container Instances] i [Azure Container Registry].

### <a name="build-a-docker-image"></a>Kompilowanie obrazu platformy Docker

Przykładowy projekt zawiera już plik platformy Docker, którego możesz użyć. Nie musisz jednak instalować platformy Docker, ponieważ w celu skompilowania obrazu w chmurze użyjemy funkcji kompilowania usługi Azure Container Registry.

Aby skompilować obraz i przygotować go do uruchomienia na platformie Azure, wykonaj następujące kroki:

1. Instalowanie i logowanie za pomocą interfejsu wiersza polecenia platformy Azure
1. Tworzenie grupy zasobów platformy Azure
1. Tworzenie usługi Azure Container Registry (ACR)
1. Budowanie obrazu Docker
1. Publikowanie obrazu platformy Docker w utworzonej wcześniej usłudze ACR
1. (Opcjonalnie) Kompilowanie i publikowanie w usłudze ACR za pomocą jednego polecenia


#### <a name="set-up-azure-cli"></a>Konfigurowanie interfejsu wiersza polecenia platformy Azure

Upewnij się, że masz subskrypcję platformy Azure, [zainstalowany interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i że uwierzytelnienie na koncie przebiegło pomyślnie:

```bash
az login
```

#### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

```bash
export ARG=microprofileRG
export ADCL=eastus
az group create --name $ARG --location $ADCL
```

#### <a name="create-an-azure-container-registry-instance"></a>Utworzenie wystąpienia usługi Azure Container Registry

To polecenie spowoduje utworzenie globalnie unikatowego (miejmy nadzieję) rejestru kontenerów przy użyciu nazwy podstawowej z liczbą losową.

```bash
export RANDINT=`date +"%m%d%y$RANDOM"`
export ACR=mydockerrepo$RANDINT
az acr create --name $ACR -g $ARG --sku Basic --admin-enabled
```

#### <a name="build-the-docker-image"></a>Budowanie obrazu Docker

Mimo że można łatwo skompilować obraz platformy Docker lokalnie przy użyciu samej platformy Docker, z kilku powodów warto rozważyć skompilowanie go w chmurze:

1. Nie trzeba instalować platformy Docker lokalnie
1. Jest to znacznie szybsze, ponieważ kompilacja odbywa się w innym miejscu (z wyjątkiem czasu przekazywania kontekstu)
1. Proces w chmurze ma dostęp do szybszego Internetu, dzięki czemu pobieranie jest szybsze
1. Obraz trafia bezpośrednio do usługi Container Registry

Z tych względów skompilujemy obraz przy użyciu funkcji kompilowania usługi [Funkcja kompilowania usługi Azure Container Registry]:

```bash
export IMG_NAME="mympapp:latest"
az acr build -r $ACR -t $IMG_NAME -g $ARG .
...
Build complete
Build ID: aa1 was successful after 1m2.674577892s
```

#### <a name="deploy-docker-image-from-azure-container-registry-acr-into-container-instances-aci"></a>Wdrażanie obrazu platformy Docker z usługi Azure Container Registry (ACR) do usługi Container Instances (ACI)

Teraz, gdy obraz jest dostępny w usłudze ACR, wypchnijmy i utwórzmy wystąpienie kontenera w usłudze ACI. Najpierw jednak musimy się upewnić, że możemy uwierzytelnić się w usłudze ACR:

```bash
export ACR_REPO=`az acr show --name $ACR -g $ARG --query loginServer -o tsv`
export ACR_PASS=`az acr credential show --name $ACR -g $ARG --query "passwords[0].value" -o tsv`
export ACI_INSTANCE=myapp`date +"%m%d%y$RANDOM"`

az container create --resource-group $ARG --name $ACR --image $ACR_REPO/$IMG_NAME --cpu 1 --memory 1 --registry-login-server $ACR_REPO --registry-username $ACR --registry-password $ACR_PASS --dns-name-label $ACI_INSTANCE --ports 8080
```

#### <a name="test-your-deployed-microprofile-application"></a>Testowanie wdrożonej aplikacji MicroProfile

Aplikacja powinna być teraz gotowa i uruchomiona. Aby przetestować ją z poziomu wiersza polecenia, spróbuj wykonać następujące polecenie:

```bash
curl http://$ACI_INSTANCE.$ADCL.azurecontainer.io:8080/api/hello
````

Gratulacje! Aplikacja MicroProfile została pomyślnie skompilowana i wdrożona jako kontener platformy Docker na platformie Microsoft Azure.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat różnych technologii omówionych w tym artykule, zobacz następujące artykuły:

* [Logowanie do platformy Azure z interfejsu wiersza polecenia platformy Azure](/azure/xplat-cli-connect)

<!-- URL List -->

[Funkcja kompilowania usługi Azure Container Registry]: https://docs.microsoft.com/azure/container-registry/container-registry-build-overview
[MicroProfile.io]: https://microprofile.io
[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure for Java Developers]: https://docs.microsoft.com/azure/java/
[Azure portal]: https://portal.azure.com/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Maven]: http://maven.apache.org/
[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->
[Azure Container Instances]: https://docs.microsoft.com/azure/container-instances/
[Azure Container Registry]:  https://docs.microsoft.com/azure/container-registry