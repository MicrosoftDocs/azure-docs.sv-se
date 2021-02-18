---
title: Visa diagnostikloggar för Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Lär dig att konfigurera och komma åt diagnostikloggar för Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 0b84e26962b00ee5b9d4c34cab7efbcc9aa0bf01
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582808"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Åtkomst till diagnostikloggar för Azure Data Lake Storage Gen1
Lär dig att aktivera diagnostikloggning för ditt Azure Data Lake Storage Gen1-konto och hur du visar de loggar som samlats in för ditt konto.

Organisationer kan aktivera diagnostikloggning för sitt Azure Data Lake Storage Gen1 konto för att samla in gransknings spårningar för data åtkomst som innehåller information, till exempel en lista över användare som har åtkomst till data, hur ofta data lagras, hur mycket data som lagras i kontot osv. När den är aktive rad loggas diagnostiken och/eller förfrågningarna på bästa möjliga villkor. Både förfrågningar och diagnostikloggar skapas endast om det finns begär Anden som görs mot tjänst slut punkten.

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Aktivera diagnostikloggning för ditt Data Lake Storage Gen1 konto
1. Logga in på nya [Azure Portal](https://portal.azure.com).
2. Öppna ditt Data Lake Storage Gen1 konto och klicka på **diagnostikinställningar** från bladet data Lake Storage gen1 konto.
3. Klicka på **Aktivera diagnostik** i bladet **diagnostikinställningar** .

    ![Skärm bild av Data Lake Storage gen 1-konto med alternativet för diagnostiska inställningar och alternativet Aktivera diagnostik som kallas för.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Aktivera diagnostikloggar")

3. På bladet **diagnostikinställningar** gör du följande ändringar för att konfigurera diagnostisk loggning.
   
    ![Skärm bild av avsnittet diagnostiska inställningar med text rutan namn och alternativet Spara som anropas.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Aktivera diagnostikloggar")
   
   * I **namn** anger du ett värde för konfiguration av diagnos logg.
   * Du kan välja att lagra/bearbeta data på olika sätt.
     
        * Välj alternativet att **arkivera till ett lagrings konto** för att lagra loggar till ett Azure Storage-konto. Du använder det här alternativet om du vill arkivera de data som ska bearbetas vid ett senare tillfälle. Om du väljer det här alternativet måste du ange ett Azure Storage konto för att spara loggarna till.
        
        * Välj alternativet att **strömma till en Event Hub** för att strömma loggdata till en Azure Event Hub. Förmodligen kommer du att använda det här alternativet om du har en efterföljande bearbetnings pipeline för att analysera inkommande loggar i real tid. Om du väljer det här alternativet måste du ange information om den Azure Event Hub som du vill använda.

        * Välj alternativet att **Skicka till Log Analytics** för att analysera de genererade loggdata med hjälp av tjänsten Azure Monitor. Om du väljer det här alternativet måste du ange information om arbets ytan Log Analytics som du använder för att utföra logg analys. Se [Visa eller analysera data som samlats in med Azure Monitor loggar Sök](../azure-monitor/logs/log-analytics-tutorial.md) för information om hur du använder Azure Monitor loggar.
     
   * Ange om du vill hämta gransknings loggar eller begär ande loggar eller båda.
   * Ange hur många dagar data ska behållas. Kvarhållning är bara tillgängligt om du använder Azure Storage-kontot för att arkivera loggdata.
   * Klicka på **Spara**.

När du har aktiverat diagnostikinställningar kan du titta på loggarna på fliken **diagnostikloggar** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Visa diagnostikloggar för ditt Data Lake Storage Gen1 konto
Det finns två sätt att Visa loggdata för ditt Data Lake Storage Gen1-konto.

* Från vyn Data Lake Storage Gen1 konto inställningar
* Från det Azure Storage konto där data lagras

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Använda vyn Data Lake Storage Gen1 inställningar
1. Från bladet Data Lake Storage Gen1 konto **Inställningar** klickar du på **diagnostikloggar**.
   
    ![Visa diagnostikloggar](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visa diagnostikloggar") 
2. På bladet **diagnostik-loggar** bör du se loggarna kategoriserade efter **gransknings loggar** och **begär ande loggar**.
   
   * Begär ande loggar fångar varje API-begäran som gjorts på Data Lake Storage Gen1 kontot.
   * Gransknings loggar liknar begär ande loggar men ger en mycket mer detaljerad analys av de åtgärder som utförs på Data Lake Storage Gen1 kontot. Till exempel kan ett enda överförings-API-anrop i begär ande loggar resultera i flera "append"-åtgärder i gransknings loggarna.
3. Hämta loggarna genom att klicka på länken **Hämta** för varje loggpost.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Från det Azure Storage konto som innehåller loggdata
1. Öppna bladet Azure Storage konto som är associerat med Data Lake Storage Gen1 för loggning och klicka sedan på blobbar. Bladet **BLOB service** visar två behållare.
   
    ![Skärm bild av bladet Data Lake Storage gen 1-bladet de BLOB-alternativ som valts och bladet med blogg tjänster med namnen på de två BLOB-tjänsterna som anropas.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visa diagnostikloggar")
   
   * Behållar **insikter-loggar-audit** innehåller gransknings loggarna.
   * Behållar **insikter – loggar-förfrågningar** innehåller förfrågnings loggar.
2. I dessa behållare lagras loggarna under följande struktur.
   
    ![Skärm bild av logg strukturen som den lagras i behållaren.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visa diagnostikloggar")
   
    Den fullständiga sökvägen till en Gransknings logg kan exempelvis vara `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    På samma sätt kan den fullständiga sökvägen till en begär ande logg vara `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Förstå logg data strukturen
Gransknings-och förfrågnings loggarna är i JSON-format. I det här avsnittet tittar vi på strukturen i JSON för begäran och gransknings loggar.

### <a name="request-logs"></a>Begär ande loggar
Här är en exempel post i den JSON-formaterade begär ande loggen. Varje Blob har ett rot objekt som kallas **poster** som innehåller en matris med logg objekt.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Begär logg schema
| Namn | Typ | Description |
| --- | --- | --- |
| time |Sträng |Tids stämplingen (i UTC) för loggen |
| resourceId |Sträng |ID för den resurs som åtgärden ägde rum på |
| category |Sträng |Logg kategorin. Till exempel **begär Anden**. |
| operationName |Sträng |Namnet på den åtgärd som loggas. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| callerIpAddress |Sträng |IP-adressen för klienten som gör begäran |
| correlationId |Sträng |ID för den logg som kan användas för att gruppera samman en uppsättning relaterade logg poster |
| identity |Objekt |Den identitet som genererade loggen |
| properties |JSON |Se nedan för information |

#### <a name="request-log-properties-schema"></a>Schema för begär ande logg egenskaper
| Namn | Typ | Description |
| --- | --- | --- |
| HttpMethod |Sträng |HTTP-metoden som används för åtgärden. Till exempel GET. |
| Sökväg |Sträng |Den sökväg som åtgärden utfördes på |
| RequestContentLength |int |Innehålls längden för HTTP-begäran |
| ClientRequestId |Sträng |Det ID som unikt identifierar den här begäran |
| StartTime |Sträng |Tiden då servern tog emot begäran |
| EndTime |Sträng |Tiden då servern skickade ett svar |
| StoreIngressSize |Lång |Storlek i byte som inträngt i Data Lake Store |
| StoreEgressSize |Lång |Storlek i byte som pågick från Data Lake Store |
| QueryParameters |Sträng |Beskrivning: det här är http-frågeparametrar. Exempel 1: API-version = 2014-01-01&op = getfilestatus exempel 2: OP = APPEND&append = True&syncFlag = DATA&filesessionid = bee3355a-4925-4435-bb4d-ceea52811aeb&leaseID = bee3355a-4925-4435-bb4d-ceea52811aeb&offset = 28313319&API-version = 2017-08-01 |

### <a name="audit-logs"></a>Granskningsloggar
Här är en exempel post i den JSON-formaterade gransknings loggen. Varje Blob har ett rot objekt som heter **poster** som innehåller en matris med logg objekt

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Schema för spårningslogg
| Namn | Typ | Description |
| --- | --- | --- |
| time |Sträng |Tids stämplingen (i UTC) för loggen |
| resourceId |Sträng |ID för den resurs som åtgärden ägde rum på |
| category |Sträng |Logg kategorin. Till exempel **granskning**. |
| operationName |Sträng |Namnet på den åtgärd som loggas. Till exempel getfilestatus. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| resultSignature |Sträng |Ytterligare information om åtgärden. |
| correlationId |Sträng |ID för den logg som kan användas för att gruppera samman en uppsättning relaterade logg poster |
| identity |Objekt |Den identitet som genererade loggen |
| properties |JSON |Se nedan för information |

#### <a name="audit-log-properties-schema"></a>Schema för gransknings logg egenskaper
| Namn | Typ | Description |
| --- | --- | --- |
| StreamName |Sträng |Den sökväg som åtgärden utfördes på |

## <a name="samples-to-process-the-log-data"></a>Exempel för bearbetning av loggdata
När du skickar loggar från Azure Data Lake Storage Gen1 till Azure Monitor loggar (se [Visa eller analysera data som samlats in med Azure Monitor loggar](../azure-monitor/logs/log-analytics-tutorial.md) för att läsa mer om hur du använder Azure Monitor loggar) returnerar följande fråga en tabell som innehåller en lista över användarens visnings namn, tidpunkten för händelserna och antalet händelser för tiden för händelsen tillsammans med ett visuellt diagram. Det kan enkelt ändras för att Visa användar-GUID eller andra attribut:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 innehåller ett exempel på hur du bearbetar och analyserar loggdata. Du kan hitta exemplet på [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
