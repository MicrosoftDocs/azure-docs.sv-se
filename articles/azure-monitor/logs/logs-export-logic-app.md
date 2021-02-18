---
title: Arkivera data från Log Analytics arbets yta till Azure Storage med hjälp av Logic app
description: Beskriver en metod för att använda Azure Logic Apps för att fråga efter data från en Log Analytics arbets yta och skicka till Azure Storage.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: 040c487df83c117e177b8a8b0e8fddde8682c67f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621670"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Arkivera data från Log Analytics arbets yta till Azure Storage med hjälp av Logic app
Den här artikeln beskriver en metod för att använda [Azure Logic Apps](../../logic-apps/index.yml) för att fråga efter data från en Log Analytics arbets yta i Azure Monitor och skicka till Azure Storage. Använd den här processen när du behöver exportera Azure Monitor loggdata för scenarier för granskning och efterlevnad, eller om du vill tillåta en annan tjänst att hämta dessa data.  

## <a name="other-export-methods"></a>Andra export metoder
Metoden som beskrivs i den här artikeln beskriver en schemalagd export från en logg fråga med hjälp av en Logic app. Andra alternativ för att exportera data för specifika scenarier är följande:

- Om du vill exportera alla data från din Log Analytics-arbetsyta till ett Azure Storage-konto eller en händelsehubben, använder du data export funktionen Log Analytics arbets yta för Azure Monitor loggar. Se [Log Analytics data export för arbets ytan i Azure Monitor (för hands version)](logs-data-export.md)
- Exportera med hjälp av en logisk app. Se [Azure Monitor logs Connector för Logic Apps och energi automatisering](logicapp-flow-connector.md).
- Exportera till en lokal dator med hjälp av PowerShell-skript. Se [Invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Översikt
I den här proceduren används [Azure Monitor loggar Connector](/connectors/azuremonitorlogs/) som gör att du kan köra en logg fråga från en Logic app och använda dess utdata i andra åtgärder i arbets flödet. [Azure Blob Storage-anslutningen](/connectors/azureblob/) används i den här proceduren för att skicka frågeresultatet till Azure Storage. De andra åtgärderna beskrivs i avsnitten nedan.

![Översikt över Logic app](media/logs-export-logic-app/logic-app-overview.png)

När du exporterar data från en Log Analytics arbets yta bör du filtrera och aggregera dina loggdata och optimera din fråga för att begränsa mängden data som bearbetas av ditt Logic app-arbetsflöde till nödvändiga data. Om du till exempel behöver arkivera inloggnings händelser kan du filtrera efter nödvändiga händelser och endast projicera de obligatoriska fälten med följande fråga: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

När du exporterar data enligt ett schema, använder du funktionen ingestion_time () i din fråga för att se till att du inte är missa sent inkommande data. Om data fördröjs på grund av nätverks-eller plattforms problem säkerställer det att den tas med i nästa Logic app-körning. Se [Lägg till Azure Monitor loggar åtgärd](#add-azure-monitor-logs-action) för ett exempel.

## <a name="prerequisites"></a>Förutsättningar
Följande är förutsättningar som måste slutföras innan du slutför den här proceduren.

- Log Analytics arbets yta. Den användare som skapar Logic-appen måste ha minst Läs behörighet till arbets ytan. 
- Azure Storage-konto. Lagrings kontot måste inte finnas i samma prenumeration som din Log Analytics-arbetsyta. Den användare som skapar Logic app måste ha Skriv behörighet till lagrings kontot. 


## <a name="connector-limits"></a>Kopplings gränser
Log Analytics arbets ytor och logg frågor i Azure Monitor är flera hyres tjänster som omfattar gränser som skyddar och isolerar kunder och underhåller tjänst kvalitet. När du frågar efter en stor mängd data bör du tänka på följande gränser, som kan påverka hur du konfigurerar den återkommande Logic app och logg frågan:

- Logg frågor får inte returnera mer än 500 000 rader.
- Logg frågor får inte returnera mer än 64 000 000 byte.
- Logg frågor får inte köras mer än 10 minuter som standard. 
- Log Analytics Connector är begränsat till 100 samtal per minut.


## <a name="create-container-in-the-storage-account"></a>Skapa behållare i lagrings kontot
Använd proceduren i [skapa en behållare](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) för att lägga till en behållare till ditt lagrings konto för att lagra exporterade data. Namnet som används för behållaren i den här artikeln är **loganalytics-data**, men du kan använda vilket namn som helst.


## <a name="create-logic-app"></a>Skapa en logikapp

Gå till **Logic Apps** i Azure Portal och klicka på **Lägg till**. Välj en **prenumeration**, en **resurs grupp** och en **region** för att lagra den nya Logic-appen och ge den ett unikt namn. Du kan aktivera **Log Analytics** inställningen för att samla in information om körnings data och händelser enligt beskrivningen i [Konfigurera Azure Monitor loggar och samla in diagnostikdata för Azure Logic Apps](../../logic-apps/monitor-logic-apps-log-analytics.md). Den här inställningen krävs inte för att använda anslutnings programmet för Azure Monitor-loggar.

![Skapa en logikapp](media/logs-export-logic-app/create-logic-app.png)


Klicka på **Granska + skapa** och sedan på **skapa**. När distributionen är klar klickar du på **gå till resurs** för att öppna **Logic Apps designer**.

## <a name="create-a-trigger-for-the-logic-app"></a>Skapa en utlösare för Logic app
Under **börja med en gemensam utlösare** väljer du **upprepning**. Detta skapar en logisk app som körs automatiskt med jämna mellanrum. I rutan **frekvens** för åtgärden väljer du **dag** och i rutan **intervall** anger du **1** för att köra arbets flödet en gång per dag.

![Upprepnings åtgärd](media/logs-export-logic-app/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor loggar
Klicka på **+ nytt steg** för att lägga till en åtgärd som körs efter upprepnings åtgärden. Under **Välj en åtgärd**, Skriv **Azure Monitor** och välj sedan **Azure Monitor loggar**.

![Åtgärd för Azure Monitor loggar](media/logs-export-logic-app/select-azure-monitor-connector.png)

Klicka på **Azure Log Analytics – kör frågor och lista resultat**.

![Skärm bild av en ny åtgärd som läggs till i ett steg i Logic Apps designer. Azure Monitor loggar markeras under Välj en åtgärd.](media/logs-export-logic-app/select-query-action-list.png)

Du uppmanas att välja en klient och bevilja åtkomst till Log Analytics arbets ytan med det konto som arbets flödet ska använda för att köra frågan.


## <a name="add-azure-monitor-logs-action"></a>Åtgärden Lägg till Azure Monitor loggar
Med åtgärden Azure Monitor loggar kan du ange vilken fråga som ska köras. Logg frågan som används i det här exemplet är optimerad för varje timme och samlar in data som matas in för den specifika körnings tiden. Om arbets flödet till exempel körs vid 4:35 blir tidsintervallet 4:00 till 5:00. Om du ändrar Logic-appen så att den körs med en annan frekvens behöver du även ändra frågan. Om du till exempel ställer in upprepningen på att köras dagligen ställer du in StartTime i frågan på startofday (make_datetime (år, månad, dag, 0, 0)). 

Välj **prenumerationen** och **resurs gruppen** för din Log Analytics-arbetsyta. Välj *Log Analytics arbets yta* för **resurs typen** och välj sedan namnet på arbets ytan under **resurs namn**.

Lägg till följande logg fråga i **frågefönstret** .  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

**Tidsintervallet** anger de poster som ska ingå i frågan baserat på kolumnen **TimeGenerated** . Detta ska vara inställt på ett värde som är lika med eller högre än det tidsintervall som valts i frågan. Eftersom den här frågan inte använder **TimeGenerated** -kolumnen är **Ange i frågealternativet** inte tillgängligt. Se [omfånget för frågor](../log-query/scope.md) om du vill ha mer information om tidsintervallet. 

Välj **senaste 4 timmarna** för **tidsintervallet**. På så sätt ser du till att alla poster med en större inmatnings tid än **TimeGenerated** tas med i resultatet.
   
![Skärm bild av inställningarna för åtgärden ny Azure Monitor loggar med namnet kör fråga och visualisera resultat.](media/logs-export-logic-app/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Lägg till parsa JSON-aktivitet (valfritt)
Utdata från åtgärden **Kör fråga och lista resultat** är FORMATERAD i JSON. Du kan parsa dessa data och hantera dem som en del av åtgärden förberedelse av **Skriv** åtgärder. 

Du kan ange ett JSON-schema som beskriver den nytto last som du förväntar dig att ta emot. Designern parsar JSON-innehåll med hjälp av det här schemat och genererar användarvänliga token som representerar egenskaperna i ditt JSON-innehåll. Du kan sedan enkelt referera till och använda dessa egenskaper i din Logic app-arbetsflöde. 


Klicka på **+ nytt steg** och klicka sedan på **+ Lägg till en åtgärd**. Under **Välj en åtgärd**, skriver du **JSON** och väljer sedan **parsa JSON**.

![Välj parsa JSON-aktivitet](media/logs-export-logic-app/select-parse-json.png)

Klicka i **innehålls** rutan om du vill visa en lista över värden från tidigare aktiviteter. Välj **brödtext** från åtgärden **Kör fråga och lista resultat** . Detta är utdata från logg frågan.

[![Välj brödtext](media/logs-export-logic-app/select-body.png)](media/logs-export-logic-app/select-body.png#lightbox)

5.  Klicka på **Använd exempel nytto last för att skapa schemat**. Kör logg frågan och kopiera de utdata som ska användas för exempel nytto lasten.  I exempel frågan här kan du använda följande utdata:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Parsa JSON-nyttolast](media/logs-export-logic-app/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Lägg till åtgärden Skriv
Åtgärden **Skriv** använder PARSade JSON-utdata och skapar objektet som du behöver lagra i blobben.

Klicka på **+ nytt steg** och klicka sedan på **+ Lägg till en åtgärd**. Under **Välj en åtgärd** **skriver du Skriv och** väljer sedan åtgärden **Skriv** .

![Välj Skriv åtgärd](media/logs-export-logic-app/select-compose.png)


Klicka i rutan **indata** om du vill visa en lista med värden från tidigare aktiviteter. Välj **brödtext** från åtgärden **parsa JSON** . Detta är parsade utdata från logg frågan.

[![Välj brödtext för Skriv åtgärd](media/logs-export-logic-app/select-body-compose.png)](media/logs-export-logic-app/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Lägg till åtgärden Skapa BLOB
Åtgärden skapa BLOB skriver den sammansatta JSON-filen till lagring.

Klicka på **+ nytt steg** och klicka sedan på **+ Lägg till en åtgärd**. Under **Välj en åtgärd** skriver du **BLOB** och väljer sedan åtgärden **skapa BLOB** .

![Välj Skapa BLOB](media/logs-export-logic-app/select-create-blob.png)

Ange ett namn för anslutningen till ditt lagrings konto i **anslutnings namnet** och klicka sedan på mappikonen i rutan **mappsökväg** för att välja behållaren i ditt lagrings konto. Klicka på **BLOB-namnet** om du vill se en lista över värden från tidigare aktiviteter. Klicka på **uttryck** och ange ett uttryck som matchar ditt tidsintervall. För den här frågan som körs varje timme anger följande uttryck BLOB-namnet per föregående timme: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![BLOB-uttryck](media/logs-export-logic-app/blob-expression.png)](media/logs-export-logic-app/blob-expression.png#lightbox)

Klicka på rutan **BLOB-innehåll** om du vill visa en lista med värden från föregående aktiviteter och välj sedan **utdata** i avsnittet **Skriv** .


![Skapa BLOB-uttryck](media/logs-export-logic-app/create-blob.png)


## <a name="test-the-logic-app"></a>Testa logikappen
Testa arbets flödet genom att klicka på **Kör**. Om arbets flödet innehåller fel visas det i steget med problemet. Du kan visa körningarna och gå vidare till varje steg för att Visa indata och utdata för att undersöka felen. Se [Felsöka och diagnostisera arbets flödes fel i Azure Logic Apps](../../logic-apps/logic-apps-diagnosing-failures.md) vid behov.

[![Kör historik](media/logs-export-logic-app/runs-history.png)](media/logs-export-logic-app/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Visa loggar i lagring
Gå till menyn **lagrings konton** i Azure Portal och välj ditt lagrings konto. Klicka på panelen **blobbar** och välj den behållare som du angav i åtgärden Skapa blob. Välj en av blobarna och redigera sedan **BLOB**.

[![BLOB-data](media/logs-export-logic-app/blob-data.png)](media/logs-export-logic-app/blob-data.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg frågor i Azure Monitor](../log-query/log-query-overview.md).
- Läs mer om [Logic Apps](../../logic-apps/index.yml)
- Lär dig mer om att [Automatisera energi](https://flow.microsoft.com).
