---
title: Hantera begränsnings problem eller "429-för många begär Anden"-fel
description: Hur du löser problem med begränsning eller HTTP 429 för många begär Anden, i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366061"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Hantera begränsnings problem (429-"för många begär Anden"-fel) i Azure Logic Apps

I [Azure Logic Apps](../logic-apps/logic-apps-overview.md)returnerar din Logic [-app meddelandet "http 429 för många begär Anden"](https://developer.mozilla.org/docs/Web/HTTP/Status/429) vid begränsning, vilket inträffar när antalet förfrågningar överskrider den hastighet som målet kan hantera under en angiven tids period. Begränsning kan skapa problem som fördröjd data bearbetning, minskad prestanda hastighet och fel som överskrider den angivna återförsöks principen.

![Begränsning i SQL Server koppling](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Här följer några vanliga typer av begränsningar som din Logic app kan uppleva:

* [Logic app](#logic-app-throttling)
* [Anslutning](#connector-throttling)
* [Mål tjänst eller system](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Logic app-begränsning

Den Azure Logic Apps tjänsten har sina egna [data flödes gränser](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Så om din Logi Kap par överskrider dessa gränser, får din Logic app-resurs begränsad, inte bara en angiven instans eller kör.

Du hittar begränsnings händelser på den här nivån genom att kontrol lera din Logic Apps **mått** ruta i Azure Portal.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. På menyn Logic app, under **övervakning**, väljer du **mått**.

1. Under **diagram rubrik** väljer du **Lägg till mått** så att du kan lägga till ett annat mått i det befintliga.

1. I det första mått fältet i listan **mått** väljer du **Åtgärds begränsade händelser**. I det andra mått fältet väljer du **Utlös begränsad händelse** i listan **mått** .

För att kunna hantera begränsning på den här nivån har du följande alternativ:

* Begränsa antalet Logic App-instanser som kan köras samtidigt.

  Om din Logic Apps utlösnings villkor är uppfyllt mer än en gång samtidigt kan flera utlösare för din Logic app köras samtidigt eller *parallellt*. Detta innebär att varje Utlös ande instans utlöses innan föregående arbets flödes instans slutförs.

  Även om standard antalet utlösare som kan köras samtidigt är [obegränsat](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits), kan du begränsa detta antal genom [att aktivera inställningen för den samtidiga](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)körningen och vid behov välja en annan gräns än standardvärdet.

* Aktivera högt data flödes läge.

  En Logic app har en [standard gräns för antalet åtgärder som kan köras under ett rullande intervall på 5 minuter](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Aktivera [hög data flödes läge](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) på din Logic app om du vill höja gränsen till maximalt antal åtgärder.

* Inaktivera funktionen för att dela upp matrisen ("dela på") i utlösare.

  Om en utlösare returnerar en matris för återstående arbets flödes åtgärder som ska bearbetas, delar utlösaren [ **dela vid** inställningen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) upp mat ris objekten och startar en arbets flödes instans för varje mat ris objekt, vilket utlöser flera samtidiga körningar upp till gränsen för [ **delning**](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Om du vill kontrol lera begränsningen inaktiverar du **delningen vid** beteendet och kör din Logic Apps hela matrisen med ett enda anrop, i stället för att hantera ett enskilt objekt per anrop.

* Återtvingande åtgärder i mindre Logic Apps.

  Som tidigare nämnts är en logisk app begränsad till ett [standard antal åtgärder som kan köras under en 5 minuters period](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Även om du kan öka den här gränsen genom att aktivera [läget för hög genom strömning](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode), kan du också fundera över om du vill dela upp din Logi Kap par åtgärder i mindre Logic-appar så att antalet åtgärder som körs i varje Logi Kap par förblir under gränsen. På så sätt minskar du belastningen på en enda Logic app-resurs och distribuerar belastningen över flera Logic Apps. Den här lösningen fungerar bättre för åtgärder som hanterar stora data uppsättningar eller som ökar så många aktiviteter som körs samtidigt, loop-iterationer eller åtgärder i varje slinga, vilket innebär att de överskrider åtgärds körnings gränsen.

  Den här Logic-appen gör till exempel allt arbete för att hämta tabeller från en SQL Server-databas och hämtar raderna från varje tabell. **För varje** slinga upprepas samtidigt genom varje tabell så att åtgärden **Hämta rader** returnerar raderna för varje tabell. Beroende på mängden data i dessa tabeller kan de här åtgärderna överskrida gränsen för åtgärds körningar.

  ![Logic app "före" omfactoring](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Efter omstrukturering är Logic app nu en överordnad och underordnad Logic-app. Överordnad hämtar tabellerna från SQL Server och anropar sedan en underordnad Logic-app för varje tabell för att hämta raderna:

  ![Skapa Logic app för en åtgärd](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Här är den underordnade Logic-appen som anropas av den överordnade Logic-appen för att hämta raderna för varje tabell:

  ![Skapa en annan Logic app för en andra åtgärd](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Anslutnings begränsning

Varje koppling har sina egna begränsningar för begränsning, som du hittar på kopplingens tekniska referens sida. Till exempel har [Azure Service Bus Connector](/connectors/servicebus/) en begränsnings gräns som tillåter upp till 6 000 anrop per minut, medan SQL Server anslutningen har [begränsningar som varierar beroende på åtgärds typ](/connectors/sql/).

Vissa utlösare och åtgärder, till exempel HTTP, har en ["återförsöks princip"](../logic-apps/logic-apps-exception-handling.md#retry-policies) som du kan anpassa baserat på [begränsningen för återförsök](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) för att implementera undantags hantering. Den här principen anger om och hur ofta en utlösare eller åtgärd försöker utföra en begäran när den ursprungliga begäran Miss lyckas eller timeout och resulterar i ett svar på 408, 429 eller 5xx. Så när begränsningen startar och returnerar ett 429-fel, Logic Apps följande princip för återförsök som stöds.

Om du vill veta om en utlösare eller åtgärd stöder principer för återförsök kontrollerar du inställningarna för utlösaren eller åtgärden. Om du vill visa en utlösare eller åtgärds återförsök går du till din Logic Apps körnings historik, väljer den körning som du vill granska och expanderar utlösaren eller åtgärden för att visa information om indata, utdata och eventuella återförsök, till exempel:

![Visa åtgärdens körnings historik, nya försök, indata och utdata](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Även om historiken för återförsök innehåller fel information kan du ha problem med att skilja mellan anslutnings begränsning och [mål begränsning](#destination-throttling). I så fall kan du behöva granska svarets information eller utföra vissa beräkningar för begränsnings intervall för att identifiera källan.

För Logic Apps i den globala Azure Logic Apps tjänsten för flera klient organisationer sker begränsningen på *anslutnings* nivå. Till exempel, för logi Kap par som körs i en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), sker begränsningen fortfarande för icke-ISE-anslutningar eftersom de körs i den globala Logic Appss tjänsten för flera innehavare. ISE-anslutningar, som skapas av ISE-kopplingar, är dock inte begränsade eftersom de körs i din ISE.

För att kunna hantera begränsning på den här nivån har du följande alternativ:

* Konfigurera flera anslutningar för en enda åtgärd så att Logic app partitionerar data för bearbetning.

  För det här alternativet bör du överväga om du kan distribuera arbets belastningen genom att dela upp en åtgärds begär anden över flera anslutningar till samma mål med samma autentiseringsuppgifter.

  Anta till exempel att din Logic app hämtar tabeller från en SQL Server-databas och hämtar sedan raderna från varje tabell. Baserat på antalet rader som du måste bearbeta kan du använda flera anslutningar och flera **för varje** loop för att dela upp det totala antalet rader i mindre mängder för bearbetning. I det här scenariot används två **för varje loop för** att dela upp det totala antalet rader på hälften. Det första **för varje** slinga använder ett uttryck som hämtar den första halvan. Den andra **för varje** slinga använder ett annat uttryck som hämtar den andra halvan, till exempel:<p>

    * Uttryck 1: `take()` funktionen hämtar fram sidan av en samling. Mer information finns i [ **`take()`** funktionen](workflow-definition-language-functions-reference.md#take).

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Uttryck 2: `skip()` funktionen tar bort början av en samling och returnerar alla andra objekt. Mer information finns i [ **`skip()`** funktionen](workflow-definition-language-functions-reference.md#skip).

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Här är ett visuellt exempel som visar hur du kan använda dessa uttryck:

    ![Skapa och Använd flera anslutningar för en enda åtgärd](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Konfigurera en annan anslutning för varje åtgärd.

  För det här alternativet bör du överväga om du kan distribuera arbets belastningen genom att sprida varje åtgärds begär anden över sin egen anslutning, även när åtgärder ansluter till samma tjänst eller system och använder samma autentiseringsuppgifter.

  Anta till exempel att din Logic app hämtar tabellerna från en SQL Server-databas och hämtar varje rad i varje tabell. Du kan använda separata anslutningar så att tabellerna som hämtas använder en anslutning, medan varje rad använder en annan anslutning.

  ![Skapa och Använd olika anslutningar för varje åtgärd](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Ändra samtidigheten eller parallellitet för en ["for each"-loop](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Som standard körs loopen för varje upprepning på samma tid upp till [samtidigheten](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Om du har en koppling som är begränsad i en "för varje"-slinga kan du minska antalet upprepningar som körs parallellt. Mer information finns i de här ämnena:
  
  * ["För varje-slinga-ändra samtidighet eller kör sekventiellt](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Språk schema för arbets flödes definition – för varje loop](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Språk schema för arbets flödes definition-ändra för varje upprepnings valuta](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Språk schema för arbets flödes definition – kör "för varje" slingor i turordning](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Mål tjänst eller system begränsning

Även om en anslutning har egna begränsnings gränser kan mål tjänsten eller det system som anropas av anslutnings tjänsten också ha begränsnings gränser. Vissa API: er i Microsoft Exchange Server har till exempel striktare begränsnings gränser än Office 365 Outlook Connector.

Som standard körs en Logic Apps-instans och alla slingor eller grenar i dessa instanser *parallellt*. Det här beteendet innebär att flera instanser kan anropa samma slut punkt på samma tidpunkt. Varje instans vet inte om den andra förekomsten, så försök att försöka utföra misslyckade åtgärder kan skapa [konkurrens villkor](https://en.wikipedia.org/wiki/Race_condition) där flera anrop försöker köras samtidigt, men för att lyckas måste dessa anrop tas emot i mål tjänsten eller systemet innan begränsningen börjar inträffa.

Anta till exempel att du har en matris som har 100 objekt. Du använder en "for each"-slinga för att iterera genom matrisen och aktivera slingan ' s concurrency-kontroll så att du kan begränsa antalet parallella iterationer till 20 eller den [aktuella standard gränsen](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Inuti den här slingan infogar en åtgärd ett objekt från matrisen i en SQL Server databas som endast tillåter 15 anrop per sekund. Det här scenariot resulterar i ett begränsnings problem eftersom en efter släpning av återförsök bygger upp och aldrig kommer att köras.

I den här tabellen beskrivs tids linjen för vad som händer i slingan när åtgärdens återförsöksintervall är 1 sekund:

| Tidpunkt | Antal åtgärder som körs | Antal åtgärder som inte fungerar | Antal försök som väntar |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 sekunder | 20 infogningar | 5 misslyckande, på grund av SQL-gräns | 5 återförsök |
| T + 0,5 sekunder | 15 infogningar, på grund av tidigare 5 omförsök | Alla 15 misslyckanden, på grund av tidigare SQL-gräns, gäller fortfarande för ytterligare 0,5 sekunder | 20 återförsök <br>(föregående 5 + 15 nya) |
| T + 1 sekund | 20 infogningar | 5 misslyckande plus föregående 20 försök på grund av SQL-gränsen | 25 återförsök (föregående 20 + 5 nya)
|||||

För att kunna hantera begränsning på den här nivån har du följande alternativ:

* Skapa Logi Kap par så att varje hanterar en enda åtgärd.

  * Om du fortsätter med exemplet SQL Server scenario i det här avsnittet kan du skapa en logisk app som placerar mat ris objekt i en kö, till exempel en [Azure Service Bus kö](../connectors/connectors-create-api-servicebus.md). Sedan skapar du en annan Logic-app som endast utför infognings åtgärden för varje objekt i kön. På så sätt körs bara en instans av logi Kap par vid en angiven tidpunkt, som antingen Slutför infognings åtgärden och flyttar till nästa objekt i kön, eller så får instansen 429 fel men försöker inte att försöka utföra nyförsök.

  * Skapa en överordnad Logic-app som anropar en underordnad eller kapslad Logic-app för varje åtgärd. Om överordnad behöver anropa olika underordnade appar baserat på det överordnade objektets resultat kan du använda en villkors åtgärd eller växel åtgärd som avgör vilken underordnad app som ska anropas. Det här mönstret kan hjälpa dig att minska antalet anrop eller åtgärder.

    Anta till exempel att du har två Logic Apps, var och en med en avsöknings utlösare som kontrollerar ditt e-postkonto varje minut för ett speciellt ämne, till exempel "lyckad" eller "Misslyckad". Den här installationen resulterar i 120 anrop per timme. Om du i stället skapar en enda överordnad Logic-app som avsöker varje minut men anropar en underordnad Logic-app som körs baserat på om ämnet är "lyckades" eller "haveri", klipper du ut antalet avsöknings kontroller till hälften eller 60 i det här fallet.

* Konfigurera batchbearbetning.

  Om mål tjänsten stöder batch-åtgärder kan du hantera begränsningar genom att bearbeta objekt i grupper eller batchar i stället för enskilda. Om du vill implementera batch-bearbetnings lösningen skapar du en "batch-mottagare" Logic app och "batch-avsändare". Batch-avsändaren samlar in meddelanden eller objekt tills dina angivna kriterier är uppfyllda och skickar sedan dessa meddelanden eller objekt i en enda grupp. Batch-mottagaren accepterar den gruppen och bearbetar dessa meddelanden eller objekt. Mer information finns i [batch-bearbeta meddelanden i grupper](../logic-apps/logic-apps-batch-process-send-receive-messages.md).

* Använd webhook-versionerna för utlösare och åtgärder i stället för avsöknings versioner.

  Varför? En avsöknings utlösare fortsätter att kontrol lera mål tjänsten eller systemet vid angivna intervall. Ett frekvent intervall, till exempel varje sekund, kan skapa begränsnings problem. En webhook-utlösare eller åtgärd, till exempel [http-webhook](../connectors/connectors-native-webhook.md), skapar dock bara ett enda anrop till mål tjänsten eller systemet, vilket sker vid den aktuella prenumerationen och begär att målet meddelar utlösaren eller åtgärden endast när en händelse inträffar. På så sätt behöver inte utlösaren eller åtgärden kontinuerligt kontrol lera målet.
  
  Om mål tjänsten eller systemet har stöd för webhookar eller tillhandahåller en anslutning som har en webhook-version är det här alternativet bättre än att använda avsöknings versionen. Om du vill identifiera webhook-utlösare och åtgärder kontrollerar du att de har `ApiConnectionWebhook` typen eller att de inte kräver att du anger en upprepning. Mer information finns i [APIConnectionWebhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och [APIConnectionWebhook åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md)
