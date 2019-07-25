---
title: Uwierzytelnianie przy użyciu zestawu Azure SDK dla języka Go
description: Informacje na temat metod uwierzytelniania dostępnych w zestawie Azure SDK dla języka Go i sposobu ich używania.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.devlang: go
ms.openlocfilehash: 34a3995b4eb5cc9012ca03b11fa8199460b9f9d4
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68292078"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a>Metody uwierzytelniania w zestawie Azure SDK dla języka Go

Zestaw Azure SDK dla języka Go oferuje różne sposoby uwierzytelniania na platformie Azure. Te _typy_ uwierzytelniania są wywoływane przez różne _metody_ uwierzytelniania. Ten artykuł zawiera opis dostępnych typów i metod oraz informacje pozwalające na wybranie najbardziej odpowiedniego rozwiązania dla Twojej aplikacji.

## <a name="available-authentication-types-and-methods"></a>Dostępne metody i typy uwierzytelniania

Zestaw Azure SDK dla języka Go oferuje kilka różnych typów uwierzytelniania korzystających z różnych zestawów poświadczeń. Każdy z tych typów uwierzytelniania jest udostępniany za pośrednictwem innych metod uwierzytelniania, które przedstawiają sposób traktowania tych poświadczeń jako danych wejściowych w zestawie SDK. W poniższej tabeli opisano dostępne typy uwierzytelniania i sytuacje, w których zalecamy ich użycie w aplikacji.

| Typ uwierzytelniania | Zalecane, gdy... |
|---------------------|---------------------|
| Uwierzytelnianie oparte na certyfikatach | Masz certyfikat X509 skonfigurowany dla jednostki lub użytkownika usługi Azure Active Directory (AAD). Aby dowiedzieć się więcej, zobacz [Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory]. |
| Poświadczenia klienta | Masz skonfigurowaną jednostkę usługi dla tej aplikacji lub klasy aplikacji, do której ona należy. Aby dowiedzieć się więcej, zobacz [Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure]. |
| Tożsamości zarządzane dla zasobów platformy Azure | Aplikacja działa na zasobie platformy Azure, który został skonfigurowany przy użyciu tożsamości zarządzanej. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure]. |
| Token urządzenia | Twoja aplikacja jest przeznaczona do korzystania z niej __tylko__ w sposób interaktywny. Użytkownicy mogą mieć włączone uwierzytelnianie wieloskładnikowe. Użytkownicy mogą logować się za pomocą przeglądarki internetowej. Aby uzyskać więcej informacji, zobacz [Używanie tokenu urządzenia](#use-device-token-authentication).|
| Nazwa użytkownika/hasło | Posiadasz interaktywną aplikację, dla której nie może być używana inna metoda uwierzytelniania. Użytkownicy nie mają włączonego uwierzytelniania wieloskładnikowego na potrzeby logowania do usługi AAD. |

> [!IMPORTANT]
> Jeśli używasz typu uwierzytelniania innego niż poświadczenia klienta, aplikacja musi zostać zarejestrowana w usłudze Azure Active Directory. Aby dowiedzieć się, jak to zrobić, zobacz [Integrating applications with Azure Active Directory (Integrowanie aplikacji za pomocą usługi Azure Active Directory)](/azure/active-directory/develop/active-directory-integrating-applications).
>
> [!NOTE]
> Jeśli nie masz specjalnych wymagań, unikaj uwierzytelniania nazwy użytkownika/hasła. W sytuacjach, w których logowanie oparte na użytkowniku jest właściwym rozwiązaniem, przeważnie można w zamian użyć uwierzytelniania tokenu urządzenia.

[Wprowadzenie do uwierzytelniania opartego na certyfikacie w usłudze Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure]: /cli/azure/create-an-azure-service-principal-azure-cli
[Tożsamości zarządzane dla zasobów platformy Azure]: /azure/active-directory/managed-identities-azure-resources/overview

Te typy uwierzytelniania są dostępne za pośrednictwem różnych metod.

* [_Uwierzytelnianie oparte na środowisku_](#use-environment-based-authentication) polega na odczytywaniu poświadczeń bezpośrednio ze środowiska programu.
* [_Uwierzytelnianie na podstawie pliku_](#use-file-based-authentication) polega na załadowaniu pliku zawierającego poświadczenia jednostki usługi.
* [_Uwierzytelnianie oparte na kliencie_](#use-an-authentication-client) polega na użyciu obiektu w kodzie i wyznaczeniu użytkownika jako osoby odpowiedzialnej za podawanie poświadczeń podczas wykonywania programu.
* [_Uwierzytelnianie tokenu urządzenia_](#use-device-token-authentication) wymaga od użytkowników interaktywnego logowania się przez przeglądarkę internetową przy użyciu tokenu.

Wszystkie typy i funkcje uwierzytelniania są dostępne w pakiecie `github.com/Azure/go-autorest/autorest/azure/auth`.

> [!NOTE]
> Jeśli nie masz specjalnych wymagań, unikaj uwierzytelniania opartego na kliencie. Ta metoda uwierzytelniania upraszcza stosowanie niewłaściwych rozwiązań. W szczególności użycie uwierzytelniania opartego na kliencie zachęca do umieszczania poświadczeń w kodzie. Pisanie kodu niestandardowego na potrzeby uwierzytelniania może zostać również wyeliminowane w przyszłych wydaniach zestawu SDK, jeśli zmienią się wymagania dotyczące uwierzytelniania.

## <a name="use-environment-based-authentication"></a>Używanie uwierzytelniania opartego na środowisku

Jeśli korzystasz z aplikacji w kontrolowanych ustawieniach, uwierzytelnianie oparte na środowisku jest naturalnym wyborem. W przypadku tej metody uwierzytelniania konfigurujesz środowisko powłoki przed uruchomieniem aplikacji. W czasie wykonywania zestaw SDK dla języka Go odczytuje te zmienne środowiskowe do uwierzytelniania za pomocą platformy Azure.

Uwierzytelnianie oparte na środowisku oferuje obsługę wszystkich metod uwierzytelniania, z wyjątkiem tokenów urządzeń, które są uwzględniane w następującej kolejności:

* Poświadczenia klienta
* Certyfikaty X509
* Nazwa użytkownika/hasło
* Tożsamości zarządzane dla zasobów platformy Azure

Jeśli typ uwierzytelniania ma nieustawione wartości lub następuje odmowa, zestaw SDK automatycznie próbuje użyć kolejnego typu uwierzytelniania. Gdy nie ma już typów do wypróbowania, zestaw SDK zwraca błąd.

W poniższej tabeli przedstawiono zmienne środowiskowe, które należy ustawić dla każdego typu uwierzytelniania obsługiwanego podczas uwierzytelniania opartego na środowisku.


|  Typ uwierzytelniania   |     Zmienna środowiskowa     |                                                                                                     Opis                                                                                                      |
|------------------------|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Poświadczenia klienta** |      `AZURE_TENANT_ID`       |                                                                    Identyfikator dzierżawy usługi Active Directory, do której należy jednostka usługi.                                                                     |
|                        |      `AZURE_CLIENT_ID`       |                                                                                       Nazwa lub identyfikator jednostki usługi.                                                                                       |
|                        |    `AZURE_CLIENT_SECRET`     |                                                                                  Wpis tajny skojarzony z jednostką usługi.                                                                                   |
|    **Certyfikat**     |      `AZURE_TENANT_ID`       |                                                                   Identyfikator dzierżawy usługi Active Directory, przy użyciu której zarejestrowano certyfikat.                                                                    |
|                        |      `AZURE_CLIENT_ID`       |                                                                              Identyfikator klienta aplikacji skojarzony z certyfikatem.                                                                              |
|                        |   `AZURE_CERTIFICATE_PATH`   |                                                                                       Ścieżka do pliku certyfikatu klienta.                                                                                       |
|                        | `AZURE_CERTIFICATE_PASSWORD` |                                                                                       Hasło certyfikatu klienta.                                                                                       |
| **Nazwa użytkownika/hasło**  |      `AZURE_TENANT_ID`       |                                                                           Identyfikator dzierżawy usługi Active Directory, do której należy użytkownik.                                                                           |
|                        |      `AZURE_CLIENT_ID`       |                                                                                              Identyfikator klienta aplikacji.                                                                                              |
|                        |       `AZURE_USERNAME`       |                                                                                            Nazwa użytkownika na potrzeby logowania się.                                                                                             |
|                        |       `AZURE_PASSWORD`       |                                                                                            Hasło na potrzeby logowania się.                                                                                             |
|  **Tożsamość zarządzana**  |                              | Do uwierzytelniania z użyciem tożsamości zarządzanych nie są wymagane żadne poświadczenia. Aplikacja musi działać na zasobie platformy Azure skonfigurowanym do używania tożsamości zarządzanych. Aby uzyskać szczegółowe informacje, zobacz [Tożsamości zarządzane dla zasobów platformy Azure]. |

Aby nawiązać połączenie z punktem końcowym chmury lub zarządzania innym niż domyślna publiczna chmura platformy Azure, ustaw poniższe zmienne środowiskowe. Najbardziej typowe przyczyny ich ustawiania to korzystanie z usługi Azure Stack, z chmury w innym regionie geograficznym lub z klasycznego modelu wdrożenia platformy Azure.

| Zmienna środowiskowa | Opis  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | Nazwa środowiska chmury, z którym będzie nawiązywane połączenie. |
| `AZURE_AD_RESOURCE` | Identyfikator zasobu usługi Active Directory używany podczas nawiązywania połączenia jako identyfikator URI punktu końcowego zarządzania. |

W przypadku korzystania z uwierzytelniania opartego na środowisku należy wywołać funkcję [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment), aby pobrać obiekt autoryzatora. Ten obiekt jest następnie ustawiany na właściwość `Authorizer` klientów, aby umożliwić im dostęp do platformy Azure.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a>Uwierzytelnianie w usłudze Azure Stack

Na potrzeby uwierzytelniania w usłudze Azure Stack należy ustawić następujące zmienne:

| Zmienna środowiskowa | Opis  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | Punkt końcowy usługi Azure Active Directory. |
| `AZURE_AD_RESOURCE` | Identyfikator zasobu usługi Active Directory. |

Te zmienne można pobrać z informacji o metadanych usługi Azure Stack. Aby pobrać metadane, otwórz przeglądarkę internetową w środowisku Azure Stack i skorzystaj z adresu URL: `(ResourceManagerURL)/metadata/endpoints?api-version=1.0`

Adres `ResourceManagerURL` różni się w zależności od nazwy regionu, nazwy komputera i zewnętrznej, w pełni kwalifikowanej nazwy domeny (FQDN) wdrożenia usługi Azure Stack:

| Środowisko | ResourceManagerURL |
|----------------------|--------------|
| Zestaw deweloperski | `https://management.local.azurestack.external/` |
| Zintegrowane systemy | `https://management.(region).ext-(machine-name).(FQDN)` |

Aby uzyskać więcej informacji na temat korzystania z zestawu Azure SDK dla języka Go w usłudze Azure Stack, zobacz [Use API version profiles with Go in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go) (Korzystanie z profilów wersji interfejsu API za pomocą języka Go w usłudze Azure Stack).

## <a name="use-file-based-authentication"></a>Używanie uwierzytelniania opartego na pliku

Uwierzytelnianie oparte na pliku korzysta z formatu pliku wygenerowanego przez [interfejs wiersza polecenia platformy Azure](/cli/azure). Można łatwo utworzyć ten plik, tworząc nową jednostkę usługi przy użyciu parametru `--sdk-auth`. Jeśli planujesz użycie uwierzytelniania opartego na pliku, upewnij się, że ten argument zostanie podany podczas tworzenia jednostki usługi. Ponieważ interfejs wiersza polecenia wyświetla dane wyjściowe w lokalizacji `stdout`, przekieruj dane wyjściowe do pliku.

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

Ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` na lokalizację, w której znajduje się plik autoryzacji. Ta zmienna środowiskowa jest odczytywana przez aplikację, a poświadczenia w jej obrębie są analizowane. Jeśli musisz wybrać plik autoryzacji w czasie wykonywania, zmodyfikuj środowisko programu przy użyciu funkcji [os.Setenv](https://golang.org/pkg/os/#Setenv).

Aby załadować informacje o uwierzytelnianiu, wywołaj funkcję [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile). W przeciwieństwie do autoryzacji opartej na środowisku autoryzacja na podstawie pliku wymaga punktu końcowego zasobu.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

Aby uzyskać więcej informacji na temat używania jednostek usług i zarządzania ich uprawnieniami dostępu, zobacz [Tworzenie jednostki usługi przy użyciu interfejsu wiersza polecenia platformy Azure].

## <a name="use-device-token-authentication"></a>Używanie uwierzytelniania tokenu urządzenia

Jeśli chcesz, aby użytkownicy logowali się interaktywnie, najlepszym sposobem jest uwierzytelnianie tokenu urządzenia. Ten przebieg uwierzytelniania przekazuje użytkownikowi token do wklejenia w witrynie logowania firmy Microsoft. Następnie użytkownik może się w niej uwierzytelnić za pomocą konta usługi Azure Active Directory (AAD). Ta metoda uwierzytelniania obsługuje konta z włączonym uwierzytelnianiem wieloskładnikowym,w przeciwieństwie do standardowego uwierzytelniania za pomocą nazwy użytkownika/hasła.

Aby użyć uwierzytelniania tokenu urządzenia, należy utworzyć autoryzator [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) przy użyciu funkcji [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig). Wywołaj element [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) w obiekcie wynikowym, aby rozpocząć proces uwierzytelniania. Uwierzytelnianie przepływu urządzenia blokuje wykonywanie programu do momentu ukończenia całego przepływu uwierzytelniania.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a>Używanie klienta uwierzytelniania

Jeśli potrzebujesz określonego typu uwierzytelniania i chcesz, aby program wykonał zadania związane z ładowaniem informacji o uwierzytelnianiu od użytkownika, możesz użyć dowolnego klienta zgodnego z interfejsem [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig). Użyj typu, który zaimplementuje ten interfejs, w następujących sytuacjach:

* Napisanie programu interaktywnego
* Użycie specjalnych plików konfiguracji
* Istnienie wymagania, które nie pozwala na użycie wbudowanej metody uwierzytelniania

> [!WARNING]
> Nigdy nie umieszczaj poświadczeń platformy Azure w kodzie aplikacji. Wprowadzanie wpisów tajnych do pliku binarnego aplikacji ułatwia osobie atakującej ich wyodrębnienie, bez względu na to, czy aplikacja została uruchomiona. Powoduje to narażenie na ryzyko wszystkich zasobów platformy Azure z autoryzowanymi poświadczeniami!

W poniższej tabeli przedstawiono listę typów w zestawie SDK, które są zgodne z interfejsem `AuthorizerConfig`.

| Typ uwierzytelniania | Typ autoryzatora |
|---------------------|-----------------------|
| Uwierzytelnianie oparte na certyfikacie | [ClientCertificateConfig] |
| Poświadczenia klienta | [ClientCredentialsConfig] |
| Tożsamości zarządzane dla zasobów platformy Azure | [MSIConfig] |
| Nazwa użytkownika/hasło | [UsernamePasswordConfig] |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

Utwórz wystawcę uwierzytelniania przy użyciu skojarzonej funkcji `New`, a następnie wywołaj instrukcję `Authorize` w obiekcie wynikowym w celu uwierzytelnienia. Aby na przykład używać uwierzytelniania opartego na certyfikacie:

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
