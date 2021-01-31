---
title: Azure-lagringsanalys mått (klassisk)
description: Lär dig hur du använder Lagringsanalys mått i Azure Storage. Lär dig mer om transaktions-och kapacitets mått, hur mått lagras, aktivera mått med mera.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 8f698aadc24d0dc0691743f1d8dd54c5d5fd287e
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220964"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure-lagringsanalys mått (klassisk)

Den **31 augusti 2023** Lagringsanalys Mät värden, även kallade *klassiska mått* , kommer att dras tillbaka. Mer information finns i det [officiella meddelandet](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Om du använder klassiska mått ser du till att gå över till mått i Azure Monitor före detta datum. Den här artikeln hjälper dig med övergången. 

Azure Storage använder Lagringsanalys-lösningen för att lagra mått som innehåller samlad transaktions statistik och kapacitets data om begär anden till en lagrings tjänst. Transaktioner rapporteras på API-åtgärds nivå och på lagrings tjänst nivå. Kapaciteten rapporteras på lagrings tjänst nivå. Mått data kan användas för att:
- Analysera användningen av lagrings tjänsten.
- Diagnostisera problem med begär Anden som görs mot lagrings tjänsten.
- Förbättra prestanda för program som använder en tjänst.

 Lagringsanalys mått är aktiverade som standard för nya lagrings konton. Du kan konfigurera mått i [Azure Portal](https://portal.azure.com/), med hjälp av PowerShell eller med hjälp av Azure CLI. Steg-för-steg-anvisningar finns i [Aktivera och hantera Azure Storage analytiska mått (klassisk)](./storage-monitor-storage-account.md). Du kan också aktivera Lagringsanalys program mässigt via REST API eller klient biblioteket. Använd åtgärderna ange tjänst egenskaper för att aktivera Lagringsanalys för varje tjänst.  

> [!NOTE]
> Lagringsanalys mått är tillgängliga för Azure Blob Storage, Azure Queue Storage, Azure Table Storage och Azure Files.
> Lagringsanalys mått är nu klassiska mått. Vi rekommenderar att du använder [lagrings mått i Azure Monitor](../blobs/monitor-blob-storage.md) i stället för Lagringsanalys mått.

## <a name="transaction-metrics"></a>Transaktionsmått  
 En robust uppsättning data registreras i timmar eller minuter för varje lagrings tjänst och begärd API-åtgärd, vilket omfattar ingångs-och utgångs-, tillgänglighets-, fel-och kategoriserade förfrågningar i procent. En fullständig lista över transaktions information finns i [schemat för Lagringsanalys Metrics Table](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Transaktions data registreras på tjänst nivå och API-åtgärds nivå. På service nivå skrivs statistik som sammanfattar alla begärda API-åtgärder till en tabell enhet varje timme, även om inga förfrågningar har gjorts till tjänsten. På API-åtgärds nivån skrivs statistik endast till en entitet om åtgärden begärdes inom den timmen.  

 Om du till exempel utför en **GetBlob** -åtgärd på Blob-tjänsten loggar Lagringsanalys måtten begäran och inkluderar den i sammanställda data för Blob-tjänsten och **GetBlob** -åtgärden. Om ingen **GetBlob** -åtgärd begärs under timmen, skrivs ingen entitet till *$MetricsTransactionsBlob* för åtgärden.  

 Transaktions mått registreras för användar förfrågningar och förfrågningar som görs av Lagringsanalys. Till exempel registreras begär Anden som Lagringsanalys till Skriv loggar och tabell enheter.

## <a name="capacity-metrics"></a>Kapacitetsmått  

> [!NOTE]
>  För närvarande är kapacitets måtten bara tillgängliga för Blob-tjänsten.

 Kapacitets data registreras dagligen för lagrings kontots BLOB service och två tabell enheter skrivs. En entitet ger statistik för användar data, och den andra innehåller statistik om `$logs` BLOB-behållaren som används av Lagringsanalys. Tabellen *$MetricsCapacityBlob* innehåller följande statistik:  

- **Kapacitet**: mängden lagrings utrymme som används av lagrings kontots BLOB service, i byte.  
- **ContainerCount**: antalet BLOB-behållare i lagrings kontots BLOB service.  
- **ObjectCount**: antalet allokerade och icke allokerade block eller sid-blobar i lagrings kontots BLOB service.  

  Mer information om kapacitets mått finns i [Lagringsanalys Metrics Table schema](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Hur mått lagras  

 Alla mät värden för var och en av lagrings tjänsterna lagras i tre tabeller som reserver ATS för den tjänsten. En tabell är för transaktionsinformation, en tabell är för information om minut transaktioner och en annan tabell är för kapacitets information. Transaktions-och minut transaktions information består av begär ande-och svars data. Kapacitets informationen består av lagrings användnings data. Tim mått, minut mått och kapacitet för ett lagrings kontos blob-tjänst nås i tabeller som heter enligt beskrivningen i följande tabell.  

|Mått nivå|Tabell namn|Stöds för versioner|  
|-------------------|-----------------|----------------------------|  
|Tim mått, primär plats|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Versioner före den 15 augusti 2013. Även om dessa namn fortfarande stöds, rekommenderar vi att du växlar till att använda de tabeller som följer.|  
|Tim mått, primär plats|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Alla versioner. Stöd för fil tjänst mått är bara tillgängligt i version 5 april 2015 och senare.|  
|Minut mått, primär plats|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Alla versioner. Stöd för fil tjänst mått är bara tillgängligt i version 5 april 2015 och senare.|  
|Tim mått, sekundär plats|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Alla versioner. Geo-redundant replikering med Läs behörighet måste vara aktiverat.|  
|Minut mått, sekundär plats|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Alla versioner. Geo-redundant replikering med Läs behörighet måste vara aktiverat.|  
|Kapacitet (endast blob-tjänst)|$MetricsCapacityBlob|Alla versioner.|  

 Tabellerna skapas automatiskt när Lagringsanalys har Aktiver ATS för lagrings tjänstens slut punkt. De går att komma åt via lagrings kontots namnrymd, till exempel `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . Mått tabellerna visas inte i en List åtgärd och måste nås direkt via tabell namnet.

## <a name="metrics-alerts"></a>Mått varningar
Överväg att konfigurera aviseringar i [Azure Portal](https://portal.azure.com) så att du automatiskt får ett meddelande om viktiga förändringar i hur dina lagrings tjänster fungerar. Steg-för-steg-anvisningar finns i [skapa mått aviseringar](storage-monitor-storage-account.md#create-metric-alerts).

Om du använder ett Storage Explorer-verktyg för att hämta data i ett avgränsat format kan du använda Microsoft Excel för att analysera data. En lista över tillgängliga Storage Explorer-verktyg finns i [Azure Storage klient verktyg](./storage-explorers.md).

> [!IMPORTANT]
> Det kan finnas en fördröjning mellan en lagrings händelse och när motsvarande tim-eller minut mått data registreras. Om det gäller minut mått kan flera minuters data skrivas samtidigt. Det här problemet kan leda till transaktioner från tidigare minuter som sammanställs i transaktionen för den aktuella minuten. När det här problemet inträffar kanske aviserings tjänsten inte har alla tillgängliga mått data för det konfigurerade aviserings intervallet, vilket kan leda till att aviseringar uppstår oväntade.
>

## <a name="billing-on-storage-metrics"></a>Fakturering av lagrings mått
Skriv förfrågningar för att skapa tabell enheter för mått debiteras enligt de standard priser som gäller för alla Azure Storage åtgärder.  

Läs-och borttagnings begär Anden för Mät data av en klient faktureras också enligt standardpriser. Om du har konfigurerat en princip för data bevarande debiteras du inte när Azure Storage tar bort gamla mått data. Om du tar bort Analytics-data debiteras ditt konto för borttagnings åtgärderna.  

Kapaciteten som används av mått tabellerna är också fakturerbar. Använd följande information för att beräkna hur mycket kapacitet som används för att lagra mått data:  

-   Om en tjänst per timme använder varje API i varje tjänst lagras cirka 148 KB data varje timme i tabellerna med mått transaktioner om du har aktiverat en sammanfattning på tjänst nivå och API-nivå.  
-   Om en tjänst inom varje timme använder sig av varje API i tjänsten lagras cirka 12 KB data varje timme i tabellerna med mått transaktioner om du bara har aktiverat en sammanfattning på tjänst nivå.  
-   Kapacitets tabellen för blobbar har två rader tillagda varje dag som du har valt för loggar. Det här scenariot innebär att storleken på den här tabellen ökar med upp till cirka 300 byte.

## <a name="next-steps"></a>Nästa steg
* [Övervaka ett lagringskonto](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schema för Lagringsanalys mått tabellen](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Lagringsanalys loggning](storage-analytics-logging.md)