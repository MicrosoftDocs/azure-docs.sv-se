---
title: Ansluta till Azure Event Hubs
description: Skapa automatiserade uppgifter och arbets flöden som övervakar och hanterar händelser med hjälp av Azure Event Hubs och Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 65da8e07c01561577fe7eff449bfc10348c7f277
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716609"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Övervaka, ta emot och skicka händelser med Azure Event Hub och Azure Logic Apps

Den här artikeln visar hur du kan övervaka och hantera händelser som skickas till [azure Event Hubs](../event-hubs/event-hubs-about.md) inifrån en Logic app med Azure Event Hubs-anslutningen. På så sätt kan du skapa logikappar som automatiserar aktiviteter och arbetsflöden för att kontrollera, skicka och ta emot händelser från Event Hub. Information om anslutningsspecifika teknisk information finns i referens för [Azure Event Hubs Connector](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [Azure Event Hubs-namnområde och händelsehubben](../event-hubs/event-hubs-create.md)

* Den Logic-app där du vill komma åt Händelsehubben. Om du vill starta din Logic-app med en Azure Event Hubs-utlösare behöver du en [Tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Kontrol lera behörigheter och hämta anslutnings sträng

För att se till att din Logi Kap par kan komma åt din Händelsehubben, kontrol lera dina behörigheter och hämta anslutnings strängen för ditt Event Hubs namn område.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till din Event Hubs- *namnrymd*, inte en speciell händelsehubben. 

1. Välj **principer för delad åtkomst** på namn områdes menyn under **Inställningar**. Under **anspråk** kontrollerar du att du har behörighet att **Hantera** för det namn området.

   ![Hantera behörigheter för ditt Event Hub-namnområde](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Om du senare vill ange din anslutnings information manuellt hämtar du anslutnings strängen för Event Hubs namn området.

   1. Under **princip** väljer du **RootManageSharedAccessKey**.

   1. Hitta anslutnings strängen för primär nyckeln. Välj kopierings knappen och spara anslutnings strängen för senare användning.

      ![Kopiera Event Hubs namn områdets anslutnings sträng](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Kontrol lera att anslutnings strängen inte har parametern om du vill bekräfta att anslutnings strängen är kopplad till Event Hubs namn området eller med en speciell händelsehubben `EntityPath` . Om du hittar den här parametern är anslutnings strängen för en speciell Händelsehubben "entity" och är inte rätt sträng som ska användas med din Logic app.

1. Fortsätt nu med att [lägga till en Event Hubs utlösare](#add-trigger) eller [lägg till en Event Hubs-åtgärd](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Lägga till Event Hubs-utlösare

I Azure Logic Apps måste varje Logi Kap par starta med en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts)som utlöses när en enskild händelse inträffar eller när ett särskilt villkor uppfylls. Varje gång utlösaren utlöses skapar Logic Apps-motorn en Logic App-instans och börjar köra appens arbets flöde.

Det här exemplet visar hur du kan starta ett Logic app-arbetsflöde när nya händelser skickas till Händelsehubben. 

> [!NOTE]
> Alla Event Hub-utlösare är *tids avsöknings* utlösare, vilket innebär att utlösaren bearbetar alla händelser och väntar sedan 30 sekunder per partition för att fler händelser ska visas i händelsehubben. Så om utlösaren har kon figurer ATS med fyra partitioner kan fördröjningen ta upp till två minuter innan utlösaren Slutför avsökningen av alla partitioner. Om inga händelser tas emot under den här fördröjningen hoppas utlösarens körning över. Annars fortsätter utlösaren att läsa händelser tills Händelsehubben är tom. Nästa Utlös ande sökning sker baserat på upprepnings intervallet som du anger i utlösarens egenskaper.

1. I Azure Portal eller Visual Studio skapar du en tom Logic-app som öppnas Logic Apps designer. I det här exemplet används Azure Portal.

1. I rutan Sök anger du "Event Hub" som filter. Välj den här utlösaren i listan utlösare: **när händelser är tillgängliga i Event Hub – Event Hubs**

   ![Välj utlösare](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Om du uppmanas att ange anslutnings information [skapar du din Event Hubs-anslutning nu](#create-connection). 

1. I utlösaren anger du information om Händelsehubben som du vill övervaka. Öppna listan **Lägg till ny parameter** om du vill ha fler egenskaper. Om du väljer en parameter lägger du till den egenskapen i utlösnings kortet.

   ![Egenskaper för utlösare](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på händelsehubb** | Ja | Namnet på Händelsehubben som du vill övervaka |
   | **Innehålls typ** | Inga | Händelsens innehålls typ. Standardvärdet är `application/octet-stream`. |
   | **Konsumentgruppens namn** | Inga | [Namnet på konsument gruppen för Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) som ska användas för att läsa händelser. Om inget värde anges används standard konsument gruppen. |
   | **Maximalt antal händelser** | Inga | Maximalt antal händelser. Utlösaren returnerar mellan ett och antalet händelser som anges av den här egenskapen. |
   | **Intervall** | Ja | Ett positivt heltal som beskriver hur ofta arbets flödet körs baserat på frekvensen |
   | **Frekvens** | Ja | Tidsenhet för upprepningen |
   ||||

   **Ytterligare egenskaper**

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Innehålls schema** | Inga | JSON Content schema för de händelser som ska läsas från Händelsehubben. Om du till exempel anger innehålls schemat kan du utlösa Logic app för de händelser som matchar schemat. |
   | **Minsta partitionsnyckel** | Inga | Ange det minsta [partitions](../event-hubs/event-hubs-features.md#partitions) -ID som ska läsas. Som standard är alla partitioner lästa. |
   | **Maximal partitionsnyckel** | Inga | Ange det maximala [partitions](../event-hubs/event-hubs-features.md#partitions) -ID som ska läsas. Som standard är alla partitioner lästa. |
   | **Tidszon** | Inga | Gäller endast när du anger en start tid eftersom den här utlösaren inte accepterar UTC-förskjutning. Välj den tidszon som du vill använda. <p>Mer information finns i [skapa och köra återkommande aktiviteter och arbets flöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Start tid** | Inga | Ange en start tid i följande format: <p>ÅÅÅÅ-MM-DDThh: mm: SS om du väljer en tidszon<p>\- eller -<p>ÅÅÅÅ-MM-DDThh: mm: ssZ om du inte väljer en tidszon<p>Mer information finns i [skapa och köra återkommande aktiviteter och arbets flöden med Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. När du är klar väljer du **Spara** i verktygsfältet designer.

1. Fortsätt nu att lägga till en eller flera åtgärder i din Logic app för de uppgifter som du vill utföra med utlösnings resultaten. 

   Om du till exempel vill filtrera händelser baserat på ett speciellt värde, till exempel en kategori, kan du lägga till ett villkor så att åtgärden **skicka händelse** endast skickar händelser som uppfyller ditt villkor. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Lägga till Event Hubs-åtgärd

I Azure Logic Apps är en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) ett steg i arbets flödet som följer en utlösare eller en annan åtgärd. I det här exemplet börjar Logic-appen med en Event Hubs-utlösare som söker efter nya händelser i Händelsehubben.

1. Öppna din Logic app i Logic Apps designer i Azure Portal eller Visual Studio. I det här exemplet används Azure Portal.

1. Under utlösare eller åtgärd väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över den anslutande pilen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "Event Hub" som filter.
Välj den här åtgärden i listan åtgärder: **skicka händelse-Event Hubs**

   ![Välj åtgärden "skicka händelse"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Om du uppmanas att ange anslutnings information [skapar du din Event Hubs-anslutning nu](#create-connection). 

1. Ange information om de händelser som du vill skicka i åtgärden. Öppna listan **Lägg till ny parameter** om du vill ha fler egenskaper. Om du väljer en parameter läggs egenskapen till i åtgärds kortet.

   ![Välj Event Hub-namn och ange händelse innehåll](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn på händelsehubb** | Ja | Händelsehubben dit du vill skicka händelsen |
   | **Innehåll** | Inga | Innehållet för den händelse som du vill skicka |
   | **Egenskaper** | Inga | Egenskaperna och värdena för appen som ska skickas |
   | **Partitionsnyckel** | Inga | [Partitions](../event-hubs/event-hubs-features.md#partitions) -ID för varifrån händelsen ska skickas |
   ||||

   Du kan till exempel skicka utdata från din Event Hubs-utlösare till en annan Händelsehubben:

   ![Exempel på sändnings händelse](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Ansluta till Händelsehubben

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. När du uppmanas att ange anslutnings information anger du följande information:

   | Egenskap | Krävs | Värde | Beskrivning |
   |----------|----------|-------|-------------|
   | **Anslutnings namn** | Ja | <*anslutnings namn*> | Namnet som ska skapas för anslutningen |
   | **Event Hubs namnrymd** | Ja | <*Event-Hubbs-namnrymd*> | Välj det Event Hubs namn område som du vill använda. |
   |||||  

   Exempel:

   ![Skapa Event Hub-anslutning](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Om du vill ange anslutnings strängen manuellt väljer du **Ange anslutnings information manuellt**. 
   Lär dig [hur du hittar din anslutnings sträng](#permissions-connection-string).

2. Välj den Event Hubs princip som ska användas, om den inte redan har valts. Välj **Skapa**.

   ![Skapa Event Hub-anslutning, del 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. När du har skapat anslutningen fortsätter du med åtgärden [Lägg till Event Hubs utlösare](#add-trigger) eller [Lägg till Event Hubs](#add-action).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger-fil, finns på [kopplingens referens sida](/connectors/eventhubs/).

> [!NOTE]
> För logi Kap par i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandets gränser](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
