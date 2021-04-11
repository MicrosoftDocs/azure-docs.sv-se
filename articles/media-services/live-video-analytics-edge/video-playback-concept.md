---
title: Videouppspelning – Azure
description: Platshållare
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2020d64538b2fcc846ab9a146e2fc95325abd26b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063380"
---
# <a name="video-playback"></a>Videouppspelning 

## <a name="suggested-pre-reading"></a>Föreslagen för läsning 

* [Real tids analys av video i IoT Edge översikt](overview.md)
* [Video analys i real tid med IoT Edge terminologi](terminology.md)
* [Media diagram-koncept](media-graph-concept.md)

## <a name="overview"></a>Översikt  

Du kan använda [medie diagram](media-graph-concept.md) för att spela in video i en Azure Media Services [till gång](terminology.md#asset). I det här dokumentet får du lära dig om de steg du behöver vidta för att kunna spela upp en till gång med befintliga strömmande funktioner i Azure Media Services.

## <a name="streaming-endpoint"></a>Slutpunkt för direktuppspelning 

Du kan använda Azure Media Services för att [strömma](terminology.md#streaming) till gången till Videos pelare med branschstandardiserade http-baserade medie strömnings protokoll som http Live Streaming (HLS) och MPEG-streck. Den här konverteringen av media från inspelat innehåll till strömmande format hanteras av en [slut punkt för direkt uppspelning](../latest/streaming-endpoint-concept.md), vilket är en resurs som du måste etablera i ditt Azure Media Service-konto.

## <a name="streaming-policy"></a>Strömmande princip 

Azure Media Services erbjuder olika metoder för att skydda dina video strömmar, enligt beskrivningen i [skydda ditt innehåll med Media Services dynamisk krypterings](../latest/drm-content-protection-concept.md) artikel. På hög nivå är alternativen för innehålls skydd följande:

* **Direkt uppspelning** – där ingen kryptering används under strömningen.
* **Använd Advanced Encryption Standard (AES-128)** – och implementera en metod för att endast leverera nycklar för att dekryptera videon till autentiserade användare.
* **Använd DRM-system (Digital Rights Management)** – om du vill kontrol lera användning, ändring och leverans av video till enheter som tillämpar dessa principer.

För att uppnå innehålls skydd kan du definiera och skapa en [strömmande princip](../latest/streaming-policy-concept.md) i ditt Media Service-konto och använda den för att strömma alla till gångar (förutsatt att alla strömmar har samma krav för säkerhet). Du kan också använda någon av de fördefinierade principerna (till exempel Predefined_ClearStreamingOnly).

## <a name="streaming-locator"></a>Strömmande positionerare  

När du har en slut punkt för direkt uppspelning som har startats i ditt medie tjänst konto och en definierad strömmande princip, kan du fortsätta att strömma inspelade media från en till gång via HLS-eller tank streck. Webb-och mobilappar behöver en URL som pekar på den HLS eller streck data strömmen. Du kan bygga den här URL: en med hjälp av [streaming Locator](../latest/streaming-locators-concept.md). Som det beskrivs i artikeln, och som visas i [skapa ett exempel på en strömmande plats och skapa URL: er](../latest/create-streaming-locator-build-url.md) , sammanställs strömnings-URL: en från slut punkten för direkt uppspelning, strömnings princip och strömmande positionerare.

## <a name="content-recorded-using-file-sink"></a>Innehåll som registrerats med filsink  

Som det beskrivs i [medie Graph-filmottagaren](media-graph-concept.md#file-sink)kan du använda medie diagram för att spela in videor till det lokala fil systemet på Edge-enheten med hjälp av en fil mottagare i medie diagrammet. Fil mottagaren genererar [MP4](https://developer.mozilla.org/docs/Web/Media/Formats/Containers#MP4) -filer och du kan använda HTML5- [ &lt; video &gt; ](https://developer.mozilla.org/docs/Web/HTML/Element/video) elementet för att spela upp sådant innehåll. 

## <a name="next-steps"></a>Nästa steg

[Azure IoT Edge](../../iot-edge/index.yml)
<!--
## Next steps

[Playback recording](playback-recording-how-to.md)
-->
