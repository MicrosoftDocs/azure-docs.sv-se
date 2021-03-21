---
title: 'Så här skyddar du indatamängds begränsad enhet med Azure AD och Azure Maps REST API: er'
titleSuffix: Azure Maps
description: 'Så här konfigurerar du ett webbläsarbaserat program som stöder inloggning till Azure AD och anropar Azure Maps REST-API: er.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895638"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Skydda en inmatad begränsad enhet med Azure AD och Azure Maps REST-API: er

Den här guiden beskriver hur du skyddar offentliga program eller enheter som inte kan lagra hemligheter på ett säkert sätt eller som accepterar inläsade webbläsare. De här typerna av program är under kategori av IoT eller sakernas internet. Några exempel på dessa program kan vara: smarta TV-enheter eller program för sensor data sändning. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Skapa en program registrering i Azure AD

> [!NOTE]
> * **Nödvändig läsning:** [Scenario: Skriv bords program som anropar webb-API: er](../active-directory/develop/scenario-desktop-overview.md)
> * I följande scenario används enhets kod flödet, som inte omfattar en webbläsare för att hämta en token.

Skapa ett enhets baserat program i Azure AD för att aktivera Azure AD-inloggning. Det här programmet kommer att beviljas åtkomst till Azure Maps REST-API: er.

1. I listan med Azure-tjänster i Azure Portal väljer du **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

2. Ange ett **namn**, Välj **konton i den här organisations katalogen endast** som den **typ av konto som stöds**. I **omdirigerings-URI: er** anger du **offentlig klient/ursprunglig (mobil & Desktop)** och lägger sedan till i `https://login.microsoftonline.com/common/oauth2/nativeclient` värdet. Mer information finns i Azure AD [Desktop-appen som anropar webb-API: er för registrering av appar](../active-directory/develop/scenario-desktop-app-registration.md). **Registrera** sedan programmet.

    > [!div class="mx-imgBorder"]
    > ![Lägg till registrerings information för namn och omdirigerings-URI](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Navigera till **autentisering** och aktivera **behandla program som en offentlig klient**. Detta aktiverar autentisering med enhets kod med Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Aktivera app-registrering som offentlig klient](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Välj sedan **API-behörigheter**  >  **Lägg till en behörighet**. Sök efter och välj **Azure Maps** under **API: er som används i organisationen**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

5. Markera kryss rutan bredvid **åtkomst Azure Maps** och välj sedan **Lägg till behörigheter**.

    > [!div class="mx-imgBorder"]
    > ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

6. Konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC) för användare eller grupper. Se [bevilja rollbaserad åtkomst för användare att Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Lägg till kod för att hämta token Flow i programmet, för implementerings information, se [enhetens kod flöde](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). När du hämtar token refererar du till omfånget: `user_impersonation` som valdes i tidigare steg.

> [!Tip]
> Använd Microsoft Authentication Library (MSAL) för att hämta åtkomsttoken. Se rekommendationer i [Desktop-appen som anropar webb-API: kod konfiguration](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Skapa HTTP-begäran med den hämtade token från Azure AD och skicka begäran med en giltig HTTP-klient.

### <a name="sample-request"></a>Exempel förfrågan
Här är ett exempel på en begäran om att ladda upp ett enkelt geografiskt område som visas som en cirkel geometri med hjälp av en mitt punkt och en radie.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 Exempel texten nedan finns i en netjson:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Exempel svar:

Sidhuvud
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Brödtext
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Nästa steg

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)