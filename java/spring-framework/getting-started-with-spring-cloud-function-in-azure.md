---
title: Wprowadzenie do funkcji Spring Cloud na platformie Azure
description: Dowiedz się więcej na temat korzystania z funkcji Spring Cloud na platformie Azure.
documentationcenter: java
author: judubois
manager: brborges
ms.author: judubois
ms.date: 07/17/2019
ms.service: azure-functions
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 621fa4c79511149ef18a60fd4143490773e49271
ms.sourcegitcommit: 1586dacf8ea29f24f3bc9ccbf0eb07638b5596d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79313265"
---
# <a name="getting-started-with-spring-cloud-function-in-azure"></a>Wprowadzenie do funkcji Spring Cloud na platformie Azure

W tym artykule opisano proces tworzenia funkcji języka Java i publikowania jej w usłudze Azure Functions przy użyciu [funkcji Spring Cloud](https://spring.io/projects/spring-cloud-function). Gdy wszystko będzie gotowe, Twój kod funkcji zostanie uruchomiony w [planie zużycia](/azure/azure-functions/functions-scale#consumption-plan) na platformie Azure i może być wyzwalany za pomocą żądania HTTP.

[!INCLUDE [quickstarts-free-trial-note](../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu języka Java, musisz mieć zainstalowane następujące składniki:

- Zestaw [Java Developer Kit](https://aka.ms/azure-jdks), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure)
- [Podstawowe narzędzia usługi Azure Functions](/azure/azure-functions/functions-run-local#v2), wersja 2.7.1158 lub nowsza

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="what-we-are-going-to-build"></a>Co zamierzamy skompilować

Zamierzamy skompilować klasyczną funkcję „Hello, World”, która jest uruchamiana w usłudze Azure Functions i którą konfiguruje się za pomocą funkcji Spring Cloud.

Otrzyma ona prosty obiekt JSON `User` zawierający nazwę użytkownika, który odsyła do tego użytkownika obiekt `Greeting` zawierający komunikat powitalny.

Kompilowany tutaj projekt jest dostępny w witrynie [https://github.com/Azure-Samples/hello-spring-function-azure](https://github.com/Azure-Samples/hello-spring-function-azure), dlatego jeśli chcesz zobaczyć efekt końcowy opisany szczegółowo w tym przewodniku Szybki start, możesz skorzystać bezpośrednio z tego repozytorium przykładów.

## <a name="create-a-new-maven-project"></a>Tworzenie nowego projektu Maven

Zamierzamy utworzyć pusty projekt Maven i skonfigurować go za pomocą funkcji Spring Cloud i usługi Azure Functions.

W pustym folderze utwórz nowy plik *pom.xml* i skopiuj/wklej zawartość z naszego przykładowego projektu z witryny [https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml](https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml).

> [!NOTE]
> Ten plik używa zależności narzędzia Maven z aplikacji Spring Boot i funkcji Spring Cloud oraz konfiguruje wtyczki aplikacji Spring Boot i usługi Azure Functions w narzędziu Maven.

W aplikacji należy dostosować kilka właściwości:

- `<functionAppName>` jest nazwą funkcji platformy Azure
- `<functionAppRegion>` jest nazwą regionu świadczenia usługi Azure, w którym jest wdrażana funkcja
- `<functionResourceGroup>` jest nazwą grupy zasobów platformy Azure, której używasz

Właściwości te należy zmienić na samym początku pliku *pom.xml*:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <azure.functions.maven.plugin.version>1.4.1</azure.functions.maven.plugin.version>
    <azure.functions.java.library.version>1.3.0</azure.functions.java.library.version>
    <functionAppName>my-spring-function</functionAppName>
    <functionAppRegion>westus</functionAppRegion>
    <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    <functionResourceGroup>my-resource-group</functionResourceGroup>
    <start-class>com.example.HelloFunction</start-class>
    <wrapper.version>1.0.22.RELEASE</wrapper.version>
</properties>
```

## <a name="create-azure-configuration-files"></a>Tworzenie plików konfiguracji platformy Azure

Utwórz folder *src/main/azure* i dodaj do niego poniższe pliki konfiguracji usługi Azure Functions.

*host.json*:

```json
{
  "version": "2.0",
  "functionTimeout": "00:10:00"
}
```

*local.settings.json*:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "java",
    "AzureWebJobsDashboard": ""
  }
}
```

## <a name="create-domain-objects"></a>Tworzenie obiektów domeny

Usługa Azure Functions może odbierać i wysyłać obiekty w formacie JSON.
Zamierzamy utworzyć obiekty `User` i `Greeting` reprezentujące nasz model domeny.
Jeśli chcesz dostosować ten przewodnik Szybki start, aby był dla Ciebie ciekawszy, możesz utworzyć bardziej złożone obiekty z większą liczbą właściwości.

Utwórz folder *src/main/java/com/example/model* i dodaj następujące dwa pliki:

*User.java*:

```java
package com.example.model;

public class User {

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

*Greeting.java*:

```java
package com.example.model;

public class Greeting {

    public Greeting() {
    }

    public Greeting(String message) {
        this.message = message;
    }

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

## <a name="create-the-spring-boot-application"></a>Tworzenie aplikacji Spring Boot

Ta aplikacja będzie zarządzać całą logiką biznesową i będzie mieć dostęp do całego ekosystemu aplikacji Spring Boot.
W porównaniu ze standardową funkcją platformy Azure zapewnia to dwie główne korzyści:

- Aplikacja nie jest uzależniona od interfejsów API usługi Azure Functions, dzięki czemu można ją łatwo przenosić do innych systemów. Na przykład można jej używać także w zwykłej aplikacji Spring Boot.
- Aplikacja może używać wszystkich adnotacji `@Enable` z aplikacji Spring Boot, aby łatwo dodawać nowe, zaawansowane funkcje.

W folderze *src/main/java/com/example* utwórz następujący plik, który jest zwykłą aplikacją Spring Boot:

*HelloFunction.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.util.function.Function;

@SpringBootApplication
public class HelloFunction {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(HelloFunction.class, args);
    }

    @Bean
    public Function<User, Greeting> hello() {
        return user -> new Greeting("Welcome, " + user.getName());
    }
}
```

> [!NOTE] 
> Funkcja `hello()` jest dość charakterystyczna:
> 
> - Zwraca funkcję `java.util.function.Function`, która będzie używana w tym przewodniku Szybki start. Zawiera logikę biznesową i używa standardowego interfejsu API języka Java do przekształcania jednego obiektu na inny.
> - Ponieważ ma adnotację `@Bean`, jest funkcją Spring Bean i domyślnie jej nazwa jest jedną z metod — `hello`. Jest to ważne, jeśli chcesz tworzyć inne funkcje w swojej aplikacji, ponieważ ta nazwa musi być zgodna z nazwą usługi Azure Functions, którą utworzymy w następnej sekcji.

## <a name="create-the-azure-function"></a>Tworzenie funkcji platformy Azure

Aby w pełni wykorzystać interfejs API usługi Azure Functions, zamierzamy zaprogramować określoną klasę: jest ona funkcją platformy Azure, która będzie delegować swoje wykonywanie do funkcji Spring Cloud utworzonej w poprzednim kroku.

W folderze *src/main/java/com/example* utwórz następującą funkcję platformy Azure:

*HelloHandler.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.HttpMethod;
import com.microsoft.azure.functions.HttpRequestMessage;
import com.microsoft.azure.functions.annotation.AuthorizationLevel;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.HttpTrigger;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import java.util.Optional;

public class HelloHandler extends AzureSpringBootRequestHandler<User, Greeting> {

    @FunctionName("hello")
    public Greeting execute(
            @HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<User>> request,
            ExecutionContext context) {

        context.getLogger().info("Greeting user name: " + request.getBody().get().getName());
        return handleRequest(request.getBody().get(), context);
    }
}
```

Ta klasa języka Java jest funkcją platformy Azure zawierającą następujące ciekawe funkcje:

- Rozszerza ona procedurę obsługi `AzureSpringBootRequestHandler` tworzącą połączenie między usługą Azure Functions i funkcją Spring Cloud. W ten sposób udostępnia metodę `handleRequest()`, która jest używana w jej metodzie `execute()`.
- Nazwa funkcji, zgodnie z definicją w adnotacji `@FunctionName("hello")`, jest taka sama jak funkcji Spring Bean, którą skonfigurowaliśmy w poprzednim kroku — `hello`.
- Jest to prawdziwa funkcja platformy Azure, dlatego w tym miejscu możesz użyć pełnego interfejsu API usługi Azure Functions.

## <a name="add-unit-tests"></a>Dodawanie testów jednostkowych

Oczywiście ten krok jest opcjonalny, ale dobrzy deweloperzy powinni dodawać testy jednostkowe w celu sprawdzenia, czy aplikacja działa prawidłowo.

Utwórz folder *src/test/java/com/example* i dodaj następujące testy JUnit:

*HelloFunctionTest.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.junit.Test;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloFunctionTest {

    @Test
    public void test() {
        Greeting result = new HelloFunction().hello().apply(new User("foo"));
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }

    @Test
    public void start() throws Exception {
        AzureSpringBootRequestHandler<User, Greeting> handler = new AzureSpringBootRequestHandler<>(
                HelloFunction.class);
        Greeting result = handler.handleRequest(new User("foo"), null);
        handler.close();
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }
}
```

Teraz możesz przetestować funkcję platformy Azure przy użyciu narzędzia Maven:

```bash
mvn clean test
```

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Zanim wdrożysz aplikację do funkcji platformy Azure, najpierw przetestuj ją lokalnie.

W pierwszej kolejności musisz spakować aplikację do pliku JAR:

```bash
mvn package
```

Po spakowaniu aplikacji możesz uruchomić ją za pomocą wtyczki `azure-functions` narzędzia Maven:

```bash
mvn azure-functions:run
```

Funkcja platformy Azure powinna być teraz dostępna na hoście lokalnym za pośrednictwem portu 7071. Możesz przetestować funkcję, wysyłając jej żądanie POST z obiektem `User` w formacie JSON. Na przykład przy użyciu programu cURL:

```bash
curl http://localhost:7071/api/hello -d "{\"name\":\"Azure\"}"
```

Funkcja powinna odpowiedzieć obiektem `Greeting`, nadal w formacie JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Poniżej znajduje się zrzut ekranu przedstawiający żądanie cURL w górnej części ekranu i lokalną funkcję platformy Azure u dołu:

 ![Funkcja platformy Azure działająca lokalnie][RFL01]

## <a name="deploy-the-function-to-azure-functions"></a>Wdrażanie funkcji w usłudze Azure Functions

Teraz zamierzasz opublikować funkcję platformy Azure w środowisku produkcyjnym. Pamiętaj, że do skonfigurowania funkcji zostaną użyte właściwości `<functionAppName>`, `<functionAppRegion>` i `<functionResourceGroup>` zdefiniowane w pliku *pom.xml*.

> [!NOTE]
> Wtyczka Maven musi uwierzytelnić się na platformie Azure. Jeśli masz zainstalowany interfejs wiersza polecenia platformy Azure, przed kontynuowaniem użyj polecenia `az login`.
> Zajrzyj [tutaj](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication), aby uzyskać dostęp do większej liczby opcji uwierzytelniania.

Uruchom narzędzie Maven, aby automatycznie wdrożyć funkcję:

```bash
mvn azure-functions:deploy
```

Teraz przejdź do witryny [Azure Portal](https://portal.azure.com), aby znaleźć utworzony element `Function App`.

Kliknij funkcję:

- Zanotuj adres URL funkcji z jej omówienia.
- Wybierz kartę **Funkcje platformy**, aby znaleźć usługę **Przesyłanie strumieniowe dzienników**, a następnie wybierz tę usługę, aby sprawdzić uruchomioną funkcję.

Teraz, podobnie jak w poprzedniej sekcji, użyj programu cURL, aby uzyskać dostęp do uruchomionej funkcji. Zastąp `your-function-name` rzeczywistą nazwą swojej funkcji:

```bash
curl https:/your-function-name.azurewebsites.net/api/hello -d "{\"name\":\"Azure\"}"
```

Podobnie jak w poprzedniej sekcji, funkcja powinna odpowiedzieć obiektem `Greeting`, nadal w formacie JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Gratulacje — masz funkcję Spring Cloud działającą w usłudze Azure Functions!

<!-- IMG List -->

[RFL01]: ./media/getting-started-with-spring-cloud-function-in-azure/RFL01.png
