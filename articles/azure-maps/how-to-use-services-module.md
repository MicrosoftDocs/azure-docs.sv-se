---
title: Använd modulen Azure Maps Services | Microsoft Azure Maps
description: Lär dig mer om modulen Azure Maps tjänster. Se hur du läser in och använder detta hjälp bibliotek för att få åtkomst till Azure Maps REST-tjänster i webb-eller Node.js program.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008607"
---
# <a name="use-the-azure-maps-services-module"></a>Använd modulen Azure Maps tjänster

Azure Maps Web SDK tillhandahåller en *Services-modul*. Den här modulen är ett hjälp bibliotek som gör det enkelt att använda Azure Maps REST-tjänsterna i webb-eller Node.js program med hjälp av Java Script eller TypeScript.

## <a name="use-the-services-module-in-a-webpage"></a>Använda modulen tjänster på en webb sida

1. Skapa en ny HTML-fil.
1. Läs in Azure Maps Services-modulen. Du kan läsa in den på ett av två sätt:
    - Använd den globalt värdbaserade Azure Content Delivery Network-versionen av modulen Azure Maps tjänster. Lägg till en skript referens till `<head>` filens element:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - Du kan också läsa in modulen tjänster för Azure Maps Web SDK-källkod lokalt genom att använda [Azure-Maps-rest NPM-](https://www.npmjs.com/package/azure-maps-rest) paketet och sedan vara värd för det med din app. Det här paketet innehåller även TypeScript-definitioner. Använd det här kommandot:
    
        > **npm install azure-maps-rest**
    
        Lägg sedan till en skript referens till `<head>` filens element:

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. Skapa en pipeline för autentisering. Pipelinen måste skapas innan du kan initiera en klient slut punkt för tjänst-URL. Använd dina egna Azure Maps konto nycklar eller Azure Active Directory (Azure AD) autentiseringsuppgifter för att autentisera en Azure Maps Sök tjänst klient. I det här exemplet kommer URL-klienten för Sök tjänsten att skapas. 

    Om du använder en prenumerations nyckel för autentisering:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Om du använder Azure AD för autentisering:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Mer information finns i [autentisering med Azure Maps](azure-maps-authentication.md).

1. I följande kod används den nya URL-klienten för Azure Maps Search-tjänsten för att koda en adress: "1 Microsoft Way, Redmond, WA". I koden används `searchAddress` funktionen och resultatet visas som en tabell i bröd texten på sidan.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    Här är det fullständiga kod exemplet som körs:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Använda modulen tjänster" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>med hjälp av modulen tjänster</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure Government Cloud Support

Azure Maps Web SDK stöder Azure Government molnet. Alla JavaScript-och CSS-URL: er som används för att komma åt Azure Maps Web SDK förblir desamma, men följande aktiviteter måste göras för att ansluta till Azure Government moln versionen av Azure Maps-plattformen.

När du använder den interaktiva kart kontrollen lägger du till följande kodrad innan du skapar en instans av `Map` klassen. 

```javascript
atlas.setDomain('atlas.azure.us');
```

Se till att använda en Azure Maps autentiseringsinformation från Azure Government moln plattform när du autentiserar kartan och tjänsterna.

När du använder modulen tjänster måste domänen för tjänsterna anges när du skapar en instans av en API URL-slutpunkt. Följande kod skapar till exempel en instans av `SearchURL` klassen och pekar domänen i Azure Government molnet.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ändra URL-domänen till om du vill få åtkomst till Azure Maps REST-tjänster direkt `atlas.azure.us` . Om du till exempel använder Search API-tjänsten, ändra URL-domänen från `https://atlas.microsoft.com/search/` till `https://atlas.azure.us/search/` .

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

Mer kod exempel som använder modulen tjänster finns i följande artiklar:

> [!div class="nextstepaction"]
> [Visa Sök resultat på kartan](./map-search-location.md)

> [!div class="nextstepaction"]
> [Hämta information från en koordinat](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Visa anvisningar från A till B](./map-route.md)