---
title: Vänta och svara på händelser
description: Automatisera arbets flöden som utlöser, pausar och återupptar baserat på händelser på en tjänst slut punkt med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: cdbf853a96f319cb27c10136004a1398014e602f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109160"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Skapa och kör automatiska händelsebaserade arbets flöden genom att använda HTTP-webhookar i Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http webhook-anslutningen kan du skapa automatiserade uppgifter och arbets flöden som prenumererar på en tjänst slut punkt, vänta på vissa händelser och köra baserat på dessa händelser, i stället för att regelbundet kontrol lera eller *avsöka* den slut punkten.

Här följer några exempel på webhook-baserade arbets flöden:

* Vänta tills ett objekt tas emot från en [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) innan du utlöser en Logic app-körning.
* Vänta på ett godkännande innan du fortsätter med ett arbets flöde.

Den här artikeln visar hur du använder webhook-utlösaren och webhook-åtgärden så att din Logi Kap par kan ta emot och svara på händelser på en tjänst slut punkt.

## <a name="how-do-webhooks-work"></a>Hur fungerar Webhooks?

En webhook-utlösare är händelse-baserad, som inte är beroende av att kontrol lera eller avsökningar regelbundet för nya objekt. När du sparar en Logic-app som börjar med en webhook-utlösare, eller när du ändrar din Logi Kap par från inaktive rad till aktive rad, utlöses en återanrops-URL till den angivna tjänst slut punkten *genom att en* *återanrops-URL* registreras Utlösaren väntar sedan på att tjänst slut punkten ska anropa URL: en, som börjar köra Logic app. I likhet med [begär ande utlösaren](connectors-native-reqres.md)utlöses Logic app omedelbart när den angivna händelsen inträffar. Webhook-utlösaren *avbryter prenumerationen* från tjänst slut punkten om du tar bort utlösaren och sparar din Logic app, eller när du ändrar din Logi Kap par från aktive rad till inaktive rad.

En webhook-åtgärd är också händelse-baserad och *prenumererar* på den angivna tjänst slut punkten genom att registrera en *återanrops-URL* med den slut punkten. Webhook-åtgärden pausar Logi Kap parns arbets flöde och väntar tills tjänst slut punkten anropar URL: en innan Logic-appen återupptar körningen. Webhook-åtgärden *avbryter prenumerationen* på tjänst slut punkten i följande fall:

* När webhook-åtgärden har slutförts
* Om körningen av Logic app avbryts under väntan på svar
* Innan tids gränsen nåtts för Logic app

Till exempel är Office 365 Outlook Connector [**-e-**](connectors-create-api-office365-outlook.md) poståtgärden skicka godkännande ett exempel på en webhook-åtgärd som följer det här mönstret. Du kan utöka det här mönstret till vilken tjänst som helst med hjälp av webhook-åtgärden.

Mer information finns i de här ämnena:

* [Webhooks och prenumerationer](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Skapa anpassade API: er som stöder en webhook](../logic-apps/logic-apps-create-api-app.md)

Information om kryptering, säkerhet och auktorisering för inkommande samtal till din Logic app, till exempel [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), som tidigare kallades Secure SOCKETS Layer (SSL) eller [Azure Active Directory öppen autentisering (Azure AD OAuth)](../active-directory/develop/index.yml)finns i [skydda åtkomst och data åtkomst för inkommande anrop till begär ande-baserade utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för en redan distribuerad slut punkt eller API som har stöd för webhook-prenumerationen och prenumerations mönstret för [webhook-utlösare i Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) eller [webhook-åtgärder i Logic Apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) efter behov

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Den Logic-app där du vill vänta på specifika händelser vid mål slut punkten. Börja med HTTP webhook-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP webhook-åtgärden startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP-utlösaren som det första steget.

## <a name="add-an-http-webhook-trigger"></a>Lägg till en HTTP-webhook-utlösare

Den här inbyggda utlösaren anropar prenumerations slut punkten på mål tjänsten och registrerar en återanrops-URL med mål tjänsten. Din Logic app väntar sedan på att mål tjänsten ska skicka en `HTTP POST` begäran till återanrops-URL: en. När den här händelsen inträffar utlöses utlösaren och skickar alla data i begäran till arbets flödet.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. Skriv som ditt filter i rutan Sök i designer `http webhook` . Välj **http-webhook-** utlösaren i listan **utlösare** .

   ![Välj utlösare för HTTP-webhook](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   I det här exemplet byter du ut utlösaren till `HTTP Webhook trigger` så att steget har ett mer beskrivande namn. Exemplet lägger också till en HTTP-webhook-åtgärd och båda namnen måste vara unika.

1. Ange värdena för de [Parametrar för http-webhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) som du vill använda för prenumerations-och uppsägnings samtal.

   I det här exemplet innehåller utlösaren de metoder, URI: er och meddelande texter som ska användas vid prenumerations-och uppsägnings åtgärder.

   ![Ange Utlösar parametrarna för HTTP-webhook](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Prenumerations metod** | Ja | Den metod som ska användas för att prenumerera på mål slut punkten |
   | **Prenumerera-URI** | Ja | Den URL som ska användas för att prenumerera på mål slut punkten |
   | **Prenumerera-brödtext** | Inga | All meddelande text som ska tas med i prenumerationsbegäran. Det här exemplet innehåller återanrops-URL: en som unikt identifierar prenumeranten, som är din Logic app, genom `@listCallbackUrl()` att använda uttrycket för att hämta din Logi-URL för logi Kap par. |
   | **Avbryt prenumeration – metod** | Inga | Den metod som ska användas vid avbetalning från mål slut punkten |
   | **Avbryt prenumeration – URI** | Inga | Den URL som ska användas för att avsluta prenumerationen från mål slut punkten |
   | **Avbryt prenumeration – brödtext** | Inga | En valfri meddelande text som ska tas med i begäran om att avbryta prenumerationen <p><p>**Obs**: den här egenskapen stöder inte användning av `listCallbackUrl()` funktionen. Utlösaren inkluderar dock automatiskt och skickar huvuden `x-ms-client-tracking-id` och `x-ms-workflow-operation-name` , som mål tjänsten kan använda för att identifiera prenumeranten unikt. |
   ||||

1. Om du vill lägga till andra utlösnings egenskaper öppnar du listan **Lägg till ny parameter** .

   ![Lägg till fler utlösnings egenskaper](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Om du till exempel behöver använda autentisering kan du lägga till egenskaperna **Prenumerera-autentisering** och **avprenumerera-autentisering** . Mer information om autentiseringstyper som är tillgängliga för HTTP-webhook finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

   När du sparar din Logi Kap par anropas prenumerations slut punkten på mål tjänsten och återanrops-URL: en registreras. Din Logic app väntar sedan på att mål tjänsten ska skicka en `HTTP POST` begäran till återanrops-URL: en. När den här händelsen inträffar utlöses utlösaren och skickar alla data i begäran till arbets flödet. Om den här åtgärden slutförs, avbryter utlösaren från slut punkten och din Logic app fortsätter det återstående arbets flödet.

## <a name="add-an-http-webhook-action"></a>Lägg till en HTTP webhook-åtgärd

Den här inbyggda åtgärden anropar prenumerations slut punkten på mål tjänsten och registrerar en återanrops-URL med mål tjänsten. Din Logic app pausar och väntar sedan på att mål tjänsten ska skicka en `HTTP POST` begäran till återanrops-URL: en. När den här händelsen inträffar skickar åtgärden alla data i begäran till arbets flödet. Om åtgärden slutförs, avbryts åtgärden från slut punkten och din Logic app fortsätter att köra det återstående arbets flödet.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

   I det här exemplet används HTTP webhook-utlösaren som det första steget.

1. Under steget där du vill lägga till åtgärden HTTP-webhook väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Skriv som ditt filter i rutan Sök i designer `http webhook` . I listan **åtgärder** väljer du åtgärden **http-webhook** .

   ![Välj åtgärden HTTP-webhook](./media/connectors-native-webhook/select-http-webhook-action.png)

   Det här exemplet byter namn på åtgärden till "HTTP-webhook-åtgärd", så att steget har ett mer beskrivande namn.

1. Ange värdena för HTTP webhook-åtgärdens parametrar, som liknar [parametrarna för http webhook-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)som du vill använda för prenumerations-och uppsägnings samtal.

   I det här exemplet inkluderar åtgärden de metoder, URI: er och meddelande texter som ska användas när du utför prenumerations-och uppsägnings åtgärder.

   ![Ange åtgärds parametrar för HTTP-webhook](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Prenumerations metod** | Ja | Den metod som ska användas för att prenumerera på mål slut punkten |
   | **Prenumerera-URI** | Ja | Den URL som ska användas för att prenumerera på mål slut punkten |
   | **Prenumerera-brödtext** | Inga | All meddelande text som ska tas med i prenumerationsbegäran. Det här exemplet innehåller återanrops-URL: en som unikt identifierar prenumeranten, som är din Logic app, genom `@listCallbackUrl()` att använda uttrycket för att hämta din Logi-URL för logi Kap par. |
   | **Avbryt prenumeration – metod** | Inga | Den metod som ska användas vid avbetalning från mål slut punkten |
   | **Avbryt prenumeration – URI** | Inga | Den URL som ska användas för att avsluta prenumerationen från mål slut punkten |
   | **Avbryt prenumeration – brödtext** | Inga | En valfri meddelande text som ska tas med i begäran om att avbryta prenumerationen <p><p>**Obs**: den här egenskapen stöder inte användning av `listCallbackUrl()` funktionen. Åtgärden tar dock automatiskt med och skickar huvuden `x-ms-client-tracking-id` och `x-ms-workflow-operation-name` , som mål tjänsten kan använda för att identifiera prenumeranten unikt. |
   ||||

1. Om du vill lägga till andra åtgärds egenskaper öppnar du listan **Lägg till ny parameter** .

   ![Lägg till fler åtgärds egenskaper](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Om du till exempel behöver använda autentisering kan du lägga till egenskaperna **Prenumerera-autentisering** och **avprenumerera-autentisering** . Mer information om autentiseringstyper som är tillgängliga för HTTP-webhook finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

   När den här åtgärden körs anropar din Logic app prenumerations slut punkten på mål tjänsten och registrerar återanrops-URL: en. Logic app pausar sedan arbets flödet och väntar på att mål tjänsten ska skicka en `HTTP POST` begäran till återanrops-URL: en. När den här händelsen inträffar skickar åtgärden alla data i begäran till arbets flödet. Om åtgärden slutförs, avbryts åtgärden från slut punkten och din Logic app fortsätter att köra det återstående arbets flödet.

## <a name="trigger-and-action-outputs"></a>Utlösare och åtgärds utdata

Här är mer information om utdata från en HTTP-webhook-utlösare eller åtgärd som returnerar denna information:

| Egenskapsnamn | Typ | Beskrivning |
|---------------|------|-------------|
| sidhuvud | objekt | Huvudena från begäran |
| body | objekt | Objektet med bröd text innehållet från begäran |
| statuskod | int | Status koden från begäran |
||||

| Statuskod | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Har godkänts |
| 400 | Felaktig begäran |
| 401 | Behörighet saknas |
| 403 | Förbjudet |
| 404 | Hittades inte |
| 500 | Internt serverfel. Ett okänt fel uppstod. |
|||

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärds parametrar, som liknar varandra, finns i [Parametrar för http-webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger).

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data åtkomst för inkommande anrop till begär ande-baserade utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
