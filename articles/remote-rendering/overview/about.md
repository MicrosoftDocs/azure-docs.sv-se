---
title: Om
description: Introduktion till Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 6001fff54b86b7c18684092aad6c7fdf64f81990
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729954"
---
# <a name="about-azure-remote-rendering"></a>Om Azure Remote Rendering

*Azure Remote rendering* (arr) är en tjänst som gör att du kan återge högkvalitativt, interaktivt 3D-innehåll i molnet och strömma det i real tid till enheter, till exempel HoloLens 2.

![Diagram som visar ett exempel på en interaktiv 3D Automobile-motor med hög kvalitet.](../media/arr-engine.png)

Enheter som inte är insatta har begränsad beräknings kraft för rendering av komplexa modeller. För många program kan det vara oacceptabelt, men för att minska den visuella åter givningen på något sätt. I följande skärm bild jämförs modellen med fullständig information med en modell som har decimated med ett gemensamt verktyg för innehålls skapande:

![Exempelmodell](./media/engine-model-decimated.png)

Den reducerade modellen består av cirka 200 000 trianglar (inklusive de detaljerade inre delarna), jämfört med fler än 18 000 000 trianglar i den ursprungliga modellen.

*Fjärrrendering* löser det här problemet genom att flytta åter givnings arbets belastningen till avancerade GPU: er i molnet. En molnbaserad grafik motor i molnet återger avbildningen, kodar den som en video ström och strömmar till mål enheten.

## <a name="hybrid-rendering"></a>Hybrid åter givning

I de flesta program räcker det inte att bara återge en komplex modell. Du behöver också anpassade användar gränssnitt för att tillhandahålla funktioner för användaren. Med Azure Remote rendering tvingas du inte att använda ett dedikerat GRÄNSSNITTs ramverk, i stället stöder *hybrid åter givning*. Det innebär att du kan återge element på enheten med din önskade metod, till exempel [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

I slutet av en ram kombinerar Azure Remote rendering automatiskt det lokalt återgivna innehållet med fjärravbildningen. Det kan även göra detta med rätt ocklusion.

## <a name="multi-gpu-rendering"></a>Rendering av flera GPU

Vissa modeller är för komplexa för att kunna återges med interaktiva bild hastigheter, även för en avancerad GPU. Särskilt i industriell visualisering är detta ett vanligt problem. För att push-överföra gränserna kan Azure-fjärråter givning distribuera arbets belastningen till flera GPU: er. Resultaten sammanfogas till en enda bild, vilket gör processen helt transparent för användaren.

## <a name="high-level-architecture"></a>Övergripande arkitektur

Det här diagrammet illustrerar arkitekturen för fjärråter givning:

![Arkitektur](./media/arr-high-level-architecture.png)

En fullständig cykel för avbildnings generering omfattar följande steg:

1. På klient sidan: ram installation
    1. Din kod: användarindata bearbetas. scen diagrammet uppdateras
    1. ARR-kod: scen diagram uppdateringar och förväntad Head-attityd skickas till servern
1. Server sidan: fjärrrendering
    1. Åter givnings motor distribuerar åter givning över tillgängliga GPU: er
    1. Utdata från flera GPU: er består av en enda bild
    1. Avbildningen kodas som video ström och skickas tillbaka till klienten
1. På klient sidan: slutförd
    1. Din kod: valfritt lokalt innehåll (UI, markörer,...) återges
    1. ARR-kod: on ' finns ', lokalt renderat innehåll sammanfogas automatiskt med video strömmen

Nätverks fördröjning är det huvudsakliga problemet. Tiden för att skicka en begäran och få resultatet är vanligt vis för lång för interaktiva bild Rute hastigheter. Därför kan mer än en ram finnas i flygning när som helst.

## <a name="next-steps"></a>Nästa steg

* [Systemkrav](system-requirements.md)
* [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
