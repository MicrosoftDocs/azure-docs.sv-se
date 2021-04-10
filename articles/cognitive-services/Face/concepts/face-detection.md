---
title: Koncept för ansikts igenkänning och attribut
titleSuffix: Azure Cognitive Services
description: Ansikts igenkänning är en åtgärd för att hitta människo ansikten i en bild och eventuellt returnera olika typer av ansikts data.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 68d1e9744d937cf80327c3f41cc69f4af97d3400
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98600182"
---
# <a name="face-detection-and-attributes"></a>Ansikts igenkänning och attribut

I den här artikeln förklaras begreppen ansikts igenkänning och attribut för ansikts data. Ansikts igenkänning är en åtgärd för att hitta människo ansikten i en bild och eventuellt returnera olika typer av ansikts data.

Du kan använda åtgärden för att [identifiera](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) ansikten i en bild. Varje identifierad ansikte motsvarar minst ett faceRectangle-fält i svaret. Den här uppsättningen med pixel koordinater för vänster, topp, bredd och höjd markerar den placerade ytan. Med hjälp av dessa koordinater kan du få fram platsen och dess storlek. I API-svaret visas ansikten i storleks ordningen från störst till minsta.

## <a name="face-id"></a>Ansikts-ID

Ansikts-ID: t är en unik identifierare-sträng för varje identifierad ansikte i en bild. Du kan begära ett ansikts-ID i ditt [ansikts-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API-anrop.

## <a name="face-landmarks"></a>Ansiktslandmärken

Ansikts landmärken är en uppsättning enkla punkter på ett ansikte, till exempel elever eller spets. Som standard finns 27 fördefinierade landmärkespunkter. Följande bild visar alla 27 punkter:

![Ett ansikts diagram med alla 27 landmärken märkta](../Images/landmarks.1.jpg)

Koordinaterna för punkterna returneras i antal bild punkter.

## <a name="attributes"></a>Attribut

Attribut är en uppsättning funktioner som du kan välja att identifiera i API: t för [ansikts igenkänning](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . Följande attribut kan identifieras:

* **Ålder**. Den uppskattade åldern i år av ett visst ansikte.
* **Oskärpa**. Blurriness för ytan i bilden. Det här attributet returnerar ett värde mellan noll och en och en informell klassificering av låg, medel eller hög.
* **Känslo**. En lista över känslor med identifierings förtroende för den aktuella ytan. Förtroende poängen normaliseras och poängen i alla känslor-enheter kan läggas upp till ett. Känslor som returneras är lycka, ledsenhet, neutral, ilska, prefresta, avsky, överraskning och frukt.
* **Exponering**. Exponeringen av ansikte i bilden. Det här attributet returnerar ett värde mellan noll och ett informellt omdöme för underexponering, goodExposure eller överexponering.
* **Ansikts hår**. Den uppskattade förekomsten av ansikts hår och längden för den aktuella ytan.
* **Kön**. Den uppskattade kön för den aktuella ytan. Möjliga värden är hane, hona och jämställdhets.
* **Glasögon**. Om den aktuella ytan har glasögon. Möjliga värden är noglas, ReadingGlasses, sol glasögon och SIME Goggles.
* **Hår**. Hår texten. Det här attributet visar om hår är synligt, om baldness identifieras och vilka hår färger som identifieras.
* **Head-attityd**. Facets orientering i 3D-rymden. Det här attributet beskrivs i vinklar, rullning och Yaw i grader. Värde intervallen är-90 grader till 90 grader,-90-grader till 90 grader och-90 grader till 90 grader. Se följande diagram för vinkel mappningar:

    ![Ett huvud med axlarna för bredd, rulle och Yaw som är märkta](../Images/headpose.1.jpg)
* **Makeup**. Om FACET har makeup. Det här attributet returnerar ett booleskt värde för eyeMakeup och lipMakeup.
* **Brus**. Det visuella bruset som har identifierats i ansikts bilden. Det här attributet returnerar ett värde mellan noll och en och en informell klassificering av låg, medel eller hög.
* **Ocklusion**. Om det finns objekt som blockerar delar av FACET. Det här attributet returnerar ett booleskt värde för eyeOccluded, foreheadOccluded och mouthOccluded.
* **Leende**. Leende-uttrycket för den aktuella ytan. Värdet är mellan noll och ett för ett tydligt leende.

> [!IMPORTANT]
> Ansikts attribut förväntas genom användning av statistiska algoritmer. De kanske inte alltid är korrekt. Var försiktig när du fattar beslut baserat på attribut data.

## <a name="input-data"></a>Indata

Använd följande tips för att se till att dina inmatade bilder ger de mest exakta identifierings resultaten:

* De inspelnings bild format som stöds är JPEG, PNG, GIF för den första ramen och BMP.
* Bild filens storlek får inte vara större än 6 MB.
* Den minsta identifierbara storleken är 36 x 36 pixlar i en bild som inte är större än 1920 x 1080 bild punkter. Bilder som är större än 1920 x 1080 bild punkter har en proportionellt större storlek. Att minska ansikts storleken kan leda till att vissa ytor inte identifieras, även om de är större än den minsta identifierade storleken.
* Maximal ansikts storlek är 4096 x 4096 bild punkter.
* Ansikten utanför storleks intervallet på 36 x 36 till 4096 x 4096 pixlar kommer inte att identifieras.
* Vissa ansikten kanske inte kan identifieras på grund av tekniska utmaningar. Extrema ansikts vinklar (Head attityd) eller ansikts ocklusion (objekt som sol glasögon eller händerna som blockerar en del av ansiktet) kan påverka identifieringen. Front-och närbelägna ansikten ger bäst resultat.

Om du identifierar ansikten från en video-feed kan du förbättra prestanda genom att justera vissa inställningar på video kameran:

* **Utjämning**: många video kameror använder en jämnare påverkan. Du bör inaktivera detta om du kan skapa en oskärpa mellan ramar och minska skärpan.
* **Slutarhastighet**: en snabbare slutarhastighet minskar mängden rörelse mellan ramar och gör varje ram tydligare. Vi rekommenderar en slutarhastighet på 1/60 sekund eller snabbare.
* **Vinklad vinkel**: vissa kameror anger slutarhastighets vinkel i stället för slutarhastighet. Om möjligt bör du använda en lägre slutarhastighets vinkel. Detta leder till tydligare video bild rutor.

    >[!NOTE]
    > En kamera med en nedre vinklad vinkel får mindre ljus i varje ram, så bilden blir mörkare. Du måste bestämma vilken rätt nivå som ska användas.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med ansikts igenkännings koncept kan du läsa mer om hur du skriver ett skript som identifierar ansikten i en specifik bild.

* [Identifiera ansikten i en bild](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
