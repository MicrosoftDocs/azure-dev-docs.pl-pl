---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 5527a38151afd531cbd256428bd729b7aba8fe45
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897593"
---
### <a name="provision-a-public-ip-address"></a>Aprowizacja publicznego adresu IP

Jeśli aplikacja ma być dostępna spoza sieci wewnętrznej lub wirtualnej, będziesz potrzebować publicznego statycznego adresu IP. Ten adres IP należy zaaprowizować w grupie zasobów węzła klastra, jak pokazano w następującym przykładzie:

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
