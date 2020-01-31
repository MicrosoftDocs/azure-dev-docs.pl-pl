---
ms.date: 09/05/2018
ms.technology: azure-cli
ms.openlocfilehash: af03f2efbb74e55dfcd14b6a2ac894a74eba321f
ms.sourcegitcommit: 4cf22356d6d4817421b551bd53fcba76bdb44cc1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76871891"
---
Zestaw [Azure SDK dla języka Go](https://github.com/Azure/azure-sdk-for-go) jest zgodny z językiem Go w wersji 1.8 lub nowszej. W przypadku środowisk korzystających z [profilów Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-go) wymagana jest wersja 1.9 lub nowsza.
Jeśli chcesz zainstalować język Go, wykonaj [instrukcje instalacji języka Go](https://golang.org/doc/install).

Zestaw Azure SDK dla języka Go i jego zależności można pobrać za pomocą polecenia `go get`.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Ciąg `Azure` w adresie URL musi zaczynać się wielką literą. W przeciwnym razie podczas pracy z zestawem SDK mogą wystąpić problemy związane z wielkością liter. Ciąg `Azure` musi zaczynać się wielką literą także w Twoich instrukcjach importu.
