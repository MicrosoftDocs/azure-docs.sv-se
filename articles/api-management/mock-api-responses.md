---
title: Självstudie – Fingera API-svar i API Management – Azure Portal | Microsoft Docs
description: I den här självstudien använder du API Management för att ange en princip för ett API så att den returnerar ett fingerat svar om backend inte är tillgänglig för att skicka verkliga svar.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 6214594f825908388762ac35cc8c8722ff9135f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874359"
---
# <a name="tutorial-mock-api-responses"></a>Självstudie: Fingera API-svar

Backend-API:er kan importeras till ett API API Management (APIM) eller skapas och hanteras manuellt. Stegen i den här självstudien visar hur du använder APIM för att skapa ett tomt API och hantera det manuellt. Ange sedan en princip för ett API så att det returnerar ett fingerat svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av APIM-instanser även om serverdelen inte är tillgänglig för att skicka verkliga svar. 

Möjligheten att modellera svar kan vara användbar i ett antal scenarier:

+ Om API-fasaden utformas först och implementeringen av serverdelen kommer senare. Eller om serverdelen utvecklas parallellt.
+ Om serverdelen tillfälligt inte fungerar eller inte går att skala.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett test-API 
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Svar från fingerat API":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
+ Slutför följande snabbstart: Skapa [en Azure API Management instans](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Skapa ett test-API 

Stegen i det här avsnittet visar hur du skapar ett tomt API utan serverdel. 


1. Logga in på Azure Portal och gå till din API Management instans.
1. Välj **API:er**  >  **+ Lägg till tomt**  >  **API.**
1. I fönstret **Skapa ett tomt API** väljer du **Fullständig.**
1. Ange *Test-API* som **Visningsnamn.**
1. Välj **Obegränsat** för **Produkter.**
1. Se **till att** Hanterad är valt i **Gateways**.
1. Välj **Skapa**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Skapa tomt API":::

## <a name="add-an-operation-to-the-test-api"></a>Lägg till en åtgärd till test-API:et

Ett API exponerar en eller flera åtgärder. I det här avsnittet lägger du till en åtgärd i det tomma API som du skapade. Ett fel genereras när du anropar åtgärden efter att du slutfört stegen i det här avsnittet. Du får inga fel när du har slutfört stegen senare i avsnittet [Aktivera fingerade](#enable-response-mocking) svar.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj det API som du skapade i föregående steg.
1. Välj **+ Lägg till åtgärd**.
1. I **fönstret Frontend** anger du följande värden.

     | Inställning             | Värde                             | Beskrivning                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Visningsnamn**    | *Testanrop*                       | Namnet som visas i [utvecklarportalen](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (HTTP-verb) | GET                               | Välj ett av de fördefinierade HTTP-verben.                                                                                                                                         |
    | **URL**             | */test*                           | En URL-sökväg för API:et.                                                                                                                                                                       |
    | **Beskrivning**     |                                   |  Valfri beskrivning av åtgärden som används för att tillhandahålla dokumentation i utvecklarportalen till utvecklare som använder det här API:et.                                                    |
    
1. Välj fliken **Svar** under fälten URL, Visningsnamn och Beskrivning. Ange inställningar på den här fliken för att definiera svarsstatuskoder, innehållstyper, exempel och scheman.
1. Välj **+ Lägg till** svar och välj **200 OK** i listan.
1. Under rubriken **Återangivelser** till höger väljer du **+ Lägg till återgivning**.
1. Ange *application/json* i sökrutan och välj **innehållstypen application/json.**
1. I textrutan **Exempel** anger du `{ "sampleField" : "test" }`.
1. Välj **Spara**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Lägg till API-åtgärd" border="false":::

Även om det inte krävs för det här exemplet kan ytterligare inställningar för en API-åtgärd konfigureras på andra flikar, inklusive:


|Flik      |Description  |
|---------|---------|
|**Query**     |  Lägg till frågeparametrar. Förutom att ange ett namn och en beskrivning kan du ange värden som är tilldelade till en frågeparameter. Ett av värdena kan vara markerat som standard (valfritt).        |
|**Förfrågan**     |  Definiera typer av begärandeinnehåll, exempel och scheman.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Så här börjar du använda Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Om du vill lägga till en åtgärd i test-API:et kör du [kommandot az apim api operation](/cli/azure/apim/api/operation#az_apim_api_operation_create) create:

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Kör kommandot [az apim api operation list för](/cli/azure/apim/api/operation#az_apim_api_operation_list) att se alla dina åtgärder för ett API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Om du vill ta bort en åtgärd använder du [kommandot az apim api operation delete.](/cli/azure/apim/api/operation#az_apim_api_operation_delete) Hämta åtgärds-ID från föregående kommando.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Behåll den här åtgärden för användning i resten av den här artikeln.

---

## <a name="enable-response-mocking"></a>Aktivera simulerade svar

1. Välj det API som du skapade i [Skapa ett test-API.](#create-a-test-api)
1. Välj den teståtgärd som du lade till.
1. Kontrollera att fliken Design är markerad i **fönstret** till höger.
1. I fönstret **Inkommande bearbetning** väljer du **+ Lägg till princip**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Lägga till bearbetningsprincip" border="false":::

1. Välj **Fingera**  svar från galleriet.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Panelen Princip för simulerade svar" border="false":::

1. I textrutan **API Management-svar** anger du **200 OK, application/json**. Det här alternativet anger att ditt API ska returnera svars-exemplet som du definierade i föregående avsnitt.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Ange fingerade svar":::

1. Välj **Spara**.

    > [!TIP]
    > Ett gult fält med texten **Mocking (Fingera)** är aktiverat för ditt API anger att svar som returneras från API Management fingeras av [fingerningsprincipen](api-management-advanced-policies.md#mock-response) och inte skapas av backend-enheten.

## <a name="test-the-mocked-api"></a>Testa det simulerade API:et

1. Välj det API som du skapade i [Skapa ett test-API.](#create-a-test-api)
1. Välj fliken **Test**.
1. Kontrollera att API:et **Testanrop** är markerat. Välj **Skicka** för att göra ett testanrop.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Testa det simulerade API:et":::

1. **HTTP-svaret** visar den JSON som användes som ett exempel i den första delen av självstudien.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Fingera HTTP-svar":::

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
