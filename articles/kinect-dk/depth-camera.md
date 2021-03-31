---
title: Azure Kinect DK djup kamera
description: Förstå drift principerna och viktiga funktioner i djup kameran i din Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, djup kamera, TOF, principer, prestanda, ogiltighet
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277509"
---
# <a name="azure-kinect-dk-depth-camera"></a>Azure Kinect DK djup kamera

Den här sidan beskriver hur du använder djup kameran i din Azure Kinect DK. Djup kameran är den andra av de två kamerorna. Som det beskrivs i föregående avsnitt är den andra kameran RGB-kameran.  

## <a name="operating-principles"></a>Drift principer

Azure Kinect DK djup-kameran implementerar ToF-principen för amplitud-modulerad konstant våg (AMCW). Kameran kastar modulerad belysning i närheten av IR (NIR)-spektrumet till scenen. Den registrerar sedan en indirekt mätning av den tid det tar för ljuset att resa från kameran till scenen och tillbaka.

Dessa mått bearbetas för att generera en djup karta. En djup karta är en uppsättning Z-koordinatens värden för varje bild punkt i bilden, mätt i millimeter.

Tillsammans med en djup karta får vi också en så kallad ren IR-läsning. Värdet för pixlar i den rena IR-inläsningen är proportionellt mot mängden ljus som returneras från scenen. Bilden ser ut ungefär som en vanlig IR-avbildning. Figuren nedan visar ett exempel på en djup karta (vänster) och en motsvarande ren IR-bild (höger).

![Djup och IR sida vid sida](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Huvudfunktioner

De tekniska egenskaperna för djup kameran är:

- 1 – megapixlar ToF avbildnings krets med avancerad pixel teknik som möjliggör högre modulerings frekvenser och djup precision.
- Två NIR laser-dioder som aktiverar nära och breda FoV (Field-of-view) djup läge.
- Världens minsta ToF-pixel, vid 3,5 μm per 3,5 μm.
- Automatisk justering per bild punkt ger ett stort dynamiskt intervall som tillåter nära och långt objekt att samlas in på ett säkert sätt.
- Global slutarhastighet som ger bättre prestanda i solljus.
- Beräknings metod för djup i flera faser som möjliggör robust noggrannhet även om det finns krets-, laser-och kraft försörjnings variation.
- Låga systematiska och slumpmässiga fel.

![Djup modul](./media/concepts/depth-camera-depth-module.jpg)

Djup kameran överför RAW-modulerade IR-avbildningar till värd datorn. På datorn konverterar GPU-accelererade djups motorn den råa signalen till djup Maps.Djup kameran stöder flera lägen. Lägena **smal vy (FOV)** är idealiska för scener med mindre omfattning i X-och Y-dimensioner, men större utrymmen i Z-dimensionen. Om scenen har stora X-och Y-omfång, men mindre Z-intervall, är de **breda FOV-lägena** bättre lämpade.

Djup kameran stöder **2x2 diskretisering-lägen** för att utöka Z-intervallet jämfört med motsvarande unbinned- **lägen**. Diskretisering görs med kostnaden för att sänka bild upplösningen. Alla lägen kan köras med upp till 30 bild rutor per sekund (FPS) med undantag för läget 1 megapixlar (MP) som körs med en högsta bild frekvens på 15 fps. Djup kameran är också ett **passivt IR-läge**. I det här läget är tändarna i kameran inte aktiva och endast omgivande belysning observeras.

## <a name="camera-performance"></a>Kamera prestanda

Kamerans prestanda mäts som systematiska och slumpmässiga fel.

### <a name="systematic-error"></a>Systematiskt fel

Systematiskt fel definieras som skillnaden mellan det uppmätta djupet efter borttagning av brus och korrekt (mark sanningen) djup. Vi beräknar det temporala genomsnittet över många ramar i en statisk scen för att eliminera djupet brus så mycket som möjligt. Mer exakt, det systematiska felet definieras som:

![Systematiskt djup fel](./media/concepts/depth-camera-systematic-error.png)

Där *d <sub>t</sub>* anger mått djupet vid tid *t*, *N* , är antalet bild rutor som används i genomsnitts proceduren och *d <sub>gt</sub>* är sanningen djup.

Den systematiska fel specifikationen för djup kameran är exklusive MPI (Multiple Path interferens). MPI är när en sensor pixel integrerar ljus som reflekteras av mer än ett objekt. MPI begränsas delvis i vår djup kamera med högre modulerings frekvens, tillsammans med djupet djupet, som vi kommer att introducera senare.

### <a name="random-error"></a>Slumpmässigt fel

Vi antar att vi tar 100 bilder av samma objekt utan att flytta kameran. Objektets djup är något annorlunda i var och en av 100-bilderna. Denna skillnad orsakas av tagnings brus. Avbilds brus är antalet photons som når sensorn varierar med en slumpmässig faktor över tid. Vi definierar detta slumpmässiga fel på en statisk scen som standard avvikelsen för djupet som beräknas som:

![Slumpmässigt djup fel](./media/concepts/depth-camera-random-error.png)

Om *N* anger antalet djup mätningar, visar *d <sub>t</sub>* att djup måttet vid tid *t* och *d* anger att medelvärdet beräknas för alla djup mätningar *d <sub>t</sub>*.

## <a name="invalidation"></a>Ogiltig förklarade

I vissa situationer kan djup kameran inte ange rätt värden för vissa bild punkter. I dessa fall blir djupet pixlar ogiltiga. Ogiltiga pixlar anges av djup svärdet lika med 0. Orsaker till att djup motorn inte kan skapa korrekta värden är:

- Utanför Aktiv IR-lysande mask
- Mättad IR-signal
- Låg IR-signal
- Filtrera avvikare
- Störningar i flera sökvägar

### <a name="illumination-mask"></a>Belysnings mask

Pixlarna blir ogiltiga när de ligger utanför den aktiva IR-Undermasken. Vi rekommenderar inte att du använder signalen av sådana pixlar för att beräkna djupet. I bilden nedan visas ett exempel på ogiltighet av belysnings masken. De inverifierade pixlarna är svarta färg pixlarna utanför cirkeln i de breda FoV lägena (vänster) och sexhörning i de smala FoV lägena (höger).

![Invaliditet utanför belysnings masken](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Signal styrka

Pixlarna blir ogiltiga när de innehåller en mättad IR-signal. När pixlarna är mättade går fas informationen förlorad. I bilden nedan visas ett exempel på ogiltighet av en mättad IR-signal. Se pilar som pekas på bild punkter i både djup-och IR-bilder.

![Invaliditets-mättnad](./media/concepts/depth-camera-invalidation-saturation.png)

Invaliditet kan också inträffa när IR-signalen inte är tillräckligt stark för att generera djup. I bilden nedan visas ett exempel på ogiltighet av en låg IR-signal. Se pilarna som pekas till exempel pixlar i både djup-och IR-bilder.

![Låg verifierings låg signal](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Tvetydigt djup

Pixlar kan också vara ogiltiga om de tar emot signaler från fler än ett objekt i scenen. Ett vanligt fall där den här sortens ogiltighet kan ses finns i hörn.  På grund av scen geometrin reflekterar IR-ljuset från kameran från en vägg och till en annan. Detta reflekterande ljus orsakar tvetydighet i pixelns uppmätta djup. Filter i djup algoritmen identifierar dessa tvetydiga signaler och invaliderar pixlarna.

I figurerna nedan visas exempel på ogiltighet vid identifiering av flera sökvägar. Du kan också se hur samma yta som blivit ogiltig från en kameravy (översta raden) kan visas igen från en annan kameravy (nedre rad). Den här bilden visar att ytor som är invaliderade från ett perspektiv kan vara synliga från ett annat.

![Invaliditet för flera sökvägar – hörn](./media/concepts/depth-camera-invalidation-multipath.png)

Ett annat vanligt fall av flera sökvägar är pixlar som innehåller den blandade signalen från förgrunden och bakgrunden (till exempel runt objekt kanter). Under snabb rörelse kan du se fler invaliderade pixlar runt kanterna. De ytterligare inverifierade pixlarna beror på exponerings intervallet för den råa djup insamlingen.

![Invaliditet för flera sökvägar – kanter](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Nästa steg

[Koordinatsystem](coordinate-systems.md)
