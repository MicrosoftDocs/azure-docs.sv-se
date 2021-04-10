---
title: Aktivera och hantera Azure-lagringsanalys loggar (klassisk) | Microsoft Docs
description: Lär dig hur du övervakar ett lagrings konto i Azure med hjälp av Azure-lagringsanalys.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 0c182e1093c29206d27a0e55a46dd9a5607fa6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701713"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Aktivera och hantera Azure-lagringsanalys loggar (klassisk)

[Azure-lagringsanalys](storage-analytics.md) innehåller loggar för blobbar, köer och tabeller. Du kan använda [Azure Portal](https://portal.azure.com) för att konfigurera loggar registreras för ditt konto. Den här artikeln visar hur du aktiverar och hanterar loggar. Information om hur du aktiverar mått finns i [Aktivera och hantera Azure-lagringsanalys mått (klassisk)]().  Det finns kostnader för att undersöka och lagra övervaknings data i Azure Portal. Mer information finns i [Lagringsanalys](storage-analytics.md).

> [!NOTE]
> Vi rekommenderar att du använder Azure Storage loggar i Azure Monitor i stället för Lagringsanalys loggar. Azure Storage loggar i Azure Monitor finns i offentlig för hands version och är tillgänglig för för hands testning i alla offentliga moln regioner. Den här för hands versionen aktiverar loggar för blobbar (som innehåller Azure Data Lake Storage Gen2), filer, köer och tabeller. Mer information finns i följande artiklar:
>
> - [Övervaka Azure-Blob Storage](../blobs/monitor-blob-storage.md)
> - [Övervaknings Azure Files](../files/storage-files-monitoring.md)
> - [Övervaka Azure-Queue Storage](../queues/monitor-queue-storage.md)
> - [Övervaka Azure Table Storage](../tables/monitor-table-storage.md)

En djupgående guide om hur du använder Lagringsanalys och andra verktyg för att identifiera, diagnostisera och felsöka Azure Storage-relaterade problem finns i [övervaka, diagnostisera och felsöka Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Aktivera loggar

Du kan instruera Azure Storage att spara diagnostikloggar för läsnings-, skriv-och borttagnings begär Anden för BLOB-, tabell-och Queue-tjänsterna. Den data bevarande princip som du anger gäller även för dessa loggar.

> [!NOTE]
> Azure Files stöder för närvarande Lagringsanalyss mått, men har inte stöd för Lagringsanalys loggning.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)väljer du **lagrings konton** och sedan namnet på lagrings kontot för att öppna bladet lagrings konto.

2. Välj **diagnostikinställningar (klassisk)** i avsnittet **övervakning (klassisk)** på Meny bladet.

    ![Meny alternativet diagnostik under övervakning i Azure Portal.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Se till att **status** är inställt **på på** och välj de **tjänster** som du vill aktivera loggning för.

   > [!div class="mx-imgBorder"]
   > ![Konfigurera loggning i Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Kontrol lera att kryss rutan **ta bort data** är markerad.  Ange sedan antalet dagar som du vill att loggdata ska behållas genom att flytta skjutreglaget under kryss rutan eller genom att direkt ändra värdet som visas i text rutan bredvid skjutreglagets kontroll. Standardvärdet för nya lagrings konton är sju dagar. Om du inte vill ange en bevarande princip anger du noll. Om det inte finns någon bevarande princip, är det upp till dig att ta bort loggdata.

   > [!WARNING]
   > Loggar lagras som data i ditt konto. loggdata kan ackumuleras i ditt konto över tid som kan öka lagrings kostnaderna. Om du bara behöver logga data under en liten tids period kan du minska kostnaderna genom att ändra data lagrings principen. Inaktuella loggdata (data som är äldre än din bevarande princip) tas bort av systemet. Vi rekommenderar att du anger en bevarande princip baserat på hur länge du vill behålla loggdata för ditt konto. Mer information finns i [fakturering av lagrings mått](storage-analytics-metrics.md#billing-on-storage-metrics) .

4. Klicka på **Spara**.

   Diagnostikloggar sparas i en BLOB-behållare med namnet *$logs* i ditt lagrings konto. Du kan visa loggdata med hjälp av en lagrings Utforskare som [Microsoft Azure Storage Explorer](https://storageexplorer.com)eller program mässigt med hjälp av lagrings klient biblioteket eller PowerShell.

   Information om hur du kommer åt $logs-behållaren finns i [Storage Analytics-loggning](storage-analytics-logging.md).

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

6. Använd **set-AzStorageServiceLoggingProperty** för att ändra de aktuella logg inställningarna. De cmdletar som styr lagrings loggningen använder en **LoggingOperations** -parameter som är en sträng som innehåller en kommaavgränsad lista över begär ande typer som ska loggas. De tre möjliga typerna av begäran är **läsa**, **skriva** och **ta bort**. Om du vill stänga av loggning använder du värdet **none** för parametern **LoggingOperations** .  

   Följande kommando växlar vid loggning för läsnings-, skriv-och borttagnings begär anden i Kötjänst i ditt standard lagrings konto med kvarhållning inställt på fem dagar:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Loggar lagras som data i ditt konto. loggdata kan ackumuleras i ditt konto över tid som kan öka lagrings kostnaderna. Om du bara behöver logga data under en liten tids period kan du minska kostnaderna genom att ändra data lagrings principen. Inaktuella loggdata (data som är äldre än din bevarande princip) tas bort av systemet. Vi rekommenderar att du anger en bevarande princip baserat på hur länge du vill behålla loggdata för ditt konto. Mer information finns i [fakturering av lagrings mått](storage-analytics-metrics.md#billing-on-storage-metrics) .
   
   Följande kommando stänger av loggningen av tabell tjänsten på ditt standard lagrings konto:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Information om hur du konfigurerar Azure PowerShell-cmdletar så att de fungerar med din Azure-prenumeration och hur du väljer det standard lagrings konto som ska användas finns i: [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Ändra kvarhållningsperioden för logg data

Loggdata kan ackumuleras i ditt konto över tid som kan öka lagrings kostnaderna. Om du bara behöver logga data under en liten tids period kan du minska kostnaderna genom att ändra logg data kvarhållningsperioden. Om du till exempel bara behöver loggar för tre dagar ställer du in logg perioden för logg data till värdet `3` . På så sätt kommer loggar att tas bort automatiskt från ditt konto efter tre dagar. Det här avsnittet visar hur du visar din aktuella logg period för logg data och sedan uppdaterar den perioden om det är vad du vill göra.

> [!NOTE]
> De här stegen gäller endast för konton där inställningen **hierarkiskt namn område** inte är aktive rad. Om du har aktiverat inställningen på ditt konto stöds inte inställningen för kvarhållning av dagar ännu. I stället måste du ta bort loggarna manuellt genom att använda ett verktyg som stöds, till exempel Azure Storage Explorer, REST eller en SDK. Information om hur du hittar dessa loggar i ditt lagrings konto finns i [Hur loggar lagras](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. I [Azure Portal](https://portal.azure.com)väljer du **lagrings konton** och sedan namnet på lagrings kontot för att öppna bladet lagrings konto.
2. Välj **diagnostikinställningar (klassisk)** i avsnittet **övervakning (klassisk)** på Meny bladet.

    ![Meny alternativet diagnostik under övervakning i Azure Portal](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Kontrol lera att kryss rutan **ta bort data** är markerad.  Ange sedan antalet dagar som du vill att loggdata ska behållas genom att flytta skjutreglaget under kryss rutan eller genom att direkt ändra värdet som visas i text rutan bredvid skjutreglagets kontroll.

   > [!div class="mx-imgBorder"]
   > ![Ändra kvarhållningsperioden i Azure Portal](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Standard antalet dagar för nya lagrings konton är sju dagar. Om du inte vill ange en bevarande princip anger du noll. Om det inte finns någon bevarande princip är det upp till dig att ta bort övervaknings data.
   
4. Klicka på **Spara**.

   Diagnostikloggar sparas i en BLOB-behållare med namnet *$logs* i ditt lagrings konto. Du kan visa loggdata med hjälp av en lagrings Utforskare som [Microsoft Azure Storage Explorer](https://storageexplorer.com)eller program mässigt med hjälp av lagrings klient biblioteket eller PowerShell.

   Information om hur du kommer åt $logs-behållaren finns i [Storage Analytics-loggning](storage-analytics-logging.md).

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

5. Hämta lagrings konto kontexten som definierar lagrings kontot.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Ersätt `<resource-group-name>` placeholder-värdet med namnet på din resurs grupp.

   * Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto. 

6. Använd [Get-AzStorageServiceLoggingProperty](/powershell/module/az.storage/get-azstorageserviceloggingproperty) för att visa den aktuella logg bevarande principen. I följande exempel skrivs kvarhållningsperiod för blob-och Queue Storage-tjänster till-konsolen.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   I konsolens utdata visas kvarhållningsperioden under `RetentionDays` kolumn rubriken.

   > [!div class="mx-imgBorder"]
   > ![Bevarande princip i PowerShell-utdata](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Använd [set-AzStorageServiceLoggingProperty](/powershell/module/az.storage/set-azstorageserviceloggingproperty) för att ändra kvarhållningsperioden. I följande exempel ändras kvarhållningsperioden till 4 dagar.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Information om hur du konfigurerar Azure PowerShell-cmdletar så att de fungerar med din Azure-prenumeration och hur du väljer det standard lagrings konto som ska användas finns i: [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[.NET-V12](#tab/dotnet)

I följande exempel skrivs kvarhållningsperiod för blob-och Queue Storage-tjänster till-konsolen.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

I följande exempel ändras kvarhållningsperioden till 4 dagar. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

I följande exempel skrivs kvarhållningsperiod för blob-och Queue Storage-tjänster till-konsolen.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

I följande exempel ändras kvarhållningsperioden för loggar för blob-och Queue Storage-tjänsterna till 4 dagar. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Verifiera att loggdata tas bort

Du kan kontrol lera att loggarna tas bort genom att visa innehållet i `$logs` behållaren för ditt lagrings konto. Följande bild visar innehållet i en mapp i `$logs` behållaren. Mappen motsvarar 2021 januari och varje mapp innehåller loggar för en dag. Om dagen idag var i januari 29 2021 och din bevarande princip är inställd på endast en dag, ska den här mappen innehålla loggar för endast en dag.

> [!div class="mx-imgBorder"]
> ![Lista över loggmappen i Azure Portal](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Visa loggdata

 Om du vill visa och analysera loggdata bör du hämta de blobbar som innehåller de loggdata som du är intresse rad av för en lokal dator. Många verktyg för lagrings surfning gör att du kan ladda ned blobbar från ditt lagrings konto. Du kan också använda det Azure Storage team som angavs med kommando rads verktyget [AzCopy](storage-use-azcopy-v10.md) för Azure Copy för att hämta logg data.  
 
>[!NOTE]
> `$logs`Behållaren är inte integrerad med event Grid, så du får inga meddelanden när loggfilerna skrivs. 

 För att se till att du hämtar loggdata som du är intresse rad av och för att undvika att ladda ned samma logg data mer än en gång:  

-   Använd namngivnings konventionen för datum och tid för blobbar som innehåller loggdata för att spåra vilka blobbar som du redan har laddat ned för analys för att undvika att ladda ned samma data mer än en gång.  

-   Använd metadata på Blobbarna som innehåller loggdata för att identifiera den specifika perioden som blobben innehåller loggdata för att identifiera den exakta blobben som du behöver hämta.  

Information om hur du kommer igång med AzCopy finns i [Kom igång med AZCopy](storage-use-azcopy-v10.md) 

I följande exempel visas hur du kan hämta logg data för Queue Service för timmar som börjar vid 09, 10 AM och 11 AM den 20 maj, 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Mer information om hur du hämtar vissa filer finns i [Hämta blobbar från Azure Blob Storage med hjälp av AzCopy v10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

När du har hämtat dina loggdata kan du se logg posterna i filerna. Loggfilerna använder ett avgränsat text format som många logg läsnings verktyg kan parsa (mer information finns i hand boken [övervakning, diagnostisering och fel sökning Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Olika verktyg har olika funktioner för formatering, filtrering, sortering och AD söker efter innehållet i dina loggfiler. Mer information om logg fils format och innehåll för lagrings loggning finns i [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format) och [Lagringsanalys loggade åtgärder och status meddelanden](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Nästa steg

* Mer information om Lagringsanalys finns i [Lagringsanalys](storage-analytics.md) för Lagringsanalys.
* Mer information om hur du använder ett .NET-språk för att konfigurera lagrings loggning finns i [referens för lagrings klient bibliotek](/previous-versions/azure/dn261237(v=azure.100)). 
* Allmän information om hur du konfigurerar lagrings loggning med hjälp av REST API finns i [Aktivera och konfigurera Lagringsanalys](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Läs mer om formatet för Lagringsanalys loggar. Se [Lagringsanalys logg format](/rest/api/storageservices/storage-analytics-log-format).
