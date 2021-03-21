---
title: Använd Azure Storage analys för att samla in loggar och statistik data | Microsoft Docs
description: Med Lagringsanalys kan du spåra mått data för alla lagrings tjänster och samla in loggar för BLOB-, Queue-och table-lagring.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/03/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: a5ccaa95c8f29395770395076f75f36019a4355c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737689"
---
# <a name="storage-analytics"></a>Lagringsanalys

Azure-lagringsanalys utför loggning och tillhandahåller mått data för ett lagrings konto. Du kan använda dessa data för att spåra begär Anden, analysera användnings trender och diagnostisera problem med ditt lagrings konto.

Om du vill använda Lagringsanalys måste du aktivera det separat för varje tjänst som du vill övervaka. Du kan aktivera den från [Azure Portal](https://portal.azure.com). Mer information finns i [övervaka ett lagrings konto i Azure Portal](./manage-storage-analytics-logs.md). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd egenskaperna [Ange BLOB service](/rest/api/storageservices/set-blob-service-properties), ange egenskaper för [Queue Service](/rest/api/storageservices/set-queue-service-properties), [Ange Table service-egenskaper](/rest/api/storageservices/set-table-service-properties)och [ange åtgärder för fil tjänst egenskaper](/rest/api/storageservices/Get-File-Service-Properties) för att aktivera Lagringsanalys för varje tjänst.

De aggregerade data lagras i en välkänd BLOB (för loggning) och i välkända tabeller (för mått) som kan nås med hjälp av Blob Service-och Table service-API: er.

Lagringsanalys har en gräns på 20 TB för mängden lagrade data som är oberoende av den totala gränsen för ditt lagrings konto. Mer information om begränsningar för lagrings konton finns i [skalbarhets-och prestanda mål för standard lagrings konton](scalability-targets-standard-account.md).

En djupgående guide om hur du använder Lagringsanalys och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Fakturering för Lagringsanalys

Alla mått data skrivs av tjänsterna för ett lagrings konto. Därför är varje Skriv åtgärd som utförs av Lagringsanalys fakturerbar. Dessutom är mängden lagrings utrymme som används av mät data också fakturerbar.

Följande åtgärder som utförs av Lagringsanalys är fakturerbara:

* Begär Anden om att skapa blobbar för loggning.
* Begär Anden om att skapa tabell enheter för mått.

Om du har konfigurerat en princip för data bevarande debiteras du inte för borttagnings transaktioner när Lagringsanalys tar bort gamla loggnings-och mått data. Ta bort transaktioner från en klient är dock fakturerbara. Mer information om bevarande principer finns i [ställa in en Lagringsanalys data lagrings princip](/rest/api/storageservices/Setting-a-Storage-Analytics-Data-Retention-Policy).

### <a name="understanding-billable-requests"></a>Förstå fakturerbara förfrågningar

Varje begäran som görs till ett kontos lagrings tjänst är antingen fakturerbar eller ej fakturerbar. Lagringsanalys loggar varje enskild begäran som görs till en tjänst, inklusive ett status meddelande som anger hur begäran hanterades. På samma sätt lagrar Lagringsanalys mått för både en tjänst och API-åtgärderna för tjänsten, inklusive procent andelen och antalet vissa status meddelanden. Tillsammans kan de här funktionerna hjälpa dig att analysera dina fakturerbara förfrågningar, göra förbättringar i programmet och diagnostisera problem med förfrågningar till dina tjänster. Mer information om fakturering finns i [förstå Azure Storage fakturering-bandbredd, transaktioner och kapacitet](/archive/blogs/windowsazurestorage/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

När du tittar på Lagringsanalys data kan du använda tabellerna i avsnittet [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) för att avgöra vilka förfrågningar som är fakturerbara. Sedan kan du jämföra dina loggar och mät data med status meddelanden för att se om du debiteras för en viss begäran. Du kan också använda tabellerna i föregående avsnitt för att undersöka tillgänglighet för en lagrings tjänst eller en enskild API-åtgärd.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagringskonto i Azure-portalen](./manage-storage-analytics-logs.md)
* [Lagringsanalys mått](storage-analytics-metrics.md)
* [Loggning för lagringsanalys](storage-analytics-logging.md)