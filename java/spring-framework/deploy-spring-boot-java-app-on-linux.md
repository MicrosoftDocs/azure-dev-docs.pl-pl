---
title: Wdrażanie aplikacji internetowej Spring Boot w systemie Linux w usłudze Azure App Service
description: W tym samouczku przedstawiono procedurę wdrażania aplikacji internetowej Spring Boot jako aplikacji internetowej systemu Linux na platformie Microsoft Azure.
services: azure app service
documentationcenter: java
ms.date: 12/31/2019
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: mvc
ms.openlocfilehash: fb8e49ce59c363276a0ed615b3da29ca8d02f09e
ms.sourcegitcommit: efa585ecdcf1cc54a6f0b664fb83cd4f0ccc7b2c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2020
ms.locfileid: "79990490"
---
# <a name="deploy-a-spring-boot-application-to-linux-on-azure-app-service"></a>Wdrażanie aplikacji Spring Boot w systemie Linux w usłudze Azure App Service

W tym samouczku przedstawiono procedurę konteneryzacji aplikacji [Spring Boot] przy użyciu platformy [Docker] i wdrażania własnego obrazu platformy Docker na hoście z systemem Linux w usłudze [Azure App Service](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).

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

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Pierwsze kroki Spring Boot na platformie Docker

Poniżej przedstawiono kroki wymagane do utworzenia prostej aplikacji internetowej Spring Boot i przetestowania jej lokalnie.

1. Otwórz wiersz polecenia i utwórz katalog lokalny, w którym będzie przechowywana aplikacja, a następnie przejdź do tego katalogu, na przykład:

   ```bash
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Sklonuj przykładowy projekt [Spring Boot na platformie Docker — wprowadzenie] do utworzonego katalogu, na przykład:

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Zmień katalog na ukończony projekt, na przykład:

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Skompiluj plik JAR przy użyciu narzędzia Maven, na przykład:

   ```bash
   mvn package
   ```

1. Po utworzeniu aplikacji internetowej przejdź do katalogu `target`, w którym znajduje się plik JAR, i uruchom aplikację internetową; na przykład:

   ```bash
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar --server.port=80
   ```

1. Przetestuj aplikację internetową, przechodząc do niej lokalnie przy użyciu przeglądarki internetowej. Jeśli na przykład masz dostęp do narzędzia curl, a serwer Tomcat został skonfigurowany do uruchamiania na porcie 80:

   ```bash
   curl http://localhost
   ```

1. Powinien zostać wyświetlony następujący komunikat: **Hello Docker World**

   ![Przeglądanie aplikacji przykładowej lokalnie][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Tworzenie rejestru usługi Azure Container Registry do użytku jako prywatny rejestr platformy Docker

W poniższych krokach objaśniono sposób tworzenia rejestru usługi Azure Container Registry przy użyciu witryny Azure Portal.

> [!NOTE]
>
> Jeśli chcesz użyć interfejsu wiersza polecenia platformy Azure zamiast witryny Azure Portal, wykonaj kroki opisane w artykule [Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą interfejsu wiersza polecenia platformy Azure w wersji 2.0](/azure/container-registry/container-registry-get-started-azure-cli).

1. Przejdź do witryny [Azure Portal] i zaloguj się.

   Po zalogowaniu się do konta w witrynie Azure Portal wykonaj kroki opisane w artykule [Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą witryny Azure Portal], które sparafrazowano poniżej ze względów praktycznych.

1. Kliknij ikonę menu **+ Nowy**, kliknij pozycję **Kontenery**, a następnie kliknij pozycję **Azure Container Registry**.

   ![Tworzenie nowego rejestru usługi Azure Container Registry][AR01]

1. Gdy zostanie wyświetlona strona **Tworzenie rejestru kontenerów**, wprowadź odpowiednie wartości w polach **Nazwa rejestru**, **Subskrypcja**, **Grupa zasobów** i **Lokalizacja**. Wybierz pozycję **Włącz** dla opcji **Użytkownik administracyjny**. Następnie kliknij pozycję **Utwórz**.

   ![Konfigurowanie ustawień rejestru usługi Azure Container Registry][AR03]

1. Po utworzeniu rejestru kontenerów przejdź do rejestru kontenerów w witrynie Azure Portal i kliknij pozycję **Klucze dostępu**. Zanotuj nazwę użytkownika i hasło na potrzeby kolejnych kroków.

   ![Klucze dostępu do usługi Azure Container Registry][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Konfigurowanie narzędzia Maven do korzystania z kluczy dostępu do usługi Azure Container Registry

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład: „*C:\SpringBoot\gs-spring-boot-docker\complete*” or „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* przy użyciu edytora tekstu.

1. Zaktualizuj kolekcję `<properties>` w pliku *pom.xml* przy użyciu najnowszej wersji wtyczki [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin), wartości serwera logowania i ustawień dostępu dla rejestru usługi Azure Container Registry z poprzedniej sekcji tego samouczka. Przykład:

   ```xml
   <properties>
      <jib-maven-plugin.version>1.7.0</jib-maven-plugin.version>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <username>wingtiptoysregistry</username>
      <password>{put your Azure Container Registry access key here}</password>
   </properties>
   ```

1. Dodaj wtyczkę [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) do kolekcji `<plugins>` w pliku *pom.xml*.  W tym przykładzie użyto wersji 1.8.0.

   Określ obraz podstawowy w wierszu `<from>/<image>` tutaj `mcr.microsoft.com/java/jre:8-zulu-alpine`. Określ nazwę obrazu końcowego, który ma zostać skompilowany przy użyciu obrazu podstawowego w wierszu `<to>/<image>`.  

   Wartość `{docker.image.prefix}` uwierzytelniania to **Serwer logowania** na pokazanej wcześniej stronie rejestru. Wartość `{project.artifactId}` to nazwa i numer wersji pliku JAR z pierwszej kompilacji projektu przy użyciu narzędzia Maven.

   Określ nazwę użytkownika i hasło w okienku rejestru w węźle `<to>/<auth>`. Przykład:

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>1.8.0</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jre:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
            <auth>
               <username>${username}</username>
               <password>${password}</password>
            </auth>
        </to>
     </configuration>
   </plugin>
   ```

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot i uruchom następujące polecenie, aby ponownie skompilować aplikację i wypchnąć kontener do rejestru usługi Azure Container Registry:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Gdy wtyczka Jib jest używana do wypychania obrazu do rejestru usługi Azure Container Registry, obraz nie będzie używać pliku *Dockerfile*. Szczegółowe informacje zawiera [ten](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html) dokument.
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>Tworzenia aplikacji internetowej w systemie Linux w usłudze Azure App Service przy użyciu obrazu kontenera

1. Przejdź do witryny [Azure Portal] i zaloguj się.

2. Kliknij ikonę menu **+ Utwórz zasób**, kliknij pozycję **Środowisko obliczeniowe**, a następnie kliknij pozycję **Web App for Containers**.
   
   ![Tworzenie nowej aplikacji internetowej w witrynie Azure Portal][LX01]

3. Gdy zostanie wyświetlona strona **Aplikacja internetowa w systemie Linux**, wprowadź następujące informacje:

   * Wybierz pozycję **Subskrypcja** z listy rozwijanej.

   * Wybierz istniejącą grupę zasobów (opcja **Grupa zasobów**) lub określ nazwę, aby utworzyć nową grupę zasobów.

   * Wprowadź unikatową nazwę w polu **Nazwa aplikacji**, na przykład: „*wingtiptoyslinux*”

   * Określ wartość `Docker Container`, aby opublikować (opcja **Publikuj**).

   * Wybierz *Linux* jako **System operacyjny**.

   * Wybierz **Region**.

   * Zaakceptuj **Plan systemu Linux** i wybierz istniejący **Plan usługi App Service** lub kliknij przycisk **Utwórz nowy**, aby utworzyć nowy plan usługi App Service.

   * Kliknij pozycję **Next: Docker**.

   ![Konfigurowanie ustawień aplikacji internetowej][LX02]

      Na stronie **Aplikacja internetowa** wybierz pozycję **Docker** i wprowadź następujące informacje:

   * Wybierz opcję **Jeden kontener**.

   * **Rejestr**: Wybierz kontener, na przykład: „*wingtiptoysregistry*”

   * **Obraz**: Wybierz utworzony wcześniej obraz, na przykład: „*gs-spring-boot-docker*”

   * **Tag**: Wybierz tag obrazu, na przykład: „*najnowszy*”

   * **Polecenie uruchamiania**: Pozostaw to pole puste, ponieważ obraz ma już polecenie uruchamiania

   Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Przeglądanie + tworzenie**.

   ![Konfigurowanie ustawień aplikacji internetowej][LX02-A]

   * Kliknij pozycję **Przegląd + utwórz**.

Przejrzyj informacje podsumowujące i kliknij pozycję **Utwórz**.

Po zakończeniu wdrażania kliknij przycisk **Przejdź do zasobu**.  Na stronie wdrożenia zostanie wyświetlony adres URL umożliwiający uzyskanie dostępu do aplikacji.

   ![Uzyskiwanie adresu URL wdrożenia][LX02-B]

> [!NOTE]
>
> Platforma Azure będzie automatycznie mapować żądania internetowe na osadzony serwer Tomcat, który działa na porcie 80. Jednak jeśli osadzony serwer Tomcat został skonfigurowany tak, aby był uruchamiany na porcie 8080 lub niestandardowym porcie, należy dodać do aplikacji internetowej zmienną środowiskową, która definiuje port osadzonego serwera Tomcat. Aby to zrobić, wykonaj następujące kroki:
>
> 1. Przejdź do witryny [Azure Portal] i zaloguj się.
>
> 2. Kliknij ikonę **Aplikacje internetowe** i wybierz swoją aplikację na stronie **App Services**.
>
> 3. Kliknij pozycję **Konfiguracja** na lewym pasku nawigacyjnym.
>
> 4. W sekcji **Ustawienia aplikacji** dodaj nowe ustawienie o nazwie **WEBSITES_PORT** i wprowadź niestandardowy numer portu dla wartości.
>
> 5. Kliknij przycisk **OK**. Następnie kliknij przycisk **Save** (Zapisz).
>
> ![Zapisywanie niestandardowego numeru portu w witrynie Azure Portal][LX03]

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

### <a name="additional-resources"></a>Zasoby dodatkowe

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Wdrażanie aplikacji Spring Boot w usłudze Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)
* [Wdrażanie aplikacji platformy Spring Boot w klastrze Kubernetes w usłudze Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md)

Aby uzyskać więcej informacji na temat używania platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Praca z narzędziami Azure DevOps i językiem Java].

Aby uzyskać więcej informacji na temat przykładowego projektu aplikacji Spring Boot na platformie Docker, zobacz [Spring Boot na platformie Docker — Wprowadzenie].

Aby uzyskać pomoc dotyczącą rozpoczynania pracy z własnymi aplikacjami Spring Boot, zapoznaj się z narzędziem **Spring Initializr** na stronie https://start.spring.io/.

Aby uzyskać więcej informacji na temat rozpoczynania tworzenia prostej aplikacji Spring Boot, zapoznaj się z narzędziem Spring Initializr na stronie https://start.spring.io/.

Aby uzyskać dodatkowe przykłady użycia niestandardowych obrazów platformy Docker na platformie Azure, zobacz [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux].

<!-- URL List -->

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/overview
[Azure Container Service (AKS)]: https://azure.microsoft.com/services/container-service/
[Azure dla deweloperów języka Java]: /azure/java/
[Azure Portal]: https://portal.azure.com/
[Tworzenie prywatnego rejestru kontenerów platformy Docker za pomocą witryny Azure Portal]: /azure/container-registry/container-registry-get-started-portal
[Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Usługa Git]: https://github.com/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/java/
[Maven]: http://maven.apache.org/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot na platformie Docker — wprowadzenie]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- IMG List -->

[SB01]: ./media/deploy-spring-boot-java-app-on-linux/SB01.png
[SB02]: ./media/deploy-spring-boot-java-app-on-linux/SB02.png
[AR01]: ./media/deploy-spring-boot-java-app-on-linux/AR01.png
[AR03]: ./media/deploy-spring-boot-java-app-on-linux/AR03.png
[AR04]: ./media/deploy-spring-boot-java-app-on-linux/AR04.png
[LX01]: ./media/deploy-spring-boot-java-app-on-linux/LX01.png
[LX02]: ./media/deploy-spring-boot-java-app-on-linux/LX02.png
[LX02-A]: ./media/deploy-spring-boot-java-app-on-linux/LX02-A.png
[LX02-B]: ./media/deploy-spring-boot-java-app-on-linux/LX02-B.png
[LX03]: ./media/deploy-spring-boot-java-app-on-linux/LX03.png
