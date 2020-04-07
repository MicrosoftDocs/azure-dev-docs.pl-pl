---
title: Wdrażanie aplikacji Spring Boot w usłudze Azure Kubernetes Service
titleSuffix: Azure Kubernetes Service
description: W tym samouczku przedstawiono procedurę wdrażania aplikacji Spring Boot w klastrze Kubernetes na platformie Microsoft Azure.
services: container-service
documentationcenter: java
ms.date: 11/12/2019
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: dedd7a8c30e71b1197781838c1006745884eb67d
ms.sourcegitcommit: 31f6d047f244f1e447faed6d503afcbc529bd28c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80306790"
---
# <a name="deploy-spring-boot-application-to-the-azure-kubernetes-service"></a>Wdrażanie aplikacji Spring Boot w usłudze Azure Kubernetes Service

**[Kubernetes]** i **[Docker]** to rozwiązania open source, które pomagają deweloperom zautomatyzować wdrażanie i skalowanie aplikacji uruchomionych w kontenerach oraz zarządzanie nimi.

Ten samouczek pomoże Ci połączyć te dwie popularne technologie open source w celu opracowania i wdrożenia aplikacji Spring Boot na platformie Microsoft Azure. Mówiąc dokładniej, za pomocą rozwiązania *[Spring Boot]* utworzysz aplikację, platforma *[Kubernetes]* pomoże Ci we wdrożeniu kontenera, a usługa [Azure Kubernetes Service (AKS)] będzie hostować Twoją aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* [Interfejs wiersza polecenia platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji Apache [Maven] (wersja 3).
* Klient usługi [Usługa Git].
* Klient platformy [Docker].
* [Pomocnik poświadczeń platformy Docker usługi ACR](https://github.com/Azure/acr-docker-credential-helper).

> [!NOTE]
>
> Ze względu na wymagania tego samouczka dotyczące wirtualizacji nie można wykonać kroków opisanych w tym artykule na maszynie wirtualnej. Należy użyć komputera fizycznego z włączonymi funkcjami wirtualizacji.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Spring Boot on Docker Getting Started

Poniższa procedura przeprowadzi Cię przez proces kompilowania aplikacji internetowej Spring Boot i testowania jej lokalnie.

1. Otwórz wiersz polecenia i utwórz katalog lokalny, w którym będzie przechowywana aplikacja, a następnie przejdź do tego katalogu, na przykład:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   — lub —
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Sklonuj do katalogu przykładowy projekt [Spring Boot na platformie Docker — wprowadzenie].
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Przejdź do katalogu ukończonego projektu.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Za pomocą narzędzia Maven skompiluj i uruchom przykładową aplikację.
   ```
   mvn package spring-boot:run
   ```

1. Przetestuj aplikację internetową, przechodząc do witryny `http://localhost:8080` lub używając następującego polecenia `curl`:
   ```
   curl http://localhost:8080
   ```

1. Powinien zostać wyświetlony następujący komunikat: **Hello Docker World**

   ![Przeglądanie przykładowej aplikacji lokalnie][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Tworzenie usługi Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure

1. Otwórz wiersz polecenia.

1. Zaloguj się do konta platformy Azure:
   ```azurecli
   az login
   ```

1. Wybierz subskrypcję platformy Azure:
   ```azurecli
   az account set -s <YourSubscriptionID>
   ```

1. Utwórz grupę zasobów na potrzeby zasobów platformy Azure używanych w tym samouczku.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. W grupie zasobów utwórz prywatny rejestr kontenerów platformy Azure. Samouczek wypchnie później przykładową aplikację jako obraz platformy Docker do tego rejestru. Zamień wartość `wingtiptoysregistry` na unikatową nazwę rejestru.
   ```azurecli
   az acr create --resource-group wingtiptoys-kubernetes --location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Wypychanie aplikacji do rejestru kontenerów za pośrednictwem narzędzia Jib

1. Zaloguj się do usługi Azure Container Registry z poziomu interfejsu wiersza polecenia platformy Azure.
   ```azurecli
   # set the default name for Azure Container Registry, otherwise you will need to specify the name in "az acr login"
   az configure --defaults acr=wingtiptoysregistry
   az acr login
   ```

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* w edytorze tekstu.

1. Zaktualizuj kolekcję `<properties>` w pliku *pom.xml* za pomocą nazwy rejestru usługi Azure Container Registry i najnowszej wersji wtyczki [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>2.1.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```
1. Zaktualizuj kolekcję `<plugins>` w pliku *pom.xml* tak, aby element `<plugin>` zawierał wpis dla `jib-maven-plugin`, jak pokazano w poniższym przykładzie. Pamiętaj, że korzystamy z obrazu podstawowego z usługi Microsoft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, który zawiera oficjalnie obsługiwany zestaw JDK dla platformy Azure. Inne obrazy podstawowe usługi MCR z oficjalnie obsługiwanymi zestawami JDK to [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) i [Java SE JDK and Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot i uruchom następujące polecenie, aby skompilować obraz i wypchnąć go do rejestru:

   ```cmd
   mvn compile jib:build
   ```

> [!NOTE]
>
> Ze względów bezpieczeństwa interfejsu wiersza polecenia platformy Azure i usługi Azure Container Registry poświadczenie utworzone za pomocą polecenia `az acr login` jest ważne przez 1 godzinę. Jeśli wystąpi błąd *401 Brak autoryzacji*, możesz jeszcze raz uruchomić polecenie `az acr login -n <your registry name>` w celu ponownego uwierzytelnienia.
>

## <a name="create-a-kubernetes-cluster-on-aks-using-the-azure-cli"></a>Tworzenie klastra Kubernetes w usłudze AKS przy użyciu interfejsu wiersza polecenia platformy Azure

1. Utwórz klaster Kubernetes w usłudze Azure Kubernetes Service. Poniższe polecenie spowoduje utworzenie klastra *Kubernetes* w grupie zasobów *wingtiptoys-kubernetes*. Nazwą klastra będzie *wingtiptoys-akscluster*, a prefiksem DNS — *wingtiptoys-kubernetes*.
   ```azurecli
   az aks create --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster \ 
    --dns-name-prefix=wingtiptoys-kubernetes --generate-ssh-keys
   ```
   Wykonanie tego polecenia może chwilę potrwać.

1. Jeśli używasz usługi Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS), musisz przyznać usłudze Azure Kubernetes Service dostęp do ściągania w usłudze Azure Container Registry. Podczas tworzenia usługi Azure Kubernetes Service platforma Azure tworzy domyślną jednostkę usługi. Uruchom poniższe skrypty w powłoce Bash lub programie PowerShell, aby udzielić usłudze AKS dostępu do usługi ACR. Więcej szczegółów znajdziesz w artykule [Uwierzytelnianie za pomocą usługi Azure Container Registry z poziomu usługi Azure Kubernetes Service].

```bash
   # Get the id of the service principal configured for AKS
   CLIENT_ID=$(az.cmd aks show -g wingtiptoys-kubernetes -n wingtiptoys-akscluster --query "servicePrincipalProfile.clientId" --output tsv)
   
   # Get the ACR registry resource id
   ACR_ID=$(az.cmd acr show -g wingtiptoys-kubernetes -n wingtiptoysregistry --query "id" --output tsv)
   
   # Create role assignment
   az.cmd role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

  — lub —

```PowerShell
   # Get the id of the service principal configured for AKS
   $CLIENT_ID = az aks show -g wingtiptoys-kubernetes -n wingtiptoys-akscluster --query "servicePrincipalProfile.clientId" --output tsv
   
   # Get the ACR registry resource id
   $ACR_ID = az acr show -g wingtiptoys-kubernetes -n wingtiptoysregistry --query "id" --output tsv
   
   # Create role assignment
   az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

1. Przy użyciu interfejsu wiersza polecenia platformy Azure zainstaluj polecenie `kubectl`. W przypadku użytkowników systemu Linux może być konieczne poprzedzenie tego polecenia poleceniem `sudo`, ponieważ wdraża ono interfejs wiersza polecenia platformy Kubernetes w katalogu `/usr/local/bin`.
   ```azurecli
   az aks install-cli
   ```

1. Pobierz informacje o konfiguracji klastra, aby umożliwić zarządzanie klastrem za pomocą interfejsu internetowego platformy Kubernetes i polecenia `kubectl`. 
   ```azurecli
   az aks get-credentials --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Wdrażanie obrazu w klastrze Kubernetes

Ten samouczek wdraża aplikację przy użyciu polecenia `kubectl`, a następnie umożliwia eksplorowanie wdrożenia za pomocą interfejsu internetowego platformy Kubernetes.

### <a name="deploy-with-kubectl"></a>Wdrażanie za pomocą polecenia kubectl

1. Otwórz wiersz polecenia.

1. Uruchom kontener w klastrze Kubernetes, używając polecenia `kubectl run`. Nadaj nazwę usługi aplikacji w klastrze Kubernetes i pełną nazwę obrazu. Przykład:
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   W tym poleceniu:

   * Nazwa kontenera `gs-spring-boot-docker` jest określana bezpośrednio po poleceniu `run`

   * Parametr `--image` określa połączony serwer logowania i nazwę obrazu jako `wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. Uwidocznij klaster Kubernetes zewnętrznie, używając polecenia `kubectl expose`. Określ nazwę usługi, publiczny port TCP służący do uzyskiwania dostępu do aplikacji oraz wewnętrzny port docelowy, na którym nasłuchuje aplikacja. Przykład:
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   W tym poleceniu:

   * Nazwa kontenera `gs-spring-boot-docker` jest określana bezpośrednio po poleceniu `expose deployment`.

   * Parametr `--type` określa, że klaster korzysta z modułu równoważenia obciążenia.

   * Parametr `--port` określa publiczny port TCP 80. Na tym porcie uzyskuje się dostęp do aplikacji.

   * Parametr `--target-port` określa wewnętrzny port TCP 8080. Na tym porcie moduł równoważenia obciążenia przekazuje żądania do aplikacji.

1. Po wdrożeniu aplikacji w klastrze wyślij zapytanie na zewnętrzny adres IP i otwórz go w przeglądarce internetowej:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=default
   ```

   ![Przeglądanie przykładowej aplikacji na platformie Azure][SB02]



### <a name="deploy-with-the-kubernetes-web-interface"></a>Wdrażanie za pomocą interfejsu internetowego platformy Kubernetes

1. Otwórz wiersz polecenia.

1. Otwórz witrynę internetową konfiguracji klastra Kubernetes w domyślnej przeglądarce:
   ```
   az aks browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```

1. Gdy witryna internetowa konfiguracji platformy Kubernetes zostanie otwarta w przeglądarce, wybierz link, aby **wdrożyć konteneryzowaną aplikację**:

   ![Witryna internetowa konfiguracji platformy Kubernetes][KB01]

1. Po wyświetleniu strony **Tworzenie zasobów** określ następujące opcje:

   a. Wybierz pozycję **UTWÓRZ APLIKACJĘ**.

   b. W polu **Nazwa aplikacji** wprowadź nazwę aplikacji Spring Boot, na przykład: „*gs-spring-boot-docker*”.

   d. W polu **Obraz kontenera** wprowadź serwer logowania i obraz kontenera z poprzednich kroków, na przykład: „*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*”.

   d. W polu **Usługa** wybierz pozycję **Zewnętrzna**.

   e. W polach tekstowych **Port** i **Port docelowy** określ port zewnętrzny i wewnętrzny.

   ![Witryna internetowa konfiguracji platformy Kubernetes][KB02]


1. Wybierz pozycję **Wdróż**, aby wdrożyć kontener.

   ![Wdrożenie na platformie Kubernetes][KB05]

1. Po wdrożeniu aplikacji Spring Boot będzie ona widoczna w obszarze **Usługi**.

   ![Usługi Kubernetes][KB06]

1. Wybierając link do **zewnętrznych punktów końcowych**, możesz zobaczyć, że aplikacja Spring Boot działa na platformie Azure.

   ![Usługi Kubernetes][KB07]

   ![Przeglądanie przykładowej aplikacji na platformie Azure][SB02]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Wdrażanie aplikacji Spring Boot w usłudze Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

Aby uzyskać więcej informacji na temat korzystania z platformy Azure przy użyciu języka Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] oraz [Praca z narzędziami Azure DevOps i językiem Java].

Aby uzyskać więcej informacji na temat wdrażania aplikacji Java na platformie Kubernetes za pomocą programu Visual Studio Code, zobacz [Samouczki języka Java — Visual Studio Code].

Aby uzyskać więcej informacji na temat przykładowego projektu aplikacji Spring Boot na platformie Docker, zobacz [Spring Boot na platformie Docker — wprowadzenie].

Poniższe linki zawierają dodatkowe informacje na temat tworzenia aplikacji Spring Boot:

* Aby uzyskać więcej informacji na temat tworzenia prostej aplikacji Spring Boot, zapoznaj się z narzędziem Spring Initializr na stronie https://start.spring.io/.

Poniższe linki zawierają dodatkowe informacje na temat korzystania z rozwiązania Kubernetes na platformie Azure:

* [Wprowadzenie do klastra Kubernetes w usłudze Azure Kubernetes Service](/azure/aks/intro-kubernetes)

Więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Kubernetes znajduje się w podręczniku użytkownika polecenia **kubectl** w witrynie <https://kubernetes.io/docs/user-guide/kubectl/>.

W witrynie internetowej platformy Kubernetes znajduje się kilka artykułów, w których omówiono używanie obrazów w rejestrach prywatnych:

* [Konfigurowanie kont usługi dla zasobników]
* [Przestrzenie nazw]
* [Ściąganie obrazu z rejestru prywatnego]

Aby uzyskać dodatkowe przykłady użycia niestandardowych obrazów platformy Docker z platformą Azure, zobacz [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux].

Aby uzyskać więcej informacji na temat iteracyjnego uruchamiania i debugowania kontenerów bezpośrednio w usłudze Azure Kubernetes Service (AKS) za pomocą usługi Azure Dev Spaces, zobacz [Wprowadzenie do usługi Azure Dev Spaces z językiem Java]

<!-- URL List -->

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure Kubernetes Service (AKS)]: https://azure.microsoft.com/services/kubernetes-service/
[Azure dla deweloperów języka Java]: /azure/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/java/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot na platformie Docker — wprowadzenie]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Konfigurowanie kont usługi dla zasobników]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[Przestrzenie nazw]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Ściąganie obrazu z rejestru prywatnego]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- Newly added -->
[Uwierzytelnianie za pomocą usługi Azure Container Registry z poziomu usługi Azure Kubernetes Service]: /azure/container-registry/container-registry-auth-aks/
[Samouczki języka Java — Visual Studio Code]: https://code.visualstudio.com/docs/java/java-kubernetes/
[Wprowadzenie do usługi Azure Dev Spaces z językiem Java]: https://docs.microsoft.com/azure/dev-spaces/get-started-java
<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-on-kubernetes/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-on-kubernetes/SB02.png

[AR01]: ./media/deploy-spring-boot-java-app-on-kubernetes/AR01.png
[AR02]: ./media/deploy-spring-boot-java-app-on-kubernetes/AR02.png
[AR03]: ./media/deploy-spring-boot-java-app-on-kubernetes/AR03.png
[AR04]: ./media/deploy-spring-boot-java-app-on-kubernetes/AR04.png

[KB01]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB01.png
[KB02]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB02.png
[KB03]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB03.png
[KB04]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB04.png
[KB05]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB05.png
[KB06]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB06.png
[KB07]: ./media/deploy-spring-boot-java-app-on-kubernetes/KB07.png
