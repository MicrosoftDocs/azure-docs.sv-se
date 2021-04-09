---
title: Komma igång med Microsoft Azure Maps-kontrollen för webb kartan
description: Ta reda på hur du lägger till kartor i webb program och mobilappar med hjälp av Kartkontroll JavaScript-biblioteket för klient sidan i Azure Maps. Se så här lokaliserar du kartor.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b283cacf94ff3567428787e50a3d7db9ba91fd9c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864048"
---
# <a name="use-the-azure-maps-map-control"></a>Använda kartkontrollen i Azure Maps

Med Kartkontroll på klient sidans JavaScript-bibliotek kan du återge kartor och inbäddade Azure Maps funktioner i ditt webb-eller mobil program.

I den här dokumentationen används Azure Maps Web SDK, men Azure Maps tjänsterna kan användas med valfri kart kontroll. [Här](open-source-projects.md#third-part-map-control-plugins) följer några populära kart kontroller med öppen källkod som Azure Mapss teamet har skapat plugin-program för.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Kartkontroll på en webb sida måste du ha något av följande krav:

* [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account) och [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

* Hämta dina Azure Active Directory-autentiseringsuppgifter (AAD) med [autentiseringsalternativ](/javascript/api/azure-maps-control/atlas.authenticationoptions).

## <a name="create-a-new-map-in-a-web-page"></a>Skapa en ny karta på en webb sida

Du kan bädda in en karta på en webb sida med hjälp av Kartkontroll JavaScript-bibliotek på klient sidan.

1. Skapa en ny HTML-fil.

2. Läs in Azure Maps Web SDK. Du kan välja ett av två alternativ:

    * Använd den globalt värdbaserade CDN-versionen av Azure Maps Web SDK genom att lägga till referenser till Java Script och StyleSheet i `<head>` HTML-filens element:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Läs in Azure Maps Web SDK-källkod lokalt med [Azure-Maps-Control NPM-](https://www.npmjs.com/package/azure-maps-control) paketet och var värd för den med din app. Det här paketet innehåller även TypeScript-definitioner.

      > **NPM installera Azure-Maps-Control**

    Lägg sedan till referenser till Azure Maps-formatmallen i `<head>` filens element:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Typescript-definitioner kan importeras till ditt program genom att lägga till följande kod:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Om du vill rendera kartan så att den fyller hela sidans brödtext lägger du till följande `<style>` element i `<head>` elementet.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. Lägg till ett-element i sidans huvuddel `<div>` och ge det ett `id` av **myMap**.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Nu ska vi initiera kart kontrollen. För att kunna autentisera kontrollen måste du antingen äga en Azure Maps prenumerations nyckel eller använda autentiseringsuppgifter för Azure Active Directory (AAD) med [autentiseringsalternativ](/javascript/api/azure-maps-control/atlas.authenticationoptions).

    Om du använder en prenumerations nyckel för autentisering, kopiera och klistra in följande skript element i `<head>` elementet och under det första `<script>` elementet. Ersätt `<Your Azure Maps Key>` med din Azure Maps primära prenumerations nyckel.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Om du använder Azure Active Directory (AAD) för autentisering, kopiera och klistra in följande skript element i `<head>` elementet och under det första `<script>` elementet.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Mer information om autentisering med Azure Maps finns i [autentisering med Azure Maps](azure-maps-authentication.md) -dokument. Dessutom kan en lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps hittas [här](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >I det här exemplet har vi gått igenom `id` kartan `<div>` . Ett annat sätt att göra detta är att skicka i `HTMLElement` objektet genom `document.getElementById('myMap')` att skicka som den första parametern.

6. Om du vill kan det vara bra att lägga till följande `meta` element på `head` sidans element:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Genom att sätta ihop allt, ska din HTML-fil se ut ungefär så här:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Öppna filen i webbläsaren och visa den återgivna kartan. Det bör se ut som på bilden nedan:

   ![Kart bild som visar åter givnings resultat](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map&quot;></a>Lokalisera kartan

Azure Maps tillhandahåller två olika sätt att ställa in språk och regional vy för den renderade kartan. Det första alternativet är att lägga till den här informationen i det globala `atlas` namn området, vilket leder till att alla instanser av kart kontroll i appen används som standard för de här inställningarna. Följande ställer in språket på franska (&quot;fr-FR") och den regionala vyn till "Auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Det andra alternativet är att skicka den här informationen till kart alternativen när du läser in kartan så här:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Det går att läsa in flera kart instanser på samma sida med olika språk-och region inställningar. Dessutom kan de här inställningarna uppdateras när kartan har lästs in med hjälp av `setStyle` kartans funktion.

Här är ett exempel på Azure Maps med språket "fr-FR" och den regionala vyn inställt på "Auto".

![Kart bild som visar etiketter på franska](./media/how-to-use-map-control/websdk-localization.png)

En fullständig lista över språk som stöds och nationella vyer beskrivs [här](supported-languages.md).

## <a name="azure-government-cloud-support"></a>Azure Government Cloud Support

Azure Maps Web SDK stöder Azure Government molnet. Alla JavaScript-och CSS-URL: er som används för att komma åt Azure Maps Web SDK förblir desamma. Följande uppgifter måste göras för att ansluta till Azure Government Cloud-versionen av Azure Mapss plattformen.

När du använder den interaktiva kart kontrollen lägger du till följande kodrad innan du skapar en instans av `Map` klassen.

```javascript
atlas.setDomain('atlas.azure.us');
```

Se till att använda Azure Maps autentiseringsinformation från Azure Government moln plattform när du autentiserar kartan och tjänsterna.

När du använder modulen tjänster måste domänen för tjänsterna anges när du skapar en instans av en API URL-slutpunkt. Följande kod skapar till exempel en instans av `SearchURL` klassen och pekar domänen i Azure Government molnet.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Ändra URL-domänen till om du vill få åtkomst till Azure Maps REST-tjänster direkt `atlas.azure.us` . Om du till exempel använder Search API-tjänsten, ändra URL-domänen från `https://atlas.microsoft.com/search/` till `https://atlas.azure.us/search/` .

## <a name="javascript-frameworks"></a>JavaScript-ramverk

Om du utvecklar med ett JavaScript-ramverk kan något av följande projekt med öppen källkod vara användbart:

* [ng – Azure-Maps](https://github.com/arnaudleclerc/ng-azure-maps) -vinkel 10-omslutning runt Azure Maps.
* [AzureMapsControl. Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – en Azure Maps blixt komponent.
* [Azure Maps Reakta komponent](https://github.com/WiredSolutions/react-azure-maps) – ett reaktat omslutning för kontrollen Azure Maps.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) – en Azure Maps komponent för Vue-program.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar och interagerar med en karta:

> [!div class="nextstepaction"]
> [Skapa en karta](map-create.md)

Lär dig hur du formaterar en karta:

> [!div class="nextstepaction"]
> [Välj ett kartformat](choose-map-style.md)

Lär dig metod tips och se exempel:

> [!div class="nextstepaction"]
> [Bästa praxis](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)

En lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns i:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
