---
title: Felsöka Azure Cosmos DB förbjudna undantag
description: Lär dig hur du diagnostiserar och åtgärdar förbjudna undantag.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971915"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB förbjudna undantag
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP-statuskoden 403 representerar begäran som är förbjuden att slutföra.

## <a name="firewall-blocking-requests"></a>Blockera begär Anden om brand vägg
I det här scenariot är det vanligt att se fel som de nedan:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Lösning
Kontrol lera att de aktuella [brand Väggs inställningarna](how-to-configure-firewall.md) är korrekta och inkludera de IP-adresser eller nätverk som du försöker ansluta från.
Om du nyligen har uppdaterat dem bör du tänka på att ändringar kan ta **upp till 15 minuter att tillämpa**.

## <a name="next-steps"></a>Nästa steg
* Konfigurera [IP-brandvägg](how-to-configure-firewall.md).
* Konfigurera åtkomst från [virtuella nätverk](how-to-configure-vnet-service-endpoint.md).
* Konfigurera åtkomst från [privata slut punkter](how-to-configure-private-endpoints.md).
