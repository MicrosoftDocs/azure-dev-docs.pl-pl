---
title: Zarządzanie pamięciami podręcznymi Redis Cache za pomocą programu Azure Explorer for Eclipse
description: Dowiedz się, jak zarządzać pamięciami podręcznymi Azure Redis Cache za pomocą programu Azure Explorer for Eclipse.
documentationcenter: java
ms.date: 02/01/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: e1c7ac18067cd11a0d878da5d3e464ddd2ad6079
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "76999599"
---
# <a name="managing-redis-caches-using-the-azure-explorer-for-eclipse"></a>Zarządzanie pamięciami podręcznymi Redis Cache za pomocą programu Azure Explorer for Eclipse

Program Azure Explorer będący częścią zestawu Azure Toolkit for Eclipse udostępnia deweloperom języka Java łatwe w użyciu rozwiązanie do zarządzania kontami pamięciami podręcznymi Redis Cache na koncie platformy Azure z poziomu zintegrowanego środowiska projektowego (IDE) środowiska Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="create-a-redis-cache-by-using-eclipse"></a>Tworzenie pamięci podręcznej Redis Cache przy użyciu środowiska Eclipse

Poniższe kroki przeprowadzą Cię przez procedurę tworzenia pamięci podręcznej Redis Cache przy użyciu programu Azure Explorer.

1. Zaloguj się do konta platformy Azure, korzystając z instrukcji przedstawionych w artykule [Sign-in instructions for the Azure Toolkit for Eclipse] (Instrukcje logowania dla zestawu narzędzi Azure Toolkit for Eclipse).

1. W oknie narzędzi programu **Azure Explorer** rozwiń węzeł **Azure**, kliknij prawym przyciskiem myszy pozycję **Redis Caches** (Pamięci podręczne Redis Cache), a następnie kliknij pozycję **Create Redis Cache** (Utwórz pamięć podręczną Redis Cache).

   ![Menu tworzenia pamięci podręcznej Redis Cache][CR01]

1. Po wyświetleniu okna dialogowego **New Redis Cache** (Nowa pamięć podręczna Redis Cache) określ następujące opcje:

   ![Okno dialogowe tworzenia nowej pamięci podręcznej Redis Cache][CR02]

   a. **Nazwa DNS**: Określa poddomenę systemu DNS dla nowej pamięci podręcznej Redis Cache, która poprzedza ciąg „.redis.cache.windows.net”; na przykład: *wingtiptoys.redis.cache.windows.net*.

   b. **Subskrypcja**: określa subskrypcję platformy Azure, która ma być używana dla nowej pamięci podręcznej Redis Cache.

   d. **Grupa zasobów**: określa grupę zasobów dla pamięci podręcznej Redis Cache; musisz wybrać jedną z następujących opcji:
      * **Create New** (Utwórz nową): wskazuje, że chcesz utworzyć nową grupę zasobów.
      * **Use Existing** (Użyj istniejącej): wskazuje, że wybierzesz grupę z listy grup zasobów skojarzonych z kontem platformy Azure.

   d. **Lokalizacja**: określa lokalizację, w której jest tworzona pamięć podręczna Redis Cache; na przykład *Zachodnie stany USA*.

   e. **Warstwa cenowa**: określa warstwę cenową używaną przez pamięć podręczną Redis Cache; to ustawienie określa liczbę połączeń klienta. (Aby uzyskać więcej informacji, zobacz temat [Redis Cache — cennik]).

   f. **Port bez protokołu SSL**: określa, czy pamięć podręczna Redis Cache zezwala na połączenia inne niż SSL; domyślnie dozwolone są tylko połączenia SSL.

1. Po określeniu wszystkich ustawień pamięci podręcznej Redis Cache kliknij przycisk **OK**.

Utworzona pamięć podręczna Redis Cache będzie wyświetlana w programie Azure Explorer.

   ![Pamięć podręczna Redis Cache w programie Azure Explorer][CR03]

> [!NOTE]
>
> Więcej informacji na temat konfigurowania pamięci podręcznej Redis Cache znajduje się w temacie [Jak skonfigurować usługę Azure Redis Cache].
>

## <a name="display-the-properties-for-your-redis-cache-in-eclipse"></a>Wyświetlanie właściwości pamięci podręcznej Redis Cache w środowisku Eclipse

1. W programie Azure Explorer kliknij prawym przyciskiem myszy pamięć podręczną Redis Cache, a następnie kliknij pozycję **Show properties** (Pokaż właściwości).

   ![Menu kontekstowe programu Azure Explorer umożliwiające wyświetlanie właściwości pamięci podręcznej Redis Cache][SP01]

1. W programie Azure Explorer są wyświetlane właściwości pamięci podręcznej Redis Cache.

   ![Właściwości pamięci podręcznej Redis][SP02]

## <a name="delete-your-redis-cache-by-using-eclipse"></a>Usuwanie pamięci podręcznej Redis Cache przy użyciu środowiska Eclipse

1. W programie Azure Explorer kliknij prawym przyciskiem myszy pamięć podręczną Redis Cache, a następnie kliknij pozycję **Delete** (Usuń).

   ![Menu kontekstowe programu Azure Explorer umożliwiające usuwanie pamięci podręcznej Redis Cache][DE01]

1. Kliknij przycisk **OK** po wyświetleniu monitu o usunięcie pamięci podręcznej Redis Cache.

   ![Monit o usunięcie pamięci podręcznej Redis Cache][DE02]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o pamięciach podręcznych usługi Azure Redis Cache oraz ustawieniach konfiguracji i cenach, zobacz następujące linki:

* [Azure Redis Cache]
* [Dokumentacja usługi Redis Cache]
* [Redis Cache — cennik]
* [Jak skonfigurować usługę Azure Redis Cache]

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

<!-- URL List -->

[Redis Cache — cennik]: https://azure.microsoft.com/pricing/details/cache/
[Azure Redis Cache]: https://azure.microsoft.com/services/cache/
[Dokumentacja usługi Redis Cache]: /azure/redis-cache/
[Jak skonfigurować usługę Azure Redis Cache]: /azure/redis-cache/cache-configure

<!-- IMG List -->

[CR01]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR01.png
[CR02]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR02.png
[CR03]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/CR03.png

[SP01]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/SP01.png
[SP02]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/SP02.png

[DE01]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/DE01.png
[DE02]: media/azure-toolkit-for-eclipse-managing-redis-caches-using-azure-explorer/DE02.png
