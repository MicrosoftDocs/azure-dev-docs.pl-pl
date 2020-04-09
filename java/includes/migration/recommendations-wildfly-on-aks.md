---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: ab1cd8d8671b12c44a06ae01268364741f7138f6
ms.sourcegitcommit: 2e4167c9e47cea3f2e7dc2607884b2e0d4214556
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813799"
---
### <a name="recommendations"></a>Zalecenia

* Rozważ dodanie nazwy DNS do adresu IP przydzielonego do kontrolera ruchu przychodzącego lub modułu równoważenia obciążenia aplikacji. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Konfigurowanie nazwy DNS](/azure/aks/ingress-static-ip#create-an-ingress-controller) w temacie [Tworzenie kontrolera ruchu przychodzącego ze statycznym publicznym adresem IP w usłudze AKS](/azure/aks/ingress-static-ip).

* Rozważ [dodanie do swojej aplikacji elementu chart programu HELM](https://helm.sh/docs/topics/charts/). Wykres HELM umożliwia parametryzację wdrożenia aplikacji w celu używania i dostosowywania aplikacji przez bardziej różnorodnych klientów.

* Zaprojektuj i zaimplementuj strategię DevOps. Aby zachować niezawodność przy jednoczesnym zwiększaniu szybkości tworzenia rozwiązań, rozważ automatyzację wdrożeń i testowania przy użyciu usługi Azure Pipelines. Aby uzyskać więcej informacji, zobacz [Tworzenie i wdrażanie w usłudze AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Włącz monitorowanie platformy Azure dla klastra. Aby uzyskać więcej informacji, zobacz [Włączanie monitorowania już wdrożonego klastra usługi AKS](/azure/azure-monitor/insights/container-insights-enable-existing-clusters). Dzięki temu monitor platformy Azure może zbierać dzienniki kontenerów, śledzić użycie i tak dalej.

* Rozważ ujawnienie metryk właściwych dla aplikacji za pośrednictwem systemu Prometheus. Prometheus to struktura metryk typu open source, szeroko przyjęta w społeczności Kubernetes. Wycinanie metryk systemu Prometheus można skonfigurować w usłudze Azure Monitor zamiast hostować własny serwer Prometheus w celu włączenia agregacji metryk z aplikacji i umożliwienia automatycznego reagowania na nietypowe warunki lub ich eskalowania. Aby uzyskać więcej informacji, zobacz [Konfigurowanie wycinania metryk systemu Prometheus z usługą Azure Monitor dla kontenerów](/azure/azure-monitor/insights/container-insights-prometheus-integration).

* Zaprojektuj i zaimplementuj strategię ciągłości działania i odzyskiwania po awarii. W przypadku aplikacji o krytycznym znaczeniu rozważ zastosowanie architektury wdrażania w wielu regionach. Aby uzyskać więcej informacji, zobacz [Najlepsze rozwiązania dotyczące zapewnienia ciągłości działania firmy i odzyskiwania po awarii w usłudze AKS](/azure/aks/operator-best-practices-multi-region).

* Zapoznaj się z [zasadami obsługi wersji platformy Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). To Ty ponosisz odpowiedzialność za aktualizowanie klastra usługi AKS, aby mieć pewność, że zawsze korzysta z obsługiwanej wersji. Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi AKS](/azure/aks/upgrade-cluster).

* Wszyscy członkowie zespołu odpowiedzialni za administrowanie klastrami i programowanie aplikacji powinni zapoznać się z odpowiednimi najlepszymi rozwiązaniami dotyczącymi usługi AKS. Aby uzyskać więcej informacji, zobacz [Najlepsze rozwiązania dla deweloperów i operatorów klastra w zakresie tworzenia aplikacji i zarządzania nimi w usłudze AKS](/azure/aks/best-practices).

* Upewnij się, że plik wdrożenia określa sposób przeprowadzania aktualizacji stopniowych. Aby uzyskać więcej informacji, zobacz [Wdrażanie aktualizacji stopniowej](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment) w dokumentacji platformy Kubernetes.

* Skonfiguruj automatyczne skalowanie, aby radzić sobie z obciążeniem w czasie szczytu. Aby uzyskać więcej informacji, zobacz [Automatyczne skalowanie klastra odpowiednio do wymagań aplikacji w usłudze AKS](/azure/aks/cluster-autoscaler).

* Rozważ monitorowanie rozmiaru pamięci podręcznej kodu oraz dodanie parametrów maszyny JVM `-XX:InitialCodeCacheSize` i `-XX:ReservedCodeCacheSize` w pliku Dockerfile w celu zoptymalizowania wydajności. Aby uzyskać więcej informacji, zobacz [Dostrajanie pamięci podręcznej kodu](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) w dokumentacji programu Oracle.
