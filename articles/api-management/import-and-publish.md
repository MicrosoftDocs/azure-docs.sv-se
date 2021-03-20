---
title: Självstudie – importera och publicera ditt första API i Azure API Management
description: I den här självstudien importerar du en OpenAPI Specification-API till Azure API Management och testar sedan ditt API i Azure Portal.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91626983"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Självstudie: importera och publicera ditt första API

Den här självstudien visar hur du importerar ett OpenAPI-API i JSON-format till Azure API Management. Microsoft tillhandahåller Server dels-API: et som används i det här exemplet och är värd för det på Azure på [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

När du har importerat Server dels-API: et till API Management blir ditt API Management-API ett fasad för Server dels-API: et. Du kan anpassa fasad efter dina behov i API Management utan att behöva röra Server dels-API: et. Mer information finns i [Transformera och skydda ditt API](transform-api.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera ett API till API Management
> * Testa API:et i Azure Portal

Efter importen kan du hantera API: et i Azure Portal.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nytt API i API Management":::

## <a name="prerequisites"></a>Förutsättningar

- Förstå [Azure API Management terminologi](api-management-terminology.md).
- [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Importera och publicera ett Server dels-API

I det här avsnittet visas hur du importerar och publicerar ett OpenAPI-API för-specifikation.

1. I det vänstra navigerings fönstret på API Management-instansen väljer du **API: er**.
1. Välj panelen **openapi** .
1. I fönstret **skapa från openapi-specifikation** väljer du **fullständig**.
1. Ange värdena från följande tabell. Välj **skapa** för att skapa ditt API.

   Du kan ange API-värden under skapandet eller senare genom att gå till fliken **Inställningar** .

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Skapa ett API":::


   |Inställning|Värde|Beskrivning|
   |-------|-----|-----------|
   |**OpenAPI-specifikation**|*https: \/ /conferenceapi.azurewebsites.net? format = JSON*|Tjänsten som implementerar API: et. API Management vidarebefordrar begär anden till den här adressen.|
   |**Visningsnamn**|När du har angett föregående tjänst-URL fyller API Management det här fältet baserat på JSON.|Namnet som visas i [Developer-portalen](api-management-howto-developer-portal.md).|
   |**Namn**|När du har angett föregående tjänst-URL fyller API Management det här fältet baserat på JSON.|Ett unikt namn för API: et.|
   |**Beskrivning**|När du har angett föregående tjänst-URL fyller API Management det här fältet baserat på JSON.|En valfri beskrivning av API: et.|
   |**URL-schema**|**HTTPS**|Vilka protokoll som har åtkomst till API: et.|
   |**API URL-suffix**|*konferens*|Suffixet som läggs till i bas-URL: en för API Managements tjänsten. API Management skiljer API: er från deras suffix så att suffixet måste vara unikt för varje-API för en specifik utgivare.|
   |**Taggar**| |Taggar för att ordna API: er för sökning, gruppering eller filtrering.|
   |**Produkter**|**Obegränsat**|Associering av en eller flera API: er. Varje API Management instans levereras med två exempel produkter: **starter** och **obegränsade**. Du publicerar ett API genom att associera API: et med en produkt, **obegränsat** i det här exemplet.<br/><br/> Du kan inkludera flera API: er i en produkt och erbjuda dem till utvecklare via Developer-portalen. Om du vill lägga till detta API till en annan produkt skriver du eller väljer produkt namnet. Upprepa det här steget om du vill lägga till API: et till flera produkter. Du kan också lägga till API: er till produkter senare från sidan **Inställningar** .<br/><br/>  Mer information om produkter finns i [skapa och publicera en produkt](api-management-howto-add-products.md).|
   |**Gateways**|**Hanterade**|API-Gateway (er) som exponerar API: et. Det här fältet är endast tillgängligt i tjänster för **utvecklare** och **Premium** -nivån.<br/><br/>**Managed** anger den gateway som är inbyggd i API Management tjänsten och som finns i Microsoft Azure. [Gatewayer med egen värd](self-hosted-gateway-overview.md) är bara tillgängliga i tjänst nivåerna Premium och Developer. Du kan distribuera dem lokalt eller i andra moln.<br/><br/> Om inga gatewayer har valts är API: et inte tillgängligt och API-begärandena lyckas inte.|
   |**Vilken är versionen för det här API:et?**|Markera eller avmarkera|Mer information finns i [publicera flera versioner av ditt API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Om du vill publicera API: t till API-konsumenter måste du associera det med en produkt.

2. Välj **Skapa**.

Om du har problem med att importera en API-definition kan du läsa [listan över kända problem och begränsningar](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testa det nya API: et i Azure Portal

Du kan anropa API-åtgärder direkt från Azure Portal, vilket ger ett bekvämt sätt att visa och testa åtgärderna.

1. I det vänstra navigerings fönstret på API Management-instansen väljer du **API: er**  >  **demo konferens-API**.
1. Välj fliken **test** och välj sedan **GetSpeakers**. Sidan visar **frågeparametrar** och **rubriker**, om det finns några. **OCP-APIM-Subscription-Key** fylls i automatiskt för den prenumerations nyckel som är kopplad till detta API.
1. Välj **Skicka**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Test-API i Azure Portal":::

   Server delen svarar med **200 OK** och vissa data.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera ditt första API
> * Testa API:et i Azure Portal

Gå vidare till nästa självstudie och lär dig hur du skapar och publicerar en produkt:

> [!div class="nextstepaction"]
> [Skapa och publicera en produkt](api-management-howto-add-products.md)
