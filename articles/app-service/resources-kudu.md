---
title: Översikt över kudu-tjänsten
description: Lär dig mer om motorn som driver kontinuerlig distribution i App Service och dess funktioner.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610028"
---
# <a name="kudu-service-overview"></a>Översikt över kudu-tjänsten

Kudu är motorn bakom ett antal funktioner i [Azure App Service](overview.md) som rör käll kontroll baserad distribution och andra distributions metoder som Dropbox och OneDrive sync. 

## <a name="access-kudu-for-your-app"></a>Få åtkomst till kudu för din app
När som helst skapar du en app, App Service skapar en Companion-app för den som skyddas av HTTPS. Den här kudu-appen är tillgänglig på:

- Appen är inte i isolerad nivå: `https://<app-name>.scm.azurewebsites.net`
- App på isolerad nivå (App Service-miljön): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Mer information finns i [åtkomst till kudu-tjänsten](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Kudu-funktioner

Kudu ger dig nyttig information om din App Service-app, till exempel:

- Appinställningar
- Anslutningssträngar
- Miljövariabler
- Servervariabler
- HTTP-rubriker

Den innehåller också andra funktioner, till exempel:

- Kör kommandon i [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Hämta IIS-diagnostiska dumpar eller Docker-loggar.
- Hantera IIS-processer och plats tillägg.
- Lägg till distributions-webhookar för Windows APS.
- Tillåt ZIP-distribution av användar gränssnitt med `/ZipDeploy` .
- Genererar [anpassade distributions skript](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Tillåter åtkomst med [REST API](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Fler resurser

Kudu är ett [projekt med öppen källkod](https://github.com/projectkudu/kudu)och har sin dokumentation på [kudu wiki](https://github.com/projectkudu/kudu/wiki).

