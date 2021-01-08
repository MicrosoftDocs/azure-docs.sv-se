---
title: Schemalägg återkommande uppgifter och arbets flöden
description: Schemalägg och kör återkommande automatiserade uppgifter och arbets flöden med upprepnings utlösaren i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 9565ad1efc5ae3dc03b94c78ce8ce52e8dd48c65
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019201"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Skapa, Schemalägg och kör återkommande uppgifter och arbets flöden med upprepnings utlösaren i Azure Logic Apps

För att regelbundet köra aktiviteter, processer eller jobb enligt ett särskilt schema kan du starta ditt Logic app-arbetsflöde med den inbyggda **upprepnings** utlösaren, som körs internt i Azure Logic Apps. Du kan ange datum och tid samt en tidszon för att starta arbets flödet och en upprepning för att upprepa det arbets flödet. Om utlösaren missar upprepningar av någon anledning, till exempel på grund av avbrott eller inaktiverade arbets flöden, bearbetar inte den här utlösaren missade upprepningar, men startar om upprepningar vid nästa schemalagda intervall. Mer information om inbyggda schema utlösare och åtgärder finns i [schemalägga och köra återkommande automatiserade uppgifter och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Här följer några mönster som denna utlösare stöder tillsammans med mer avancerade upprepningar och komplexa scheman:

* Kör omedelbart och upprepa varje *n* antal sekunder, minuter, timmar, dagar, veckor eller månader.

* Starta vid ett visst datum och klock slag, kör och upprepa varje *n* sekunder, minuter, timmar, dagar, veckor eller månader.

* Kör och upprepa en eller flera gånger varje dag, till exempel kl. 8:00 och 5:00 PM.

* Kör och upprepa varje vecka, men endast för vissa dagar, till exempel lördag och söndag.

* Kör och upprepa varje vecka, men endast för vissa dagar och tidpunkter, som måndag till fredag kl. 8:00 och 5:00 PM.

För skillnader mellan den här utlösaren och den glidande fönster utlösaren eller mer information om schemaläggning av återkommande arbets flöden, se [schemalägga och köra återkommande automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Om du vill utlösa din Logic app och bara köra en gång i framtiden, se [Kör jobb en gång](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

## <a name="add-the-recurrence-trigger"></a>Lägg till upprepnings utlösaren

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa en tom logikapp.

1. När Logic Apps Designer visas går du till rutan Sök och anger `recurrence` som filter. Välj den här utlösaren i listan utlösare som det första steget i ditt Logic app-arbetsflöde: **upprepning**

   ![Välj utlösare för upprepning](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Ange intervall och frekvens för upprepningen. I det här exemplet anger du dessa egenskaper för att köra arbets flödet varje vecka.

   ![Ange intervall och frekvens](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | **Intervall** | `interval` | Ja | Integer | Ett positivt heltal som beskriver hur ofta arbets flödet körs baserat på frekvensen. Här följer de lägsta och högsta intervallen: <p>– Månad: 1-16 månader <br>– Vecka: 1-71 veckor <br>– Dag: 1-500 dagar <br>– Timme: 1 – 12000 timmar <br>-Minute: 1 – 72000 minuter <br>-Sekund: 1 – 9999999 sekunder<p>Om intervallet till exempel är 6 och frekvensen är "månad", är upprepningen var 6: a månad. |
   | **Frekvens** | `frequency` | Ja | Sträng | Tidsenhet för upprepning: **sekund**, **minut**, **timme**, **dag**, **vecka** eller **månad** |
   ||||||

   > [!IMPORTANT]
   > När upprepningar inte anger avancerade alternativ för schemaläggning baseras framtida upprepningar på den senaste körnings tiden.
   > Start tiderna för dessa upprepningar kan uppstå på grund av faktorer som svars tid under lagrings anrop. För att se till att din Logi Kap par inte saknar upprepning, särskilt när frekvensen är i dagar eller längre, använder du ett av följande alternativ:
   > 
   > * Ange en start tid för upprepningen.
   > 
   > * Ange i hur många timmar och minuter som upprepningen ska köras med hjälp av egenskaperna som heter **vid dessa timmar** och **på dessa minuter**.
   > 
   > * Använd den [glidande fönster utlösaren](../connectors/connectors-native-sliding-window.md)i stället för upprepnings utlösaren.

1. Om du vill ange avancerade alternativ för schemaläggning öppnar du listan **Lägg till ny parameter** . Alla alternativ som du väljer visas i utlösaren efter val.

   ![Avancerade alternativ för schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | **Tidszon** | `timeZone` | Inga | Sträng | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Välj den tidszon som du vill använda. |
   | **Start tid** | `startTime` | Inga | Sträng | Ange ett start datum och en tid som har högst 49 år i framtiden och som måste följa [ISO 8601 datum tid](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) i [formatet UTC-tid](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en [UTC-förskjutning](https://en.wikipedia.org/wiki/UTC_offset): <p><p>ÅÅÅÅ-MM-DDThh: mm: SS om du väljer en tidszon <p>\- eller - <p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte väljer en tidszon <p>Om du till exempel vill ha 18 september 2020 på 2:00 PM anger du "2020-09-18T14:00:00" och väljer en tidszon som Pacific, normal tid. Eller ange "2020-09-18T14:00:00Z" utan en tidszon. <p><p>**Viktigt:** Om du inte väljer en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). Om du väljer ett tids zons värde behöver du inte lägga till ett "ö" i slutet av **Start tid** svärdet. Om du gör det ignorerar Logic Apps tids zonens värde eftersom "Z" indikerar ett UTC-tidsformat. <p><p>För enkla scheman är start tiden den första förekomsten, medan utlösaren i komplexa scheman inte utlöses tidigare än start tiden. [*Hur kan jag använda start datum och-tid?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Dessa dagar** | `weekDays` | Inga | Sträng eller sträng mat ris | Om du väljer "vecka" kan du välja en eller flera dagar när du vill köra arbets flödet: **måndag**, **tisdag**, **onsdag**, **torsdag**, **fredag**, **lördag** och **söndag** |
   | **Vid dessa timmar** | `hours` | Inga | Heltals-eller heltals mat ris | Om du väljer "dag" eller "vecka" kan du välja ett eller flera heltal från 0 till 23 som de timmar på dagen då du vill köra arbets flödet. <p><p>Om du t. ex. anger "10", "12" och "14", får du 10 AM, 12 PM och 2 PM för timmar på dagen, men minuterna beräknas baserat på när upprepningen startar. Om du vill ställa in vissa minuter av dagen, till exempel 10:00 AM, 12:00 PM och 2:00 PM, anger du dessa värden med hjälp av egenskapen med namnet **i minuter**. |
   | **Vid dessa minuter** | `minutes` | Inga | Heltals-eller heltals mat ris | Om du väljer "dag" eller "vecka" kan du välja ett eller flera heltal från 0 till 59 som minuter i timmen när du vill köra arbets flödet. <p>Du kan till exempel ange "30" som minut märke och använda föregående exempel för timmar på dagen, du får 10:30 AM, 12:30 PM och 2:30 PM. <p>**Obs!** ibland kan tidsstämpeln för den utlösta körningen variera upp till 1 minut från den schemalagda tiden. Om du behöver skicka tidsstämpeln exakt som schemalagt till efterföljande åtgärder kan du använda mall uttryck för att ändra tidstämpeln. Mer information finns i [datum-och tids funktioner för uttryck](../logic-apps/workflow-definition-language-functions-reference.md#date-time-functions). |
   |||||

   Anta till exempel att idag är fredag den 4 september 2020. Följande upprepnings utlösare utlöses inte *tidigare* än start datum och-tid, som är fredag den 18 september 2020 kl. 8:00 PST. Däremot anges upprepnings schema för 10:30, 12:30 PM och 2:30 PM endast på måndagar. Första gången utlösaren utlöses och skapar en arbets flödes instans för Logic app på måndag kl. 10:30. Mer information om hur start tiden fungerar finns i [exemplen för start tid](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Framtida körningar sker vid 12:30 PM och 2:30 PM samma dag. Varje upprepning skapar en egen arbets flödes instans. Därefter upprepas hela schemat igen nästa måndag. [*Vad är några andra exempel på förekomster?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Exempel på avancerad schemaläggning](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > Utlösaren visar en för hands version för den angivna upprepningen bara när du väljer "dag" eller "vecka" som frekvens.

1. Nu ska du bygga ditt återstående arbets flöde med andra åtgärder. Fler åtgärder som du kan lägga till finns i [kopplingar för Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Arbets flödes definition-upprepning

I din Logic Apps underliggande arbets flödes definition, som använder JSON, kan du Visa [definitionen av upprepnings utlösaren](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) med de alternativ som du har valt. Om du vill visa den här definitionen väljer du **kodvyn** i verktygsfältet designer. Om du vill gå tillbaka till designern väljer du verktygsfältet designer, **Designer**.

Det här exemplet visar hur en definition för upprepnings utlösare kan se ut i en underliggande arbets flödes definition:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

<a name="daylight-saving-standard-time"></a>

## <a name="trigger-recurrence-shift-between-daylight-saving-time-and-standard-time"></a>Utlös upprepnings Skift mellan sommar tid och normal tid

Återkommande inbyggda utlösare följer det schema som du anger, inklusive alla tids zoner som du anger. Om du inte väljer en tidszon kan sommar tid (sommar tid) påverka när Utlösare körs, till exempel att byta start tid en timme framåt när sommar tid startar och en timme bakåt när sommar tiden slutar.

För att undvika det här skiftet så att din Logi Kap par körs vid den angivna start tiden, se till att du väljer en tidszon. På så sätt byter UTC-tiden för din Logic app också till att räkna med säsongs tiden. Vissa tider kan dock orsaka problem när tiden flyttas. Mer information och exempel finns i [upprepning för sommar tid och normal tid](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

## <a name="next-steps"></a>Nästa steg

* [Pausa arbets flöden med fördröjnings åtgärder](../connectors/connectors-native-delay.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
