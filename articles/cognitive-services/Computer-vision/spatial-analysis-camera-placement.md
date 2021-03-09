---
title: Placering av rums analys kamera
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar en kamera för användning med Spatial analys
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 4c2d1cd1c73b377d85501fd31d0f5a1893df5183
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487431"
---
# <a name="camera-placement-guide"></a>Guide för kamera placering

Den här artikeln innehåller rekommendationer för kamera placering för rums analys (offentlig för hands version). Den innehåller allmänna rikt linjer och rekommendationer för höjd, vinkel och kamera-till-plats-avstånd för alla inkluderade åtgärder. 

> [!NOTE]
> Den här guiden är utformad för axel M3045-V-kameran. Den här kameran använder lösnings 1920x1080, 106 grad horisontellt i vyn, 59 gradens lodräta fält och en fast bredd på 2,8 mm. Principerna nedan gäller för alla kameror, men vissa rikt linjer kring kamera höjden och avståndet från kamera till fokus måste justeras för användning med andra kameror. 

## <a name="general-guidelines"></a>Allmänna riktlinjer

Tänk på följande allmänna rikt linjer när du placerar kameror för rums analys:

* **Belysnings höjd.** Placera kameror under belysnings Arma turer så att Arma turer inte blockerar kamerorna.
* **Hinder.** För att undvika att Visa kameravy, noterar du hinder, till exempel stavar, signering, hyllor, väggar och befintliga LP-kameror.
* **Miljö bakgrunds belysning.** Bakgrunds belysning påverkar kamerans bild kvalitet. Undvik svåra bakgrunds förhållanden genom att undvika att dirigera kameror till externa Windows-och glas dörrar.
* **Lokala sekretess regler och föreskrifter.** Lokala föreskrifter kan begränsa vilka kameror som kan avbilda. Se till att du förstår lokala regler och föreskrifter innan du monterar kameror.
* **Skapar struktur.** HVAC, sprinklers och befintliga kablar kan begränsa hård montering av kameror.
* **Kabel hantering.** Se till att du kan dirigera en Ethernet-kabel från planerade kamera monterings platser till PoE-växeln (Power-on-Internet).

## <a name="height-focal-point-distance-and-angle"></a>Höjd, brännvidd och vinkel

Du måste överväga tre saker när du bestämmer hur du ska installera en kamera för rums analys:
- Kamerans höjd
- Avstånd för kamera till brännvidd
- Kamerans vinkel i förhållande till golv planet

Det är också viktigt att veta i vilken riktning som majoriteten av folken ska gå (riktning) i förhållande till kamera fältet i vyn om möjligt. Den här riktningen är viktig för system prestanda.

![Bild av en person som går i riktning](./media/spatial-analysis/person-walking-direction.png)

I följande bild visas upphöjnings läget för personens riktning.

![Vy över höjning och plan](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Kamerans höjd

I allmänhet ska kameror monteras 12-14 fötter från marken. För identifiering av ansikts mask rekommenderar vi att kameror monteras 8-12 fötter från marken. När du planerar monteringen av kameran i det här intervallet bör du tänka på hinder (till exempel: hylla, hängande ljus, hängande signering och visning) som kan påverka kameravyn och sedan justera höjden efter behov.

## <a name="camera-to-focal-point-distance"></a>Avstånd för kamera till brännvidd

_Avståndet från kamera till brännvidd_ är det linjära avståndet från fokus punkten (eller mitten av kamera bilden) till kameran som mäts på marken.

![Kamera till fokus-punkt avstånd](./media/spatial-analysis/camera-focal-point.png)

Avståndet mäts på golv planet.

![Hur kamera-till-fokus-punkt avstånd mäts från golvet](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Från ovan ser det ut så här:

![Hur kamera-till-fokus-punkt avstånd mäts från över](./media/spatial-analysis/camera-focal-point-above.png)

Använd tabellen nedan för att fastställa kamerans avstånd från fokus punkten baserat på olika monterings höjd. De här avstånden är för optimal placering. Observera att tabellen ger vägledning under 12-14-rekommendationen eftersom vissa tak kan begränsa höjden. För identifiering av ansikts mask, rekommenderat värde för kamera till fokus-avstånd (min/max) är 4 "-10" för kamera höjden mellan 8 och 12.

| Kamerans höjd | Avstånd för kamera till fokus punkt (min/max) |  
| ------------- | ---------------------------------------- |  
| 7,8            | 4.6-8                                  |  
| 10           | 5,8-10                                 |  
| 12.5           | 7-12                                   |  
| längre           | 8 '-14 ' '                                  |  
| 16           | 9.2 – 16                                 |  
| tjugo           | 11,5 '-20 '                                |  

Följande bild visar kameravyer från de närmast och de närmast bästa och mest blickande punkterna.

| Är                                      | Längst                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Närmaste avstånd för kamera till fokus-punkt](./media/spatial-analysis/focal-point-closest.png) | ![Längst ned till höger](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Monterings intervall för kamera vinkel

I det här avsnittet beskrivs acceptabla monterings intervall för kamera vinkel. Dessa monterings intervall visar godtagbar räckvidd för optimal placering.

### <a name="line-configuration"></a>Linje konfiguration

För åtgärden **cognitiveservices. vision. spatialanalysis-personcrossingline** , +/-5 är den optimala kamera monterings vinkeln för att maximera noggrannheten.

För identifiering av ansikts mask är +/-30 grader den optimala kamerans monterings vinkel för kamera höjden mellan 8 och 12.

Följande bild visar kameravy med hjälp av rekommendationerna längst till vänster (-) och längst till höger (+) för att använda **cognitiveservices. vision. spatialanalysis-personcrossingline** för att göra ingångs beräkningen på en dörr väg.

| Vänster vy                | Vyn längst till höger                |  
| ---------------------------- | ----------------------------- |  
| ![Kamera vinkel till vänster](./media/spatial-analysis/camera-angle-left.png) | ![Kamera vinkel till höger](./media/spatial-analysis/camera-angle-right.png) |  

Följande bild visar kamera placering och monterings vinklar från en fågel ögon-vy.

![Fågelperspektiv](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Zon konfiguration

Vi rekommenderar att du placerar kameror med 10 fötter eller mer över marken för att garantera att det täckta arean är tillräckligt stort. 

När zonen är intill ett hinder som en vägg eller hylla, monterar du kameror på det angivna avståndet från målet inom det acceptabla intervallet i 120 grad enligt följande bild.

![Acceptabel kamera vinkel](./media/spatial-analysis/camera-angle-acceptable.png)

Följande bild visar simuleringar för vänster och höger kameravy i ett områden bredvid en hylla.

| Vänster vy        | Höger vy        |  
| ---------------- | ----------------- |  
| ![Vänster vy](./media/spatial-analysis/end-cap-left.png) | ![Höger vy](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Köer

**Cognitiveservices. vision. spatialanalysis-personcount**, **cognitiveservices. vision. spatialanalysis-persondistance** och **cognitiveservices. vision. spatialanalysis-personcrossingpolygon-** kunskaper kan användas för att övervaka köer. För optimala köade data kvalitet är det möjligt att dra tillbaka band hinder till att minimera ocklusion i kön och se till att platsen för köer är konsekvent över tid.

![Återkallning av band kön](./media/spatial-analysis/retractable-belt-queue.png)

Den här typen av barriär är prioriterad framför täckande barriärer för bildande av kön för att maximera precisionen hos insikterna i systemet.

Det finns två typer av köer: linjära och Sick--Sack.

Följande bild visar rekommendationer för linjära köer:

![Rekommendation för linjär kö](./media/spatial-analysis/camera-angle-linear-queue.png)

Följande bild innehåller simuleringar för vyerna till vänster och höger kamera för linjära köer. Observera att du kan montera kameran på motsatt sida av kön.

| Vänster vy                          | Höger vy                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Vänster vinkel för linjär kö](./media/spatial-analysis/camera-angle-linear-left.png) | ![Rät vinkel för linjär kö](./media/spatial-analysis/camera-angle-linear-right.png) |  

För Sick--Sack-köer är det bäst att undvika att placera kameran direkt mot köns riktnings riktning, som visas i följande bild. Observera att var och en av de fyra exempel kamera positionerna i bilden ger den perfekta vyn med en acceptabel avvikelse på +/-15 grader i varje riktning.

Följande illustrationer simulerar vyn från en kamera som placeras på de idealiska platserna för en Sick--Sack-kö.

| Visa 1        | Visa 2        |  
| ------------- | ------------- |  
| ![Visa 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Visa 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Visa 3 |  Visa 4 |  
| ---- | ----  |
| ![Visa 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Visa 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Organiska köer

Organiska Queue rader formas ekologiskt. Den här typen av kö är acceptabel om köer inte bildar fler än 2-3 personer och linje formulären i zon definitionen. Om köns längd vanligt vis är mer än 2-3 personer rekommenderar vi att du använder en lyftbar barriär för att hjälpa till att vägleda riktningen och se till att linje formulären i zon definitionen fungerar.

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webb program inventering](spatial-analysis-web-app.md)
* [Konfigurera åtgärder för rums analys](./spatial-analysis-operations.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för zon-och linje placering](spatial-analysis-zone-line-placement.md)
