---
title: Importera SOAP API med Azure Portal | Microsoft Docs
description: 'Lär dig hur du importerar en standard-XML-representation av ett SOAP-API och sedan testar API: et i Azure-och Developer-portalerna.'
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
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011072"
---
# <a name="import-soap-api"></a>Importera SOAP API

Den här artikeln visar hur du importerar en standard XML-representation av ett SOAP API. Artikeln visar också hur du testar API Management API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera SOAP API
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Gå till din API Management-tjänst i Azure Portal och välj **API: er** på menyn.
2. Välj **WSDL** från listan **Lägg till ett nytt API**.

    ![SOAP API](./media/import-soap-api/wsdl-api.png)
3. I **WSDL-specifikationen**, anger du URL:en till ditt SOAP API.
4. Alternativknappen **SOAP-direkt** är markerad som standard. Med det här valet, kommer API:t att visas som SOAP. Kunden måste använda SOAP-regler. Om du vill ”restify” API:et, följer du stegen i [Importera ett SOAP API och konvertera det till REST](restify-soap-api.md).

    ![Skärm bild som visar dialog rutan skapa från W S D L där du kan ange en W S D L-specifikation.](./media/import-soap-api/pass-through.png)
5. Tryck tabb.

    Följande fält fyllas i med informationen från SOAP API:t: visningsnamn, namn, beskrivning.
6. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här API Management-instansen. Det måste vara unikt i den här API Management-instansen.
7. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används den *obegränsade* produkten.  Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat API Management-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Obegränsat**   
8. Ange andra API-inställningar. Du kan ange värden när du skapar eller konfigurerar dem senare genom att gå till fliken **Inställningar** . Inställningarna beskrivs i själv studie kursen [Importera och publicera din första API](import-and-publish.md#import-and-publish-a-backend-api) .
9. Välj **Skapa**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testa det nya API: et i administrations portalen

Åtgärder kan anropas direkt från administratörsportalen, vilket ger ett bekvämt sätt att visa och testa åtgärderna för ett API.  

1. Välj det API som du skapade i föregående steg.
2. Tryck på fliken **Test**.
3. Välj någon åtgärd.

    Sidan visar fält för frågeparametrar och fält för sidhuvudena. Ett av huvudena är Ocp-Apim-prenumeration-Key, för prenumerationsnyckeln till den produkt som är associerad med det här API:et. Om du skapade API Management-instansen är du redan administratör, vilket innebär att nyckeln fylls i automatiskt. 
1. Tryck på **Skicka**.

    Serverdelen svarar med **200 OK** och några data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)