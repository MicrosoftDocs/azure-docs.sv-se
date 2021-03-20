---
title: Använd Azure Kinect kalibrerings funktioner
description: Lär dig hur du använder kalibrerings funktionerna för Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, koordinera system, kalibrering, funktioner, kamera, inre, extrinsic, projekt, projekt, omvandling, RGB-d, punkt moln
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277496"
---
# <a name="use-azure-kinect-calibration-functions"></a>Använd Azure Kinect kalibrerings funktioner

Kalibrerings funktionerna gör det möjligt att transformera punkter mellan koordinatsystemet för varje sensor på Azure Kinect-enheten. Program som kräver konvertering av hela avbildningar kan dra nytta av de accelererade åtgärder som finns tillgängliga i [Transformations funktioner](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Hämta kalibrerings data

Det är nödvändigt att hämta enhets kalibreringen för att utföra koordinera system transformationer. Kalibrerings data lagras i data typen [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) . Den hämtas från enheten via funktionen [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). Kalibrerings data är inte bara till för varje enhet, utan också till kamerans drift läge. Därför måste [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) kräva `depth_mode` `color_resolution` parametrarna och som inmatade.

### <a name="opencv-compatibility"></a>OpenCV-kompatibilitet

Kalibrerings parametrarna är kompatibla med [opencv](https://opencv.org/). Mer information om de enskilda kamera kalibrerings parametrarna finns även i [opencv-dokumentationen](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Se även [opencv Compatibility-exempel](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) för SDK som visar konvertering mellan [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) typ och motsvarande opencv-datastrukturer.

## <a name="coordinate-transformation-functions"></a>Koordinera omvandlings funktioner

Figuren nedan visar olika koordinatsystem i Azure-Kinect samt funktioner för att konvertera mellan dem. Vi utesluter 3D-koordinatsystemet i Gyroscope och accelerometer för att hålla bilden enkelt.

   ![Koordinera omvandling](./media/how-to-guides/coordinate-transformation.png)

Markering på lins förvrängning: 2D-koordinater refererar alltid till den förvrängda bilden i SDK: n. I det [avförvrängnings exempel](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) på SDK: t visas bild avförvrängning. I allmänhet påverkas inte 3D-punkter av lins förvrängning.

### <a name="convert-between-3d-coordinate-systems"></a>Konvertera mellan 3D-koordinatsystem

Funktionen [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) konverterar en 3D-punkt i käll koordinatsystemet till en 3D-punkt i mål koordinatsystemet med kamerans extrinsic-kalibrering. Källa och mål kan ställas in på någon av de fyra 3D-koordinaterna, det vill säga färg kamera, djup kamera, Gyroscope eller accelerometer. Om källa och mål är identiska returneras den oförändrade indata-3D-punkten som utdata.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Konvertera mellan 2D-och 3D-koordinatsystem

Funktionen [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) konverterar en 3D-punkt i käll koordinatsystemet till en 2D-koordinat för mål kameran. Den här funktionen kallas ofta för Project-funktion. Även om källan kan ställas in på något av de fyra 3D-koordinaterna måste målet vara djupet eller färg kameran. Om källa och mål är olika konverteras indatakällans 3D-punkt till 3D-koordinatsystemet för mål kameran med hjälp av [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). När 3D-punkten representeras i mål kamera koordinatsystemet, beräknas motsvarande 2D-pixel koordinater med mål kamerans inbyggda kalibrering. Om en 3D-punkt faller utanför det synliga avsnittet i mål kameran anges det giltiga värdet 0.

Funktionen [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) konverterar en 2D-pixel i käll kameran till en 3D-punkt i mål kamera koordinatsystemet. Källan måste vara en färg-eller djup kamera. Målet kan anges till alla fyra 3D-koordinatsystem. Förutom 2D-pixeln, krävs det att pixelns djup värde (i millimeter) i käll kamerans bild krävs som indata till funktionen, ett sätt att härleda djupet i färg kamerans geometri är att använda funktionen [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Funktionen beräknar 3D Ray-ledande från käll kamerans fokus punkt genom den angivna pixel koordinaten med käll kamerans inbyggda kalibrering. Djup svärdet används sedan för att hitta den exakta platsen för 3D-punkten på den här Ray-filen. Den här åtgärden kallas ofta projekt funktion. Om käll-och mål kameraerna är olika transformerar funktionen 3D-punkten till koordinatsystemet för målet via [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Om en 2D-punkts koordinat faller utanför det synliga avsnittet i käll kameran anges värdet 0 för det giltiga värdet.

### <a name="converting-between-2d-coordinate-systems"></a>Konvertera mellan 2D-koordinatsystem

Funktionen [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) konverterar en 2D-pixel koordinat för käll kameran till en 2D-koordinat för mål kameran. Källa och mål måste anges till färg eller djup kamera. Funktionen kräver att pixelns djup värde (i millimeter) i käll kamera bilden är indata, ett sätt att härleda djupet i färg kamerans geometri är att använda funktionen [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Den anropar [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) för att konvertera till en 3D-punkt i käll kamera systemet. Sedan anropas [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) för att konvertera till en 2D-koordinat för mål kamerans bild. Det giltiga värdet är inställt på 0, om [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) eller [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) returnerar ett ogiltigt resultat.

## <a name="related-samples"></a>Relaterade exempel

- [Exempel på OpenCV-kompatibilitet](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Exempel på förvrängning](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Exempel på moln med snabb punkt](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Nästa steg

Nu när du vet om kamera kalibreringar kan du också lära dig att
>[!div class="nextstepaction"]
>[Avbilda enhetssynkronisering](capture-device-synchronization.md)

Du kan också granska

[Koordinatsystem](coordinate-systems.md)
