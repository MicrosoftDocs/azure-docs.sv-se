---
title: Konfigurera PostMan för Azure Media Services v3 REST API
description: 'Den här artikeln visar hur du konfigurerar Postman så att den kan användas för att anropa Azure Media Services REST-API: er för (AMS).'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b0a521ddeee6d93ce809e5e6e85eefa1f01a3f12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953979"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Konfigurera PostMan för Media Services v3 REST API-anrop

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här artikeln visar hur du konfigurerar **Postman** så att den kan användas för att anropa Azure Media Services REST-API: er för (AMS). Artikeln visar hur du importerar miljö-och samlings filer till **Postman**. Samlingen innehåller grupperade definitioner av HTTP-begäranden som anropar Azure Media Services (AMS) REST-API: er. Miljöfilen innehåller variabler som används av samlingen.

Innan du börjar utveckla bör du läsa [utveckla med Media Services v3-API: er](media-services-apis-overview.md).

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./create-account-howto.md). Kom ihåg att komma ihåg resurs gruppens namn och namnet på Media Services kontot. 
- Hämta information som krävs för att [få åtkomst till API: er](./access-api-howto.md)
- Installera [Postman](https://www.getpostman.com/) REST-klienten för att köra REST API:er som visas i några AMS REST-självstudierna. 

    Vi använder **Postman** men ett REST-verktyg skulle vara lämpligt. Andra alternativ är: **Visual Studio Code** med rest-plugin-programmet eller **Telerik-Fiddler**. 

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="download-postman-files"></a>Hämta Postman-filer

Klona en GitHub-lagringsplats som innehåller Postman-samlingen och miljöfilerna.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Konfigurera Postman

### <a name="configure-the-environment"></a>Konfigurera miljön 

1. Öppna **Postman** -appen.
2. På höger sida om skärmen, väljer du alternativet **Hantera miljö**.

    ![Hantera miljö](./media/develop-with-postman/postman-import-env.png)
4. Från dialogrutan **Hantera miljö**, klickar du på **Importera**.
2. Bläddra till `Azure Media Service v3 Environment.postman_environment.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Miljön **Azure Media Service v3-miljö** läggs till.

    > [!Note]
    > Uppdatera åtkomstvariablerna med värden som du fick från avsnittet **Åtkomst till Media Services API** ovan.

7. Dubbelklicka på den valda filen och ange värden som du har fått genom att följa stegen i åtkomst till API.
8. Stäng dialogrutan.
9. Välj miljön **Azure Media Service v3-miljö** från listmenyn.

    ![Välj miljö](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurera samlingen

1. Klicka på **Importera** för att importera samlingsfilen.
1. Bläddra till `Media Services v3.postman_collection.json`-filen som hämtades när du klonade `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Välj filen **Media Services v3.postman_collection.json**.

    ![Importera en fil](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Hämta Azure AD-token 

Innan du börjar ändra AMS v3-resurser måste du hämta och ange Azure AD-token för autentisering av tjänstens huvud namn.

1. I det vänstra fönstret i Postman-appen väljer du "steg 1: Hämta AAD auth-token".
2. Välj sedan Hämta Azure AD-token för autentisering för tjänstens huvudnamn.
3. Tryck på **Skicka**.

    Följande **POST**-åtgärd skickas.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. Svaret kommer tillbaka med token och anger miljövariabeln AccessToken till token-värdet.  

    ![Hämta AAD-token](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Felsökning 

* Om programmet Miss lyckas med "HTTP 504: Gateway-tidsgräns" kontrollerar du att plats variabeln inte uttryckligen har angetts till ett annat värde än den förväntade platsen för Media Services kontot. 
* Om du får ett fel meddelande om att det inte gick att hitta kontot kontrollerar du också att egenskapen location i meddelandets JSON-meddelande har angetts till den plats där Media Servicess kontot finns. 

## <a name="see-also"></a>Se även

- [Skapa filter med Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-baserat REST-API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Nästa steg

- [Strömma filer med rest](stream-files-tutorial-with-rest.md).  
- [Självstudier: Koda en fjärrfil baserat på URL och strömma videon – REST](stream-files-tutorial-with-rest.md)
