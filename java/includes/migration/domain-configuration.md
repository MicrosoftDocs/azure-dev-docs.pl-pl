---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 33146c309d8fdaf21dc218c11054460cfc3dc8f4
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001069"
---
### <a name="domain-configuration"></a>Konfiguracja domeny

Główną jednostką konfiguracji na serwerze WebLogic jest domena. W związku z tym plik *config.xml* zawiera wiele konfiguracji, nad którymi trzeba dokładnie się zastanowić podczas migracji. Plik zawiera odwołania do dodatkowych plików XML przechowywanych w podkatalogach. Firma Oracle radzi, aby do konfigurowania obiektów i usług zarządzanych przez serwer WebLogic używać zazwyczaj **konsoli administracyjnej** oraz zezwalać serwerowi WebLogic na obsługę pliku *config.xml*. Aby uzyskać więcej informacji, zobacz [Pliki konfiguracji domeny](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/domcf/config_files.html).

#### <a name="inside-your-application"></a>W aplikacji

Sprawdź *WEB-INF/weblogic.xml* i/lub plik *WEB-INF/web.xml*.
