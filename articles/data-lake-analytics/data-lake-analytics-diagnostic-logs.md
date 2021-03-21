---
title: Aktivera och Visa diagnostikloggar för Azure Data Lake Analytics
description: Lär dig att konfigurera och komma åt diagnostikloggar för Azure Data Lake Analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221017"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Åtkomst till diagnostikloggar för Azure Data Lake Analytics

Med diagnostisk loggning kan du samla in gransknings historik för data åtkomst. Dessa loggar innehåller information som:

* En lista över användare som har åtkomst till data.
* Hur ofta data får nås.
* Hur mycket data som lagras i kontot.

## <a name="enable-logging"></a>Aktivera loggning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Öppna ditt Data Lake Analytics-konto och välj **diagnostikloggar** i avsnittet __övervaka__ . Välj sedan __Aktivera diagnostik__.

    ![Skärm bild som visar åtgärden "diagnostikloggar" som valts och aktivera diagnostik för att samla in följande loggar "markerat.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Från __diagnostikinställningar__ anger du ett __namn__ för den här loggnings konfigurationen och väljer sedan loggnings alternativ.

    ![Aktivera diagnostik för att samla in gransknings-och förfrågnings loggar](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Aktivera diagnostikloggar")

   * Du kan välja att lagra/bearbeta data på tre olika sätt.

     * Välj __arkivera till ett lagrings konto__ för att lagra loggar i ett Azure Storage-konto. Använd det här alternativet om du vill arkivera data. Om du väljer det här alternativet måste du ange ett Azure Storage-konto för att spara loggarna till.

     * Välj **Stream till en händelsehubben** för att strömma loggdata till en Azure Event Hub. Använd det här alternativet om du har en underordnad bearbetnings pipeline som analyserar inkommande loggar i real tid. Om du väljer det här alternativet måste du ange information om den Azure Event Hub som du vill använda.

     * Välj __Skicka till Log Analytics__ för att skicka data till Azure Monitors tjänsten. Använd det här alternativet om du vill använda Azure Monitor loggar för att samla in och analysera loggar.
   * Ange om du vill hämta gransknings loggar eller begär ande loggar eller båda.  En begär ande logg fångar varje API-begäran. I en Gransknings logg registreras alla åtgärder som utlöses av denna API-begäran.

   * För __Arkiv till ett lagrings konto__ anger du antalet dagar som data ska bevaras.

   * Klicka på __Spara__.

        > [!NOTE]
        > Du måste välja antingen __Arkiv till ett lagrings konto__, __strömma till en händelsehubben__ eller __Skicka till Log Analytics innan du__ klickar på knappen __Spara__ .

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Använd det Azure Storage konto som innehåller loggdata

1. Om du vill visa BLOB-behållarna som innehåller loggnings data öppnar du Azure Storage kontot som används för Data Lake Analytics loggning och klickar sedan på __blobbar__.

   * Behållar **insikter-loggar-audit** innehåller gransknings loggarna.
   * Behållar **insikter – loggar-förfrågningar** innehåller förfrågnings loggar.

2. I behållarna lagras loggarna under följande fil struktur:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > `##`Posterna i sökvägen innehåller året, månaden, dagen och timmen då loggen skapades. Data Lake Analytics skapar en fil varje timme, så `m=` innehåller alltid värdet `00` .

    Den fullständiga sökvägen till en Gransknings logg kan exempelvis vara:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    På samma sätt kan den fullständiga sökvägen till en begär ande logg vara:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Logg struktur

Gransknings-och förfrågnings loggarna är i ett strukturerat JSON-format.

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
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Begär logg schema

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |Sträng |Tids stämplingen (i UTC) för loggen |
| resourceId |Sträng |Identifieraren för resursen som åtgärden utfördes på |
| category |Sträng |Logg kategorin. Till exempel **begär Anden**. |
| operationName |Sträng |Namnet på den åtgärd som loggas. Till exempel GetAggregatedJobHistory. |
| resultType |Sträng |Status för åtgärden, till exempel 200. |
| callerIpAddress |Sträng |IP-adressen för klienten som gör begäran |
| correlationId |Sträng |Loggens identifierare. Det här värdet kan användas för att gruppera en uppsättning relaterade logg poster. |
| identity |Objekt |Den identitet som genererade loggen |
| properties |JSON |Se nästa avsnitt (begär logg egenskaper schema) för mer information |

#### <a name="request-log-properties-schema"></a>Schema för begär ande logg egenskaper

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| HttpMethod |Sträng |HTTP-metoden som används för åtgärden. Till exempel GET. |
| Sökväg |Sträng |Den sökväg som åtgärden utfördes på |
| RequestContentLength |int |Innehålls längden för HTTP-begäran |
| ClientRequestId |Sträng |Identifieraren som unikt identifierar den här begäran |
| StartTime |Sträng |Tiden då servern tog emot begäran |
| EndTime |Sträng |Tiden då servern skickade ett svar |

### <a name="audit-logs"></a>Granskningsloggar

Här är en exempel post i den JSON-formaterade gransknings loggen. Varje Blob har ett rot objekt som kallas **poster** som innehåller en matris med logg objekt.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Schema för spårningslogg

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| time |Sträng |Tids stämplingen (i UTC) för loggen |
| resourceId |Sträng |Identifieraren för resursen som åtgärden utfördes på |
| category |Sträng |Logg kategorin. Till exempel **granskning**. |
| operationName |Sträng |Namnet på den åtgärd som loggas. Till exempel JobSubmitted. |
| resultType |Sträng |En under status för jobb status (operationName). |
| resultSignature |Sträng |Ytterligare information om jobb status (operationName). |
| identity |Sträng |Användaren som begärde åtgärden. Till exempel susan@contoso.com. |
| properties |JSON |Mer information finns i nästa avsnitt (gransknings loggens egenskaps schema) |

> [!NOTE]
> **resultType** och **resultSignature** ger information om resultatet av en åtgärd och endast innehåller ett värde om en åtgärd har slutförts. De innehåller till exempel bara ett värde när **operationName** innehåller värdet **JobStarted** eller **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schema för gransknings logg egenskaper

| Namn | Typ | Beskrivning |
| --- | --- | --- |
| JobId |Sträng |Det ID som tilldelats jobbet |
| JobName |Sträng |Det namn som har angetts för jobbet |
| JobRunTime |Sträng |Körningen som används för att bearbeta jobbet |
| SubmitTime |Sträng |Den tid (i UTC) som jobbet skickades |
| StartTime |Sträng |Tiden då jobbet började köras efter överföring (i UTC-tid) |
| EndTime |Sträng |Tiden då jobbet avslutades |
| Parallellitet |Sträng |Antalet Data Lake Analytics enheter som har begärts för det här jobbet vid överföring |

> [!NOTE]
> **SubmitTime**, **StartTime** **, slut tid och** **parallellitet** ger information om en åtgärd. Dessa poster innehåller bara ett värde om åtgärden har startats eller slutförts. Till exempel innehåller **SubmitTime** endast ett värde efter att **OperationName** har värdet **JobSubmitted**.

## <a name="process-the-log-data"></a>Bearbeta loggdata

Azure Data Lake Analytics innehåller ett exempel på hur du bearbetar och analyserar loggdata. Du kan hitta exemplet på [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Nästa steg

[Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
