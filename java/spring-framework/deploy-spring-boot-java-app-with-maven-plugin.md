---
title: Wdrażanie aplikacji internetowej Spring Boot w postaci pliku JAR w usłudze Azure App Service dla systemu Linux
description: Dowiedz się, jak wdrożyć plik JAR aplikacji Spring Boot w usłudze App Service w systemie Linux, używając wtyczki narzędzia Maven dla aplikacji internetowej platformy Azure.
services: app-service
documentationcenter: java
ms.date: 12/19/2018
ms.service: app-service
ms.topic: article
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: cb6a5de6f48092fdb6b1756363c8be7300d72a56
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001104"
---
# <a name="deploy-a-spring-boot-jar-file-app-to-azure-app-service-with-maven-and-azure-on-linux"></a>Wdrażanie aplikacji Spring Boot w postaci pliku JAR w usłudze Azure App Service za pomocą narzędzia Maven i platformy Azure w systemie Linux

W tym przewodniku Szybki start użyjesz [wtyczki Maven dla aplikacji internetowych usługi Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme), aby wdrożyć aplikację Spring Boot spakowaną jako plik JAR środowiska Java SE w [usłudze Azure App Service w systemie Linux](/azure/app-service/containers/). Wdrożenie w środowisku Java ma przewagę nad [serwerem Tomcat i plikami WAR](/azure/app-service/containers/quickstart-java), gdy chcesz skonsolidować zależności, środowisko uruchomieniowe i konfigurację aplikacji w pojedynczym artefakcie, który można wdrożyć.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym samouczku, musisz zainstalować i skonfigurować następujące elementy:

* [Interfejs wiersza polecenia platformy Azure](/cli/azure/) — lokalnie lub za pośrednictwem usługi [Azure Cloud Shell](https://shell.azure.com).
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie Apache [Maven](https://maven.apache.org/), wersja 3.
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="install-and-sign-in-to-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure i logowanie się do niego

Najprostszym i najłatwiejszym sposobem na wdrożenie aplikacji Spring Boot za pomocą wtyczki narzędzia Maven jest użycie [interfejsu wiersza polecenia platformy Azure](/cli/azure/).

Zaloguj się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:
   
   ```shell
   az login
   ```
   
Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania.

## <a name="clone-the-sample-app"></a>Klonowanie przykładowej aplikacji

W tej sekcji sklonujesz ukończoną aplikację Spring Boot i przetestujesz ją lokalnie.

1. Otwórz wiersz polecenia lub okno terminalu i utwórz katalog lokalny, w którym będzie przechowywana aplikacja Spring Boot, a następnie przejdź do tego katalogu, na przykład:
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   — lub —
   ```shell
   mkdir ~/SpringBoot
   cd ~/SpringBoot
   ```

1. Sklonuj przykładowy projekt [Spring Boot Getting Started] do utworzonego katalogu, na przykład:
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot
   ```

1. Zmień katalog na ukończony projekt, na przykład:
   ```shell
   cd gs-spring-boot/complete
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

1. Powinien zostać wyświetlony następujący komunikat: **Greetings from Spring Boot!**

## <a name="configure-maven-plugin-for-azure-app-service"></a>Konfigurowanie wtyczki Maven dla usługi Azure App Service

W tej sekcji skonfigurujesz projekt Spring Boot `pom.xml` tak, aby narzędzie Maven mogło wdrożyć aplikację w usłudze Azure App Service w systemie Linux.

1. Otwórz plik `pom.xml` w edytorze kodu.

2. W sekcji `<build>` pliku pom.xml dodaj poniższy wpis `<plugin>` wewnątrz tagu `<plugins>`.

   ```xml
   <plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.0</version>
   </plugin>
   ```

3. Następnie możesz skonfigurować wdrożenie, uruchomić poniższe polecenie narzędzia Maven w wierszu polecenia, a następnie za pomocą **liczby** wybierz te opcje w wierszu:
    * **OS**: linux  
    * **javaVersion**: Java 8    
    
   ```cmd
   mvn azure-webapp:config
   ```

   Po wyświetleniu monitu **Confirm (Y/N)** (Potwierdź T/N) naciśnij klawisz **„Y”** , aby zakończyć konfigurację.

   ```cmd
   ~@Azure:~/gs-spring-boot/complete$ mvn azure-webapp:config
   [INFO] Scanning for projects...
   [INFO]
   [INFO] -----------------< org.springframework:gs-spring-boot >-----------------
   [INFO] Building gs-spring-boot 0.1.0
   [INFO] --------------------------------[ jar ]---------------------------------
   [INFO]
   [INFO] --- azure-webapp-maven-plugin:1.9.0:config (default-cli) @ gs-spring-boot ---
   [WARNING] The plugin may not work if you change the os of an existing webapp.
   Define value for OS(Default: Linux):
   1. linux [*]
   2. windows
   3. docker
   Enter index to use:
   Define value for javaVersion(Default: Java 8):
   1. Java 11
   2. Java 8 [*]
   Enter index to use:
   Please confirm webapp properties
   AppName : gs-spring-boot-1559091271202
   ResourceGroup : gs-spring-boot-1559091271202-rg
   Region : westeurope
   PricingTier : Premium_P1V2
   OS : Linux
   RuntimeStack : JAVA 8-jre8
   Deploy to slot : false
   Confirm (Y/N)? : Y
   ```

4. Dodaj sekcję `<appSettings>` do sekcji `<configuration>` wtyczki `<azure-webapp-maven-plugin>`, aby nasłuchiwać na porcie *80*.

   ```xml
   <plugin>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-webapp-maven-plugin</artifactId>
       <version>1.9.0</version>
       <configuration>
          <schemaVersion>V2</schemaVersion>
          <resourceGroup>gs-spring-boot-1559091271202-rg</resourceGroup>
          <appName>gs-spring-boot-1559091271202</appName>
          <region>westeurope</region>
          <pricingTier>P1V2</pricingTier>
          <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>
            <webContainer>jre8</webContainer>
          </runtime>
          <!-- Begin of App Settings  -->
          <appSettings>
             <property>
                   <name>JAVA_OPTS</name>
                   <value>-Dserver.port=80</value>
             </property>
          </appSettings>
          <!-- End of App Settings  -->
          <deployment>
            <resources>
              <resource>
                <directory>${project.basedir}/target</directory>
                <includes>
                  <include>*.jar</include>
                </includes>
              </resource>
            </resources>
          </deployment>
         </configuration>
   </plugin>
   ```

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Po skonfigurowaniu wszystkich ustawień w poprzednich sekcjach tego artykułu możesz przystąpić do wdrażania aplikacji internetowej na platformie Azure. Aby to zrobić, wykonaj następujące kroki:

1. Jeśli w pliku *pom.xml* wprowadzono jakiekolwiek zmiany, w wierszu polecenia lub w oknie terminalu, które były używane wcześniej, skompiluj ponownie plik JAR przy użyciu narzędzia Maven, na przykład:
   ```shell
   mvn clean package
   ```

1. Wdróż aplikację internetową na platformie Azure przy użyciu narzędzia Maven, na przykład:
   ```shell
   mvn azure-webapp:deploy
   ```

Narzędzie Maven wdroży aplikację internetową na platformie Azure. Jeśli aplikacja internetowa lub plan aplikacji internetowej jeszcze nie istnieją, zostaną dla Ciebie utworzone. Zanim aplikacja internetowa będzie widoczna pod adresem URL widocznym w danych wyjściowych, może upłynąć kilka minut. Przejdź do adresu URL w przeglądarce internetowej.  Powinien zostać wyświetlony komunikat: Greetings from Spring Boot!

Po wdrożeniu aplikacji internetowej będzie można zarządzać nią za pośrednictwem witryny [Azure Portal].

* Aplikacja internetowa będzie wyświetlana w usłudze **App Services**:

   ![Aplikacja internetowa wyświetlana w usłudze App Services w witrynie Azure Portal][AP01]

* Natomiast adres URL aplikacji internetowej będzie wyświetlany w sekcji **Omówienie** Twojej aplikacji internetowej:

   ![Znajdź adres URL swojej aplikacji internetowej w usłudze App Services w witrynie Azure Portal][AP02]

Sprawdź, czy wdrożenie zakończyło się pomyślnie, używając tego samego polecenia cURL co poprzednio. Zamiast parametru `localhost` użyj adresu URL aplikacji internetowej z portalu. Powinien zostać wyświetlony następujący komunikat: **Greetings from Spring Boot!** 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

- W witrynie Azure Portal wybierz pozycję Grupa zasobów z menu po lewej stronie.
- W polu **Filtruj według nazwy** wpisz **gs-spring-boot-** . Grupa zasobów utworzona w ramach tego samouczka powinna mieć ten prefiks.
- Wybierz grupę zasobów utworzoną w ramach tego samouczka.
- Z górnego menu wybierz pozycję Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-esources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat różnych technologii omówionych w tym artykule, zobacz następujące artykuły:

* [Wtyczka Maven dla usługi Azure Web Apps]

* [Jak używać wtyczki Maven dla usługi Azure Web Apps do wdrażania konteneryzowanych aplikacji Spring Boot na platformie Azure](deploy-containerized-spring-boot-java-app-with-maven-plugin.md)

* [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Dokumentacja ustawień narzędzia Maven](https://maven.apache.org/settings.html)

<!-- URL List -->

[Azure Command-Line Interface (CLI)]: /cli/azure/overview
[Azure for Java Developers]: /azure/java/
[Azure Portal]: https://portal.azure.com/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Working with Azure DevOps and Java]: /azure/devops/
[Maven]: http://maven.apache.org/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot Getting Started]: https://github.com/spring-guides/gs-spring-boot (Wprowadzenie do aplikacji Spring Boot)
[Spring Framework]: https://spring.io/
[Wtyczka Maven dla usługi Azure Web Apps]: /java/api/overview/azure/maven/azure-webapp-maven-plugin/readme

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->


[AP01]: ./media/deploy-spring-boot-java-app-with-maven-plugin/web-app-listed-azure-portal.png
[AP02]: ./media/deploy-spring-boot-java-app-with-maven-plugin/determine-web-app-url.png
