---
title: Så här skyddar du ett enda sid program med användar inloggning
titleSuffix: Azure Maps
description: Så här konfigurerar du ett enda webb program som stöder enkel inloggning med Azure AD med Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895621"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Skydda ett enda sid program med användar inloggning

Följande guide gäller ett program som finns på en innehålls Server eller som har minimala webb server beroenden. Programmet tillhandahåller skyddade resurser som endast skyddas till Azure AD-användare. Målet med scenariot är att göra det möjligt för webb programmet att autentisera till Azure AD och anropa Azure Maps REST-API: er för användarens räkning.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Skapa en program registrering i Azure AD

Skapa webb programmet i Azure AD för användare för att logga in. Webb programmet delegerar användar åtkomsten till Azure Maps REST-API: er.

1. I listan med Azure-tjänster i Azure Portal väljer du **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

2. Ange ett **namn**, Välj en **Support konto typ**, ange en omdirigerings-URI som representerar URL: en som Azure AD utfärdar token och är URL: en där kart kontrollen är värd. Ett detaljerat exempel finns [Azure Maps Azure AD-exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Välj **Registrera**.  

3. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Under **Appregistreringar** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Sök efter och välj **Azure Maps** under **API: er som används i organisationen**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

4. Markera kryss rutan bredvid **åtkomst Azure Maps** och välj sedan **Lägg till behörigheter**.

    > [!div class="mx-imgBorder"]
    > ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

5. Aktivera `oauth2AllowImplicitFlow` . Om du vill aktivera det går du till avsnittet **manifest** i appens registrering `oauth2AllowImplicitFlow` `true` .

6. Kopiera ID för Azure AD-appen och Azure AD-klient-ID: t från App-registreringen som ska användas i webb-SDK: n. Lägg till registrerings informationen för Azure AD-appen och `x-ms-client-id` från Azure Map-kontot till webb-SDK: n.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC) för användare eller grupper. Se [följande avsnitt för att aktivera Azure RBAC](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nästa steg

Mer förståelse för program scenario med en sida:
> [!div class="nextstepaction"]
> [Enkelsidig app](../active-directory/develop/scenario-spa-overview.md)

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)