---
title: Självstudie – de blå API-svaren i API Management-Azure Portal | Microsoft Docs
description: I den här självstudien använder du API Management för att ange en princip för ett API så att den returnerar ett skissat svar om Server delen inte är tillgänglig för att skicka riktiga svar.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 75727d139242e1b537505d2ed907ae20fc5479f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100547286"
---
# <a name="tutorial-mock-api-responses"></a>Självstudie: blå API-svar

Server dels-API: er kan importeras till ett API Management (APIM) API eller skapas och hanteras manuellt. Stegen i den här självstudien visar hur du använder APIM för att skapa ett tomt API och hanterar det manuellt, och sedan anger du en princip för ett API så att den returnerar ett skissat svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av APIM-instanser även om serverdelen inte är tillgänglig för att skicka verkliga svar. 

Möjligheten att modellera upp svar kan vara användbar i ett antal scenarier:

+ Om API-fasaden utformas först och implementeringen av serverdelen kommer senare. Eller om serverdelen utvecklas parallellt.
+ Om serverdelen tillfälligt inte fungerar eller inte går att skala.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett test-API 
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Interagerat API-svar":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Skapa ett test-API 

Stegen i det här avsnittet visar hur du skapar ett tomt API utan serverdel. 


1. Logga in på Azure Portal och navigera till API Management-instansen.
1. Välj **API: er**  >  **+ Lägg till API**  >  **tomt API**.
1. I fönstret **skapa ett tomt API** väljer du **fullständig**.
1. Ange *test-API* för **visnings namn**.
1. Välj **obegränsat** för **produkter**.
1. Se till att **hanterad** är valt i **gatewayer**.
1. Välj **Skapa**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Skapa tomt API":::

## <a name="add-an-operation-to-the-test-api"></a>Lägg till en åtgärd till test-API:et

Ett API visar en eller flera åtgärder. I det här avsnittet lägger du till en åtgärd i det tomma API som du skapade. Ett fel genereras när du anropar åtgärden efter att du slutfört stegen i det här avsnittet. Du får inga fel när du har slutfört stegen senare i avsnittet [Aktivera svars modeller](#enable-response-mocking) .

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj det API som du skapade i föregående steg.
1. Välj **+ Lägg till åtgärd**.
1. Ange följande värden i fönstret **klient** del.

     | Inställning             | Värde                             | Beskrivning                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Visningsnamn**    | *Testanrop*                       | Namnet som visas i [Developer-portalen](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (HTTP-verb) | GET                               | Välj en av de fördefinierade HTTP-verben.                                                                                                                                         |
    | **URL**             | */test*                           | En URL-sökväg för API:et.                                                                                                                                                                       |
    | **Beskrivning**     |                                   |  Valfri beskrivning av åtgärden som används för att tillhandahålla dokumentation i Developer-portalen till utvecklare som använder detta API.                                                    |
    
1. Välj fliken **svar** , som finns under fälten URL, visnings namn och beskrivning. Ange inställningar på den här fliken för att definiera svars status koder, innehålls typer, exempel och scheman.
1. Välj **+ Lägg till svar** och välj **200 OK** i listan.
1. Under rubriken **Återangivelser** till höger väljer du **+ Lägg till återgivning**.
1. Ange *Application/JSON* i sökrutan och välj innehålls typen **Application/JSON** .
1. I textrutan **Exempel** anger du `{ "sampleField" : "test" }`.
1. Välj **Spara**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Lägg till API-åtgärd" border="false":::

Även om detta inte krävs för det här exemplet kan ytterligare inställningar för en API-åtgärd konfigureras på andra flikar, inklusive:


|Flik      |Beskrivning  |
|---------|---------|
|**Query**     |  Lägg till frågeparametrar. Förutom att ange ett namn och en beskrivning kan du ange värden som har tilldelats till en frågeparameter. Ett av värdena kan vara markerat som standard (valfritt).        |
|**Förfrågan**     |  Definiera innehålls typer, exempel och scheman för begäran.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill lägga till en åtgärd i test-API: et kör du kommandot [AZ APIM API operation Create](/cli/azure/apim/api/operation#az_apim_api_operation_create) :

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Kör kommandot [AZ APIM API operation List](/cli/azure/apim/api/operation#az_apim_api_operation_list) för att se alla dina åtgärder för ett API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Om du vill ta bort en åtgärd använder du kommandot [AZ APIM API operation Delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) . Hämta åtgärds-ID: t från föregående kommando.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Se till att den här åtgärden används i resten av den här artikeln.

---

## <a name="enable-response-mocking"></a>Aktivera simulerade svar

1. Välj det API som du skapade i [skapa ett test-API](#create-a-test-api).
1. Välj den teståtgärd som du lade till.
1. I fönstret till höger ser du till att fliken **design** är markerad.
1. I fönstret **inkommande bearbetning** väljer du **+ Lägg till princip**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Lägg till bearbetnings princip" border="false":::

1. Välj **blå svar**  från galleriet.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Panelen Princip för simulerade svar" border="false":::

1. I textrutan **API Management-svar** anger du **200 OK, application/json**. Det här alternativet anger att ditt API ska returnera svars-exemplet som du definierade i föregående avsnitt.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Ange skissande svar":::

1. Välj **Spara**.

    > [!TIP]
    > Ett gult fält med text **modelleringen är aktiverat** för ditt API anger att svar som returneras från API Management är nedlagda av den [skissade principen](api-management-advanced-policies.md#mock-response) och inte produceras av Server delen.

## <a name="test-the-mocked-api"></a>Testa det simulerade API:et

1. Välj det API som du skapade i [skapa ett test-API](#create-a-test-api).
1. Välj fliken **Test**.
1. Kontrollera att API:et **Testanrop** är markerat. Välj **Skicka** för att göra ett testanrop.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testa det simulerade API:et":::

1. **HTTP-svaret** visar den JSON som användes som ett exempel i den första delen av självstudien.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Modeller för HTTP-svar":::

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett test-API
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
