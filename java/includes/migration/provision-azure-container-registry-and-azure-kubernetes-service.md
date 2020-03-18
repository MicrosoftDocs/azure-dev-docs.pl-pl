---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e72cefaab3ccdbbaae01992cc11285944fb09a36
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897423"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Aprowizacja usług Azure Container Registry i Azure Kubernetes Service

Użyj następujących poleceń, aby utworzyć rejestr kontenerów i klaster usługi Azure Kubernetes, którego jednostka usługi ma rolę czytelnika w rejestrze. Upewnij się, że [wybrano odpowiedni model sieci](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) dopasowany do wymagań w zakresie łączności sieciowej klastra.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
