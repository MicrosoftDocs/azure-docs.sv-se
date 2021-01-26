---
title: Lägg till och anropa funktioner från Azure Logic Apps
description: Anropa och kör anpassad kod i funktioner som görs i Azure från automatiserade uppgifter och arbets flöden i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791887"
---
# <a name="call-functions-from-azure-logic-apps"></a>Anropa funktioner från Azure Logic Apps

När du vill köra kod som utför ett särskilt jobb i dina Logic Apps kan du skapa en egen funktion med hjälp av [Azure Functions](../azure-functions/functions-overview.md). Den här tjänsten hjälper dig skapa funktioner i Node.js, C# och F# så att du inte behöver skapa en komplett app eller infrastruktur för att köra kod. Du kan också anropa Logi Kap par [inifrån Functions](#call-logic-app). Azure Functions tillhandahåller data behandling utan server i molnet och är användbart för att utföra uppgifter som exempel:

* Utöka din Logic Apps beteende med Functions i Node.js eller C#.
* Utför beräkningar i ditt Logic app-arbetsflöde.
* Använd avancerade format eller beräknings fält i dina Logic Apps.

Om du vill köra kodfragment utan att använda Azure Functions, lär du dig hur du [lägger till och kör infogad kod](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integreringen mellan Logic Apps och Azure Functions fungerar för närvarande inte med aktiverade platser.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En Function-app, som är en behållare för en funktion som skapas i Azure Functions, tillsammans med den funktion som du skapar. Om du inte har en Function-app måste du [först skapa din Function-app](../azure-functions/functions-get-started.md). Du kan sedan skapa din funktion antingen utanför din Logic app i Azure Portal eller inifrån [din Logic app](#create-function-designer) i Logic App Designer.

* När du arbetar med Logic Apps gäller samma krav för Function-appar och fungerar oavsett om de är befintliga eller nya:

  * Din Function-app och Logic-appen måste använda samma Azure-prenumeration.

  * Nya Function-appar måste använda antingen .NET eller Java Script som körnings stack. När du lägger till en ny funktion i befintliga Function-appar kan du välja antingen C# eller Java Script.

  * Din funktion använder mallen för **http-utlösare** .

    Mallen för HTTP-utlösare kan acceptera innehåll som har `application/json` typen från din Logic app. När du lägger till en funktion i din Logic app, visar Logic Apps designer anpassade funktioner som skapas från den här mallen i din Azure-prenumeration.

  * Din funktion använder inte anpassade vägar om du inte har definierat en [openapi-definition](../azure-functions/functions-openapi-definition.md) (kallades tidigare en Swagger- [fil](https://swagger.io/)).

  * Om du har en OpenAPI-definition för din funktion ger Logic Apps designer en rikare upplevelse när du arbetar med funktions parametrar. Innan din Logi Kap par kan hitta och komma åt funktioner som har OpenAPI-definitioner, [ställer du in din Function-app genom att följa dessa steg](#function-swagger).

* Den Logic app där du vill lägga till funktionen, inklusive en [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) som det första steget i din Logic app

  Innan du kan lägga till åtgärder som kör Functions måste din Logic-app starta med en utlösare. Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabb start: skapa din första Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Hitta funktioner som har OpenAPI beskrivningar

För en rikare upplevelse när du arbetar med funktions parametrar i Logic Apps designer [genererar du en openapi-definition](../azure-functions/functions-openapi-definition.md), som tidigare kallades en [Swagger-fil](https://swagger.io/)för din funktion. Följ dessa steg om du vill konfigurera en Function-app så att din Logi Kap par kan hitta och använda funktioner som har Swagger beskrivningar:

1. Kontrol lera att din Function-App körs aktivt.

1. I din Function-app ställer du in [resurs delning mellan ursprung (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) så att alla ursprung tillåts genom att följa dessa steg:

   1. I listan **Function Apps** väljer du din Function-app. Välj **Platform features** CORS i den högra rutan  >  .

      ![Välj din Function-app > "plattforms funktioner" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. Under **CORS** lägger du till jokertecknet asterisk ( **`*`** ), men tar bort alla andra ursprung i listan och väljer **Spara**.

      ![Ange "CORS * till jokertecknet" * "](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Få åtkomst till egenskaps värden i HTTP-begäranden

Webhook-funktioner kan godkänna HTTP-begäranden som indata och skicka dessa förfrågningar till andra funktioner. Även om Logic Apps har [funktioner som konverterar datetime-värden](../logic-apps/workflow-definition-language-functions-reference.md), visar den här grundläggande exempel-JavaScript-funktionen hur du kan komma åt en egenskap i ett Request-objekt som skickas till funktionen och utföra åtgärder på egenskap svärdet. För att få åtkomst till egenskaper inuti objekt använder det här exemplet [punkt operatorn (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Det här är vad som händer i den här funktionen:

1. Funktionen skapar en `data` variabel och tilldelar `body` objektet i `request` objektet till variabeln. Funktionen använder punkt operatorn (.) för att referera till `body` objektet i `request` objektet:

   ```javascript
   var data = request.body;
   ```

1. Funktionen kan nu komma åt `date` egenskapen via `data` variabeln och konvertera egenskap svärdet från datetime-typen till DateString-typ genom att anropa `ToDateString()` funktionen. Funktionen returnerar även resultatet via `body` egenskapen i funktionens svar:

   ```javascript
   body: data.date.ToDateString();
   ```

Nu när du har skapat din funktion i Azure följer du stegen för att [lägga till funktioner i Logic Apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Skapa funktioner i Logic Apps

Du kan skapa funktioner direkt från din Logic Apps-arbetsflöde med hjälp av den inbyggda Azure Functions åtgärden i Logic App Designer, men du kan bara använda den här metoden för funktioner som skrivits i Java Script. För andra språk kan du skapa funktioner med hjälp av Azure Functionss upplevelsen i Azure Portal. Mer information finns i [skapa din första funktion i Azure Portal](../azure-functions/functions-get-started.md).

Innan du kan skapa din funktion i Azure måste du dock redan ha en Function-app, som är en behållare för dina funktioner. Om du inte har en Function-app skapar du först den Function-appen. Se [skapa din första funktion i Azure Portal](../azure-functions/functions-get-started.md).

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Skapa och Lägg till din funktion genom att följa det steg som gäller för ditt scenario:

   * Under det sista steget i din Logic app-arbetsflöde väljer du **nytt steg**.

   * Mellan befintliga steg i din Logic app-arbetsflöde flyttar du musen över pilen, väljer plus tecknet (+) och väljer sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "Azure Functions" som filter. I listan åtgärder väljer du åtgärden **Välj en Azure-funktion** , till exempel:

   ![Hitta funktioner i Azure Portal.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. I listan Function Apps väljer du din Function-app. När du har öppnat åtgärds listan väljer du den här åtgärden: **Skapa ny funktion**.

   ![Välj din Function-app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. Definiera din funktion i funktions definitions redigeraren:

   1. Ange ett namn för din funktion i rutan **funktions namn** .

   1. I rutan **kod** lägger du till din kod i funktions mal len, inklusive det svar och den nytto last som du vill ska returneras till din Logic-app när funktionen har körts. När du är färdig väljer du **Skapa**.

   Ett exempel:

   ![Definiera din funktion](./media/logic-apps-azure-functions/add-code-function-definition.png)

   I mallens kod refererar *`context` objektet* till meddelandet som din Logic App skickar via fältet **begär ande text** i ett senare steg. Använd följande syntax för att komma åt `context` objektets egenskaper inifrån din funktion:

   `context.body.<property-name>`

   Om du till exempel vill referera till `content` egenskapen i `context` objektet använder du följande syntax:

   `context.body.content`

   Mallkod innehåller också en `input` variabel som lagrar värdet från `data` parametern så att din funktion kan utföra åtgärder på det värdet. I JavaScript-funktioner `data` är variabeln också en genväg för `context.body` .

   > [!NOTE]
   > Den `body` här egenskapen gäller för `context` objektet och är inte samma som **Body** -token från en åtgärds utdata, som du också kan skicka till din funktion.

1. I rutan **begär ande innehåll** anger du funktionens indatatyper, som måste formateras som ett JavaScript Object Notation (JSON)-objekt.

   Detta är det *kontext objekt* eller meddelande som din Logic App skickar till din funktion. När du klickar i fältet **begär ande text** visas den dynamiska innehålls listan så att du kan välja tokens för utdata från föregående steg. Det här exemplet anger att kontextens nytto Last innehåller en egenskap med namnet `content` som har värdet **från** token från e-utlösaren.

   !["Begär ande text" exempel – nytto last för kontext objekt](./media/logic-apps-azure-functions/function-request-body-example.png)

   Här omvandlas inte context-objektet som en sträng, så objektets innehåll läggs direkt till i JSON-nyttolasten. Men när context-objektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris, får du ett fel meddelande. Så, om det här exemplet använde den **mottagna** tidstoken i stället, kan du omvandla objektet som en sträng genom att lägga till dubbla citat tecken.

   ![Omvandla objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Om du vill ange andra uppgifter, t. ex. vilken metod som ska användas, begärandehuvuden eller frågeparametrar, eller autentisering, öppnar du listan **Lägg till ny parameter** och väljer de alternativ som du vill använda. För autentisering skiljer sig dina alternativ åt beroende på vilken funktion du har valt. Se [aktivera autentisering för functions](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Lägg till befintliga funktioner i Logic Apps

Om du vill anropa befintliga funktioner från dina Logi Kap par kan du lägga till funktioner som vilken annan åtgärd som helst i Logic Apps designer.

1. I [Azure Portal](https://portal.azure.com)öppnar du din Logic app i Logic Apps designer.

1. Under steget där du vill lägga till funktionen väljer du **nytt steg**.

1. Under **Välj en åtgärd** i rutan Sök anger du "Azure Functions" som filter. I listan åtgärder väljer du åtgärden **Välj en Azure-funktion** .

   ![Hitta en funktion i Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. I listan Function Apps väljer du din Function-app. När funktions listan visas väljer du din funktion.

   ![Välj din Function-app och-funktion](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   För funktioner som har API-definitioner (Swagger beskrivningar) och är konfigurerade så att din Logi Kap par [kan hitta och komma åt dessa funktioner](#function-swagger)kan du välja **Swagger-åtgärder**.

   ![Välj Function-appen, "Swagger-åtgärder" och din funktion](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. I rutan **begär ande innehåll** anger du funktionens indatatyper, som måste formateras som ett JavaScript Object Notation (JSON)-objekt.

   Detta är det *kontext objekt* eller meddelande som din Logic App skickar till din funktion. När du klickar i fältet **begär ande text** visas den dynamiska innehålls listan så att du kan välja tokens för utdata från föregående steg. Det här exemplet anger att kontextens nytto Last innehåller en egenskap med namnet `content` som har värdet **från** token från e-utlösaren.

   !["Begär ande text" exempel – nytto last för kontext objekt](./media/logic-apps-azure-functions/function-request-body-example.png)

   Här omvandlas inte context-objektet som en sträng, så objektets innehåll läggs direkt till i JSON-nyttolasten. Men när context-objektet inte är en JSON-token som skickar en sträng, ett JSON-objekt eller en JSON-matris, får du ett fel meddelande. Så, om det här exemplet använde den **mottagna** tidstoken i stället, kan du omvandla kontext objekt som en sträng genom att lägga till dubbla citat tecken:

   ![Omvandla objekt som sträng](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Om du vill ange andra uppgifter, till exempel vilken metod som ska användas, begärandehuvuden, frågeparametrar eller autentisering, öppnar du listan **Lägg till ny parameter** och väljer de alternativ som du vill använda. För autentisering skiljer sig dina alternativ åt beroende på vilken funktion du har valt. Se [aktivera autentisering i funktioner](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Anropa Logic Apps från Functions

När du vill utlösa en Logi Kap par inifrån en funktion måste Logic-appen börja med en utlösare som tillhandahåller en anropad slut punkt. Du kan till exempel starta Logic-appen med **http**, **Request**, Azure- **köer** eller **Event Grid** utlösare. Inuti din funktion skickar du en HTTP POST-begäran till utlösarens URL och inkluderar den nytto last som du vill att Logic app ska bearbeta. Mer information finns i [anropa, Utlös ande eller kapsla Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>Aktivera autentisering för funktioner

För att enkelt autentisera åtkomst till andra resurser som skyddas av Azure Active Directory (Azure AD) utan att behöva logga in och ange autentiseringsuppgifter eller hemligheter, kan din Logic app använda en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) (tidigare kallat HANTERAD TJÄNSTIDENTITET eller MSI). Azure hanterar den här identiteten åt dig och hjälper till att skydda dina autentiseringsuppgifter eftersom du inte måste ange eller rotera hemligheter. Läs mer om [Azure-tjänster som har stöd för hanterade identiteter för Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Om du konfigurerar din Logic app så att den använder den systemtilldelade identiteten eller en manuellt skapad, användardefinierad identitet, kan funktionen i din Logic-app också använda samma identitet för autentisering. Mer information om stöd för autentisering av funktioner i Logic Apps finns i [lägga till autentisering i utgående samtal](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Följ dessa steg om du vill konfigurera och använda den hanterade identiteten med din funktion:

1. Aktivera den hanterade identiteten på din Logic app och konfigurera identitetens åtkomst till mål resursen. Se [autentisera åtkomst till Azure-resurser med hjälp av hanterade identiteter i Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Aktivera autentisering i din Function-och Function-app genom att följa dessa steg:

   * [Konfigurera anonym autentisering i din funktion](#set-authentication-function-app)
   * [Konfigurera Azure AD-autentisering i din Function-app](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Konfigurera anonym autentisering i din funktion

Om du vill använda din Logic Apps-hanterade identitet i din funktion måste du ställa in funktionens autentiseringsnivå på anonym. Annars genererar din Logic-app ett "BadRequest"-fel.

1. Leta upp och välj din Function-app i [Azure Portal](https://portal.azure.com). I de här stegen används "FabrikamFunctionApp" som exempel på Function-appen.

1. I fönstret Function-app väljer du **plattforms funktioner**. Under **utvecklingsverktyg** väljer du **Avancerade verktyg (kudu)**.

   ![Öppna avancerade verktyg för kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. På kudu-webbplatsens namn List väljer du **cmd** på menyn för **fel söknings konsolen** .

   ![Från menyn Felsök konsol väljer du alternativet "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. När nästa sida visas väljer du **plats**  >  **wwwroot**  >  *-funktionen* i mapplistan. I dessa steg används "FabrikamAzureFunction" som exempel funktion.

   ![Välj "plats" > "wwwroot" > din funktion](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Öppna `function.json` filen för redigering.

   ![Klicka på Redigera för filen function.jspå](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. I `bindings` objektet kontrollerar du om `authLevel` egenskapen finns. Om egenskapen finns anger du egenskap svärdet till `anonymous` . Annars lägger du till egenskapen och anger värdet.

   ![Lägg till egenskapen "authLevel" och ange värdet "Anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. När du är klar sparar du inställningarna och fortsätter sedan till nästa avsnitt.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Konfigurera Azure AD-autentisering för din Function-app

Innan du startar den här uppgiften kan du söka efter och ställa in dessa värden för senare användning:

* Objekt-ID: t som genereras för den systemtilldelade identitet som representerar din Logic app

  * Om du vill generera detta objekt-ID [aktiverar du din Logic Apps systemtilldelade identitet](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * Annars kan du hitta detta objekt-ID genom att öppna din Logic app i Logic App Designer. På din Logic app-meny, under **Inställningar**, väljer du **identitet**  >  **system tilldelad**.

* Katalog-ID för din klient i Azure Active Directory (Azure AD)

  Du kan köra PowerShell-kommandot för att hämta klientens katalog-ID [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) . Eller så följer du de här stegen i Azure Portal:

  1. Leta upp och välj din Function-app i [Azure Portal](https://portal.azure.com).

  1. Hitta och välj din Azure AD-klient. I de här stegen används "Fabrikam" som exempel klient.

  1. På klientens meny, under **Hantera**, väljer du **Egenskaper**.

  1. Kopiera klient organisationens katalog-ID, till exempel, och spara detta ID för senare användning.

     ![Hitta och kopiera Azure AD-klientens katalog-ID](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* Resurs-ID för den mål resurs som du vill komma åt

  * Du hittar dessa resurs-ID: n genom att granska de [Azure-tjänster som har stöd för Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Detta resurs-ID måste exakt matcha det värde som Azure AD förväntar sig, inklusive eventuella avslutande snedstreck.

  Detta resurs-ID är också samma värde som du senare använder i **Audience** -egenskapen när du [ställer in funktions åtgärden för att använda den systemtilldelade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Nu är du redo att konfigurera Azure AD-autentisering för din Function-app.

1. Leta upp och välj din Function-app i [Azure Portal](https://portal.azure.com).

1. I fönstret Function-app väljer du **plattforms funktioner**. Under **nätverk** väljer du **autentisering/auktorisering**.

   ![Visa inställningar för autentisering och auktorisering](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Ändra inställningen för **App Service autentisering** till **på**. Välj **Logga in med Azure Active Directory** i listan **åtgärd som ska vidtas när begäran inte är autentiserad** . Under **autentiseringsproviders** väljer du **Azure Active Directory**.

   ![Aktivera autentisering med Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. I fönstret **Azure Active Directory inställningar** följer du dessa steg:

   1. Ange **hanterings läge** till **Avancerat**.

   1. I egenskapen **klient-ID** anger du objekt-ID: t för din Logic Apps system-tilldelade identitet.

   1. I egenskapen **Issuer URL** anger du URL: `https://sts.windows.net/` en och lägger till din Azure AD-klients katalog-ID.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. I egenskapen **tillåtna token för token** anger du resurs-ID för den mål resurs som du vill komma åt.

      Detta resurs-ID är samma värde som du senare använder i **Audience** -egenskapen när du [ställer in funktions åtgärden för att använda den systemtilldelade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   I det här läget ser din version ut ungefär som i det här exemplet:

   ![Azure Active Directory autentiseringsinställningar](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. När du är klar väljer du **Ok**.

1. Gå tillbaka till Logic Apps designer och följ [stegen för att autentisera åtkomst med den hanterade identiteten](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [Logic Apps anslutningar](../connectors/apis-list.md)