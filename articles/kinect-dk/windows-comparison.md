---
title: Windows-jämförelse för Azure Kinect DK
description: Maskin-och program varu skillnader mellan Azure Kinect DK och Kinect för Windows v2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, jämförelse, SDK, skillnader, maskin vara, program vara
ms.openlocfilehash: 1791767e842043bbb0eda058cc3ea92be90a00fb
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078306"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Jämförelse av Azure Kinect och Kinect i Windows v2

Azure Kinect DK-maskin-och program utvecklings paketen har skillnader jämfört med Kinect för Windows v2. Alla befintliga Kinect för Windows v2-program kommer inte att fungera direkt med Azure Kinect DK och kräver port överföring till den nya SDK: n.  

## <a name="hardware"></a>Maskinvara

Hög nivå skillnader mellan Azure Kinect Development Kit och Kinect för Windows v2 visas i följande tabell.

| Funktion | Typ | Azure Kinect DK | Kinect för Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **Ljud** | Information  | 7-MIC cirkulär matris | 4-MIC linjärt uppdelad matris |
| **Rörelse sensor** | Information | 3-axel accelerometer 3-axel gyro | accelerometer för 3 axlar |
| **RGB-kamera**    | Information | 3840 x 2160 BPT @30 fps | 1920 x 1080 BPT @30 fps |
| **Djup kamera**  | Metod   | Tid för flygning | Tid för flygning |
|                   | Lösning | 640 x 576 BPT @30 fps | 512 x 424 BPT @ 30 fps |
|                   |            | 512 x 512 BPT @30 fps |                       |
|                   |            | 1024x1024 BPT- @15 fps |                       |
| **Anslutningsmöjligheter** | Data | USB 3.1 gen 1 med typ USB-C  | USB 3,1 gen 1|
|  | Ström | Extern PSU eller USB-C | Extern PSU |
|  | Synkronisering | RGB & djup internt, extern enhet-till-enhet| RGB & djup endast internt |
| **Urbenat** | Dimensioner | 103 × 39 × 126 mm | 249 x 66 x 67 mm |
|  | Viktprocent | 440 g | 970 g |
| | Ansluter | En 1/4-20-UNC. Fyra interna skruv punkter | En 1/4-20-UNC |

Mer information finns i [maskin varu dokumentet för Azure Kinect DK](hardware-specification.md) .

## <a name="sensor-access"></a>Sensor åtkomst

I följande tabell ges jämförelse av enhets sensor för låg nivå åtkomst.

| **Funktioner**| **Azure Kinect** | **Kinect för Windows** | **Kommentarer** |
|---------|---------|------------|---------|
| **Djuplodande** | ✔️ | ✔️ |    | 
| **IR** | ✔️ | ✔️ |  |
| **Färg** | ✔️ | ✔️ | Färg formatet stöder skillnader, Azure Kinect DK stöder dessa kamera kontroller: exponering, vit balans, ljus styrka, kontrast, mättnad, skärpa och öknings kontroll |
| **Ljud** | ✔️ | ✔️ | Azure Kinect DK-MICS nås via tal-SDK eller inbyggt API för Windows |
| **IMU** | ✔️ |  | Azure Kinect DK har en fullständig 6-axel IMU och Kinect för Windows har bara 1 axel |
| **Kalibrerings data** | ✔️ | ✔️ | Kalibrering av OpenCV-kompatibel kamera modell |
| **Djup – RGB intern synkronisering** | ✔️ | ✔️ |  |
| **Extern synkronisering**| ✔️|  | Azure Kinect DK tillåter programmerbar fördröjning för extern synkronisering |
| **Dela åtkomst med flera klienter** | | ✔️ | Azure Kinect sensor SDK är beroende av WinUSB/libUSB för att komma åt enheten och har ingen tjänst implementerad för att möjliggöra åtkomst till delnings enheter med flera processer |
| **Stream Record/uppspelnings verktyg** | ✔️ | ✔️ | Azure Kinect DK använder en container-baserad implementering med öppen källkod med Matroska |

## <a name="features"></a>Funktioner

Azure Kinect SDK funktions uppsättningen skiljer sig från Kinect för Windows v2, enligt beskrivningen nedan:

| **Kinect v2-funktion** | **Data typen Kinect v2** | **Azure Kinect SDK/service** |
|--------|--------|------|
| Åtkomst till sensor data |DepthFrame| [Sensor-SDK – hämta bilder](retrieve-images.md) 
| |InfraredFrame | [Sensor-SDK – hämta bilder](retrieve-images.md) 
| | ColorFrame | [Sensor-SDK – hämta bilder](retrieve-images.md) | 
| | AudioBeamFrame |Stöds för närvarande inte 
| Innehålls spårning | BodyFrame | Body tracking SDK |
| | BodyIndexFrame | Body tracking SDK  |
| Koordinera mappning|CoordinateMapper| [Sensor-SDK – bild omvandlingar](use-image-transformation.md) |
|Ansikts spårning | FaceFrame | [Cognitive Services: ansikte](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Tal igenkänning    |    Ej tillämpligt                      |    [Cognitive Services: tal](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Nästa steg

[Kinect för Windows Developer-sidor](https://developer.microsoft.com/windows/kinect)
