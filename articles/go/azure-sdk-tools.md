---
title: Narzędzia dla deweloperów korzystających z zestawu Azure SDK dla języka Go
description: Narzędzia do pracy z zestawem Azure SDK dla Go i usługami platformy Azure
ms.date: 09/05/2018
ms.topic: conceptual
ms.openlocfilehash: e52845218de7d874e045a11c004360b7a35baa03
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/21/2020
ms.locfileid: "98618852"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Narzędzia dla deweloperów korzystających z zestawu Azure SDK dla języka Go

Oto kilka zalecanych narzędzi, które pomagają w sprawnym tworzeniu kodu w języku Go i jego bezproblemowym współdziałaniu z usługami platformy Azure.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie i konfigurowanie zasobów platformy Azure w ramach posiadanych subskrypcji. Interfejs wiersza polecenia ułatwia szybkie rozpoczęcie tworzenia typowych udostępnionych zasobów platformy Azure, dzięki czemu można się skupić na bardziej złożonych zastosowaniach usług. Interfejs wiersza polecenia obejmuje funkcje zapytań i filtrowania umożliwiające skierowanie danych wyjściowych bezpośrednio do ulubionych narzędzi wiersza polecenia. Interfejs wiersza polecenia można zainstalować w systemie lokalnym jako obraz platformy Docker lub za pośrednictwem usługi [Azure Cloud Shell](/azure/cloud-shell/overview).

> [!div class="nextstepaction"]
> [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code jest uproszczonym edytorem zapewniającym obsługę języka Go. To rozszerzenie udostępnia funkcje, takie jak autouzupełnianie, szablony `impl`, refaktoryzacja czy debugowanie. Visual Studio Code oferuje również obsługę dostępu w edytorze do kontroli źródła oraz rozszerzenia wspomagające pracę z usługami platformy Azure.

* [Instalowanie programu Visual Studio Code](https://code.visualstudio.com/Download)
* [Pobieranie rozszerzenia Visual Studio Code Go](https://code.visualstudio.com/docs/languages/go)
* [Pobieranie rozszerzenia Visual Studio Code Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>Ciągła integracja/ciągłe wdrażanie za pomocą projektu DevOps platformy Azure

Potoki projektu DevOps platformy Azure pozwalają na konfigurację systemu ciągłej integracji w aplikacjach języka Go. Wymagane jest jedynie repozytorium Git, a wdrażanie i testowanie można wykonywać bezpośrednio na platformie Azure.

> [!div class="nextstepaction"]
> [Dowiedz się, jak utworzyć potok ciągłej integracji/ciągłego wdrażania za pomocą usługi projektu DevOps platformy Azure](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>Zarządzanie zależnościami za pomocą narzędzia dep

Zestaw Azure SDK dla języka Go używa narzędzia dep do zarządzania zależnościami. Polecenie dep umożliwia ściąganie wymagań i zapewnianie im zasobów dla aplikacji języka Go, unikając konfliktów związanych z wersjami i zapewniając prawidłowe działanie projektu.

> [!div class="nextstepaction"]
> [Pobierz menedżera zależności dep](https://github.com/golang/dep)