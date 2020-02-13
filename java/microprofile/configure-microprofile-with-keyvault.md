---
title: Konfigurowanie usługi MicroProfile z usługą Azure Key Vault
description: Dowiedz się, jak wstrzyknąć wpisy tajne do usługi internetowej MicroProfile przy użyciu usługi Azure Key Vault.
services: key-vault
documentationcenter: java
author: jonathangiles
ms.author: jogiles
ms.date: 09/07/2018
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 7c09b47ccdc65ab4d6aa0ab0443fd64ef273a205
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999893"
---
# <a name="configure-microprofile-with-azure-key-vault"></a>Konfigurowanie usługi MicroProfile z usługą Azure Key Vault

W tym samouczku przedstawiono sposób konfigurowania aplikacji usługi [MicroProfile](http://microprofile.io) w celu pobierania wpisów tajnych z usługi [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) przy użyciu [interfejsów API konfiguracji usługi MicroProfile](https://microprofile.io/project/eclipse/microprofile-config), aby utworzyć połączenie bezpośrednie z usługą Azure Key Vault. Interfejsy API konfiguracji usługi MicroProfile umożliwiają deweloperom korzystanie ze standardowego interfejsu API w celu pobierania i dodawania danych konfiguracyjnych do swoich mikrousług.

Zanim przejdziemy do szczegółowego omówienia, zapoznajmy się szybko z informacjami na temat kombinacji usługi Azure Key Vault i interfejsu API konfiguracji usługi MicroProfile, aby napisać kod. Oto fragment kodu pola w klasie, do której dodano adnotacje `@Inject` i `@ConfigProperty`. Ciąg `name` określony w adnotacji jest nazwą właściwości do wyszukania w usłudze Azure Key Vault, a właściwość `defaultValue` określa wartość, która zostanie ustawiona, jeśli klucz nie zostanie odnaleziony. W efekcie wartość jest przechowywana w usłudze Azure Key Vault lub wartość domyślna zostanie automatycznie wstawiana w tym polu podczas wykonywania, upraszczając pracę deweloperów, ponieważ nie muszą oni przekazywać wartości w konstruktorach i metodach ustawiających, a zamiast tego to działanie jest obsługiwane przez usługę MicroProfile.

```java
@Inject
@ConfigProperty(name = "key-name", defaultValue = "Unknown")
String keyValue;
```

W razie potrzeby można również przejść bezpośrednio do konfiguracji usługi MicroProfile, aby zażądać wpisów tajnych, np.:

```java
public class DemoClass {
    @Inject
    Config config;

    public void method() {
        System.out.println("Hello: " + config.getValue("key-name", String.class));
    }
}
```

W tym przykładzie użyto platformy [Payara Micro](https://www.payara.fish/payara_micro) i usługi [MicroProfile](https://microprofile.io/) w celu utworzenia niewielkiego pliku war języka Java, który można uruchomić lokalnie na komputerze. Nie pokazano w nim, jak utworzyć kontener platformy Docker lub wypchnąć kod na platformę Azure, ale w sekcji linków na końcu tego samouczka znajdują się linki do innych przydatnych samouczków, w który zostało to wyjaśnione.

W tym przykładzie użyto bezpłatnej biblioteki typu „open source”, która tworzy źródło konfiguracji (przy użyciu interfejsu API konfiguracji usługi MicroProfile) dla usługi Azure Key Vault. Możesz dowiedzieć się więcej o tej bibliotece i przejrzeć kod na [stronie projektu w witrynie GitHub](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault). Dzięki zastosowaniu tej biblioteki, w tym samouczku możemy po prostu skupić się na kodzie związanym z konfigurowaniem biblioteki, a następnie wstrzyknąć klucze do kodu i nie musimy pisać żadnego kodu specyficznego dla platformy Azure.

Poniżej przedstawiono kroki wymagane do uruchomienia tego kodu na komputerze lokalnym, począwszy od utworzenia zasobu usługi Azure Key Vault.

## <a name="creating-an-azure-key-vault-resource"></a>Tworzenie zasobu usługi Azure Key Vault

Użyjemy interfejsu wiersza polecenia platformy Azure, aby utworzyć zasób usługi Azure Key Vault i wypełnić go jednym wpisem tajnym.

1. Najpierw utwórzmy jednostkę usługi platformy Azure. Zapewni to nam identyfikator klienta i klucz, których potrzebujemy, aby uzyskać dostęp do usługi Key Vault:

```bash
az login
az account set --subscription <subscription_id>

az ad sp create-for-rbac --name <service_principal_name>
```

Załóżmy, że użyliśmy `microprofile-keyvault-service-principal` jako nazwy jednostki usługi w poprzednim kroku. Odpowiedź platformy Azure na tę operację będzie mieć następującą postać (została częściowo ocenzurowana):

```json
{
  "appId": "5292398e-XXXX-40ce-XXXX-d49fXXXX9e79",
  "displayName": "microprofile-keyvault-service-principal",
  "name": "http://microprofile-keyvault-service-principal",
  "password": "9b217777-XXXX-4954-XXXX-deafXXXX790a",
  "tenant": "72f988bf-XXXX-41af-XXXX-2d7cd011db47"
}
```

Szczególnie istotne są tutaj wartości `appID` i `password` — użyjemy ich później odpowiednio jako wartości właściwości `client ID` i `key`.

Po utworzeniu jednostki usługi możesz opcjonalnie utworzyć grupę zasobów (jeśli grupa zasobów, której chcesz użyć, już istnieje, możesz pominąć ten krok). Należy pamiętać, że aby uzyskać listę lokalizacji grupy zasobów, można wywołać polecenie `az account list-locations` i użyć wartości `name` z tej listy, aby określić, gdzie należy utworzyć grupę zasobów.

```bash
# For this tutorial, the author chose to use `westus`
# and `jg-test` for the resource group name.
az group create -l <resource_group_location> -n <resource_group_name>
```

Teraz utworzymy zasób usługi Azure Key Vault. Pamiętaj, że nazwa usługi Key Vault jest używana do późniejszego odwoływania się do magazynu kluczy, więc wybierz coś łatwego do zapamiętania.

```bash
az keyvault create --name <your_keyvault_name>            \
                   --resource-group <your_resource_group> \
                   --location <location>                  \
                   --enabled-for-deployment true          \
                   --enabled-for-disk-encryption true     \
                   --enabled-for-template-deployment true \
                   --sku standard
```

Musimy również udzielić odpowiednich uprawnień do jednostki usługi, która została utworzona wcześniej, aby mogła ona uzyskiwać dostęp do wpisów tajnych usługi Key Vault. Należy pamiętać, że wartość appID jest wartością `appId` z kroku powyżej, w którym utworzono jednostkę usługi (to jest `5292398e-XXXX-40ce-XXXX-d49fXXXX9e79` — ale użyj wartości z danych wyjściowych terminala).

```bash
az keyvault set-policy --name <your_keyvault_name>   \
                       --secret-permission get list  \
                       --spn <your_sp_appId_created_in_step1>
```

Teraz możemy wypchnąć klucz tajny do usługi Key Vault. Użyjmy nazwy klucza `demo-key` i ustawmy wartość klucza `demo-value`:

```bash
az keyvault secret set --name demo-key      \
                       --value demo-value   \
                       --vault-name <your_keyvault_name>  
```

Gotowe. Teraz usługa Key Vault jest uruchomiona na platformie Azure z jednym wpisem tajnym. Możemy teraz sklonować to repozytorium i skonfigurować je, aby ten zasób był używany w naszej aplikacji.

## <a name="getting-up-and-running-locally"></a>Uruchamianie lokalne

Ten przykład jest oparty na przykładowej aplikacji dostępnej w witrynie GitHub, więc sklonujemy ją, a następnie przeanalizujemy kod krok po kroku. Wykonaj poniższe kroki, aby sklonować kod na swoją maszynę:

1. `git clone https://github.com/Azure-Samples/microprofile-configsource-keyvault.git`

1. `cd microprofile-configsource-keyvault`

1. Przejdź do folderu `src/main/resources/META-INF/microprofile-config.properties` i zmień właściwości w pliku microprofile-config.properties, wprowadzając powyższe szczegóły.

1. Spróbuj uruchomić serwer przy użyciu polecenia `mvn clean package payara-micro:start`

1. Przejdź na adres [http://localhost:8080/keyvault-configsource/api/config](http://localhost:8080/keyvault-configsource/api/config) w przeglądarce internetowej. Powinna zostać wyświetlona prosta odpowiedź, która prezentuje wartości odczytywane z usługi Azure Key Vault.

## <a name="summary"></a>Podsumowanie

Ta aplikacja przykładowa łączy interfejs API konfiguracji usługi MicroProfile, usługę Azure Key Vault oraz bezpłatną bibliotekę open source [microprofile-config-keyvault](https://github.com/Azure/azure-microprofile/tree/master/microprofile-config-keyvault), umożliwiając łatwe wstrzykiwanie danych konfiguracyjnych i wpisów tajnych do usług internetowych MicroProfile.
