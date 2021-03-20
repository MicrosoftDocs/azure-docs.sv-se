---
title: Så här skyddar du ett webb program med interaktiv enkel inloggning
titleSuffix: Azure Maps
description: Så här konfigurerar du ett webb program som stöder enkel inloggning med Azure AD med Azure Maps Web SDK med OpenID Connect-protokollet.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895579"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Skydda ett webb program med användar inloggning

Följande guide avser ett program som finns på webb servrar, underhåller flera affärs scenarier och distribuerar till webb servrar. Programmet har krav på att tillhandahålla skyddade resurser som endast skyddas till Azure AD-användare. Målet med scenariot är att göra det möjligt för webb programmet att autentisera till Azure AD och anropa Azure Maps REST-API: er för användarens räkning.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Skapa en program registrering i Azure AD

Du måste skapa webb programmet i Azure AD för att användarna ska kunna logga in. Det här webb programmet kommer sedan att delegera användar åtkomst till Azure Maps REST-API: er.

1. I listan med Azure-tjänster i Azure Portal väljer du **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

2. Ange ett **namn**, Välj en **Support konto typ**, ange en omdirigerings-URI som representerar URL: en som Azure AD utfärdar token och är URL: en där kart kontrollen är värd. Mer information finns i Azure AD [-Scenario: webbappen som loggar in användare](../active-directory/develop/scenario-web-app-sign-user-overview.md). Slutför de tillhandahållna stegen från Azure AD-scenariot.  

3. När program registreringen är klar kontrollerar du att program inloggningen fungerar för användare. När inloggningen fungerar kan programmet beviljas delegerad åtkomst till Azure Maps REST-API: er.
    
4.  Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Välj sedan **API-behörigheter**  >  **Lägg till en behörighet**. Sök efter och välj **Azure Maps** under **API: er som används i organisationen**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

5. Markera kryss rutan bredvid **åtkomst Azure Maps** och välj sedan **Lägg till behörigheter**.

    > [!div class="mx-imgBorder"]
    > ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

6. Aktivera webb programmet för att anropa Azure Maps REST-API: er genom att konfigurera appens registrering med en program hemlighet. detaljerade anvisningar finns i [en webbapp som anropar webb-API: er för registrering av appar](../active-directory/develop/scenario-web-app-call-api-app-registration.md). En hemlighet krävs för att autentisera till Azure AD å användarens vägnar. Appens registrerings certifikat eller hemlighet ska lagras i ett säkert Arkiv för webb programmet som ska hämtas för att autentisera till Azure AD. 
   
   * Om programmet redan har konfigurerat en Azure AD App-registrering och en hemlighet kan det här steget hoppas över.

> [!Tip]
> Om programmet finns i en Azure-miljö rekommenderar vi att du använder [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) och en Azure Key Vault-instans för att få åtkomst till hemligheter genom att [förvärva en](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) åtkomsttoken för åtkomst till Azure Key Vault hemligheter eller certifikat. Information om hur du ansluter till Azure Key Vault för att hämta hemligheter finns i [självstudier för att ansluta via hanterad identitet](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Implementera en säker token-slutpunkt för Azure Maps Web SDK för att få åtkomst till en token. 
   
   * Ett exempel på en styrenhet för token finns i [Azure Maps Azure AD-exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * En icke-AspNetCore implementering eller annat finns i [Hämta token för appen](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) från Azure AD-dokumentationen.
   * Den säkra token-slutpunkten ansvarar för att returnera en åtkomsttoken för den autentiserade och auktoriserade användaren att anropa Azure Maps REST-API: er.

8. Konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC) för användare eller grupper. Se [bevilja rollbaserad åtkomst för användare](#grant-role-based-access-for-users-to-azure-maps).

9. Konfigurera webb program sidan med Azure Maps Web SDK för att komma åt den säkra token-slutpunkten. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nästa steg

Mer förståelse för webb program scenario:
> [!div class="nextstepaction"]
> [Scenario: webb program som loggar in användare](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps Azure AD Web App-exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)