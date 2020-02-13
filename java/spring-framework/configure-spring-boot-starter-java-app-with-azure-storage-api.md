---
title: Jak używać programu Spring Boot Starter z interfejsem API usługi Azure Storage
description: Dowiedz się, jak skonfigurować aplikację Spring Boot Initializr z interfejsem API usługi Azure Storage.
services: storage
documentationcenter: java
ms.date: 12/19/2018
ms.service: storage
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage
ms.openlocfilehash: 5f61f3eb721c475b0941b2215a0a180ef00ea3d3
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999781"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-storage-api"></a>Jak używać programu Spring Boot Starter z interfejsem API usługi Azure Storage

## <a name="overview"></a>Omówienie

W tym artykule opisano proces tworzenia niestandardowej aplikacji przy użyciu narzędzia **Spring Initializr**, a następnie używania tej aplikacji w celu uzyskiwania dostępu do magazynu na platformie Azure przy użyciu interfejsu API usługi Azure Storage.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/overview).
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie Apache [Maven](http://maven.apache.org/), wersja 3.0 lub nowsza.

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Tworzenie aplikacji niestandardowej przy użyciu narzędzia Spring Initializr

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt **Maven** przy użyciu języka **Java**, wprowadź nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt) dla aplikacji, a następnie kliknij link **Switch to the full version** (Przełącz do pełnej wersji), aby przejść do pełnej wersji narzędzia Spring Initializr.

   ![Podstawowe opcje narzędzia Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-basic.png)

   > [!NOTE]
   >
   > Narzędzie Spring Initializr użyje nazw z pól **Group** (Grupa) i **Artifact** (Artefakt), aby utworzyć nazwę pakietu, na przykład: *com.contoso.wingtiptoysdemo*.
   >

1. Przewiń w dół do sekcji **Azure** i zaznacz pole wyboru **Azure Storage**.

   ![Opcje pełnej wersji narzędzia Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-advanced.png)

1. Przewiń w dół strony i kliknij przycisk **Generate Project** (Generuj projekt).

   ![Opcje pełnej wersji narzędzia Spring Initializr](media/configure-spring-boot-starter-java-app-with-azure-storage/spring-initializr-generate.png)

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

   ![Pobieranie niestandardowego projektu Spring Boot](media/configure-spring-boot-starter-java-app-with-azure-storage/download-app.png)

## <a name="sign-into-azure-and-select-the-subscription-to-use"></a>Logowanie do platformy Azure i wybór subskrypcji do użycia

1. Otwórz wiersz polecenia.

1. Zaloguj się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az login
   ```
   Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania.

1. Wyświetl listę swoich subskrypcji:

   ```azurecli
   az account list
   ```
   Platforma Azure zwróci listę subskrypcji i trzeba będzie skopiować identyfikator GUID dla subskrypcji, która ma zostać użyta. Na przykład:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Określ identyfikator GUID konta, które ma być używane z platformą Azure. Na przykład:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

1. Utwórz grupę zasobów dla zasobów platformy Azure, które będą używane w tym artykule. Na przykład:
   ```azurecli
   az group create --name wingtiptoysresources --location westus
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa unikatową nazwę grupy zasobów. |
   | `location` | Określa [region świadczenia usługi Azure](https://azure.microsoft.com/regions/), w którym będzie hostowana grupa zasobów. |

   W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone wyniki tworzenia grupy zasobów. Na przykład:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/wingtiptoysresources",
     "location": "westus",
     "managedBy": null,
     "name": "wingtiptoysresources",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Utwórz konto magazynu na platformie Azure w grupie zasobów dla aplikacji Spring Boot. Na przykład:
   ```azurecli
   az storage account create --name wingtiptoysstorage --resource-group wingtiptoysresources --location westus --sku Standard_LRS
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa unikatową nazwę konta magazynu. |
   | `resource-group` | Określa nazwę grupy zasobów utworzonej w poprzednim kroku. |
   | `location` | Określa [region świadczenia usługi Azure](https://azure.microsoft.com/regions/), w którym będzie hostowane konto magazynu. |
   | `sku` | Określa jedną z następujących wartości: `Premium_LRS`, `Standard_GRS`, `Standard_LRS`, `Standard_RAGRS`, `Standard_ZRS`. |

   Platforma Azure zwróci długi ciąg JSON zawierający stan aprowizacji. Na przykład: |

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "identity": null,
     "kind": "Storage"
       ...
       ... (A long list of values will be displayed here.)
       ...
     "statusOfPrimary": "available",
     "statusOfSecondary": null,
     "tags": {},
     "type": "Microsoft.Storage/storageAccounts"
   }
   ```

3. Pobierz parametry połączenia dla konta magazynu. Na przykład:
   ```azurecli
   az storage account show-connection-string --name wingtiptoysstorage --resource-group wingtiptoysresources
   ```
   Gdzie:

   | Parametr | Opis |
   | ---|---|
   | `name` | Określa unikatową nazwę konta magazynu utworzonego w poprzednich krokach. |
   | `resource-group` | Określa nazwę grupy zasobów utworzonej w poprzednich krokach. |

   Platforma Azure zwróci ciąg JSON zawierający parametry połączenia dla konta magazynu, na przykład:

   ```json
   {
     "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz=="
   }
   ```

## <a name="configure-and-compile-your-spring-boot-application"></a>Konfigurowanie i kompilowanie aplikacji Spring Boot

1. Wyodrębnij pliki z pobranego archiwum projektu do katalogu.

1. Przejdź do folderu *src/main/resources* w projekcie i otwórz plik *application.properties* w edytorze tekstu.

1. Dodaj klucz dla konta magazynu, na przykład:
   ```yaml
   azure.storage.connection-string=DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=wingtiptoysstorage;AccountKey=AbCdEfGhIjKlMnOpQrStUvWxYz==
   ```

1. Przejdź do folderu */src/main/java/com/example/wingtiptoysdemo* w projekcie i otwórz plik *WingtiptoysdemoApplication.java* w edytorze tekstu.

1. Zastąp istniejący kod Java następującym przykładem, który powoduje wyświetlenie listy obiektów blob w kontenerze:

   ```java
   package com.example.wingtiptoysdemo;

   import com.microsoft.azure.storage.*;
   import com.microsoft.azure.storage.blob.*;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.CommandLineRunner;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   import java.net.URISyntaxException;

   @SpringBootApplication
   public class WingtiptoysdemoApplication implements CommandLineRunner {

      @Autowired
      private CloudStorageAccount cloudStorageAccount;

      final String containerName = "mycontainer";

      public static void main(String[] args) {
         SpringApplication.run(WingtiptoysdemoApplication.class, args);
      }

      public void run(String... var1)
             throws URISyntaxException, StorageException {
          // Create a container (if it does not exist).
          createContainerIfNotExists(containerName);
          // Upload a blob to the container.
          uploadTextBlob(containerName);
      }

      private void createContainerIfNotExists(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Create the container if it does not exist.
            container.createIfNotExists();
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }

      private void uploadTextBlob(String containerName)
            throws URISyntaxException, StorageException {
         try
         {
            // Create a blob client.
            final CloudBlobClient blobClient = cloudStorageAccount.createCloudBlobClient();
            // Get a reference to a container. (Name must be lower case.)
            final CloudBlobContainer container = blobClient.getContainerReference(containerName);
            // Get a blob reference for a text file.
            CloudBlockBlob blob = container.getBlockBlobReference("test.txt");
            // Upload some text into the blob.
            blob.uploadText("Hello World!");
         }
         catch (Exception e)
         {
            // Output the stack trace.
            e.printStackTrace();
         }
      }
   }
   ```
   > [!NOTE]
   >
   > Powyższy przykład automatycznie konfiguruje ustawienia konta magazynu zdefiniowane w pliku *application.properties*.
   >

1. Skompiluj i uruchom aplikację:
   ```shell
   mvn clean package spring-boot:run
   ```

   Aplikacja utworzy kontener i przekaże plik tekstowy jako obiekt blob do kontenera, który zostanie wyświetlony na koncie magazynu w witrynie [Azure Portal](https://portal.azure.com).

   ![Wyświetlanie listy obiektów blob w witrynie Azure Portal](media/configure-spring-boot-starter-java-app-with-azure-storage/list-blobs-in-portal.png)

   > [!NOTE]
   > 
   > Podczas kompilowania aplikacji może zostać wyświetlony następujący komunikat o błędzie:
   > 
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] BUILD FAILURE`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[INFO] Total time: 2.616 s`<br/>
   > `[INFO] Finished at: 2017-11-11T13:14:15Z`<br/>
   > `[INFO] Final Memory: 26M/213M`<br/>
   > `[INFO] ------------------------------------------------------------------------`<br/>
   > `[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2`<br/>
   > `.18.1:test (default-test) on project wingtiptoysdemo: Execution default-test of`<br/>
   > `goal org.apache.maven.plugins:maven-surefire-plugin:2.18.1:test failed: The for`<br/>
   > `ked VM terminated without properly saying goodbye. VM crash or System.exit called?`<br/>
   > `[ERROR] Command was /bin/sh -c cd /home/robert/SpringBoot/wingtiptoysdemo && /u`<br/>
   > `sr/lib/jvm/java-8-openjdk-amd64/jre/bin/java -jar /home/robert/SpringBoot/wingt`<br/>
   > `iptoysdemo/target/surefire/surefirebooter6371623993063346766.jar /home/robert/S`<br/>
   > `pringBoot/wingtiptoysdemo/target/surefire/surefire5107893623933537917tmp /home/`<br/>
   > `robert/SpringBoot/wingtiptoysdemo/target/surefire/surefire_01414159391084128068tmp`<br/>
   > `[ERROR] -> [Help 1]`<br/>
   > 
   > W takim przypadku warto wyłączyć testowanie Maven Surefire. Aby to zrobić, dodaj następujący wpis wtyczki w pliku *pom.xml*:
   > 
   > ```xml
   > <plugin>
   >   <groupId>org.apache.maven.plugins</groupId>
   >   <artifactId>maven-surefire-plugin</artifactId>
   >   <version>2.20.1</version>
   >   <configuration>
   >     <skipTests>true</skipTests>
   >   </configuration>
   > </plugin>
   > ```
   > 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat środowiska Spring i platformy Azure, przejdź do informacji na temat Spring w centrum dokumentacji platformy Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat dodatkowych programów Spring Boot Starter, które są dostępne dla platformy Microsoft Azure, zobacz artykuł [Spring Boot Starters for Azure](spring-boot-starters-for-azure.md) (Programy Spring Boot Starter dla platformy Azure).

Aby uzyskać dodatkowe informacje na temat integrowania funkcji platformy Azure z aplikacjami opartymi na środowisku Spring, zobacz artykuł [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework/).

Aby uzyskać szczegółowe informacje na temat dodatkowych interfejsów API usługi Azure Storage, które można wywołać z aplikacji Spring Boot, zobacz następujące artykuły:
* [Używanie usługi Azure Blob Storage w języku Java](/azure/storage/blobs/storage-java-how-to-use-blob-storage)
* [Używanie usługi Azure Queue Storage w języku Java](/azure/storage/queues/storage-java-how-to-use-queue-storage)
* [Używanie usługi Azure Table Storage w języku Java](/azure/cosmos-db/table-storage-how-to-use-java)
* [Używanie usługi Azure File Storage w języku Java](/azure/storage/files/storage-java-how-to-use-file-storage)
