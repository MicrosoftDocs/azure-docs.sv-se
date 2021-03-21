---
title: Lägga till och köra kodfragment med hjälp av infogad kod
description: Lär dig hur du skapar och kör kodfragment med hjälp av infogade kod åtgärder för automatiserade uppgifter och arbets flöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 12/07/2020
ms.custom: devx-track-js
ms.openlocfilehash: 3f88fa38d62778bc3c4c1e29571d1d0ae4eeb5ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98179613"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Lägga till och köra kodfragment med hjälp av infogad kod i Azure Logic Apps

När du vill köra en kod i din Logic app kan du lägga till den inbyggda infogade infogade kod åtgärden som ett steg i din Logic app-arbetsflöde. Den här åtgärden fungerar bäst när du vill köra kod som passar det här scenariot:

* Körs i Java Script. Fler språk kommer snart.

* Har körts på fem sekunder eller färre.

* Hanterar data upp till 50 MB.

* Kräver inte att du arbetar med [ **variablerna**](../logic-apps/logic-apps-create-variables-store-values.md), vilket inte stöds ännu.

* Använder Node.js version 8.11.1. Mer information finns i [standard inbyggda objekt](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

  > [!NOTE]
  > `require()`Funktionen stöds inte av den infogade kod åtgärden för att köra Java Script.

Den här åtgärden kör kodfragmentet och returnerar utdata från det kodfragmentet som en token som heter `Result` . Du kan använda den här token med efterföljande åtgärder i din Logic Apps-arbetsflöde. För andra scenarier där du vill skapa en funktion för din kod kan du försöka [skapa och anropa en funktion via Azure Functions i stället](../logic-apps/logic-apps-azure-functions.md) i din Logic app.

I den här artikeln utlöses exempel Logic-appen när ett nytt e-postmeddelande anländer till ett arbets-eller skol konto. Kodfragmentet extraheras och returnerar alla e-postadresser som visas i e-postmeddelandets brödtext.

![Skärm bild som visar ett exempel på en Logic-app](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic app där du vill lägga till ditt kodfragment, inklusive en utlösare. Om du inte har en logisk app, se [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Exemplet i det här avsnittet använder Office 365 Outlook-utlösaren som heter **när ett nytt e-postmeddelande tas emot**.

* Ett [integrations konto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) som är länkat till din Logic app.

  * Se till att du använder ett integrations konto som är lämpligt för ditt användnings fall eller scenario.

    Till exempel är [kostnads fria](../logic-apps/logic-apps-pricing.md#integration-accounts) integrations konton endast avsedda för exempel scenarier och arbets belastningar, inte produktions scenarier, är begränsade i användning och data flöde och stöds inte av ett service nivå avtal (SLA). Andra nivåer debiteras för kostnader, men omfattar support för SLA, erbjuder mer data flöde och har högre gränser. Läs mer om [nivå](../logic-apps/logic-apps-pricing.md#integration-accounts), [priser](https://azure.microsoft.com/pricing/details/logic-apps/)och [begränsningar](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)för integrations konton.

   * Om du inte vill använda ett integrations konto kan du prova att använda [Azure Logic Apps för hands versionen](logic-apps-overview-preview.md)och skapa en Logic app från resurs typen **Logic app (för hands version)** .

     I Azure Logic Apps Preview heter infogad **kod** nu infogade kod **åtgärder** tillsammans med följande skillnader:

     * Kör **JavaScript-kod** heter nu **Kör i-rad-Java Script**.

     * Om du använder macOS eller Linux är de infogade kod åtgärderna för närvarande inte tillgängliga när du använder tillägget Azure Logic Apps (för hands version) i Visual Studio Code.

     * Inlined Code Operations-åtgärder har [uppdaterade gränser](logic-apps-overview-preview.md#inline-code-limits).

     Du kan starta från något av alternativen här:

     * Skapa Logic-appen från resurs typen **Logic app (för hands version)** [med hjälp av Azure Portal](create-stateful-stateless-workflows-azure-portal.md).

     * Skapa ett projekt för Logic-appen [genom att använda Visual Studio Code och tillägget Azure Logic Apps (förhands granskning)](create-stateful-stateless-workflows-visual-studio-code.md)

## <a name="add-inline-code"></a>Lägg till infogad kod

1. Om du inte redan har gjort det går du till [Azure Portal](https://portal.azure.com)och öppnar din Logic app i Logic App Designer.

1. I designern väljer du var du vill lägga till den infogade kod åtgärden i din Logic app-arbetsflöde.

   * Välj **nytt steg** för att lägga till åtgärden i slutet av arbets flödet.

   * Om du vill lägga till åtgärden mellan stegen flyttar du mus pekaren över pilen som kopplar stegen. Välj plus tecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

   I det här exemplet läggs den infogade kod åtgärden till i Office 365 Outlook-utlösaren.

   ![Lägg till det nya steget under utlösaren](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Under **Välj en åtgärd** i rutan Sök anger du `inline code` . I listan åtgärder väljer du åtgärden **Kör JavaScript-kod**.

   ![Välj åtgärden "Kör JavaScript-kod"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   Åtgärden visas i designern och innehåller som standard viss exempel kod, inklusive en `return` instruktion.

   ![Infogad kod åtgärd med standard exempel kod](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Ta bort exempel koden i rutan **kod** och ange din kod. Skriv den kod som du lade inne i en metod, men utan Metodsignaturen.

   Om du börjar skriva ett identifierat nyckelord visas listan Komplettera automatiskt så att du kan välja bland tillgängliga nyckelord, till exempel:

   ![Lista över kompletterade nyckelord](./media/logic-apps-add-run-inline-code/auto-complete.png)

   I det här exempel kods tycket skapas först en variabel som lagrar ett *reguljärt uttryck* som anger ett mönster som ska matcha i indatamängds text. Koden skapar sedan en variabel som lagrar e-postmeddelandets data från utlösaren.

   ![Skapa variabler](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Om du vill göra resultatet från utlösaren och föregående åtgärder lättare att referera till visas listan med dynamiskt innehåll när markören är i rutan **kod** . I det här exemplet visar listan tillgängliga resultat från utlösaren, inklusive **Body** -token, som du nu kan välja.

   När du har valt **Body** -token matchar den infogade kod åtgärden token till ett `workflowContext` objekt som refererar till e-postens `Body` egenskaps värde:

   ![Välj resultat](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   I rutan **kod** kan ditt kodfragment använda det skrivskyddade `workflowContext` objektet som inmatade. Det här objektet innehåller egenskaper som ger koden åtkomst till resultaten från utlösare och tidigare åtgärder i arbets flödet. Mer information finns i [referens utlösare och åtgärds resultat i koden](#workflowcontext) längre fram i det här avsnittet.

   > [!NOTE]
   > Om kodfragmentet refererar till åtgärds namn som använder punkt operatorn (.) måste du lägga till dessa åtgärds namn i [ **Åtgärds** parametern](#add-parameters). Dessa referenser måste också innesluta åtgärds namn med hakparenteser ([]) och citat tecken, till exempel:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   Den infogade kod åtgärden kräver ingen `return` instruktion, men resultatet från en `return` instruktion är tillgängligt för referens i senare åtgärder via **resultatet** token. Kodfragmentet returnerar till exempel resultatet genom att anropa `match()` funktionen, som söker efter matchningar i e-postmeddelandets brödtext mot det reguljära uttrycket. Åtgärden **Skriv** använder **resultat** -token för att referera till resultaten från den infogade kod åtgärden och skapar ett enda resultat.

   ![Färdig logikapp](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. När du är klar sparar du din Logic app.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Referens utlösare och åtgärd resulterar i din kod

`workflowContext`Objektet har den här strukturen, som innehåller under `actions` `trigger` egenskaperna, och `workflow` .

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Den här tabellen innehåller mer information om de här under egenskaperna:

| Egenskap | Typ | Beskrivning |
|----------|------|-------|
| `actions` | Objekt samling | Resultat objekt från åtgärder som körs innan kodfragmentet körs. Varje-objekt har ett *nyckel/värde-* par där nyckeln är namnet på en åtgärd och värdet motsvarar att anropa [funktionen Actions ()](../logic-apps/workflow-definition-language-functions-reference.md#actions) med `@actions('<action-name>')` . Åtgärdens namn använder samma åtgärds namn som används i den underliggande arbets flödes definitionen, som ersätter blank steg ("") i åtgärds namnet med under streck (_). Det här objektet ger åtkomst till egenskaps värden för åtgärden från den aktuella arbets flödes instansen. |
| `trigger` | Objekt | Resultat objekt från utlösaren och motsvarar anrop av [funktionen trigger ()](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Det här objektet ger åtkomst till utlösarens egenskaps värden från den aktuella arbets flödes instansen. |
| `workflow` | Objekt | Arbets flödes objekt som motsvarar anropet av [arbets flöde ()-funktionen](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Det här objektet ger åtkomst till egenskaps värden för arbets flödet, till exempel arbets flödets namn, körnings-ID och så vidare, från den aktuella arbets flödes instansen. |
|||

I det här avsnittets exempel `workflowContext` har objektet dessa egenskaper som din kod kan komma åt:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Lägga till parametrar

I vissa fall kanske du uttryckligen måste kräva att den infogade kod åtgärden innehåller resultat från utlösaren eller specifika åtgärder som koden refererar till som beroenden genom att lägga till parametrarna **trigger** eller **Actions** . Det här alternativet är användbart för scenarier där de refererade resultaten inte finns vid körning.

> [!TIP]
> Om du planerar att återanvända din kod kan du lägga till referenser till egenskaper med hjälp av rutan **kod** så att koden innehåller de matchade token-referenserna, i stället för att lägga till utlösaren eller åtgärder som explicita beroenden.

Anta till exempel att du har kod som hänvisar till **SelectedOption** -resultatet från **e-** poståtgärden skicka godkännande för Office 365 Outlook Connector. Under tiden analyserar Logic Apps-motorn koden för att avgöra om du har refererat till eventuella utlösare eller åtgärds resultat och inkluderar dessa resultat automatiskt. Vid körning, om du får ett fel meddelande om att den refererade utlösaren eller åtgärds resultatet inte är tillgänglig i det angivna `workflowContext` objektet kan du lägga till utlösaren eller åtgärden som ett explicit beroende. I det här exemplet lägger du till parametern **åtgärder** och anger att den infogade kod åtgärden uttryckligen inkluderar resultatet från e-poståtgärden **Skicka godkännande** .

Om du vill lägga till dessa parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda:

   ![Lägga till parametrar](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parameter | Beskrivning |
   |-----------|-------------|
   | **Åtgärder** | Ta med resultat från tidigare åtgärder. Se [Inkludera åtgärds resultat](#action-results). |
   | **Utlösare** | Ta med resultat från utlösaren. Se [Inkludera utlösare resultat](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Inkludera utlösnings resultat

Om du väljer **utlösare** tillfrågas du om du vill inkludera utlösare.

* Välj **Ja** i listan **utlösare** .

<a name="action-results"></a>

### <a name="include-action-results"></a>Inkludera åtgärds resultat

Om du väljer **åtgärder** uppmanas du att ange de åtgärder som du vill lägga till. Innan du börjar lägga till åtgärder behöver du dock den version av åtgärds namnet som visas i Logic Apps underliggande arbets flödes definition.

* Den här funktionen har inte stöd för variabler, slingor och upprepnings index.

* Namnen i din Logic Apps arbets flödes definition använder ett under streck (_), inte ett blank steg.

* För åtgärds namn som använder punkt operatorn (.) inkluderar du dessa operatorer, till exempel:

  `My.Action.Name`

1. I verktygsfältet designer väljer du **kodvyn** och söker i `actions` attributet efter åtgärdens namn.

   Till exempel `Send_approval_email_` är JSON-namnet för **e-** poståtgärden skicka godkännande.

   ![Hitta åtgärds namn i JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Gå tillbaka till designvyn genom att välja **Designer** i verktygsfältet kodvy.

1. Lägg till den första åtgärden i rutan **Åtgärds objekt – 1** genom att ange åtgärdens JSON-namn.

   ![Ange första åtgärden](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Om du vill lägga till en annan åtgärd väljer du **Lägg till nytt objekt**.

## <a name="reference"></a>Referens

Mer information om åtgärds struktur och syntax för att **köra JavaScript-kod** i din Logic Apps underliggande arbets flödes definition med hjälp av arbets flödets definitions språk finns i [avsnittet referens](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)för åtgärden.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [anslutningar för Azure Logic Apps](../connectors/apis-list.md)
