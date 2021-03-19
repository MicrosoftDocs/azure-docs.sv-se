---
title: Azure Kinect DK koordinera system
description: Beskrivning av Azure Kinect DK-koordinater som är associerade med Azures DK-sensorer
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, djup kamera, TOF, principer, prestanda, ogiltighet
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277448"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK koordinera system

I den här artikeln beskriver vi konventioner som används för 2D-och 3D-koordinatsystem.  Det finns separata koordinatsystem som är kopplade till varje sensors enhet och [kalibrerings funktionerna](use-calibration-functions.md) som tillåts för att omvandla punkter mellan dem. [Transformations funktionerna](use-image-transformation.md) transformerar hela bilder mellan koordinatsystem.  

## <a name="2d-coordinate-systems"></a>2D koordinera system

 Både djup-och färg kameror är associerade med ett oberoende 2D-koordinatsystem. En [x, y]-koordinat visas i antal pixlar där *x* sträcker sig från 0 till width-1 och *y* -intervall från 0 till height-1. Bredden och höjden beror på det valda läge där djup-och färg kameror körs. Pixel koordinaten `[0,0]` motsvarar bildens övre vänstra bild punkt. Pixel koordinater kan vara bråktal som representerar under pixel koordinater.

2D-koordinatsystemet är 0 – Centrerat, det vill säga att under pixel koordinaten `[0.0, 0.0]` representerar mitten och `[0.5,0.5]` det nedre högra hörnet av bild punkten, som visas nedan.

   ![2D koordinera system](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D-koordinatsystem

Varje kamera, accelerometer och Gyroscope är associerade med ett oberoende 3D-koordinatsystem.

Punkter i 3D-koordinaterna representeras som mått [X, Y, Z]-koordinaten tredubbla med enheter i millimeter.

### <a name="depth-and-color-camera"></a>Djup-och färg kamera

Ursprunget `[0,0,0]` finns på kamerans fokus punkt. Koordinatsystemet är riktat så att den positiva X-axeln pekar åt höger, den positiva Y-axeln pekar nedåt och de positiva Z-axlarna går framåt.

Djup kameran lutar 6 grader nedåt i färg kameran, som du ser nedan. 

Det finns två tändor som används av djup kameran. Den lysande som används i NFOV-lägen (smalt fält) justeras efter djupet kamera, så att den inte lutar. Den lysande (WFOV) lägen som används i breda läge () lutar ytterligare 1,3 grader nedåt i förhållande till djup kameran.

![3D-koordinater konventioner](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Gyroscope och accelerometer

Gyroscope-ursprunget `[0,0,0]` är identiskt med djup kamerans ursprung. Ursprunget för accelerometer sammanfaller med dess fysiska plats. Både accelerometer-och Gyroscope-koordinatsystemet är rätt tillgängliga. Koordinatsystemet positiva X-axel pekar bakåt, de positiva Y-axlarna till vänster och de positiva Z-axlarna pekar nedåt, som du ser nedan.

![IMU koordinera system](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om Azure Kinect sensor SDK](about-sensor-sdk.md)