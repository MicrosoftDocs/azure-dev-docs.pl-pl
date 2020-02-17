---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a771d0689e83e0b61bf9b8c31e0cbf36949fdc20
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000873"
---
### <a name="inventory-all-secrets"></a>Utworzenie spisu wszystkich wpisów tajnych

Przed pojawieniem się technologii typu „konfiguracja jako usługa”, takich jak usługa Azure Key Vault, nie było dobrze zdefiniowanego pojęcia „wpisu tajnego”. Zamiast tego korzystano z odrębnych zestawów ustawień konfiguracji, które efektywnie funkcjonowały jak dzisiejsze „wpisy tajne”. W przypadku serwerów aplikacji, takich jak serwer WebLogic, te wpisy tajne znajdują się w wielu różnych plikach konfiguracji i w magazynach konfiguracji. Sprawdź wszystkie pliki właściwości i konfiguracji na serwerach produkcyjnych kątem jakichkolwiek wpisów tajnych i haseł. Sprawdź plik *weblogic.xml* w swoich plikach WAR. Możesz również znaleźć pliki konfiguracji zawierające hasła lub poświadczenia wewnątrz aplikacji. Aby uzyskać więcej informacji, zobacz [Podstawowe pojęcia dotyczące usługi Azure Key Vault](/azure/key-vault/basic-concepts).
