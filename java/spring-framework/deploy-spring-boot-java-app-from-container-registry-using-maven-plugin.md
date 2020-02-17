---
title: Wdrażanie aplikacji Spring Boot w usłudze Azure Container Registry
titleSuffix: Azure App Service
description: W tym samouczku przedstawiono procedurę wdrażanie aplikacji Spring Boot z usługi Azure Container Registry na platformie Azure do usługi Azure App Service przy użyciu wtyczki narzędzia Maven.
services: container-registry
documentationcenter: java
ms.date: 12/19/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 729efd44b5b2489462c55c29f669d7bbbde8740c
ms.sourcegitcommit: d9f585bea70b01ba6657a75ea245d8519d4a5aad
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2020
ms.locfileid: "77001160"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>Wdrażanie aplikacji Spring Boot z usługi Azure Container Registry do usługi Azure App Service przy użyciu wtyczki Maven dla usługi Azure Web Apps

W tym artykule opisano sposób wdrożenia przykładowej aplikacji [Spring Boot] w usłudze Azure Container Registry, a następnie wdrożenia aplikacji w usłudze Azure App Services przy użyciu wtyczki Maven dla usługi Azure Web Apps.

> [!NOTE]
> 
> Wtyczka Maven dla usługi Azure Web Apps dla narzędzia [Apache Maven](https://maven.apache.org/) zapewnia bezproblemową integrację usługi Azure App Service z projektami Maven oraz usprawnia deweloperom proces wdrażania aplikacji internetowych w usłudze Azure App Service.
> 
> Wtyczka Maven dla usługi Azure Web Apps jest obecnie dostępna w wersji zapoznawczej. W tej chwili obsługiwana jest tylko funkcja publikowania przy użyciu protokołu FTP. W przyszłości planuje się wprowadzenie dodatkowych funkcji.
> 

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

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>Klonowanie przykładowej aplikacji Spring Boot w aplikacji internetowej platformy Docker

W tej sekcji sklonujesz konteneryzowaną aplikację Spring Boot i przetestujesz ją lokalnie.

1. Otwórz wiersz polecenia lub okno terminalu i utwórz katalog lokalny, w którym będzie przechowywana aplikacja Spring Boot, a następnie przejdź do tego katalogu, na przykład:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   — lub —
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Sklonuj przykładowy projekt [Spring Boot na platformie Docker — wprowadzenie] do utworzonego katalogu, na przykład:
   ```shell
   https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Zmień katalog na ukończony projekt, na przykład:
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. Skompiluj plik JAR przy użyciu narzędzia Maven, na przykład:
   ```shell
   mvn clean package
   ```

1. Po utworzeniu aplikacji internetowej uruchom ją, używając narzędzia Maven, na przykład:
   ```shell
   mvn spring-boot:run
   ```

1. Przetestuj aplikację internetową, przechodząc do niej lokalnie przy użyciu przeglądarki internetowej. Na przykład jeśli dostępne jest narzędzie curl, możesz użyć następującego polecenia:
   ```shell
   curl http://localhost:8080
   ```

1. Powinien zostać wyświetlony następujący komunikat: **Hello Docker World**

   ![Przeglądanie przykładowej aplikacji lokalnie][SB01]

> [!NOTE]
>
> Jeśli używasz platformy Docker lokalnie, może zostać wyświetlony komunikat o błędzie z informacją, że nie można nawiązać połączenia z hostem lokalnym na porcie 2375. W takim przypadku może być konieczne włączenie korzystania z platformy Docker lokalnie bez protokołu TLS. Aby to zrobić, otwórz ustawienia platformy Docker i zaznacz opcję **Uwidaczniaj demona na porcie TCP://localhost:2375 bez protokołu TLS**.
>
> ![Uwidacznianie demona platformy Docker na lokalnym porcie TCP 2375][TL01]

## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

W tej sekcji utworzysz jednostkę usługi platformy Azure, której wtyczka Maven będzie używać podczas wdrażania kontenera na platformie Azure.

1. Otwórz wiersz polecenia.

2. Zaloguj się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
   ```azurecli
   az login
   ```
   Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania.

3. Utwórz jednostkę usługi platformy Azure:
   ```azurecli
   az ad sp create-for-rbac --name <ServicePrincipalName>
   ```
Jeśli nie istnieją żadne parametry uwierzytelniania, jest używane uwierzytelnianie oparte na hasłach z losowo utworzonym hasłem.

4. Platforma Azure odpowiada za pomocą kodu JSON, który wygląda podobnie jak w poniższym przykładzie:
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > Wartości z tej odpowiedzi w formacie JSON będą używane podczas konfigurowania wtyczki Maven w celu wdrożenia kontenera na platformie Azure. `aaaaaaaa`, `uuuuuuuu`, `pppppppp` i `tttttttt` są wartościami zastępczymi, których użyto w tym przykładzie, aby ułatwić mapowanie tych wartości do odpowiednich elementów podczas konfigurowania pliku `settings.xml` narzędzia Maven w następnej sekcji.
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Tworzenie usługi Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure

1. Otwórz wiersz polecenia.

1. Zaloguj się do konta platformy Azure:
   ```azurecli
   az login
   ```

1. Utwórz grupę zasobów dla zasobów platformy Azure, które będą używane w tym artykule:
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   Zamień `wingtiptoysresources` z tego przykładu na unikatową nazwę grupy zasobów.

1. W grupie zasobów aplikacji Spring Boot utwórz prywatny rejestr kontenerów platformy Azure: 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   Zamień `wingtiptoysregistry` z tego przykładu na unikatową nazwę rejestru kontenerów.

1. Pobierz hasło dla rejestru kontenerów:
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Platforma Azure odpowie hasłem, na przykład:
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>Dodawanie rejestru kontenerów platformy Azure i jednostki usługi platformy Azure do ustawień narzędzia Maven

1. Otwórz plik `settings.xml` narzędzia Maven w edytorze tekstów. Ścieżka do tego pliku może być podobna do następujących:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. Dodaj ustawienia dostępu do usługi Azure Container Registry z poprzedniej sekcji tego artykułu do kolekcji `<servers>` w pliku *settings.xml*, na przykład:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   Gdzie:

   |   Element    |                                 Opis                                  |
   |--------------|------------------------------------------------------------------------------|
   |    `<id>`    |         Zawiera nazwę prywatnego rejestru kontenerów platformy Azure.          |
   | `<username>` |         Zawiera nazwę prywatnego rejestru kontenerów platformy Azure.          |
   | `<password>` | Zawiera hasło pobrane w poprzedniej sekcji tego artykułu. |


3. Dodaj ustawienia jednostki usługi platformy Azure z wcześniejszej sekcji tego artykułu do kolekcji `<servers>` w pliku *settings.xml*, na przykład:

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   Gdzie:

   |     Element     |                                                                                   Opis                                                                                   |
   |-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |     `<id>`      |                                Określa unikatową nazwę, której narzędzie Maven używa do wyszukiwania ustawień zabezpieczeń podczas wdrażania aplikacji internetowej na platformie Azure.                                |
   |   `<client>`    |                                                             Zawiera wartość `appId` z jednostki usługi.                                                             |
   |   `<tenant>`    |                                                            Zawiera wartość `tenant` z jednostki usługi.                                                             |
   |     `<key>`     |                                                           Zawiera wartość `password` z jednostki usługi.                                                            |
   | `<environment>` | Definiuje docelowe środowisko chmury platformy Azure, którym w tym przykładzie jest `AZURE`. (Pełna lista środowisk jest dostępna w dokumentacji dotyczącej [Wtyczka Maven dla usługi Azure Web Apps]) |


4. Zapisz i zamknij plik *settings.xml*.

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>Kompilowanie obrazu kontenera platformy Docker i wypychanie go do rejestru kontenerów platformy Azure

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (np. „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* przy użyciu edytora tekstu.

2. Zaktualizuj kolekcję `<properties>` w pliku *pom.xml* przy użyciu wartości serwera logowania dla usługi Azure Container Registry z poprzedniej sekcji tego samouczka, na przykład:

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   Gdzie:

   |           Element           |                                                                       Opis                                                                       |
   |-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `<azure.containerRegistry>` |                                              Określa nazwę prywatnego rejestru kontenerów platformy Azure.                                               |
   |   `<docker.image.prefix>`   | Określa adres URL prywatnego rejestru kontenerów platformy Azure, który tworzy się, dołączając sufiks „.azurecr.io” do nazwy prywatnego rejestru kontenerów. |


3. Sprawdź, czy element `<plugin>` dla wtyczki platformy Docker w pliku *pom.xml* zawiera poprawne właściwości adresu serwera logowania i nazwy rejestru z poprzedniego kroku tego samouczka. Przykład:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   Gdzie:

   |     Element     |                                       Opis                                       |
   |-----------------|-----------------------------------------------------------------------------------------|
   |  `<serverId>`   |  Określa właściwość zawierającą nazwę prywatnego rejestru kontenerów platformy Azure.   |
   | `<registryUrl>` | Określa właściwość zawierającą adres URL prywatnego rejestru kontenerów platformy Azure. |


4. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot i uruchom następujące polecenie, aby ponownie skompilować aplikację i wypchnąć kontener do rejestru kontenerów platformy Azure:

   ```shell
   mvn package docker:build -DpushImage 
   ```

5. OPCJONALNIE: Przejdź do witryny [Azure Portal] i sprawdź, czy w rejestrze kontenerów znajduje się obraz kontenera platformy Docker o nazwie **gs-spring-boot-docker**.

   ![Weryfikacja kontenera w witrynie Azure Portal][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>Dostosowywanie pliku pom.xml przed skompilowaniem i wdrożeniem kontenera na platformie Azure

Otwórz plik `pom.xml` dla aplikacji Spring Boot w edytorze tekstów, a następnie znajdź element `<plugin>` dla wtyczki `azure-webapp-maven-plugin`. Ten element powinien wyglądać podobnie jak w poniższym przykładzie:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

W przypadku wtyczki narzędzia Maven można zmodyfikować kilka wartości. Szczegółowy opis każdego z tych elementów jest dostępny w dokumentacji [Wtyczka Maven dla usługi Azure Web Apps]. Z tego względu warto w tym artykule zwrócić uwagę na pewne wartości:

| Element | Opis |
|---|---|
| `<version>` | Określa wersję [Wtyczka Maven dla usługi Azure Web Apps]. Aby upewnić się, że używasz najnowszej wersji, sprawdź wersję wymienioną w [Centralnym repozytorium Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22). |
| `<authentication>` | Określa informacje o uwierzytelnianiu dla platformy Azure, które w tym przykładzie zawierają element `<serverId>` obejmujący wartość `azure-auth`. Narzędzie Maven używa tej wartości do wyszukiwania wartości jednostki usługi platformy Azure w pliku *settings.xml* narzędzia Maven, które zostały zdefiniowane we wcześniejszej części tego artykułu. |
| `<resourceGroup>` | Określa docelową grupę zasobów, którą w tym przykładzie jest `wingtiptoysresources`. Jeśli grupa zasobów jeszcze nie istnieje, zostanie utworzona podczas wdrożenia. |
| `<appName>` | Określa nazwę docelową aplikacji internetowej. W tym przykładzie nazwą docelową jest `maven-linux-app-${maven.build.timestamp}`. Dołączono sufiks `${maven.build.timestamp}`, aby uniknąć konfliktu. (Znacznik czasu jest opcjonalny — dla nazwy aplikacji można określić dowolny unikatowy ciąg). |
| `<region>` | Określa region docelowy, którym w tym przykładzie jest `westus`. (Pełna lista znajduje się w dokumentacji dotyczącej [Wtyczka Maven dla usługi Azure Web Apps]). |
| `<containerSettings>` | Określa właściwości zawierające nazwę i adres URL kontenera. |
| `<appSettings>` | Określa wszystkie unikatowe ustawienia, których będzie używać narzędzie Maven podczas wdrażania aplikacji internetowej na platformie Azure. W tym przykładzie element `<property>` zawiera parę nazwa/wartość elementów podrzędnych, które określają port dla aplikacji. |

> [!NOTE]
>
> Ustawienia zmiany numeru portu w tym przykładzie są niezbędne tylko w przypadku zmiany portu z wartości domyślnej.
>

1. Jeśli w pliku *pom.xml* wprowadzono jakiekolwiek zmiany, w wierszu polecenia lub w oknie terminalu, które były używane wcześniej, skompiluj ponownie plik JAR przy użyciu narzędzia Maven, na przykład:
   ```shell
   mvn clean package
   ```

1. Wdróż aplikację internetową na platformie Azure przy użyciu narzędzia Maven, na przykład:
   ```shell
   mvn azure-webapp:deploy
   ```

Narzędzie Maven wdroży aplikację internetową na platformie Azure. Jeśli aplikacja internetowa jeszcze nie istnieje, zostanie utworzona.

> [!NOTE]
>
> Jeśli region określony w elemencie `<region>` pliku *pom.xml* nie ma wystarczającej liczby dostępnych serwerów podczas uruchamiania wdrożenia, może zostać wyświetlony komunikat o błędzie, który będzie wyglądać podobnie do poniższego:
>
> ```xml
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> W takim przypadku można określić inny region i ponownie uruchomić polecenie narzędzia Maven w celu wdrożenia aplikacji.
>
>

Po wdrożeniu aplikacji internetowej będzie można zarządzać nią za pośrednictwem witryny [Azure Portal].

* Aplikacja internetowa będzie wyświetlana w usłudze **App Services**:

   ![Aplikacja internetowa wyświetlana w usłudze App Services w witrynie Azure Portal][AP01]

* Natomiast adres URL aplikacji internetowej będzie wyświetlany w sekcji **Omówienie** Twojej aplikacji internetowej:

   ![Określanie adresu URL aplikacji internetowej][AP02]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat różnych technologii omówionych w tym artykule, zobacz następujące artykuły:

* [Wtyczka Maven dla usługi Azure Web Apps]

* [Logowanie do platformy Azure z interfejsu wiersza polecenia platformy Azure](/azure/xplat-cli-connect)

* [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Dokumentacja ustawień narzędzia Maven](https://maven.apache.org/settings.html)

* [Wtyczka Docker dla oprogramowania Maven]

Aby uzyskać więcej informacji na temat korzystania z platformy Azure przy użyciu języka Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] oraz [Praca z narzędziami Azure DevOps i językiem Java].

<!-- URL List -->

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure dla deweloperów języka Java]: /azure/java/
[Azure Portal]: https://portal.azure.com/
[Wtyczka Maven dla usługi Azure Web Apps]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service/containers/tutorial-custom-docker-image
[Docker]: https://www.docker.com/
[Wtyczka Docker dla oprogramowania Maven]: https://github.com/spotify/docker-maven-plugin
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/
[Maven]: https://maven.apache.org/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: https://projects.spring.io/spring-boot/
[Spring Boot na platformie Docker — wprowadzenie]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-sample-spring-boot-app.png
[CR01]: ./media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/browse-azure-portal-docker-container.png
[AP01]: ./media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/web-app-listed-azure-portal.png
[AP02]: ./media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/determine-web-app-url.png
[TL01]: ./media/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin/expose-docker-daemon-tcp-port.png
