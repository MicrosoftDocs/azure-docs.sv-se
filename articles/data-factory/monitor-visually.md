---
title: Övervaka Azure-datafabriker visuellt
description: Lär dig att visuellt övervaka Azures data fabriker
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4248969a5de384810f9718648ef8ac8f5f6d4129
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373172"
---
# <a name="visually-monitor-azure-data-factory"></a>Övervaka Azure-datafabriker visuellt

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

När du har skapat och publicerat en pipeline i Azure Data Factory kan du associera den med en utlösare eller manuellt starta en ad hoc-körning. Du kan övervaka alla dina pipelines körs internt i Azure Data Factory användar upplevelse. Öppna övervaknings upplevelsen genom att välja panelen **övervaka & hantera** på Data Factory-bladet i [Azure Portal](https://portal.azure.com/). Om du redan befinner dig i ADF-UX klickar du på ikonen **övervaka** på den vänstra panelen.

Alla data fabriks körningar visas som standard i webbläsarens lokala tidszon. Om du ändrar tids zonen fästs alla datum/tid-fält på den som du har valt.

## <a name="monitor-pipeline-runs"></a>Övervaka pipelinekörningar

Vyn standard övervakning är en lista över utlösta pipeline-körningar under den valda tids perioden. Du kan ändra tidsintervallet och filtrera efter status, pipeline-namn eller anteckning. Hovra över den angivna pipeline-körningen för att hämta de körnings bara åtgärderna, till exempel kör-och förbruknings rapporten.

![Listvy för övervakning av pipeline-körningar](media/monitor-visually/pipeline-runs.png)

Pipelinens körnings rutnät innehåller följande kolumner:

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Pipeline-namn | Namn på pipelinen |
| Kör Start | Start datum och-tid för pipeline-körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Kör slut | Slutdatum och-tid för pipeline-körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Varaktighet | Körnings tid (HH: MM: SS) |
| Utlöst av | Namnet på utlösaren som startade pipelinen |
| Status | **Misslyckades**, **lyckades**, pågår, **avbröts** eller **köade** |
| Anteckningar | Filter bara taggar som är associerade med en pipeline  |
| Parametrar | Parametrar för pipeline-körningen (namn/värde-par) |
| Fel | Om pipelinen misslyckades, körs fel |
| Körnings-ID | ID för pipeline-körningen |

Du måste manuellt välja knappen **Uppdatera** för att uppdatera listan över pipeline-och aktivitets körningar. Autouppdatering stöds inte för närvarande.

![Uppdateringsknapp](media/monitor-visually/refresh.png)

Om du vill visa resultatet av en fel söknings körning väljer du fliken **Felsök** .

![Välj ikonen Visa aktiva fel söknings körningar](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Övervaka aktivitetskörningar

Om du vill få en detaljerad översikt över de enskilda aktivitets körningarna för en viss pipeline-körning klickar du på pipelinens namn.

![Visa aktivitetskörningar](media/monitor-visually/view-activity-runs.png)

Listvyn visar aktivitets körningar som motsvarar varje pipeline-körning. Hovra över den särskilda aktivitets körningen för att hämta information om den här typen av information, till exempel JSON-indata, JSON-utdata och detaljerade verksamhetsspecifika övervaknings upplevelser.

![Det finns information om SalesAnalyticsMLPipeline, följt av en lista över aktivitets körningar.](media/monitor-visually/activity-runs.png)

| **Kolumnnamn** | **Beskrivning** |
| --- | --- |
| Aktivitetsnamn | Namn på aktiviteten inuti pipelinen |
| Aktivitetstyp | Typ av aktivitet, till exempel **copy**, **ExecuteDataFlow** eller **AzureMLExecutePipeline** |
| Åtgärder | Ikoner som gör att du kan se information om JSON-indata, JSON-utdata eller detaljerade verksamhetsspecifika övervaknings upplevelser | 
| Kör Start | Start datum och-tid för aktivitets körningen (MM/DD/ÅÅÅÅ, HH: MM: SS fm/em) |
| Varaktighet | Körnings tid (HH: MM: SS) |
| Status | **Misslyckades**, **lyckades**, **pågår** eller **avbröts** |
| Integration Runtime | Som Integration Runtime aktiviteten kördes på |
| Egenskaper för användare | Användardefinierade egenskaper för aktiviteten |
| Fel | Om aktiviteten misslyckades, körs fel |
| Körnings-ID | ID för aktivitets körning |

Om en aktivitet Miss lyckas kan du se det detaljerade fel meddelandet genom att klicka på ikonen i fel kolumnen. 

![Ett meddelande visas med fel information, inklusive felkod, fel typ och fel information.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Befordra användar egenskaper för övervakning

Höj en pipeline-aktivitets egenskap som en användar egenskap så att den blir en entitet som du övervakar. Du kan till exempel flytta **käll** -och **mål** egenskaperna för kopierings aktiviteten i din pipeline som användar egenskaper.

> [!NOTE]
> Du kan bara flytta upp till fem egenskaper för pipeline-aktiviteter som användar egenskaper.

![Skapa användar egenskaper](media/monitor-visually/promote-user-properties.png)

När du har skapat användar egenskaperna kan du övervaka dem i vyerna i övervaknings listan.

![Lägg till kolumner för användar egenskaper i listan aktivitets körningar](media/monitor-visually/choose-user-properties.png)

 Om källan för kopierings aktiviteten är ett tabell namn kan du övervaka käll tabellens namn som en kolumn i list visningen för aktivitets körningar.

![Aktivitets körnings lista med kolumner för användar egenskaper](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Kör pipelines och aktiviteter igen

Om du vill köra en pipeline som tidigare har körts från Start, hovrar du över den aktuella pipeline-körningen och väljer **Kör** om. Om du väljer flera pipeliner kan du använda knappen **Kör** om för att köra alla.

![Köra en pipeline igen](media/monitor-visually/rerun-pipeline.png)

Om du vill köra igen vid en viss tidpunkt kan du göra det från vyn aktivitets körningar. Välj den aktivitet du vill starta från och välj **Kör om från aktivitet**. 

![Kör en aktivitets körning igen](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Kör om från misslyckad aktivitet

Om en aktivitet Miss lyckas, tids gränsen uppnåddes eller avbryts, kan du köra pipelinen igen från den misslyckade aktiviteten genom att välja **Kör om från misslyckad aktivitet**.

![Kör om misslyckad aktivitet](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Visa körnings historik igen

Du kan visa körnings historiken för alla pipelines i list visningen.

![Visa historik](media/monitor-visually/rerun-history-1.png)

Du kan också Visa körnings historiken för en viss pipeline-körning.

![Visa historik för en pipeline-körning](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Övervaka förbrukning

Du kan se de resurser som förbrukas av en pipeline-körning genom att klicka på förbruknings ikonen bredvid kör. 

![Skärm bild som visar var du kan se de resurser som förbrukas av en pipeline.](media/monitor-visually/monitor-consumption-1.png)

Om du klickar på ikonen öppnas en förbruknings rapport över resurser som används av pipeline-körningen. 

![Övervaka förbrukning](media/monitor-visually/monitor-consumption-2.png)

Du kan koppla värdena till [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/details/data-factory/) för att beräkna kostnaden för pipeline-körningen. Mer information om Azure Data Factory priser finns i [förstå priser](pricing-concepts.md).

> [!NOTE]
> De här värdena som returneras av pris kalkylatorn är en uppskattning. Det visar inte exakt det belopp du debiteras per Azure Data Factory 

## <a name="gantt-views"></a>Gantt-vyer

Ett Gantt-diagram är en vy som gör att du kan se körnings historiken under ett tidsintervall. Genom att växla till en Gantt-vy visas alla pipeline-körningar grupperade efter namn som visas i relation till hur lång tid körningen tog. Du kan också gruppera efter anteckningar/taggar som du har skapat i din pipeline. Gantt-vyn är också tillgänglig på aktivitets körnings nivån.

![Exempel på ett Gantt-diagram](media/monitor-visually/select-gantt.png)

Längden på fältet informerar pipelinens varaktighet. Du kan också välja fältet för att se mer information.

![Varaktighet för Gantt-schema](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Aviseringar

Du kan generera aviseringar för mått som stöds i Data Factory. Välj **övervaka**  >  **aviseringar & mått** på sidan Data Factory övervakning för att komma igång.

![Sidan Data Factory Monitor](media/monitor-visually/start-page.png)

En introduktion till sju minuter och demonstration av den här funktionen finns på följande video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Skapa aviseringar

1.  Välj **ny aviserings regel** för att skapa en ny avisering.

    ![Knappen Ny varnings regel](media/monitor-visually/new-alerts.png)

1.  Ange regel namnet och välj allvarlighets grad för avisering.

    ![Rutor för regel namn och allvarlighets grad](media/monitor-visually/name-and-severity.png)

1.  Välj aviserings villkoret.

    ![Box för mål villkor](media/monitor-visually/add-criteria-1.png)

    ![Skärm bild som visar var du väljer ett mått för att ställa in varnings villkoret.](media/monitor-visually/add-criteria-2.png)

    ![Lista över kriterier](media/monitor-visually/add-criteria-3.png)

    Du kan skapa aviseringar för olika mått, inklusive för antalet enheter för ADF-enheter/storlek, aktivitet/pipeline/utlösare, Integration Runtime (IR) processor belastning/minne/antal noder/kö, samt för SSIS-paket körningar och SSIS IR-start/stopp-åtgärder.

1.  Konfigurera aviserings logiken. Du kan skapa en avisering för det valda måttet för alla pipeliner och motsvarande aktiviteter. Du kan också välja en viss aktivitets typ, ett aktivitets namn, ett pipelin namn eller en feltyp.

    ![Alternativ för att konfigurera aviserings logik](media/monitor-visually/alert-logic.png)

1.  Konfigurera e-post, SMS, push och röst meddelanden för aviseringen. Skapa en åtgärds grupp eller Välj en befintlig för aviserings meddelanden.

    ![Alternativ för att konfigurera meddelanden](media/monitor-visually/configure-notification-1.png)

    ![Alternativ för att lägga till ett meddelande](media/monitor-visually/configure-notification-2.png)

1.  Skapa varnings regeln.

    ![Alternativ för att skapa en varnings regel](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Nästa steg

Mer information om övervakning och hantering av pipelines finns i artikeln [övervaka och hantera pipelines program mässigt](./monitor-programmatically.md) .