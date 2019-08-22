---
title: Instalowanie zestawu Azure SDK dla języka Go
description: Jak zainstalować i skonfigurować zestaw Azure SDK dla języka Go oraz zapewnić w nim zasoby.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/14/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: bd86226cd6a6a297efe6f22ab77f4518f30e0bb3
ms.sourcegitcommit: 4eee7d9a484e35eb695248c011a63b27603d354b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642674"
---
# <a name="install-the-azure-sdk-for-go"></a>Instalowanie zestawu Azure SDK dla języka Go

Zestaw Azure SDK dla języka Go — Zapraszamy! Zestaw SDK umożliwia zarządzanie usługami platformy Azure z poziomu aplikacji w języku Go, a także interakcje z tymi usługami.

## <a name="get-the-azure-sdk-for-go"></a>Pobieranie zestawu Azure SDK dla języka Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

Niektóre usługi platformy Azure mają własne zestawy SDK dla języka Go, których nie ma w podstawowym pakiecie zestawu Azure SDK dla języka Go. W poniższej tabeli wymieniono usługi z własnymi zestawami SDK i ich nazwy pakietów. Wszystkie te pakiety są uważane za będące w wersji zapoznawczej.

| Usługa | Pakiet |
|---------|---------|
| Blob Storage | [github.com/Azure/azure-storage-blob-go](https://github.com/Azure/azure-storage-blob-go) |
| File Storage | [github.com/Azure/azure-storage-file-go](https://github.com/Azure/azure-storage-file-go) |
| Kolejka magazynu | [github.com/Azure/azure-storage-queue-go](https://github.com/Azure/azure-storage-queue-go) |
| Centrum zdarzeń | [github.com/Azure/azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) |
| Service Bus | [github.com/Azure/azure-service-bus-go](https://github.com/Azure/azure-service-bus-go) |

## <a name="vendor-the-azure-sdk-for-go"></a>Zapewnianie zasobów zestawu Azure SDK dla języka Go

Zestaw Azure SDK dla języka Go umożliwia zapewnianie zasobów za pośrednictwem narzędzia [dep](https://github.com/golang/dep). Ze względu na stabilność zapewnianie zasobów jest zalecane. Aby użyć narzędzia `dep` we własnym projekcie, dodaj element `github.com/Azure/azure-sdk-for-go` do sekcji `[[constraint]]` Twojego pliku `Gopkg.toml`. Aby na przykład zapewnić zasoby z wersji `14.0.0`, dodaj następujący wpis:

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a>Uwzględnianie w projekcie zestawu Azure SDK dla języka Go

Aby zapewnić możliwość korzystania z usług platformy Azure z poziomu kodu Go, zaimportuj wszystkie usługi, z którymi wchodzisz w interakcje, a także wymagane moduły `autorest`.
Pełną listę dostępnych modułów można uzyskać w dokumentacji GoDoc dotyczącej [dostępnych usług](https://godoc.org/github.com/Azure/azure-sdk-for-go) i [pakietów AutoRest](https://godoc.org/github.com/Azure/go-autorest). Najpopularniejsze pakiety z elementu `go-autorest`, których potrzebujesz, to:

| Pakiet | Opis |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | Obiekty zapewniające obsługę uwierzytelniania klientów w usłudze |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | Stałe zapewniające interakcje z usługami platformy Azure |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | Mechanizmy uwierzytelniania dostępu do usług platformy Azure |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | Pomocnicy asercji typów ułatwiający pracę ze strukturami danych zestawu Azure SDK |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Wersje pakietów języka Go i usług platformy Azure są określane niezależnie. Wersje usługi są częścią ścieżki importu modułu — za modułem `services`. Pełna ścieżka modułu obejmuje kolejno nazwę usługi, wersję w formacie `YYYY-MM-DD` i ponownie nazwę usługi. Przykład importu wersji `2017-03-30` usługi Compute:

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

Zaleca się użycie najnowszej wersji usługi w momencie rozpoczęcia programowania i stosowanie jej spójnie.
Wymagania dotyczące usług mogą zmieniać się w zależności od wersji, co może uszkodzić kod, nawet jeśli nie zostaną zainstalowane żadne aktualizacje zestawu SDK dla języka Go w tym czasie.

Jeśli jest potrzebna zbiorcza migawka usług, możesz również wybrać jedną wersję profilu. Obecnie jedynym zablokowanym profilem jest wersja `2017-03-09`, która może nie obejmować najnowszych funkcji usługi. Profile znajdują się w module `profiles` i mają wersję w formacie `YYYY-MM-DD`. Usługi są podzielone na grupy według wersji profilu. Aby na przykład zaimportować moduł zarządzania zasobami platformy Azure z profilu `2017-03-09`:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> Dostępne są także profile `preview` i `latest`. Ich użycie nie jest zalecane. Te profile są wersjami ruchomymi, co znaczy, że zachowanie usługi może w dowolnym momencie ulec zmianie.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z zestawu Azure SDK dla języka Go, wypróbuj szybki start.

* [Wdrażanie maszyny wirtualnej na podstawie szablonu](azure-sdk-go-qs-vm.md)
* [Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go (Przenoszenie obiektów do usługi Azure Blob Storage przy użyciu zestawu Azure Blob SDK dla języka Go)](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [Łączenie się z usługą Azure Database for PostgreSQL](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

Jeśli chcesz od razu rozpocząć pracę z innymi usługami w zestawie SDK dla języka Go, przyjrzyj się udostępnionym przykładom kodu.

* [Uwierzytelnianie za pomocą usług platformy Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [Wdrażanie nowych maszyn wirtualnych przy użyciu uwierzytelniania SSH](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Wdrażanie obrazu kontenera w usłudze Azure Container Instances](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Tworzenie klastra w usłudze Azure Kubernetes Service](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [Praca z usługami Azure Storage](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Wszystkie przykłady dotyczące zestawu Azure SDK dla języka Go](https://github.com/azure-samples/azure-sdk-for-go-samples)
