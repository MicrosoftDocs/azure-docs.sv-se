---
title: Azure Kinect Viewer
description: Förstå hur du kan visualisera alla enhets data strömmar med hjälp av Azure Kinect Viewer.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, sensor, visnings program, visualisering, djup, RGB, färg, IMU, ljud, mikrofon, punkt moln
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92166113"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect Viewer

Azure Kinect Viewer, som finns under katalogen installerade verktyg som `k4aviewer.exe` (till exempel `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` , där `X.Y.Z` är den installerade versionen av SDK), kan användas för att visualisera alla enhets data strömmar för att:

* Kontrol lera att sensorer fungerar korrekt.
* Hjälp att placera enheten.
* Experimentera med kamera inställningar.
* Läsa enhets konfigurationen.
* Uppspelnings inspelningar som gjorts med [Azure Kinect Recorder](azure-kinect-recorder.md).

Om du vill ha mer information om Azure Kinect Viewer kan [du se hur du använder Azure Kinect video](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

Azure Kinect Viewer är [öppen källkod](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) och kan användas som exempel för hur du använder API: erna.

## <a name="use-viewer"></a>Använd visnings program

Visnings programmet kan köras i två lägen: med real tids data från sensorn eller från inspelade data ([Azure Kinect Recorder](azure-kinect-recorder.md)).

### <a name="start-application"></a>Starta program

Starta programmet genom att köra `k4aviewer.exe` .

![Skärm bild som visar det startade visnings programmet.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Använd visnings programmet med real tids data

1. I avsnittet **Öppna enhet** väljer du **serie numret** för enheten som ska öppnas. Välj sedan **Uppdatera**, om enheten saknas.
2. Välj knappen **Öppna enhet** .
3. Välj **Starta** för att börja strömma data med standardinställningarna.

### <a name="use-the-viewer-with-recorded-data"></a>Använd visnings programmet med inspelade data

I avsnittet **Öppna inspelning** navigerar du till den inspelade filen och markerar den.

## <a name="check-device-firmware-version"></a>Kontrol lera den inbyggda enhetens version

Öppna den inbyggda enhetens version i fönstret konfiguration, som du ser i följande bild.

![Kontrollerar den inbyggda enhetens version med visnings programmet](./media/how-to-guides/check-firmware-update.png)

I det här fallet kör till exempel den djupbaserade kamera leverantören VB-1.5.63.

## <a name="depth-camera"></a>Djupkamera

Djup kamera visaren visar två fönster:

* Den ena kallas *aktiv ljus styrka* som visar en grå Skale bild som visar IR-ljus styrka.
* Den andra kallas *djup*, som har en färgad representation av djup data.

Hovra markören, i bild punkter i djup fönstret, för att se värdet för djup sensorn, som visas nedan.

![Djupgående vy](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB-kamera

Bilden nedan visar vyn färg kamera.

![RGB-vy](./media/how-to-guides/viewer-rgb-camera.png)

Du kan styra inställningar för RGB-kamera från konfigurations fönstret under strömningen.

![Kontroller för RGB-kamera](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Enhet för inert mått (IMU)

IMU-fönstret har två komponenter, en accelerometer och en Gyroscope.

Den övre halvan är accelerometer och visar linjär acceleration i meter per sekund<sup>2</sup>.  Den innehåller acceleration från gravitation, så om den är yttre i en tabell kommer Z-axeln troligen att visa cirka 9,8 m/s<sup>2</sup>.

Den nedre halvan är den Gyroscope delen och visar rotations rörelsen i radianer/sekund

![Vy för rörelse sensor](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Mikrofon ingång

I Microphone-vyn visas en representation av ljudet som hörs på varje mikrofon. Om det inte finns något ljud visas diagrammet som tomt, annars visas en mörk blå vågform med en ljusblå vågform som finns ovanpå den.

Den mörka vågen representerar de lägsta och högsta värdena som observeras av mikrofonen över den tids sektorn. Den ljusa vågen representerar rot medelvärdet för de värden som observeras av mikrofonen under den tids sektorn.

![Indatakälla för mikrofon](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Visualisering av punkt moln

Djup visualisering i 3D gör att du kan flytta i bilden med hjälp av beskrivna nycklar.

![Djup punkt moln](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Synkronisering av kontroll

Du kan använda visnings programmet för att konfigurera enheten som fristående (standard), huvud-eller underordnat läge när du konfigurerar synkronisering av flera enheter.
När du ändrar konfigurationen eller infogar/tar bort synkkabeln väljer du **Uppdatera** för att uppdatera.

![Extern synkronisering](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Installations guide för extern synkronisering](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
