---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: ab70d4861cc619417f78cfc053d44b22dd35014f
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897576"
---
### <a name="create-a-docker-image-for-wildfly"></a>Tworzenie obrazu platformy Docker dla serwera WildFly

Aby można było utworzyć plik Dockerfile, konieczne są:

* Obsługiwany zestaw JDK
* Instalacja serwera WildFly
* Opcje środowiska uruchomieniowego maszyny JVM.
* Sposób przekazywania zmiennych środowiskowych (jeśli dotyczy).

Następnie można wykonać kroki opisane w poniższych sekcjach, jeśli mają zastosowanie. Jako punktu wyjścia dla pliku Dockerfile i aplikacji internetowej możesz użyć [repozytorium WildFly Container Quickstart](https://github.com/Azure/wildfly-container-quickstart).

1. [Konfigurowanie usługi KeyVault FlexVolume](#configure-keyvault-flexvolume)
2. [Konfigurowanie źródeł danych](#set-up-data-sources)
3. [Konfigurowanie zasobów JNDI](#set-up-jndi-resources)
4. [Przegląd konfiguracji serwera WildFly](#review-wildfly-configuration)

#### <a name="configure-keyvault-flexvolume"></a>Konfigurowanie usługi KeyVault FlexVolume

Utwórz usługę Azure KeyVault i wypełnij wszystkie niezbędne wpisy tajne. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](/azure/key-vault/quick-create-cli). Następnie skonfiguruj usługę [KeyVault FlexVolume](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md), aby udostępnić te wpisy tajne w zasobnikach.

Będzie trzeba też zaktualizować skrypt uruchamiania używany do ładowania początkowego serwera WildFly. Ten skrypt musi zaimportować certyfikaty do magazynu kluczy używanego przez serwer WildFly przed uruchomieniem serwera.

#### <a name="set-up-data-sources"></a>Konfigurowanie źródeł danych

Aby skonfigurować serwer WildFly do uzyskiwania dostępu do źródła danych, trzeba będzie dodać plik JAR sterownika JDBC do obrazu platformy Docker, a następnie wykonać odpowiednie polecenia interfejsu wiersza polecenia JBoss. Te polecenia muszą skonfigurować źródło danych podczas tworzenia obrazu platformy Docker.

Następujące kroki zawierają instrukcje dotyczące baz danych PostgreSQL, MySQL i SQL Server.

1. Pobierz sterownik JDBC dla bazy danych [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/) lub [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

    Rozpakuj pobrane archiwum, aby pobrać plik jar sterownika.

1. Utwórz plik o nazwie takiej jak `module.xml` i dodaj następujący znacznik. Zastąp element `<module name>` (łącznie z nawiasami ostrymi) wartością `org.postgres` dla bazy danych PostgreSQL, `com.mysql` dla MySQL lub `com.microsoft` dla SQL Server. Zastąp element `<JDBC .jar file path>` nazwą pliku jar z poprzedniego kroku, włącznie z pełną ścieżką do lokalizacji, w której zostanie umieszczony plik w obrazie platformy Docker, na przykład w `/opt/database`.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Utwórz plik o nazwie takiej jak `datasource-commands.cli` i dodaj następujący kod. Zastąp element `<JDBC .jar file path>` wartością użytą w poprzednim kroku. Zastąp element `<module file path>` nazwą pliku i ścieżką z poprzedniego kroku, na przykład `/opt/database/module.xml`.

    **PostgreSQL**

    ```console
    batch
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)
    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    reload
    run batch
    shutdown
    ```

    **MySQL**

    ```console
    batch
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)
    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter
    reload
    run batch
    shutdown
    ```

    **SQL Server**

    ```console
    batch
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>
    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)
    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter
    reload
    run batch
    shutdown
    ```

1. Zaktualizuj konfigurację źródła danych JTA dla swojej aplikacji:

    Otwórz plik `src/main/resources/META-INF/persistence.xml` dla swojej aplikacji i znajdź element `<jta-data-source>`. Zastąp jego zawartość w następujący sposób:

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

1. Dodaj następujące informacje do pliku `Dockerfile`, aby źródło danych było tworzone podczas tworzenia obrazu platformy Docker

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/database/datasource-commands.cli && \
    sleep 30
    ```

1. Określ wartość `DATABASE_CONNECTION_URL`, która ma być używana, ponieważ wartości te różnią się dla każdego serwera bazy danych i są inne niż wartości w witrynie Azure Portal. Pokazane tu formaty adresów URL są wymagane do użycia przez serwer WildFly:

    **PostgreSQL**

    ```console
    jdbc:postgresql://<database server name>:5432/<database name>?ssl=true
    ```

    **MySQL**

    ```console
    jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT
    ```

    **SQL Server**

    ```console
    jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

1. Podczas tworzenia pliku YAML wdrożenia na późniejszym etapie należy przekazać zmienne środowiskowe `DATABASE_CONNECTION_URL`, `DATABASE_SERVER_ADMIN_FULL_NAME` i `DATABASE_SERVER_ADMIN_PASSWORD` z odpowiednimi wartościami.

Aby uzyskać więcej informacji na temat konfigurowania łączności bazy danych z serwerem WildFly, zobacz [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) lub [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898).

#### <a name="set-up-jndi-resources"></a>Konfigurowanie zasobów JNDI

Aby skonfigurować każdy zasób JNDI, który musi być skonfigurowany na serwerze WildFly, zazwyczaj należy wykonać następujące kroki:

1. Pobierz niezbędne pliki JAR i skopiuj je do obrazu platformy Docker.
2. Utwórz plik *module.xml* serwera WildFly odwołujący się do tych plików JAR.
3. Utwórz dowolną konfigurację wymaganą przez konkretny zasób JNDI.
4. Utwórz skrypt interfejsu wiersza polecenia JBoss, który ma być używany podczas tworzenia przez platformę Docker w celu zarejestrowania zasobu JNDI.
5. Dodaj wszystko do pliku Dockerfile.
6. Przekaż odpowiednie zmienne środowiskowe w pliku YAML wdrożenia.

W poniższym przykładzie przedstawiono kroki wymagane do utworzenia zasobu JNDI na potrzeby łączności usługi JMS z usługą Azure Service Bus.

1. Pobierz dostawcę [Apache Qpid JMS](https://qpid.apache.org/components/jms/index.html)

    Rozpakuj pobrane archiwum, aby uzyskać pliki jar.

1. Utwórz plik o nazwie takiej jak `module.xml` i dodaj następujący kod w ścieżce `/opt/servicebus`. Upewnij się, że numery wersji plików JAR zgadzają się z nazwami plików JAR z poprzedniego kroku.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
     <resources>
      <resource-root path="proton-j-0.31.0.jar"/>
      <resource-root path="qpid-jms-client-0.40.0.jar"/>
      <resource-root path="slf4j-log4j12-1.7.25.jar"/>
      <resource-root path="slf4j-api-1.7.25.jar"/>
      <resource-root path="log4j-1.2.17.jar"/>
      <resource-root path="netty-buffer-4.1.32.Final.jar" />
      <resource-root path="netty-codec-4.1.32.Final.jar" />
      <resource-root path="netty-codec-http-4.1.32.Final.jar" />
      <resource-root path="netty-common-4.1.32.Final.jar" />
      <resource-root path="netty-handler-4.1.32.Final.jar" />
      <resource-root path="netty-resolver-4.1.32.Final.jar" />
      <resource-root path="netty-transport-4.1.32.Final.jar" />
      <resource-root path="netty-transport-native-epoll-4.1.32.Final-linux-x86_64.jar" />
      <resource-root path="netty-transport-native-kqueue-4.1.32.Final-osx-x86_64.jar" />
      <resource-root path="netty-transport-native-unix-common-4.1.32.Final.jar" />
      <resource-root path="qpid-jms-discovery-0.40.0.jar" />
     </resources>
     <dependencies>
      <module name="javax.api"/>
      <module name="javax.jms.api"/>
     </dependencies>
    </module>
    ```

1. Utwórz plik `jndi.properties` w ścieżce `/opt/servicebus`.

    ```console
    connectionfactory.${MDB_CONNECTION_FACTORY}=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}
    queue.${MDB_QUEUE}=${SB_QUEUE}
    topic.${MDB_TOPIC}=${SB_TOPIC}
    ```

1. Utwórz plik o nazwie takiej jak `servicebus-commands.cli` i dodaj następujący kod.

    ```console
    batch
    /subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)
    /system-property=property.mymdb.queue:add(value=myqueue)
    /system-property=property.connection.factory:add(value=java:global/remoteJMS/SBF)
    /subsystem=ee:list-add(name=global-modules, value={"name" => "org.jboss.genericjms.provider", "slot" =>"main"}
    /subsystem=naming/binding="java:global/remoteJMS":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/opt/servicebus/jndi.properties])
    /subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=${MDB_CONNECTION_FACTORY})
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value="java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/opt/servicebus/jndi.properties")
    /subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)
    /subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)
    run-batch
    reload
    shutdown
    ```

1. Dodaj następujące elementy do pliku `Dockerfile`, aby zasób JNDI został utworzony podczas tworzenia obrazu platformy Docker

    ```console
    RUN /bin/bash -c '<WILDFLY_INSTALL_PATH>/bin/standalone.sh --start-mode admin-only &' && \
    sleep 30 && \
    <WILDFLY_INSTALL_PATH>/bin/jboss-cli.sh -c --file=/opt/servicebus/servicebus-commands.cli && \
    sleep 30
    ```

1. Podczas tworzenia pliku YAML wdrożenia na późniejszym etapie będzie trzeba przekazać zmienne środowiskowe `MDB_CONNECTION_FACTORY`, `DEFAULT_SBNAMESPACE`, `SB_SAS_POLICY`, `SB_SAS_KEY`, `MDB_QUEUE`, `SB_QUEUE`, `MDB_TOPIC` i `SB_TOPIC` z odpowiednimi wartościami.

#### <a name="review-wildfly-configuration"></a>Przegląd konfiguracji serwera WildFly

Zapoznaj się z [przewodnikiem administratora serwera WildFly](https://docs.wildfly.org/18/Admin_Guide.html) w celu uzyskania informacji o wszelkich dodatkowych krokach poprzedzających migrację, które nie zostały omówione we wcześniejszych wskazówkach.
