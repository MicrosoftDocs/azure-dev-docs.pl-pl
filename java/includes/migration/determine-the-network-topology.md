---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 9403614c7ae2990a35fcbcce6d2e104f87724da5
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77001020"
---
### <a name="determine-the-network-topology"></a>Określanie topologii sieci

Migrację należy rozpoczynać od zapoznania się z bieżącym zestawem ofert w witrynie Marketplace. Jeśli oferta nie obejmuje aspektów migrowanej architektury, musisz określić topologię sieci w bieżącym wdrożeniu i odtworzyć ją na platformie Azure — nawet po rozszerzeniu podstawowej oferty przy użyciu jednego z szablonów rozwiązań.

Ten temat jest bardzo szeroki, ale poniższa dokumentacja może pomóc Ci ukierunkować działania związane z migracją:

* Ta dokumentacja zawiera ogólne informacje związane z migracją topologii sieci na platformę Azure: [Przewodnik po wdrożeniu Fast Track](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/deploying.html#GUID-E0BE4A3E-44CD-4C95-9540-7A850BF02F6A).
* W tej dokumentacji opisano ważne zagadnienia dotyczące klastrowania, które ma wpływ na topologię sieci: [Klastrowanie serwerów WebLogic](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/clustering.html#GUID-E39A18C2-B990-485F-BFB1-0549250FABFE).
* Ze względu na to, że w systemie WebLogic źródła danych są oddzielnymi serwerami, należy je uwzględnić w ramach analizy topologii sieci. [Źródła danych na serwerach WebLogic](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jdbc.html#GUID-9FD5F552-B2E4-4FEC-8C10-503A08764B52).
* Źródła komunikatów są również oddzielnymi serwerami. [Komunikaty serwerów WebLogic](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/intro/jms.html#GUID-3B5F647D-E001-413B-AC6A-1E103BDBA93F)
* Podstawowym wymogiem jest równoważenie obciążenia. Ta dokumentacja dotyczy równoważenia obciążenia po stronie serwera WebLogic: [Równoważenie obciążenia w klastrze](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/load_balancing.html#GUID-B8F6DE4B-1AAC-428B-878B-BFDCE161C054).
