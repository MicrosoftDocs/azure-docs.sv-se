---
title: 'Självstudie – importera och hantera API: er – Azure API Management och Visual Studio Code | Microsoft Docs'
description: 'I den här självstudien får du lära dig hur du använder Azure API Management-tillägget för Visual Studio Code för att importera, testa och hantera API: er.'
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649554"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Självstudie: Använd API Management-tillägget för Visual Studio Code för att importera och hantera API: er

I den här självstudien får du lära dig hur du använder API Management-tillägget för Visual Studio Code för vanliga åtgärder i API Management. Använd den välkända Visual Studio Code-miljön för att importera, uppdatera, testa och hantera API: er.

Lär dig att:

> [!div class="checklist"]
> * Importera ett API till API Management
> * Redigera API
> * Tillämpa API Management principer
> * Testa API:et


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API i API Management-tillägget":::

En introduktion till ytterligare API Management funktioner finns i självstudierna för API Management med hjälp av [Azure Portal](import-and-publish.md).

## <a name="prerequisites"></a>Förutsättningar
- Förstå [Azure API Management terminologi](api-management-terminology.md)
- Se till att du har installerat [Visual Studio Code](https://code.visualstudio.com/) och det senaste [Azure API Management-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Skapa en API Management-instans](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importera ett API

I följande exempel importeras en OpenAPI-specifikation i JSON-format till API Management. Microsoft tillhandahåller Server dels-API: et som används i det här exemplet och är värd för det på Azure på `https://conferenceapi.azurewebsites.net?format=json` .

1. I Visual Studio Code väljer du Azure-ikonen i aktivitets fältet.
1. I Explorer-fönstret expanderar du API Management-instansen som du skapade.
1. Högerklicka på **API: er** och välj **Importera från openapi länk**. 
1. När du uppmanas anger du följande värden:
    1. En **openapi-länk** för innehåll i JSON-format. I det här exemplet: *https://conferenceapi.azurewebsites.net?format=json* .
    Denna URL är den tjänst som implementerar exempel-API: et. API Management vidarebefordrar begär anden till den här adressen.
    1. Ett **API-namn**, till exempel *demo-Conference-API*, som är unikt i API Management-instansen. Namnet får bara innehålla bokstäver, siffror och bindestreck. Det första och sista tecknet måste vara alfanumeriskt. Det här namnet används i sökvägen för att anropa API: et.

När API: et har importer ATS visas det i Explorer-fönstret och tillgängliga API-åtgärder visas under noden **åtgärder** .

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importerat API i Explorer-fönstret":::

## <a name="edit-the-api"></a>Redigera API

Du kan redigera API: et i Visual Studio Code. Du kan till exempel redigera Resource Manager JSON-beskrivningen för API: et i redigerings fönstret för att ta bort **http-** protokollet som används för att få åtkomst till API: et. Välj sedan **Arkiv**  >  **Spara**.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Redigera JSON-Beskrivning":::

Om du vill redigera OpenAPI-formatet högerklickar du på API-namnet i Explorer-fönstret och väljer **Redigera openapi**. Gör ändringarna och välj sedan **Arkiv**  >  **Spara**.

## <a name="apply-policies-to-the-api"></a>Tillämpa principer på API: et 

API Management innehåller [principer](api-management-policies.md) som du kan konfigurera för dina API: er. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principer kan vara globala, som gäller för alla API: er i API Management-instansen, eller som kan begränsas till en speciell API eller API-åtgärd.

I det här avsnittet visas hur du använder några vanliga utgående principer för ditt API som transformerar API-svaret. Principerna i det här exemplet ändrar svarshuvuden och döljer ursprungliga Server dels adresser som visas i svars texten.

1. I Explorer-fönstret väljer du **princip** under *demo-Conference-API: et* som du har importerat. Princip filen öppnas i redigerings fönstret. Den här filen konfigurerar principer för alla åtgärder i API: et. 

1. Uppdatera filen med följande innehåll i- `<outbound>` elementet:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Den första `set-header` principen lägger till ett anpassat svars huvud i demonstrations syfte.
    * Den andra `set-header` principen tar bort **X-Powered-by-** huvud, om den finns. Den här rubriken kan avslöja program ramverket som används i API-backend och utgivare tar ofta bort den.
    * `redirect-content-urls`Principen skriver om (maskera) länkar i svars texten så att de pekar på motsvarande länkar via API Management Gateway.
    
1. Spara filen. Om du uppmanas väljer du **Ladda upp** för att ladda upp filen till molnet.

## <a name="test-the-api"></a>Testa API:et

### <a name="get-the-subscription-key"></a>Hämta prenumerations nyckeln

Om du vill testa det importerade API som du har importerat och de principer som tillämpas, behöver du en prenumerations nyckel för din API Management-instans.

1. I Explorer-fönstret högerklickar du på namnet på API Management-instansen.
1. Välj **Kopiera prenumerations nyckel**.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopiera prenumerations nyckel":::

### <a name="test-an-api-operation"></a>Testa en API-åtgärd

1. I Explorer-fönstret expanderar du noden **åtgärder** under *demo-Conference-API: et* som du har importerat.
1. Välj en åtgärd som *GetSpeakers*.
1. I redigerings fönstret, bredvid **OCP-APIM-Subscription-Key**, ersätter `{{SubscriptionKey}}` du med den prenumerations nyckel som du kopierade.
1. Välj **Skicka begäran**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Skicka API-begäran från Visual Studio Code":::

När begäran lyckas svarar Server delen med **200 OK** och vissa data.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-test åtgärd":::

Observera följande uppgifter i svaret:
* Den **anpassade** rubriken läggs till i svaret.
* **X-Powered-by-** huvudet visas inte i svaret.
* URL: er till API-backend-servern omdirigeras till API Management Gateway, i det här fallet `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Spåra API-åtgärden

För detaljerad spårnings information som hjälper dig att felsöka API-åtgärden väljer du den länk som visas bredvid **OCP-APIM-trace-location**. 

JSON-filen på den platsen innehåller information om inkommande, backend-och utgående spårning så att du kan avgöra var eventuella problem inträffar efter att begäran har gjorts.

> [!TIP]
> När du testar API-åtgärder tillåter API Management tillägget valfria [princip fel sökning](api-management-debug-policies.md) (tillgängligt i tjänst nivån för utvecklare).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver tar du bort API Management-instansen genom att högerklicka och välja **Öppna i portalen** för att [ta bort API Management-tjänsten](get-started-create-service-instance.md#clean-up-resources) och dess resurs grupp.

Alternativt kan du välja **ta bort API Management** för att bara ta bort API Management-instansen (den här åtgärden tar inte bort resurs gruppen).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Ta bort API Management instans från VS Code":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien introducerades flera funktioner i API Management tillägget för Visual Studio Code som du kan använda för att importera och hantera API: er. Du har lärt dig att:

> [!div class="checklist"]
> * Importera ett API till API Management
> * Redigera API
> * Tillämpa API Management principer
> * Testa API:et

API Management tillägget ger ytterligare funktioner för att arbeta med dina API: er. Du kan till exempel [Felsöka principer](api-management-debug-policies.md) (tillgängliga i tjänst nivån för utvecklare) eller skapa och hantera [namngivna värden](api-management-howto-properties.md).