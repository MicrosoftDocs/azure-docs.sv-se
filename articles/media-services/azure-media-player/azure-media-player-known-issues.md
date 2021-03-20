---
title: Azure Media Player kända problem
description: Den aktuella versionen har följande kända problem.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 05/11/2020
ms.openlocfilehash: a31c089971b7e70e70c5906480deb2b17c197b9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87043615"
---
# <a name="known-issues"></a>Kända problem #

Den aktuella versionen har följande kända problem:

## <a name="azure-media-player"></a>Azure Media Player ##

- Felaktigt konfigurerade kodare kan orsaka problem med uppspelningen
- Strömmar med tidsstämplar som är större än 2 ^ 53 kan ha uppspelnings problem.
  - Minskning: Använd 90-kHz video-och 44,1-kHz-ljudskala
- Ingen automatisk uppspelning på mobila enheter utan användar interaktion. Den blockeras av plattformen.
- Att söka nära discontinuities kan orsaka uppspelnings fel.
- Hämtning av stora presentationer kan leda till att användar gränssnittet låser sig.
- Det går inte att automatiskt spela upp samma källa igen när presentationen har slutförts.
  - Om du vill spela upp en källa igen efter att den har avslut ATS, måste du ange källan igen.
- Tomma manifest kan orsaka problem med spelaren.
  - Det här problemet kan uppstå när du startar en Live-ström och inte tillräckligt många segment finns i manifestet.
- Uppspelnings position är kanske utanför UI-SeekBar.
- Händelse ordningen är inte konsekvent i alla tech.
- Den buffrade egenskapen är inte konsekvent i Techs.
- nativeControlsForTouch måste vara false (standard) för att undvika att objektet inte stöder egenskapen eller metoden setControls
- Affischer måste nu vara absoluta URL: er
- Mindre stötande problem kan uppstå i användar gränssnittet när enheten använder hög kontrast läge
- URL: er som innehåller "%" eller "+" i den fullständigt avkodade strängen kan ha problem med att ställa in källan

## <a name="ad-insertion"></a>AD-infogning ##

- Annonser kan ha problem som infogas (vid behov eller Live) när en Ad-Blocker installeras i webbläsaren
- Mobila enheter kan ha problem med att spela upp annonser.
- MP4 Midroll-annonser stöds för närvarande inte av Azure Media Player.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- När innehållet är klart i DVR-fönstret i Live-innehåll fortsätter tids linjen att växa tills den söker efter det eller når slutet av presentationen.
- Live-presentationer i Firefox med MSE aktiverat har problem

- Till gångar som endast är ljud spelas bara tillbaka via AzureHtml5JS-Tech.
  - Om du vill spela upp till gångar utan ljud kan du göra det genom att infoga ett tomt ljud med [verktyget Azure Media Services Explorer](https://aka.ms/amse)
  - Anvisningar om hur du infogar tyst ljud finns [här](../previous/media-services-advanced-encoding-with-mes.md#silent_audio)

## <a name="flash"></a>Utvecklingsverktyget ##

- AES-innehåll spelas inte upp i Flash version 30.0.0.134 på grund av ett fel i Adobes caching-logik. Adobe har åtgärdat problemet och släppt det i 30.0.0.154
- Tekniska och HTTP-fel (t. ex. 404 nätverks-timeout) tar spelaren längre tid att återställa än andra tekniker.
- Klicka på video områden med blixt Tech spelar inte upp/pausa spelaren.
- Om användaren har installerat Flash men inte har behörighet att läsa in den på webbplatsen kan oändlig spinning ske. Detta beror på att Windows Media Player ser till att plugin-programmet är installerat och tillgängligt och att plugin-programmet håller på att köra innehållet. JavaScript-kod har skickats men webb läsar inställningarna har blockerat plugin-programmet från att köras tills användaren har accepterat meddelandet för att tillåta plugin-programmet. Detta kan inträffa i alla webbläsare.  

## <a name="silverlight"></a>Silverlight ##

- Funktioner som saknas
- Tekniska och HTTP-fel (t. ex. 404 nätverks-timeout) tar spelaren längre tid att återställa än andra tekniker.
- Safari och Firefox på Mac-uppspelning med Silverlight måste uttryckligen definiera `"http://` eller `https://` för källan.
- Om ett API saknas för den här Tech, returnerar det vanligt vis null.
- Om användaren har installerat Flash men inte har behörighet att läsa in den på webbplatsen kan oändlig spinning ske. Detta beror på att Windows Media Player ser till att plugin-programmet är installerat och tillgängligt och att plugin-programmet håller på att köra innehållet. JavaScript-kod har skickats men webb läsar inställningarna har blockerat plugin-programmet från att köras tills användaren har accepterat meddelandet för att tillåta plugin-programmet. Detta kan inträffa i alla webbläsare.  

## <a name="native-html5"></a>Inbyggd HTML5 ##

- HTML5-Tech skickar endast canplaythrough-händelse för första uppsättnings källan.
- Den här Tech-tekniken stöder bara vad webbläsaren har implementerat.  Vissa funktioner kan saknas i den här Tech-tekniken.  
- Om ett API saknas för den här Tech, returnerar det vanligt vis null.
- Det finns problem med under texter och under texter på den här Tech-tekniken. De kanske inte är tillgängliga eller kan visas på den här Tech-tekniken.
- Att ha begränsad bandbredd i HLS/HTML5-Tech-scenariot resulterar i ljud uppspelning utan video.

### <a name="microsoft"></a>Microsoft ###

- IE8-uppspelning fungerar inte för närvarande på grund av inkompatibilitet med ECMAScript 5
- I IE och vissa versioner av Edge kan du inte ange hel skärms läge genom att Tabba till knappen och välja den eller använda F/f-kortkommandot.

## <a name="google"></a>Google ##

- Flera kodnings profiler i samma manifest har vissa uppspelnings problem i Chrome och rekommenderas inte.
- Chrome kan inte spela upp HE-AAC med AzureHtml5JS. Följ informationen i [fel spåraren](https://bugs.chromium.org/p/chromium/issues/detail?id=534301).
- Från och med Chrome V58, måste Widevine-innehåll läsas in/spelas upp via https://-protokollet annars kommer uppspelningen att Miss Miss läge.

## <a name="mozilla"></a>Mozilla ##

- Ljud Ströms växeln kräver att ljud strömmar har samma codec-privata data när du använder AzureHtml5JS. Firefox-plattformen kräver detta.

## <a name="apple"></a>Apple ##

- Safari på Mac aktiverar ofta energi spar läge som standard med inställningen "stoppa plugin-program för att spara energi", vilket blockerar plugin-program som Flash och Silverlight när de tror att de inte prioriteras för användaren. Det här blocket blockerar inte plugin-programmets befintliga funktioner. Med den här standard techOrder kan detta orsaka problem vid försök att spela upp igen
  - Minskning 1: om Videos pelaren är "framtill" eller "centrera" (inom en ram på 3000 x 3000 pixlar som börjar i det övre vänstra hörnet av dokumentet) bör det fortfarande spelas upp.
  - Minskning 2: ändra techOrder för Safari till ["azureHtml5JS", "HTML5"]. Den här lösningen har indirekt inte alla funktioner som är tillgängliga i Flash-Tech.
- PlayReady-innehåll via Silverlight kan ha problem som spelas upp i Safari.
- Innehåll för AES och begränsad token spelas inte upp med iOS och äldre Android-enheter.
  - För att uppnå det här scenariot måste du lägga till en proxy i din tjänst.
- Standard Skin för iOS-telefon visas genom.
- iPhone-uppspelning sker alltid i den inbyggda skärm spelaren.
  - Funktioner, inklusive under texter, får inte finnas kvar i den här icke-inline-uppspelningen.
- När Live-presentationen upphör kommer iOS-enheter inte att sluta fungera korrekt.
- iOS tillåter inte DVR-funktioner.
- Det går bara att göra en ljud Ströms växling för iOS, även om gränssnittet för iOSs inbyggda spelare och kräver att ljud strömmar har samma codec-privata data
- Äldre versioner av Safari kan eventuellt innehålla problem som spelas upp direkt strömmar.

## <a name="older-android"></a>Äldre Android ##

- Innehåll för AES och begränsad token spelas inte upp med iOS och äldre Android-enheter.
  - För att uppnå det här scenariot måste du lägga till en proxy i din tjänst.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
