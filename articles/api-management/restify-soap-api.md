---
title: Importera ett SOAP-API och konvertera till REST med Azure Portal | Microsoft Docs
description: 'Lär dig att importera ett SOAP-API, konvertera det till REST med API Management och testa API: et i Azure-och Developer-portalerna.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: a87f4d0748d14ab995f75279b6a192f350165d6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87843835"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Importera ett SOAP-API och konvertera till REST

Den här artikeln visar hur du importerar ett SOAP API och konverterar det till REST. Artikeln beskriver också hur du testar APIM-API.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Importera ett SOAP-API och konvertera till REST
> * Testa API:et i Azure Portal
> * Testa API:et i Developer-portalen

## <a name="prerequisites"></a>Förutsättningar

Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Importera och publicera ett serverdels-API

1. Välj **API: er** under **API-HANTERING**.
2. Välj **WSDL** från listan **Lägg till ett nytt API**.

    ![SOAP API](./media/restify-soap-api/wsdl-api.png)
3. I **WSDL-specifikationen**, anger du URL:en till ditt SOAP API.
4. Klicka på alternativknappen **SOAP till REST**. När du klickar på det här alternativet, försöker APIM göra en automatisk omvandling mellan XML och JSON. I det här fallet bör kunder anropa API:et som ett restful-API som returnerar JSON. APIM konverterar varje begäran i ett SOAP-anrop.

    ![SOAP till REST](./media/restify-soap-api/soap-to-rest.png)

5. Tryck tabb.

    Följande fält fyllas i med informationen från SOAP API:t: visningsnamn, namn, beskrivning.
6. Lägg till ett API URL-suffix. Suffixet är ett namn som identifierar det här specifika API:et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.
9. Du kan publicera API:et genom att associera det med en produkt. I det här fallet används den *obegränsade* produkten.  Om du vill att API:et ska publiceras och vara tillgänglig för utvecklare, lägger du till det till en produkt. Du kan göra det vid API-skapandet eller ställa in det senare.

    Produkter är associationer med en eller flera API:er. Du kan inkludera flera API:er och erbjuda dem till utvecklare via utvecklarportalen. Utvecklare måste först prenumerera på en produkt för att få åtkomst till API:n. När de prenumererar få de en prenumerationsnyckel som går att använda till alla API:er i produkten. Om du har skapat APIM-instansen är du redan administratör, så du prenumererar på alla produkter som standard.

    Som standard medföljer två exempelprodukter varje API Management-instans:

    * **Starter**
    * **Obegränsat**   
10. Välj **Skapa**.

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