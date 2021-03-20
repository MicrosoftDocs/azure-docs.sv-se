---
title: Exchange-meddelanden för B2B-scenarier för företags integrering
description: Ta emot och skicka B2B-meddelanden mellan handels partner i Azure Logic Apps med hjälp av Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93310467"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Ta emot och bekräfta B2B AS2-meddelanden med hjälp av Azure Logic Apps och Enterprise-integrationspaket

När du har ett integrations konto som definierar handels partner och avtal, kan du skapa ett automatiserat affärs-till-arbets-arbetsflöde (B2B) som utbyter meddelanden mellan handels partner med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) med [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps fungerar med kopplingar som stöder AS2-, X12-, EDIFACT-och RosettaNet-protokoll som är bransch standard. Du kan också kombinera dessa kopplingar med andra [anslutningar som är tillgängliga i Logic Apps](../connectors/apis-list.md), till exempel Salesforce och Office 365 Outlook.

Den här artikeln visar hur du skapar en Logi Kap par som tar emot en HTTP-begäran genom att använda en begär ande utlösare, avkodar meddelande innehållet med hjälp av åtgärderna AS2 och X12 och returnerar sedan ett svar med hjälp av svars åtgärden.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration ännu kan du [Registrera dig för ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).

* En tom Logic-app så att du kan skapa B2B-arbetsflödet genom att använda [begär](../connectors/connectors-native-reqres.md) ande utlösare som följs av följande åtgärder:

  * [AS2-avkodning](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md), som skickar ett [svar](../connectors/connectors-native-reqres.md) baserat på om AS2-avkodnings åtgärden lyckas eller Miss lyckas

  * [Avkoda X12-meddelande](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ett [integrations konto](./logic-apps-enterprise-integration-create-integration-account.md) som är associerat med din Azure-prenumeration och länkat till din Logic app. Både din Logic app och ditt integrations konto måste finnas på samma plats eller i Azure-regionen.

* Minst två [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har definierat i ditt integrations konto tillsammans med [AS2-och X12-avtal](logic-apps-enterprise-integration-agreements.md) för dessa partner.

## <a name="add-the-request-trigger"></a>Lägg till begär ande utlösare

I det här exemplet används Logic Apps designer i Azure Portal, men du kan följa liknande steg för Logic App Designer i Visual Studio.

1. I [Azure Portal](https://portal.azure.com)öppnar du din tomma Logic-app i Logic App Designer.

1. I sökrutan anger du `when a http request` och väljer **när en http-begäran tas emot** att användas som utlösare.

   ![Välj begär utlösare för att starta ditt Logic app-arbetsflöde](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Lämna rutan **JSON-schema för begär ande texten** tom eftersom X12-meddelandet är en platt fil.

   ![Lämna "JSON-schemat för begär ande texten" tomt](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. När du är klar väljer du **Spara** i verktygsfältet designer.

   Det här steget genererar **http post-URL: en** som ska användas för att skicka begäran som utlöser Logic-appen. Om du vill kopiera den här URL: en väljer du kopierings ikonen bredvid URL: en.

   ![URL som genereras för begär ande utlösare för mottagning av anrop](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>Lägg till AS2-avkodnings åtgärden

Lägg nu till de B2B-åtgärder som du vill använda. I det här exemplet används AS2-och X12-åtgärder.

1. Under utlösaren väljer du **nytt steg**. Om du vill dölja utlösnings informationen klickar du på Utlösarens namn List.

   ![Lägg till ett annat steg i ditt Logic app-arbetsflöde](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Under **Välj en åtgärd** i sökrutan anger du `as2 decode` och väljer **AS2 avkoda (v2)**.

   ![Sök efter och välj "AS2 avkodning (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. För egenskapen **meddelande att avkoda** anger du indatamängden som du vill att AS2-åtgärden ska avkoda, vilket är det `body` innehåll som tas emot av utlösaren för http-begäran. Det finns flera sätt att ange det här innehållet som indatatyp, antingen från listan med dynamiskt innehåll eller som ett uttryck:

   * Om du vill välja från en lista som visar tillgängliga utlösare klickar du i rutan **meddelande att avkoda** . När listan med dynamiskt innehåll visas, under **när en HTTP-begäran tas emot**, väljer du **text** egenskaps värde, till exempel:

     ![Välj "Body"-värde från utlösaren](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Om du vill ange ett uttryck som refererar till utlösaren `body` utdata klickar du i rutan **meddelande att avkoda** . När listan med dynamiskt innehåll visas väljer du **uttryck**. I uttrycks redigeraren anger du uttrycket här och väljer **OK**:

     `triggerOutputs()['body']`

     Eller ange det här uttrycket direkt i rutan **meddelande att avkoda** :

     `@triggerBody()`

     Uttrycket matchar **Body** -token.

     ![Matchade text utdata från utlösaren](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. För egenskapen **meddelande rubriker** anger du eventuella rubriker som krävs för åtgärden AS2, som beskrivs av det `headers` innehåll som tas emot av utlösaren för http-begäran.

   1. Om du vill ange ett uttryck som refererar till utlösaren `headers` utdata väljer du växla meddelandehuvuden **till text läge**.

      ![Skärm bild som visar "växla meddelandehuvuden till text läge" markerat.](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. Klicka **i rutan** meddelandehuvuden. När listan med dynamiskt innehåll visas väljer du **uttryck**. I uttrycks redigeraren anger du uttrycket här och väljer **OK**:

      `triggerOutputs()['Headers']`

      I AS2 avkodnings åtgärd visas nu uttrycket som en token:

      ![Skärm bild som visar @triggerOutputs token "() [" headers "] i rutan" meddelande rubriker ".](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. Växla mellan designer och kodvyn för att hämta uttrycks-token för att matcha **huvud** -token. Efter det här steget ser AS2-avkodnings åtgärden ut som i det här exemplet:

      ![Matchade meddelandehuvuden från utlösaren](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Lägg till svars åtgärd för meddelande kvitto

Om du vill meddela handels partnern att meddelandet har tagits emot kan du returnera ett svar som innehåller ett meddelande om dispositions meddelande för AS2 (MDN) med hjälp av **svars** åtgärden. Genom att lägga till den här åtgärden direkt efter **AS2 avkodnings** åtgärd, bör Logic app inte fortsätta bearbetningen om den åtgärden inte fungerar.

1. Under **AS2 avkodnings** åtgärd väljer du **nytt steg**.

1. Under **Välj en åtgärd** går du till rutan Sök och väljer **inbyggd**. Skriv `condition` i sökrutan. I listan **Åtgärder** väljer du **Villkor**.

   ![Lägg till åtgärden "villkor"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Nu visas villkors formen, inklusive sökvägar för om villkoret är uppfyllt eller inte.

   ![Skärm bild som visar villkors formen med tomma sökvägar.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Ange nu villkoret som ska utvärderas. I rutan **Välj ett värde** anger du det här uttrycket:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   I rutan i mitten ser du till att jämförelse åtgärden är inställd på `is equal to` . Ange värdet i rutan till höger `Expected` . Om du vill hämta uttrycket för att matcha den här token växlar du mellan designer och kodvy.

   ![Skärm bild som visar villkors formen med ett villkor tillagt.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Ange Svaren för att returnera om AS2- **avkodnings** åtgärden lyckas eller inte.

   1. För det fall när **AS2-avkodningen** lyckas väljer du **Lägg till en åtgärd** i formen **om True** . Under **Välj en åtgärd** i sökrutan anger `response` du och väljer **svar**.

      ![Sök efter och Välj åtgärden "svar"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Om du vill komma åt AS2-MDN från utdata för **AS2-avkodaren** anger du följande uttryck:

      * I egenskapen **rubrik** för **svars** åtgärd anger du följande uttryck:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * I egenskapen **brödtext** i **svars** åtgärd anger du följande uttryck:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Om du vill att uttryck ska matchas som tokens växlar du mellan designer och kodvyn:

      ![Matchat uttryck för att komma åt AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. För det fall då åtgärden **AS2-avkoda** Miss lyckas väljer du **Lägg till en åtgärd** i formen **om falskt** . Under **Välj en åtgärd** i sökrutan anger `response` du och väljer **svar**. Konfigurera **svars** åtgärden för att returnera den status och det fel som du vill ha.

1. Spara logikappen.

## <a name="add-decode-x12-message-action"></a>Lägg till åtgärden avkoda X12-meddelande

1. Lägg nu till åtgärden **avkoda X12-meddelande** . Under **svars** åtgärden väljer du **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** i sökrutan anger du `x12 decode` och väljer **avkoda X12-meddelande**.

   ![Sök efter och Välj åtgärden "avkoda X12-meddelande"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Om X12-åtgärden efterfrågar anslutnings information anger du namnet på anslutningen, väljer det integrations konto som du vill använda och väljer sedan **skapa**.

   ![Skapa X12-anslutning till integrations konto](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Ange nu indatamängden för X12-åtgärden. I det här exemplet används utdata från åtgärden AS2, vilket är meddelande innehållet, men Observera att innehållet är i JSON-format och att det är Base64-kodat. Så du måste konvertera det här innehållet till en sträng.

   I rutan **X12 platt fil meddelande att avkoda** anger du det här uttrycket för att konvertera AS2 utdata:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Om du vill hämta uttrycket för att matcha den här token växlar du mellan designer och kodvy.

    ![Konvertera Base64-kodat innehåll till en sträng](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Spara logikappen.

   Om du behöver ytterligare anvisningar för den här Logic-appen, till exempel för att avkoda meddelande innehållet och mata ut innehållet i JSON-objekt, fortsätter du att skapa din Logic app.

Nu är du klar med att konfigurera B2B-Logic-appen. I en verklig värld kanske du vill lagra de avkodade X12-data i en affärsrelaterad app eller ett data lager. Se till exempel följande artiklar:

* [Ansluta till SAP-system från Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Övervaka, skapa och hantera SFTP-filer med hjälp av SSH och Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Om du vill ansluta dina egna LOB-appar och använda dessa API: er i din Logic app kan du lägga till fler åtgärder eller [skriva anpassade API: er](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Nästa steg

* [Ta emot och svara på inkommande HTTPS-anrop](../connectors/connectors-native-reqres.md)
* [Exchange AS2-meddelanden för B2B Enterprise-integration](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12-meddelanden för B2B Enterprise-integration](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Läs mer om [Enterprise-integrationspaket](../logic-apps/logic-apps-enterprise-integration-overview.md)
