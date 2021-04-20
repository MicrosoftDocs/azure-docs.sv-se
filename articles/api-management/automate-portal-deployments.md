---
title: Automatisera distributioner av utvecklarportalen
titleSuffix: Azure API Management
description: Lär dig hur du automatiskt migrerar innehåll från utvecklarportalen med egen värd mellan två API Management tjänster.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741922"
---
# <a name="automate-developer-portal-deployments"></a>Automatisera distributioner av utvecklarportalen

Den API Management utvecklarportalen stöder programmatisk åtkomst till innehåll. Du kan importera data till eller exportera från en API Management-tjänst via [innehållshanterings-REST API](/rest/api/apimanagement/). Den REST API åtkomsten fungerar för både hanterade och lokala portaler.

## <a name="automated-migration-script"></a>Skript för automatiserad migrering

Du kan använda API:et för att automatisera migrering av innehåll mellan två API Management-tjänster , till exempel en tjänst i testmiljön och en tjänst i produktionsmiljön. Skriptet `scripts.v3/migrate.js` i GitHub API Management portalen [förenklar](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js) den här automatiseringsprocessen.

> [!WARNING]
> Skriptet tar bort innehållet i utvecklarportalen i din API Management tjänsten. Om du är orolig för det bör du utföra en säkerhetskopiering.

> [!NOTE]
> Om du använder en portal med egen värd med ett explicit definierat anpassat lagringskonto som värd för mediefiler (d.v.s. du definierar inställningen i konfigurationsfilen) måste du använda det ursprungliga `blobStorageUrl` `config.design.json` `scripts/migrate.js` [skriptet](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js). Det ursprungliga skriptet fungerar inte för hanterade eller lokala portaler med medielagringskontot som hanteras av API Management. I så fall använder du skriptet från `/scripts.v3` mappen i stället.

Skriptet utför följande steg:

1. Samla in portalinnehåll och media från källtjänsten API Management tjänsten.
1. Ta bort portalinnehåll och media från API Management tjänsten.
1. Ladda upp portalinnehåll och media till API Management tjänsten.
1. Om du vill kan du välja och endast för hanterade portaler – publicera portalen automatiskt.

När skriptet har körts ska måltjänsten API Management innehålla samma portalinnehåll som källtjänsten och du kommer att kunna se det som administratör.

* Om du använder en hanterad portal kan du ange att skriptet ska publicera målportalen automatiskt så att den migrerade versionen blir automatiskt tillgänglig för besökare. 
* Om du använder en portal med egen värd måste du publicera målportalen manuellt. Följ instruktionerna för publicering och värd i självstudien [för att konfigurera en utvecklarportal med egen värd.](developer-portal-self-host.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)
- [Utvecklarportalen med egen värd](developer-portal-self-host.md)