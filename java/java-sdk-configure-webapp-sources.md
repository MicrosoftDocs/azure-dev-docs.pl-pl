---
title: Konfigurowanie wdrożeń aplikacji internetowych przy użyciu języka Java | Microsoft Docs
description: Przykładowy kod w języku Java służący do konfigurowania wdrożeń Azure App Service usługi Git lub serwera FTP przy użyciu zestawu Azure SDK dla języka Java
author: rloutlaw
ms.assetid: 833e9c78-1e50-4c23-a611-f73a2f0c2983
ms.topic: article
ms.date: 03/30/2017
ms.openlocfilehash: e8cda5ca26b57e7c64d577fb5542295c930f5623
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002467"
---
# <a name="configure-azure-app-service-deployment-sources-from-your-java-applications"></a>Konfigurowanie źródeł wdrożenia usługi Azure App Service z poziomu aplikacji Java

[W tym przykładzie](https://github.com/Azure-Samples/compute-java-create-virtual-machines-across-regions-in-parallel) kod jest wdrażany w czterech aplikacjach w ramach jednego planu usługi [Azure App Service](https://docs.microsoft.com/azure/app-service/) przy użyciu różnych źródeł wdrożenia.

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Utwórz [plik uwierzytelniania](https://github.com/Azure/azure-sdk-for-java/blob/master/AUTH.md) i ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na pełną ścieżkę do pliku na komputerze. Następnie uruchom polecenie:

```
git clone https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps.git
cd app-service-java-configure-deployment-sources-for-web-apps
mvn clean compile exec:java
```

Zapoznaj się z [kompletnym przykładowym kodem w witrynie GitHub](https://github.com/Azure-Samples/app-service-java-configure-deployment-sources-for-web-apps/blob/master/src/main/java/com/microsoft/azure/management/appservice/samples/ManageWebAppSourceControl.java).

## <a name="authenticate-with-azure"></a>Uwierzytelnianie na platformie Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)]

## <a name="create-a-app-service-app-running-apache-tomcat"></a>Tworzenie aplikacji App Service z uruchomionym serwerem Apache Tomcat

```java
// create a new Standard app service plan and create a single Java 8/Tomcat 8 app in it
WebApp app1 = azure.webApps().define(app1Name)
             .withNewResourceGroup(rgName)
             .withNewAppServicePlan(planName)
             .withRegion(Region.US_WEST)
             .withPricingTier(AppServicePricingTier.STANDARD_S1)
             .withJavaVersion(JavaVersion.JAVA_8_NEWEST)
             .withWebContainer(WebContainer.TOMCAT_8_0_NEWEST)
             .create();
```

Metody `withJavaVersion()` i `withWebContainer()` umożliwiają skonfigurowanie usługi App Service pod kątem obsługi żądań HTTP przy użyciu środowiska Tomcat 8.

## <a name="deploy-a-java-application-using-ftp"></a>Wdrażanie aplikacji Java przy użyciu serwera FTP
```java
// pass the PublishingProfile that contains FTP information to a helper method 
uploadFileToFtp(app1.getPublishingProfile(), "helloworld.war", 
      ManageWebAppSourceControl.class.getResourceAsStream("/helloworld.war"));

// Use the FTP classes in the Apache Commons library to connect to Azure using 
// the information from the PublishingProfile
private static void uploadFileToFtp(PublishingProfile profile, String fileName, InputStream file) throws Exception {
        FTPClient ftpClient = new FTPClient();
        String[] ftpUrlSegments = profile.ftpUrl().split("/", 2);
        String server = ftpUrlSegments[0];
        // Tomcat will deploy WAR files uploaded to this directory.
        String path = "./site/wwwroot/webapps"; 

        // FTP the build WAR to Azure
        ftpClient.connect(server);
        ftpClient.login(profile.ftpUsername(), profile.ftpPassword());
        ftpClient.setFileType(FTP.BINARY_FILE_TYPE);
        ftpClient.changeWorkingDirectory(path);
        ftpClient.storeFile(fileName, file);
        ftpClient.disconnect();
}
```

Ten kod umożliwia przekazanie pliku WAR do katalogu `/site/wwwroot/webapps`. Domyślnie środowisko Tomcat wdraża pliki WAR umieszczone w tym katalogu w usłudze App Service.

## <a name="deploy-a-java-application-from-a-local-git-repo"></a>Wdrażanie aplikacji Java z lokalnego repozytorium Git

```java
// get the publishing profile from the App Service webapp
PublishingProfile profile = app2.getPublishingProfile();

// create a new Git repo in the sample directory under src/main/resources 
Git git = Git
    .init()
    .setDirectory(new File(ManageWebAppSourceControl.class.getResource("/azure-samples-appservice-helloworld/").getPath()))
    .call();
git.add().addFilepattern(".").call();
// add the files in the sample app to an initial commit
git.commit().setMessage("Initial commit").call(); 

// push the commit using the Azure Git remote URL and credentials in the publishing profile
PushCommand command = git.push();
command.setRemote(profile.gitUrl()); 
command.setCredentialsProvider(new UsernamePasswordCredentialsProvider(profile.gitUsername(), profile.gitPassword()));
command.setRefSpecs(new RefSpec("master:master")); 
command.setForce(true);
command.call();
```      

Ten kod umożliwia utworzenie nowego repozytorium Git w folderze `src/main/resources/azure-samples-appservice-helloworld` przy użyciu bibliotek [JGit](https://eclipse.org/jgit/). Wszystkie pliki w folderze są następnie dodawane do początkowego zatwierdzenia, które jest wypychane na platformę Azure przy użyciu informacji o wdrożeniu Git pochodzących z interfejsu `PublishingProfile` aplikacji internetowej. 

>[!NOTE]
> Układ plików w repozytorium określa sposób wdrażania plików w katalogu `/site/wwwroot/` w usłudze Azure App Service.

## <a name="deploy-an-application-from-a-public-git-repo"></a>Wdrażanie aplikacji z publicznego repozytorium Git

```java
// deploy a .NET sample app from a public GitHub repo into a new webapp
WebApp app3 = azure.webApps().define(app3Name)
                .withNewResourceGroup(rgName)
                .withExistingAppServicePlan(plan)
                .defineSourceControl()
                .withPublicGitRepository(
                   "https://github.com/Azure-Samples/app-service-web-dotnet-get-started")
                .withBranch("master")
                .attach()
                .create();
 ```

 Środowisko uruchomieniowe App Service automatycznie kompiluje i wdraża projekt .NET przy użyciu najnowszego kodu w gałęzi `master` repozytorium.

## <a name="continuous-deployment-from-a-github-repo"></a>Ciągłe wdrażanie z repozytorium GitHub

```java
// deploy the application whenever you push a new commit or merge a pull request into your master branch
WebApp app4 = azure.webApps()
                    .define(app4Name)
                    .withExistingResourceGroup(rgName)
                    .withExistingAppServicePlan(plan)
                    // Uncomment the following lines to turn on continuous deployment scenario
                    //.defineSourceControl()
                    //    .withContinuouslyIntegratedGitHubRepository("username", "reponame")
                    //    .withBranch("master")
                    //    .withGitHubAccessToken("YOUR GITHUB PERSONAL TOKEN")
                    //    .attach()
                    .create();
```  

W serwisie GitHub są używane wartości `username` i `reponame`. [Utwórz osobisty token dostępu GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) z uprawnieniami do odczytu repozytorium i przekaż go do metody `withGitHubAccessToken`. 


## <a name="sample-explanation"></a>Wyjaśnienie działania przykładu

Pierwsza aplikacja zostaje utworzona przy użyciu języka Java 8 i środowiska Tomcat 8 uruchomionego w nowo utworzonym planie usługi App Service w warstwie [Standardowa](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview). Plik WAR jest przesyłany na serwer FTP przy użyciu informacji zawartych w obiekcie `PublishingProfile`, a następnie wdrażany w środowisku Tomcat.

Druga aplikacja jest również skonfigurowana jako aplikacja Java 8/Tomcat 8 i używa tego samego planu co pierwsza. Przy użyciu bibliotek JGit tworzone jest nowe repozytorium Git w folderze, który zawiera niespakowaną aplikację internetową Java w strukturze katalogów mapowanej na usługę App Service. Nowe zatwierdzenie dodaje pliki w folderze do nowego repozytorium Git, a usługa Git wypycha to zatwierdzenie na platformę Azure za pomocą zdalnego adresu URL i nazwy użytkownika/hasła, które są dostarczane przez interfejs `PublishingProfile` aplikacji internetowej.

Trzecia aplikacja nie jest skonfigurowana dla języka Java i środowiska Tomcat. Zamiast tego przykładowy kod .NET z publicznego repozytorium GitHub jest wdrażany bezpośrednio ze źródła.

Czwarta aplikacja wdraża kod w gałęzi master przy każdym wypchnięciu zmian lub scaleniu żądania ściągnięcia z gałęzią master repozytorium GitHub.

| Klasa używana w przykładzie | Uwagi
|-------|-------|
| [WebApp](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.webapp) | Utworzona na podstawie płynnego łańcucha `azure.webApps().define()....create()`. Tworzy aplikację internetową App Service wraz z wszystkimi wymaganymi zasobami. Większość metod wykonuje zapytania na obiekcie w celu uzyskania szczegółów konfiguracji, ale metody czasownikowe, takie jak `restart()`, zmieniają stan aplikacji internetowej.
| [WebContainer](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.webcontainer) | Klasa ze statycznymi publicznymi polami używanymi jako parametry metody `withWebContainer()` podczas definiowania wystąpienia WebApp z uruchomionym kontenerem internetowym Java. Udostępnia opcje dla wersji serwerów Jetty i Tomcat.
| [PublishingProfile](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.publishingprofile) | Uzyskiwana za pośrednictwem obiektu WebApp przy użyciu metody `getPublishingProfile()`. Zawiera informacje o wdrożeniach FTP i Git, w tym nazwę użytkownika i hasło używane we wdrożeniu (są one inne niż poświadczenia do konta platformy Azure lub jednostki usługi).
| [AppServicePlan](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.appserviceplan) | Zwracana przez metodę `azure.appServices().appServicePlans().getByResourceGroup()`. Dostępne metody umożliwiają sprawdzanie pojemności, warstwy i liczby aplikacji internetowych uruchomionych w planie.
| [AppServicePricingTier](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.PricingTier) | Klasa ze statycznymi publicznymi polami reprezentującymi warstwy usługi App Service. Służy do definiowania wbudowanej warstwy planu podczas tworzenia aplikacji za pomocą metody `withPricingTier()` lub bezpośrednio podczas definiowania planu za pośrednictwem metody `azure.appServices().appServicePlans().define()`.
| [JavaVersion](https://docs.microsoft.com/java/api/com.microsoft.azure.management.appservice.javaversion) | Klasa ze statycznymi publicznymi polami reprezentującymi wersje języka Java obsługiwane przez usługę App Service. Używana razem z metodą `withJavaVersion()` w łańcuchu `define()...create()` podczas tworzenia nowej aplikacji internetowej.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [next-steps](includes/java-next-steps.md)]