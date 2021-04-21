---
title: Inbyggda utlösare och åtgärder för Azure Logic Apps
description: Använd inbyggda utlösare och åtgärder för att skapa automatiserade arbetsflöden som integrerar appar, data, tjänster och system, för att styra arbetsflöden och för att hantera data med hjälp av Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: 045d7391c9c3c2870efddc0aed4ae7590db938d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797184"
---
# <a name="built-in-triggers-and-actions-for-logic-apps"></a>Inbyggda utlösare och åtgärder för Logic Apps


[Med inbyggda utlösare](apis-list.md) och åtgärder kan du styra arbetsflödets schema och [struktur,](#control-workflow)köra din egen [kod,](#run-code-from-workflows)hantera eller manipulera [data](#manage-or-manipulate-data)och utföra andra uppgifter i dina arbetsflöden. Skiljer sig [från hanterade](managed.md)anslutningsappar är många inbyggda åtgärder inte knutna till en specifik tjänst, ett specifikt system eller protokoll. Du kan till exempel starta nästan alla arbetsflöden enligt ett schema med hjälp av upprepningsutlösaren. Eller så kan du få arbetsflödet att vänta tills det anropas med hjälp av begärandeutlösaren. Alla inbyggda åtgärder körs inbyggt i Logic Apps-tjänsten och de flesta kräver inte att du skapar en anslutning innan du använder dem. 

Logic Apps tillhandahåller också inbyggda åtgärder för ett mindre antal tjänster, system och protokoll, till exempel Azure Service Bus, Azure Functions, SQL, AS2 och så vidare. Antalet och intervallet varierar beroende på om du skapar en logikapp för flera innehavare eller en logikapp för en enskild klientorganisation. I några fall är både en inbyggd version och en version av en hanterad anslutningsapp tillgängliga. I de flesta fall ger den inbyggda versionen bättre prestanda, funktioner, priser och så vidare. Om du till exempel vill utbyta [B2B-meddelanden](../logic-apps/logic-apps-enterprise-integration-as2.md)med as2-protokollet väljer du den inbyggda versionen såvida du inte behöver spårningsfunktioner, som endast är tillgängliga i den (inaktuella) versionen av den hanterade anslutningsappen.

I följande lista beskrivs bara några av de uppgifter som du kan utföra [med inbyggda utlösare och åtgärder:](#understand-triggers-and-actions)

- Kör arbetsflöden med anpassade och avancerade scheman. Mer information om schemaläggning finns i avsnittet [om upprepningsbeteende i översikten Logic Apps anslutningsappen.](apis-list.md#recurrence-behavior)
- Organisera och kontrollera arbetsflödets struktur, till exempel med hjälp av loopar och villkor.
- Arbeta med variabler, datum, dataåtgärder, innehållstransformningar och batchåtgärder.
- Kommunicera med andra slutpunkter med HTTP-utlösare och åtgärder.
- Ta emot och svara på begäranden.
- Anropa dina egna funktioner (Azure Functions), webbappar (Azure App Services), API:er (Azure API Management), andra Logic Apps-arbetsflöden som kan ta emot begäranden och så vidare.

## <a name="understand-triggers-and-actions"></a>Förstå utlösare och åtgärder

Logic Apps innehåller följande inbyggda utlösare och åtgärder:

:::row:::
    :::column:::
        [![Ikonen Schema i Logic Apps][schedule-icon]][schedule-doc]
        \
        \
        [**Schema**][schedule-doc]
        \
        \
        [**Upprepning:**][schedule-recurrence-doc]Utlöser ett arbetsflöde baserat på den angivna upprepningen.
        \
        \
        [**Skjutfönster:**][schedule-sliding-window-doc]Utlöst ett arbetsflöde som behöver hantera data i kontinuerliga segment.
        \
        \
        [**Fördröjning:**][schedule-delay-doc]Pausa arbetsflödet under den angivna varaktigheten.
        \
        \
        [**Fördröj**][schedule-delay-until-doc]tills : Pausa arbetsflödet tills det angivna datumet och den angivna tiden.
    :::column-end:::
    :::column:::
        [![Batch-ikon i Logic Apps][batch-icon]][batch-doc]
        \
        \
        [**Batch**][batch-doc]
        \
        \
        [**Batchmeddelanden:**][batch-doc]Utlöst ett arbetsflöde som bearbetar meddelanden i batchar.
        \
        \
        [**Skicka meddelanden till batch:**][batch-doc]Anropa ett befintligt arbetsflöde som för närvarande börjar med en **Batch-meddelandeutlösare.**
    :::column-end:::
    :::column:::
        [![HTTP-ikon i Logic Apps][http-icon]][http-doc]
        \
        \
        [**HTTP**][http-doc]
        \
        \
        Anropa en HTTP- eller HTTPS-slutpunkt med hjälp av HTTP-utlösaren eller åtgärden. 
        \
        \
        Du kan också använda dessa andra inbyggda HTTP-utlösare och åtgärder: 
        - [HTTP + Swagger][http-swagger-doc]
        - [HTTP + Webhook][http-webhook-doc]
    :::column-end:::
    :::column:::
        [![Ikonen Begäran][http-request-icon]][http-request-doc]
        \
        \
        [**Begäran**][http-request-doc]
        \
        \
        [**När en HTTP-begäran tas emot:**][http-request-doc]Vänta på en begäran från ett annat arbetsflöde, en app eller en tjänst. Den här utlösaren gör arbetsflödet anropsbart utan att behöva kontrolleras eller avsökas enligt ett schema.
        \
        \
        [**Svar:**][http-request-doc]Svara på en begäran som tagits emot av **utlösaren När en HTTP-begäran tas** emot i samma arbetsflöde.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure API Management-ikon i Logic Apps][azure-api-management-icon]][azure-api-management-doc]
        \
        \
        [**Azure API Management**][azure-api-management-doc]
        \
        \
        Anropa dina egna utlösare och åtgärder i API:er som du definierar, hanterar och publicerar med [hjälp av Azure API Management](../api-management/api-management-key-concepts.md). <p><p>**Obs!** Stöds inte när du [använder förbrukningsnivån för API Management](../api-management/api-management-features.md).
    :::column-end:::
    :::column:::
        [![Azure App Services-ikonen i Logic Apps][azure-app-services-icon]][azure-app-services-doc]
        \
        \
        [**Azure App Services**][azure-app-services-doc]
        \
        \
        Anropa appar som du skapar och är värd [för Azure App Service](../app-service/overview.md), till exempel API Apps och Web Apps.
        \
        \
        När Swagger ingår visas utlösare och åtgärder som definieras av dessa appar som alla andra förstklassiga utlösare och åtgärder i Azure Logic Apps.
    :::column-end:::
    :::column:::
        [![Azure Logic Apps i Logic Apps][azure-logic-apps-icon]][nested-logic-app-doc]
        \
        \
        [**Azure Logic Apps**][nested-logic-app-doc]
        \
        \
        Anropa andra arbetsflöden som börjar med begärandeutlösaren med **namnet När en HTTP-begäran tas emot.**
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="run-code-from-workflows"></a>Köra kod från arbetsflöden

Logic Apps innehåller inbyggda åtgärder för att köra din egen kod i arbetsflödet:

:::row:::
    :::column:::
        [![Azure Functions ikonen i Logic Apps][azure-functions-icon]][azure-functions-doc]
        \
        \
        [**Azure-funktioner**][azure-functions-doc]
        \
        \
        Anropa [Azure-värdbaserade funktioner för](../azure-functions/functions-overview.md) att köra egna *kodfragment* (C# eller Node.js) i arbetsflödet.
    :::column-end:::
    :::column:::
        [![Ikon för infogade kod i Logic Apps][inline-code-icon]][inline-code-doc]
        \
        \
        [**Infogade kod**][inline-code-doc]
        \
        \
        [**Kör JavaScript-kod:**][inline-code-doc]Lägg till och kör egna infogade JavaScript-kodfragment i arbetsflödet. 
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="control-workflow"></a>Kontrollera arbetsflöde

Logic Apps innehåller inbyggda åtgärder för att strukturera och kontrollera åtgärderna i arbetsflödet:

:::row:::
    :::column:::
        [![Ikonen Villkorsåtgärd i Logic Apps][condition-icon]][condition-doc]
        \
        \
        [**Villkor**][condition-doc]
        \
        \
        Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt.
    :::column-end:::
    :::column:::
        [![För varje åtgärdsikon i Logic Apps][for-each-icon]][for-each-doc]
        \
        \
        [**För varje**][for-each-doc]
        \
        \
        Utför samma åtgärder på alla objekt i en matris.
    :::column-end:::
    :::column:::
        [![Ikon för omfångsåtgärd i Logic Apps][scope-icon]][scope-doc]
        \
        \
        [**Namn**][scope-doc]
        \
        \
        Gruppera åtgärder i *omfång*, som får sin egen status när åtgärderna i omfånget har körts klart.
    :::column-end:::
    :::column:::
        [![Växla åtgärdsikon i Logic Apps][switch-icon]][switch-doc]
        \
        \
        [**Växla**][switch-doc]
        \
        \
        Gruppera åtgärder i *ärenden*, som tilldelas unika värden förutom standardfallet. Kör endast det fall vars tilldelade värde matchar resultatet från ett uttryck, objekt eller token. Om det inte finns några matchningar kör du standardfallet.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Avsluta åtgärdsikonen i Logic Apps][terminate-icon]][terminate-doc]
        \
        \
        [**Avsluta**][terminate-doc]
        \
        \
        Stoppa ett arbetsflöde för logikapp som körs aktivt. 
    :::column-end:::
    :::column:::
        [![Tills-åtgärdsikonen i Logic Apps][until-icon]][until-doc]
        \
        \
        [**Tills**][until-doc]
        \
        \
        Upprepa åtgärder tills det angivna villkoret är sant eller något tillstånd har ändrats.
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="manage-or-manipulate-data"></a>Hantera eller manipulera data

Logic Apps innehåller inbyggda åtgärder för att arbeta med datautdata och deras format:

:::row:::
    :::column:::
        [![Åtgärdsikon för dataåtgärder i Logic Apps][data-operations-icon]][data-operations-doc]
        \
        \
        [**Dataåtgärder**][data-operations-doc]
        \
        \
        Utföra åtgärder med data. 
        \
        \
        **Skriv:** Skapa en enda utdata från flera indata med olika typer. 
        \
        \
        **Skapa CSV-tabell:** Skapa en tabell med kommateckenavgränsade värden (CSV) från en matris med JSON-objekt. 
        \
        \
        **Skapa HTML-tabell:** Skapa en HTML-tabell från en matris med JSON-objekt. 
        \
        \
        **Filtermatris:** Skapa en matris från objekt i en annan matris som uppfyller dina kriterier. 
        \
        \
        **Koppling:** Skapa en sträng från alla objekt i en matris och avgränsa dessa objekt med den angivna avgränsaren. 
        \
        \
        **Parsa JSON:** Skapa användarvänliga token från egenskaper och deras värden i JSON-innehåll så att du kan använda dessa egenskaper i arbetsflödet. 
        \
        \
        **Välj**: Skapa en matris med JSON-objekt genom att transformera objekt eller värden i en annan matris och mappa objekten till angivna egenskaper.
    :::column-end:::
    :::column:::
        ![Ikon för datum/tid-åtgärd i Logic Apps][date-time-icon]
        \
        \
        **Datum och tid**
        \
        \
        Utföra åtgärder med tidsstämplar.
        \
        \
        **Lägg till i tid:** Lägg till det angivna antalet enheter i en tidsstämpel. 
        \
        \
        **Konvertera tidszon:** Konvertera en tidsstämpel från källtidszonen till måltidszonen. 
        \
        \
        **Aktuell tid:** Returnerar den aktuella tidsstämpeln som en sträng. 
        \
        \
        **Hämta framtida tid:** Returnera den aktuella tidsstämpeln plus de angivna tidsenheterna. 
        \
        \
        **Hämta tidigare tid:** Returnera den aktuella tidsstämpeln minus de angivna tidsenheterna. 
        \
        \
        **Subtrahera från** tid: Subtrahera ett antal tidsenheter från en tidsstämpel.
    :::column-end:::
    :::column:::
        [![Åtgärdsikonen Variabler i Logic Apps][variables-icon]][variables-doc]
        \
        \
        [**Variabler**][variables-doc]
        \
        \
        Utföra åtgärder med variabler.
        \
        \
        **Lägg till i matrisvariabeln**: Infoga ett värde som det sista objektet i en matris som lagras av en variabel. 
        \
        \
        **Lägg till i strängvariabeln**: Infoga ett värde som det sista tecknet i en sträng som lagras av en variabel.
        \
        \
        **Minskningsvariabel:** Minska en variabel med ett konstant värde.
        \
        \
        **Ökningsvariabel:** Öka en variabel med ett konstant värde. 
        \
        \
        **Initiera variabeln**: Skapa en variabel och deklarera dess datatyp och ursprungliga värde. 
        \
        \
        **Ange variabel:** Tilldela ett annat värde till en befintlig variabel. 
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa anpassade API:er som du kan anropa från Logic Apps](/logic-apps/logic-apps-create-api-app)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png


<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Skapa en Azure API Management-tjänstinstans för att hantera och publicera dina API:er"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrera logikappar med App Service API Apps"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrera logikappar med Azure Functions"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Bearbeta meddelanden i grupper eller som batchar"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Utvärdera ett villkor och kör olika åtgärder baserat på om villkoret är sant eller falskt"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Utföra samma åtgärder på alla objekt i en matris"
[http-doc]: ./connectors-native-http.md "Anropa HTTP- eller HTTPS-slutpunkter från dina logikappar"
[http-request-doc]: ./connectors-native-reqres.md "Ta emot HTTP-begäranden i dina logikappar"
[http-response-doc]: ./connectors-native-reqres.md "Svara på HTTP-begäranden från dina logikappar"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Anropa REST-slutpunkter från dina logikappar"
[http-webhook-doc]: ./connectors-native-webhook.md "Vänta på specifika händelser från HTTP- eller HTTPS-slutpunkter"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Lägga till och köra JavaScript-kodfragment från dina logikappar"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrera logikappar med kapslade arbetsflöden"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Välja och filtrera matriser med frågeåtgärden"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Köra logikappar baserat på ett schema"
[schedule-delay-doc]: ./connectors-native-delay.md "Fördröjning vid körning av nästa åtgärd"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Fördröjning vid körning av nästa åtgärd"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Köra logikappar enligt ett återkommande schema"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Köra logikappar som behöver hantera data i sammanhängande segment"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Ordna åtgärder i grupper som får sin egen status när åtgärderna i gruppen har körts klart"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Ordna åtgärder i ärenden som tilldelas unika värden. Kör endast det fall vars värde matchar resultatet från ett uttryck, ett objekt eller en token. Om det inte finns några matchningar kör du standardfallet"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Stoppa eller avbryta ett aktivt arbetsflöde som körs för logikappen"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Upprepa åtgärder tills det angivna villkoret är sant eller något tillstånd har ändrats"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Utföra dataåtgärder som att filtrera matriser eller skapa CSV- och HTML-tabeller"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Utföra åtgärder med variabler, till exempel initiera, ange, öka, minska och lägga till i strängen eller matrisvariabeln"
