---
title: Wdrażanie maszyny wirtualnej platformy Azure z poziomu kodu Go
description: Wdróż maszynę wirtualną za pomocą zestawu Azure SDK dla języka Go.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: quickstart
ms.devlang: go
ms.openlocfilehash: 01f6e40e80a4c5f29a6179869a2fd95f6cea0623
ms.sourcegitcommit: 2efdb9d8a8f8a2c1914bd545a8c22ae6fe0f463b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "68291959"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>Szybki start: wdrażanie maszyny wirtualnej platformy Azure za pomocą szablonu i zestawu Azure SDK dla języka Go

Ten przewodnik Szybki start przedstawia sposób wdrażania zasobów z szablonu usługi Azure Resource Manager przy użyciu zestawu Azure SDK dla języka Go. Szablony są migawkami wszystkich zasobów zawartych w [grupie zasobów platformy Azure](/azure/azure-resource-manager/resource-group-overview). W ramach tej procedury poznasz funkcje i konwencje zestawu SDK.

Na końcu tego przewodnika znajdują się dane działającej maszyny wirtualnej, na której możesz się zalogować się przy użyciu nazwy użytkownika i hasła.

> [!NOTE]
> Aby zobaczyć tworzenie maszyny wirtualnej w języku Go bez użycia szablonu usługi Resource Manager, przejdź do [przykładu](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) przedstawiającego sposób tworzenia i konfigurowania wszystkich zasobów maszyn wirtualnych za pomocą zestawu SDK. Zastosowanie szablonu w tym przykładzie umożliwia skupienie się na konwencjach zestawu SDK bez zagłębiania się w zbyt wiele szczegółów dotyczących architektury usługi platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

W przypadku instalacji lokalnej interfejsu wiersza polecenia platformy Azure ten przewodnik Szybki start wymaga interfejsu wiersza polecenia w wersji __2.0.28__ lub nowszej. Uruchom polecenie `az --version`, aby upewnić się, że dany interfejs wiersza polecenia spełnia ten wymóg. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="install-the-azure-sdk-for-go"></a>Instalowanie zestawu Azure SDK dla języka Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby można było zalogować się do platformy Azure za pomocą aplikacji w sposób nieinterakcyjny, potrzebna jest jednostka usługi. Jednostki usługi stanowią część kontroli dostępu na podstawie ról (RBAC) tworzącej unikatową tożsamość użytkownika. Aby utworzyć nową jednostkę usługi za pomocą interfejsu wiersza polecenia, uruchom następujące polecenie:

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

Ustaw zmienną środowiskową `AZURE_AUTH_LOCATION` tak, aby była pełną ścieżką do tego pliku. Następnie zestaw SDK zlokalizuje i odczyta poświadczenia bezpośrednio z tego pliku, bez konieczności wprowadzania zmian ani rejestrowania informacji z jednostki usługi.

## <a name="get-the-code"></a>Uzyskiwanie kodu

Kod szybkiego startu oraz wszystkie jego zależności można pobrać, korzystając z polecenia `go get`.

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

Nie trzeba wprowadzać modyfikacji kodu źródłowego, jeśli zmienna `AZURE_AUTH_LOCATION` została poprawnie ustawiona. Po uruchomieniu program ładuje wszystkie niezbędne informacje dotyczące uwierzytelniania z tego miejsca.

## <a name="running-the-code"></a>Wykonywanie kodu

Uruchom szybki start za pomocą polecenia `go run`.

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

Jeśli wdrażanie zakończy się pomyślnie, zostanie wyświetlony komunikat zawierający nazwę użytkownika, adres IP i hasło umożliwiające zalogowanie się na nowo utworzonej maszynie wirtualnej. Spróbuj zalogować się do tej maszyny wirtualnej za pomocą protokołu SSH, aby sprawdzić, czy jest uruchomiona i działa. 

## <a name="cleaning-up"></a>Czyszczenie

Wyczyść zasoby utworzone w ramach tego szybkiego startu, usuwając grupę zasobów przy użyciu interfejsu wiersza polecenia.

```azurecli-interactive
az group delete -n GoVMQuickstart
```

Usuń również utworzoną wcześniej jednostkę usługi. W pliku `quickstart.auth` znajduje się klucz JSON dla elementu `clientId`. Skopiuj tę wartość do zmiennej środowiskowej `CLIENT_ID_VALUE` i uruchom następujące polecenie w wierszu polecenia platformy Azure:

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

Tu podaj wartość `CLIENT_ID_VALUE` pobraną z pliku `quickstart.auth`.

> [!WARNING]
> Nieusunięcie jednostki usługi dla tej aplikacji pozostawi ją aktywną w Twojej dzierżawie usługi Azure Active Directory.
> Chociaż nazwa i hasło dla jednostki usługi są generowane jako identyfikatory UUID, ze względów bezpieczeństwa usuń wszystkie nieużywane jednostki usługi i aplikacje platformy Azure Active Directory.

## <a name="code-in-depth"></a>Szczegóły kodu

Działanie kodu szybkiego startu jest podzielone na bloki zmiennych i kilka małych funkcji. Opisano je poniżej.

### <a name="variables-constants-and-types"></a>Zmienne, stałe i typy

Ponieważ przewodnik Szybki start jest używany jako samodzielny dokument, są w nim stosowane stałe i zmienne.

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

Deklarowane wartości zapewniają nazwy utworzonych zasobów. Podano tutaj także lokalizację. Można ją zmienić, aby sprawdzić zachowanie wdrożeń w innych centrach danych. Nie każde centrum danych ma dostępne wszystkie wymagane zasoby.

Typ `clientInfo` przechowuje informacje załadowane z pliku uwierzytelniania w celu skonfigurowania klientów w zestawie SDK i ustawienia hasła maszyny wirtualnej.

Stałe `templateFile` i `parametersFile` wskazują pliki potrzebne do wdrożenia. Element `authorizer` zostanie skonfigurowany przez zestaw SDK języka Go w celu uwierzytelnienia, a zmienna `ctx` to [kontekst języka Go](https://blog.golang.org/context) dla operacji sieciowych.

### <a name="authentication-and-initialization"></a>Uwierzytelnianie i inicjowanie

Funkcja `init` konfiguruje uwierzytelnianie. Uwierzytelnianie jest warunkiem wstępnym w każdym procesie szybkiego startu, a zatem warto uczynić je częścią inicjowania. Powoduje ono również pobieranie niektórych potrzebnych informacji z pliku uwierzytelniania w celu skonfigurowania klientów i maszyny wirtualnej.

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

Najpierw plik [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) jest wywoływany w celu załadowania informacji dotyczących uwierzytelniania z pliku znajdującego się w lokalizacji `AZURE_AUTH_LOCATION`. Następnie ten plik jest ładowany ręcznie przez funkcję `readJSON` (tutaj została pominięta) w celu ściągnięcia dwóch wartości wymaganych do uruchomienia reszty programu: Identyfikator subskrypcji klienta i wpis tajny jednostki usługi, który jest też używany na potrzeby hasła maszyny wirtualnej.

> [!WARNING]
> W celu uproszczenia przewodnika Szybki start zostanie ponownie użyte hasło jednostki usługi. Pamiętaj, aby w środowisku produkcyjnym __nigdy__ nie używać ponownie hasła zapewniającego dostępu do zasobów platformy Azure.

### <a name="flow-of-operations-in-main"></a>Przepływ operacji w funkcji main()

Funkcja `main` jest prosta — wskazuje tylko przepływ operacji i przeprowadza sprawdzanie błędów.

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

Oto działania wykonywane przez kod (z zachowaniem kolejności):

* Utworzenie grupy zasobów, w której ma zostać przeprowadzone wdrażanie (`createGroup`)
* Utworzenie wdrożenia w tej grupie (`createDeployment`)
* Uzyskiwanie i wyświetlanie danych logowania dotyczących wdrożonej maszyny wirtualnej (`getLogin`)

### <a name="create-the-resource-group"></a>Tworzenie grupy zasobów

Funkcja `createGroup` umożliwia utworzenie grupy zasobów. Przepływ wywołań i argumenty pozwalają zapoznać się ze strukturą interakcji z usługami w zestawie SDK.

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

Ogólny przebieg interakcji z usługą platformy Azure wygląda następująco:

* Utworzenie klienta przy użyciu metody `service.New*Client()`, gdzie `*` to typ zasobu `service`, z którym ma nastąpić interakcja. Ta funkcja zawsze pobiera identyfikator subskrypcji.
* Ustawienie metody autoryzacji klienta z uwzględnieniem interakcji ze zdalnym interfejsem API.
* Wywołanie metody na kliencie odpowiadającym zdalnemu interfejsowi API. Metody klienta usługi zazwyczaj pobierają nazwę zasobu i obiektu metadanych.

Funkcja [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) służy tutaj do przeprowadzania konwersji typów. Parametry metod zestawu SDK przyjmują niemal wyłącznie wskaźniki, dlatego są udostępniane są metody ułatwiające konwersję typów. Pełną listę konwerterów i opis ich zachowania można znaleźć w dokumentacji modułu [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to).

Metoda `groupsClient.CreateOrUpdate` zwraca wskaźnik do typu danych reprezentującego grupę zasobów. Bezpośrednia zwracana wartość tego rodzaju wskazuje krótkotrwałą operację, która ma być synchroniczna. W następnej sekcji przedstawimy przykład długotrwałej operacji i sposób interakcji z nią.

### <a name="perform-the-deployment"></a>Wykonywanie wdrożenia

Po utworzeniu grupy zasobów można uruchomić wdrożenie. Ten kod został podzielony na mniejsze części w celu wyróżnienia różnych części logiki kodu.

```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

Pliki wdrożenia są ładowane przez element `readJSON` (szczegóły zostały tutaj pominięte). Ta funkcja zwraca typ `*map[string]interface{}` używany podczas tworzenia metadanych dla wywołania wdrażania zasobów. Hasło maszyny wirtualnej jest również ustawiane ręcznie w obszarze parametrów wdrożenia.

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

    deploymentFuture, err := deploymentsClient.CreateOrUpdate(
        ctx,
        resourceGroupName,
        deploymentName,
        resources.Deployment{
            Properties: &resources.DeploymentProperties{
                Template:   template,
                Parameters: params,
                Mode:       resources.Incremental,
            },
        },
    )
    if err != nil {
        return
    }
```

Ten kod jest zgodny z tym samym wzorcem używanym podczas tworzenia grupy zasobów. Zostaje utworzony nowy klient (pod warunkiem, że uwierzytelnienie w usłudze Azure jest możliwe), a następnie zostaje wywołana metoda.
Metoda ma nawet taką samą nazwę (`CreateOrUpdate`) co analogiczna metoda dotycząca grupy zasobów. Ten wzorzec jest widoczny w zestawie SDK.
Metody, które mają podobne działanie, mają zwykle taką samą nazwę.

Największa różnica dotyczy wartości zwracanej przez metodę `deploymentsClient.CreateOrUpdate`. Ta wartość ma typ [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) zgodny z [wzorcem projektowym obiektu future](https://en.wikipedia.org/wiki/Futures_and_promises). Obiekty future reprezentują długotrwałą operację na platformie Azure, którą można sondować, anulować lub blokować po ich wykonaniu.

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

W przypadku tego przykładu najlepiej zaczekać na ukończenie operacji. Oczekiwanie na obiekt future wymaga zarówno [obiektu kontekstu](https://blog.golang.org/context), jak i klienta, który utworzył obiekt `Future`. Istnieją tutaj dwa możliwe źródła błędu: błąd występujący po stronie klienta podczas próby wywołania metody oraz odpowiedź serwera z informacją o błędzie. Ta druga jest zwracana jako część wywołania `deploymentFuture.Result`.

### <a name="get-the-assigned-ip-address"></a>Uzyskiwanie przypisanego adresu IP

Aby można było korzystać z nowo utworzonej maszyny wirtualnej, trzeba mieć przypisany adres IP. Adresy IP stanowią oddzielne zasoby platformy Azure powiązane z zasobami kontrolera interfejsu sieciowego.

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

Ta metoda korzysta z informacji przechowywanych w pliku parametrów. Kod może wysłać zapytanie bezpośrednio do maszyny wirtualnej, aby uzyskać dane jej kontrolera interfejsu sieciowego, wysłać zapytanie do kontrolera interfejsu sieciowego, aby uzyskać jego adres IP, a następnie wysłać zapytanie bezpośrednio do zasobu z danym adresem IP. Jest to długi łańcuch zależności. Wymaga on wykonania wielu operacji, przez co jest kosztowny. Ponieważ dane JSON są lokalne, można je załadować zamiast wykonywania powyższych operacji.

Wartość dla użytkownika maszyny wirtualnej jest również ładowana z kodu JSON. Hasło maszyny wirtualnej zostało wcześniej załadowane z pliku uwierzytelniania.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start istniejący szablon został wdrożony za pomocą języka Go. Następnie nastąpiło nawiązanie połączenia z nowo utworzoną maszyną wirtualną za pośrednictwem protokołu SSH.

Aby uzyskać więcej informacji o pracy z maszynami wirtualnymi w środowisku platformy Azure z użyciem języka Go, zapoznaj się z [przykładami dotyczącymi obliczeń na platformie Azure dla języka Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) lub [przykładami dotyczącymi zarządzania zasobami na platformie Azure dla języka Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).

Aby dowiedzieć się więcej na temat metod uwierzytelniania dostępnych w zestawie SDK oraz obsługiwanych przez typów uwierzytelniania, zobacz [Uwierzytelnianie przy użyciu zestawu Azure SDK dla języka Go](azure-sdk-go-authorization.md).
