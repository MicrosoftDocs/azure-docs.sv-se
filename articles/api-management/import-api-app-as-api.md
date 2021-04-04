---
title: Importera en API-app som ett API med Azure Portal | Microsoft Docs
description: Den här artikeln visar hur du använder API Management (APIM) för att importera API-appen som ett API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 41209233ec59f578db4ff7fd344bb96aefeb975e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994748"
---
# <a name="import-an-api-app-as-an-api"></a>Importera en API-app som ett API

Den här artikeln visar hur du importerar en API-app som ett API. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera en API-app som ett API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Förutsättningar

+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
+ Kontrollera att det finns en API-app i din prenumeration. Mer information finns i [App Service-dokumentationen](../app-service/index.yml)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.
2. Välj **API-app** från listan **Lägg till ett nytt API**.

    ![API-app](./media/import-api-app-as-api/api-app.png)
3. Tryck på **Bläddra** för att se listan över API-appar i din prenumeration.
4. Välj appen. APIM hittar den swagger som är associerad med den valda appen, hämtar den och importerar den. 

    Om APIM inte hittar swaggern visas API:et som ett ”direkt”-API. 
5. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.
6. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används den *obegränsade* produkten.  Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Obegränsat**   
7. Ange andra API-inställningar. Du kan ange värden när du skapar eller konfigurerar dem senare genom att gå till fliken **Inställningar** . Inställningarna beskrivs i själv studie kursen [Importera och publicera din första API](import-and-publish.md#import-and-publish-a-backend-api) .
8. Välj **Skapa**.

## <a name="test-the-new-api-in-the-azure-portal"></a>Testa det nya API: et i Azure Portal

Du kan anropa åtgärder direkt från Azure Portal, vilket är ett enkelt sätt att visa och testa åtgärderna i ett API.  

1. Välj det API som du skapade i föregående steg.
2. Tryck på fliken **Test**.
3. Välj någon åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade APIM-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 
1. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
