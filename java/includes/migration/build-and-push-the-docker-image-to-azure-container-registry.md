---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 8af791a3b85f5e64865cee965a37797327db0b0d
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897610"
---
### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Tworzenie obrazu platformy Docker i wypychanie go do usługi Azure Container Registry

Po utworzeniu pliku Dockerfile trzeba będzie utworzyć obraz platformy Docker i opublikować go w usłudze Azure Container Registry.

Jeśli zostało użyte nasze [repozytorium GitHub WildFly Container Quickstart](https://github.com/Azure/wildfly-container-quickstart), proces tworzenia i wypychania obrazu do usługi Azure Container Registry będzie odpowiednikiem wywołania następujących trzech poleceń.

W tych przykładach zmienna środowiskowa `MY_ACR` przechowuje nazwę rejestru kontenerów platformy Azure, a zmienna `MY_APP_NAME` przechowuje nazwę aplikacji internetowej, która ma być używana w usłudze Azure Container Registry.

Tworzenie pliku WAR:

```shell
mvn package
```

Logowanie się do usługi Azure Container Registry:

```shell
az acr login -n ${MY_ACR}
```

Tworzenie i wypychanie obrazu:

```shell
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} -f src/main/docker/Dockerfile .
```

Alternatywnie możesz użyć interfejsu wiersza polecenia platformy Docker, aby najpierw utworzyć i przetestować obraz lokalnie, jak pokazano w poniższych poleceniach. Takie podejście może uprościć testowanie i udoskonalanie obrazu przed początkowym wdrożeniem w usłudze ACR. Jednak wymaga to zainstalowania interfejsu wiersza polecenia platformy Docker i upewnienia się, że jest uruchomiony demon platformy Docker.

Tworzenie obrazu:

```shell
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Uruchamianie obrazu lokalnie:

```shell
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Teraz możesz uzyskać dostęp do swojej aplikacji pod adresem [http://localhost:8080](http://localhost:8080).

Logowanie się do usługi Azure Container Registry:

```shell
az acr login -n ${MY_ACR}
```

Wypychanie obrazu do usługi Azure Container Registry:

```shell
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Aby uzyskać bardziej szczegółowe informacje na temat tworzenia i przechowywania obrazów kontenerów na platformie Azure, zobacz w witrynie Learn moduł [Tworzenie i przechowywanie obrazów kontenerów za pomocą usługi Azure Container Registry](https://docs.microsoft.com/learn/modules/build-and-store-container-images/).
