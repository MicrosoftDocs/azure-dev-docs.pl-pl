---
title: Biblioteki zarządzania Azure dla języka Java — przykłady aplikacji internetowych
description: Pobierz przykładowy kod służący do tworzenia i aktualizowania aplikacji internetowych platformy Azure hostowanych w usłudze App Service przy użyciu bibliotek zarządzania Azure dla języka Java
keywords: Azure, Java, SDK, API, Maven, Gradle, web apps, app service
author: rloutlaw
ms.date: 04/16/2017
ms.topic: article
ms.service: multiple
ms.assetid: 43633e5c-9fb1-4807-ba63-e24c126754e2
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 30884958dd4c829cf7b268715de7c79f3a786724
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "77002484"
---
# <a name="azure-management-libraries-for-java---web-app-samples"></a>Biblioteki zarządzania Azure dla języka Java — przykłady aplikacji internetowych 

Poniższa tabela zawiera linki do źródeł Java, za pomocą których można tworzyć i konfigurować aplikacje internetowe.

| **Tworzenie aplikacji** ||
|---|---|
| [Tworzenie aplikacji internetowej i wdrażanie jej z serwera FTP lub usługi GitHub][1] | Wdróż aplikacje internetowe z lokalnej usługi Git i serwera FTP oraz przy użyciu ciągłej integracji usługi GitHub. |
| [Tworzenie aplikacji internetowej i zarządzanie miejscami wdrożenia][2] | Utwórz aplikację internetową i wdróż ją w miejscach przejściowych, a następnie zamień wdrożenia. |
| **Konfigurowanie aplikacji** ||
| [Tworzenie aplikacji internetowej i konfigurowanie domeny niestandardowej][3] | Utwórz aplikację internetową z domeną niestandardową i certyfikatem SSL z podpisem własnym. |
| **Skalowanie aplikacji** ||
| [Skalowanie aplikacji internetowej o wysokiej dostępności w wielu regionach][4] | Przeskaluj aplikację internetową w trzech różnych regionach geograficznych i udostępnij ją za pośrednictwem pojedynczego punktu końcowego przy użyciu usługi Azure Traffic Manager. | 
| **Łączenie aplikacji z zasobami** ||
| [Łączenie aplikacji internetowej z kontem magazynu][5] | Utwórz konto magazynu platformy Azure i dodaj parametry połączenia konta magazynu do ustawień aplikacji. |
| [Łączenie aplikacji internetowej z bazą danych SQL][6] | Utwórz aplikację internetową i bazę danych SQL, a następnie dodaj parametry połączenia bazy danych SQL do ustawień aplikacji. |

[1]: java-sdk-configure-webapp-sources.md
[2]: https://github.com/Azure-Samples/app-service-java-manage-staging-and-production-slots-for-web-apps/
[3]: https://github.com/Azure-Samples/app-service-java-manage-web-apps-with-custom-domains/
[4]: https://azure.microsoft.com/resources/samples/app-service-java-scale-web-apps-on-linux/
[5]: https://github.com/Azure-Samples/app-service-java-manage-storage-connections-for-web-apps/
[6]: https://github.com/Azure-Samples/app-service-java-manage-data-connections-for-web-apps/