---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000971"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Ustalanie, czy oprogramowanie WebLogic zostało dostosowane

Ustal, które z następujących dostosowań zostały wdrożone, i określ, jakie działania zostały wykonane.

* Czy zostały zmienione skrypty uruchamiania? Takie skrypty to *setDomainEnv*, *commEnv*, *startWebLogic* i *stopWebLogic*.
* Czy do środowiska JVM są przekazywane określone parametry?
* Czy do ścieżki klas serwera zostały dodane pliki JAR?
