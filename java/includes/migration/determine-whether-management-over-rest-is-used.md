---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a67bdb749231c39e1b64d7b2a982ad4c0946cc25
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "77000845"
---
### <a name="determine-whether-management-over-rest-is-used"></a>Określenie, czy używa się zarządzania za pośrednictwem interfejsu REST

Jeśli cykl życia aplikacji obejmuje zarządzanie przy użyciu interfejsu REST, należy przechwycić informacje o tym, które porty są używane w celu uzyskania dostępu do interfejsu API REST, i określić sposób ich uwierzytelniania oraz ujawniania. Po migracji należy się upewnić, że te same porty i mechanizmy uwierzytelniania są uwidocznione, aby cykl życia aplikacji mógł działać w podobny sposób, jak przed migracją. Aby uzyskać więcej informacji, zobacz [Administering Oracle WebLogic Server with RESTful Management Services (Zarządzanie serwerem Oracle WebLogic Server przy użyciu usług do zarządzania opartych na interfejsie REST)](https://docs.oracle.com/middleware/12213/wls/WLRUR/title.htm).
