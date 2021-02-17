---
title: Klicka på analys-plugin-programmet för automatisk insamling för Application Insights JavaScript SDK
description: Hur du installerar och använder klicka på analys-plugin-programmet för automatisk insamling för Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: 5ad3e1a5a4ff47fe3d5fee8b8bc79235838995b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593623"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Klicka på analys-plugin-programmet för automatisk insamling för Application Insights JavaScript SDK

Det här plugin-programmet spårar automatiskt klicknings händelser på webb sidor och använder data-* attribut på HTML-element för att fylla i Event-telemetri.

## <a name="getting-started"></a>Komma igång

Användare kan konfigurera plugin-programmet för att klicka med Auto-Collection via NPM.

### <a name="npm-setup"></a>NPM-installation

Installera NPM-paket:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Hur du använder plugin-programmet effektivt

1. Telemetridata som genereras från klicknings händelserna lagras som `customEvents` i avsnittet Application Insights i Azure Portal.
2. `name`CustomEvent fylls i baserat på följande regler:
    1.  Den `id` som anges i `data-*-id` kommer att användas som customEvent namn. Om till exempel det klickade HTML-elementet har attributet "data-Sample-ID" = "Button1", kommer "Button1" att vara customEvent namn.
    2. Om det inte finns något sådant attribut och om `useDefaultContentNameOrId` är inställt på `true` i konfigurationen, används det klickade elementets HTML-attribut `id` eller innehålls namn för elementet som customEvent-namn.
    3. Om `useDefaultContentNameOrId` är false blir customEvent-namnet "not_specified".

    > [!TIP]
    > Våra rekommendationer är att ställa in `useDefaultContentNameOrId` på sant för att skapa meningsfulla data.  
3. `parentDataTag` gör två saker:
    1. Om den här taggen finns hämtar plugin `data-*` -programmet attributen och värdena från alla överordnade HTML-element för det klickade elementet.
    2. För att förbättra effektiviteten använder plugin-programmet den här taggen som en flagga när den påträffas kommer den att stoppas av ytterligare bearbetning av DOM (Document Object Model) uppåt.
    
    > [!CAUTION]
    > När `parentDataTag` har använts börjar SDK söka efter överordnade taggar i hela programmet och inte bara det HTML-element där du använde det.
4. `customDataPrefix` anges av användaren ska alltid börja med `data-` , till exempel `data-sample-` . I HTML `data-*` utgörs de globala attributen av en klass med attribut som kallas anpassade dataattribut, vilket innebär att ägande information utbyts mellan HTML-och dess dom-representation av skript. Äldre webbläsare (Internet Explorer, Safari) släpper attribut som inte kan tolkas, såvida de inte börjar med `data-` .

    `*`I `data-*` kan ersättas med ett namn som följer [produktions regeln för XML-namn](https://www.w3.org/TR/REC-xml/#NT-Name) med följande begränsningar:
    - Namnet får inte börja med "XML", vilket innebär att det används för dessa bokstäver.
    - Namnet får inte innehålla något semikolon (U + 003A).
    - Namnet får inte innehålla versala bokstäver.

## <a name="configuration"></a>Konfiguration

| Namn                  | Typ                               | Standardvärde | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autofånga           | boolean                            | true    | Automatisk avbildnings konfiguration.                                                                                                         |
| motringningsalternativ              | [IValueCallback](#ivaluecallback)  | null    | Konfigurationen för motringning.                                                                                                                 |
| pageTags              | sträng                             | null    | Sid etiketter.                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | Anpassade datataggar har angetts för att åsidosätta standardtaggar som används för att avbilda Klicka på data.                                                           |
| urlCollectHash        | boolean                            | falskt   | Aktiverar loggning av värden efter "#"-tecknen i URL: en.                                                                          |
| urlCollectQuery       | boolean                            | falskt   | Aktiverar loggning av frågesträngen för URL: en.                                                                                      |
| behaviorValidator     | Funktion                           | null  | Callback-funktionen som ska användas för `data-*-bhvr` värde valideringen. Mer information finns i [avsnittet behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | sträng (eller) tal                 | ''      | Standard funktions värde när högerklickning har inträffat. Värdet kommer att åsidosättas om elementet har `data-*-bhvr` attributet. |
| dropInvalidEvents     | boolean                            | falskt   | Flagga för att släppa händelser som inte är användbara för att klicka på data.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Namn               | Typ     | Standardvärde | Beskrivning                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Funktion | null    | Funktion för att åsidosätta standard beteendet för pageName-infånget.                           |
| pageActionPageTags | Funktion | null    | En callback-funktion som ökar standard pageTags som samlas in under pageAction-händelsen.  |
| contentName        | Funktion | null    | En callback-funktion som fyller i anpassade contentName.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Namn                      | Typ    | Standardvärde   | Standard tag gen som ska användas i HTML |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | falskt     | E.t.         |Samlar in standard-HTML-attribut för contentName när ett visst element inte är taggat med standard-customDataPrefix eller när customDataPrefix inte anges av användaren. |
| customDataPrefix          | sträng  | `data-`   | `data-*`| Automatiskt insamling av innehålls namn och värde för element som är taggade med det angivna prefixet. Kan till exempel `data-*-id` `data-<yourcustomattribute>` användas i HTML-taggarna.   |
| aiBlobAttributeTag        | sträng  | `ai-blob` |  `data-ai-blob`| Plugin-programmet stöder ett JSON-BLOB-attribut i stället för enskilda `data-*` attribut. |
| metaDataPrefix            | sträng  | null      | Ej tillämpligt  | Automatiskt hämtning av HTML-huvudets meta-element namn och innehåll med angivet prefix vid hämtning. Kan till exempel `custom-` användas i HTML meta-taggen. |
| captureAllMetaDataContent | boolean | falskt     | E.t.   | Samla automatiskt in alla HTML-huvudets meta-element namn och innehåll. Standardvärdet är false. Om aktive rad åsidosätts den angivna metaDataPrefix. |
| parentDataTag             | sträng  | null      |  Ej tillämpligt  | Slutar att gå igenom DOM för att avbilda innehålls namn och värde för element när de påträffas med den här taggen. Kan till exempel `data-<yourparentDataTag>` användas i HTML-taggarna.|
| dntDataTag                | sträng  | `ai-dnt`  |  `data-ai-dnt`| HTML-element med det här attributet ignoreras av plugin-programmet för att samla in telemetridata.|

### <a name="behaviorvalidator"></a>behaviorValidator

BehaviorValidator-funktionerna kontrollerar automatiskt att taggade beteenden i kod följer en fördefinierad lista. Detta säkerställer att taggade beteenden överensstämmer med företagets etablerade taxonomi. Det krävs eller förväntas inte att de flesta Azure Monitor kunder kommer att använda detta, men det är tillgängligt för avancerade scenarier. Det finns tre olika återanrops funktioner i behaviorValidator som visas som en del av tillägget. Användare kan dock använda sina egna återanrops funktioner om de exponerade funktionerna inte löser ditt krav. Avsikten är att ta med dina egna beteende data strukturer, plugin-programmet använder den här verifierings funktionen när beteendet extraheras från datataggarna.

| Name                   | Beskrivning                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Använd den här motringning funktionen om dina beteende data strukturer är en sträng mat ris.|
| BehaviorMapValidator   | Använd den här motringning funktionen om dina beteende data strukturer är en ord lista.       |
| BehaviorEnumValidator  | Använd den här motringning funktionen om dina beteende data strukturer är en uppräkning.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Exempel på användning med behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Exempelapp

[Enkel webbapp med att klicka på plugin-programmet för automatisk insamling aktiverat](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Nästa steg

- Ta en titt på [GitHub-lagringsplatsen](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) och [NPM-paketet](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) för plugin-programmet för automatisk insamling av en analys.
- Använd [händelse analys i användnings miljö](usage-segmentation.md) för att analysera de bästa klicken och segmentera med tillgängliga dimensioner.
- Hitta Klicka på data under innehålls fält i customDimensions-attributet i CustomEvents-tabellen i [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query). Ytterligare vägledning finns i [exempel appen](https://go.microsoft.com/fwlink/?linkid=2152871) .
- Skapa en [arbets bok](../visualize/workbooks-overview.md) för att skapa anpassade visualiseringar av Klicka på data.
