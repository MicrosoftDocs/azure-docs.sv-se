---
title: Lägg till ett API manuellt med Azure Portal | Microsoft Docs
description: Den här självstudien visar hur du använder API Management (APIM) för att lägga till ett API manuellt.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183857"
---
# <a name="add-an-api-manually"></a>Lägga till ett API manuellt

Stegen i den här artikeln visar hur du använder Azure Portal för att lägga till ett API manuellt till API Management (APIM)-instansen. Ett vanligt scenario när du vill skapa ett tomt API och definiera det manuellt är när du vill testa API:et. Mer information om att testa API:er finns i [Testa API-svar](mock-api-responses.md).

Om du vill importera ett befintligt API, se avsnittet [relaterade ämnen](#related-topics).

I den här artikeln, skapar vi ett tomt API och anger [httpbin.org](https://httpbin.org) (en offentlig testtjänst) som serverdels-API.

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Skapa ett API

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.
2. Välj **+ Lägg till API** på den vänstra menyn.
3. Välj **Tomt API** i listan.  
    ![Tomt API](media/add-api-manually/blank-api.png)  
4. Ange inställningarna för API:et. Inställningarna beskrivs i själv studie kursen [Importera och publicera din första API](import-and-publish.md#import-and-publish-a-backend-api) .
5. Välj **Skapa**.

Nu har du inga åtgärder i API Management som mappar till åtgärderna i Server dels-API: et. Om du anropar en åtgärd som exponeras genom Server delen men inte via API Management får du en **404**.

>[!NOTE] 
> När du lägger till ett API, även om det är anslutet till en viss backend-tjänst, kommer APIM som standard inte att exponera några åtgärder förrän du tillåter dem. Om du vill tillåta en drift av backend-tjänsten skapar du en APIM-åtgärd som mappar till Server dels åtgärden.

## <a name="add-and-test-an-operation"></a>Lägg till och testa en åtgärd

Det här avsnittet visar hur du lägger till en /get-åtgärd för att mappa den till serverdelens http://httpbin.org/get-åtgärd.

### <a name="add-an-operation"></a>Lägga till en åtgärd

1. Välj det API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärd**.
3. I **URL**, väljer du **GET** och anger */get* i resursen.
4. Ange *FetchData* som **Visningsnamn**.
5. Välj **Spara**.

### <a name="test-an-operation"></a>Testa en åtgärd

Testa åtgärden i Azure Portal. Du kan också testa den i **Utvecklarportalen**.

1. Välj fliken **Test**.
2. Välj **FetchData**.
3. Tryck på **Skicka**.

Svaret som http://httpbin.org/get-åtgärden genererar visas. Om du vill omvandla dina åtgärder, se [Omvandla och skydda ditt API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Lägg till och testa en parameteriserad åtgärd

Det här avsnittet visar hur du lägger till en åtgärd som tar en parameter. I det här fallet mappar vi åtgärden till http://httpbin.org/status/200.

### <a name="add-the-operation"></a>Lägg till åtgärden

1. Välj det API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärd**.
3. I **URL**, väljer du **GET** och anger */status/{code}* i resursen. Du kan också kan du ange information som är associerad med den här parametern. Ange till exempel *Nummer* för **Typ**, *200* (standard) för **Värden**.
4. Ange GetStatus som **Visningsnamn**.
5. Välj **Spara**.

### <a name="test-the-operation"></a>Testa åtgärden 

Testa åtgärden i Azure Portal.  Du kan också testa den i **Utvecklarportalen**.

1. Välj fliken **Test**.
2. Välj **GetStatus**. Som standard är kodvärdet satt till *200*. Du kan ändra det för att testa andra värden. Ange till exempel *418*.
3. Tryck på **Skicka**.

    Svaret som http://httpbin.org/status/200-åtgärden genererar visas. Om du vill omvandla dina åtgärder, se [Omvandla och skydda ditt API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
