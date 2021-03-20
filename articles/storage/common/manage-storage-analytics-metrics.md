---
title: Aktivera och hantera Azure-lagringsanalys mått (klassisk) | Microsoft Docs
description: Lär dig hur du aktiverar, redigerar och visar Azure-lagringsanalys mått.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99221646"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Aktivera och hantera Azure-lagringsanalys mått (klassisk)

[Azure-lagringsanalys](storage-analytics.md) innehåller mått för alla lagrings tjänster för blobbar, köer och tabeller. Du kan använda [Azure Portal](https://portal.azure.com) för att konfigurera vilka mått som registreras för ditt konto och konfigurera diagram som ger visuella representationer av dina Mät data. Den här artikeln visar hur du aktiverar och hanterar mått. Information om hur du aktiverar loggar finns i [Aktivera och hantera Azure-lagringsanalys loggar (klassisk)](manage-storage-analytics-logs.md).

Vi rekommenderar att du läser [Azure Monitor för lagring](../../azure-monitor/insights/storage-insights-overview.md) (för hands version). Det är en funktion i Azure Monitor som erbjuder omfattande övervakning av dina Azure Storage-konton genom att leverera en enhetlig vy över dina Azure Storage tjänsters prestanda, kapacitet och tillgänglighet. Du behöver inte aktivera eller konfigurera något, och du kan direkt Visa dessa mått från de fördefinierade interaktiva diagrammen och andra visualiseringar som ingår.

> [!NOTE]
> Det finns kostnader för att undersöka övervaknings data i Azure Portal. Mer information finns i [Lagringsanalys](storage-analytics.md).
>
> Förstklassiga prestanda Block Blob Storage-konton stöder inte Lagringsanalys mått. Om du vill visa mått med förstklassiga prestanda Block Blob Storage-konton kan du överväga att använda [Azure Storage mått i Azure Monitor](../blobs/monitor-blob-storage.md).
>
> En djupgående guide om hur du använder Lagringsanalys och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Aktivera mått

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)väljer du **lagrings konton** och sedan namnet på lagrings kontot för att öppna instrument panelen för kontot.

2. Välj **diagnostikinställningar (klassisk)** i avsnittet **övervakning (klassisk)** på Meny bladet.
   
   ![Skärm bild som markerar alternativet diagnostikinställningar (klassisk) under avsnittet övervakning (klassisk).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Välj **typ** av mått data för varje **tjänst** som du vill övervaka och **bevarande principen** för data. Du kan också inaktivera övervakning genom att ange **status** till **av**.

   > [!div class="mx-imgBorder"]
   > ![Konfigurera loggning i Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Om du vill ange data bevarande principen flyttar du skjutreglaget för **kvarhållning (dagar)** eller anger hur många dagars data som ska behållas, från 1 till 365. Standardvärdet för nya lagrings konton är sju dagar. Om du inte vill ange en bevarande princip anger du noll. Om det inte finns någon bevarande princip är det upp till dig att ta bort övervaknings data.

   > [!WARNING]
   > Metics lagras som data i ditt konto. Mät data kan ackumuleras i ditt konto över tid, vilket kan öka lagrings kostnaderna. Om du behöver data för endast en liten tids period kan du minska kostnaderna genom att ändra principen för data lagring. Inaktuella Mät data (data som är äldre än din bevarande princip) tas bort av systemet. Vi rekommenderar att du anger en bevarande princip baserat på hur länge du vill behålla mått data för ditt konto. Mer information finns i [fakturering av lagrings mått](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

4. När du är klar med övervaknings konfigurationen väljer du **Spara**.

En standard uppsättning mått visas i diagram på bladet **Översikt** , samt **måtten (det klassiska)** bladet. När du har aktiverat mått för en tjänst kan det ta upp till en timme innan data visas i diagrammen. Du kan välja **Redigera** i ett mått diagram om du vill konfigurera vilka mått som visas i diagrammet.

Du kan inaktivera insamling av mått och loggning genom att ange **status** till **av**.

> [!NOTE]
> Azure Storage använder [Table Storage](storage-introduction.md#table-storage) för att lagra måtten för ditt lagrings konto och lagrar måtten i tabeller i ditt konto. Mer information finns här: [Hur mått lagras](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öppna ett Windows PowerShell-kommando fönster.

2. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

3. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

5. Hämta lagrings konto kontexten som definierar det lagrings konto som du vill använda.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto. 

6. Du kan använda PowerShell på den lokala datorn för att konfigurera lagrings mått i ditt lagrings konto. Använd Azure PowerShell cmdlet **set-AzStorageServiceMetricsProperty** för att ändra de aktuella inställningarna. 

   Följande kommando växlar på minut mått för Blob-tjänsten i ditt lagrings konto med kvarhållningsperioden inställd på fem dagar.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Denna cmdlet använder följande parametrar:  

   - **ServiceType**: möjliga värden är **BLOB**, **kö**, **tabell** och **fil**.
   - **MetricsType**: möjliga värden är **timme** och **minut**.  
   - **MetricsLevel**: möjliga värden är:
      - **Ingen**: stänger av övervakning.
      - **Tjänst**: samlar in mått som ingångs-och utgångs-, tillgänglighets-, fördröjnings-och procent andelar, som sammanställs för BLOB-, kö-, tabell-och fil tjänster.
      - **ServiceAndApi**: förutom tjänste måtten samlas samma uppsättning mått för varje lagrings åtgärd i Azure Storage tjänst-API: et.

   Följande kommando hämtar den aktuella mått nivån per timme och bevarande dagar för Blob-tjänsten på ditt standard lagrings konto:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Information om hur du konfigurerar Azure PowerShell-cmdletar så att de fungerar med din Azure-prenumeration och hur du väljer det standard lagrings konto som ska användas finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Mer information om hur du använder ett .NET-språk för att konfigurera lagrings mått finns i [Azure Storage klient bibliotek för .net](/dotnet/api/overview/azure/storage).  

Allmän information om hur du konfigurerar lagrings mått med hjälp av REST API finns i [Aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Mer information om hur du använder ett .NET-språk för att konfigurera lagrings mått finns i [Azure Storage klient bibliotek för .net](/dotnet/api/overview/azure/storage).  

Allmän information om hur du konfigurerar lagrings mått med hjälp av REST API finns i [Aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Visa mått i ett diagram

När du har konfigurerat Lagringsanalys mått för att övervaka ditt lagrings konto, Lagringsanalys registrerar måtten i en uppsättning välkända tabeller i ditt lagrings konto. Du kan konfigurera diagram för att Visa Tim mått i [Azure Portal](https://portal.azure.com).

Använd följande procedur för att välja vilka lagrings mått som ska visas i ett mått diagram.

1. Börja med att visa ett mått för lagrings mått i Azure Portal. Du kan hitta diagram på **bladet lagrings konto** och på bladet **mått (klassisk)** .

   I det här exemplet använder följande diagram som visas på **bladet lagrings konto**:

   ![Diagram val i Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Klicka någonstans i diagrammet för att redigera diagrammet.

3. Välj sedan **tidsintervallet** för de mått som ska visas i diagrammet och **tjänsten** (BLOB, kö, tabell, fil) vars mått du vill visa. Här väljer du de senaste vecko måtten som ska visas för Blob-tjänsten:

   ![Tidsintervall och tjänst val på bladet redigera diagram](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Välj de enskilda **mått** som du vill ska visas i diagrammet och klicka sedan på **OK**.

   ![Enskilda mått val i bladet redigera diagram](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Dina diagram inställningar påverkar inte insamling, sammanställning eller lagring av övervaknings data i lagrings kontot.

#### <a name="metrics-availability-in-charts"></a>Mått tillgänglighet i diagram

Listan över tillgängliga mått ändras baserat på vilken tjänst du har valt i list rutan och enhets typen för det diagram som du redigerar. Du kan till exempel välja procent värden som *PercentNetworkError* och *PercentThrottlingError* endast om du redigerar ett diagram som visar enheter i procent:

![Diagram över begär ande fel i procent i Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Mått matchning

Måtten som du har valt i **diagnostik** bestämmer upplösningen för de mått som är tillgängliga för ditt konto:

* **Aggregerad** övervakning innehåller mått som ingångs-/utgångs-, tillgänglighets-, fördröjnings-och procent andels procent. Dessa mått sammanställs från BLOB-, tabell-, fil-och Queue-tjänsterna.
* **Per API** ger bättre upplösning, med mått som är tillgängliga för enskilda lagrings åtgärder, förutom agg regeringar på tjänst nivå.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Hämta mått för att arkivera eller analysera lokalt

Om du vill hämta måtten för långsiktig lagring eller analysera dem lokalt måste du använda ett verktyg eller skriva kod för att läsa tabellerna. Tabellerna visas inte om du visar alla tabeller i ditt lagrings konto, men du kan komma åt dem direkt efter namn. Många verktyg för lagrings surfning är medvetna om dessa tabeller och gör att du kan visa dem direkt. En lista över tillgängliga verktyg finns i [Azure Storage klient verktyg](./storage-explorers.md).

|Mått|Tabell namn|Kommentarer| 
|-|-|-|  
|Tim mått|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|I tidigare versioner än den 15 augusti 2013 var de här tabellerna kända som:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Mått för fil tjänsten är tillgängliga från och med version 5 april 2015.|  
|Minut mått|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Kan endast aktive ras med hjälp av PowerShell eller program mässigt.<br /><br /> Mått för fil tjänsten är tillgängliga från och med version 5 april 2015.|  
|Kapacitet|$MetricsCapacityBlob|Endast Blob Service.|  

Fullständig information om scheman för dessa tabeller finns i [Lagringsanalys Metrics Table schema](/rest/api/storageservices/storage-analytics-metrics-table-schema). Följande exempel rader visar bara en delmängd av tillgängliga kolumner, men de illustrerar några viktiga funktioner i hur lagrings statistik sparar dessa mått:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Tillgänglighet|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|användarvänlig Vissa|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|användarvänlig QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|användarvänlig QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|användarvänlig UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

I det här exemplet på minut mått data använder partitionsnyckel tiden vid minut upplösning. Rad nyckeln identifierar den typ av information som lagras i raden. Informationen består av åtkomst typen och typen av begäran:  

-   Åtkomst typen är antingen **användare** eller **system**, där **användaren** refererar till alla användar förfrågningar till lagrings tjänsten och **systemet** refererar till begär Anden som görs av Lagringsanalys.  
-   Typen av begäran är antingen **alla**, i så fall är det en sammanfattnings rad, eller så identifierar den det specifika API: t, till exempel **QueryEntity** eller **UpdateEntity**.  

I det här exempel data visas alla poster för en enda minut (från 11:10:00), så antalet **QueryEntities** -begäranden plus antalet **QueryEntity** -begäranden plus antalet **UpdateEntity** -begäranden lägger till upp till sju. Den här summan visas på raden **användare: alla** . På samma sätt kan du härleda den genomsnittliga svars tiden från slut punkt till slut punkt 104,4286 för **användaren: all** rad genom att beräkna ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Visa mått data program mässigt

I följande lista visas exempel C#-kod som ger till gång till minut måtten för ett antal minuter och visar resultatet i ett konsol fönster. Kod exemplet använder Azure Storage klient bibliotek version 4. x eller senare, vilket omfattar klassen **CloudAnalyticsClient** som fören klar åtkomsten till mått tabellerna i Storage. 

> [!NOTE]
> **CloudAnalyticsClient** -klassen ingår inte i V12 för Azure Blob Storage-klienten för .net. Den **31 augusti 2023** Lagringsanalys Mät värden, även kallade *klassiska mått* , kommer att dras tillbaka. Mer information finns i det [officiella meddelandet](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Om du använder klassiska mått rekommenderar vi att du övergår till mått i Azure Monitor före det datumet. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Lägg till mått diagram på portalens instrument panel

Du kan lägga till Azure Storage Mät diagram för alla dina lagrings konton till portalens instrument panel.

1. Välj Klicka på **Redigera instrument panel** när du visar instrument panelen i [Azure Portal](https://portal.azure.com).
1. I **panel galleriet** väljer du **hitta paneler efter**  >  **typ**.
1. Välj **Skriv**  >  **lagrings konton**.
1. I **resurser** väljer du det lagrings konto vars mått du vill lägga till på instrument panelen.
1. Välj **Kategorier**  >  **övervakning**.
1. Dra och släpp diagram panelen på instrument panelen för måttet som du vill visa. Upprepa för alla mått som du vill ska visas på instrument panelen. I följande bild markeras diagrammet "blobbar-totalt antal förfrågningar" som ett exempel, men alla diagram är tillgängliga för placering på instrument panelen.

   ![Panel galleri i Azure Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Välj **anpassning som gjorts** nära överkanten på instrument panelen när du är klar med att lägga till diagram.

När du har lagt till diagram på instrument panelen kan du ytterligare anpassa dem enligt beskrivningen i anpassa mått diagram.

## <a name="next-steps"></a>Nästa steg

* Mer information om Lagringsanalys finns i [Lagringsanalys](storage-analytics.md) för Lagringsanalys.
* [Konfigurera Lagringsanalys loggar](manage-storage-analytics-logs.md).
* Läs mer om Metrics-schemat. Se [schemat för Lagringsanalys Metrics-tabellen](/rest/api/storageservices/storage-analytics-metrics-table-schema).