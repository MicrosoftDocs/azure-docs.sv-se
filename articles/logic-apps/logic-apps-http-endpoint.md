---
title: Anropa, utlösa eller kapsla Logi Kap par genom att använda begär ande utlösare
description: Konfigurera HTTPS-slutpunkter för att anropa, utlösa eller kapsla Logic app-arbetsflöden i Azure Logic Apps
services: logic-apps
ms.workload: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 11/19/2020
ms.openlocfilehash: b345168dad63b1846d46c12721587eaffb5f887e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94981212"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-https-endpoints-in-azure-logic-apps"></a>Anropa, utlösa eller kapsla Logi Kap par genom att använda HTTPS-slutpunkter i Azure Logic Apps

För att din Logi Kap par ska kunna anropas via en URL och kunna ta emot inkommande begär Anden från andra tjänster, kan du på ett enhetligt sätt exponera en synkron HTTPS-slutpunkt genom att använda en begäran-baserad utlösare på din Logic app. Med den här funktionen kan du anropa din Logic app från andra Logi Kap par och skapa ett mönster för slut punkter som kan anropas. Om du vill konfigurera en slut punkt som kan anropas för att hantera inkommande samtal kan du använda någon av följande utlösnings typer:

* [Förfrågan](../connectors/connectors-native-reqres.md)
* [HTTP-webhook](../connectors/connectors-native-webhook.md)
* Hanterade anslutnings utlösare som har [typen ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) och kan ta emot inkommande HTTPS-begäranden

Den här artikeln visar hur du skapar en anropad slut punkt i din Logic app genom att använda begär ande utlösare och anropa slut punkten från en annan Logic app. Alla principer gäller identiskt med de andra utlösare typer som du kan använda för att ta emot inkommande begär Anden.


Mer information om säkerhet finns i auktorisering och kryptering för inkommande samtal till din Logi Kap par, till exempel [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), som tidigare kallades Secure SOCKETS Layer (SSL), [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), visar din Logic-app med Azure API Management eller begränsar IP-adresserna som har inkommande anrop, se [säker åtkomst och data åtkomst för inkommande anrop till begär ande-baserade utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den Logic app där du vill använda utlösaren för att skapa en slut punkt som kan anropas. Du kan börja med antingen en tom Logic-app eller en befintlig Logic-app där du kan ersätta den aktuella utlösaren. Det här exemplet börjar med en tom Logic-app. Om du inte har använt Logic Apps, se [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-callable-endpoint"></a>Skapa en slut punkt som går att anropa

1. Logga in på [Azure-portalen](https://portal.azure.com). Skapa och öppna en tom Logic-app i Logic App Designer.

1. Välj **inbyggd i** rutan Sök. I rutan Sök anger `request` du som filter. Välj **när en HTTP-begäran tas emot** från listan utlösare.

   ![Sök efter och välj begär ande utlösare](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Du kan också ange ett JSON-schema som beskriver den nytto last eller de data som du förväntar dig att utlösaren ska ta emot i rutan **JSON-schema för begär ande text** .

   Designern använder det här schemat för att skapa tokens som representerar utlösare. Du kan sedan enkelt referera till dessa utdata i din Logic app-arbetsflöde. Läs mer om [tokens som genereras från JSON-scheman](#generated-tokens).

   I det här exemplet anger du följande schema:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![Ange JSON-schema för begär ande åtgärden](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Du kan också skapa ett JSON-schema genom att ange en exempel nytto last:

   1. I utlösaren för **begäran** väljer **du Använd exempel nytto last för att generera schemat**.

   1. I rutan **Ange eller klistra in ett exempel-JSON-nyttolast** , anger du exempelets nytto Last, till exempel:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. När du är klar väljer du **klar**.

      I rutan **begär text-JSON-schema** visas nu det genererade schemat.

1. Spara logikappen.

   I rutan **http post URL** visas nu den genererade återanrops-URL som andra tjänster kan använda för att anropa och utlösa din Logic app. URL: en innehåller frågeparametrar som anger en nyckel för signatur för delad åtkomst (SAS) som används för autentisering.

   ![URL för återanrop har genererats för slut punkt](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

1. För att kopiera återanrops-URL: en, har du följande alternativ:

   * Till höger om rutan **http post URL** väljer du **Kopiera URL** (Kopiera fil ikon).

   * Gör det här anropet med hjälp av metoden som utlösaren för begäran förväntar sig. I det här exemplet används `POST` metoden:

     `POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01`

   * Kopiera återanrops-URL: en från din Logic Apps **översikts** fönster.

     1. På din Logic Apps-meny väljer du **Översikt**.

     1. I avsnittet **Sammanfattning** väljer du **Se utlösarens historik**.

        ![Hämta slut punkts-URL från Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

     1. Under **återanrops-URL [post]**, kopierar du URL: en:

        ![Kopiera slut punkts-URL från Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url-post.png)

<a name="select-method"></a>

## <a name="select-expected-request-method"></a>Välj förväntad metod för begäran

Som standard förväntas utlösaren av begäran en `POST` begäran. Du kan dock ange en annan metod som Anroparen måste använda, men bara en enda metod.

1. Öppna listan **Lägg till ny parameter** i begär ande utlösare och välj **metod**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen "metod" i utlösare](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. I listan **metod** väljer du den metod som utlösaren ska förvänta sig i stället. Du kan också ange en anpassad metod.

   Välj till exempel metoden **Get** så att du kan testa slut punktens URL senare.

   ![Välj metod för begäran som förväntas av utlösaren](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

<a name="endpoint-url-parameters"></a>

## <a name="pass-parameters-through-endpoint-url"></a>Skicka parametrar via slut punkts-URL

När du vill acceptera parameter värden via slut punktens URL har du följande alternativ:

* [Acceptera värden via get parametrar](#get-parameters) eller URL-parametrar.

  Dessa värden skickas som namn-värde-par i slut punktens URL. För det här alternativet måste du använda GET-metoden i din begäran-utlösare. I en efterföljande åtgärd kan du hämta parametervärdena som utlöser utdata genom att använda `triggerOutputs()` funktionen i ett uttryck.

* [Acceptera värden via en relativ sökväg](#relative-path) för parametrar i din begäran-utlösare.

  Dessa värden skickas via en relativ sökväg i slut punktens URL. Du måste också uttryckligen [välja den metod](#select-method) som utlösaren förväntar sig. I en efterföljande åtgärd kan du hämta parametervärdena som utlöser utdata genom att referera till dessa utdata direkt.

<a name="get-parameters"></a>

### <a name="accept-values-through-get-parameters"></a>Acceptera värden via GET-parametrar

1. Öppna **listan Lägg till ny parameter** i begär ande utlösare, Lägg till egenskapen **metod** i utlösaren och välj **Get** -metoden.

   Mer information finns i [Välj metod för förväntad begäran](#select-method).

1. Under begär ande utlösare lägger du till den åtgärd där du vill använda parametervärdet. I det här exemplet lägger du till **svars** åtgärden.

   1. Under utlösaren för begäran väljer du **nytt steg**  >  **Lägg till en åtgärd**.
   
   1. Under **Välj en åtgärd** går du till rutan Sök och anger `response` som ditt filter. I listan åtgärder väljer du åtgärden **svar** .

1. Följ dessa steg om du vill bygga det `triggerOutputs()` uttryck som hämtar parametervärdet:

   1. Klicka i egenskapen för svars åtgärdens **brödtext** så att listan med dynamiskt innehåll visas och välj **uttryck**.

   1. I rutan **uttryck** anger du det här uttrycket, ersätter `parameter-name` med parameter namnet och väljer **OK**.

      `triggerOutputs()['queries']['parameter-name']`

      ![Lägg till uttrycket "triggerOutputs ()" i utlösaren](./media/logic-apps-http-endpoint/trigger-outputs-expression.png)

      I egenskapen **Body** matchas uttrycket med `triggerOutputs()` token.

      ![Löst "triggerOutputs ()"-uttryck](./media/logic-apps-http-endpoint/trigger-outputs-expression-token.png)

      Om du sparar Logic-appen, navigerar bort från designern och återgår till designern, visar token det parameter namn som du har angett, till exempel:

      ![Löst uttryck för parameter namn](./media/logic-apps-http-endpoint/resolved-expression-parameter-token.png)

      I kodvyn visas egenskapen **Body** i svars åtgärdens definition på följande sätt:

      `"body": "@{triggerOutputs()['queries']['parameter-name']}",`

      Anta till exempel att du vill skicka ett värde för en parameter med namnet `postalCode` . Egenskapen **Body** anger strängen, `Postal Code: ` med ett avslutande blank steg, följt av motsvarande uttryck:

      ![Lägg till exempel "triggerOutputs ()"-uttrycket för att utlösa](./media/logic-apps-http-endpoint/trigger-outputs-expression-postal-code.png)

1. Om du vill testa den anropande slut punkten kopierar du återanrops-URL: en från begär ande utlösare och klistrar in URL: en i ett annat I URL: en lägger du till parameter namnet och värdet efter frågetecknet ( `?` ) till URL: en i följande format och trycker på RETUR.

   `...?{parameter-name=parameter-value}&api-version=2016-10-01...`

   `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?{parameter-name=parameter-value}&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   Webbläsaren returnerar ett svar med den här texten: `Postal Code: 123456`

   ![Svar från sändning av begäran till återanrops-URL](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

1. Om du vill placera parameter namnet och värdet på en annan plats i URL: en, måste du använda et-tecknet ( `&` ) som prefix, till exempel:

   `...?api-version=2016-10-01&{parameter-name=parameter-value}&...`

   Det här exemplet visar återanrops-URL: en med exempel parameterns namn och värde `postalCode=123456` i olika positioner i URL: en:

   * första position: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?postalCode=123456&api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

   * andra position: `https://prod-07.westus.logic.azure.com:433/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke?api-version=2016-10-01&postalCode=123456&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

> [!NOTE]
> Om du vill inkludera hash-eller pund symbolen ( **#** ) i URI: n använder du den här kodade versionen i stället: `%25%23`

<a name="relative-path"></a>

### <a name="accept-values-through-a-relative-path"></a>Acceptera värden via en relativ sökväg

1. Öppna listan **Lägg till ny parameter** i begär ande utlösare och välj **relativ sökväg**, som lägger till den här egenskapen i utlösaren.

   ![Lägg till egenskapen "relativ sökväg" i utlösaren](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. I egenskapen **relativ sökväg** anger du den relativa sökvägen för parametern i JSON-schemat som du vill att din URL ska acceptera, till exempel `/address/{postalCode}` .

   ![Ange den relativa sökvägen för parametern](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. Under begär ande utlösare lägger du till den åtgärd där du vill använda parametervärdet. I det här exemplet lägger du till **svars** åtgärden.

   1. Under utlösaren för begäran väljer du **nytt steg**  >  **Lägg till en åtgärd**.

   1. Under **Välj en åtgärd** går du till rutan Sök och anger `response` som ditt filter. I listan åtgärder väljer du åtgärden **svar** .

1. I svars åtgärdens egenskap **Body** inkluderar du den token som representerar den parameter som du angav i utlösarens relativa sökväg.

   Anta till exempel att du vill att svars åtgärden ska returneras `Postal Code: {postalCode}` .

   1. I egenskapen **Body** anger du `Postal Code: ` med ett avslutande blank steg. Behåll markören i redigerings rutan så att listan över dynamiskt innehåll förblir öppen.

   1. I listan med dynamiskt innehåll, från avsnittet **när en HTTP-begäran tas emot** , väljer du token **Postnr** .

      ![Lägg till den angivna parametern i svars texten](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

      Egenskapen **Body** innehåller nu den valda parametern:

      ![Exempel på svars text med parameter](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Spara logikappen.

   I utlösaren för förfrågningar uppdateras återanrops-URL: en och nu ingår den relativa sökvägen, till exempel:

   `https://prod-07.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}`

1. Om du vill testa den anropande slut punkten kopierar du den uppdaterade återanrops-URL: en från begär ande utlösaren, klistrar in webb adressen i ett annat webbläsarfönster, ersätter `{postalCode}` i URL: en med `123456` och trycker

   Webbläsaren returnerar ett svar med den här texten: `Postal Code: 123456`

   ![Svar från sändning av begäran till återanrops-URL](./media/logic-apps-http-endpoint/callback-url-returned-response.png)

> [!NOTE]
> Om du vill inkludera hash-eller pund symbolen ( **#** ) i URI: n använder du den här kodade versionen i stället: `%25%23`

## <a name="call-logic-app-through-endpoint-url"></a>Anropa Logic app via slut punkts-URL

När du har skapat slut punkten kan du utlösa Logic-appen genom att skicka en HTTPS-begäran till slut punktens fullständiga URL. Logic Apps har inbyggt stöd för slut punkter för direkt åtkomst.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Tokens som genererats från schema

När du anger ett JSON-schema i begär ande utlösaren genererar Logic App Designer tokens för egenskaperna i schemat. Du kan sedan använda dessa token för att skicka data via ditt Logic app-arbetsflöde.

Om du till exempel lägger till fler egenskaper, till exempel `"suite"` till JSON-schemat, är tokens för dessa egenskaper tillgängliga för dig i senare steg för din Logic app. Här är det fullständiga JSON-schemat:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Skapa kapslade Logic Apps

Du kan kapsla arbets flöden i din Logic app genom att lägga till andra Logic Apps som kan ta emot begär Anden. Följ dessa steg om du vill ta med dessa Logic Apps:

1. Under steget där du vill anropa en annan Logic app väljer du **nytt steg**  >  **Lägg till en åtgärd**.

1. Under **Välj en åtgärd** väljer du **inbyggt**. I rutan Sök anger `logic apps` du som filter. I listan åtgärder väljer du **Välj ett Logic Apps arbets flöde**.

   ![Kapsla Logic app i aktuell Logic app](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   Designern visar de berättigade Logic Apps som du kan välja.

1. Välj den Logic-app som ska anropas från din aktuella Logic-app.

   ![Välj Logic app som ska anropas från den aktuella Logic-appen](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Referens innehåll från en inkommande begäran

Om den inkommande begärans innehålls typ är `application/json` , kan du referera till egenskaperna i den inkommande begäran. Annars behandlas det här innehållet som en enda binär enhet som du kan skicka till andra API: er. Om du vill referera till det här innehållet i din Logic app-arbetsflöde måste du först konvertera det innehållet.

Om du till exempel skickar innehåll som är av `application/xml` typen kan du använda [ `@xpath()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#xpath) för att utföra en XPath-extrahering eller använda [ `@json()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#json) för att konvertera XML till JSON. Lär dig mer om att arbeta med [innehålls typer](../logic-apps/logic-apps-content-type.md)som stöds.

Du kan använda [ `@triggerOutputs` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)för att hämta utdata från en inkommande begäran. Anta till exempel att du har utdata som ser ut som i det här exemplet:

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

För att få åtkomst till `body` egenskapen specifikt kan du använda [ `@triggerBody()` uttrycket](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) som en genväg.

## <a name="respond-to-requests"></a>Svara på begär Anden

Ibland vill du svara på vissa förfrågningar som utlöser din Logic app genom att returnera innehåll till anroparen. Använd svars åtgärden för att skapa status kod, sidhuvud och brödtext för ditt svar. Den här åtgärden kan visas var som helst i din Logic app, inte bara i slutet av arbets flödet. Om din Logic app inte innehåller någon svars åtgärd svarar slut punkten *omedelbart* med status **202** .

För att den ursprungliga anroparen ska kunna hämta svaret måste alla nödvändiga steg för svaret slutföras inom [tids gränsen för begäran](./logic-apps-limits-and-config.md) , om inte den Utlös ande Logic-appen anropas som en kapslad Logic-app. Om inget svar returneras inom den här gränsen, tids gränsen överskrids den inkommande begäran och tar emot **408-klientens timeout** -svar.

För kapslade Logic Apps fortsätter appen för överordnad logik att vänta på ett svar tills alla steg har slutförts, oavsett hur lång tid det tar.

### <a name="construct-the-response"></a>Skapa svaret

I svars texten kan du inkludera flera huvuden och vilken typ av innehåll som helst. Det här svarets rubrik anger till exempel att svarets innehålls typ är `application/json` och att texten innehåller värden för `town` `postalCode` egenskaperna och, baserat på det JSON-schema som beskrivs tidigare i det här avsnittet för begäran utlösare.

![Tillhandahåll svars innehåll för HTTPS-svars åtgärd](./media/logic-apps-http-endpoint/content-for-response-action.png)

Svaren har följande egenskaper:

| Egenskap (Visa) | Egenskap (JSON) | Beskrivning |
|--------------------|-----------------|-------------|
| **Status kod** | `statusCode` | HTTPS-statuskod som ska användas i svaret på inkommande begäran. Den här koden kan vara vilken giltig status kod som helst som börjar med 2xx, 4xx eller 5xx. 3xx status koder är dock inte tillåtna. |
| **Sidhuvuden** | `headers` | En eller flera huvuden som ska inkluderas i svaret |
| **Brödtext** | `body` | Ett Body-objekt som kan vara en sträng, ett JSON-objekt eller till och med ett binärt innehåll som refereras från ett föregående steg |
||||

Om du vill visa JSON-definitionen för svars åtgärden och din Logic Apps fullständiga JSON-definition går du till verktygsfältet Logic App Designer och väljer **kodvyn**.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Frågor och svar

#### <a name="q-what-about-url-security"></a>F: vad gäller URL-säkerhet?

**A**: Azure på ett säkert sätt skapar callback-URL: er för Logic app med hjälp av [signaturen för delad åtkomst (SAS)](/rest/api/storageservices/delegate-access-with-shared-access-signature). Den här signaturen passerar som en frågeparameter och måste verifieras innan din Logi Kap par kan köras. Azure genererar signaturen med en unik kombination av en hemlig nyckel per Logic app, Utlösarens namn och åtgärden som utförs. Så om någon har åtkomst till den hemliga Logic app-nyckeln kan de inte generera en giltig signatur.

> [!IMPORTANT]
> För produktions-och högre säkerhets system rekommenderar vi starkt att du anropar din Logic-app direkt från webbläsaren av följande anledningar:
>
> * Den delade åtkomst nyckeln visas i URL: en.
> * Du kan inte hantera principer för säkerhets innehåll på grund av delade domäner i Azure Logic Apps kunder.

Mer information om säkerhet finns i auktorisering och kryptering för inkommande samtal till din Logi Kap par, till exempel [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), som tidigare kallades Secure SOCKETS Layer (SSL), [Azure Active Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml), visar din Logic-app med Azure API Management eller begränsar IP-adresserna som har inkommande anrop, se [säker åtkomst och data åtkomst för inkommande anrop till begär ande-baserade utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

#### <a name="q-can-i-configure-callable-endpoints-further"></a>F: kan jag konfigurera uppringnings bara slut punkter ytterligare?

**A**: Ja, https-slutpunkter har stöd för mer avancerad konfiguration via [Azure API Management](../api-management/api-management-key-concepts.md). Den här tjänsten ger också möjlighet att hantera alla dina API: er på ett konsekvent sätt, inklusive Logic Apps, konfigurera anpassade domän namn, använda fler autentiseringsmetoder med mera, till exempel:

* [Ändra metoden för begäran](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [Ändra URL-segmenten för begäran](../api-management/api-management-transformation-policies.md#RewriteURL)
* Konfigurera API Management domäner i [Azure Portal](https://portal.azure.com/)
* Konfigurera en princip för att kontrol lera grundläggande autentisering

## <a name="next-steps"></a>Nästa steg

* [Ta emot och svara på inkommande HTTPS-anrop genom att använda Azure Logic Apps](../connectors/connectors-native-reqres.md)
* [Säker åtkomst och data i Azure Logic Apps-åtkomst för inkommande anrop till begär ande-baserade utlösare](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
