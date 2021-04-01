---
title: Felsöka Azure Cosmos DB tjänsten otillgängliga undantag med Java v4 SDK
description: Lär dig hur du diagnostiserar och åtgärdar Azure Cosmos DB tjänsten otillgängliga undantag med Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340098"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB Java v4 SDK-tjänsten otillgängliga undantag
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK kunde inte ansluta till Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för otillgängliga tjänst undantag.

### <a name="the-required-ports-are-being-blocked"></a>De portar som krävs blockeras
Kontrol lera att alla [nödvändiga portar](sql-sdk-connection-modes.md#service-port-ranges) är aktiverade.

### <a name="client-side-transient-connectivity-issues"></a>Problem med tillfälliga anslutningar på klient Sidan
Undantag för otillgängliga tjänster kan vara en yta när det finns tillfälliga anslutnings problem som orsakar tids gränser. Normalt innehåller stack spårningen som är relaterad till det här scenariot ett `ServiceUnavailableException` fel med diagnostikinformation. Exempel:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Följ [fel söknings stegen för begäran](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) för att lösa problemet.

### <a name="service-outage"></a>Tjänstavbrott
Kontrol lera [Azure-statusen](https://status.azure.com/status) för att se om det finns ett pågående problem.


## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-java-sdk-v4-sql.md) problem när du använder Azure Cosmos DB Java v4 SDK.
* Lär dig mer om prestanda rikt linjer för [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).