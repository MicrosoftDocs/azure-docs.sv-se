---
title: Azure Media Player plugin-Galleri
description: Den här artikeln innehåller en lista över plugin-program som är tillgängliga för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: d958d93731f38b4b553bca94f48c740c1b75cfd5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449858"
---
# <a name="azure-media-player-plugin-gallery"></a>Azure Media Player plugin-Galleri #

## <a name="plugins"></a>Plugin-program ##

| Namn på plugin                         | URL för demonstration                    | Källkod                | Beskrivning    |
|-------------------------------------|-----------------------------|----------------------------|----------------|
| Ytterligare funktioner                 | | | |
| **Nyårs!** AMP360Video                | [Demonstration](http://www.babylonjs.com/demos/amp360video/)                        | [GitHub](https://github.com/BabylonJS/Extensions/tree/master/Amp360Video)                     | Med plugin-programmet kan du visualisera 360-video i amp antingen på Skriv bordet eller i VR-kompatibla enheter. Den fullständiga dokumentationen finns [här](https://doc.babylonjs.com/extensions/amp360video): |
|  Beskrivning av Sprite                         | [Demonstration](https://www.smwcentral.net/?p=section&a=details&id=10301)                        | [GitHub](https://github.com/RickShahid/SpriteTip)                    | Azure Media Player (AMP) plugin-programmet för tids linje åter givning av en video miniatyr bild som genereras från Azure Media Services (AMS) Media Encoder Standard (marknads status). |
| Diagnostiskt överlägg                 | [Demonstration](https://openidconnectweb.azurewebsites.net/Diagnoverlay.html)                        | [GitHub](https://github.com/willzhan/diagnoverlay)                     | Detta plugin-program visar: alla nyckel parametrar, statistik för video, alla händelser i livs cykeln för video uppspelning och information om DRM-skydd, till exempel nyckel-ID, URL: er för licens hämtning, om den är skyddad.                                                                                                                                                                      |
| Kalkylator för bild frekvens och tidskod | Ingen demo tillgänglig | [GitHub](https://github.com/mconverti/media-services-javascript-azure-media-player-framerate-timecode-calculator-plugin)                     | Det här plugin-programmet beräknar bild frekvensen för videon baserat på `tfhd` / `trun` MP4-rutorna för det första MPEG-streck-videofragmentet, parsar tids skalan från klient manifestet för MPEG-streck och ger också ett sätt att generera tids koden för en specifik absolut tid från spelaren (samt ger den absoluta tiden för spelaren som anges för tidskod) |
| <strike>Uppspelnings hastighet</strike>                      | [Demonstration](https://azure-samples.github.io/media-services-javascript-Azure-Media-Player-playback-rate-plugin/)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Med det här plugin-programmet kan användarna kontrol lera vilken hastighet i videon. *OBS! den här funktionen är automatiskt tillgänglig i version AMP v 2.0.0 + men inaktive rad som standard.* Om du vill lära dig hur du aktiverar det kan du kolla in våra exempel [här](https://github.com/Azure-Samples/azure-media-player-samples) |
| Tips för hov rings tid                      | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-time-tip-plugin)                     | Visar ett tids tips över förlopps indikatorn vid mus hovring för att söka efter tid. *Obs! det här plugin-programmet är redan integrerat i amp* , men om du är intresse rad av att se hur det är programmerat kan du ta en titt.                                                                                                                 |
| Rubrik överlägg                       | [Demonstration](https://azure-samples.github.io/media-services-javascript-azure-media-player-title-overlay-plugin)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-title-overlay-plugin)                     | Överlägg som kan konfigureras med en video rubrik över skärmen |
| Markörer för tids linje                    | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timelinemarkers/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-timeline-markers-plugin)                     | Det här plugin-programmet tar i en matris med gånger och överlappar små markörer över förlopps indikatorn vid dessa tidpunkter. |
| Analys                           | | | |
| Application Insights                | [Blogg inlägg](https://azure.microsoft.com/blog/player-analytics-azure-media-player-plugin/)                   | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-application-insights-plugin)                     | Plugin-program som spårar dina spelares mått och portar till Power BI för en intuitiv grafisk representation av dina visnings upplevelser. |
| Google Analytics                    | Ej tillämpligt                         | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-google-analytics-plugin)                     | Google Analytics-plugin för Azure Media Player |
| Diagnostik                         | | | |
| Diagnostik-utdata                  | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/diagnosticslogger/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-diagnostic-logger-plugin)                     | Det här plugin-programmet visar en matris med diagnostik från spelaren, för att se hur den fungerar i praktiken, gå till demonstrations länken och öppna din JavaScript-konsol. |
| Enkel åtkomst                      | | | |
| Zooma in                             | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/zoom/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-zoom-plugin)                     | Det här plugin-programmet visar en drags aktive rad zoomnings skalning på spelarens skärm så att läsarna kan zooma in på ditt innehåll |
| Direkt under texter                       | [Blogg](https://azure.microsoft.com/blog/live-real-time-captions-with-azure-media-services-and-player/)inlägg i Azure,[SubPly-inlägg](http://www.subply.com/en/Products/AzureLiveCaptions.htm) | Ej tillämpligt | *Se post för mer information.* Slut punkt till slut punkt som har utformats för Live-textning inbyggt plugin-program för Azure Media Player, klicka på länken längst till vänster för att gå till SubPly-webbplatsen och lär dig mer om lösningen |
| Snabb tangenter                            | <strike>[Demo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)</strike>                        | <strike>[GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-hot-keys-plugin)</strike>                     | Med plugin-programmet för snabb tangenter kan du kontrol lera olika aspekter av spelaren med generiska plugin-kombinationer som F för helskärm, M för ljud av och piltangenterna för kontroll för förlopps indikator. *Obs! det här plugin-programmet har redan integrerats i AMP men du är kostnads fritt att använda det som en resurs* |
| Socialt                              | | | |
| Dela                               | [Demonstration](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/share/example.html)                        | [GitHub](https://github.com/Azure-Samples/media-services-javascript-azure-media-player-social-share-plugin)                     | Detta plugin-program lägger till en resurs-knapp i spelarens kontroll fält så att dina användare kan dela videon som de ser med sina vänner via Facebook, Twitter eller LinkedIn. |

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
