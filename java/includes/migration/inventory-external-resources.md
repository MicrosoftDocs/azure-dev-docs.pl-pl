---
author: yevster
ms.author: yebronsh
ms.date: 1/20/2020
ms.openlocfilehash: 4b5b73eee66c4a5c9eb28b79804e0dc610f639d6
ms.sourcegitcommit: 951fc116a9519577b5d35b6fb584abee6ae72b0f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80612087"
---
### <a name="inventory-external-resources"></a>Utworzenie spisu zasobów zewnętrznych

Zasoby zewnętrzne, takie jak źródła danych, brokery komunikatów JMS i inne, są wstrzykiwane za pośrednictwem interfejsu Java Naming and Directory Interface (JNDI). Niektóre takie zasoby mogą wymagać migracji lub ponownej konfiguracji.

#### <a name="inside-your-application"></a>W aplikacji

Sprawdź plik *META-INF/context.xml*. Poszukaj elementów `<Resource>` wewnątrz elementu `<Context>`.

#### <a name="on-the-application-servers"></a>Na serwerach aplikacji

Sprawdź pliki *$CATALINA_BASE/conf/context.xml* i *$CATALINA_BASE/conf/server.xml* oraz pliki *.xml* w katalogach *$CATALINA_BASE/conf/[nazwa-aparatu]/[nazwa-hosta]* .

W plikach *context.xml* zasoby JNDI będą opisywane przez elementy `<Resource>` w elemencie `<Context>` najwyższego poziomu.

W plikach *server.xml* zasoby JNDI będą opisywane przez elementy `<Resource>` w elemencie `<GlobalNamingResources>`.

#### <a name="datasources"></a>Źródła danych

Źródła danych to zasoby JNDI z atrybutem `type` ustawionym na `javax.sql.DataSource`. Dla każdego źródła danych należy udokumentować następujące informacje:

* Jaka jest nazwa źródła danych?
* Jaka jest konfiguracja puli połączeń?
* Gdzie mogę znaleźć plik JAR sterownika JDBC?

Aby uzyskać więcej informacji, zobacz przewodnik [JNDI Datasource How-To](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html) (Źródło danych JNDI — porady) w dokumentacji serwera Tomcat.

#### <a name="all-other-external-resources"></a>Wszystkie inne zasoby zewnętrzne

Nie jest możliwe udokumentowanie każdej możliwej zależności zewnętrznej w tym przewodniku. Twój zespół odpowiada za sprawdzenie, czy każda zależność zewnętrzna aplikacji może być spełniona po migracji.
