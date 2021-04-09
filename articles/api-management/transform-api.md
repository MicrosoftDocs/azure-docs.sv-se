---
title: Självstudie – transformera och skydda ditt API i Azure API Management | Microsoft Docs
description: I den här självstudien får du lära dig hur du skyddar ditt API i API Management med omvandlings-och begränsnings principer (Rate-Limiting).
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010239"
---
# <a name="tutorial-transform-and-protect-your-api"></a>Självstudie: transformera och skydda ditt API

I självstudien visas hur du transformerar ditt API så att det inte visar information om den privata server delen. Du kanske till exempel vill dölja informationen om teknik stacken som körs på Server delen. Du kanske också vill dölja ursprungliga URL: er som visas i bröd texten i API: ns HTTP-svar och istället omdirigera dem till APIM-gatewayen.

Den här kursen visar också hur enkelt det är att lägga till skydd för Server dels-API: et genom att konfigurera en hastighets gräns med Azure API Management. Du kanske vill begränsa antalet API-anrop så att API: t inte används av utvecklare. Mer information finns i [API Management principer](api-management-policies.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> -   Omvandla ett API och ta bort svarshuvuden
> -   Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen
> -   Skydda ett API genom att lägga till en princip för hastighets begränsning (begränsning)
> -   Testa omvandlingarna

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="Principer i portalen":::

## <a name="prerequisites"></a>Förutsättningar

-   Lär dig [Azure API Management-terminologin](api-management-terminology.md).
-   Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
-   Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
-   Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Omvandla ett API och ta bort svarshuvuden

I det här avsnittet visas hur du döljer de HTTP-huvuden som du inte vill visa för dina användare. Det här exemplet visar hur du tar bort följande rubriker i HTTP-svaret:

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa det ursprungliga svaret:

1. Välj **API: er** i API Management tjänst instans.
1. Välj **demo konferens-API** från din API-lista.
1. Välj fliken **test** överst på skärmen.
1. Välj åtgärden **GetSpeakers** och välj **Skicka**.

Det ursprungliga svaret bör se ut ungefär så här:

:::image type="content" source="media/transform-api/original-response.png" alt-text="Ursprungligt API-svar":::

Som du kan se innehåller svaret **x-ASPNET-versionen** och **x-Powered-by-** huvudena.

### <a name="set-the-transformation-policy"></a>Ange en transformationsprincip

1. Välj **demo konferens API**  >  **design**  >  **alla åtgärder**.
4. I avsnittet **utgående bearbetning** väljer du ikonen kod redigerare ( **</>** ).

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="Navigera till utgående princip" border="false":::

1. Placera markören inuti det **&lt; utgående &gt;** elementet och välj **Visa kodfragment** i det övre högra hörnet.
1. I det högra fönstret under **omvandlings principer** väljer du **Ange HTTP-huvud** två gånger (för att infoga två princip kod avsnitt).

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="Ange HTTP-huvud princip":::

1. Ändra **\<outbound>** koden så att den ser ut så här:

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="Ange HTTP-huvud":::

1. Välj **Spara**.

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen

I det här avsnittet visas hur du döljer ursprungliga URL: er som visas i bröd texten i API: ns HTTP-svar och istället omdirigerar dem till APIM-gatewayen.

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa det ursprungliga svaret:

1. Välj **demo konferens API**-  >  **test**.
1. Välj åtgärden **GetSpeakers** och välj **Skicka**.

    Som du kan se innehåller svaret de ursprungliga URL: erna för Server delen:

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="Ursprungliga URL: er som svar":::


### <a name="set-the-transformation-policy"></a>Ange en transformationsprincip

1.  Välj **demo konferens API**  >  **alla drifts**  >  **design**.
1.  I avsnittet **utgående bearbetning** väljer du ikonen kod redigerare ( **</>** ).
1.  Placera markören inuti det **&lt; utgående &gt;** elementet och välj **Visa kodfragment** i det övre högra hörnet.
1.  I det högra fönstret under **omvandlings principer** väljer du **maskera URL: er i innehåll**. 
1.  Välj **Spara**.

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Skydda ett API genom att lägga till en princip för frekvensbegränsningar (begränsning)

I det här avsnittet visas hur du lägger till skydd för ditt serverdels-API genom att konfigurera frekvensbegränsningar. Du kanske till exempel vill begränsa antalet API-anrop så att API: et inte används av utvecklarna. I det här exemplet har gränsen angetts till 3 anrop per 15 sekunder för varje prenumerations-ID. Efter 15 sekunder kan en utvecklare försöka att anropa API:et igen.

1.  Välj **demo konferens API**  >  **alla drifts**  >  **design**.
1.  I avsnittet **inkommande bearbetning** väljer du ikonen kod redigerare ( **</>** ).
1.  Placera markören inuti det **&lt; inkommande &gt;** elementet och välj **Visa kodfragment** i det övre högra hörnet.

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="Ange princip för inkommande" border="false":::

1.  I det högra fönstret under **åtkomst begränsnings principer** väljer du **+ begränsa anrops frekvens per nyckel**.
1.  Ändra din **hastighets begränsning efter nyckel** kod (i- **\<inbound\>** elementet) till följande kod:

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>Testa omvandlingarna

I det här läget ser principerna ut så här om du tittar på koden i kod redigeraren:

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

I resten av det här avsnittet testas principtransformationer som du anger i den här övningen.

### <a name="test-the-stripped-response-headers"></a>Testa borttagna svarshuvuden

1. Välj **demo konferens API**-  >  **test**.
1. Välj åtgärden **GetSpeakers** och välj **Skicka**.

    Som du kan se har rubrikerna tagits bort:

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="Rensade svars rubriker":::

### <a name="test-the-replaced-url"></a>Testa den ersatta URL:en

1. Välj **demo konferens API**-  >  **test**.
1. Välj åtgärden **GetSpeakers** och välj **Skicka**.

    Som du kan se har URL: en ersatts.

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="Ersatt URL":::

### <a name="test-the-rate-limit-throttling"></a>Testa frekvensgränsen (begränsningen)

1. Välj **demo konferens API**-  >  **test**.
1. Välj åtgärden **GetSpeakers**. Välj **Skicka** tre gånger i rad.

    När du har skickat begäran 3 gånger får du svaret **429 för många begär Anden** .

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="För många begär Anden":::

1. Vänta i 15 sekunder och välj **Skicka** igen. Den här gången bör få svaret **200 OK**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> -   Omvandla ett API och ta bort svarshuvuden
> -   Ersätt ursprungliga URL:er i API-svarets brödtext med URL:er för APIM-gatewayen
> -   Skydda ett API genom att lägga till en princip för frekvensbegränsningar (begränsning)
> -   Testa omvandlingarna

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Övervaka ditt API](api-management-howto-use-azure-monitor.md)
