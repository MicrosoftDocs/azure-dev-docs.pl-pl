---
title: Wdrażanie usługi Java MicroProfile
titleSuffix: Azure Web App for Containers
description: Dowiedz się, jak wdrożyć usługę MicroProfile przy użyciu platformy Docker i usługi Azure Web App for Containers.
services: container-registry;app-service
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 6deaced31e9cbe6ebd1ef1eb20bd0414ab5df471
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999865"
---
# <a name="deploy-a-java-based-microprofile-service-to-azure-web-app-for-containers"></a>Wdrażanie opartej na języku Java usługi MicroProfile na platformie Azure Web App for Containers

Usługa MicroProfile to świetny sposób umożliwiający tworzenie niezwykle małych aplikacji Java, które można szybko i łatwo wdrażać w usługach takich jak [Azure Web App for Containers](https://azure.microsoft.com/services/app-service/containers/). W tym samouczku utworzymy prostą mikrousługę opartą na usłudze MicroProfile, która zostanie następnie skonteneryzowana do kontenera platformy Docker i wdrożona w usłudze [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), a potem będzie hostowana przy użyciu usługi Azure Web App for Containers.

> [!NOTE]
>
> Ta procedura działa z dowolną implementacją aplikacji MicroProfile.io, o ile obraz kontenera platformy Docker jest samowykonywalny (tj. zawiera środowisko uruchomieniowe).

Mówiąc dokładniej, w tym przykładzie użyto platformy [Payara Micro](https://www.payara.fish/payara_micro) i usługi [MicroProfile 1.3](https://microprofile.io/), aby utworzyć niewielki plik war środowiska Java (5085 bajtów na maszynie autora), a następnie dodać go do obrazu platformy Docker (którego rozmiar wynosi około 174 megabajty). Ten obraz platformy Docker zawiera wszystkie składniki niezbędne do w pełni skonteneryzowanego wdrożenia tej aplikacji internetowej.

Ze względu na sposób działania platformy Docker często zdarza się, że nie trzeba za każdym razem ponownie wdrażać całego obrazu platformy Docker o rozmiarze 174 megabajtów, gdy kod źródłowy aplikacji zostanie zmieniony, ponieważ platforma Docker przekaże tylko różnice (których rozmiar jest znacznie mniejszy). Dzięki temu proces wykonywania nowej wersji aplikacji usługi MicroProfile za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania jest bardzo efektywny i szybki, ogranicza problemy oraz umożliwia szybkie tworzenie iteracji.

W tym samouczku najpierw utworzymy i uruchomimy kod lokalnie, a następnie wdrożymy go jako aplikację internetową na platformie Azure. W obu przypadkach będziemy korzystać z platformy Docker w celu uproszczenia i standaryzacji naszych wysiłków. Przed rozpoczęciem utworzymy rejestr usługi Azure Container Registry do przechowywania naszych kontenerów platformy Docker.

## <a name="creating-an-azure-container-registry"></a>Tworzenie rejestru usługi Azure Container Registry

Użyjemy witryny [Azure Portal](https://portal.azure.com), aby utworzyć rejestr usługi Azure Container Registry, ale należy pamiętać, że istnieją alternatywne opcje, takie jak Azure CLI. Wykonaj poniższe kroki, aby utworzyć nowy rejestr kontenerów platformy Azure:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i utwórz nowy zasób usługi Azure Container Registry. Podaj nazwę rejestru (należy pamiętać, że jest to nazwa, która powinna być ustawiona jako właściwość `docker.registry` w pliku `pom.xml`). Zmień wartości domyślne zależnie od potrzeb, a następnie kliknij przycisk „create” (utwórz).

1. Gdy rejestr kontenerów będzie dostępny (około 30 sekund po kliknięciu przycisku „create”), kliknij rejestr kontenerów, a następnie kliknij link „Access keys” (Klucze dostępu) w menu po lewej stronie. W tym miejscu należy włączyć ustawienie „admin user” (administrator), aby można było uzyskać dostęp do rejestru kontenerów z poziomu naszych maszyn (w celu wypchnięcia kontenerów platformy Docker), a także w celu włączenia dostępu z wystąpienia usługi Azure Web Apps for Containers, które wkrótce skonfigurujemy.

1. W obszarze „Access keys” (Klucze dostępu) zanotuj wartości `username` i `password`. Skopiujemy i wkleimy je do naszego globalnego pliku `settings.xml` narzędzia Maven. Aby uzyskać więcej informacji na temat ustawień narzędzia Maven, zapoznaj się z witryną internetową [projektu Apache Maven](https://maven.apache.org/settings.html). Jako odniesienie poniżej przedstawiono zaciemnioną wersję pliku `${user.home}/.m2/settings.xml` w systemie autora:

    ```xml
    <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                          https://maven.apache.org/xsd/settings-1.0.0.xsd">
        <servers>
          <server>
            <id>jogilescr.azurecr.io</id>
            <username>jogilescr</username>
            <password>ojoirshois.this-isn't-real.hrihslirhlishrglih</password>
          </server>
        </servers>
    </settings>
    ```

Po wykonaniu tej czynności możemy zacząć tworzenie i uruchamianie aplikacji usługi MicroProfile lokalnie.

## <a name="creating-our-microprofile-application"></a>Tworzenie aplikacji usługi MicroProfile

Ten przykład jest oparty na przykładowej aplikacji dostępnej w witrynie GitHub, więc sklonujemy ją, a następnie przeanalizujemy kod krok po kroku. Wykonaj poniższe kroki, aby sklonować kod na swoją maszynę:

1. `git clone https://github.com/Azure-Samples/microprofile-docker-helloworld.git`
1. `cd microprofile-docker-helloworld`

W tym katalogu znajduje się plik `pom.xml`, który służy do określania projektu w formacie używanym przez narzędzie Maven do kompilowania. Ten plik można edytować zgodnie z własnymi potrzebami. W szczególności właściwości `docker.registry` i `docker.name` należy zmienić na `docker.registry` i `docker.name` utworzone podczas konfiguracji rejestru kontenerów platformy Azure.

Innym plikiem wartym uwagi w tym katalogu jest plik Dockerfile, który przedstawiono poniżej:

```dockerfile
FROM payara/micro

ARG WAR_FILE
COPY target/${WAR_FILE} $DEPLOY_DIR

EXPOSE 8080
```

Ten plik Dockerfile po prostu tworzy nowy kontener platformy Docker na podstawie kontenera platformy Payara Micro dla platformy Docker, a następnie kopiuje plik war, który jest tworzony w ramach procesu kompilacji. Udostępnia również port 8080, dzięki czemu możemy uzyskać dostęp do usługi, gdy zostanie ona uruchomiona w kontenerze platformy Docker.

W katalogu `src` ostatecznie odkryjemy klasę `Application` przedstawioną poniżej:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld;

import javax.ws.rs.ApplicationPath;

@ApplicationPath("/api")
public class Application extends javax.ws.rs.core.Application { }
```

Adnotacja `@ApplicationPath("/api")` określa podstawowy punkt końcowy dla tej mikrousługi — to oznacza, że wszystkie punkty końcowe będą miały prefiks `/api` poprzedzający resztę adresu URL wymaganego do uzyskania dostępu do określonego punktu końcowego interfejsu REST.

Wewnątrz pakietu `api` jest klasa o nazwie `API`, która zawiera następujący kod:

```java
package com.microsoft.azure.samples.microprofile.docker.helloworld.api;

import javax.enterprise.context.ApplicationScoped;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;

import static javax.ws.rs.core.MediaType.TEXT_HTML;

@ApplicationScoped
@Path("/")
public class API {

    @GET
    @Path("/helloworld")
    @Produces(TEXT_HTML)
    public String info() {
        return "Hello, world!";
    }
}
```

Zastosowanie adnotacji `@Path("/helloworld")` sprawia, że ten punkt końcowy interfejsu REST, w połączeniu z prefiksem `/api` określonym w klasie `Application`, będzie mieć postać `/api/helloworld`. Gdy ten punkt końcowy jest wywoływany za pomocą żądania HTTP GET, można zauważyć, że ta metoda spowoduje wygenerowanie tekstu/kodu HTML, a w rzeczywistości jest to po prostu zakodowany ciąg „Hello, World!”.

Omówiliśmy teraz cały kod wymagany do utworzenia mikrousługi przy użyciu usługi MicroProfile. Teraz możemy użyć narzędzia Maven do skompilowania i dodania go do kontenera platformy Docker oraz uruchomić go lokalnie. Możemy to zrobić, wykonując następujące czynności:

1. Uruchom polecenie `mvn clean package` i poczekaj na jego pomyślne zakończenie.

1. Uruchom polecenie `docker run -it --rm -p 8080:8080 <docker.registry>/<docker.name>:latest`. Na przykład `docker run -it --rm -p 8080:8080 jogilescr.azurecr.io/samples/docker-helloworld:latest`, jeśli właściwość `docker.registry` ma wartość `jogilescr.azurecr.io`, a właściwość `docker.name` ma wartość `samples/docker-helloworld`.

1. Spróbuj otworzyć stronę [http://localhost:8080/microprofile/api/helloworld](http://localhost:8080/microprofile/api/helloworld) i [http://localhost:8080/health](http://localhost:8080/health) w przeglądarce internetowej. Jeśli zobaczysz oczekiwaną odpowiedź „Hello, World!” (i informacje dotyczące kondycji dla punktu końcowego [/health](http://localhost:8080/health)), oznacza to, że pomyślnie wdrożono aplikację usługi MicroProfile na komputerze lokalnym.

## <a name="pushing-to-the-azure-container-registry"></a>Wypychanie do usługi Azure Container Registry

Teraz, po pomyślnym skompilowaniu i uruchomieniu aplikacji usługi MicroProfile na komputerze lokalnym, następnym krokiem jest wypchnięcie tego kontenera do naszego rejestru kontenerów. W tym samouczku używamy usługi Azure Container Registry, ale każdy rejestr kontenerów będzie działał (o ile plik `pom.xml` zostanie edytowany w celu wskazania odpowiedniej lokalizacji).

1. Uruchom polecenie `mvn clean package`, aby wyczyścić, skompilować i utworzyć lokalny obraz platformy Docker.
2. Uruchom polecenie `mvn dockerfile:push`, aby wypchnąć go do usługi Azure Container Registry.

Na tym etapie obraz kontenera platformy Docker został przekazany do usługi Azure Container Registry, ale jeszcze nie jest uruchomiony, ponieważ trzeba wdrożyć go w wystąpieniu usługi Azure Web App for Containers. Zrobimy to teraz.

## <a name="creating-an-azure-web-app-for-containers-instance"></a>Tworzenie wystąpienia usługi Azure Web App for Containers

1. Wróć do witryny [Azure Portal](https://portal.azure.com) i utwórz nowe wystąpienie usługi Web App for Containers (w obszarze pod nagłówkiem „Sieć Web + aplikacje mobilne” w menu). Kilka wskazówek:

   1. Nazwa określona w tym miejscu będzie publicznym adresem URL aplikacji internetowej (mimo że w razie potrzeby można dodać domenę niestandardową), dlatego warto wybrać nazwę, którą można łatwo zapamiętać.

   1. Po wyświetleniu sekcji „Konfigurowanie kontenera” możesz wybrać opcję „Azure Container Registry” dla źródła obrazu, a następnie wybrać właściwy obraz z listy rozwijanej.

   1. Nie trzeba podawać żadnej wartości w polu „Plik startowy”.

1. Po utworzeniu wystąpienia (ponownie trwa to bardzo krótko), kliknij je, a następnie kliknij element menu „Ustawienia aplikacji”. W tym miejscu należy dodać nowe ustawienie aplikacji, gdzie klucz to `WEBSITES_PORT`, a wartość to `8080`. Informuje ono platformę Azure, który port ma zostać ujawniony w kontenerze, i zostanie on zamapowany na zewnętrzny port 80.

1. Opcjonalnie kliknij link „Kontener platformy Docker” i włącz opcję „Ciągłe wdrażanie", dzięki czemu po każdej aktualizacji obrazu usługi Azure Container Registry będzie on automatycznie aktualizowany w wystąpieniu usługi Azure Web App for Containers.

1. Powinno być możliwe uzyskanie dostępu do wystąpień hostowanych na platformie Azure pod adresem `http://<appname>.azurewebsites.net/microprofile/api/helloworld` i `http://<appname>.azurewebsites.net/health`.

## <a name="summary"></a>Podsumowanie

W tym samouczku omówiliśmy proces tworzenia prostej mikrousługi opartej na usłudze MicroProfile, przenosząc ją do kontenera platformy Docker, uruchamiając lokalnie i publikując na platformie Azure. Rozszerzenie tej mikrousługi o bardziej użyteczne funkcje wykracza poza zakres tego samouczka, ale w Internecie można znaleźć wiele artykułów i porad dotyczących usługi MicroProfile. Zachęcamy też do zapoznania się z materiałami w witrynie [MicroProfile.io](https://microprofile.io/).
