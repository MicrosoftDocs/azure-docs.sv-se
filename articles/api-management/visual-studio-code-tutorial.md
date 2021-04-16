---
title: Självstudie – Importera och hantera API:er – Azure API Management och Visual Studio Code | Microsoft Docs
description: I den här självstudien lär du dig hur du använder Azure API Management-tillägget för Visual Studio Code för att importera, testa och hantera API:er.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0090d981e93cee12f2feaaf7d2c12f341564f6ec
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482338"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Självstudie: Använda API Management för att Visual Studio Code för att importera och hantera API:er

I den här självstudien lär du dig att använda API Management-tillägget för Visual Studio Code för vanliga åtgärder i API Management. Använd den välbekanta Visual Studio Code för att importera, uppdatera, testa och hantera API:er.

Lär dig att:

> [!div class="checklist"]
> * Importera ett API till API Management
> * Redigera API:et
> * Tillämpa API Management principer
> * Testa API:et


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API i API Management tillägg":::

En introduktion till ytterligare funktioner API Management finns i API Management självstudier med hjälp av [Azure Portal](import-and-publish.md).

## <a name="prerequisites"></a>Förutsättningar
- Förstå [terminologi API Management Azure-tjänster](api-management-terminology.md)
- Kontrollera att du har [Visual Studio Code och](https://code.visualstudio.com/) det senaste Azure [API Management-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [Skapa en API Management instans](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Importera ett API

I följande exempel importeras en OpenAPI-specifikation i JSON-format till API Management. Microsoft tillhandahåller server-API:et som används i det här exemplet och är värd för det på Azure på `https://conferenceapi.azurewebsites.net?format=json` .

1. I Visual Studio Code väljer du Azure-ikonen i Aktivitetsfältet.
1. I Explorer-fönstret expanderar du den API Management som du skapade.
1. Högerklicka på **API:er** och välj **Importera från OpenAPI Link**. 
1. Ange följande värden när du uppmanas till det:
    1. En **OpenAPI-länk** för innehåll i JSON-format. I det här exemplet: *https://conferenceapi.azurewebsites.net?format=json* .
    Den här URL:en är den tjänst som implementerar exempel-API:et. API Management vidarebefordrar begäranden till den här adressen.
    1. Ett **API-namn,** till *exempel demo-conference-api*, som är unikt i API Management instansen. Det här namnet får bara innehålla bokstäver, siffror och bindestreck. De första och sista tecknen måste vara alfanumeriska. Det här namnet används i sökvägen för att anropa API:et.

När API:et har importerats visas det i Explorer-fönstret och tillgängliga API-åtgärder visas under **noden Åtgärder.**

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importerat API i Explorer-fönstret":::

## <a name="edit-the-api"></a>Redigera API:et

Du kan redigera API:et i Visual Studio Code. Du kan till exempel redigera Resource Manager JSON-beskrivningen av API:et i redigeringsfönstret för att ta bort **http-protokollet** som används för åtkomst till API:et. Välj sedan **Arkiv**  >  **Spara.**

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="Redigera JSON-beskrivning":::

Om du vill redigera OpenAPI-formatet högerklickar du på API-namnet i Explorer-fönstret och väljer **Redigera OpenAPI.** Gör dina ändringar och välj sedan **Arkiv**  >  **Spara.**

## <a name="apply-policies-to-the-api"></a>Tillämpa principer på API:et 

API Management innehåller [principer som](api-management-policies.md) du kan konfigurera för dina API:er. Principer är en samling instruktioner som körs sekventiellt på begäran av eller efter ett svar från ett API. Principer kan vara globala, som gäller för alla API:er i din API Management instans, eller så kan de vara begränsade till en specifik API- eller API-åtgärd.

Det här avsnittet visar hur du tillämpar några vanliga utgående principer på ditt API som transformerar API-svaret. Principerna i det här exemplet ändrar svarshuvuden och döljer ursprungliga url:er för backend som visas i svarstexten.

1. I Explorer-fönstret väljer du **Princip** under *demo-conference-api:et* som du har importerat. Principfilen öppnas i redigeringsfönstret. Den här filen konfigurerar principer för alla åtgärder i API:et. 

1. Uppdatera filen med följande innehåll i `<outbound>` -elementet:
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

    * Den första `set-header` principen lägger till ett anpassat svarshuvud i demonstrationssyfte.
    * Den andra `set-header` principen tar bort huvudet **X-Powered-By,** om det finns. Det här huvudet kan visa det programramverk som används i API-backend och utgivare tar ofta bort det.
    * Principen skriver om (maskerar) länkar i svarstexten så att de pekar på motsvarande länkar `redirect-content-urls` via API Management gateway.
    
1. Spara filen. Om du uppmanas väljer du **Ladda upp** för att ladda upp filen till molnet.

## <a name="test-the-api"></a>Testa API:et

### <a name="get-the-subscription-key"></a>Hämta prenumerationsnyckeln

Om du vill testa det importerade API:et som du har importerat och de principer som tillämpas behöver du en prenumerationsnyckel för din API Management instans.

1. I Explorer-fönstret högerklickar du på namnet på din API Management instans.
1. Välj **Kopiera prenumerationsnyckel.**

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Kopiera prenumerationsnyckel":::

### <a name="test-an-api-operation"></a>Testa en API-åtgärd

1. I Explorer-fönstret expanderar du **noden Åtgärder** under *demo-conference-api:et* som du har importerat.
1. Välj en åtgärd, till *exempel GetSpeakers,* och högerklicka sedan på åtgärden och välj **Testa åtgärd**.
1. I redigeringsfönstret bredvid **Ocp-Apim-Subscription-Key** ersätter du `{{SubscriptionKey}}` med den prenumerationsnyckel som du kopierade.
1. Välj **Skicka begäran**. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Skicka API-begäran från Visual Studio Code":::

När begäran lyckas svarar backend med **200 OK och** vissa data.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-teståtgärd":::

Lägg märke till följande information i svaret:
* Det **anpassade** huvudet läggs till i svaret.
* **Huvudet X-Powered-By** visas inte i svaret.
* URL:er till API-backend omdirigeras till API Management gateway, i det här fallet `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Spåra API-åtgärden

Om du vill ha detaljerad spårningsinformation som hjälper dig att felsöka API-åtgärden väljer du länken som visas bredvid **Ocp-APIM-Trace-Location**. 

JSON-filen på den platsen innehåller inkommande, backend- och utgående spårningsinformation så att du kan avgöra var eventuella problem uppstår efter att begäran har gjorts.

> [!TIP]
> När du testar API-åtgärder tillåter API Management-tillägget valfri [principfelsökning](api-management-debug-policies.md) (tillgängligt på tjänstnivån Utvecklare).

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs tar API Management instansen  bort genom att högerklicka och välja Öppna i portalen för att [API Management tjänsten](get-started-create-service-instance.md#clean-up-resources) och dess resursgrupp.

Alternativt kan du välja Ta **bort API Management** om du bara vill API Management instansen (den här åtgärden tar inte bort dess resursgrupp).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Ta API Management instans från VS Code":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien introducerades flera funktioner i API Management-tillägget för Visual Studio Code som du kan använda för att importera och hantera API:er. Du har lärt dig att:

> [!div class="checklist"]
> * Importera ett API till API Management
> * Redigera API:et
> * Tillämpa API Management principer
> * Testa API:et

Tillägget API Management ytterligare funktioner som fungerar med dina API:er. Du kan till [exempel felsöka -procedurer](api-management-debug-policies.md) (tillgängliga på tjänstnivån Utvecklare) eller skapa och hantera [namngivna värden](api-management-howto-properties.md).
