---
title: Azure Media Player hjälpmedel
description: Läs mer om de Azure Media Player inställningarna för hjälpmedel.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: 26e25f5391524020ef7968ba627997ea16a80921
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449910"
---
# <a name="accessibility"></a>Tillgänglighet #

Azure Media Player fungerar med skärm läsar funktioner som Windows skärm läsaren och Apple OSX/iOS VoiceOver. Alternativa taggar är tillgängliga för GRÄNSSNITTs knapparna och skärm läsaren kan läsa dessa alternativa Taggar när användaren navigerar till dem. Ytterligare konfigurationer kan ställas in på operativ system nivå.

## <a name="captions-and-subtitles"></a>Under texter och under texter ##

För tillfället stöder Azure Media Player för närvarande endast textning för händelser på begäran med WebVTT-format och Live-händelser med CEA 708. TTML-formatet stöds inte för tillfället. Med undertextning kan spelaren tillgodose och hjälpa en bredare publik, till exempel personer med nedsatt hörsel eller användare som vill kunna läsa med på ett annat språk. Undertexter även öka engagemanget video, förbättra förståelsen och göra det möjligt att visa videon i ljudkänsliga miljöer, t.ex. på en arbetsplats.  

## <a name="high-contrast-mode"></a>Hög kontrast läge ##

Standard gränssnittet i Azure Media Player är kompatibelt med de flesta visnings lägena enhet/webbläsare med hög kontrast. Konfigurationer kan ställas in på operativ system nivå.

## <a name="mobility-options"></a>Mobilitets alternativ ##

### <a name="tabbing-focus"></a>Fokusera på flikar ###

Fokus fokus, som tillhandahålls av allmänna HTML-standarder, är tillgänglig i Azure Media Player. För att kunna aktivera TABB-fokusering måste du lägga till `tabindex=0` (eller ett annat värde om du förstår hur TABB-ordningen påverkas i HTML) till HTML-koden `<video>` så här: `<video ... tabindex=0>...</video>` . På vissa plattformar kan fokus för kontrollerna bara finnas om kontrollerna är synliga och om plattformen stöder dessa funktioner.

När tabbordningen är aktive rad kan slutanvändaren effektivt navigera och kontrol lera Videos pelaren utan att behöva göra något med musen. Varje snabb meny eller ett element som kan kontrol leras kan navigera till genom att trycka på TABB-knappen och markera med RETUR eller blank steg. Genom att trycka på RETUR eller blank steg på en snabb meny expanderar du den så att slutanvändaren kan fortsätta tabba genom att välja ett meny alternativ. När du befinner dig i det sammanhang där det önskade objektet finns trycker du på retur eller blanksteg för att välja det.

### <a name="hotkeys"></a>Snabb tangenter ###

Azure Media Player stöder styrning via tangent bords snabb tangent. I en webbläsare är det enda sättet att styra det underliggande video elementet genom att fokusera på spelaren. När den är i fokus på spelaren kan snabb tangenterna styra spelarens funktion.  I tabellen nedan beskrivs olika snabbtangenter och deras associerade beteende:

| Snabbtangent              | Beteende                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Player startar/avslutar helskärmsläge                                  |
| M/m                  | Slå på/av ljud för spelaren                                          |
| Upp- och nedpil.    | Spelarens volym kommer öka/minska                                    |
| Vänster och höger pil | Videoförloppet ökar/minskar                                  |
| 0,1,2,3,4,5,6,7,8,9  | Video förloppet ändras till 0% \- 90% beroende på vilken nyckel som trycks ned |
| Klicka på åtgärd         | Videon spelas upp/pausar                                                   |

## <a name="next-steps"></a>Nästa steg

<!---Some context for the following links goes here--->
- [Azure Media Player snabb start](azure-media-player-quickstart.md)