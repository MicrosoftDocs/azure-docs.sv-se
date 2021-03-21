---
title: Diagnostikloggar
titleSuffix: Azure Content Delivery Network
description: Lär dig hur du använder Azure Diagnostic-loggar för att spara kärn analys, vilket gör att du kan exportera användnings statistik från din Azure Content Delivery Network-slutpunkt.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100573230"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Diagnostikloggar – Azure Content Delivery Network

Med Azure Diagnostic-loggar kan du Visa kärn analyser och spara dem i en eller flera destinationer, inklusive:

* Azure-lagringskonto
* Log Analytics-arbetsyta
* Azure Event Hubs

Den här funktionen är tillgänglig på CDN-slutpunkter för alla pris nivåer. 

Med diagnostikloggar kan du exportera grundläggande användnings statistik från CDN-slutpunkten till olika typer av källor så att du kan använda dem på ett anpassat sätt. Du kan göra följande typer av data export:

* Exportera data till Blob Storage, exportera till CSV och generera grafer i Excel.
* Exportera data till Event Hubs och korrelera med data från andra Azure-tjänster.
* Exportera data till Azure Monitor loggar och visa data i din egen Log Analytics arbets yta

Det krävs en Azure CDN profil för följande steg. Se [skapa en Azure CDN profil och slut punkt](cdn-create-new-endpoint.md) innan du fortsätter.

## <a name="enable-logging-with-the-azure-portal"></a>Aktivera loggning i Azure-portalen

Följ de här stegen för att aktivera loggning för din Azure CDN-slutpunkt:

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. I Azure Portal navigerar du till **alla resurser**  ->  **din-CDN-profil**

2. Välj den CDN-slutpunkt som du vill aktivera diagnostikloggar för:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Välj CDN-slutpunkt." border="true":::

3. Välj **diagnostikloggar** i avsnittet **övervakning** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Välj diagnostikloggar." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Aktivera loggning med Azure Storage

Följ dessa steg om du vill använda ett lagrings konto för att lagra loggarna:

 >[!NOTE] 
 >Du måste ange ett lagrings konto för att kunna slutföra dessa steg. Se: **[skapa ett Azure Storage konto](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** för mer information.
    
1. Ange ett namn för inställningarna för diagnostikinställningar för **diagnostiskt inställnings namn**.
 
2. Välj **Arkiv till ett lagrings konto** och välj sedan **CoreAnalytics**. 

3. För **kvarhållning (dagar)** väljer du antalet dagar för kvarhållning. En kvarhållning på noll dagar lagrar loggarna på obestämd tid. 

4. Välj prenumerations-och lagrings konto för loggarna.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Diagnostikloggar-lagring." border="true":::

3. Välj **Spara**.

### <a name="send-to-log-analytics"></a>Skicka till Log Analytics

Följ dessa steg om du vill använda Log Analytics för loggarna:

>[!NOTE] 
>En Log Analytics-arbetsyta krävs för att slutföra de här stegen. Mer information hittar du **[i: skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/logs/quick-create-workspace.md)** .
    
1. Ange ett namn för inställningarna för diagnostikinställningar för **diagnostiskt inställnings namn**.

2. Välj **Skicka till Log Analytics** och välj sedan **CoreAnalytics**. 

3. Välj prenumerationen och Log Analytics arbets ytan för loggarna.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Diagnostikloggar – Log Analytics." border="true":::

4. Välj **Spara**.

### <a name="stream-to-an-event-hub"></a>Strömma till en händelsehubb

Följ dessa steg om du vill använda en Event Hub för loggarna:

>[!NOTE] 
>En Event Hub krävs för att slutföra de här stegen. Se: **[snabb start: skapa en Event Hub med Azure Portal](../event-hubs/event-hubs-create.md)** för mer information.
    
1. Ange ett namn för inställningarna för diagnostikinställningar för **diagnostiskt inställnings namn**.

2. Välj **Stream till en händelsehubben** och välj sedan **CoreAnalytics**. 

3. Välj namn området för prenumeration och händelsehubben för loggarna.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Diagnostikloggar – Event Hub." border="true":::

4. Välj **Spara**.


## <a name="enable-logging-with-powershell"></a>aktivera loggning med PowerShell

I följande exempel visas hur du aktiverar diagnostikloggar via Azure PowerShell-cmdletar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Aktivera diagnostikloggar i ett lagrings konto

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Om du vill aktivera diagnostikloggar i ett lagrings konto anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Aktivera diagnostikloggar för Log Analytics arbets yta

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för en arbets yta Log Analytics anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Aktivera diagnostikloggar för Event Hub-namnområde

1. Logga in på Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Om du vill aktivera diagnostikloggar för en arbets yta Log Analytics anger du dessa kommandon. Ersätt variablerna med dina värden:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Använda diagnostikloggar från Azure Storage
I det här avsnittet beskrivs schemat för CDN Core Analytics, Organization i ett Azure Storage-konto och innehåller exempel kod för att ladda ned loggarna i en CSV-fil.

### <a name="using-microsoft-azure-storage-explorer"></a>Använda Microsoft Azure Storage Explorer
Information om hur du hämtar verktyget finns [Azure Storage Explorer](https://storageexplorer.com/). När du har laddat ned och installerat program varan konfigurerar du det så att det använder samma Azure Storage-konto som har kon figurer ATS som ett mål för CDN-diagnostikloggar.

1.  Öppna **Microsoft Azure Storage Explorer**
2.  Hitta lagrings kontot
3.  Expandera noden **BLOB containers** under det här lagrings kontot.
4.  Välj behållaren med namnet *Insights-logs-coreanalytics*.
5.  Resultaten visas i den högra rutan, med början på den första nivån som *resourceId =*. Fortsätt att markera varje nivå tills du hittar filen *PT1H.js*. En förklaring av sökvägen finns i [BLOB Path-format](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Varje Blob- *PT1H.jsi* filen representerar analys loggar för en timme för en angiven CDN-slutpunkt eller dess anpassade domän.
7.  Schemat för innehållet i denna JSON-fil beskrivs i avsnittet-schemat i Core Analytics-loggarna.


#### <a name="blob-path-format"></a>Blobb Sök vägs format

Kärn analys loggar genereras varje timme och data samlas in och lagras i en enda Azure-blob som en JSON-nyttolast. Verktyget för lagrings Utforskaren tolkar "/" som en katalog avgränsare och visar hierarkin. Sökvägen till Azure-blobben visas som om det finns en hierarkisk struktur och representerar BLOB-namnet. Namnet på blobben följer följande namngivnings konvention:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Fält Beskrivning:**

|Värde|Beskrivning|
|-------|---------|
|Prenumerations-ID:t    |ID för Azure-prenumerationen i GUID-format.|
|Namnet på resursgruppen |Namnet på resurs gruppen som CDN-resurserna tillhör.|
|Profilnamn |Namn på CDN-profilen|
|Slut punkts namn |Namn på CDN-slutpunkten|
|Year|  Fyrsiffrig representation av året, till exempel 2017|
|Månad| Tvåsiffrig representation av månads nummer. 01 = januari... 12 = december|
|Dag|   Tvåsiffrig representation av dagen i månaden|
|PT1H.jspå| Faktisk JSON-fil där analys data lagras|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportera huvud analys data till en CSV-fil

För att få åtkomst till Core Analytics tillhandahålls exempel kod för ett verktyg. Med det här verktyget kan du ladda ned JSON-filerna till ett geografiskt semikolonavgränsat fil format som kan användas för att skapa diagram eller andra agg regeringar.

Så här kan du använda verktyget:

1.  Besök GitHub-länken: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Ladda ned koden.
3.  Följ anvisningarna för att kompilera och konfigurera.
4.  Kör verktyget.
5.  Den resulterande CSV-filen visar analys data i en enkel, platt hierarki.

## <a name="log-data-delays"></a>Logga data fördröjningar

I följande tabell visas fördröjningar i logg data för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Akamai** och **Azure CDN Standard/Premium från Verizon**.

Microsoft logg data fördröjningar | Data fördröjningar för Verizon-logg | Akamai logg data fördröjningar
--- | --- | ---
Försenat med 1 timme. | Försenat med 1 timme och kan ta upp till 2 timmar innan slut för ande av slut punkts spridningen påbörjas. | Fördröjt med 24 timmar; om det skapades mer än 24 timmar sedan tar det upp till 2 timmar innan det börjar visas. Om den nyligen har skapats kan det ta upp till 25 timmar innan loggarna börjar visas.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostiska logg typer för CDN Core Analytics

Microsoft erbjuder för närvarande endast grundläggande analys loggar, som innehåller mått som visar statistik över HTTP-svar och utgångs statistik som visas från CDN pop/Kants.

### <a name="core-analytics-metrics-details"></a>Statistik information om kärn analys
I följande tabell visas en lista över mått som är tillgängliga i Core Analytics-loggarna för:

* **Azure CDN Standard från Microsoft**
* **Azure CDN Standard från Akamai**
* **Azure CDN Standard/Premium från Verizon**

Alla mått är inte tillgängliga från alla leverantörer, även om sådana skillnader är minimala. Tabellen visar även om ett specifikt mått är tillgängligt från en leverantör. Måtten är endast tillgängliga för de CDN-slutpunkter som har trafik på dem.


|Metric                     | Beskrivning | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totalt antal begär ande träffar under denna period. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Antal begär Anden som resulterade i en 2xx HTTP-kod (till exempel 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Antal begär Anden som resulterade i en 3xx HTTP-kod (till exempel 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Antal begär Anden som resulterade i en 4xx HTTP-kod (till exempel 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Antal begär Anden som resulterade i en 5xx HTTP-kod (till exempel 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Antal andra HTTP-koder (utanför 2xx-5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Antal begär Anden som resulterade i ett 200-HTTP-kods svar. | Ja | Inga  |Ja |
| RequestCountHttpStatus206 | Antal begär Anden som resulterade i ett 206-HTTP-kods svar. | Ja | Inga  |Ja |
| RequestCountHttpStatus302 | Antal begär Anden som resulterade i ett 302-HTTP-kods svar. | Ja | Inga  |Ja |
| RequestCountHttpStatus304 | Antal begär Anden som resulterade i ett 304-HTTP-kods svar. | Ja | Inga  |Ja |
| RequestCountHttpStatus404 | Antal begär Anden som resulterade i ett 404-HTTP-kods svar. | Ja | Inga  |Ja |
| RequestCountCacheHit | Antal begär Anden som resulterade i en cacheträff. Till gången betjänades direkt från POP till klienten. | Ja | Ja | Inga  |
| RequestCountCacheMiss | Antal begär Anden som resulterade i ett cache-missar. Ett cache-missar innebär att till gången inte hittades på den POP som är närmast klienten och hämtades från ursprunget. | Ja | Ja | Inga |
| RequestCountCacheNoCache | Antal begär anden till en till gång som inte tillåts att cachelagras på grund av en användar konfiguration på gränsen. | Ja | Ja | Inga |
| RequestCountCacheUncacheable | Antal begär anden till till gångar som inte kan cachelagras av till gångens Cache-Control och som förfaller huvuden. Det här antalet anger att den inte ska cachelagras på en POP-eller HTTP-klient. | Ja | Ja | Inga |
| RequestCountCacheOthers | Antal begär Anden med cache-status som inte omfattas av ovan. | Inga | Ja | Inga  |
| EgressTotal | Utgående data överföring i GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Utgående data överföring * för svar med 2xx HTTP-status koder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus3xx | Utgående data överföring för svar med 3xx HTTP-status koder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus4xx | Utgående data överföring för svar med 4xx HTTP-status koder i GB. | Ja | Ja | Inga  |
| EgressHttpStatus5xx | Utgående data överföring för svar med 5xx HTTP-status koder i GB. | Ja | Ja | Inga |
| EgressHttpStatusOthers | Utgående data överföring för svar med andra HTTP-statuskod i GB. | Ja | Ja | Inga  |
| EgressCacheHit | Utgående data överföring för svar som levererades direkt från CDN-cachen på CDN-pop/-kanter. | Ja | Ja | Inga |
| EgressCacheMiss. | Utgående data överföring för svar som inte hittades på den närmaste POP-servern och som hämtats från ursprungs servern. | Ja | Ja | Inga |
| EgressCacheNoCache | Utgående data överföring för till gångar som förhindras från att cachelagras på grund av en användar konfiguration på gränsen. | Ja | Ja | Inga |
| EgressCacheUncacheable | Utgående data överföring för till gångar som inte kan cachelagras av till gångens Cache-Control och, eller som upphör att gälla. Anger att den inte ska cachelagras på en POP-eller HTTP-klient. | Ja | Ja | Inga |
| EgressCacheOthers | Utgående data överföringar för andra cache-scenarier. | Inga | Ja | Inga |

* Utgående data överföring avser trafik som levereras från CDN POP-servrar till klienten.


### <a name="schema-of-the-core-analytics-logs"></a>Schema för kärn analys loggar 

Alla loggar lagras i JSON-format och varje post har sträng fält enligt följande schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Där *tid* representerar start tiden för den timmes gräns för vilken statistiken rapporteras. Ett mått som inte stöds av en CDN-Provider, i stället för ett dubbel-eller heltals värde, resulterar i ett null-värde. Det här null-värdet anger att ett mått saknas och skiljer sig från värdet 0. En uppsättning av dessa mått per domän konfigureras på slut punkten.

Exempel egenskaper:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Diagnostic-loggar](../azure-monitor/essentials/platform-logs-overview.md)
* [Core Analytics via Azure CDN kompletterande Portal](./cdn-analyze-usage-patterns.md)
* [Azure Monitor-loggar](../azure-monitor/logs/log-query-overview.md)
* [Azure Log Analytics REST API](/rest/api/loganalytics)