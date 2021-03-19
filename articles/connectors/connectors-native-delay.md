---
title: Fördröj nästa åtgärd i arbets flöden
description: Vänta på att köra nästa åtgärd i Logic app-arbetsflöden genom att använda fördröjningen eller fördröjningen tills åtgärder i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74787344"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Fördröj körningen av nästa åtgärd i Azure Logic Apps

Om du vill att din Logic app ska vänta en stund innan du kör nästa åtgärd kan du lägga till den inbyggda **fördröjnings schema** åtgärden innan en åtgärd i din Logic app-arbetsflöde. Eller så kan du lägga till den inbyggda **fördröjningen tills schema** åtgärden väntar tills ett visst datum och en viss tidpunkt innan du kör nästa åtgärd. Mer information om inbyggda schema åtgärder och utlösare finns i [schemalägga och köra återkommande automatiserade uppgifter och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

* **Fördröjning**: vänta tills det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader innan nästa åtgärd körs.

* **Fördröjning till**: vänta tills det angivna datumet och den angivna tiden innan nästa åtgärd körs.

Här följer några exempel på hur du kan använda dessa åtgärder:

* Vänta till en veckodag och skicka en status uppdatering via e-post.

* Fördröj ditt arbets flöde tills ett HTTP-anrop har slutförts innan du återupptar och hämtar data.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic Apps](../logic-apps/logic-apps-overview.md). Innan du kan använda en åtgärd måste din Logic app börja med en utlösare. Du kan använda valfri utlösare som du vill och lägga till andra åtgärder innan du lägger till en fördröjnings åtgärd. I det här avsnittet används en Office 365 Outlook-utlösare. Lär dig [hur du skapar din första Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om du inte har använt Logic Apps igen.

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>Lägg till fördröjnings åtgärden

1. I Logic App Designer, under steget där du vill lägga till fördröjnings åtgärden, väljer du **nytt steg**.

   Om du vill lägga till fördröjnings åtgärden mellan stegen flyttar du pekaren över pilen som kopplar stegen. Välj plus tecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "fördröjning" som filter. I listan åtgärder väljer du den här åtgärden: **fördröjning**

   ![Lägg till åtgärden fördröjning](./media/connectors-native-delay/add-delay-action.png)

1. Ange hur lång tid som ska förflyta innan nästa åtgärd körs.

   ![Ange hur lång tid det tar för fördröjningen](./media/connectors-native-delay/delay-time-intervals.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Antal | count | Ja | Integer | Antalet tidsenheter som ska förskjutas |
   | Enhet | unit | Ja | Sträng | Tidsenhet, till exempel: `Second` ,,, `Minute` , `Hour` `Day` `Week` eller `Month` |
   ||||||

1. Lägg till andra åtgärder som du vill köra i arbets flödet.

1. När du är klar sparar du din Logic app.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>Lägg till åtgärden fördröjning – till

1. I Logic App Designer, under steget där du vill lägga till fördröjnings åtgärden, väljer du **nytt steg**.

   Om du vill lägga till fördröjnings åtgärden mellan stegen flyttar du pekaren över pilen som kopplar stegen. Välj plus tecknet (+) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "fördröjning" som filter. I listan åtgärder väljer du den här åtgärden: **fördröjning tills**

   ![Lägg till åtgärden fördröj tills](./media/connectors-native-delay/add-delay-until-action.png)

1. Ange slutdatum och slut tid för när du vill återuppta arbets flödet.

   ![Ange tidsstämpel för när fördröjningen ska avslutas](./media/connectors-native-delay/delay-until-timestamp.png)

   | Egenskap | JSON-namn | Krävs | Typ | Beskrivning |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | Ja | Sträng | Slutdatum och slut tid för att återuppta arbets flödet med det här formatet: <p>ÅÅÅÅ-MM-DDThh: mm: ssZ <p>Om du till exempel vill ha 18 september 2017 vid 2:00 PM, anger du "2017-09-18T14:00:00Z". <p>**Obs:** Tids formatet måste följa [ISO 8601-datum](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) /tid-specifikationen i [UTC-datum](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), men utan en UTC- [förskjutning](https://en.wikipedia.org/wiki/UTC_offset). Utan en tidszon måste du lägga till bokstaven "Z" i slutet utan blank steg. Detta "Z" avser motsvarande [nautiska tid](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Lägg till andra åtgärder som du vill köra i arbets flödet.

1. När du är klar sparar du din Logic app.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och köra återkommande uppgifter och arbets flöden med upprepnings utlösaren](../connectors/connectors-native-recurrence.md)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)