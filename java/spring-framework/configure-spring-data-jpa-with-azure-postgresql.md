---
title: Jak używać interfejsu Spring Data JPA z usługą Azure PostgreSQL
description: Dowiedz się, jak skonfigurować interfejs Spring Data JPA oraz używać go z bazą danych w usłudze Azure Database for PostgreSQL.
documentationcenter: java
ms.date: 12/19/2018
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.topic: conceptual
ms.openlocfilehash: e968f6a86cc8616b0ae79e5d55756acea76040a6
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422543"
---
# <a name="how-to-use-spring-data-jpa-with-azure-postgresql"></a>Jak używać interfejsu Spring Data JPA z usługą Azure PostgreSQL

W tym artykule przedstawiono tworzenie przykładowej aplikacji, która używa narzędzia [Spring Data] do przechowywania informacji w bazie danych usługi [Azure Database for PostgreSQL](/azure/postgresql/) oraz pobierania informacji z tej bazy danych przy użyciu interfejsu [Java Persistence API (JPA)](https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/), wersja 3.0 lub nowsza.
* [Curl](https://curl.haxx.se/) lub podobne narzędzie HTTP do testowania funkcjonalności.
* Narzędzie wiersza polecenia [psql](https://www.postgresql.org/docs/current/app-psql.html).
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="create-a-postgresql-database-for-azure"></a>Tworzenie bazy danych PostgreSQL dla platformy Azure

### <a name="create-a-postgresql-database-server-using-the-azure-portal"></a>Tworzenie serwera bazy danych PostgreSQL przy użyciu witryny Azure Portal

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia baz danych PostgreSQL można znaleźć w temacie [Create an Azure Database for PostgreSQL server by using the Azure portal](/azure/postgresql/quickstart-create-server-database-portal) (Tworzenie serwera usługi Azure Database for PostgreSQL w witrynie Azure Portal).

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+Utwórz zasób**, pozycję **Bazy danych**, a następnie pozycję **Azure Database for PostgreSQL**.

   ![Tworzenie bazy danych PostgreSQL][POSTGRESQL01]

1. Wybierz pozycję **Pojedynczy serwer** lub **Grupa serwerów w hiperskali**.

1. Kliknij pozycję **Utwórz**.

1. Wprowadź następujące informacje:

   - **Subskrypcja**: określ subskrypcję platformy Azure, która ma zostać użyta.
   - **Grupa zasobów**: określ, czy chcesz utworzyć nową grupę zasobów, czy wybrać istniejącą grupę.
   - **Nazwa serwera**: wybierz unikatową nazwę serwera PostgreSQL — ta wartość zostanie użyta do utworzenia w pełni kwalifikowanej nazwy domeny, np. *wingtiptoyspostgresql.postgres.database.azure.com*.
   - **Źródło danych**: na potrzeby tego samouczka wybierz `Blank`, aby utworzyć nową bazę danych.
   - **Nazwa użytkownika administratora**: określ nazwę administratora bazy danych.
   - **Hasło** i **Potwierdź hasło**: określ hasło administratora bazy danych.
   - **Lokalizacja**: określ najbliższy region geograficzny bazy danych.
   - **Wersja**: określ najbardziej aktualną wersję bazy danych.

   ![Tworzenie właściwości bazy danych PostgreSQL][POSTGRESQL02]

1. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **Przejrzyj i utwórz**.

1. Przejrzyj i sprawdź wybrane opcje, a następnie kliknij przycisk **Utwórz**.

### <a name="configure-a-firewall-rule-for-your-postgresql-database-server-using-the-azure-portal"></a>Konfigurowanie reguły zapory dla serwera bazy danych PostgreSQL przy użyciu witryny Azure Portal

1. Po zakończeniu wdrażania kliknij przycisk **Przejdź do zasobu**.

1. Kliknij pozycję **Zabezpieczenia połączeń**.

1. Utwórz nową regułę, określając unikatową nazwę reguły, wprowadź zakres adresów IP, które będą potrzebować dostępu do bazy danych, a następnie kliknij pozycję **Zapisz**. (W tym ćwiczeniu należy podać adres IP swojego komputera deweloperskiego, który jest klientem.  Można użyć tego adresu w obu pozycjach: **Początkowy adres IP** oraz **Końcowy adres IP**).

   ![Wybieranie bazy danych PostgreSQL][POSTGRESQL03]

### <a name="retrieve-the-connection-string-for-your-postgresql-server-using-the-azure-portal"></a>Pobieranie parametrów połączenia dla serwera PostgreSQL przy użyciu witryny Azure Portal

1. Na stronie **Wszystkie zasoby** kliknij utworzoną bazę danych PostgreSQL.

1. Kliknij pozycję **Parametry połączenia** i skopiuj wartość znajdującą się w polu tekstowym **JDBC**.

   ![Pobieranie parametrów połączenia interfejsu JDBC][POSTGRESQL05]

### <a name="create-postgresql-database-using-the-psql-command-line-utility"></a>Tworzenie bazy danych PostgreSQL przy użyciu narzędzia wiersza polecenia `psql`

1. Otwórz powłokę poleceń i połącz się z serwerem PostgreSQL, wprowadzając polecenie `psql`, jak w poniższym przykładzie:

   ```shell
   psql --host=wingtiptoyspostgresql.postgres.database.azure.com --port=5432 --username=wingtiptoysuser@wingtiptoyspostgresql --dbname=postgres
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `host` | Określa w pełni kwalifikowaną nazwę serwera PostgreSQL z wcześniejszej części tego artykułu. |
   | `host` | Określa port serwera PostgreSQL, którym domyślnie jest `5432`. |
   | `username` | Określa nazwę administratora PostgreSQL i skróconą nazwę serwera z wcześniejszej części tego artykułu. |
   | `dbname` | Określa, że chcesz obecnie używać domyślnej bazy danych `postgres`. |

   Serwer PostgreSQL powinien odpowiedzieć wyświetleniem ekranu, który wygląda podobnie do poniższego przykładu:

   ```shell
   psql (9.3.24, server 10.5)
   SSL connection (cipher: ECDHE-RSA-AES256-SHA384, bits: 256)
   Type "help" for help.
   
   postgres=>
   ```
   > Uwaga: Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że serwer nie rozpoznaje tego adresu IP, będzie on zawierać adres IP używany przez klienta.  Wróć i przypisz go zgodnie z wcześniejszym opisem: *Konfigurowanie reguły zapory dla serwera przy użyciu witryny Azure Portal*.

1. Utwórz bazę danych o nazwie *mypgsqldb*, wprowadzając polecenie `psql` jak w przykładzie poniżej:

   ```SQL
   CREATE DATABASE mypgsqldb;
   ```

   Serwer PostgreSQL powinien odpowiedzieć wyświetleniem ekranu, który wygląda podobnie do poniższego przykładu:

   ```shell
   CREATE DATABASE
   ```

1. OPCJONALNIE: Możesz sprawdzić, czy baza danych została utworzona, wprowadzając `\l` w poleceniu `psql`. Serwer PostgreSQL powinien odpowiedzieć w sposób podobny do poniższego przykładu:

   ```shell
                   List of databases
          Name        |      Owner      | Encoding
   -------------------+-----------------+----------
    azure_maintenance | azure_superuser | UTF8
    azure_sys         | azure_superuser | UTF8
    mypgsqldb         | wingtiptoysuser | UTF8
    postgres          | azure_superuser | UTF8
    template0         | azure_superuser | UTF8
    template1         | azure_superuser | UTF8
   (6 rows)
   ```

1. Wpisz `\q`, aby zamknąć narzędzie `psql`.

## <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Otwórz powłokę poleceń i sklonuj przykładowy projekt przy użyciu polecenia git, jak w poniższym przykładzie:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jpa-on-azure.git
   ```

1. Znajdź plik *application.properties* w katalogu *resources* przykładowego projektu lub utwórz ten plik, jeśli jeszcze nie istnieje.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj lub skonfiguruj następujące wiersze w pliku i zastąp przykładowe wartości odpowiednimi wartościami skopiowanymi wcześniej:

   ```yaml
   spring.datasource.url=jdbc:postgresql://wingtiptoyspostgresql.postgres.database.azure.com:5432/mypgsqldb?ssl=true&sslmode=prefer
   spring.datasource.username=wingtiptoysuser@wingtiptoyspostgresql
   spring.datasource.password=********
    ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `spring.datasource.url` | Określa parametry połączenia interfejsu JDBC PostgreSQL z wcześniejszej części tego artykułu. |
   | `spring.datasource.username` | Określa nazwę administratora PostgreSQL z wcześniejszej części tego artykułu, z dołączoną skróconą nazwą serwera. |
   | `spring.datasource.password` | Określa hasło administratora PostgreSQL z wcześniejszej części tego artykułu. |

1. Zapisz i zamknij plik *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Pakowanie i testowanie aplikacji przykładowej 

1. Skompiluj aplikację przykładową przy użyciu narzędzia Maven, na przykład:

   ```shell
   mvn clean package -P postgresql
   ```

1. Uruchom aplikację przykładową, na przykład:

   ```shell
   java -jar target/spring-data-jdbc-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Utwórz nowe rekordy przy użyciu narzędzia `curl` z poziomu wiersza polecenia, tak jak w następujących przykładach:

   ```shell
   curl -s -d '{"name":"dog","species":"canine"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d '{"name":"cat","species":"feline"}' -H "Content-Type: application/json" -X POST http://localhost:8080/pets
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

W tym samouczku utworzono przykładową aplikację Java, która używa rozwiązania Spring Data do przechowywania informacji w bazie danych Azure PostgreSQL oraz pobierania informacji z tej bazy danych przy użyciu interfejsu JPA.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji na temat korzystania z platformy Azure przy użyciu języka Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] oraz [Praca z narzędziami Azure DevOps i językiem Java].

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Praca z narzędziami Azure DevOps i językiem Java]: /azure/devops/
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[POSTGRESQL01]: media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-01.png
[POSTGRESQL02]: media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-02.png
[POSTGRESQL03]: media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-03.png
[POSTGRESQL04]: media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-04.png
[POSTGRESQL05]: media/configure-spring-data-jpa-with-azure-postgresql/create-postgresql-05.png
