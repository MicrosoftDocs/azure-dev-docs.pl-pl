---
title: Wdrażanie aplikacji Spring Boot przy użyciu wtyczki Maven Fabric8
description: W tym samouczku przedstawiono procedurę wdrażania aplikacji Spring Boot na platformie Microsoft Azure przy użyciu wtyczki Fabric8 dla narzędzia Apache Maven.
services: container-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: dd6d926b4d4bb21e16fbb922f2daa29cd9d09301
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999690"
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>Wdrażanie aplikacji Spring Boot przy użyciu wtyczki Maven Fabric8

**[Fabric8]** to rozwiązanie open-source oparte na platformie **[Kubernetes]** , które ułatwia deweloperom tworzenie aplikacji w kontenerach systemu Linux.

Ten samouczek zawiera instrukcje dotyczące używania wtyczki Fabric8 dla narzędzia Maven w celu utworzenia aplikacji i wdrożenia jej na hoście z systemem Linux w usłudze [Azure Container Service (AKS)].

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym samouczku wymagane są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* [Interfejs wiersza polecenia platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji Apache [Maven] (wersja 3).
* Klient usługi [Usługa Git].
* Klient platformy [Docker].

> [!NOTE]
>
> Ze względu na wymagania tego samouczka dotyczące wirtualizacji nie można wykonać kroków opisanych w tym artykule na maszynie wirtualnej. Należy użyć komputera fizycznego z włączonymi funkcjami wirtualizacji.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Spring Boot on Docker Getting Started

Poniższa procedura przeprowadzi Cię przez proces kompilowania aplikacji internetowej Spring Boot i testowania jej lokalnie.

1. Otwórz wiersz polecenia i utwórz katalog lokalny, w którym będzie przechowywana aplikacja, a następnie przejdź do tego katalogu, na przykład:
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   — lub —
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. Sklonuj do katalogu przykładowy projekt [Spring Boot na platformie Docker — Wprowadzenie].
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Zmień katalog na ukończony projekt, na przykład:
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   — lub —
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. Za pomocą narzędzia Maven skompiluj i uruchom przykładową aplikację.
   ```shell
   mvn clean package spring-boot:run
   ```

1. Przetestuj aplikację internetową, przechodząc do witryny http://localhost:8080 lub używając następującego polecenia `curl`:
   ```shell
   curl http://localhost:8080
   ```

   Powinien zostać wyświetlony komunikat **Hello Docker World**.

   ![Przeglądanie przykładowej aplikacji lokalnie][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Kubernetes i tworzenie grupy zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

1. Otwórz wiersz polecenia.

1. Wpisz poniższe polecenie, aby zalogować się do konta platformy Azure:
   ```azurecli
   az login
   ```
   Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania

   Interfejs wiersza polecenia platformy Azure wyświetli listę Twoich kont, na przykład:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Jeśli nie masz jeszcze zainstalowanego interfejsu wiersza polecenia platformy Kubernetes (`kubectl`), możesz go zainstalować przy użyciu interfejsu wiersza polecenia platformy Azure, na przykład:
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > W przypadku użytkowników systemu Linux może być konieczne poprzedzenie tego polecenia poleceniem `sudo`, ponieważ wdraża ono interfejs wiersza polecenia platformy Kubernetes w katalogu `/usr/local/bin`.
   >
   > Jeśli masz już zainstalowane narzędzie `kubectl`, ale Twoja wersja narzędzia `kubectl` jest zbyt stara, podczas próby wykonania czynności opisanych w dalszej części tego artykułu może zostać wyświetlony komunikat o błędzie podobny do poniższego:
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > W takim przypadku konieczne będzie ponowne zainstalowanie narzędzia `kubectl` w celu zaktualizowania wersji.
   >

1. Utwórz grupę zasobów dla zasobów platformy Azure, które będą używane w tym samouczku, na przykład:
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   Gdzie:  
      * *wingtiptoys-kubernetes* jest unikatową nazwą grupy zasobów  
      * *westeurope* jest odpowiednią lokalizacją geograficzną dla Twojej aplikacji  

   Interfejs wiersza polecenia platformy Azure wyświetli wyniki tworzenia grupy zasobów, na przykład:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>Tworzenie klastra Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure

1. Utwórz klaster Kubernetes w nowej grupie zasobów, na przykład:  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   Gdzie:  
      * *wingtiptoys-kubernetes* jest nazwą grupy zasobów z wcześniejszej części tego artykułu  
      * *wingtiptoys-cluster* jest unikatową nazwą klastra Kubernetes
      * *wingtiptoys* jest unikatową nazwą DNS dla aplikacji

   Interfejs wiersza polecenia platformy Azure wyświetli wyniki tworzenia klastra, na przykład:  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. Pobierz poświadczenia dla nowego klastra Kubernetes, na przykład:  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. Sprawdź połączenie za pomocą następującego polecenia:
   ```shell 
   kubectl get nodes
   ```

   Powinna zostać wyświetlona lista węzłów i stanów, jak na poniższym przykładzie:

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>Tworzenie prywatnego rejestru kontenerów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

1. W grupie zasobów utwórz prywatny rejestr kontenerów platformy Azure w celu hostowania obrazu platformy Docker, na przykład:
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `wingtiptoys-kubernetes` | Określa nazwę grupy zasobów z wcześniejszej części tego artykułu. |
   | `wingtiptoysregistry` | Określa unikatową nazwę rejestru prywatnego. |
   | `westeurope` | Określa odpowiednią lokalizację geograficzną dla Twojej aplikacji. |

   Interfejs wiersza polecenia platformy Azure wyświetli wyniki tworzenia rejestru, na przykład:  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

2. Pobierz hasło dla rejestru kontenerów z poziomu interfejsu wiersza polecenia platformy Azure.
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   Interfejs wiersza polecenia platformy Azure wyświetli hasło dla rejestru, na przykład:  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

3. Przejdź do katalogu konfiguracji dla instalacji narzędzia Maven (domyślnie ~/.m2/ lub C:\Users\nazwa_użytkownika\.m2) i otwórz plik *settings.xml* za pomocą edytora tekstów.

4. Dodaj adres URL usługi Azure Container Registry, nazwę użytkownika i hasło do nowej kolekcji `<server>` w pliku *settings.xml*.
   Elementy `id` i `username` są nazwą rejestru. Użyj wartości `password` z poprzedniego polecenia (bez cudzysłowów).

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

5. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „ */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* w edytorze tekstów.

6. Zaktualizuj kolekcję `<properties>` w pliku *pom.xml* za pomocą wartości serwera logowania dla usługi Azure Container Registry.

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

7. Zaktualizuj kolekcję `<plugins>` w pliku *pom.xml* tak, aby element `<plugin>` zawierał adres serwera logowania i nazwę rejestru dla usługi Azure Container Registry.

   ```xml
   <plugin>
     <groupId>com.spotify</groupId>
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

8. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot i uruchom następujące polecenie narzędzia Maven, aby skompilować kontener platformy Docker i wypchnąć obraz do rejestru:

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Narzędzie Maven wyświetli wyniki kompilacji, na przykład:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>Konfigurowanie aplikacji Spring Boot w celu używania wtyczki Maven Fabric8

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład: „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „ */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* przy użyciu edytora tekstów.

1. Zaktualizuj kolekcję `<plugins>` w pliku *pom.xml*, aby dodać wtyczkę Maven Fabric8:

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. Przejdź do głównego katalogu źródłowego aplikacji Spring Boot (na przykład: „*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*” lub „ */home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*”) i utwórz nowy folder o nazwie *fabric8*.

1. W nowym folderze *fabric8* utwórz trzy pliki fragmentów YAML:

   a. Utwórz plik o nazwie **deployment.yml** z następującą zawartością:
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. Utwórz plik o nazwie **secrets.yml** z następującą zawartością:
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   d. Utwórz plik o nazwie **service.yml** z następującą zawartością:
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. Uruchom następujące polecenie narzędzia Maven, aby skompilować plik listy zasobów platformy Kubernetes:

   ```shell
   mvn fabric8:resource
   ```

   To polecenie scala wszystkie pliki yaml zasobów platformy Kubernetes w folderze *src/main/fabric8* do pliku YAML zawierającego listę zasobów platformy Kubernetes, który można zastosować bezpośrednio do klastra Kubernetes lub wyeksportować do elementu chart programu Helm.

   Narzędzie Maven wyświetli wyniki kompilacji, na przykład:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. Uruchom następujące polecenie narzędzia Maven, aby zastosować plik listy zasobów do klastra Kubernetes:

   ```shell
   mvn fabric8:apply
   ```

   Narzędzie Maven wyświetli wyniki kompilacji, na przykład:  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. Po wdrożeniu aplikacji w klastrze wyślij zapytanie względem zewnętrznego adresu IP, używając aplikacji `kubectl`, na przykład:
   ```shell
   kubectl get svc -w
   ```

   Aplikacja `kubectl` wyświetli wewnętrzny i zewnętrzny adres IP, na przykład:

   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```

   Przy użyciu zewnętrznego adresu IP możesz otworzyć aplikację w przeglądarce internetowej.

   ![Przeglądanie przykładowej aplikacji zewnętrznie][SB02]

## <a name="delete-your-kubernetes-cluster"></a>Usuwanie klastra Kubernetes

Gdy klaster Kubernetes nie będzie już potrzebny, możesz usunąć grupę zasobów za pomocą polecenia `az group delete`, co spowoduje usunięcie wszystkich powiązanych zasobów, na przykład:

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Wdrażanie aplikacji Spring Boot w usłudze Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Wdrażanie aplikacji Spring Boot w systemie Linux w usłudze Azure Container Service](deploy-spring-boot-java-app-on-linux.md)
* [Wdrażanie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Aby uzyskać więcej informacji na temat używania platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Praca z narzędziami Azure DevOps i językiem Java].

Aby uzyskać więcej informacji na temat przykładowego projektu aplikacji Spring Boot na platformie Docker, zobacz [Spring Boot na platformie Docker — Wprowadzenie].

Aby uzyskać pomoc dotyczącą rozpoczynania pracy z własnymi aplikacjami Spring Boot, zapoznaj się z narzędziem **Spring Initializr** na stronie <https://start.spring.io/>.

Aby uzyskać więcej informacji na temat rozpoczynania tworzenia prostej aplikacji Spring Boot, zapoznaj się z narzędziem Spring Initializr na stronie <https://start.spring.io/>.

Aby uzyskać dodatkowe przykłady użycia niestandardowych obrazów platformy Docker na platformie Azure, zobacz [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux].

<!-- URL List -->

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure dla deweloperów języka Java]: /azure/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/java/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot na platformie Docker — Wprowadzenie]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-using-fabric8-maven-plugin/SB02.png
