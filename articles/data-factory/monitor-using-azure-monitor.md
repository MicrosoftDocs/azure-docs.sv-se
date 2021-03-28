---
title: Övervaka data fabriker med hjälp av Azure Monitor
description: Lär dig hur du använder Azure Monitor för att övervaka/Azure Data Factory pipelines genom att aktivera diagnostiska loggar med information från Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 55b2fd911b32eae4f04ad9690de091e9dbd46ea4
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643970"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Övervaka och varna Data Factory med Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Moln program är komplexa och har många rörliga delar. Övervakare tillhandahåller data för att se till att dina program håller sig igång i felfritt tillstånd. Övervakare hjälper dig också att undvika potentiella problem och felsöka tidigare. Du kan använda övervaknings data för att få djupgående insikter om dina program. Den här informationen hjälper dig att förbättra programmets prestanda och hanterbarhet. Det hjälper dig också att automatisera åtgärder som annars kräver manuell åtgärd.

Azure Monitor tillhandahåller infrastruktur mått och loggar på bas nivå för de flesta Azure-tjänster. Azure Diagnostic-loggar genereras av en resurs och ger omfattande, frekventa data om driften av resursen. Azure Data Factory (ADF) kan skriva diagnostikloggar i Azure Monitor. En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Mer information finns i [Översikt av Azure Monitor](../azure-monitor/overview.md).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Behåll Azure Data Factory Mät värden och pipelinen – kör data

Data Factory lagrar pipelinen – kör data endast i 45 dagar. Använd Azure Monitor om du vill behålla dessa data under en längre tid. Med övervakaren kan du vidarebefordra diagnostikloggar för analys till flera olika mål.

* **Lagrings konto**: Spara dina diagnostikloggar till ett lagrings konto för granskning eller manuell kontroll. Du kan använda diagnostikinställningar för att ange Retentions tiden i dagar.
* **Event Hub**: strömma loggarna till Azure Event Hubs. Loggarna blir inmatade i en partner tjänst/anpassad analys lösning som Power BI.
* **Log Analytics**: analysera loggarna med Log Analytics. Data Factory-integration med Azure Monitor är användbar i följande scenarier:
  * Du vill skriva komplexa frågor på en omfattande uppsättning mått som publiceras av Data Factory som ska övervakas. Du kan skapa anpassade aviseringar för dessa frågor via övervakaren.
  * Du vill övervaka över data fabriker. Du kan dirigera data från flera data fabriker till en arbets yta för en övervakare.

Du kan också använda ett lagrings konto eller ett namn område för händelse hubb som inte finns i prenumerationen på den resurs som avger loggar. Den användare som konfigurerar inställningen måste ha rätt åtkomst kontroll för Azure-rollbaserad åtkomst kontroll till båda prenumerationerna.

## <a name="configure-diagnostic-settings-and-workspace"></a>Konfigurera diagnostikinställningar och arbets yta

Skapa eller Lägg till diagnostikinställningar för din data fabrik.

1. I portalen går du till övervaka. Välj **Inställningar**  >  **diagnostikinställningar**.

1. Välj den data fabrik som du vill ange en diagnostisk inställning för.

1. Om det inte finns några inställningar på den valda data fabriken uppmanas du att skapa en inställning. Välj **Aktivera diagnostik**.

   ![Skapa en diagnostisk inställning om inga inställningar finns](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Om det finns befintliga inställningar på data fabriken visas en lista över inställningar som redan har kon figurer ATS på data fabriken. Välj **Lägg till diagnostikinställning**.

   ![Lägg till en diagnostisk inställning om det finns inställningar](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Ange ett namn, Välj **Skicka till Log Analytics** och välj sedan en arbets yta från **Log Analytics arbets yta**.

    * I _Azure-diagnostik-_ läge flödar diagnostikloggar till _AzureDiagnostics_ -tabellen.

    * I _resurs särskilt_ läge loggar diagnostikloggar från Azure Data Factory flödet i följande tabeller:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Du kan välja olika loggar som är relevanta för dina arbets belastningar att skicka till Log Analytics tabeller. Om du till exempel inte använder SQL Server Integration Services (SSIS) alls, behöver du inte välja några SSIS-loggar. Om du vill logga SSIS Integration Runtime (IR) åtgärder för start/stopp/underhåll kan du välja SSIS IR-loggar. Om du anropar SSIS-paket med hjälp av T-SQL på SQL Server Management Studio (SSMS), SQL Server Agent eller andra särskilda verktyg, kan du välja SSIS paket loggar. Om du anropar SSIS-paket körningar via kör SSIS-paket aktiviteter i ADF-pipeline kan du välja alla loggar.

    * Om du väljer _AllMetrics_ görs olika ADF-mått som du kan använda för att övervaka eller utlösa aviseringar, inklusive mått för ADF-aktivitet, pipeline och utlösare, samt för SSIS IR-åtgärder och körning av SSIS-paket.

   ![Namnge dina inställningar och välj en arbets yta för Log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Eftersom en Azure-loggfil inte kan ha fler än 500 kolumner rekommenderar vi **starkt** att du väljer _resurs-/regionsspecifika läge_. Mer information finns i [referens för AzureDiagnostics-loggar](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Välj **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här data fabriken. Diagnostikloggar strömmas till den arbets ytan så snart som nya händelse data genereras. Upp till 15 minuter kan förflyta mellan när en händelse genereras och när den visas i Log Analytics.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installera Azure Data Factory Analytics-lösning från Azure Marketplace

Den här lösningen ger dig en översikt över övergripande hälso tillstånd för Data Factory, med alternativ för att öka detalj nivån och felsöka oväntade beteende mönster. Med hjälp av avancerade, färdiga Box-vyer kan du få insikter om nyckel bearbetning, inklusive:

* En översikt över Data Factory-pipeline, aktivitet och utlösare körs
* Möjlighet att öka detalj nivån för Data Factory-aktivitet som körs efter typ
* Översikt över den övre pipelinen för Data Factory, aktivitets fel

1. Gå till **Azure Marketplace**, Välj **analys** filter och Sök efter **Azure Data Factory Analytics (för hands version)**

   ![Gå till "Azure Marketplace", ange "Analytics filter" och välj "Azure Data Factory Analytics (Preview")](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Information om **Azure Data Factory Analytics (för hands version)**

   ![Information om "Azure Data Factory Analytics (för hands version)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Välj **skapa** och skapa eller Välj **arbets ytan Log Analytics**.

   ![Skapa en ny lösning](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Övervaka Data Factory mått

Genom att installera den här lösningen skapas en standard uppsättning vyer i avsnittet arbets böcker i den valda Log Analytics-arbetsytan. Resultatet blir att följande mått aktive ras:

* ADF-körningar-1) pipeline körs med Data Factory
* ADF-körningar – 2) aktiviteter körs genom Data Factory
* ADF-körningar-3) utlösare körs av Data Factory
* ADF-fel-1) de 10 vanligaste pipeline-felen per Data Factory
* ADF-fel – 2) topp 10-aktivitet körs genom Data Factory
* ADF-fel-3) de 10 vanligaste utlösarna fel per Data Factory
* ADF-statistik-1) aktivitets körningar efter typ
* ADF-statistik – 2) utlösare körs efter typ
* ADF-statistik-3) maximal pipeline kör varaktighet

![Fönster med "arbets böcker (förhands granskning)" och "AzureDataFactoryAnalytics" markerat](media/data-factory-monitor-oms/monitor-oms-image6.png)

Du kan visualisera föregående mått, titta på frågorna bakom dessa mått, redigera frågorna, skapa aviseringar och vidta andra åtgärder.

![Grafisk representation av pipelines körs av Data Factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (för hands version) skickar diagnostikloggar till _resursbaserade_ mål tabeller. Du kan skriva frågor mot följande tabeller: _ADFPipelineRun_, _ADFTriggerRun_ och _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Data Factory mått

Med övervakaren kan du få insyn i prestanda och hälsa för dina Azure-arbetsbelastningar. Den viktigaste typen av övervaknings data är måttet, som även kallas för prestanda räknaren. Måtten genereras av de flesta Azure-resurser. Övervakaren tillhandahåller flera olika sätt att konfigurera och använda dessa mått för övervakning och fel sökning.

Här följer några av de mått som har spridits av Azure Data Factory version 2:

| **Mått**                           | **Mått visnings namn**                  | **Enhet** | **Sammansättningstyp** | **Beskrivning**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Avbrutna aktiviteter kör mått           | Antal    | Totalt                | Det totala antalet aktivitets körningar som avbrutits inom en minut period. |
| ActivityFailedRuns                   | Misslyckad aktivitet kör mått             | Antal    | Totalt                | Det totala antalet aktivitets körningar som misslyckades inom en minut period. |
| ActivitySucceededRuns                | Genomförd aktivitet kör mått          | Antal    | Totalt                | Det totala antalet aktivitets körningar som lyckades inom ett minut fönster. |
| PipelineCancelledRuns                 | Avbruten pipeline kör mått           | Antal    | Totalt                | Det totala antalet pipelines körningar som avbrutits inom en minut period. |
| PipelineFailedRuns                   | Misslyckad pipeline kör mått             | Antal    | Totalt                | Det totala antalet pipelines körningar som misslyckats inom ett minut fönster. |
| PipelineSucceededRuns                | Slutförd pipeline kör mått          | Antal    | Totalt                | Det totala antalet pipeline-körningar som lyckades inom ett minut fönster. |
| TriggerCancelledRuns                  | Avbrutna utlösare kör mått            | Antal    | Totalt                | Det totala antalet Utlös ande körningar som avbrutits inom en minut period. |
| TriggerFailedRuns                    | Misslyckad utlösare kör mått              | Antal    | Totalt                | Det totala antalet Utlös ande körningar som misslyckats inom en minut period. |
| TriggerSucceededRuns                 | Lyckade utlösare kör mått           | Antal    | Totalt                | Det totala antalet Utlös ande körningar som lyckades inom en minut period. |
| SSISIntegrationRuntimeStartCancelled  | Start måtten för integrerings körningen av SSIS har avbrutits           | Antal    | Totalt                | Det totala antalet SSIS för integration runtime som avbrutits inom en minut period. |
| SSISIntegrationRuntimeStartFailed    | Det gick inte att starta mått för integration runtime-SSIS             | Antal    | Totalt                | Det totala antalet SSIS-integrerings körningar som inleds inom en minut period. |
| SSISIntegrationRuntimeStartSucceeded | Slutförda start mått för integration runtime-SSIS          | Antal    | Totalt                | Det totala antalet SSIS-integrerings körningar som har startats inom en minut period. |
| SSISIntegrationRuntimeStopStuck      | Stopp mått för SSIS-integration runtime               | Antal    | Totalt                | Det totala antalet SSIS för integration runtime som har fastnat i ett minut fönster. |
| SSISIntegrationRuntimeStopSucceeded  | Slutförde SSIS för integration runtime           | Antal    | Totalt                | Det totala antalet SSIS för integration runtime som lyckades inom en minut period. |
| SSISPackageExecutionCancelled         | Mät värden för körning av SSIS-paket  | Antal    | Totalt                | Det totala antalet körningar av SSIS-paket som avbrutits inom en minut period. |
| SSISPackageExecutionFailed           | Det gick inte att köra Mät värden för SSIS-paket    | Antal    | Totalt                | Totalt antal körningar av SSIS-paket som misslyckades inom en minuts period. |
| SSISPackageExecutionSucceeded        | SSIS-paketets körnings mått har slutförts | Antal    | Totalt                | Det totala antalet körningar av SSIS-paketet som lyckades inom en minut period. |

Följ anvisningarna i [Azure Monitor data plattform](../azure-monitor/data-platform.md)för att få åtkomst till måtten.

> [!NOTE]
> Endast händelser från slutförd, utlöst aktivitet och pipeline-körningar genereras. Pågår och fel söknings körningar genereras **inte** . Å andra sidan genereras händelser från **alla** SSIS-paket, inklusive de som slutförs och pågår, oavsett deras anrops metoder. Du kan till exempel aktivera paket körningar på Azure-aktiverade SQL Server Data Tools (SSDT), via T-SQL på SSMS, SQL Server Agent eller andra angivna verktyg och som Utlös ande eller fel söknings körning av kör SSIS-paket aktiviteter i ADF-pipeliner.

## <a name="data-factory-alerts"></a>Data Factory aviseringar

Logga in på Azure Portal och välj **övervaka**  >  **aviseringar** för att skapa aviseringar.

![Aviseringar på Portal-menyn](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Skapa aviseringar

1. Välj **+ ny varnings regel** för att skapa en ny avisering.

    ![Ny varnings regel](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definiera varnings villkoret.

    > [!NOTE]
    > Se till att markera **alla** i list rutan **Filtrera efter resurs typ** .

    !["Definiera aviserings villkor" > "Välj mål", som öppnar fönstret "Välj en resurs" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definiera aviserings villkor" > Lägg till kriterier, som öppnar fönstret "Konfigurera signal logik"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Fönstret "Konfigurera signal typ"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definiera aviserings informationen.

    ![Aviseringsinformation](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definiera åtgärds gruppen.

    ![Skapa en regel med "ny åtgärds grupp" markerad](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Skapa en ny åtgärds grupp](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Konfigurera e-post, SMS, push och röst](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definiera en åtgärdsgrupp](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Konfigurera diagnostikloggar via Azure Monitor REST API

### <a name="diagnostic-settings"></a>Diagnostikinställningar

Använd diagnostikinställningar för att konfigurera diagnostikloggar för icke-Compute-resurser. Inställningarna för en resurs kontroll har följande funktioner:

* De anger var diagnostikloggar ska skickas. Exempel på detta är ett Azure Storage-konto, en Azure Event Hub eller övervaknings loggar.
* De anger vilka logg kategorier som skickas.
* De anger hur länge varje logg kategori ska behållas i ett lagrings konto.
* En kvarhållning på noll dagar innebär att loggar bevaras för alltid. Annars kan värdet vara ett valfritt antal dagar från 1 till 2 147 483 647.
* Om bevarande principer har angetts men lagring av loggar i ett lagrings konto är inaktive rad, har bevarande principerna ingen påverkan. Det här tillståndet kan till exempel inträffa när endast Event Hubs eller övervaka loggar alternativ är markerade.
* Bevarande principer tillämpas per dag. Gränsen mellan dagar sker vid midnatt Coordinated Universal Time (UTC). I slutet av en dag raderas loggar från dagar som överskrider bevarande principen. Om du till exempel har en bevarande princip på en dag tas loggarna från innan igår bort i början av i dag.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Aktivera diagnostikloggar via Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Skapa eller uppdatera en diagnostisk inställning i övervakaren REST API

##### <a name="request"></a>Förfrågan

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Sidhuvuden

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ange `Content-Type` sidhuvudet till `application/json` .
* Ange Authorization-huvudet till den JSON-webbtoken som du fick från Azure Active Directory (Azure AD). Mer information finns i [autentisera begär Anden](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="body"></a>Brödtext

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Egenskap | Typ | Description |
| --- | --- | --- |
| **storageAccountId** |Sträng | Resurs-ID för det lagrings konto som du vill skicka diagnostikloggar till. |
| **serviceBusRuleId** |Sträng | Service Bus-regelns ID för det namn område för Service Bus som du vill ha Event Hubs skapat för för strömning av diagnostikloggar. Regel-ID: t har formatet `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Komplex typ | En matris med mått tids kornig het och deras bevarande principer. Egenskapens värde är tomt. |
|**mått**| Parameter värden för pipeline-körningen som ska skickas till den anropade pipelinen| Ett JSON-objekt som mappar parameter namn till argument värden. |
| **Transaktionslogg**| Komplex typ| Namnet på en diagnostisk loggnings kategori för en resurs typ. Hämta en lista med diagnostiska logg kategorier för en resurs genom att utföra en åtgärd för att hämta diagnostiska inställningar. |
| **kategori**| Sträng| En matris med logg kategorier och deras bevarande principer. |
| **timeGrain** | Sträng | Måttets granularitet, som samlas in i ISO 8601-varaktighets format. Egenskap svärdet måste vara `PT1M` , vilket anger en minut. |
| **aktiva**| Boolesk | Anger om insamlingen av mått-eller logg kategori har Aktiver ATS för den här resursen. |
| **Retention Policy**| Komplex typ| Beskriver bevarande principen för en mått-eller logg kategori. Den här egenskapen används endast för lagrings konton. |
|**antalet**| Int| Antalet dagar att behålla måtten eller loggarna. Om egenskap svärdet är 0 sparas loggarna alltid. Den här egenskapen används endast för lagrings konton. |

##### <a name="response"></a>Svarsåtgärder

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Hämta information om diagnostikinställningar i övervakaren REST API

##### <a name="request"></a>Förfrågan

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Sidhuvuden

* Ersätt `{api-version}` med `2016-09-01`.
* Ersätt `{resource-id}` med ID för den resurs som du vill redigera diagnostikinställningar för. Mer information finns i [använda resurs grupper för att hantera dina Azure-resurser](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Ange `Content-Type` sidhuvudet till `application/json` .
* Ange Authorization-huvudet till en JSON-webbtoken som du fick från Azure AD. Mer information finns i [autentisera begär Anden](../active-directory/develop/authentication-vs-authorization.md).

##### <a name="response"></a>Svarsåtgärder

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Mer information finns i [diagnostikinställningar](/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema för loggar och händelser

### <a name="monitor-schema"></a>Övervaka schema

#### <a name="activity-run-log-attributes"></a>Aktivitet-kör logg-attribut

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet i TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Sträng| ID för aktivitets körningen. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Sträng| ID för pipeline-körningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `ActivityRuns` . | `ActivityRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational` . | `Informational` |
|**operationName**| Sträng | Namnet på aktiviteten med dess status. Om aktiviteten är start pulsslaget är egenskap svärdet `MyActivity -` . Om aktiviteten är slut pulsslaget är egenskap svärdet `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| Sträng | Namnet på pipelinen. | `MyPipeline` |
|**activityName**| Sträng | Aktivitetens namn. | `MyActivity` |
|**har**| Sträng | Start tiden för aktiviteten körs i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**ändamål**| Sträng | Slut tiden för aktiviteten körs i TimeSpan UTC-format. Om Diagnostic-loggen visar att en aktivitet har startat men ännu inte har avslut ATS, är egenskap svärdet `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Pipeline-kör logg-attribut

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet i TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Sträng| ID för pipeline-körningen. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `PipelineRuns` . | `PipelineRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational` . | `Informational` |
|**operationName**| Sträng | Namnet på pipelinen tillsammans med dess status. När pipeline-körningen är färdig är egenskap svärdet `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| Sträng | Namnet på pipelinen. | `MyPipeline` |
|**har**| Sträng | Start tiden för aktiviteten körs i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`. |
|**ändamål**| Sträng | Slut tiden för aktiviteten körs i TimeSpan UTC-format. Om Diagnostic-loggen visar att en aktivitet har startat men ännu inte har avslut ATS, är egenskap svärdet `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**statusfältet**| Sträng | Slut status för pipeline-körningen. Möjliga egenskaps värden är `Succeeded` och `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Utlös läge-körning av log-attribut

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Egenskap | Typ | Beskrivning | Exempel |
| --- | --- | --- | --- |
| **Nivå** |Sträng | Nivån för diagnostikloggar. För aktivitets körnings loggar anger du egenskap svärdet 4. | `4` |
| **correlationId** |Sträng | Unikt ID för spårning av en viss begäran. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **tid** | Sträng | Tiden för händelsen i UTC-formatet i TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Sträng| ID: t för utlösarens körning. | `08587023010602533858661257311` |
|**resourceId**| Sträng | Det ID som är kopplat till Data Factory-resursen. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**kategori**| Sträng | Kategorin för diagnostikloggar. Ange egenskapens värde till `PipelineRuns` . | `PipelineRuns` |
|**nivå**| Sträng | Nivån för diagnostikloggar. Ange egenskapens värde till `Informational` . | `Informational` |
|**operationName**| Sträng | Namnet på utlösaren med dess slutgiltiga status, som anger om utlösaren har utlösts. Om pulsslaget lyckades är egenskap svärdet `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Sträng | Namnet på utlösaren. | `MyTrigger` |
|**triggerType**| Sträng | Typ av utlösare. Möjliga egenskaps värden är `Manual Trigger` och `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Sträng | Händelse av utlösaren. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**har**| Sträng | Start tiden för utlösaren utlöses i TimeSpan UTC-format. | `2017-06-26T20:55:29.5007959Z`|
|**statusfältet**| Sträng | Slutgiltig status visar om utlösaren har utlösts. Möjliga egenskaps värden är `Succeeded` och `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS för integration runtime

Här följer SSIS för IR-åtgärder för start/stopp/underhåll.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Egenskap                   | Typ   | Beskrivning                                                   | Exempel                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **tid**                   | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sträng | Namnet på din SSIS-IR-åtgärd                            | `Start/Stop/Maintenance` |
| **kategori**               | Sträng | Kategorin för diagnostikloggar                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | Sträng | Unikt ID för att spåra en viss åtgärd             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Sträng | Namnet på din ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | Sträng | Namnet på din SSIS-IR                                      | `MySSISIR` |
| **nivå**                  | Sträng | Nivån för diagnostikloggar                                  | `Informational` |
| **resultType**             | Sträng | Resultatet av din SSIS-IR-åtgärd                          | `Started/InProgress/Succeeded/Failed` |
| **meddelande**                | Sträng | Utmatnings meddelandet för din SSIS IR-åtgärd                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | Sträng | Unikt ID för din ADF-resurs                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS för händelse meddelandets kontext

Här är de loggnings attributen för villkor som rör händelse meddelanden som genereras av SSIS-paket körningar på din SSIS IR. De förmedlar liknande information som [SSIS-katalog (SSISDB) händelse meddelande kontext tabell eller vy](/sql/integration-services/system-views/catalog-event-message-context) som visar körnings värden för många SSIS-paket egenskaper. De genereras när du väljer `Basic/Verbose` loggnings nivå och användbart för fel sökning/kompatibilitetskontroll.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Egenskap                   | Typ   | Beskrivning                                                          | Exempel                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **tid**                   | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sträng | Detta är inställt på `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **kategori**               | Sträng | Kategorin för diagnostikloggar                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | Sträng | Unikt ID för att spåra en viss åtgärd                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sträng | Namnet på din ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | Sträng | Namnet på din SSIS-IR                                             | `MySSISIR` |
| **nivå**                  | Sträng | Nivån för diagnostikloggar                                         | `Informational` |
| **operationId**            | Sträng | Unikt ID för att spåra en viss åtgärd i SSISDB          | `1` (1 betyder åtgärder relaterade till paket som **inte** lagras i SSISDB/anropas via T-SQL) |
| **contextDepth**           | Sträng | Djupet i händelse meddelande kontexten                              | `0` (0 anger kontexten innan paket körningen börjar, 1 anger kontexten när ett fel inträffar, och det ökar när kontexten är ytterligare från felet) |
| **packagePath**            | Sträng | Sökvägen till paket objekt som händelse meddelandets kontext källa      | `\Package` |
| **contextType**            | Sträng | Typ av paket objekt som kontext källa för händelse meddelande      | `60`(se [fler kontext typer](/sql/integration-services/system-views/catalog-event-message-context#remarks)) |
| **contextSourceName**      | Sträng | Namnet på paket objekt som kontext källa för händelse meddelande      | `MyPackage` |
| **contextSourceId**        | Sträng | Unikt ID för paket objekt som kontext källa för händelse meddelande | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **Namn**           | Sträng | Namnet på paket egenskapen för händelse meddelandets kontext källa   | `DelayValidation` |
| **propertyValue**          | Sträng | Värdet för egenskapen Package för händelse meddelandets kontext källa  | `False` |
| **resourceId**             | Sträng | Unikt ID för din ADF-resurs                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS Event Messages log attribut

Här är de logg-attribut för händelse meddelanden som genereras av SSIS-paket körningar på din SSIS IR. De förmedlar liknande information som [SSISDB händelse meddelande tabell eller vy](/sql/integration-services/system-views/catalog-event-messages) som visar detaljerad text/metadata för händelse meddelanden. De genereras på alla loggnings nivåer utom `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Egenskap                   | Typ   | Beskrivning                                                        | Exempel                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **tid**                   | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sträng | Detta är inställt på `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **kategori**               | Sträng | Kategorin för diagnostikloggar                                    | `SSISPackageEventMessages` |
| **correlationId**          | Sträng | Unikt ID för att spåra en viss åtgärd                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sträng | Namnet på din ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | Sträng | Namnet på din SSIS-IR                                           | `MySSISIR` |
| **nivå**                  | Sträng | Nivån för diagnostikloggar                                       | `Informational` |
| **operationId**            | Sträng | Unikt ID för att spåra en viss åtgärd i SSISDB        | `1` (1 betyder åtgärder relaterade till paket som **inte** lagras i SSISDB/anropas via T-SQL) |
| **messageTime**            | Sträng | Tiden då ditt händelse meddelande skapas i UTC-format          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | Sträng | Typ av händelse meddelande                                     | `70`(se [fler meddelande typer](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **messageSourceType**      | Sträng | Typ av händelse meddelande källa                              | `20`(se [fler typer av meddelande källor](/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database#remarks)) |
| **meddelande**                | Sträng | Texten för ditt händelse meddelande                                     | `MyPackage:Validation has started.` |
| **packageName**            | Sträng | Namnet på den körda paket filen                             | `MyPackage.dtsx` |
| **eventName**              | Sträng | Namnet på relaterad körnings händelse                                 | `OnPreValidate` |
| **messageSourceName**      | Sträng | Namnet på paket komponenten som källa för händelse meddelande         | `Data Flow Task` |
| **messageSourceId**        | Sträng | Unikt ID för paket komponenten som källa för händelse meddelande    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | Sträng | Namnet på data flödes komponenten som din händelse meddelande källa       | `SSIS.Pipeline` |
| **packagePath**            | Sträng | Sökvägen till paket objekt som din händelse meddelande källa            | `\Package\Data Flow Task` |
| **executionPath**          | Sträng | Den fullständiga sökvägen från det överordnade paketet till komponenten som körs            | `\Transformation\Data Flow Task` (Den här sökvägen fångar även komponent iterationer) |
| **threadId**               | Sträng | Unikt ID för tråden som körs när ditt händelse meddelande loggas | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS körbar statistik logg attribut

Här är de loggfiler för körbar statistik som genereras av SSIS-paket körningar på din SSIS IR, där körbara filer är behållare eller uppgifter i paketets kontroll flöde. De förmedlar liknande information som [SSISDB-körbara statistik tabell eller vy](/sql/integration-services/system-views/catalog-executable-statistics) som visar en rad för varje körbar fil som körs, inklusive dess iterationer. De genereras på loggnings nivå förutom `None` och är användbara för att identifiera Flask halsar/haverier på uppgifts nivå.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Egenskap                   | Typ   | Beskrivning                                                      | Exempel                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **tid**                   | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sträng | Detta är inställt på `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **kategori**               | Sträng | Kategorin för diagnostikloggar                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | Sträng | Unikt ID för att spåra en viss åtgärd                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sträng | Namnet på din ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | Sträng | Namnet på din SSIS-IR                                         | `MySSISIR` |
| **nivå**                  | Sträng | Nivån för diagnostikloggar                                     | `Informational` |
| **executionId frågeparameter**            | Sträng | Unikt ID för att spåra en viss körning i SSISDB      | `1` (1 indikerar körningar relaterade till paket som **inte** lagras i SSISDB/som anropas via T-SQL) |
| **executionPath**          | Sträng | Den fullständiga sökvägen från det överordnade paketet till komponenten som körs          | `\Transformation\Data Flow Task` (Den här sökvägen fångar även komponent iterationer) |
| **/St**              | Sträng | Tiden då den körbara filen går in i UTC-format  | `2017-06-28T21:00:27.3534352Z` |
| **Slut**                | Sträng | Tiden då den körbara filen anges efter körning i UTC-format | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Sträng | Körnings tiden för den körbara filen i millisekunder                   | `1,125` |
| **executionResult**        | Sträng | Resultatet av att köra körbara filer                                 | `0` (0 betyder att det går att slutföra, 1 betyder att det är slut, 2 betyder slut för ande och 3 anger att annulleringen ska avbrytas) |
| **executionValue**         | Sträng | Det användardefinierade värdet som returnerades genom att köra körbar fil            | `1` |
| **resourceId**             | Sträng | Unikt ID för din ADF-resurs                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS för komponent faser för körning

Här följer logg attributen för körnings statistik för data flödes komponenter som genereras av SSIS-paket körningar på din SSIS IR. De förmedlar liknande information som [SSISDB i faser eller vyer för körnings komponenter](/sql/integration-services/system-views/catalog-execution-component-phases) som visar den tid som data flödes komponenterna ägnat i alla körnings faser. De genereras när du väljer `Performance/Verbose` loggnings nivå och användbart för att hämta statistik för körning av data flöden.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Egenskap                   | Typ   | Beskrivning                                                         | Exempel                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **tid**                   | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sträng | Detta är inställt på `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **kategori**               | Sträng | Kategorin för diagnostikloggar                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | Sträng | Unikt ID för att spåra en viss åtgärd                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sträng | Namnet på din ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | Sträng | Namnet på din SSIS-IR                                            | `MySSISIR` |
| **nivå**                  | Sträng | Nivån för diagnostikloggar                                        | `Informational` |
| **executionId frågeparameter**            | Sträng | Unikt ID för att spåra en viss körning i SSISDB         | `1` (1 indikerar körningar relaterade till paket som **inte** lagras i SSISDB/som anropas via T-SQL) |
| **packageName**            | Sträng | Namnet på den körda paket filen                              | `MyPackage.dtsx` |
| **/TN**               | Sträng | Namnet på uppgiften körd data flöde                                 | `Data Flow Task` |
| **subcomponentName**       | Sträng | Namnet på data flödes komponenten                                     | `Derived Column` |
| **fasa**                  | Sträng | Körnings fasens namn                                         | `AcquireConnections` |
| **/St**              | Sträng | Tiden då körnings fasen startar i UTC-format                  | `2017-06-28T21:00:27.3534352Z` |
| **Slut**                | Sträng | Tiden då körnings fasen slutar i UTC-format                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Sträng | Sökväg för körning av data flödes uppgift                            | `\Transformation\Data Flow Task` |
| **resourceId**             | Sträng | Unikt ID för din ADF-resurs                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS för körnings data statistik

Här följer de Säkerhetsattributen för data förflyttningar genom varje steg i pipeline för data flöden, från överordnade till efterföljande komponenter, som genereras av SSIS-paket körningar på din SSIS IR. De förmedlar liknande information som [SSISDB för körnings data statistik eller vy](/sql/integration-services/system-views/catalog-execution-data-statistics) som visar rad antal data som flyttats genom data flödes aktiviteter. De genereras när du väljer `Verbose` loggnings nivå och användbart för data flödes data flöde.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Egenskap                     | Typ   | Beskrivning                                                        | Exempel                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **tid**                     | Sträng | Tiden för händelsen i UTC-format: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Sträng | Detta är inställt på `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **kategori**                 | Sträng | Kategorin för diagnostikloggar                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | Sträng | Unikt ID för att spåra en viss åtgärd                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Sträng | Namnet på din ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | Sträng | Namnet på din SSIS-IR                                           | `MySSISIR` |
| **nivå**                    | Sträng | Nivån för diagnostikloggar                                       | `Informational` |
| **executionId frågeparameter**              | Sträng | Unikt ID för att spåra en viss körning i SSISDB        | `1` (1 indikerar körningar relaterade till paket som **inte** lagras i SSISDB/som anropas via T-SQL) |
| **packageName**              | Sträng | Namnet på den körda paket filen                             | `MyPackage.dtsx` |
| **/TN**                 | Sträng | Namnet på uppgiften körd data flöde                                | `Data Flow Task` |
| **dataflowPathIdString**     | Sträng | Unikt ID för spårning av data flödes Sök väg                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Sträng | Namnet på data flödets sökväg                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Sträng | Namnet på data flödes komponenten som skickar data                    | `SQLDB Table3` |
| **destinationComponentName** | Sträng | Namnet på den data flödes komponent som tar emot data                 | `Derived Column` |
| **rowsSent**                 | Sträng | Antalet rader som skickats av käll komponenten                        | `500` |
| **createdTime**              | Sträng | Tiden då rad värden hämtas i UTC-format                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Sträng | Sökväg för körning av data flödes uppgift                           | `\Transformation\Data Flow Task` |
| **resourceId**               | Sträng | Unikt ID för din ADF-resurs                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics schema

Log Analytics ärver schemat från övervakaren med följande undantag:

* Den första bokstaven i varje kolumn namn är kapitaliserad. Kolumn namnet "correlationId" i övervakaren är till exempel "CorrelationId" i Log Analytics.
* Det finns ingen "nivå"-kolumn.
* Den dynamiska "Properties"-kolumnen bevaras som följande dynamiska JSON-Blob-typ.

    | Azure Monitor kolumn | Log Analytics kolumn | Typ |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamisk |
    | $. Properties. Anteckningar | Anteckningar | Dynamisk |
    | $. Properties. Inleveranstransport | Indata | Dynamisk |
    | $. Properties. Utdataparametrar | Utdata | Dynamisk |
    | $. Properties. Fel. felkod | Felkod | int |
    | $. Properties. Fel. meddelande | ErrorMessage | sträng |
    | $. Properties. Fels | Fel | Dynamisk |
    | $. Properties. Föregångarna | Föregångarna | Dynamisk |
    | $. Properties. Komponentparametrar | Parametrar | Dynamisk |
    | $.properties.SystemParameters | SystemParameters | Dynamisk |
    | $. Properties. Taggen | Taggar | Dynamisk |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Övervaka SSIS-åtgärder med Azure Monitor

Om du vill lyfta & flytta dina SSIS-arbetsbelastningar kan du [etablera SSIS IR i ADF](./tutorial-deploy-ssis-packages-azure.md) som stöder:

- Köra paket som distribuerats i SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell)
- Köra paket som distribuerats i fil systemet, Azure Files eller SQL Server databas (MSDB) som hanteras av Azure SQL-hanterad instans (paket distributions modell)

När du har allokerat kan du [kontrol lera SSIS IR-drift status med Azure PowerShell eller på **Monitor** Hub på ADF-portalen](./monitor-integration-runtime.md#azure-ssis-integration-runtime). Med projekt distributions modell lagras SSIS-paket körnings loggar i SSISDB interna tabeller eller vyer, så att du kan fråga, analysera och visuellt presentera dem med hjälp av angivna verktyg som SSMS. Med paket distributions modell kan SSIS-paketets körnings loggar lagras i fil systemet eller Azure Files som CSV-filer som du fortfarande behöver parsa och bearbeta med andra angivna verktyg innan du kan fråga, analysera och visuellt presentera dem.

Nu med [Azure Monitor](../azure-monitor/data-platform.md) -integrering kan du fråga, analysera och visuellt presentera alla mått och loggar som genereras från SSIS IR-åtgärder och SSIS-paket körningar på Azure Portal. Dessutom kan du också generera aviseringar på dem.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Konfigurera diagnostikinställningar och arbets yta för SSIS-åtgärder

Om du vill skicka alla mått och loggar som genereras från SSIS IR-åtgärder och SSIS-paket körningar till Azure Monitor måste du [Konfigurera diagnostikinställningar och arbets yta för din ADF](#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>SSIS drifts mått

SSIS drifts [mått](../azure-monitor/essentials/data-platform-metrics.md) är prestanda räknare eller numeriska värden som beskriver STATUSEN för IR-start och stopp åtgärder i SSIS, samt SSIS-programpaket vid en viss tidpunkt. De är en del av [ADF-mått i Azure Monitor](#data-factory-metrics).

När du konfigurerar diagnostikinställningar och arbets yta för din ADF på Azure Monitor, kan du markera kryss rutan _AllMetrics_ om du vill att SSIS operativa måtten ska vara tillgängliga för [interaktiv analys med Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md), [presentation på Azure Dashboard](../azure-monitor/app/tutorial-app-dashboards.md)och [nästan real tids aviseringar](../azure-monitor/alerts/alerts-metric.md).

![Namnge dina inställningar och välj en arbets yta för Log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS-operativa aviseringar

Om du vill generera aviseringar för SSIS-operativa mått från ADF-portalen [väljer du sidan **aviseringar & mått** i ADF **Monitor** Hub och följer de stegvisa anvisningarna](./monitor-visually.md#alerts).

![Höja SSIS-operativa aviseringar från ADF-portalen](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Om du vill generera aviseringar för SSIS-operativa mått från Azure Portal, [väljer du sidan **aviseringar** i Azure **Monitor** Hub och följer de stegvisa anvisningarna](#data-factory-alerts).

![Att öka SSIS-aviseringar från Azure Portal](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS operativa loggar

SSIS operativa [loggar](../azure-monitor/logs/data-platform-logs.md) är händelser som genereras av SSIS IR-åtgärder och körningar av SSIS-paket som ger tillräckligt med kontext på identifierade problem och är användbara för rotor Saks analys. 

När du konfigurerar diagnostikinställningar och arbets yta för din ADF på Azure Monitor, kan du välja relevanta SSIS-operativa loggar och skicka dem till Log Analytics som baseras på Azure Datautforskaren. I där kommer de att göras tillgängliga för [analys med hjälp av omfattande frågespråk](../azure-monitor/logs/log-query-overview.md), [presentation på Azure-instrumentpanelen och i](../azure-monitor/app/tutorial-app-dashboards.md) [nära real tid](../azure-monitor/alerts/alerts-log.md).

![Namnge dina inställningar och välj en arbets yta för Log Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

Scheman och innehåll för körnings loggar för SSIS-paket i Azure Monitor och Log Analytics liknar scheman för SSISDB interna tabeller eller vyer.

| Azure Monitor logg kategorier          | Log Analytics tabeller                     | SSISDB interna tabeller/vyer              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Mer information om SSIS-attribut/egenskaper för drifts loggning finns i [Azure Monitor och Log Analytics scheman för ADF](#schema-of-logs-and-events).

De valda SSIS-paketets körnings loggar skickas alltid till Log Analytics oavsett vilka metoder som anropas. Du kan till exempel aktivera paket körningar på Azure-aktiverade SSDT, via T-SQL på SSMS, SQL Server Agent eller andra angivna verktyg och som Utlös ande eller fel söknings körning av kör SSIS-paket aktiviteter i ADF-pipeline.

När du frågar efter SSIS IR-åtgärd i loggar analyser kan du använda **OperationName** -och **ResultType** -egenskaper som är inställda på respektive `Start/Stop/Maintenance` `Started/InProgress/Succeeded/Failed` . 

![Frågar efter SSIS IR-åtgärds loggar på Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

När du frågar efter SSIS-paketets körnings loggar i loggar analyser kan du koppla dem med hjälp av **OperationId** / **executionId frågeparameter** / **correlationId** -egenskaper. **OperationId** / **ExecutionId frågeparameter** är alltid inställda på `1` för alla åtgärder/körningar relaterade till paket som **inte** lagras i SSISDB/som anropas via T-SQL.

![Fråga om körnings loggar för SSIS-paket på Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Nästa steg
[Övervaka och hantera pipelines program mässigt](monitor-programmatically.md)
