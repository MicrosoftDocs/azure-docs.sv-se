---
title: Så här övervakar du Azure-avdelningens kontroll
description: Lär dig hur du konfigurerar Azure avdelningens kontroll-mått, varningar och diagnostikinställningar med hjälp av Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101667739"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Azure avdelningens kontroll-mått i Azure Monitor

Den här artikeln beskriver hur du konfigurerar mått, aviseringar och diagnostikinställningar för Azure-avdelningens kontroll med hjälp av Azure Monitor.

## <a name="monitor-azure-purview"></a>Övervaka Azure-avdelningens kontroll

Azure avdelningens kontroll-administratörer kan använda Azure Monitor för att spåra drift status för avdelningens kontroll-konto. Mått samlas in för att tillhandahålla data punkter för att spåra potentiella problem, felsöka och förbättra tillförlitligheten för avdelningens kontroll-kontot. Måtten skickas till Azure Monitor för händelser som inträffar i Azure avdelningens kontroll.

## <a name="aggregated-metrics"></a>Sammanställda mått

Måtten kan nås från Azure Portal för ett avdelningens kontroll-konto. Åtkomst till måtten styrs av roll tilldelningen för avdelningens kontroll-kontot. Användarna måste vara en del av rollen "övervaknings läsare" i Azure avdelningens kontroll för att se måtten. Kolla in [övervaknings läsar roll behörigheter](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) för att lära dig mer om åtkomst nivåerna för roller.

Den person som skapade avdelningens kontroll-kontot får automatiskt behörighet att visa mått. Om någon annan vill se måtten lägger du till dem i **övervaknings läsar** rollen genom att följa dessa steg:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Lägga till en användare i rollen övervaknings läsare

Om du vill lägga till en användare i **övervaknings läsar** rollen kan ägaren av avdelningens kontroll-kontot eller prenumerations ägaren följa dessa steg:

1. Gå till [Azure Portal](https://portal.azure.com) och Sök efter namnet på Azure avdelningens kontroll-kontot.

2. Välj **Åtkomstkontroll (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Skärm bild som visar hur du får åtkomst till IAM.":::

3. Välj **Lägg till en roll tilldelning**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Skärm bild som visar hur du lägger till roll tilldelning.":::

4. Välj roll **övervaknings läsaren** och ange tilldela åtkomst till **Azure AD-användare,-grupp eller tjänstens huvud namn**. Och tilldela AAD-kontot åtkomst till måtten.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Skärm bild som visar hur du lägger till övervaknings läsar rollen.":::

## <a name="metrics-visualization"></a>Mått visualisering

Användare i rollen **övervaknings läsare** kan se de sammanställda måtten och diagnostikloggar som skickas till Azure Monitor. Måtten visas i Azure Portal för motsvarande avdelningens kontroll-konto. I Azure Portal väljer du avsnittet mått om du vill se en lista över alla tillgängliga mått.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Skärm bild som visar avsnittet tillgängliga avdelningens kontroll-mått." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure avdelningens kontroll-användare kan också komma åt sidan mått direkt från hanterings centret för Azure avdelningens kontroll-kontot. Välj Azure Monitor på huvud sidan i avdelningens kontroll Management Center för att starta Azure Portal.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Skärm bild för att starta avdelningens kontroll-mått från Management Center." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Tillgängliga mått

För att bekanta dig med hur du använder mått avsnittet i Azure Portal för att läsa följande två dokument. [Komma igång med Metric Explorer](../azure-monitor/essentials/metrics-getting-started.md) och [avancerade funktioner i Metric Explorer](../azure-monitor/essentials/metrics-charts.md).

Följande tabell innehåller en lista över mått som är tillgängliga för att utforska i Azure Portal:

| Måttnamn | Namnrymd för mått | Sammansättningstyp | Beskrivning |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Skanningen avbröts | Automatiserad sökning | Sum <br> Antal | Sammanställ inläsningar av avbrutna data källor över tids period |
| Sökningen är klar | Automatiserad sökning | Sum <br> Antal | Sammanställ slutförda data käll genomsökningar över en tids period |
| Sökningen misslyckades | Automatiserad sökning | Sum <br> Antal | Sammanställ inläsningar av misslyckade data källor över tids period |
| Tids åtgång för genomsökning | Automatiserad sökning | Min <br> Max <br> Sum <br> Genomsnitt | Sammanställa den totala tiden det tar för genomsökningar över tids perioden |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Diagnostikloggar till Azure Storage konto

Händelser för rå telemetri genereras till Azure Monitor. Händelser kan loggas till ett kund lagrings konto som du väljer för ytterligare analys. Export av loggar görs via diagnostikinställningar för avdelningens kontroll-kontot på Azure Portal.

Följ stegen för att skapa en diagnostisk inställning för ditt Azure avdelningens kontroll-konto.

1. Skapa en ny diagnostisk inställning för att samla in plattforms loggar och-mått genom att följa den här artikeln: [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/essentials/diagnostic-settings.md). Välj endast målet som Azure Storage-konto.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Skärm bild som visar hur du skapar en diagnostisk logg." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Logga händelserna till ett lagrings konto. Ett dedikerat lagrings konto rekommenderas för arkivering av diagnostikloggar. Följ den här artikeln för att [skapa ett lagrings konto](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Skärm bild som visar tilldelning av lagrings konto för diagnostikloggar." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Låt upp till 15 minuter innan du börjar ta emot loggar i det nyligen skapade lagrings kontot. [Se data kvarhållning och schema för resurs loggar i Azure Storage konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). När diagnostikloggar har kon figurer ATS, kommer händelserna att flöda till lagrings kontot.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Händelsen spårar livs cykeln för genomsökning. En skannings åtgärd följer förloppet genom en sekvens med tillstånd, från kö, körs och slutligen Terminal Services lyckades | Misslyckades | Avbrutits. En händelse loggas för varje tillstånds över gång och schema för händelsen har följande egenskaper.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Exempel loggen för en händelse instans visas i avsnittet nedan.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Nästa steg

[Visa till gångs insikter](asset-insights.md)