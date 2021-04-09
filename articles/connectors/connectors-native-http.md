---
title: Anropa tjänst slut punkter med HTTP eller HTTPS
description: Skicka utgående HTTP-eller HTTPS-begäranden till tjänst slut punkter från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 02/18/2021
tags: connectors
ms.openlocfilehash: dab5b755347e46d8d509e8014bba8f496ca9c900
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719448"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Anropa tjänstslutpunkter via HTTP eller HTTPS från Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och den inbyggda http-utlösaren eller åtgärden kan du skapa automatiserade uppgifter och arbets flöden som kan skicka utgående förfrågningar till slut punkter på andra tjänster och system via http eller https. Använd den inbyggda [åtgärden utlösare och svar](../connectors/connectors-native-reqres.md)för att ta emot och svara på inkommande https-anrop i stället.

Du kan till exempel övervaka en tjänst slut punkt för din webbplats genom att kontrol lera slut punkten enligt ett angivet schema. När den angivna händelsen inträffar i slut punkten, till exempel om din webbplats går nedåt, utlöser händelsen din Logic app-arbetsflöde och kör åtgärderna i det arbets flödet.

* Om du vill kontrol lera eller *polla* en slut punkt i ett återkommande schema [lägger du till http-utlösaren](#http-trigger) som det första steget i arbets flödet. Varje gången som utlösaren kontrollerar slut punkten anropar utlösaren eller skickar en *begäran* till slut punkten. Svaret på slut punkten avgör om din Logic Apps-arbetsflöde körs. Utlösaren skickar allt innehåll från slut punktens svar till åtgärder i din Logic app.

* [Lägg till HTTP-åtgärden](#http-action)om du vill anropa en slut punkt från någon annan plats i arbets flödet. Svaret på slut punkten avgör hur arbets flödets återstående åtgärder ska köras.

Den här artikeln visar hur du använder HTTP-utlösare och HTTP-åtgärder så att din Logi Kap par kan skicka utgående anrop till andra tjänster och system.

Information om kryptering, säkerhet och auktorisering för utgående samtal från din Logic app, t. ex. [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), som tidigare kallades Secure SOCKETS Layer (SSL), självsignerade certifikat eller [Azure Active Directory öppen autentisering (Azure AD OAuth)](../active-directory/develop/index.yml)finns i [skydda åtkomst och data åtkomst för utgående anrop till andra tjänster och system](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* URL: en för den mål slut punkt som du vill anropa

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md)?

* Den Logic-app från vilken du vill anropa mål slut punkten. Börja med HTTP-utlösaren genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda HTTP-åtgärden startar du din Logic-app med valfri utlösare som du vill använda. I det här exemplet används HTTP-utlösaren som det första steget.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>Lägg till en HTTP-utlösare

Den här inbyggda utlösaren gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din tomma Logic-app i Logic App Designer.

1. Välj **inbyggd i** rutan Sök i designer. I rutan Sök anger `http` du som filter. Välj **http-** utlösare i listan **utlösare** .

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

   I det här exemplet byter du ut utlösaren till "HTTP-utlösare" så att steget får ett mer beskrivande namn. Exemplet lägger också till en HTTP-åtgärd och båda namnen måste vara unika.

1. Ange värdena för de [http trigger-parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) som du vill inkludera i anropet till mål slut punkten. Konfigurera upprepningen för hur ofta du vill att utlösaren ska kontrol lera mål slut punkten.

   ![Ange parametrar för HTTP-utlösare](./media/connectors-native-http/http-trigger-parameters.png)

   Om du väljer en annan autentiseringstyp än **ingen** är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information om autentiseringstyper som är tillgängliga för HTTP finns i följande avsnitt:

   * [Lägg till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Fortsätt att skapa ditt Logic Apps-arbetsflöde med åtgärder som körs när utlösaren utlöses.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>Lägg till en HTTP-åtgärd

Den här inbyggda åtgärden gör ett HTTP-anrop till den angivna URL: en för en slut punkt och returnerar ett svar.

1. Logga in på [Azure-portalen](https://portal.azure.com). Öppna din Logic app i Logic App Designer.

   I det här exemplet används HTTP-utlösaren som det första steget.

1. Under steget där du vill lägga till HTTP-åtgärden väljer du **nytt steg**.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger `http` du som filter. Välj **http-** åtgärd i listan **åtgärder** .

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   Det här exemplet byter namn på åtgärden till "HTTP-åtgärd" så att steget har ett mer beskrivande namn.

1. Ange värdena för de [http-åtgärds parametrar](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) som du vill ta med i anropet till mål slut punkten.

   ![Ange HTTP-åtgärds parametrar](./media/connectors-native-http/http-action-parameters.png)

   Om du väljer en annan autentiseringstyp än **ingen** är autentiseringsinställningarna annorlunda beroende på ditt val. Mer information om autentiseringstyper som är tillgängliga för HTTP finns i följande avsnitt:

   * [Lägg till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Autentisera åtkomst till resurser med hanterade identiteter](../logic-apps/create-managed-service-identity.md)

1. Om du vill lägga till andra tillgängliga parametrar öppnar du listan **Lägg till ny parameter** och väljer de parametrar som du vill använda.

1. Kom ihåg att spara din Logic app när du är klar. I verktygsfältet designer väljer du **Spara**.

## <a name="trigger-and-action-outputs"></a>Utlösare och åtgärds utdata

Här är mer information om utdata från en HTTP-utlösare eller åtgärd som returnerar denna information:

| Egenskap | Typ | Beskrivning |
|----------|------|-------------|
| `headers` | JSON-objekt | Huvudena från begäran |
| `body` | JSON-objekt | Objektet med bröd text innehållet från begäran |
| `status code` | Integer | Status koden från begäran |
|||

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

## <a name="content-with-multipartform-data-type"></a>Innehåll med multipart/form-datatyp

Om du vill hantera innehåll som har en `multipart/form-data` typ i HTTP-begäranden kan du lägga till ett JSON-objekt som innehåller `$content-type` `$multipart` attributen och för HTTP-begärans text med det här formatet.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Anta till exempel att du har en logisk app som skickar en HTTP POST-begäran för en Excel-fil till en webbplats genom att använda webbplatsens API, som stöder `multipart/form-data` typen. Så här kan den här åtgärden se ut:

![Multiform-data för multipart](./media/connectors-native-http/http-action-multipart.png)

Här är samma exempel som visar HTTP-åtgärdens JSON-definition i den underliggande arbets flödes definitionen:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>Innehåll med Application/x-www-form-urlencoded-typ

Om du vill tillhandahålla urlencoded data i bröd texten för en HTTP-begäran måste du ange att data har `application/x-www-form-urlencoded` innehålls typen. Lägg till rubriken i HTTP-utlösaren eller åtgärden `content-type` . Ange värdet för header till `application/x-www-form-urlencoded` .

Anta till exempel att du har en logisk app som skickar en HTTP POST-begäran till en webbplats som stöder `application/x-www-form-urlencoded` typen. Så här kan den här åtgärden se ut:

![Skärm bild som visar en HTTP-begäran med rubriken "Content-Type" inställd på "Application/x-www-form-urlencoded"](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Svars beteende för asynkron begäran

Som standard följer alla HTTP-baserade åtgärder i Azure Logic Apps standard mönstret för [asynkron åtgärd](/azure/architecture/patterns/async-request-reply). Det här mönstret anger att efter att en HTTP-åtgärd anropar eller skickar en begäran till en slut punkt, tjänst, system eller API, returnerar mottagaren omedelbart ett ["202 godkänt"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) -svar. Den här koden bekräftar att mottagaren har accepterat begäran men inte avslutat bearbetning. Svaret kan innehålla ett `location` huvud som anger URL: en och ett uppdaterings-ID som anroparen kan använda för att avsöka eller kontrol lera status för den asynkrona begäran tills mottagaren slutar bearbeta och returnerar svaret ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) eller något annat icke-202-svar. Anroparen behöver dock inte vänta på begäran att slutföra bearbetningen och kan fortsätta att köra nästa åtgärd. Mer information finns i avsnittet om [asynkron mikrotjänst integrering framtvingar sig oberoende av mikrotjänster](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* I Logic Apps designer har HTTP-åtgärden, men inte utlösaren, en **asynkron mönster** inställning som är aktive rad som standard. Den här inställningen anger att anroparen inte väntar på att bearbetningen ska slutföras och kan gå vidare till nästa åtgärd, men fortsätter att kontrol lera statusen tills bearbetningen stoppas. Om inställningen är inaktive rad anger den här inställningen att anroparen väntar på att bearbetningen ska slutföras innan den går vidare till nästa åtgärd.

  Du hittar den här inställningen genom att följa dessa steg:

  1. I HTTP-åtgärdens namn List väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

  1. Hitta inställningen för **asynkrona mönster** .

     ![Inställningen "asynkront mönster"](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP-åtgärdens underliggande JavaScript Object Notation-definition (JSON) följer implicit efter det asynkrona åtgärds mönstret.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Inaktivera asynkrona åtgärder

Ibland kanske du vill använda HTTP-åtgärdens asynkrona beteende i vissa scenarier, till exempel när du vill:

* [Undvik HTTP-tidsgräns för tids krävande uppgifter](#avoid-http-timeouts)
* [Inaktivera kontroll av plats rubriker](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>Inaktivera **asynkron mönster** inställning

1. I Logic Apps designer, i HTTP-åtgärdens namn List, väljer du knappen med tre punkter (**...**), som öppnar åtgärdens inställningar.

1. Hitta inställningen för **asynkront mönster** **, aktivera inställningen om den** är aktive rad och välj **färdig**.

   ![Inaktivera inställningen "asynkront mönster"](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Inaktivera asynkront mönster i åtgärdens JSON-definition

I HTTP-åtgärdens underliggande JSON-definition [lägger du till `"DisableAsyncPattern"` alternativet åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) i åtgärdens definition så att åtgärden följer synkront åtgärds mönster i stället. Mer information finns även i [köra åtgärder i ett synkront åtgärds mönster](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>Undvik HTTP-tidsgräns för tids krävande uppgifter

HTTP-begäranden har en [tids](../logic-apps/logic-apps-limits-and-config.md#http-limits)gräns. Om du har en tids krävande HTTP-åtgärd som har nått tids gränsen på grund av den här gränsen har du följande alternativ:

* [Inaktivera HTTP-åtgärdens asynkrona åtgärds mönster](#disable-asynchronous-operations) så att åtgärden inte avsöks kontinuerligt eller kontrol lera status för begäran. I stället väntar åtgärden på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

* Ersätt HTTP-åtgärden med [åtgärden http-webhook](../connectors/connectors-native-webhook.md), som väntar på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Inaktivera kontroll av plats rubriker

Vissa slut punkter, tjänster, system eller API: er returnerar ett "202 ACCEPTERAt"-svar som inte har någon `location` rubrik. För att undvika att en HTTP-åtgärd kontinuerligt kontrollerar status för begäran när `location` rubriken inte finns kan du välja mellan följande alternativ:

* [Inaktivera HTTP-åtgärdens asynkrona åtgärds mönster](#disable-asynchronous-operations) så att åtgärden inte avsöks kontinuerligt eller kontrol lera status för begäran. I stället väntar åtgärden på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

* Ersätt HTTP-åtgärden med [åtgärden http-webhook](../connectors/connectors-native-webhook.md), som väntar på att mottagaren ska svara med status och resultat när begäran har slutfört bearbetningen.

## <a name="known-issues"></a>Kända problem

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Utelämnade HTTP-huvuden

Om en HTTP-utlösare eller åtgärd inkluderar dessa huvuden, tar Logic Apps bort huvudena från det genererade begär ande meddelandet utan att visa någon varning eller ett fel:

* `Accept-*` huvuden utom för `Accept-version`
* `Allow`
* `Content-*` huvuden förutom `Content-Disposition` , `Content-Encoding` , och `Content-Type` , som används när du använder post-och placerings åtgärder. Logic Apps släpper dock dessa huvuden när du använder åtgärden Hämta.
* `Cookie` sidhuvud, men Logic Apps följer alla värden som du anger med egenskapen **cookie** .
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Även om Logic Apps inte hindrar dig från att spara Logi Kap par som använder en HTTP-utlösare eller en åtgärd med dessa huvuden, Logic Apps ignorerar dessa huvuden.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer information om utlösare och åtgärds parametrar finns i följande avsnitt:

* [Parametrar för HTTP-utlösare](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [Parametrar för HTTP-åtgärd](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>Nästa steg

* [Säker åtkomst och data åtkomst för utgående anrop till andra tjänster och system](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [Anslutningsappar för Logic Apps](../connectors/apis-list.md)
