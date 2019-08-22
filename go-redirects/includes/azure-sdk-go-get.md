---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: a51c8667c74a2611ae8769aa42fd1a94f9253bc8
ms.sourcegitcommit: 4eee7d9a484e35eb695248c011a63b27603d354b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642946"
---
Zestaw [Azure SDK dla języka Go](https://github.com/Azure/azure-sdk-for-go) jest zgodny z językiem Go w wersji 1.8 lub nowszej. W przypadku środowisk korzystających z [profilów Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-go) wymagana jest wersja 1.9 lub nowsza.
Jeśli chcesz zainstalować język Go, wykonaj [instrukcje instalacji języka Go](https://golang.org/doc/install).

Zestaw Azure SDK dla języka Go i jego zależności można pobrać za pomocą polecenia `go get`.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Ciąg `Azure` w adresie URL musi zaczynać się wielką literą. W przeciwnym razie podczas pracy z zestawem SDK mogą wystąpić problemy związane z wielkością liter. Ciąg `Azure` musi zaczynać się wielką literą także w Twoich instrukcjach importu.
