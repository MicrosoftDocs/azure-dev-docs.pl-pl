---
title: Jak używać interfejsu Spring Data JPA z usługą Azure SQL Database
description: Dowiedz się, jak używać interfejsu Spring Data JPA z usługą Azure SQL Database.
services: sql-database
documentationcenter: java
ms.date: 12/19/2018
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 180eac30a5aaeb10abe09904c578463bfe5a71da
ms.sourcegitcommit: 3b76a0aa1683f28bcb42cd4d506426b48e5b0397
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80537202"
---
# <a name="how-to-use-spring-data-jpa-with-azure-sql-database"></a>Jak używać interfejsu Spring Data JPA z usługą Azure SQL Database

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono tworzenie przykładowej aplikacji, która używa rozwiązania [Spring Data] do przechowywania informacji w usłudze [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) oraz pobierania informacji z tej usługi przy użyciu interfejsu [Java Persistence API (JPA)](https://docs.oracle.com/javaee/7/tutorial/persistence-intro.htm).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/), wersja 3.0 lub nowsza.
* [Curl](https://curl.haxx.se/) lub podobne narzędzie HTTP do testowania funkcjonalności.
* Klient usługi [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-sql-database"></a>Tworzenie bazy danych Azure SQL Database

### <a name="create-a-sql-database-server-using-the-azure-portal"></a>Tworzenie serwera bazy danych SQL przy użyciu witryny Azure Portal

> [!NOTE]
> 
> Więcej szczegółowych informacji na temat tworzenia baz danych SQL na platformie Azure można znaleźć w temacie [Tworzenie bazy danych Azure SQL Database w witrynie Azure Portal](/azure/sql-database/sql-database-get-started-portal).

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **+ Utwórz zasób**, pozycję **Bazy danych**, a następnie pozycję **SQL Database**.

   ![Tworzenie bazy danych SQL][SQL01]

1. Wprowadź następujące informacje:

   - **Nazwa bazy danych**: wybierz unikatową nazwę bazy danych SQL — zostanie ona utworzona na serwerze SQL, który określisz później.
   - **Subskrypcja**: określ subskrypcję platformy Azure, która ma zostać użyta.
   - **Grupa zasobów**: określ, czy chcesz utworzyć nową grupę zasobów, lub wybierz istniejącą grupę.
   - **Wybierz źródło**: na potrzeby tego samouczka wybierz `Blank database`, aby utworzyć nową bazę danych.

   ![Określanie właściwości bazy danych SQL][SQL02]
   
1. Kliknij pozycję **Serwer**, a następnie pozycję **Utwórz nowy serwer** i podaj następujące informacje:

   - **Nazwa serwera**: wybierz unikatową nazwę serwera SQL — ta wartość zostanie użyta do utworzenia w pełni kwalifikowanej nazwy domeny, takiej jak *wingtiptoyssql.database.windows.net*.
   - **Identyfikator logowania administratora serwera**: określ nazwę administratora bazy danych.
   - **Hasło** i **Potwierdź hasło**: określ hasło administratora bazy danych.
   - **Lokalizacja**: określ najbliższy region geograficzny bazy danych.

1. Po wprowadzeniu wszystkich powyższych informacji kliknij przycisk **OK**.

1. Kliknij pozycję **Przejrzyj i utwórz**.

### <a name="configure-a-firewall-rule-for-your-sql-server-using-the-azure-portal"></a>Konfigurowanie reguły zapory dla serwera SQL przy użyciu witryny Azure Portal

Po utworzeniu bazy danych i serwera SQL można skonfigurować ustawienia zabezpieczeń.

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzony serwer SQL.

1. W sekcji **Omówienie** kliknij pozycję **Pokaż ustawienia zapory**.

   ![Pokaż ustawienia zapory][SQL06]

1. W sekcji **Zapory i sieci wirtualne** utwórz nową regułę, określając unikatową nazwę reguły, wprowadź zakres adresów IP, które będą potrzebować dostępu do bazy danych, a następnie kliknij pozycję **Zapisz**. (W tym ćwiczeniu należy podać adres IP swojego komputera deweloperskiego, który jest klientem.  Można użyć tego adresu w obu pozycjach: **Początkowy adres IP** oraz **Końcowy adres IP**).

   ![Konfigurowanie ustawień zapory][SQL07]

### <a name="retrieve-the-connection-string-for-your-sql-server-using-the-azure-portal"></a>Pobieranie parametrów połączenia dla serwera SQL przy użyciu witryny Azure Portal

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i zaloguj się.

1. Kliknij pozycję **Wszystkie zasoby**, a następnie kliknij utworzoną bazę danych SQL.

1. Kliknij pozycję **Parametry połączenia**.


   ![Wybieranie bazy danych SQL][SQL08]

1. Kliknij pozycję **JDBC**, a następnie skopiuj wartość znajdującą się w polu tekstowym JDBC.

   ![Pobieranie parametrów połączenia interfejsu JDBC][SQL09]

## <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Otwórz powłokę poleceń i sklonuj przykładowy projekt przy użyciu polecenia git, jak w poniższym przykładzie:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-jdbc-on-azure.git
   ```

1. Znajdź plik *application.properties* w katalogu *resources* przykładowego projektu lub utwórz ten plik, jeśli jeszcze nie istnieje.

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj lub skonfiguruj następujące wiersze w pliku i zastąp przykładowe wartości odpowiednimi wartościami skopiowanymi wcześniej:

   ```yaml
   spring.datasource.url=jdbc:sqlserver://wingtiptoyssql.database.windows.net:1433;database=wingtiptoys;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
   spring.datasource.username=wingtiptoysuser@wingtiptoyssql
   spring.datasource.password=********
    ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `spring.datasource.url` | Określa edytowaną wersję parametrów połączenia JDBC SQL z wcześniejszej części tego artykułu. |
   | `spring.datasource.username` | Określa nazwę administratora SQL z wcześniejszej części tego artykułu, z dołączoną skróconą nazwą serwera. |
   | `spring.datasource.password` | Określa hasło administratora SQL z wcześniejszej części tego artykułu. |

1. Zapisz i zamknij plik *application.properties*.

> [!NOTE]
> Pierwsza właściwość pliku *application.properties* to `spring.jpa.hibernate.ddl-auto=create`, czyli właściwość hibernacji, która automatycznie usuwa i ponownie tworzy schemat bazy danych przy uruchamianiu aplikacji.
> Ta konfiguracja jest przydatna podczas tworzenia i testowania, ale nie należy jej używać w środowisku produkcyjnym.

## <a name="package-and-test-the-sample-application"></a>Pakowanie i testowanie aplikacji przykładowej

1. Skompiluj aplikację przykładową przy użyciu narzędzia Maven, na przykład:

   ```shell
   mvn clean package -P sql
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

W tym samouczku utworzono przykładową aplikację Java, która używa rozwiązania Spring Data do przechowywania informacji w bazie danych usługi Azure SQL Database oraz pobierania informacji z tej bazy danych przy użyciu interfejsu JPA.

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

[SQL01]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-01.png
[SQL02]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-02.png
[SQL03]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-03.png
[SQL04]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-04.png
[SQL05]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-05.png
[SQL06]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-06.png
[SQL07]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-07.png
[SQL08]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-08.png
[SQL09]: media/configure-spring-data-jpa-with-azure-sql-server/create-azure-sql-09.png
