---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 29ea7925e91b1f42e0f83b88fbc7f7d9a8fd3629
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897644"
---
### <a name="deploy-to-aks"></a>Wdrażanie w usłudze AKS

Utwórz i zastosuj pliki YAML platformy Kubernetes. Aby uzyskać więcej informacji, zobacz temat [Szybki start: Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure](/azure/aks/kubernetes-walkthrough#run-the-application). Jeśli tworzysz zewnętrzny moduł równoważenia obciążenia (dla aplikacji lub kontrolera ruchu przychodzącego), upewnij się, że adres IP aprowizowany w poprzedniej sekcji zostanie podany jako `LoadBalancerIP`.

Uwzględnij uzewnętrzniane parametry jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz [Definiowanie zmiennych środowiskowych dla kontenera](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Nie dołączaj wpisów tajnych (takich jak hasła, klucze interfejsu API i parametry połączenia JDBC). Są one omówione w następującej sekcji.

Należy pamiętać o uwzględnieniu ustawień pamięci i procesora podczas tworzenia pliku YAML wdrożenia, aby kontenery miały właściwe rozmiary.
