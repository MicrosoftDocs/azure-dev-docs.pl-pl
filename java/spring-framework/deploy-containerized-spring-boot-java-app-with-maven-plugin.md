---
title: Wdrażanie aplikacji Spring Boot na platformie Azure za pomocą narzędzia Maven
description: Dowiedz się, jak używać wtyczki Maven dla usługi Azure Web Apps do wdrażania aplikacji Spring Boot na platformie Azure.
services: app-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 5468016954c562258245958cf7b95d2200dcc5f0
ms.sourcegitcommit: db803eba96ffa73b21b94fcb41439cb9b7a0e3c8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2019
ms.locfileid: "77000327"
---
# <a name="use-maven-for-azure-web-apps-to-deploy-a-containerized-spring-boot-app-to-azure"></a>Korzystanie z oprogramowania Maven dla usługi Azure Web Apps do wdrażania konteneryzowanych aplikacji Spring Boot na platformie Azure

W tym artykule opisano użycie [wtyczki Maven dla usługi Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin) w celu wdrożenia przykładowej aplikacji Spring Boot w kontenerze platformy Docker w usłudze Azure App Services.

> [!NOTE]
> 
> Wtyczka Maven dla usługi Azure Web Apps przeznaczona do narzędzia [Apache Maven](https://maven.apache.org/) zapewnia bezproblemową integrację usługi Azure App Service z projektami Maven oraz ułatwia deweloperom proces wdrażania aplikacji internetowych w usłudze Azure App Service.
> 
> Wtyczka Maven dla usługi Azure Web Apps jest obecnie dostępna jako wersja zapoznawcza. Obecnie obsługiwana jest tylko funkcja publikowania przy użyciu protokołu FTP. W przyszłości planuje się wprowadzenie dodatkowych funkcji.
> 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym samouczku wymagane są następujące elementy:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* [Interfejs wiersza polecenia platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji Apache [Maven] (wersja 3).
* Klient usługi [Usługa Git].
* Klient platformy [Docker].

> [!NOTE]
>
> Ze względu na wymagania dotyczące wirtualizacji w tym samouczku nie można wykonać kroków opisanych w tym artykule na maszynie wirtualnej. Należy użyć komputera fizycznego z włączonymi funkcjami wirtualizacji.
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

1. Sklonuj przykładowy projekt [Spring Boot na platformie Docker — Wprowadzenie] do utworzonego katalogu, na przykład:
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker
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

## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

W tej sekcji utworzysz jednostkę usługi platformy Azure, której wtyczka Maven będzie używać podczas wdrażania kontenera na platformie Azure.

1. Otwórz wiersz polecenia.

2. Zaloguj się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
   ```shell
   az login
   ```
   Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania.

3. Utwórz jednostkę usługi platformy Azure:
   ```shell
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   Gdzie:

   | Parametr  |                    Opis                     |
   |------------|----------------------------------------------------|
   | `uuuuuuuu` | Określa nazwę użytkownika jednostki usługi. |
   | `pppppppp` | Określa hasło jednostki usługi.  |


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

## <a name="configure-maven-to-use-your-azure-service-principal"></a>Konfigurowanie narzędzia Maven do korzystania z jednostki usługi platformy Azure

W tej sekcji użyjesz wartości z jednostki usługi platformy Azure, aby skonfigurować uwierzytelnianie, które zostanie użyte przez narzędzie Maven podczas wdrażania kontenera na platformie Azure.

1. Otwórz plik `settings.xml` narzędzia Maven w edytorze tekstów. Ścieżka do tego pliku może być podobna do następujących:
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

2. Dodaj ustawienia jednostki usługi platformy Azure z poprzedniej sekcji tego samouczka do kolekcji `<servers>` w pliku *settings.xml*, na przykład:

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


3. Zapisz i zamknij plik *settings.xml*.

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>OPCJONALNIE: Wdrażanie lokalnego pliku platformy Docker w usłudze Docker Hub

Jeśli masz konto platformy Docker, możesz utworzyć lokalnie obraz kontenera platformy Docker i wypchnąć go do usługi Docker Hub. W tym celu wykonaj następujące czynności.

1. Otwórz plik `pom.xml` dla aplikacji Spring Boot w edytorze tekstów.

1. Znajdź element podrzędny `<imageName>` elementu `<containerSettings>`.

1. Zaktualizuj wartość `${docker.image.prefix}` przy użyciu nazwy swojego konta platformy Docker:
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. Wybierz jedną z następujących metod wdrażania:

   * Skompiluj lokalnie obraz kontenera za pomocą narzędzia Maven, a następnie użyj platformy Docker, aby wypchnąć kontener do usługi Docker Hub:
      ```shell
      mvn clean package docker:build
      docker push
      ```

   * Jeśli masz zainstalowaną [Wtyczka platformy Docker dla narzędzia Maven], możesz automatycznie skompilować obraz kontenera i wypchnąć go do usługi Docker Hub przy użyciu parametru `-DpushImage`:
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>OPCJONALNIE: Dostosowanie pliku pom.xml przed wdrożeniem kontenera na platformie Azure

Otwórz plik `pom.xml` dla aplikacji Spring Boot w edytorze tekstów, a następnie znajdź element `<plugin>` dla `azure-webapp-maven-plugin`. Ten element powinien wyglądać podobnie jak w poniższym przykładzie:

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
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

Istnieje kilka wartości, które można zmodyfikować dla wtyczki Maven. Szczegółowy opis tych elementów jest dostępny w dokumentacji dotyczącej [Wtyczka Maven dla usługi Azure Web Apps]. Z tego względu warto w tym artykule zwrócić uwagę na pewne wartości:

| Element | Opis |
|---|---|
| `<version>` | Określa wersję [Wtyczka Maven dla usługi Azure Web Apps]. Aby upewnić się, że używasz najnowszej wersji, sprawdź wersję wymienioną w [Centralnym repozytorium Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22). |
| `<authentication>` | Określa informacje o uwierzytelnianiu dla platformy Azure, które w tym przykładzie zawierają element `<serverId>` obejmujący `azure-auth`. Maven używa tej wartości do wyszukiwania wartości jednostki usługi platformy Azure w pliku *settings.xml* narzędzia Maven, które zostały zdefiniowane we wcześniejszej części tego artykułu. |
| `<resourceGroup>` | Określa docelową grupę zasobów, którą w tym przykładzie jest `maven-plugin`. Jeśli grupa zasobów jeszcze nie istnieje, zostanie utworzona podczas wdrożenia. |
| `<appName>` | Określa nazwę docelową aplikacji internetowej. W tym przykładzie nazwą docelową jest `maven-linux-app-${maven.build.timestamp}`. Dołączono sufiks `${maven.build.timestamp}`, aby uniknąć konfliktu. (Znacznik czasu jest opcjonalny — dla nazwy aplikacji można określić dowolny unikatowy ciąg). |
| `<region>` | Określa region docelowy, którym w tym przykładzie jest `westus`. (Pełna lista znajduje się w dokumentacji dotyczącej [Wtyczka Maven dla usługi Azure Web Apps]). |
| `<appSettings>` | Określa wszystkie unikatowe ustawienia, których będzie używać narzędzie Maven podczas wdrażania aplikacji internetowej na platformie Azure. W tym przykładzie element `<property>` zawiera parę nazwa/wartość elementów podrzędnych, które określają port dla aplikacji. |

> [!NOTE]
>
> Ustawienia zmiany numeru portu w tym przykładzie są niezbędne tylko w przypadku zmiany portu z wartości domyślnej.
>

## <a name="build-and-deploy-your-container-to-azure"></a>Kompilowanie i wdrażanie kontenera na platformie Azure

Po skonfigurowaniu wszystkich ustawień w poprzednich sekcjach tego artykułu możesz przystąpić do wdrażania kontenera na platformie Azure. Aby to zrobić, wykonaj następujące kroki:

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

* Adres URL aplikacji internetowej będzie wyświetlany w sekcji **Przegląd** Twojej aplikacji internetowej:

   ![Określanie adresu URL aplikacji internetowej][AP02]

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat różnych technologii omówionych w tym artykule, zobacz następujące artykuły:

* [Wtyczka Maven dla usługi Azure Web Apps]

* [Log in to Azure from the Azure CLI](/azure/xplat-cli-connect) (Logowanie do platformy Azure z interfejsu wiersza polecenia platformy Azure)

* [How to use the Maven Plugin for Azure Web Apps to deploy a Spring Boot app to Azure App Service ](deploy-spring-boot-java-app-with-maven-plugin.md) (Jak używać wtyczki Maven dla usługi Azure Web Apps do wdrażania aplikacji Spring Boot w usłudze Azure App Service)

* [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli) (Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure 2.0)

* [Dokumentacja ustawień narzędzia Maven](https://maven.apache.org/settings.html)

* [Wtyczka platformy Docker dla narzędzia Maven]

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

<!-- URL List -->

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure dla deweloperów języka Java]: /azure/java/
[Azure Portal]: https://portal.azure.com/
[Docker]: https://www.docker.com/
[Wtyczka platformy Docker dla narzędzia Maven]: https://github.com/spotify/docker-maven-plugin
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Maven]: https://maven.apache.org/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: https://projects.spring.io/spring-boot/
[Spring Boot na platformie Docker — Wprowadzenie]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Wtyczka Maven dla usługi Azure Web Apps]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[AP01]: ./media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP01.png
[AP02]: ./media/deploy-containerized-spring-boot-java-app-with-maven-plugin/AP02.png
