---
title: Jak używać platformy Spring i usługi Cosmos DB z usługą App Service w systemie Linux
description: W tym artykule omówimy proces kompilowania, konfigurowania, wdrażania, rozwiązywania problemów i skalowania aplikacji internetowych w języku Java w usłudze Azure App Service dla systemu Linux.
documentationcenter: java
ms.reviewer: joshuapa
ms.date: 4/24/2019
ms.service: cosmos-db
ms.topic: article
ms.openlocfilehash: 53bc7d9ebd03fb122d58dd52145876e698dc751f
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001118"
---
# <a name="how-to-use-spring-and-cosmos-db-with-app-service-on-linux"></a>Jak używać platformy Spring i usługi Cosmos DB z usługą App Service w systemie Linux

W tym artykule omówimy proces kompilowania, konfigurowania, wdrażania, rozwiązywania problemów i skalowania aplikacji internetowych w języku Java w usłudze Azure App Service dla systemu Linux.

Zademonstrujemy sposób użycia następujących składników:

- [Spring Boot Starter z interfejsem API języka SQL w usłudze Azure Cosmos DB](configure-spring-boot-starter-java-app-with-cosmos-db.md)
- [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-introduction)
- [App Service dla systemu Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać czynności opisane w tym artykule, wymagane są:

- Aby wdrożyć w chmurze aplikację internetową w języku Java, potrzebujesz subskrypcji platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub założyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Interfejs wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Java 8 JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)
- [Maven 3](http://maven.apache.org/)

## <a name="clone-the-sample-java-web-app-repository"></a>Klonowanie przykładowego repozytorium aplikacji internetowych w języku Java

W tym ćwiczeniu będziesz używać aplikacji Spring Todo, która jest aplikacją języka Java utworzoną przy użyciu narzędzi [Spring Boot](https://spring.io/projects/spring-boot), [Spring Data for Cosmos DB](https://docs.microsoft.com/azure/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) i [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-introduction).
1. Sklonuj aplikację Spring Todo i skopiuj zawartość folderu **.prep**, aby zainicjować projekt:

    W przypadku środowiska powłoki Bash:
    ```bash
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    yes | cp -rf .prep/* .
    ```

    W przypadku systemu Windows:
    ```cmd
    git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
    cd e2e-java-experience-in-app-service-linux-part-2
    xcopy .prep /f /s /e /y
    ```

2. Zmień katalog na następujący folder w sklonowanym repozytorium:

   ```bash
   cd initial\spring-todo-app
   ```

## <a name="create-an-azure-cosmos-db-from-azure-cli"></a>Tworzenie usługi Azure Cosmos DB z poziomu interfejsu wiersza polecenia platformy Azure

Poniższa procedura umożliwia utworzenie bazy danych Azure Cosmos przy użyciu interfejsu wiersza polecenia.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i ustaw identyfikator subskrypcji.

    ```bash
    az login
    ```

2. Ustaw identyfikator subskrypcji, jeśli to konieczne.

    ```bash
    az account set -s <your-subscription-id>
    ```

3. Utwórz grupę zasobów platformy Azure i zanotuj jej nazwę do późniejszego użycia.

    ```bash
    az group create -n <your-azure-group-name> \
    -l <your-resource-group-region>
    ```

4. Utwórz bazę danych Cosmos DB i określ jej typ jako GlobalDocumentDB.
Nazwa bazy danych Cosmos DB musi zawierać tylko małe litery. Pamiętaj, aby zanotować pole `documentEndpoint` w odpowiedzi. Będzie ono potrzebne później.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
     ```

5. Pobierz klucze usługi Azure Cosmos DB, zapisz wartość `primaryMasterKey` do późniejszego użycia.

    ```bash
    az cosmosdb keys list -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="build-and-run-the-app-locally"></a>Lokalne kompilowanie i uruchamianie aplikacji

Poniższa procedura umożliwia uruchomienie aplikacji na komputerze deweloperskim.

1. W wybranej konsoli skonfiguruj zmienne środowiskowe widoczne w poniższych sekcjach kodu, używając informacji o połączeniu platformy Azure i usługi Cosmos DB, które zostały zebrane wcześniej w tym artykule. Musisz podać unikatową nazwę zmiennej **WEBAPP_NAME** i wartość zmiennej **REGION**.

W przypadku systemu Linux (powłoka Bash):

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

W przypadku systemu Windows (wiersz polecenia):
```cmd
set COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
set COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
set COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>
set RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
set WEBAPP_NAME=<put-your-Webapp-name-here>
set REGION=<put-your-REGION-here>
```

> [!NOTE]
> Jeśli chcesz aprowizować te zmienne za pomocą skryptu, w katalogu .prep znajduje się szablon dla powłoki Bash, który możesz skopiować, aby używać go jako punktu początkowego.

2. Zmień katalog na następujący:

    ```bash
    cd initial/spring-todo-app
    ``` 
 
3. Uruchom lokalnie aplikację Spring Todo za pomocą następującego polecenia:

    ```bash
    mvn package spring-boot:run
    ```

4. Po uruchomieniu aplikacji możesz sprawdzić poprawność wdrożenia, uzyskując dostęp do aplikacji Spring Todo tutaj: [http://localhost:8080/](http://localhost:8080/).

 ![Aplikacja Spring działająca w środowisku lokalnym][SCDB01]

## <a name="deploy-to-app-service-linux"></a>Wdrażanie w usłudze App Service w systemie Linux

Poniższa procedura służy do wdrażania aplikacji w systemie Linux na platformie Azure.

1. Otwórz plik pom.xml skopiowany wcześniej do katalogu **initial/spring-todo-app** repozytorium. Upewnij się, że zawiera on [wtyczkę narzędzia Maven dla usługi Azure App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md), która jest widoczna w poniższym pliku pom.xml. Jeśli wersja nie jest ustawiona na **1.6.0**, zaktualizuj wartość.

```xml
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.6.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

2. Wdrażanie w środowisku Java SE w usłudze App Service dla systemu Linux

    ```bash
    mvn azure-webapp:deploy
    ```

```bash
// Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.6.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesnt exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlan11111111-1111-1111'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

3. Przejdź do aplikacji internetowej działającej w środowisku Java SE w usłudze App Service dla systemu Linux:

    ```bash
    https://<WEBAPP_NAME>.azurewebsites.net
    ```

![Aplikacja Spring działająca w usłudze App Service w systemie Linux][SCDB02]

## <a name="troubleshoot-spring-todo-app-on-azure-by-viewing-logs"></a>Rozwiązywanie problemów z aplikacją Spring Todo na platformie Azure z pomocą przeglądania dzienników

Poniższa procedura otwiera pliki dzienników na platformie Azure.

1. Skonfiguruj dzienniki dla wdrożonej aplikacji internetowej Java w usłudze Azure App Service w systemie Linux:

    ```bash
    az webapp log config --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME} \
     --web-server-logging filesystem
    ```
2. Otwórz zdalny strumień dzienników aplikacji internetowej Java z poziomu komputera lokalnego:

    ```bash
    az webapp log tail --name ${WEBAPP_NAME} \
     --resource-group ${RESOURCEGROUP_NAME}
     ```

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers needs non existent service dev
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======


```

3. Po zakończeniu możesz porównać wyniki z kodem w witrynie [e2e-java-experience-in-app-service-linux-part-2/complete](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2/tree/master/complete).


## <a name="scale-out-the-spring-todo-app"></a>Skalowanie w poziomie aplikacji Spring Todo

Aby przeskalować aplikację, wykonaj poniższą procedurę.

1. Skalowanie aplikacji internetowej w języku Java przy użyciu interfejsu wiersza polecenia platformy Azure:

    ```bash
    az appservice plan update --number-of-workers 2 \
      --name ${WEBAPP_PLAN_NAME} \
      --resource-group ${RESOURCEGROUP_NAME}
    ```

## <a name="next-steps"></a>Następne kroki

- [Java in App Service Linux dev guide](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-java) (Przewodnik dla deweloperów dotyczący języka Java w usłudze App Service dla systemu Linux)
- [Azure for Java Developers](https://docs.microsoft.com/azure/java/) (Platforma Azure dla deweloperów języka Java) Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Wdrażanie aplikacji Spring Boot w usłudze Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Aby uzyskać więcej informacji na temat korzystania z platformy Azure przy użyciu języka Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] oraz [Praca z narzędziami Azure DevOps i językiem Java].

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem <https://github.com/spring-guides/> udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Azure Cosmos DB Documentation]: /azure/cosmos-db/
[Azure dla deweloperów języka Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data for Azure Cosmos DB SQL API]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Spring Boot Document DB Starter for Azure]:https://github.com/Microsoft/azure-spring-boot-starters/tree/master/azure-documentdb-spring-boot-starter-sample
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Praca z narzędziami Azure DevOps i językiem Java]: https://azure.microsoft.com/services/devops/java/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/

<!-- IMG List -->

[SCDB01]: ./media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB01.png
[SCDB02]: ./media/configure-spring-app-with-cosmos-db-on-app-service-linux/SCDB02.png
