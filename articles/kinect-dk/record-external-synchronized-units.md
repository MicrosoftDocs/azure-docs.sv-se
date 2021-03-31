---
title: Använd Azure Kinect Recorder med externa synkroniserade enheter
description: Lär dig hur du registrerar data från enheter som kon figurer ATS för extern synkronisering med hjälp av Azure Kinect-inspelaren.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensor, visare, extern synkronisering, fas fördröjning, djup, RGB, kamera, ljud kabel, inspelning
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277472"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Använd Azure Kinect Recorder med externa synkroniserade enheter

Den här artikeln innehåller information om hur [Azure Kinect-inspelaren](azure-kinect-recorder.md) kan registrera konfigurerade enheter för dataexternal-synkronisering.

## <a name="prerequisites"></a>Förutsättningar

- [Konfigurera flera Azure Kinect DK-enheter för extern synkronisering](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Begränsningar för extern synkronisering

- Huvud enheten kan inte ha synkronisering i kabeln är ansluten.
- Huvud enheten måste strömma RGB-kamera för att kunna aktivera synkronisering.
- Alla enheter måste använda samma kamera konfiguration (RAM hastighet och upplösning).
- Alla enheter måste köra samma inbyggda program vara för enheten (uppdatera instruktioner för[inbyggd program vara](update-device-firmware.md) ).
- Alla underordnade enheter måste startas före huvud enheten.
- Samma exponerings värde måste anges för alla enheter.
- Var och en *av* de underordnade original inställningarna är i förhållande till huvud enheten.

## <a name="record-when-each-unit-has-a-host-pc"></a>Spela in när varje enhet har en värddator dator

I exemplet nedan har varje enhet en egen dedikerad värddator dator.
Vi rekommenderar att du ansluter enheter till dedikerade datorer för att förhindra problem med USB-bandbredd och CPU/GPU-användning.

### <a name="subordinate-1"></a>Underordnade-1

1. Konfigurera registrering för den första enheten

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Enheten börjar vänta

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Underordnade-2

1. Konfigurera registrering för den andra enheten

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Enheten börjar vänta

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Huvudserver

1. Börja spela in på huvud datorn

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Vänta tills registreringen är färdig

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Spela in när flera enheter är anslutna till en dator med en enda värd

Du kan ha flera Azure Kinect-DKs anslutna till en dator med en enda värd. Men det kan vara mycket krävande för USB-bandbredd och värd beräkning. Så här minskar du efter frågan:

- Anslut varje enhet till en egen USB-värdstyrenhet.
- Ha en kraftfull GPU som kan hantera djup motor för varje enhet.
- Registrera bara sensorer som behövs och Använd en lägre bild hastighet.

Starta alltid underordnade enheter först och huvud gruppen sist.

## <a name="subordinate-1"></a>Underordnade-1

1. Starta inspelaren på underordnad

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. Enheten övergår i vänte läge

## <a name="master"></a>Huvudserver

1. Starta huvud enhet

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Vänta tills registreringen är klar

## <a name="playing-recording"></a>Spelar upp inspelning

Du kan använda [Azure Kinect Viewer](azure-kinect-viewer.md) för att spela upp inspelningen.



## <a name="tips"></a>Tips

- Använd manuell exponering för att spela in synkroniserade kameror. Automatisk exponering för RGB-kamera kan påverka tids synkronisering.
- Om du startar om den underordnade enheten går synkroniseringen förlorad.
- Vissa [kamera lägen](hardware-specification.md#depth-camera-supported-operating-modes) stöder högst 15 fps. Vi rekommenderar att du inte blandar lägen/bild hastigheter mellan enheterna
- Genom att ansluta flera enheter till en enda dator kan du enkelt fylla USB-bandbredd, överväga att använda separata värddator datorer per enhet. Var också uppmärksam på processor-och GPU-beräkning.
- Inaktivera mikrofonen och IMU om de inte behövs för att förbättra tillförlitligheten.

Information om eventuella problem finns i [fel sökning](troubleshooting.md)

## <a name="see-also"></a>Se även

- [Konfigurera extern synkronisering](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Azure Kinect Recorder](azure-kinect-recorder.md) för inspelade inställningar och ytterligare information.
- [Azure Kinect Viewer](azure-kinect-viewer.md) för att spela upp inspelningar eller ställa in RGB-kamera egenskaper som inte är tillgängliga via inspelning.
- [Azure Kinect firmware-verktyg](azure-kinect-firmware-tool.md) för uppdatering av enhetens inbyggda program vara.
