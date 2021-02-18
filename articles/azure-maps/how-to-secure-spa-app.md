---
title: Så här skyddar du ett enda sid program med icke-interaktiv inloggning
titleSuffix: Azure Maps
description: Så här konfigurerar du ett enda webb program med icke-interaktiv rollbaserad åtkomst kontroll i Azure (Azure RBAC) och Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092722"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Så här skyddar du ett enda sid program med icke-interaktiv inloggning

Följande guide avser ett program som använder Azure Active Directory (Azure AD) för att tillhandahålla en åtkomsttoken för att Azure Maps program när användaren inte kan logga in på Azure AD. Det här flödet kräver värd för en webb tjänst som måste skyddas för att endast kunna nås av webb programmet för en enskild sida. Det finns flera implementeringar som kan utföra autentisering till Azure AD. Den här guiden utnyttjar produkten och Azure-funktionen för att hämta åtkomsttoken.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> Azure Maps har stöd för åtkomsttoken från användar inloggning/interaktiva flöden. Interaktiva flöden möjliggör ett mer begränsat omfång av åtkomst hantering och hemlig hantering.

## <a name="create-azure-function"></a>Skapa Azure-funktion

Skapa ett skyddat webb tjänst program som ansvarar för autentisering till Azure AD. 

1. Skapa en funktion i Azure Portal. Mer information finns i [skapa en Azure-funktion](../azure-functions/functions-get-started.md).

2. Konfigurera CORS-principen på Azure-funktionen så att den kan nås av webb programmet för en enskild sida. Detta skyddar webb läsar klienter till de tillåtna ursprungen för ditt webb program. Se [Lägg till CORS-funktioner](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Lägg till en systemtilldelad identitet](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) i Azure-funktionen för att skapa ett huvud namn för tjänsten för att autentisera till Azure AD.  

4. Bevilja rollbaserad åtkomst för den systemtilldelade identiteten till Azure Maps kontot. Mer information finns i [bevilja rollbaserad åtkomst](#grant-role-based-access) .

5. Skriv kod för Azure-funktionen för att hämta Azure Maps åtkomsttoken med hjälp av systemtilldelad identitet med en av de mekanismer som stöds eller REST-protokollet. Se [Hämta token för Azure-resurser](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Ett exempel på ett REST-protokoll:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Exempel svar:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Konfigurera säkerhet för Azure Function-HttpTrigger

   * [Skapa en funktions åtkomst nyckel](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * [Säker HTTP-slutpunkt](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) för Azure-funktionen i produktion.
   
7. Konfigurera webb programmet Azure Maps Web SDK. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Bevilja rollbaserad åtkomst

Du ger åtkomst till Azure-rollbaserad *åtkomst kontroll (Azure RBAC)* genom att tilldela den systemtilldelade identiteten till en eller flera roll definitioner för Azure. Om du vill visa de roll definitioner för Azure som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller** och Sök sedan efter roller som börjar med *Azure Maps*.

1. Gå till ditt **Azure Maps-konto**. Välj roll tilldelningen **åtkomst kontroll (IAM)**  >  .

    > [!div class="mx-imgBorder"]
    > ![Bevilja åtkomst med Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. På fliken **roll tilldelningar** under **roll** väljer du en inbyggd Azure Maps roll definition som **Azure Maps data läsare** eller **Azure Maps data deltagare**. Under **tilldela åtkomst till** väljer du **Funktionsapp**. Välj huvudobjektet efter namn. Välj sedan **Spara**.

   * Se information om att [tilldela Azure-roller](../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps inbyggda roll definitioner ger en mycket stor behörighet till många Azure Maps REST-API: er. För att begränsa API-åtkomsten till ett minimum, se [skapa en anpassad roll definition och tilldela den systemtilldelade identiteten](../role-based-access-control/custom-roles.md) till den anpassade roll definitionen. Detta gör att den minsta behörighet som krävs för att programmet ska kunna få åtkomst till Azure Maps.

## <a name="next-steps"></a>Nästa steg

Mer förståelse för program scenario med en sida:
> [!div class="nextstepaction"]
> [Enkelsidig app](../active-directory/develop/scenario-spa-overview.md)

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska andra exempel som visar hur du integrerar Azure AD med Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)