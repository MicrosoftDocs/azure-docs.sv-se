---
title: Utforma filer för konvertering
description: Rekommendationer om hur du bäst placerar filer i indatafilen.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 9a094755dfb9381b1e4d6abdf1c0e6342d7427c1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308447"
---
# <a name="laying-out-files-for-conversion"></a>Utforma filer för konvertering

För att kunna bearbeta en till gång korrekt måste konverterings tjänsten kunna hitta alla indatafiler.
Dessa består av den huvudsakliga till gångs filen som konverteras och ofta några andra filer som sökvägar i till gångs filen refererar till.
Begäran om att konvertera en till gång har två parametrar som avgör hur konverterings tjänsten hittar dessa filer: `settings.inputLocation.blobPrefix` (som är valfritt) och `settings.inputLocation.relativeInputAssetPath` .
De dokumenteras fullständigt på sidan [konvertering REST API](conversion-rest-api.md) .
För att kunna utforma filer är det viktigast att notera att `BlobPrefix` fastställer fullständig uppsättning filer som är tillgängliga för konverterings tjänsten vid bearbetning av till gången.

> [!Note]
> Tjänsten laddar ned alla filer under indatamängden. BlobPrefix. Se till att fil namn och sökvägar inte överskrider [gränsen för Windows](https://docs.microsoft.com/windows/win32/fileio/maximum-file-path-limitation) -sökvägar för att undvika problem med tjänsten. 

## <a name="placing-files-so-they-can-be-found"></a>Placera filer så att de kan hittas

När en käll till gång använder externa filer lagras Sök vägarna till filerna i till gången.
Konverterings tjänsten måste tolka dessa sökvägar i ett fil system som inte är samma som det ursprungliga fil systemet.
Om Sök vägarna lagras som relativa sökvägar och den relativa platsen mellan käll till gången och filen den refererar till är oförändrad, är det enkelt för konverterings tjänsten att hitta den refererade filen.

> [!Note]
> Vi rekommenderar att du placerar filer i indatafilen så att de relativa platserna för filerna är desamma som när du skapade till gången.

> [!Note]
> Föredra att skapa till gångar som har relativa sökvägar.
> Självstudien om hur du [ställer in material för 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) ger ett 3ds Max-exempel på hur du ser till att en till gång använder relativa sökvägar.

## <a name="finding-textures"></a>Hitta texturer

På grund av många sätt som till gångar kan genereras måste konverterings tjänsten vara flexibel.
I synnerhet måste det hantera situationer där Sök vägarna i till gången och placeringen av texturer inte matchar exakt.
Ett exempel är när till gångar skapas som innehåller absoluta sökvägar, eftersom dessa sökvägar aldrig kommer att matcha det fil system som används av konverterings tjänsten.
För att hantera den här situationen bland andra använder vi en metod för bästa prestanda för att hitta texturer.

Algoritmen för att hitta texturer är följande: Ange en sökväg som lagrad i en till gång, hitta det längsta under Sök vägs suffix som, när det används som en relativ sökväg från käll till gångens plats, riktar en fil som finns.
Om ingen sådan under Sök väg (inklusive hela sökvägen) riktar sig till en fil, betraktas strukturen som saknas.

Tänk på följande contrived-fil system: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Om FBX. refererar till en struktur med den relativa sökvägen `..\Textures\MyAssetTextures\myTexture.png` kommer konverterings tjänsten att använda fil E. I själva verket kan den använda vilken som helst av filerna A, C och E, om de finns, med fil E att föredra eftersom den hittas med det längsta suffixet.
Filer B och D kommer aldrig att användas, eftersom `Textures\myTexture.png` ingår inte i något suffix för den lagrade sökvägen.
Om till gången i stället innehöll Sök vägarna `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` eller `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , kommer konverterings tjänsten att kunna hitta filerna A och C om de finns (förskjutande C över a). Men E kan inte hittas på det här sättet och filen måste flyttas.
Detta kan åtgärdas genom att flytta mappen texturer bredvid till gången.

> [!Note]
> Om texturer inte hittas, är det möjligt att se till att till gången är på samma nivå som under träd som innehåller texturer.

## <a name="next-steps"></a>Nästa steg

- [Modell konvertering](model-conversion.md)
