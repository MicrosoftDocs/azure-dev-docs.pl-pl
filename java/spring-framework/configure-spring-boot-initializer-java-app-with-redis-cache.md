---
title: Tworzenie aplikacji Spring Boot Initializr — usługa Azure Cache for Redis
description: Skonfiguruj aplikację Spring Boot utworzoną za pomocą narzędzia Spring Initializr do korzystania z usługi Redis w chmurze przy użyciu usługi Azure Cache for Redis.
services: redis-cache
documentationcenter: java
ms.date: 12/19/2018
ms.service: cache
ms.tgt_pltfrm: cache-redis
ms.topic: conceptual
ms.openlocfilehash: e70b5f9b8427bebd9c5ca3761a664464ad3b0909
ms.sourcegitcommit: 670874dfe49e6ffa5bee88555851878f0da93042
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2019
ms.locfileid: "77000348"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-redis-in-the-cloud-with-azure-redis-cache"></a>Konfigurowanie aplikacji Spring Boot Initializr do korzystania z usługi Redis w chmurze przy użyciu usługi Azure Cache for Redis

W tym artykule przedstawiono tworzenie pamięci podręcznej Redis w chmurze przy użyciu witryny Azure Portal, następnie użycie narzędzia **[Spring Initializr]** do utworzenia aplikacji niestandardowej i utworzenie aplikacji internetowej Java, która będzie przechowywać i pobierać dane przy użyciu pamięci podręcznej Redis.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

## <a name="create-a-custom-application-using-the-spring-initializr"></a>Tworzenie aplikacji niestandardowej przy użyciu narzędzia Spring Initializr

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt **Maven** przy użyciu języka **Java**, wprowadź nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt) dla aplikacji, a następnie kliknij link **Switch to the full version** (Przełącz do pełnej wersji), aby przejść do pełnej wersji narzędzia Spring Initializr.

   ![Podstawowe opcje narzędzia Spring Initializr][SI01]

   > [!NOTE]
   >
   > Narzędzie Spring Initializr użyje nazw z pól **Group** (Grupa) i **Artifact** (Artefakt), aby utworzyć nazwę pakietu, na przykład: *com.contoso.myazuredemo*.
   >

1. Przewiń w dół do sekcji **Web** (Sieć Web) i zaznacz pole **Web** (Sieć Web), następnie przewiń w dół do sekcji **NoSQL** i zaznacz pole **Redis**, a następnie przewiń w dół strony i kliknij przycisk **Generate Project** (Generuj projekt).

   ![Opcje pełnej wersji narzędzia Spring Initializr][SI02]

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

   ![Pobieranie niestandardowego projektu Spring Boot][SI03]

1. Po wyodrębnieniu plików w systemie lokalnym Twoja niestandardowa aplikacja Spring Boot będzie gotowa do edycji.

   ![Pliki niestandardowego projektu Spring Boot][SI04]

## <a name="create-a-redis-cache-on-azure"></a>Tworzenie pamięci podręcznej Redis na platformie Azure

1. Przejdź do witryny Azure Portal pod adresem <https://portal.azure.com/> i kliknij pozycję **+Nowy**.

   ![Azure Portal][AZ01]

1. Kliknij pozycję **Bazy danych**, a następnie pozycję **Redis Cache**.

   ![Azure Portal][AZ02]

1. Na stronie **Nowa pamięć podręczna Redis** podaj następujące informacje:

   * W polu **Nazwa DNS** podaj nazwę DNS pamięci podręcznej.
   * Wypełnij pola **Subskrypcja**, **Grupa zasobów**, **Lokalizacja** i **Warstwa cenowa**.
   * Na potrzeby tego samouczka zaznacz opcję **Odblokuj port 6379**.

   > [!NOTE]
   >
   > W przypadku pamięci podręcznych Redis można korzystać z protokołu SSL, ale trzeba wtedy użyć innego klienta usługi Redis, np. Jedis. Więcej informacji znajduje się w temacie [How to use Azure Redis Cache with Java][Redis Cache with Java] (Jak korzystać z usługi Azure Cache for Redis za pomocą języka Java).
   >

   Po określeniu tych opcji kliknij przycisk **Utwórz**, aby utworzyć pamięć podręczną.

   ![Azure Portal][AZ03]

1. Po utworzeniu pamięci podręcznej zostanie ona wyświetlona na liście na **pulpicie nawigacyjnym** platformy Azure, a także na stronach **Wszystkie zasoby** i **Pamięci podręczne Redis**. Możesz kliknąć pamięć podręczną w dowolnej z tych lokalizacji, aby otworzyć stronę właściwości pamięci podręcznej.

   ![Azure Portal][AZ04]

1. Po wyświetleniu strony zawierającej listę właściwości pamięci podręcznej kliknij pozycję **Klucze dostępu** i skopiuj klucze dostępu do pamięci podręcznej.

   ![Azure Portal][AZ05]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>Konfigurowanie niestandardowej aplikacji Spring Boot do korzystania z usługi Azure Cache for Redis

1. Znajdź plik *application.properties* w katalogu *resources* aplikacji lub utwórz ten plik, jeśli jeszcze nie istnieje.

   ![Znalezienie pliku application.properties][RE01]

1. Otwórz plik *application.properties* w edytorze tekstów, a następnie dodaj w pliku następujące wiersze i zastąp przykładowe wartości odpowiednimi właściwościami z pamięci podręcznej:

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6379

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![Edytowanie pliku application.properties][RE02]

   > [!NOTE] 
   > 
   > Jeśli używasz innego klienta Redis, np. Jedis, który włącza protokół SSL, musisz w pliku *application.properties* określić, że chcesz używać protokołu SSL i korzystać z portu 6380. Przykład:
   > 
   > ```yaml
   > # Specify the DNS URI of your Redis cache.
   > spring.redis.host=myspringbootcache.redis.cache.windows.net
   > # Specify the access key for your Redis cache.
   > spring.redis.password=57686f6120447564652c2049495320526f636b73=
   > # Specify that you want to use SSL.
   > spring.redis.ssl=true
   > # Specify the SSL port for your Redis cache.
   > spring.redis.port=6380
   > ```
   > 
   > Więcej informacji znajduje się w temacie [How to use Azure Redis Cache with Java][Redis Cache with Java] (Jak korzystać z usługi Azure Cache for Redis za pomocą języka Java). 
   > 

1. Zapisz i zamknij plik *application.properties*.

1. Utwórz folder o nazwie *controller* w folderze źródłowym pakietu, na przykład:

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   — lub —

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. Utwórz nowy plik o nazwie *HelloController.java* w folderze *controller*. Otwórz plik w edytorze tekstów i wpisz do niego następujący kod:

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.data.redis.core.ValueOperations;

   @RestController
   public class HelloController {
   
      @Autowired
      private StringRedisTemplate template;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         ValueOperations<String, String> ops = this.template.opsForValue();

         // Add a Hello World string to your cache.
         String key = "greeting";
         if (!this.template.hasKey(key)) {
             ops.set(key, "Hello World!");
         }

         // Return the string from your cache.
         return ops.get(key);
      }
   }
   ```
   
   Musisz zastąpić `com.contoso.myazuredemo` nazwą pakietu projektu.

1. Zapisz i zamknij plik *HelloController.java*.

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Przetestuj aplikację internetową, przechodząc do adresu http://localhost:8080 przy użyciu przeglądarki sieci Web lub, jeśli masz dostęp do narzędzia curl, użyj składni jak w przykładzie poniżej:

   ```shell
   curl http://localhost:8080
   ```

   Powinien zostać wyświetlony komunikat „Hello World!” z przykładowego kontrolera, który zostanie pobrany dynamicznie z pamięci podręcznej Redis.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Deploy a Spring Boot Application to the Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Aby uzyskać więcej informacji na temat korzystania z platformy Azure z językiem Java, zapoznaj się z tematami [Azure dla deweloperów języka Java] i [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

Aby uzyskać więcej informacji na temat rozpoczęcia pracy z usługą Redis Cache przy użyciu języka Java na platformie Azure, zobacz temat [How to use Azure Redis Cache with Java][Redis Cache with Java] (Jak korzystać z usługi Azure Cache for Redis za pomocą języka Java).

**[Platforma Spring]** jest rozwiązaniem open-source, które pomaga deweloperom języka Java tworzyć aplikacje na poziomie przedsiębiorstwa. Jednym z bardziej popularnych projektów opartych na tej platformie jest [Spring Boot], który oferuje uproszczoną metodę tworzenia autonomicznych aplikacji Java. Aby ułatwić deweloperom rozpoczęcie pracy z aplikacją Spring Boot, pod adresem <https://github.com/spring-guides/> udostępniono przykładowe pakiety aplikacji Spring Boot. Oprócz możliwości wyboru z listy podstawowych projektów Spring Boot narzędzie **[Spring Initializr]** pomaga deweloperom rozpocząć tworzenie niestandardowych aplikacji Spring Boot.

<!-- URL List -->

[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/java/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Platforma Spring]: https://spring.io/
[Redis Cache with Java]: /azure/redis-cache/cache-java-get-started

<!-- IMG List -->

[AZ01]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ01.png
[AZ02]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ02.png
[AZ03]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ03.png
[AZ04]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ04.png
[AZ05]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/AZ05.png

[SI01]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/SI01.png
[SI02]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/SI02.png
[SI03]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/SI03.png
[SI04]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/SI04.png

[RE01]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/RE01.png
[RE02]: ./media/configure-spring-boot-initializer-java-app-with-redis-cache/RE02.png
