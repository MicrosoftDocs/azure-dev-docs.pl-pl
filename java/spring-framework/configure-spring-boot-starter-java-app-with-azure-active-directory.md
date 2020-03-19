---
title: Używanie szablonu startowego Spring Boot dla usługi Azure Active Directory
description: Dowiedz się, jak skonfigurować aplikację Spring Boot Initializer przy użyciu szablonu startowego usługi Azure Active Directory.
services: active-directory
documentationcenter: java
ms.date: 03/05/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 9b2915f6f754a62d53ecebb9251778c76fccce9a
ms.sourcegitcommit: 9f9f5c51472dbdd7b9304b02364ed136dcf81f1c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139559"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Samouczek: Zabezpieczanie aplikacji internetowej Java przy użyciu szablonu startowego Spring Boot dla usługi Azure Active Directory

## <a name="overview"></a>Omówienie

W tym artykule pokazano, jak utworzyć aplikację Java za pomocą narzędzia **[Spring Initializr]** , które korzysta z szablonu startowego Spring Boot dla usługi Azure Active Directory (Azure AD).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

 * Tworzenie aplikacji Java przy użyciu narzędzia Spring Initializr
 * Konfigurowanie usługi Azure Active Directory
 * Zabezpieczanie aplikacji za pomocą klas i adnotacji Spring Boot
 * Tworzenie i testowanie aplikacji Java

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

## <a name="create-an-app-using-spring-initializr"></a>Tworzenie aplikacji przy użyciu narzędzia Spring Initializr

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt **Maven** przy użyciu języka **Java**, wprowadź dla aplikacji nazwy w polach **Group** (Grupa) i **Artifact** (Artefakt).

   ![Określanie nazwy grupy i artefaktu][create-spring-app-01]

1. Przewiń ekran w dół i dodaj **zależności** dla środowiska **Spring Web**, usługi **Azure Active Directory** i rozwiązania **Spring Security**.

1. W dolnej części strony kliknij przycisk **Generuj**.

   ![Wybieranie szablonów startowych Security (Zabezpieczenia), Web (Sieć Web) i Azure Active Directory][create-spring-app-02]

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="create-azure-active-directory-instance"></a>Tworzenie wystąpienia usługi Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Tworzenie wystąpienia usługi Active Directory

1. Zaloguj się w witrynie <https://portal.azure.com>.

1. Kliknij kolejno pozycje **+Utwórz zasób**, później **Tożsamość**, a następnie **Azure Active Directory**.

   ![Tworzenie wystąpienia usługi Azure Active Directory][create-directory-01]

1. Wprowadź **Nazwę organizacji** i **Początkową nazwę domeny**. Skopiuj pełny adres URL katalogu. Będzie on używany do dodawania kont użytkowników w dalszej części tego samouczka. (Na przykład: `wingtiptoysdirectory.onmicrosoft.com`). 

Skopiuj pełny adres URL katalogu. Będzie on używany do dodawania kont użytkowników w dalszej części tego samouczka. (Na przykład: wingtiptoysdirectory.onmicrosoft.com).

Po zakończeniu kliknij przycisk **Utwórz**. Utworzenie nowego zasobu potrwa kilka minut.

   ![Określanie nazw Azure Active Directory][create-directory-02]

1. Po zakończeniu kliknij, aby uzyskać dostęp do nowego katalogu.

   ![Wybieranie nazwy konta platformy Azure][create-directory-03]

1. Skopiuj **identyfikator dzierżawy**. Użyjesz tej wartości do skonfigurowania pliku *application.properties* w dalszej części tego samouczka.

   ![Kopiuj swój identyfikator dzierżawy][create-directory-05]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Dodawanie rejestracji aplikacji dla aplikacji Spring Boot

1. W menu portalu kliknij pozycję **Rejestracje aplikacji**, a następnie kliknij pozycję **Zarejestruj aplikację**.

   ![Dodawanie rejestracji nowej aplikacji][create-app-registration-01]

1. Określ aplikację, a następnie kliknij pozycję **Zarejestruj**.

   ![Tworzenie rejestracji nowej aplikacji][create-app-registration-02]

1. Gdy zostanie wyświetlona strona rejestracji aplikacji, skopiuj **identyfikator aplikacji** i **identyfikator dzierżawy**. Te wartości zostaną użyte do skonfigurowania pliku *application.properties* w dalszej części tego samouczka.

   ![Tworzenie kluczy rejestracji aplikacji][create-app-registration-03]

1. Kliknij pozycję **Certyfikaty i wpisy tajne** w okienku nawigacji po lewej.  Następnie kliknij pozycję **Nowy wpis tajny klienta**.

   ![Tworzenie kluczy rejestracji aplikacji][create-app-registration-03.5]

1. Dodaj **opis** i wybierz czas trwania z listy **Wygasa**.  Kliknij pozycję **Add** (Dodaj). Wartość klucza zostanie automatycznie wypełniona.

   ![Określanie parametrów klucza rejestracji aplikacji][create-app-registration-04]

1. Skopiuj i zapisz wartość wpisu tajnego klienta, aby skonfigurować plik *application.properties* w dalszej części tego samouczka. (Nie będzie można później pobrać tej wartości).

   ![Określanie parametrów klucza rejestracji aplikacji][create-app-registration-04.5]

1. Kliknij pozycję **Uprawnienia interfejsu API** w okienku nawigacji po lewej. 

1. Na stronie **Uprawnienia interfejsu API** kliknij pozycję **Wyraź zgodę administratora** i po wyświetleniu monitu kliknij pozycję **Tak**.

   ![Udzielanie uprawnień dostępu][create-app-registration-08]

1. Na stronie głównej rejestracji aplikacji kliknij pozycję **Uwierzytelnianie**, a następnie kliknij pozycję **Dodaj platformę**.  Następnie kliknij pozycję **Aplikacje internetowe**.

    ![Edycja adresów URL odpowiedzi][create-app-registration-09]

1. Wprowadź wartość <http:<span></span>//localhost:8080/login/oauth2/code/azure> jako nowy **identyfikator URI przekierowania**, a następnie kliknij pozycję **Konfiguruj**.

    ![Dodawanie nowego adresu URL odpowiedzi][create-app-registration-10]

1. Na stronie głównej rejestracji aplikacji kliknij pozycję **Manifest**, ustaw wartość parametru `oauth2AllowImplicitFlow` na `true`, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie manifestu aplikacji][create-app-registration-11]

    > [!NOTE]
    > 
    > Aby uzyskać więcej informacji na temat parametru `oauth2AllowImplicitFlow` i innych ustawień aplikacji, zobacz [Azure Active Directory application manifest][AAD app manifest] (Manifest aplikacji Azure Active Directory). 
    >

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Dodawanie konta użytkownika do katalogu i dodawanie tego konta do grupy

1. Na stronie **Przegląd** katalogu Active Directory kliknij pozycję **Wszyscy użytkownicy**, a następnie kliknij pozycję **Nowy użytkownik**.

   ![Dodawanie nowego konta użytkownika][create-user-01]

1. Gdy zostanie wyświetlony panel **Użytkownik**, wprowadź **nazwę użytkownika** i **nazwę**.  Następnie kliknij pozycję **Utwórz**.

   ![Wprowadzanie informacji o koncie użytkownika][create-user-02]

   > [!NOTE]
   > 
   > Podczas wprowadzania nazwy użytkownika należy podać swój adres URL katalogu określony we wcześniejszej części tego samouczka. Na przykład:
   >
   > `wingtipuser@wingtiptoysdirectory.onmicrosoft.com`
   > 

1. Kliknij pozycję **Grupy**, a następnie **utwórz nową grupę**, której będziesz używać do autoryzacji w aplikacji.

1. Następnie kliknij pozycję **Nie wybrano żadnych członków**. (Na potrzeby tego samouczka utworzymy grupę o nazwie *users*).  Wyszukaj użytkownika utworzonego w poprzednim kroku.  Kliknij pozycję **Wybierz**, aby dodać użytkownika do grupy.  Następnie kliknij pozycję **Utwórz**, aby utworzyć nową grupę.

   ![Wybieranie użytkownika dla grupy][create-user-03]

1. Wróć do panelu **Użytkownicy**, wybierz użytkownika testowego, a następnie kliknij pozycję **Resetuj hasło** i skopiuj hasło. Będzie ono używane podczas logowania się do aplikacji w dalszej części tego samouczka. 

   ![Pokazywanie hasła][create-user-04]

## <a name="configure-and-compile-your-app"></a>Konfigurowanie i kompilowanie aplikacji

1. Wyodrębnij pliki z archiwum projektu utworzonego i pobranego wcześniej w tym samouczku do katalogu.

1. Przejdź do folderu nadrzędnego dla projektu, a następnie otwórz plik `pom.xml` projektu Maven w edytorze tekstów.

1. Dodaj zależności dotyczące zabezpieczeń Spring OAuth2 do pliku `pom.xml`:

   ```xml
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-jose</artifactId>
   </dependency>
   ```

1. Zapisz i zamknij plik *pom.xml*.

1. Przejdź do folderu *src/main/resources* w projekcie i otwórz plik *application.properties* w edytorze tekstów.

1. Określ ustawienia dotyczące rejestracji aplikacji przy użyciu utworzonych wcześniej wartości. Na przykład:

   ```yaml
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

   # Specifies your App Registration's Application ID:
   spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

   # Specifies your App Registration's secret key:
   spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.active-directory-groups=Users
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `azure.activedirectory.tenant-id` | Zawiera **Identyfikator katalogu** Active Directory, określony wcześniej. |
   | `spring.security.oauth2.client.registration.azure.client-id` | Zawiera **Identyfikator aplikacji** pochodzący z rejestracji aplikacji, która została wcześniej ukończona. |
   | `spring.security.oauth2.client.registration.azure.client-secret` | Zawiera **Wartość** pochodzącą z klucza rejestracji aplikacji, która została wcześniej ukończona. |
   | `azure.activedirectory.active-directory-groups` | Zawiera listę grup Active Directory, które mają być używane na potrzeby autoryzacji. |

   > [!NOTE]
   > 
   > Aby uzyskać pełną listę wartości, które są dostępne w pliku *application.properties*, zobacz [przykład Azure Active Directory Spring Boot][AAD Spring Boot Sample] w witrynie GitHub.
   >

1. Zapisz i zamknij plik *application.properties*.

1. Utwórz folder o nazwie *controller* w folderze źródłowym Java aplikacji. Na przykład: *src/main/java/com/wingtiptoys/security/controller*.

1. Utwórz nowy plik Java o nazwie *HelloController.java* w folderze *controller* i otwórz go w edytorze tekstów.

1. Wprowadź następujący kod, a następnie zapisz i zamknij plik:

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.access.prepost.PreAuthorize;
   import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
   import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
   import org.springframework.ui.Model;

   @RestController
   public class HelloController {
      @Autowired
      @PreAuthorize("hasRole('Users')")
      @RequestMapping("/")
      public String helloWorld() {
         return "Hello World!";
      }
   }
   ```
   > [!NOTE]
   > 
   > Nazwa grupy określona dla metody `@PreAuthorize("hasRole('')")` musi zawierać jedną z grup określonych w polu `azure.activedirectory.active-directory-groups` pliku *application.properties*.
   > 
   > Można również określić różne ustawienia autoryzacji dla różnych mapowań żądań. Na przykład:
   >
   > ``` java
   > public class HelloController {
   >    @Autowired
   >    @PreAuthorize("hasRole('Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('Group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('Group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```
   >    

1. Utwórz folder o nazwie *security* w folderze źródłowym Java aplikacji. Na przykład: *src/main/java/com/wingtiptoys/security/security*.

1. Utwórz nowy plik Java o nazwie *WebSecurityConfig.java* w folderze *security* i otwórz go w edytorze tekstów.

1. Wprowadź poniższy kod, a następnie zapisz i zamknij plik:

    ```java
    package com.wingtiptoys.security;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
    import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
    import org.springframework.security.oauth2.core.oidc.user.OidcUser;

    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Autowired
        private OAuth2UserService<OidcUserRequest, OidcUser> oidcUserService;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
        }
    }
    ```

## <a name="build-and-test-your-app"></a>Tworzenie i testowanie aplikacji

1. Otwórz wiersz polecenia i zmień katalog na folder, w którym znajduje się plik *pom.xml*.

1. Skompiluj swoją aplikację Spring Boot przy użyciu narzędzia Maven i uruchom ją, na przykład:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

   ![Kompilowanie aplikacji][build-application]

1. Po skompilowaniu i uruchomieniu aplikacji przez narzędzie Maven otwórz adres http:<span></span>//localhost:8080 w przeglądarce internetowej. Powinien zostać wyświetlony monit o podanie nazwy użytkownika i hasła.

   ![Logowanie do aplikacji][application-login]

   > [!NOTE]
   > 
   > Może pojawić się monit o zmianę hasła, jeśli jest to pierwsze logowanie na nowym koncie użytkownika.
   > 
   > ![Zmiana hasła][update-password]
   > 

1. Po pomyślnym zalogowaniu powinien zostać wyświetlony przykładowy tekst „Hello World” pochodzący z kontrolera.

   ![Pomyślne logowanie][hello-world]

   > [!NOTE]
   > 
   > Konta użytkowników, które nie są autoryzowane, otrzymają komunikat **HTTP 403 Brak autoryzacji**.
   >

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono nową aplikację sieci Web w języku Java przy użyciu szablonu startowego Azure Active Directory, skonfigurowano nową dzierżawę usługi Azure AD i zarejestrowano w niej nową aplikację, a następnie skonfigurowano aplikację do używania adnotacji i klas Spring do ochrony aplikacji sieci Web.

## <a name="see-also"></a>Zobacz też
* Aby uzyskać informacje na temat nowych opcji interfejsu użytkownika, zobacz [Nowy przewodnik szkoleniowy na temat rejestracji aplikacji w witrynie Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: /azure/java/
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-spring-boot-backend-sample

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png
[create-spring-app-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-02.png
[create-spring-app-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-03.png

[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png
[create-directory-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-05.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03.5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04.5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-06]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-06.png
[create-app-registration-07]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-07.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png
[create-app-registration-11]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-11.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-world]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-world.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png
