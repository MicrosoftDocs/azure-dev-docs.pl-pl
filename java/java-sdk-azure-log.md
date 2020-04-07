---
title: Konfigurowanie rejestrowania za pomocą zestawu Azure SDK dla języka Java
description: Dowiedz się, jak skonfigurować platformy rejestrowania dla bibliotek klienckich zestawu Azure SDK dla języka Java
keywords: Azure, Java, SDK, rejestrowanie
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9f7ad8d772b7de1335ebc3ba77cdea8aa1e8b683
ms.sourcegitcommit: 31f6d047f244f1e447faed6d503afcbc529bd28c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80319904"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Konfigurowanie rejestrowania za pomocą zestawu Azure SDK dla języka Java

W tym artykule przedstawiono przykładowe konfiguracje rejestrowania dla zestawu [Azure SDK](https://azure.microsoft.com/downloads/) dla języka Java. Aby uzyskać więcej szczegółów na temat opcji konfiguracji, takich jak ustawianie poziomów dziennika lub rejestrowanie niestandardowe według klasy, zapoznaj się z dokumentacją dla wybranej platformy rejestrowania.

Biblioteki klienckie zestawu Azure SDK dla języka Java używają fasady [Simple Logging Facade for Java](https://www.slf4j.org/) (SLF4J). Fasada SLF4J umożliwia korzystanie z preferowanej platformy rejestrowania, która jest wywoływana w czasie wdrażania aplikacji.

> [!NOTE]
> Ten artykuł ma zastosowanie do najnowszych wersji bibliotek klienckich zestawu Azure SDK. Aby sprawdzić, czy biblioteka jest obsługiwana, zapoznaj się z listą najnowszych wydań zestawu [Azure SDK](https://azure.github.io/azure-sdk/releases/latest/java.html). Jeśli aplikacja używa starszej wersji bibliotek klienckich zestawu Azure SDK, zapoznaj się z określonymi instrukcjami w dokumentacji odpowiedniej usługi.

## <a name="declare-a-logging-framework"></a>Deklarowanie platformy rejestrowania

Przed zaimplementowaniem tych rejestratorów należy zadeklarować odpowiednią platformę jako zależność w projekcie. Aby uzyskać więcej informacji, zobacz[podręcznik użytkownika fasady SLF4J](http://www.slf4j.org/manual.html#projectDep).

## <a name="configure-log4j-or-log4j-2"></a>Konfigurowanie narzędzia Log4J lub Log4J 2

Rejestrowanie przy użyciu narzędzi Log4J i Log4J 2 można skonfigurować w pliku właściwości lub w pliku XML. Aby uzyskać szczegółowe informacje na temat rejestrowania przy użyciu narzędzi Log4J i Log4J 2, zapoznaj się z[podręcznikiem Apache Log4j 2](https://logging.apache.org/log4j/2.x/manual/configuration.html).

### <a name="use-a-properties-file"></a>Używanie pliku właściwości

W katalogu *./src/main/resource* projektu utwórz nowy plik o nazwie *log4J.properties* lub *log4j2.properties* (ten drugi dla narzędzia Logj4 2). Skorzystaj z tych przykładów, aby rozpocząć pracę.

Przykład narzędzia Log4J:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

Przykład narzędzia Log4j2:

```properties
appender.console.type = Console
appender.console.name = LogToConsole
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR
```

### <a name="use-an-xml-file"></a>Używanie pliku XML

Alternatywnie możesz skonfigurować narzędzia Log4J i Log4j 2 za pomocą pliku XML. W katalogu *./src/main/resource* projektu utwórz nowy plik o nazwie *log4J.xml* lub *log4j2.xml* (ten drugi dla narzędzia Logj4 2). Skorzystaj z tych przykładów, aby rozpocząć pracę.

Przykład narzędzia Log4J:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

  <appender name="console" class="org.apache.log4j.ConsoleAppender">
    <param name="Target" value="System.out"/>
    <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%m%n" />
    </layout>
  </appender>
  <logger name="com.azure.core" additivity="true">
    <level value="ERROR" />
    <appender-ref ref="console" />
  </logger>

  <root>
    <priority value ="info"></priority>
    <appender-ref ref="console"></appender>
  </root>

</log4j:configuration>
```

Przykład narzędzia Log4j2:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.azure.core" level="error" additivity="true">
            <appender-ref ref="console" />
        </Logger>
        <Root level="info" additivity="false">
            <appender-ref ref="console" />
        </Root>
     </Loggers>
</Configuration>
```

## <a name="configure-logback"></a>Konfigurowanie narzędzia Logback

[Logback](https://logback.qos.ch/manual/introduction.html) jest jedną ze popularnych platform rejestrowania i natywną implementacją fasady SLF4J. Aby skonfigurować narzędzie Logback, utwórz nowy plik XML o nazwie *logback.xml* w katalogu *./src/main/resources* projektu. Więcej informacji na temat opcji konfiguracji można znaleźć w [witrynie internetowej projektu Logback](https://logback.qos.ch/manual/configuration.html).

Oto przykładowa konfiguracja narzędzia Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%message%n</pattern>
    </encoder>
  </appender>

  <logger name="com.azure.core" level="ERROR" />

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

Oto prosta konfiguracja narzędzia Logback do rejestrowania do konsoli:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="Console"
    class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="Console" />
  </root>
</configuration>
```

Oto konfiguracja rejestrowania do pliku, który jest przerzucany po każdej godzinie i archiwizowanym w formacie GZIP:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder
      class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="info">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

### <a name="configure-logback-for-a-spring-boot-application"></a>Konfigurowanie narzędzia Logback dla aplikacji Spring Boot

Oprogramowanie Spring wyszukuje konfiguracje projektu, w tym rejestrowania, w pliku *application.properties* znajdującym się w katalogu *./src/main/resources*. W pliku *application.properties* dodaj następujący wiersz, aby połączyć plik *logback.xml* z aplikacja Spring Boot:

```properties
logging.config=classpath:logback.xml
```

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Konfigurowanie rejestrowania rezerwowego na potrzeby debugowania tymczasowego

W scenariuszach, w których nie można ponownie wdrożyć aplikacji przy użyciu rejestratora SLF4J, możesz użyć rejestratora rezerwowego, który jest wbudowany w biblioteki klienckie platformy Azure dla języka Java, w usłudze Azure Core 1.3.0 lub nowszej. Aby włączyć ten rejestrator, należy najpierw potwierdzić, że nie ma rejestratora SLF4J (ponieważ będzie miał pierwszeństwo), a następnie ustawić zmienną środowiskową `AZURE_LOG_LEVEL`. Po ustawieniu zmiennej środowiskowej należy ponownie uruchomić aplikację, aby rozpocząć generowanie dzienników.

W poniższej tabeli przedstawiono dozwolone wartości tej zmiennej środowiskowej.

|Poziom dziennika   |Dozwolone wartości zmiennej środowiskowej   |
|----------|-----------|
|PEŁNY   |„verbose”, „debug”     |
|INFORMACYJNY|„info”, „information”, „informational”  |
|OSTRZEŻENIE     |„warn”, „warning”       |
|BŁĄD    |„err”, „error”  |

## <a name="next-steps"></a>Następne kroki

- [Włączanie rejestrowania diagnostyki dla aplikacji w usłudze Azure App Service](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Omówienie opcji rejestrowania i inspekcji zabezpieczeń platformy Azure](/azure/security/fundamentals/log-audit)
- [Dowiedz się, jak pracować z dziennikami platformy Azure](/azure/azure-monitor/platform/platform-logs-overview)
