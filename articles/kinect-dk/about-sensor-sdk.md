---
title: Om Azure Kinect sensor SDK
description: Översikt över Azure Kinect sensor Software Development Kit (SDK), dess funktioner och verktyg.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, Recording, sensor, SDK, åtkomst, djup, video, kamera, IMU, rörelse, sensor, ljud, mikrofon, Matroska, sensor-SDK, hämtning
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277686"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Om Azure Kinect sensor SDK

Den här artikeln innehåller en översikt över Azure Kinect sensor Software Development Kit (SDK), dess funktioner och verktyg.

## <a name="features"></a>Funktioner

Azure Kinect sensor SDK ger plattforms oberoende åtkomst på låg nivå för Azure Kinect-enhets konfiguration och maskin sensor strömmar, inklusive:

- Djup kamera åtkomst och läges kontroll (ett passivt IR-läge, plus breda och smala fält för att visa djup) 
- Åtkomst och kontroll över RGB-kamera (till exempel exponering och vit balans) 
- Rörelse sensor (Gyroscope och accelerometer)-åtkomst 
- Synkroniserat djup – RGB-kamera strömning med konfigurerbar fördröjning mellan kameror 
- Synkronisering av extern enhet med konfigurerbar fördröjnings förskjutning mellan enheter 
- Kamera ram meta-data åtkomst för bild upplösning, tidsstämpel osv. 
- Åtkomst till enhets kalibrerings data 

## <a name="tools"></a>Verktyg

- Ett [Azure Kinect Viewer](azure-kinect-viewer.md) för att övervaka enhets data strömmar och konfigurera olika lägen.
- Ett API för [Azure Kinect-inspelning](azure-kinect-recorder.md) och uppspelnings läsare som använder [container formatet Matroska](record-file-format.md).
- Ett [uppdaterings verktyg](azure-kinect-firmware-tool.md)för Azure Kinect DK-programvara.

## <a name="sensor-sdk"></a>Sensor-SDK

- [Hämta sensor-SDK](sensor-sdk-download.md).
- Sensor-SDK: n är tillgänglig i [öppen källkod på GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Mer information om användning finns i [dokumentationen för sensor SDK API](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Nästa steg

Nu kan du lära dig mer om Azure Kinect sensor SDK: n. du kan också:
>[!div class="nextstepaction"]
>[Hämta sensor SDK Code](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Hitta och öppna enhet](find-then-open-device.md)
