---
title: Wdrażanie aplikacji Spring/Tomcat w usłudze App Service za pomocą usługi Azure Database for MySQL
description: Kompleksowy samouczek dotyczący usługi App Service języka Java w programie MySQL
author: KarlErickson
ms.author: karler
ms.date: 11/12/2019
ms.service: app-service
ms.topic: article
ms.openlocfilehash: 4daf41e1cf13d57a42230cd8ed6af4a2258e5e01
ms.sourcegitcommit: 9f9f5c51472dbdd7b9304b02364ed136dcf81f1c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139304"
---
# <a name="deploy-a-spring-app-to-app-service-with-mysql"></a>Wdrażanie aplikacji Spring w usłudze App Service za pomocą programu MySQL

W tym samouczku omówimy proces kompilowania, konfigurowania, wdrażania, rozwiązywania problemów i skalowania aplikacji internetowych w języku Java w usłudze App Service dla systemu Linux.

Ten samouczek jest oparty na popularnej przykładowej aplikacji Spring PetClinic. W tym temacie przetestujesz lokalnie wersję HSQLDB aplikacji, a następnie wdrożysz ją w usłudze [Azure App Service](/azure/app-service/containers). Następnie skonfigurujesz i wdrożysz wersję korzystającą z usługi [Azure Database for MySQL](/azure/mysql). Na koniec dowiesz się, jak uzyskać dostęp do dzienników aplikacji i skalować ją w poziomie, zwiększając liczbę procesów roboczych uruchamiających aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/overview)
* [Java 8](http://java.oracle.com/)
* [Maven 3](http://maven.apache.org/)
* [Usługa Git](https://github.com/)
* [Tomcat](https://tomcat.apache.org/download-80.cgi)
* [Interfejs wiersza polecenia programu MySQL](http://dev.mysql.com/downloads/mysql/)

## <a name="get-the-sample"></a>Pobieranie przykładu

Aby rozpocząć pracę z przykładową aplikacją, sklonuj i przygotuj repozytorium źródłowe, używając poniższych poleceń.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux.git
cd e2e-java-experience-in-app-service-linux
yes | cp -rf .prep/* .
```

## <a name="build-and-run-the-hsqldb-sample-locally"></a>Lokalne kompilowanie i uruchamianie przykładu HSQLDB

Najpierw przetestujemy przykład lokalnie, używając wersji HSQLDB jako bazy danych.

Przejdź do wersji HSQLDB przykładu, a następnie skompiluj ją.

```bash
cd initial-hsqldb/spring-framework-petclinic
mvn package
```

Następnie ustaw zmienną środowiskową TOMCAT_HOME na lokalizację instalacji rozwiązania Tomcat.

```bash
export TOMCAT_HOME=<Tomcat install directory>
```

Następnie zaktualizuj plik *pom.xml*, aby skonfigurować narzędzie Maven na potrzeby wdrożenia pliku WAR rozwiązania Tomcat. Dodaj poniższy kod XML jako element podrzędny istniejącego elementu `<plugins>`. W razie potrzeby zmień `1.7.7` na bieżącą wersję wtyczki [Cargo Maven 2 Plugin](https://mvnrepository.com/artifact/org.codehaus.cargo/cargo-maven2-plugin).

```xml
<plugin>
    <groupId>org.codehaus.cargo</groupId>
    <artifactId>cargo-maven2-plugin</artifactId>
    <version>1.7.7</version>
    <configuration>
        <container>
            <containerId>tomcat8x</containerId>
            <type>installed</type>
            <home>${TOMCAT_HOME}</home>
        </container>
        <configuration>
            <type>existing</type>
            <home>${TOMCAT_HOME}</home>
        </configuration>
        <deployables>
            <deployable>
                <groupId>${project.groupId}</groupId>
                <artifactId>${project.artifactId}</artifactId>
                <type>war</type>
                <properties>
                    <context>/</context>
                </properties>
            </deployable>
        </deployables>
    </configuration>
</plugin>
```

Gdy konfiguracja będzie gotowa, możesz wdrożyć aplikację lokalnie w rozwiązaniu Tomcat.

```bash
mvn cargo:deploy
```

Następnie uruchom rozwiązanie Tomcat.

```bash
${TOMCAT_HOME}/bin/catalina.sh run
```

Teraz możesz przejść do witryny [http://localhost:8080](http://localhost:8080) w przeglądarce, aby zobaczyć uruchomioną aplikację i zorientować się, jak ona działa. Po zakończeniu naciśnij kombinację klawiszy Ctrl+C w wierszu polecenia powłoki Bash, aby zatrzymać rozwiązanie Tomcat.

## <a name="deploy-to-azure-app-service"></a>Wdrażanie w usłudze Azure App Service

Teraz, gdy już wiesz, jak aplikacja działa lokalnie, wdrożymy ją na platformie Azure.

Najpierw ustaw poniższe zmienne środowiskowe.

```bash
export RESOURCEGROUP_NAME=<resource group>
export WEBAPP_NAME=<web app>
export WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
export REGION=<region>
```

Narzędzie Maven będzie używać tych wartości do tworzenia zasobów platformy Azure z podanymi nazwami. Korzystając ze zmiennych środowiskowych, można zachować wpisy tajne konta poza plikami projektu.

Następnie zaktualizuj plik *pom.xml*, aby skonfigurować narzędzie Maven na potrzeby wdrożenia na platformie Azure. Po dodanym wcześniej elemencie `<plugin>` dodaj poniższy kod XML. W razie potrzeby zmień `1.9.0` na bieżącą wersję [wtyczki Maven dla usługi Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.0</version>
    <configuration>
        <schemaVersion>v2</schemaVersion>
        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>
        <runtime>
            <os>linux</os>
            <javaVersion>jre8</javaVersion>            
            <webContainer>TOMCAT 8.5</webContainer>
        </runtime>
    </configuration>
</plugin>
```

Następnie zaloguj się do platformy Azure.

```azurecli
az login
```

W dalszej kolejności wdróż aplikację w usłudze App Service dla systemu Linux.

```bash
mvn azure-webapp:deploy
```

Teraz możesz przejść do witryny `https://<app-name>.azurewebsites.net` (po zastąpieniu `<app-name>`), aby wyświetlić uruchomioną aplikację.

## <a name="set-up-azure-database-for-mysql"></a>Konfigurowanie usługi Azure Database for MySQL

Następnie zaczniemy korzystać z programu MySQL, zamiast z przykładu HSQLDB. Utworzymy wystąpienie serwera MySQL na platformie Azure i dodamy bazę danych, po czym zaktualizujemy konfigurację aplikacji przy użyciu nowych informacji o połączeniu z bazą danych.

Najpierw ustaw poniższe zmienne środowiskowe do użycia w dalszych czynnościach.

```bash
export MYSQL_SERVER_NAME=<server>
export MYSQL_SERVER_FULL_NAME=${MYSQL_SERVER_NAME}.mysql.database.azure.com
export MYSQL_SERVER_ADMIN_LOGIN_NAME=<admin>
export MYSQL_SERVER_ADMIN_PASSWORD=<password>
export MYSQL_DATABASE_NAME=<database>
export DOLLAR=\$
```

Następnie utwórz i zainicjuj serwer bazy danych. Jako konfiguracji początkowej użyj polecenia [az mysql up](/cli/azure/ext/db-up/mysql?view=azure-cli-latest#ext-db-up-az-mysql-up). Następnie użyj polecenia [az mysql server configuration set](/cli/azure/mysql/server/configuration?view=azure-cli-latest#az-mysql-server-configuration-set), aby zwiększyć limit czasu połączenia i ustawić strefę czasową serwera.

```azurecli
az mysql up \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server-name ${MYSQL_SERVER_NAME} \
    --database-name ${MYSQL_DATABASE_NAME} \
    --admin-user ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
    --admin-password ${MYSQL_SERVER_ADMIN_PASSWORD}

az mysql server configuration set --name wait_timeout \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value 2147483

az mysql server configuration set --name time_zone \
    --resource-group ${RESOURCEGROUP_NAME} \
    --server ${MYSQL_SERVER_NAME} --value -8:00
```

W dalszej kolejności utwórz bazę danych za pomocą interfejsu wiersza polecenia programu MySQL.

```bash
mysql -u ${MYSQL_SERVER_ADMIN_LOGIN_NAME} \
 -h ${MYSQL_SERVER_FULL_NAME} -P 3306 -p
```

W interfejsie wiersza polecenia programu MySQL uruchom poniższe polecenie, zastępując element `<database name>` wartością określoną wcześniej dla zmiennej środowiskowej `MYSQL_DATABASE_NAME`.

```console
CREATE DATABASE <database name>;
```

Program MySQL jest teraz gotowy do użycia.

## <a name="configure-the-app-for-mysql"></a>Konfigurowanie aplikacji dla programu MySQL

W dalszej kolejności dodamy informacje o połączeniu do wersji aplikacji dla programu MySQL, a następnie wdrożymy ją w usłudze App Service.

Najpierw przejdź do poprawnego folderu w wierszu polecenia powłoki Bash.

```bash
cd ../../initial-mysql/spring-framework-petclinic
```

Następnie zaktualizuj plik *pom.xml*, aby program MySQL stał się aktywną konfiguracją. Usuń element `<activation>` z profilu HSQLDB i umieść go w profilu MySQL, jak pokazano poniżej. Pozostała część fragmentu kodu przedstawia dotychczasową konfigurację. Zwróć uwagę, jak narzędzie Maven konfiguruje dostęp do programu MySQL, używając ustawionych wcześniej zmiennych środowiskowych.

```xml
<profile>
    <id>MySQL</id>
    <activation>
        <activeByDefault>true</activeByDefault>
    </activation>
    <properties>
        <db.script>mysql</db.script>
        <jpa.database>MYSQL</jpa.database>
        <jdbc.driverClassName>com.mysql.jdbc.Driver</jdbc.driverClassName>
        <jdbc.url>jdbc:mysql://${DOLLAR}{MYSQL_SERVER_FULL_NAME}:3306/${DOLLAR}{MYSQL_DATABASE_NAME}?useUnicode=true</jdbc.url>
        <jdbc.username>${DOLLAR}{MYSQL_SERVER_ADMIN_LOGIN_NAME}</jdbc.username>
        <jdbc.password>${DOLLAR}{MYSQL_SERVER_ADMIN_PASSWORD}</jdbc.password>
    </properties>
    ...
</profile>
```

Następnie zaktualizuj plik *pom.xml*, aby skonfigurować narzędzie Maven na potrzeby wdrożenia na platformie Azure i użycia w programie MySQL. Po dodanym wcześniej elemencie `<plugin>` dodaj poniższy kod XML. W razie potrzeby zmień `1.9.0` na bieżącą wersję [wtyczki Maven dla usługi Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

```xml
<plugin>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-webapp-maven-plugin</artifactId>
    <version>1.9.0</version>
    <configuration>

        <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
        <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
        <appName>${WEBAPP_NAME}</appName>
        <region>${REGION}</region>

        <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>

        <appSettings>
            <property>
                <name>MYSQL_SERVER_FULL_NAME</name>
                <value>${MYSQL_SERVER_FULL_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_LOGIN_NAME</name>
                <value>${MYSQL_SERVER_ADMIN_LOGIN_NAME}</value>
            </property>
            <property>
                <name>MYSQL_SERVER_ADMIN_PASSWORD</name>
                <value>${MYSQL_SERVER_ADMIN_PASSWORD}</value>
            </property>
            <property>
                <name>MYSQL_DATABASE_NAME</name>
                <value>${MYSQL_DATABASE_NAME}</value>
            </property>
        </appSettings>

    </configuration>
</plugin>
```

Następnie skompiluj aplikację i przetestuj ją lokalnie, wdrażając ją i uruchamiając w rozwiązaniu Tomcat.

```bash
mvn package
mvn cargo:deploy
${TOMCAT_HOME}/bin/catalina.sh run
```

Teraz możesz wyświetlić aplikację lokalnie w witrynie [http://localhost:8080](http://localhost:8080). Aplikacja będzie wyglądać i zachowywać się tak samo jak wcześniej, ale zamiast przykładu HSQLDB będzie używała usługi Azure Database for MySQL. Po zakończeniu naciśnij kombinację klawiszy Ctrl+C w wierszu polecenia powłoki Bash, aby zatrzymać rozwiązanie Tomcat.

Na koniec wdróż aplikację w usłudze App Service.

```bash
mvn azure-webapp:deploy
```

Teraz możesz przejść do witryny `https://<app-name>.azurewebsites.net`, aby wyświetlić uruchomioną aplikację korzystającą z usług App Service i Azure Database for MySQL.

## <a name="access-the-app-logs"></a>Dostęp do dzienników aplikacji

Jeśli musisz rozwiązać problemy, możesz przejrzeć dzienniki aplikacji. Aby otworzyć zdalny strumień dzienników na komputerze lokalnym, użyj poniższego polecenia.

```azurecli
az webapp log tail --name ${WEBAPP_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Po zakończeniu przeglądania dzienników naciśnij kombinację klawiszy CTRL+C, aby zatrzymać strumień.

Strumień dzienników jest również dostępny w witrynie `https://<app-name>.scm.azurewebsites.net/api/logstream`.

## <a name="scale-out"></a>Skalowanie w poziomie

Aby obsłużyć zwiększony ruch do aplikacji, możesz przeprowadzić skalowanie w poziomie do wielu wystąpień, używając poniższego polecenia.

```azurecli
az appservice plan update --number-of-workers 2 \
    --name ${WEBAPP_PLAN_NAME} \
    --resource-group ${RESOURCEGROUP_NAME}
```

Gratulacje! Aplikacja internetowa w języku Java została skompilowana i przeskalowana przy użyciu rozwiązań Spring, JSP, Spring Data, Hibernate, JDBC, App Service dla systemu Linux i Azure Database for MySQL.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich sekcjach utworzono zasoby platformy Azure w grupie zasobów. Jeśli te zasoby nie będą raczej używane w przyszłości, usuń grupę zasobów, uruchamiając poniższe polecenie.

```azurecli
az group delete --name ${RESOURCEGROUP_NAME}
```

## <a name="next-steps"></a>Następne kroki

W dalszej kolejności zapoznaj się z innymi opcjami konfiguracji i ciągłej integracji/ciągłego wdrażania dostępnymi dla języka Java w usłudze App Service.

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Java systemu Linux dla usługi Azure App Service](/azure/app-service/containers/configure-language-java)
> [!div class="nextstepaction"]
> [Kompilowanie i wdrażanie aplikacji internetowej w języku Java przy użyciu usługi Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp?view=azure-devops&tabs=java-tomcat)
> [!div class="nextstepaction"]
> [Wdrażanie w usłudze Azure App Service przy użyciu wtyczki Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)
