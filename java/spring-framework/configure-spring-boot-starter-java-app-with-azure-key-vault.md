---
title: Używanie szablonu startowego Spring Boot dla rozwiązania Azure Key Vault
description: Dowiedz się, jak skonfigurować aplikację Spring Boot Initializer przy użyciu szablonu startowego Azure Key Vault.
services: key-vault
documentationcenter: java
ms.date: 10/29/2019
ms.service: key-vault
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 13ca873a838d3097484343a28d1f12300550ded8
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999809"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-key-vault"></a>Używanie szablonu startowego Spring Boot dla rozwiązania Azure Key Vault

W tym artykule pokazano tworzenie aplikacji za pomocą narzędzia **[Spring Initializr]** , która korzysta z szablonu startowego Spring Boot dla rozwiązania Azure Key Vault, aby pobrać parametry połączenia przechowywane jako wpis tajny w magazynie kluczy.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, wymagane są:

* Subskrypcja platformy Azure — jeśli nie masz jeszcze subskrypcji platformy Azure, możesz uaktywnić [korzyści dla subskrybentów MSDN] lub utworzyć [bezpłatne konto platformy Azure].
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie [Apache Maven](http://maven.apache.org/) w wersji 3.0 lub nowszej.

## <a name="create-an-app-using-spring-initializr"></a>Tworzenie aplikacji przy użyciu narzędzia Spring Initializr

Poniższa procedura umożliwia utworzenie aplikacji przy użyciu narzędzia Spring Initializr.

1. Przejdź do <https://start.spring.io/>.

1. Określ, że chcesz wygenerować projekt **Maven** w języku **Java**.  

1. Określ nazwy **Group** (Grupa) i **Artifact** (Artefakt) dla swojej aplikacji.

1. W sekcji **Dependencies** (Zależności) wprowadź **Azure Key Vault**.

1. Przewiń w dół do końca strony, a następnie kliknij przycisk **Generate** (Generuj).

   ![Generowanie projektu Spring Boot][secrets-01]

1. Po wyświetleniu monitu pobierz projekt do ścieżki na komputerze lokalnym.

## <a name="sign-into-azure"></a>Logowanie do platformy Azure

Poniższa procedura umożliwia uwierzytelnienie użytkownika w interfejsie wiersza polecenia platformy Azure.

1. Otwórz wiersz polecenia.

1. Zaloguj się do konta platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure:

   ```azurecli
   az login
   ```

Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania.

1. Wyświetl listę swoich subskrypcji:

   ```azurecli
   az account list
   ```

   Platforma Azure zwróci listę subskrypcji i trzeba będzie skopiować identyfikator GUID subskrypcji, która ma zostać użyta. Na przykład:

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "ssssssss-ssss-ssss-ssss-ssssssssssss",
       "isDefault": true,
       "name": "Converted Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "tttttttt-tttt-tttt-tttt-tttttttttttt",
       "user": {
         "name": "contoso@microsoft.com",
         "type": "user"
       }
     }
   ]
   ```

1. Określ identyfikator GUID konta, które ma być używane z platformą Azure. Na przykład:

   ```azurecli
   az account set -s ssssssss-ssss-ssss-ssss-ssssssssssss
   ```

## <a name="create-a-new-azure-key-vault"></a>Tworzenie nowego magazynu kluczy rozwiązania Azure Key Vault

Poniższa procedura tworzy i inicjuje magazyn kluczy.

1. Utwórz grupę zasobów na zasoby platformy Azure, które będą używane z tym magazynem kluczy. Na przykład:

   ```azurecli
   az group create --name vged-rg2 --location westus
   ```

   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa unikatową nazwę grupy zasobów. |
   | `location` | Określa [region świadczenia usługi Azure](https://azure.microsoft.com/regions/), w którym będzie hostowana grupa zasobów. |

   W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone wyniki tworzenia grupy zasobów. Na przykład:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/resourceGroups/vged-rg2",
     "location": "westus",
     "managedBy": null,
     "name": "vged-rg2",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```

2. Utwórz jednostkę usługi platformy Azure na podstawie rejestracji aplikacji. Na przykład:
   ```shell
   az ad sp create-for-rbac --name "vgeduser"
   ```
   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa nazwę jednostki usługi platformy Azure. |

   Interfejs wiersza polecenia platformy Azure zwróci komunikat o stanie JSON, który zawiera elementy *appId* i *password* do późniejszego użycia jako identyfikator klienta i hasło klienta. Na przykład:

   ```json
   {
     "appId": "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii",
     "displayName": "vgeduser",
     "name": "http://vgeduser",
     "password": "pppppppp-pppp-pppp-pppp-pppppppppppp",
     "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

3. Utwórz nowy magazyn kluczy w grupie zasobów. Na przykład:

   ```azurecli
   az keyvault create --name vgedkeyvault --resource-group vged-rg2 --location westus --enabled-for-deployment true --enabled-for-disk-encryption true --enabled-for-template-deployment true --sku standard --query properties.vaultUri
   ```

   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa unikatową nazwę magazynu kluczy. |
   | `location` | Określa [region świadczenia usługi Azure](https://azure.microsoft.com/regions/), w którym będzie hostowana grupa zasobów. |
   | `enabled-for-deployment` | Określa [opcję wdrożenia magazynu kluczy](/cli/azure/keyvault). |
   | `enabled-for-disk-encryption` | Określa [opcję szyfrowania magazynu kluczy](/cli/azure/keyvault). |
   | `enabled-for-template-deployment` | Określa [opcję szyfrowania magazynu kluczy](/cli/azure/keyvault). |
   | `sku` | Określa [opcję jednostki SKU magazynu kluczy](/cli/azure/keyvault). |
   | `query` | Określa wartość, która ma zostać pobrana z odpowiedzi, czyli identyfikator URI magazynu kluczy potrzebny do ukończenia tego samouczka. |

   Interfejs wiersza polecenia platformy Azure wyświetli identyfikator URI dla magazynu kluczy, który będzie później używany. Na przykład:  

   ```azurecli
   "https://vgedkeyvault.vault.azure.net"

   ```

4. Ustaw zasady dostępu dla jednostki usługi platformy Azure, która została utworzona wcześniej. Na przykład:

   ```azurecli
   az keyvault set-policy --name vgedkeyvault --secret-permission set get list delete --spn "iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii"
   ```

   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `name` | Określa nazwę magazynu kluczy, taką jak wcześniejsza. |
   | `secret-permission` | Określa [zasady zabezpieczeń](/cli/azure/keyvault) dotyczące magazynu kluczy. |
   | `spn` | Określa identyfikator GUID do rejestracji aplikacji, taki jak wcześniejszy. |

   W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone wyniki tworzenia zasad zabezpieczeń. Na przykład:  

   ```json
   {
     "id": "/subscriptions/ssssssss-ssss-ssss-ssss-ssssssssssss/...",
     "location": "westus",
     "name": "vgedkeyvault",
     "properties": {
       ...
       ... (A long list of values will be displayed here.)
       ...
     },
     "resourceGroup": "vged-rg2",
     "tags": {},
     "type": "Microsoft.KeyVault/vaults"
   }
   ```

5. Zapisz wpis tajny w nowym magazynie kluczy. Na przykład:

   ```azurecli
   az keyvault secret set --vault-name "vgedkeyvault" --name "connectionString" --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
   ```

   Gdzie:

   | Parametr | Opis |
   |---|---|
   | `vault-name` | Określa nazwę magazynu kluczy, taką jak wcześniejsza. |
   | `name` | Określa nazwę wpisu tajnego. |
   | `value` | Określa wartość wpisu tajnego. |

   W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone wyniki tworzenia wpisu tajnego. Na przykład:  

   ```json
   {
     "attributes": {
       "created": "2017-12-01T09:00:16+00:00",
       "enabled": true,
       "expires": null,
       "notBefore": null,
       "recoveryLevel": "Purgeable",
       "updated": "2017-12-01T09:00:16+00:00"
     },
     "contentType": null,
     "id": "https://vgedkeyvault.vault.azure.net/secrets/connectionString/123456789abcdef123456789abcdef",
     "kid": null,
     "managed": null,
     "tags": {
       "file-encoding": "utf-8"
     },
     "value": "jdbc:sqlserver://.database.windows.net:1433;database=DATABASE;"
   }
   ```

## <a name="configure-and-compile-your-app"></a>Konfigurowanie i kompilowanie aplikacji

Aby skonfigurować i skompilować aplikację, należy wykonać poniższą procedurę.

1. Wyodrębnij pliki z plików archiwum projektu Spring Boot, które zostały pobrane wcześniej do katalogu.

2. Przejdź do folderu *src/main/resources* w projekcie i otwórz plik *application.properties* w edytorze tekstów.

3. Dodaj wartości dotyczące magazynu kluczy przy użyciu wartości z kroków wykonanych wcześniej w tym samouczku. Na przykład:

   ```yaml
   azure.keyvault.uri=https://vgedkeyvault.vault.azure.net/
   azure.keyvault.client-id=iiiiiiii-iiii-iiii-iiii-iiiiiiiiiiii
   azure.keyvault.client-key=pppppppp-pppp-pppp-pppp-pppppppppppp
   ```

   Gdzie:

   |          Parametr          |                                 Opis                                 |
   |-----------------------------|-----------------------------------------------------------------------------|
   |    `azure.keyvault.uri`     |           Określa identyfikator URI ustalony podczas tworzenia magazynu kluczy.           |
   | `azure.keyvault.client-id`  |  Określa identyfikator GUID *appId* ustalony podczas tworzenia jednostki usługi.   |
   | `azure.keyvault.client-key` | Określa identyfikator GUID *password* ustalony podczas tworzenia jednostki usługi. |


4. Przejdź do głównego pliku kodu źródłowego projektu. Na przykład: */src/main/java/com/vged/secrets*.

5. Otwórz główny plik Java aplikacji w edytorze tekstów, na przykład *SecretsApplication.java*, i dodaj następujące wiersze kodu do pliku:

   ```java
   package com.vged.secrets;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.boot.CommandLineRunner;

   @SpringBootApplication
   public class SecretsApplication implements CommandLineRunner {

      @Value("${connectionString}")
      private String connectionString;

      public static void main(String[] args) {
         SpringApplication.run(SecretsApplication.class, args);
      }

      public void run(String... varl) throws Exception {
         System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
      }
   }
   ```
   Ten przykład kodu pobiera parametry połączenia z magazynu kluczy i wyświetla je w wierszu polecenia.

6. Zapisz i zamknij plik Java

## <a name="build-and-test-your-app"></a>Tworzenie i testowanie aplikacji

Aby przetestować aplikację, należy wykonać poniższą procedurę.

1. Przejdź do katalogu, w którym znajduje się plik *pom.xml* aplikacji Spring Boot:

1. Skompiluj aplikację Spring Boot przy użyciu narzędzia Maven. Na przykład:

   ```bash
   mvn clean package
   ```

   Maven wyświetli wyniki kompilacji.

   ![Stan kompilowania aplikacji Spring Boot][build-application-01]

1. Uruchom aplikację Spring Boot przy użyciu narzędzia Maven. Aplikacja wyświetli parametry połączenia z magazynu kluczy. Przykład:

   ```bash
   mvn spring-boot:run
   ```

   ![Komunikat o czasie wykonywania aplikacji Spring Boot][build-application-02]

## <a name="summary"></a>Podsumowanie

W tym samouczku utworzono nową aplikację internetową w języku Java przy użyciu narzędzia **[Spring Initializr]** , utworzono magazyn kluczy Azure Key Vault do przechowywania informacji poufnych, a następnie skonfigurowano aplikację do pobierania informacji z magazynu kluczy.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Aby uzyskać więcej informacji o korzystaniu z magazynów kluczy rozwiązania Azure Key Vault, zobacz następujące artykuły:

* [Dokumentacja usługi Key Vault].

* [Co to jest usługa Azure Key Vault?]

Aby uzyskać więcej informacji o korzystaniu z aplikacji Spring Boot na platformie Azure, zobacz następujące artykuły:

* [Deploy a Spring Boot Application to the Azure App Service](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)

* [Running a Spring Boot Application on a Kubernetes Cluster in the Azure Container Service](deploy-spring-boot-java-app-on-kubernetes.md) (Uruchamianie aplikacji Spring Boot w klastrze Kubernetes w usłudze Azure Container Service)

Aby uzyskać więcej informacji na temat używania Azure z językiem Java, zobacz witryny [Azure dla deweloperów języka Java] oraz [Working with Azure DevOps and Java] (Praca z narzędziami Azure DevOps i językiem Java).

<!-- URL List -->

[Dokumentacja usługi Key Vault]: /azure/key-vault/
[Co to jest usługa Azure Key Vault?]: /azure/key-vault/key-vault-get-started
[Azure dla deweloperów języka Java]: /azure/java/
[Bezpłatne konto platformy Azure]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Praca z narzędziami Azure DevOps i językiem Java)
[Korzyści dla subskrybentów MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[secrets-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-01.png
[secrets-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-02.png
[secrets-03]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/secrets-03.png

[build-application-01]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-01.png
[build-application-02]: media/configure-spring-boot-starter-java-app-with-azure-key-vault/build-application-02.png
