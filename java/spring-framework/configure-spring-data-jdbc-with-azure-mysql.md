---
title: Jak używać interfejsu Spring Data JDBC z usługą Azure Database for MySQL
description: Dowiedz się, jak używać interfejsu Spring Data JDBC z bazą danych usługi Azure Database for MySQL.
documentationcenter: java
ms.date: 01/07/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: conceptual
ms.openlocfilehash: 7a6550be633b29d97d55b8db2f50b2c57d0ba30d
ms.sourcegitcommit: 2ad3f7ce8c87331f8aff759ac2a3dc1b29581866
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "77000698"
---
# <a name="how-to-use-spring-data-jdbc-with-azure-mysql"></a>Jak używać interfejsu Spring Data JDBC z usługą Azure MySQL

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono tworzenie przykładowej aplikacji, która używa rozwiązania [Spring Data] do przechowywania informacji w bazie danych usługi [Azure Database for MySQL](/azure/mysql/) oraz pobierania informacji z tej bazy danych przy użyciu interfejsu [Java Database Connectivity (JDBC)](https://docs.oracle.com/javase/8/docs/technotes/guides/jdbc/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.
* [Curl](https://curl.haxx.se/) lub podobne narzędzie HTTP do testowania funkcjonalności.
* Narzędzie wiersza polecenia [mysql](https://dev.mysql.com/downloads/).
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-database-for-mysql"></a>Tworzenie usługi Azure Database for MySQL 

### <a name="create-a-server-using-the-azure-portal"></a>Tworzenie serwera przy użyciu witryny Azure Portal

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia baz danych MySQL można znaleźć w temacie [Create an Azure Database for MySQL server by using the Azure portal](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal) (Tworzenie serwera usługi Azure Database for MySQL przy użyciu witryny Azure Portal).

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+Utwórz zasób**, pozycję **Bazy danych**, a następnie pozycję **Azure Database for MySQL**.

   ![Tworzenie bazy danych MySQL][MYSQL01]

1. Wprowadź następujące informacje:

   - **Subskrypcja**: określ subskrypcję platformy Azure, która ma zostać użyta.
   - **Grupa zasobów**: określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.
   - **Nazwa serwera**: wybierz unikatową nazwę serwera MySQL — ta wartość zostanie użyta do utworzenia w pełni kwalifikowanej nazwy domeny, takiej jak *wingtiptoysmysql.mysql.database.azure.com*.
   - **Źródło danych**: na potrzeby tego samouczka wybierz `Blank`, aby utworzyć nową bazę danych.
   - **Nazwa użytkownika administratora**: określ nazwę administratora bazy danych.
   - **Hasło** i **Potwierdź hasło**: określ hasło administratora bazy danych.
   - **Lokalizacja**: określ najbliższy region geograficzny bazy danych.
   - **Wersja**: określ najbardziej aktualną wersję bazy danych.

   ![Tworzenie właściwości bazy danych MySQL][MYSQL02]

1. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Przejrzyj i utwórz**.

1. Przejrzyj specyfikację i kliknij przycisk **Utwórz**.

### <a name="configure-a-firewall-rule-for-your-server-using-the-azure-portal"></a>Konfigurowanie reguły zapory dla serwera przy użyciu witryny Azure Portal

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzone zasoby usługi Azure Database for MySQL.

1. Kliknij pozycję **Zabezpieczenia połączeń** i w sekcji **Reguły zapory** utwórz nową regułę, określając unikatową nazwę reguły. Wprowadź zakres adresów IP, które będą potrzebować dostępu do bazy danych, a następnie kliknij przycisk **Zapisz**. (W tym ćwiczeniu należy podać adres IP swojego komputera deweloperskiego, który jest klientem.  Można użyć tego adresu w obu pozycjach: **Początkowy adres IP** oraz **Końcowy adres IP**).

   ![Konfigurowanie zabezpieczeń połączenia][MYSQL04]

### <a name="retrieve-the-connection-string-for-your-server-using-the-azure-portal"></a>Pobieranie parametrów połączenia dla serwera przy użyciu witryny Azure Portal

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie wybierz utworzony zasób usługi Azure Database for MySQL.

1. Kliknij pozycję **Parametry połączenia** i skopiuj wartość znajdującą się w polu tekstowym **JDBC**.

   ![Pobieranie parametrów połączenia interfejsu JDBC][MYSQL05]

### <a name="create-a-database-using-the-mysql-command-line-utility"></a>Tworzenie bazy danych przy użyciu narzędzia wiersza polecenia `mysql`

1. Otwórz powłokę poleceń i połącz się z serwerem MySQL, wprowadzając polecenie `mysql`, jak w poniższym przykładzie:

   ```shell
   mysql --host wingtiptoysmysql.mysql.database.azure.com --user wingtiptoysuser@wingtiptoysmysql -p
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `host` | Określa w pełni kwalifikowaną nazwę serwera MySQL z wcześniejszej części tego artykułu. |
   | `user` | Określa nazwę administratora MySQL i skróconą nazwę serwera z wcześniejszej części tego artykułu. |
   | `p` | Określa, aby zaczekać, aż zostanie wyświetlony monit o podanie hasła. |

   Serwer MySQL powinien odpowiedzieć wyświetleniem ekranu, który wygląda podobnie do poniższego przykładu:

   ```shell
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64552
   Server version: 5.6.39.0 MySQL Community Server (GPL)
   
   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.
   
   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.
   
   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
   
   mysql>
   ```
   > Uwaga: Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że serwer nie rozpoznaje tego adresu IP, będzie on zawierać adres IP używany przez klienta.  Wróć i przypisz go zgodnie z wcześniejszym opisem: *Konfigurowanie reguły zapory dla serwera przy użyciu witryny Azure Portal*.

1. Utwórz bazę danych o nazwie *mysqldb*, wprowadzając polecenie `mysql` jak w przykładzie poniżej:

   ```SQL
   CREATE DATABASE mysqldb;
   ```

   Serwer MySQL powinien odpowiedzieć wyświetleniem ekranu, który wygląda podobnie do poniższego przykładu:

   ```shell
   Query OK, 1 row affected (0.30 sec)
   ```

1. OPCJONALNIE: Możesz sprawdzić, czy baza danych została utworzona, wprowadzając polecenie `mysql` jak w przykładzie poniżej:

   ```SQL
   SHOW DATABASES;
   ```

   Serwer MySQL powinien odpowiedzieć wyświetleniem ekranu, który wygląda podobnie do poniższego przykładu:

   ```shell
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | mysqldb            |
   | performance_schema |
   | sys                |
   +--------------------+
   ```

1. Wpisz `\q`, aby zamknąć narzędzie `mysql`.

## <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Otwórz powłokę poleceń i sklonuj przykładowy projekt przy użyciu polecenia git, jak w poniższym przykładzie:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jdbc-on-azure.git
   ```

1. Znajdź plik *application.properties* w katalogu *resources* przykładowego projektu lub utwórz ten plik, jeśli jeszcze nie istnieje.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj lub skonfiguruj następujące wiersze w pliku i zastąp przykładowe wartości odpowiednimi wartościami skopiowanymi wcześniej:

   ```yaml
   spring.datasource.url=jdbc:mysql://wingtiptoysmysql.mysql.database.azure.com:3306/mysqldb?useSSL=true&requireSSL=false&serverTimezone=UTC
   spring.datasource.username=wingtiptoysuser@wingtiptoysmysql
   spring.datasource.password=********
    ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `spring.datasource.url` | Określa parametry połączenia interfejsu JDBC MySQL z wcześniejszej części tego artykułu wraz z dodaną strefą czasową. |
   | `spring.datasource.username` | Określa nazwę administratora MySQL z wcześniejszej części tego artykułu, z dołączoną skróconą nazwą serwera. |
   | `spring.datasource.password` | Określa hasło administratora MySQL z wcześniejszej części tego artykułu. |

1. Zapisz i zamknij plik *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Pakowanie i testowanie aplikacji przykładowej 

1. Skompiluj aplikację przykładową przy użyciu narzędzia Maven, na przykład:

   ```shell
   mvn clean package -P mysql
   ```

1. Uruchom aplikację przykładową, na przykład:

   ```shell
   java -jar target/spring-data-jdbc-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Utwórz nowe rekordy przy użyciu narzędzia `curl` z poziomu wiersza polecenia, tak jak w następujących przykładach:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Aplikacja powinna zwrócić wartości podobne do następujących:

   ```shell
   Added Pet(id=1, name=dog, species=canine).

   Added Pet(id=2, name=cat, species=feline).
   ```

1. Pobierz wszystkie istniejące rekordy przy użyciu narzędzia `curl` z poziomu wiersza polecenia, tak jak w następujących przykładach:

   ```shell
   curl -s http://localhost:8080/pets
   ```
    
   Aplikacja powinna zwrócić wartości podobne do następujących:

   ```json
   [{"id":1,"name":"dog","species":"canine"},{"id":2,"name":"cat","species":"feline"}]
   ```

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono przykładową aplikację Java, która używa rozwiązania Spring Data do przechowywania informacji w bazie danych usługi Azure Database for MySQL oraz pobierania informacji z tej bazy danych przy użyciu interfejsu JDBC.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[MYSQL01]: media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-01.png
[MYSQL02]: media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-02.png
[MYSQL04]: media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-04.png
[MYSQL05]: media/configure-spring-data-jdbc-with-azure-mysql/create-mysql-05.png
