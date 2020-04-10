---
title: Używanie szablonu startowego Spring Boot dla usługi Azure Active Directory B2C
description: Dowiedz się, jak skonfigurować aplikację Spring Boot Initializer przy użyciu szablonu startowego usługi Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: panli
ms.date: 02/06/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 63fa23a946841d284d229561e597ea1d9450e822
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740695"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>Samouczek: Zabezpieczenie aplikacji internetowej Java przy użyciu szablonu startowego Spring Boot dla usługi Azure Active Directory B2C.

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak utworzyć aplikację Java za pomocą narzędzia [Spring Initializr](https://start.spring.io/), które korzysta z szablonu startowego Spring Boot dla usługi Azure Active Directory (Azure AD).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji Java przy użyciu narzędzia Spring Initializr
> * Konfigurowanie usługi Azure Active Directory B2C
> * Zabezpieczanie aplikacji za pomocą klas i adnotacji Spring Boot
> * Tworzenie i testowanie aplikacji Java

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

## <a name="create-an-app-using-spring-initializr"></a>Tworzenie aplikacji przy użyciu narzędzia Spring Initializr

1. Przejdź do <https://start.spring.io/>.

2. Określ, że chcesz wygenerować projekt **Maven** przy użyciu języka **Java**, wprowadź dla aplikacji nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt), a następnie wybierz moduły **Web** (Sieć web) i **Security** (Zabezpieczenia) narzędzia Spring Initializr.

   ![Określanie nazw w polach Group (Grupa) i Aritifact (Artefakt)](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/si-n.png)


3. Kliknij `Generate Project` i po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="create-azure-active-directory-instance"></a>Tworzenie wystąpienia usługi Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Tworzenie wystąpienia usługi Active Directory

1. Zaloguj się w witrynie <https://portal.azure.com>.

2. Kliknij pozycję **+ Utwórz zasób**, pozycję **Tożsamość**, a następnie pozycję **Zobacz wszystko**.  Wyszukaj usługę **Azure Active Directory B2C**.

   ![Tworzenie nowego wystąpienia usługi Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. Kliknij pozycję **Utwórz**.

   ![Pobranie nazwy dzierżawy B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. Wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD B2C**.

   ![Tworzenie nowej usługi Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. Wprowadź nazwę organizacji i początkową nazwę domeny, a następnie zapisz nazwę domeny do użycia w przyszłości.  Kliknij pozycję **Utwórz**.

   ![Wybieranie katalogu Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. Po zakończeniu tworzenia usługi Active Directory przejdź do nowego katalogu.  Możesz też wyszukać frazę `b2c`, a następnie kliknąć usługę `Azure AD B2C`.

   ![Znajdowanie wystąpienia usługi Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Dodawanie rejestracji aplikacji dla aplikacji Spring Boot

1. Wybierz pozycję **Azure AD B2C** z menu portalu, kliknij pozycję **Aplikacje**, a następnie kliknij przycisk **Dodaj**.

   ![Dodawanie rejestracji nowej aplikacji](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. Określ nazwę aplikacji w polu **Nazwa** i dodaj wartość `http://localhost:8080/home` w polu **Identyfikator URI przekierowania**. Kliknij pozycję **Zapisz**.  Następnie zarejestruj **Identyfikator aplikacji** jako identyfikator `${your-client-id}`.  

   ![Dodawanie identyfikatora URI przekierowania aplikacji](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c2-n.png)

3. Wybierz pozycję **Certyfikaty i klucze tajne** z poziomu swojej aplikacji, kliknij pozycję **Wygeneruj klucz**, aby wygenerować klucz `${your-client-secret}`, a następnie kliknij przycisk **Zapisz**.

   ![Tworzenie przepływu użytkownika](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

4. Wybierz pozycję **Przepływy użytkownika** po lewej stronie, a następnie kliknij pozycję **Nowy przepływ użytkownika**.

5. Wybierz pozycję **Rejestrowanie lub logowanie**, **Edytowanie profilu** i **Resetowanie hasła**, aby odpowiednio utworzyć przepływy użytkownika. Wypełnij dla przepływu użytkownika pola **Nazwa** i **Atrybuty użytkownika i oświadczenia**, a następnie kliknij przycisk **Utwórz**.

   ![Konfigurowanie przepływu użytkownika](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c4-n.png)

## <a name="configure-and-compile-your-app"></a>Konfigurowanie i kompilowanie aplikacji

1. Wyodrębnij pliki z archiwum projektu utworzonego i pobranego wcześniej w tym samouczku do katalogu.

2. Przejdź do folderu nadrzędnego dla projektu, a następnie otwórz plik `pom.xml` projektu Maven w edytorze tekstów.

3. Dodaj zależności dotyczące zabezpieczeń Spring OAuth2 do pliku `pom.xml`:

   ```xml
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
       <version>2.1.6.M2</version>
   </dependency>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   <dependency>
       <groupId>org.thymeleaf.extras</groupId>
       <artifactId>thymeleaf-extras-springsecurity5</artifactId>
   </dependency>
   ```

4. Zapisz i zamknij plik *pom.xml*.

5. Przejdź do folderu *src/main/resources* w projekcie i otwórz plik *application.yml* w edytorze tekstów.

6. Określ ustawienia dotyczące rejestracji aplikacji przy użyciu utworzonych wcześniej wartości. Na przykład:

   ```yaml
   azure:
     activedirectory:
       b2c:
         tenant: ${your-tenant-name}
         client-id: ${your-client-id}
         client-secret: ${your-client-secret}
         reply-url: ${your-redirect-uri-from-aad} # should be absolute url.
         logout-success-url: ${you-logout-success-url}
         user-flows:
           sign-up-or-sign-in: ${your-sign-up-or-in-user-flow}
           profile-edit: ${your-profile-edit-user-flow}     # optional
           password-reset: ${your-password-reset-user-flow} # optional
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `azure.activedirectory.b2c.tenant` | Zawiera wcześniej użyty element `${your-tenant-name` usługi AD B2C. |
   | `azure.activedirectory.b2c.client-id` | Zawiera element `${your-client-id}` pochodzący z wcześniej utworzonej aplikacji. |
   | `azure.activedirectory.b2c.client-secret` | Zawiera element `${your-client-secret}` pochodzący z wcześniej utworzonej aplikacji. |
   | `azure.activedirectory.b2c.reply-url` | Zawiera jeden z elementów **Identyfikator URI przekierowania** pochodzący z wcześniej utworzonej aplikacji. |
   | `azure.activedirectory.b2c.logout-success-url` | Określ adres URL po pomyślnym wylogowaniu z aplikacji. |
   | `azure.activedirectory.b2c.user-flows` | Zawiera nazwę przepływów użytkownika, które zostały utworzone wcześniej.

   > [!NOTE]
   > 
   > Aby uzyskać pełną listę wartości, które są dostępne w pliku *application.yml*, zobacz [Przykład Azure Active Directory B2C Spring Boot][Przykład AAD B2C Spring Boot] w serwisie GitHub.
   >

7. Zapisz i zamknij plik *application.yml*.

8. Utwórz folder o nazwie *controller* w folderze źródłowym Java dla aplikacji.

9. Utwórz nowy plik Java o nazwie *WebController.java* w folderze *controller* i otwórz go w edytorze tekstów.

10. Wprowadź następujący kod, a następnie zapisz i zamknij plik:

    ```java
    package com.example.demo.controller;
    
    import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class WebController {
    
        private void initializeModel(Model model, OAuth2AuthenticationToken token) {
            if (token != null) {
                final OAuth2User user = token.getPrincipal();
    
                model.addAttribute("grant_type", user.getAuthorities());
                model.addAllAttributes(user.getAttributes());
            }
        }
    
        @GetMapping(value = "/")
        public String index(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);
    
            return "home";
        }
    
        @GetMapping(value = "/greeting")
        public String greeting(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);
    
            return "greeting";
        }
    
        @GetMapping(value = "/home")
        public String home(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);
    
            return "home";
        }
    }
    ```

11. Utwórz folder o nazwie *security* w folderze źródłowym Java dla aplikacji.

12. Utwórz nowy plik Java o nazwie *WebSecurityConfiguration.java* w folderze *security* i otwórz go w edytorze tekstów.

13. Wprowadź następujący kod, a następnie zapisz i zamknij plik:

    ```java
    package com.example.demo.security;
    
    import com.microsoft.azure.spring.autoconfigure.b2c.AADB2COidcLoginConfigurer;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    
    @EnableWebSecurity
    public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {
    
        private final AADB2COidcLoginConfigurer configurer;
    
        public WebSecurityConfiguration(AADB2COidcLoginConfigurer configurer) {
            this.configurer = configurer;
        }
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .apply(configurer)
            ;
        }
    }
    ```
14. Skopiuj pliki `greeting.html` i `home.html` z [przykładu Azure AD B2C Spring Boot](https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-b2c-oidc-spring-boot-sample/src/main/resources/templates), a następnie zastąp `${your-profile-edit-user-flow}` i `${your-password-reset-user-flow}` odpowiednimi nazwami przepływów użytkownika, które zostały utworzone wcześniej.

## <a name="build-and-test-your-app"></a>Kompilowanie i testowanie aplikacji

1. Otwórz wiersz polecenia i zmień katalog na folder, w którym znajduje się plik *pom.xml*.

2. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

3. Po skompilowaniu i uruchomieniu aplikacji przez narzędzie Maven otwórz adres <http://localhost:8080/> w przeglądarce internetowej. Powinno nastąpić przekierowanie na stronę logowania.

   ![Strona logowania](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. Kliknij link z nazwą przepływu użytkownika `${your-sign-up-or-in}`. Powinno nastąpić przekierowanie do usługi AD B2C w celu rozpoczęcia procesu uwierzytelniania.

   ![Logowanie do usługi Azure AD B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo2-n.png)

4. Po pomyślnym zalogowaniu w przeglądarce powinna zostać wyświetlona przykładowa strona `home page`:

   ![Pomyślne logowanie](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono nową aplikację internetową w języku Java przy użyciu szablonu startowego usługi Azure Active Directory B2C, skonfigurowano nową dzierżawę usługi Azure AD B2C i zarejestrowano w niej nową aplikację, a następnie skonfigurowano aplikację do używania adnotacji i klas Spring w celu ochrony aplikacji internetowej.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)
