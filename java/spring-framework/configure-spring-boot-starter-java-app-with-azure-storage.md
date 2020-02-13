---
title: Używanie szablonu startowego Spring Boot dla usługi Azure Storage
description: Dowiedz się, jak skonfigurować aplikację Spring Boot Initializer przy użyciu szablonu startowego Azure Storage.
services: storage
documentationcenter: java
ms.date: 12/19/2018
ms.service: storage
ms.topic: article
ms.workload: storage
ms.openlocfilehash: 64e7c948a7e70291e0a4f89461d9c00f72e61482
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999774"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-storage"></a>Używanie szablonu startowego Spring Boot dla usługi Azure Storage

W tym artykule opisano proces tworzenia niestandardowej aplikacji przy użyciu narzędzia **Spring Initializr**, dodawania szablonu startowego magazynu Azure do aplikacji, a następnie używania aplikacji do przekazania obiektu blob do konta magazynu Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz uaktywnić [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Interfejs wiersza polecenia (CLI) platformy Azure](https://docs.microsoft.com/cli/azure/index).
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie Apache [Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

> [!IMPORTANT]
>
> Aby wykonać kroki opisane w tym artykule, potrzebny jest program Spring Boot w wersji 2.0 lub nowszej.
>

## <a name="create-an-azure-storage-account-and-blob-container-for-your-application"></a>Tworzenie konta usługi Azure Storage i kontenera obiektów blob dla aplikacji

Poniższa procedura umożliwia utworzenie konta i kontenera magazynu Azure.

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+Utwórz zasób**, później pozycję **Magazyn**, a następnie kliknij pozycję **Konto magazynu**.

   ![Tworzenie konta usługi Azure Storage][IMG01]

1. Na stronie **Tworzenie konta magazynu** wprowadź następujące informacje:

   * Wybierz **subskrypcję**.
   * Wybierz istniejącą **grupę zasobów** lub utwórz nową.
   * Wprowadź unikatową **nazwę konta magazynu**, która stanie się częścią identyfikatora URI konta magazynu. Na przykład: w przypadku wprowadzenia **wingtiptoysstorage** jako **nazwy** identyfikator URI przyjmie wartość *wingtiptoysstorage.core.windows.net*.
   * Wybierz **lokalizację** nowego konta magazynu.
1. Po określeniu opcji wymienionych powyżej kliknij przycisk **Przejrzyj i utwórz**. 
1. Zapoznaj się ze specyfikacją, a następnie kliknij przycisk **Utwórz**, aby utworzyć konto magazynu.
1. Po zakończeniu wdrażania kliknij przycisk **Przejdź do zasobu**.
1. Kliknij pozycję **Kontenery**.
1. Kliknij pozycję **+ Kontener**.
   * Nazwij kontener.
   * Wybierz pozycję *Blob* z listy rozwijanej.

   ![Tworzenie kontenera obiektów blob][IMG02]

1. Po utworzeniu kontenera obiektów blob pojawi się on na liście w witrynie Azure Portal.

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Tworzenie prostej aplikacji Spring Boot za pomocą narzędzia Spring Initializr

Poniższa procedura umożliwia utworzenie aplikacji Spring Boot.

1. Przejdź do <https://start.spring.io/>.

1. Określ następujące opcje:

   * Wygeneruj projekt **Maven**.
   * Wybierz język **Java**.
   * Określ wersję narzędzia **Spring Boot** równą lub większą niż 2.0.
   * Określ nazwy **Grupa** i **Artefakt** dla swojej aplikacji.
   * Dodaj zależność **Internet**.

      ![Podstawowe opcje narzędzia Spring Initializr][SI01]

   > [!NOTE]
   >
   > Narzędzie Spring Initializr używa nazw z pól **Group** (Grupa) i **Artifact** (Artefakt) do utworzenia nazwy pakietu. Na przykład: *com.wingtiptoys.storage*.
   >

1. Po określeniu opcji wymienionych powyżej kliknij przycisk **Generate** (Generuj).

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

1. Po wyodrębnieniu plików w systemie lokalnym prosta aplikacja Spring Boot będzie gotowa do edycji.

## <a name="configure-your-spring-boot-app-to-use-the-azure-storage-starter"></a>Konfigurowanie aplikacji Spring Boot do korzystania z szablonu startowego Azure Storage

Poniższa procedura umożliwia skonfigurowanie aplikacji Spring Boot do korzystania z magazynu Azure.

1. Znajdź plik *pom.xml* w katalogu głównym aplikacji. Na przykład:

   `C:\SpringBoot\storage\pom.xml`

   — lub —

   `/users/example/home/storage/pom.xml`

1. Otwórz plik *pom.xml* w edytorze tekstów, a następnie dodaj szablon startowy Spring Cloud Azure Storage do listy `<dependencies>`:

   ```xml
   <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>spring-azure-starter-storage</artifactId>
      <version>1.0.0.M2</version>
   </dependency>
   ```

   ![Edytuj plik pom.xml][SI03]

1. Zapisz i zamknij plik *pom.xml*.

## <a name="create-an-azure-credential-file"></a>Utwórz plik poświadczeń platformy Azure

Poniższa procedura tworzy plik poświadczeń platformy Azure.

1. Otwórz wiersz polecenia.

1. Nawiguj do katalogu *resources* aplikacji Spring Boot. Na przykład:

   ```shell
   cd C:\SpringBoot\storage\src\main\resources
   ```

   — lub —

   ```shell
   cd /users/example/home/storage/src/main/resources
   ```

1. Zaloguj się do swojego konta platformy Azure:

   ```azurecli
   az login
   ```

1. Wyświetl listę swoich subskrypcji:

   ```azurecli
   az account list
   ```
   Platforma Azure zwróci listę subskrypcji i trzeba będzie skopiować identyfikator GUID subskrypcji, która ma zostać użyta. Na przykład:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "11111111-1111-1111-1111-111111111111",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "22222222-2222-2222-2222-222222222222",
       "user": {
         "name": "gena.soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. Określ identyfikator GUID subskrypcji, która ma być używana z platformą Azure. Na przykład:

   ```azurecli
   az account set -s 11111111-1111-1111-1111-111111111111
   ```

1. Utwórz plik poświadczeń platformy Azure:

   ```azurecli
   az ad sp create-for-rbac --sdk-auth > my.azureauth
   ```

   To polecenie utworzy plik *my.azureauth* w katalogu *resources* z zawartością podobną do podanej w poniższym przykładzie:

   ```json
   {
     "clientId": "33333333-3333-3333-3333-333333333333",
     "clientSecret": "44444444-4444-4444-4444-444444444444",
     "subscriptionId": "11111111-1111-1111-1111-111111111111",
     "tenantId": "22222222-2222-2222-2222-222222222222",
     "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
     "resourceManagerEndpointUrl": "https://management.azure.com/",
     "activeDirectoryGraphResourceId": "https://graph.windows.net/",
     "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
     "galleryEndpointUrl": "https://gallery.azure.com/",
     "managementEndpointUrl": "https://management.core.windows.net/"
   }
   ```

## <a name="configure-your-spring-boot-app-to-use-your-azure-storage-account"></a>Konfigurowanie aplikacji Spring Boot do korzystania z konta Azure Storage

Poniższa procedura umożliwia skonfigurowanie aplikacji Spring Boot do korzystania z konta magazynu Azure.

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji. Na przykład:

   `C:\SpringBoot\storage\src\main\resources\application.properties`

   — lub —

   `/users/example/home/storage/src/main/resources/application.properties`

2. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj w pliku poniższe wiersze kodu i zastąp przykładowe wartości odpowiednimi właściwościami dotyczącymi konta magazynu:

   ```yaml
   spring.cloud.azure.credential-file-path=my.azureauth
   spring.cloud.azure.resource-group=wingtiptoysresources
   spring.cloud.azure.region=West US
   spring.cloud.azure.storage.account=wingtiptoysstorage
   ```
   Gdzie:

   |                   Pole                   |                                            Opis                                            |
   |-------------------------------------------|---------------------------------------------------------------------------------------------------|
   | `spring.cloud.azure.credential-file-path` |            Określa plik poświadczeń Azure utworzony wcześniej w tym samouczku.             |
   |    `spring.cloud.azure.resource-group`    |           Określa grupę zasobów Azure, która zawiera Twoje konto usługi Azure Storage.            |
   |        `spring.cloud.azure.region`        | Określa region geograficzny podany podczas tworzenia konta usługi Azure Storage. |
   |   `spring.cloud.azure.storage.account`    |            Określa konto usługi Azure Storage utworzone wcześniej w tym samouczku.             |


3. Zapisz i zamknij plik *application.properties*.

## <a name="add-sample-code-to-implement-basic-azure-storage-functionality"></a>Dodawanie przykładowego kodu w celu zaimplementowania podstawowej funkcjonalności magazynu Azure

W tej sekcji utworzysz niezbędne klasy języka Java do przechowywania obiektu blob na koncie magazynu Azure.

### <a name="modify-the-main-application-class"></a>Modyfikowanie głównej klasy aplikacji

1. Znajdź główny plik Java aplikacji w katalogu pakietu aplikacji. Na przykład:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\StorageApplication.java`

   — lub —

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/StorageApplication.java`

1. Otwórz główny plik Java aplikacji w edytorze tekstów, a następnie dodaj poniższe wiersze kodu do pliku. Zastąp wingtiptoys własnymi wartościami:

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class StorageApplication {
      public static void main(String[] args) {
         SpringApplication.run(StorageApplication.class, args);
      }
   }
   ```

1. Zapisz i zamknij główny plik Java aplikacji.

### <a name="add-a-web-controller-class"></a>Dodawanie klasy kontrolera sieci Web

1. Utwórz nowy plik Java o nazwie *WebController.java* w katalogu pakietów swojej aplikacji. Na przykład:

   `C:\SpringBoot\storage\src\main\java\com\wingtiptoys\storage\WebController.java`

   — lub —

   `/users/example/home/storage/src/main/java/com/wingtiptoys/storage/WebController.java`

1. Otwórz plik Java kontrolera sieci Web w edytorze tekstów, a następnie dodaj poniższe wiersze kodu do pliku.  Zmień *wingtiptoys* na swoją grupę zasobów, a *storage* na nazwę swojego artefaktu.

   ```java
   package com.wingtiptoys.storage;

   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.core.io.Resource;
   import org.springframework.core.io.WritableResource;
   import org.springframework.util.StreamUtils;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PostMapping;
   import org.springframework.web.bind.annotation.RequestBody;
   import org.springframework.web.bind.annotation.RestController;

   import java.io.IOException;
   import java.io.OutputStream;
   import java.nio.charset.Charset;

   @RestController
   public class WebController {

      @Value("blob://test/myfile.txt")
      private Resource blobFile;

      @GetMapping(value = "/")
      public String readBlobFile() throws IOException {
         return StreamUtils.copyToString(
            this.blobFile.getInputStream(),
            Charset.defaultCharset()) + "\n";
      }

      @PostMapping(value = "/")
      public String writeBlobFile(@RequestBody String data) throws IOException {
         try (OutputStream os = ((WritableResource) this.blobFile).getOutputStream()) {
            os.write(data.getBytes());
         }
         return "File was updated.\n";
      }
   }
   ```

   Gdzie składnia `@Value("blob://[container]/[blob]")` odpowiednio definiuje nazwy kontenera i obiektu blob, w których mają być przechowywane dane.

1. Zapisz i zamknij plik Java kontrolera sieci Web.

1. Otwórz wiersz polecenia i zmień katalog na folder, w którym znajduje się plik *pom.xml*. Na przykład:

   `cd C:\SpringBoot\storage`

   — lub —

   `cd /users/example/home/storage`

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Gdy aplikacja jest uruchomiona, możesz użyć narzędzia *curl* w celu przetestowania swojej aplikacji, na przykład:

   a. Wyślij żądanie POST, aby zaktualizować zawartość pliku:

      ```shell
      curl -X POST -H "Content-Type: text/plain" -d "Hello World" http://localhost:8080/
      ```

      Powinna zostać wyświetlona odpowiedź, że plik został zaktualizowany.

   b. Wyślij żądanie GET, aby sprawdzić zawartość pliku:

      ```shell
      curl -X GET http://localhost:8080/
      ```

     Powinien zostać wyświetlony tekst „Hello world”, opublikowany przed chwilą.

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono nową aplikację Java przy użyciu narzędzia **[Spring Initializr]** , dodano szablon startowy magazynu Azure do aplikacji, a następnie skonfigurowano aplikację w celu przekazania obiektu blob do konta magazynu Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-key-vault?view=azure-java-stable)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat dodatkowych szablonów startowych Spring Boot, które są dostępne dla platformy Microsoft Azure, zobacz artykuł [Spring Boot Starters for Azure](spring-boot-starters-for-azure.md) (Szablony startowe Spring Boot dla platformy Azure).

Aby uzyskać szczegółowe informacje na temat dodatkowych interfejsów API magazynu Azure, które można wywołać z aplikacji Spring Boot, zobacz następujące artykuły:
* [Szybki start: Zarządzanie obiektami BLOB za pomocą zestawu SDK V12 języka Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Jak używać usługi Azure Queue Storage w języku Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Jak korzystać z usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB przy użyciu języka Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Programowanie dla Azure Files przy użyciu języka Java](/azure/storage/files/storage-java-how-to-use-file-storage)

<!-- IMG List -->

[IMG01]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-01.png
[IMG02]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-02.png
[IMG03]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-03.png
[IMG04]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-04.png
[IMG05]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-storage-account-05.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-01.png
[SI02]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-02.png
[SI03]: ./media/configure-spring-boot-starter-java-app-with-azure-storage/create-project-03.png
