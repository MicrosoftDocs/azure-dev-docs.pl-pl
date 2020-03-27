---
title: Wprowadzenie do zestawu Azure SDK dla języka Java
description: Dowiedz się, jak tworzyć zasoby w chmurze platformy Azure oraz łączyć się z nimi i korzystać z nich w aplikacjach Java.
keywords: Azure, Java, SDK, API, uwierzytelnianie, wprowadzenie
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: b1e10b79-f75e-4605-aecd-eed64873e2d3
ms.custom: seo-java-august2019
ms.openlocfilehash: 306a56ad4d74a316eefbb4b9f24cc14fbc5b9644
ms.sourcegitcommit: efa585ecdcf1cc54a6f0b664fb83cd4f0ccc7b2c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2020
ms.locfileid: "79990441"
---
# <a name="get-started-with-cloud-development-using-java-on-azure"></a>Wprowadzenie do programowania w chmurze przy użyciu języka Java na platformie Azure

Ten przewodnik przeprowadzi Cię przez proces konfigurowania środowiska deweloperskiego na potrzeby programowania na platformie Azure w języku Java. Następnie utworzysz zasoby platformy Azure i połączysz się z nimi w celu wykonania niektórych podstawowych zadań, takich jak przekazywanie pliku lub wdrażanie aplikacji internetowej. Gdy wszystko będzie gotowe, możesz zacząć korzystać z usług platformy Azure we własnych aplikacjach Java.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, [skorzystaj z bezpłatnej wersji próbnej](https://azure.microsoft.com/free/)
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) lub [interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).
- [Java 8](https://www.azul.com/downloads/zulu/) (język uwzględniony w usłudze Azure Cloud Shell)
- [Maven 3](https://maven.apache.org/download.cgi) (narzędzie uwzględnione w usłudze Azure Cloud Shell)

## <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania

Aplikacja języka Java wymaga uprawnień do odczytu i tworzenia w ramach subskrypcji platformy Azure do uruchamiania przykładowego kodu z tego samouczka. Utwórz jednostkę usługi i skonfiguruj aplikację do uruchamiania przy użyciu jej poświadczeń. Jednostki usługi oferują sposób tworzenia nieinterakcyjnego konta skojarzonego z tożsamością, któremu przyznawane są tylko uprawnienia wymagane do działania aplikacji.

[Utwórz jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/create-an-azure-service-principal-azure-cli) i przechwyć dane wyjściowe. W argumencie hasła wprowadź [bezpieczne hasło](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy) zamiast elementu `MY_SECURE_PASSWORD`. Hasło musi składać się z 8–16 znaków i spełniać co najmniej 3 z 4 następujących kryteriów:

* Zawiera małe litery
* Zawiera wielkie litery
* Zawiera cyfry
* Zawiera jeden z następujących symboli: @ # $ % ^ & * - _ ! + = [ ] { } | \ : ' , . ? / ` ~ " ( ) ;

```azurecli-interactive
az ad sp create-for-rbac --name AzureJavaTest --password "MY_SECURE_PASSWORD"
```

Powoduje to wyświetlenie odpowiedzi w następującym formacie:

```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "AzureJavaTest",
  "name": "http://AzureJavaTest",
  "password": password,
  "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
}
```

Następnie skopiuj następujący plik do pliku tekstowego w systemie:

```text
# sample management library properties file
subscription=ssssssss-ssss-ssss-ssss-ssssssssssss
client=cccccccc-cccc-cccc-cccc-cccccccccccc
key=kkkkkkkkkkkkkkkk
tenant=tttttttt-tttt-tttt-tttt-tttttttttttt
managementURI=https\://management.core.windows.net/
baseURL=https\://management.azure.com/
authURL=https\://login.windows.net/
graphURL=https\://graph.windows.net/
```

Zastąp cztery pierwsze wartości następującymi wartościami:

- subscription: użyj wartości *id* z `az account show` w interfejsie wiersza polecenia platformy Azure 2.0.
- client: użyj wartości *appId* z danych wyjściowych pobranych z jednostki usługi.
- key: użyj wartości *password* z danych wyjściowych jednostki usługi.
- tenant: użyj wartości *tenant* z danych wyjściowych jednostki usługi.

Zapisz ten plik w bezpiecznej lokalizacji w systemie, w której kod może go odczytać. Ten plik może być używany na potrzeby przyszłego kodu, dlatego zaleca się jego przechowywanie poza aplikacją opisaną w tym artykule.

Ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku uwierzytelniania w powłoce.   

```bash
export AZURE_AUTH_LOCATION=/Users/raisa/azureauth.properties
```

Jeśli pracujesz w środowisku systemu Windows, dodaj zmienną do właściwości systemu. Otwórz okno programu PowerShell przy użyciu uprawnień administratora i po zastąpieniu drugiej zmiennej ścieżką do pliku wprowadź następujące polecenie:

```powershell
setx AZURE_AUTH_LOCATION "C:\<fullpath>\azureauth.properties" /m
```

## <a name="tooling"></a>Narzędzia

### <a name="create-a-new-maven-project"></a>Tworzenie nowego projektu Maven

> [!NOTE]
> W tym przewodniku narzędzie do kompilowania Maven jest używane do kompilowania i uruchamiania przykładowego kodu, ale inne narzędzia do kompilowania, takie jak Gradle, również współdziałają z bibliotekami platformy Azure dla języka Java. 

Utwórz projekt Maven z poziomu wiersza polecenia w nowym katalogu w systemie:

```shell
mkdir java-azure-test
cd java-azure-test
mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=AzureApp  \ 
-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Spowoduje to utworzenie podstawowego projektu Maven w folderze `testAzureApp`. Dodaj następujące wpisy do projektu `pom.xml`, aby zaimportować biblioteki używane w przykładowym kodzie w tym samouczku.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.3.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-storage</artifactId>
    <version>5.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.1.jre8</version>
</dependency>
```

Dodaj wpis `build` w elemencie `project` najwyższego poziomu, aby użyć wtyczki [maven-exec-plugin](https://www.mojohaus.org/exec-maven-plugin/) do uruchamiania przykładów:

```XML
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.AzureApp</mainClass>
            </configuration>
        </plugin>
    </plugins>
</build>
 ```

### <a name="install-the-azure-toolkit-for-intellij"></a>Instalowanie zestawu narzędzi Azure Toolkit for IntelliJ

[Zestaw narzędzi platformy Azure](intellij/azure-toolkit-for-intellij-installation.md) jest niezbędny, jeśli zamierzasz programowo wdrażać aplikacje internetowe lub interfejsy API, ale nie jest on obecnie używany w przypadku innych rodzajów programowania. Poniżej przedstawiono podsumowanie procesu instalacji. Aby zapoznać się z przewodnikiem Szybki start, odwiedź stronę [przewodnika Szybki start dla zestawu narzędzi Azure Toolkit for IntelliJ](intellij/azure-toolkit-for-intellij-create-hello-world-web-app.md).

- Wybierz menu **File** (Plik), a następnie wybierz pozycję **Settings...** (Ustawienia). 

- Wybierz pozycję **Browse repositories...** (Przeglądaj repozytoria), a następnie wyszukaj ciąg „Azure” i zainstaluj zestaw narzędzi **Azure Toolkit for IntelliJ**.

- Uruchom ponownie platformę IntelliJ.

### <a name="install-the-azure-toolkit-for-eclipse"></a>Instalowanie zestawu narzędzi Azure Toolkit for Eclipse

[Zestaw narzędzi platformy Azure](eclipse/azure-toolkit-for-eclipse.md) jest niezbędny, jeśli zamierzasz programowo wdrażać aplikacje internetowe lub interfejsy API, ale nie jest on obecnie używany w przypadku innych rodzajów programowania. Poniżej przedstawiono podsumowanie procesu instalacji. Aby zapoznać się z przewodnikiem Szybki start, odwiedź stronę [przewodnika Szybki start dla zestawu narzędzi Azure Toolkit for Eclipse](eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app.md).

- Wybierz menu **Help** (Pomoc), a następnie wybierz pozycję **Install New software** (Zainstaluj nowe oprogramowanie).

- W polu **Work with:** (Praca z:) wprowadź `http://dl.microsoft.com/eclipse` i naciśnij klawisz Enter.

- Następnie zaznacz pole wyboru obok pozycji **Azure Toolkit for Java** i usuń zaznaczenie pola wyboru **Contact all update sites during install to find required software** (Skontaktuj się ze wszystkimi witrynami aktualizacji podczas instalacji, aby znaleźć wymagane oprogramowanie). Następnie wybierz przycisk Next (Dalej).

## <a name="create-a-linux-virtual-machine"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz nowy plik o nazwie `AzureApp.java` w katalogu `src/main/java/com/fabirkam` projektu i wklej go w następującym bloku kodu. Zaktualizuj zmienne `userName` i `sshKey` przy użyciu rzeczywistych wartości dla maszyny. Kod tworzy nową maszynę wirtualną z systemem Linux o nazwie `testLinuxVM` w grupie zasobów `sampleResourceGroup` działającej w regionie platformy Azure Wschodnie stany USA.

```java
package com.fabrikam;

import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.compute.VirtualMachine;
import com.microsoft.azure.management.compute.KnownLinuxVirtualMachineImage;
import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
import com.microsoft.azure.management.appservice.WebApp;
import com.microsoft.azure.management.storage.StorageAccount;
import com.microsoft.azure.management.storage.SkuName;
import com.microsoft.azure.management.storage.StorageAccountKey;
import com.microsoft.azure.management.sql.SqlDatabase;
import com.microsoft.azure.management.sql.SqlServer;
import com.microsoft.azure.management.resources.fluentcore.arm.Region;
import com.microsoft.azure.management.resources.fluentcore.utils.SdkContext;

import com.microsoft.rest.LogLevel;

import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;

import java.io.File;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

public class AzureApp {

    public static void main(String[] args) {

        final String userName = "YOUR_VM_USERNAME";
        final String sshKey = "YOUR_PUBLIC_SSH_KEY";

        try {

            // use the properties file with the service principal information to authenticate
            // change the name of the environment variable if you used a different name in the previous step
            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            // create a Ubuntu virtual machine in a new resource group 
            VirtualMachine linuxVM = azure.virtualMachines().define("testLinuxVM")
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup("sampleVmResourceGroup")
                    .withNewPrimaryNetwork("10.0.0.0/24")
                    .withPrimaryPrivateIPAddressDynamic()
                    .withoutPrimaryPublicIPAddress()
                    .withPopularLinuxImage(KnownLinuxVirtualMachineImage.UBUNTU_SERVER_16_04_LTS)
                    .withRootUsername(userName)
                    .withSsh(sshKey)
                    .withUnmanagedDisks()
                    .withSize(VirtualMachineSizeTypes.STANDARD_D3_V2)
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
}
```

Uruchom przykład z wiersza polecenia:

```shell
mvn compile exec:java
```

Niektóre odpowiedzi i żądania REST będą widoczne w konsoli programu, ponieważ zestaw SDK wykonuje bazowe wywołania do interfejsu API REST platformy Azure w celu skonfigurowania maszyny wirtualnej i jej zasobów. Po zakończeniu działania programu sprawdź maszynę wirtualną w subskrypcji przy użyciu interfejsu wiersza polecenia platformy Azure 2.0:

```azurecli-interactive
az vm list --resource-group sampleVmResourceGroup
```

Po sprawdzeniu, czy kod działa, użyj interfejsu wiersza polecenia, aby usunąć maszynę wirtualną i jej zasoby.

```azurecli-interactive
az group delete --name sampleVmResourceGroup
```

## <a name="deploy-a-web-app-from-a-github-repo"></a>Wdrażanie aplikacji internetowej z repozytorium GitHub

Zastąp metodę główną w pliku `AzureApp.java` poniższą metodą, aktualizując zmienną `appName` do unikatowej wartości przed uruchomieniem kodu. Ten kod wdraża aplikację internetową z gałęzi `master` w publicznym repozytorium GitHub do nowej [aplikacji internetowej usługi Azure App Service](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) działającej w warstwie cenowej Bezpłatna.

```java
    public static void main(String[] args) {
        try {

            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            final String appName = "YOUR_APP_NAME";

            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            WebApp app = azure.webApps().define(appName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleWebResourceGroup")
                    .withNewWindowsPlan(PricingTier.FREE_F1)
                    .defineSourceControl()
                    .withPublicGitRepository(
                            "https://github.com/Azure-Samples/app-service-web-java-get-started")
                    .withBranch("master")
                    .attach()
                    .create();

        } catch (Exception e) {
            System.out.println(e.getMessage());
            e.printStackTrace();
        }
    }
```

Uruchom kod tak jak poprzednio przy użyciu programu Maven:

```shell
mvn clean compile exec:java
```

Otwórz przeglądarkę wskazującą na aplikację przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az appservice web browse --resource-group sampleWebResourceGroup --name YOUR_APP_NAME
```

Po zweryfikowaniu wdrożenia usuń aplikację internetową i plan z subskrypcji.

```azurecli-interactive
az group delete --name sampleWebResourceGroup
```

## <a name="connect-to-an-azure-sql-database"></a>Łączenie z bazą danych Azure SQL Database

Zastąp bieżącą metodę główną w pliku `AzureApp.java` poniższym kodem, ustawiając wartość rzeczywistą dla zmiennej `dbPassword`.
Ten kod tworzy nową bazę danych SQL z regułą zapory zezwalającą na dostęp zdalny, a następnie nawiązuje połączenie z nią przy użyciu sterownika JBDC bazy danych SQL Database. 

```java
    public static void main(String args[])
    {
        // create the db using the management libraries
        try {
            final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
            Azure azure = Azure.configure()
                    .withLogLevel(LogLevel.BASIC)
                    .authenticate(credFile)
                    .withDefaultSubscription();

            final String adminUser = SdkContext.randomResourceName("db",8);
            final String sqlServerName = SdkContext.randomResourceName("sql",10);
            final String sqlDbName = SdkContext.randomResourceName("dbname",8);
            final String dbPassword = "YOUR_PASSWORD_HERE";


            SqlServer sampleSQLServer = azure.sqlServers().define(sqlServerName)
                            .withRegion(Region.US_EAST)
                            .withNewResourceGroup("sampleSqlResourceGroup")
                            .withAdministratorLogin(adminUser)
                            .withAdministratorPassword(dbPassword)
                            .withNewFirewallRule("0.0.0.0","255.255.255.255")
                            .create();

            SqlDatabase sampleSQLDb = sampleSQLServer.databases().define(sqlDbName).create();

            // assemble the connection string to the database
            final String domain = sampleSQLServer.fullyQualifiedDomainName();
            String url = "jdbc:sqlserver://"+ domain + ":1433;" +
                    "database=" + sqlDbName +";" +
                    "user=" + adminUser+ "@" + sqlServerName + ";" +
                    "password=" + dbPassword + ";" +
                    "encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;";

            // connect to the database, create a table and insert a entry into it
            Connection conn = DriverManager.getConnection(url);

            String createTable = "CREATE TABLE CLOUD ( name varchar(255), code int);";
            String insertValues = "INSERT INTO CLOUD (name, code ) VALUES ('Azure', 1);";
            String selectValues = "SELECT * FROM CLOUD";
            Statement createStatement = conn.createStatement();
            createStatement.execute(createTable);
            Statement insertStatement = conn.createStatement();
            insertStatement.execute(insertValues);
            Statement selectStatement = conn.createStatement();
            ResultSet rst = selectStatement.executeQuery(selectValues);

            while (rst.next()) {
                System.out.println(rst.getString(1) + " "
                        + rst.getString(2));
            }


        } catch (Exception e) {
            System.out.println(e.getMessage());
            System.out.println(e.getStackTrace().toString());
        }
    }
```

Uruchom przykład z wiersza polecenia:

```shell
mvn clean compile exec:java
```

Następnie wyczyść zasoby przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name sampleSqlResourceGroup
```

## <a name="write-a-blob-into-a-new-storage-account"></a>Pisanie obiektu blob na nowym koncie magazynu

Zastąp bieżącą metodę główną w pliku `AzureApp.java` poniższym kodem. Ten kod tworzy [konto usługi magazynu platformy Azure](https://docs.microsoft.com/azure/storage/storage-introduction), a następnie używa bibliotek usługi Azure Storage dla języka Java do tworzenia nowego pliku tekstowego w chmurze.

```java
public static void main(String[] args) {

    try {

        // use the properties file with the service principal information to authenticate
        // change the name of the environment variable if you used a different name in the previous step
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
                .withLogLevel(LogLevel.BASIC)
                .authenticate(credFile)
                .withDefaultSubscription();

        // create a new storage account
        String storageAccountName = SdkContext.randomResourceName("st",8);
        StorageAccount storage = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_WEST2)
                    .withNewResourceGroup("sampleStorageResourceGroup")
                    .create();

        // create a storage container to hold the file
        List<StorageAccountKey> keys = storage.getKeys();
        final String storageConnection = "DefaultEndpointsProtocol=https;"
                + "AccountName=" + storage.name()
                + ";AccountKey=" + keys.get(0).value()
                + ";EndpointSuffix=core.windows.net";

        CloudStorageAccount account = CloudStorageAccount.parse(storageConnection);
        CloudBlobClient serviceClient = account.createCloudBlobClient();

        // Container name must be lower case.
        CloudBlobContainer container = serviceClient.getContainerReference("helloazure");
        container.createIfNotExists();

        // Make the container public
        BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
        containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
        container.uploadPermissions(containerPermissions);

        // write a blob to the container
        CloudBlockBlob blob = container.getBlockBlobReference("helloazure.txt");
        blob.uploadText("hello Azure");

    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }
    }
}
```

Uruchom przykład z wiersza polecenia:

```shell
mvn clean compile exec:java
```

Plik `helloazure.txt` można wyszukać na koncie magazynu za pomocą witryny Azure Portal lub [Eksploratora usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs).

Wyczyść konto magazynu przy użyciu interfejsu wiersza polecenia:

```azurecli-interactive
az group delete --name sampleStorageResourceGroup
```

## <a name="explore-more-samples"></a>Dalsze przykłady

Aby dowiedzieć się więcej na temat sposobów używania bibliotek zarządzania platformy Azure dla języka Java do zarządzania zasobami i automatyzowania zadań, zobacz nasz przykładowy kod związany z [maszynami wirtualnymi](java-sdk-azure-virtual-machine-samples.md), [aplikacjami internetowymi](java-sdk-azure-web-apps-samples.md) i [bazą danych SQL](java-sdk-azure-sql-database-samples.md).

## <a name="reference-and-release-notes"></a>Dokumentacja i informacje o wersji

Dostępna jest [dokumentacja](https://docs.microsoft.com/java/api) dotycząca wszystkich pakietów.

## <a name="get-help-and-give-feedback"></a>Uzyskiwanie pomocy i przesyłanie opinii

Publikuj pytania do społeczności w witrynie [Stack Overflow](https://stackoverflow.com/questions/tagged/azure+java). Zgłaszaj usterki i otwieraj problemy dotyczące bibliotek platformy Azure dla języka Java w [usłudze GitHub projektu](https://github.com/Azure/azure-sdk-for-java).
