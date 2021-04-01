---
title: Tal enheter SDK Roobo Smart Audio dev kit v1-tal service
titleSuffix: Azure Cognitive Services
description: Krav och anvisningar för att komma igång med tal enheter SDK, Roobo Smart Audio dev kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015298"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Enhet: Roobo Smart Audio dev kit

Den här artikeln innehåller enhets information för Roobo Smart Audio dev kit.

## <a name="set-up-the-development-kit"></a>Konfigurera utvecklings paketet

1. Utvecklings paketet har två mikrousb-anslutningar. Den vänstra kopplingen är att sätta utvecklings paketet och är markerat som exponent i bilden nedan. Det högra är att kontrol lera den och har marker ATS som en fel sökning i avbildningen.

    ![Ansluta dev-paketet](media/speech-devices-sdk/qsg-1.png)

1. Utveckla utvecklings paketet genom att använda en mikrousb-kabel för att ansluta ström porten till en dator eller ett nätadapter. En grön energi indikator kommer att tändas under det översta brädet.

1. Om du vill styra utvecklings paketet ansluter du fel söknings porten till en dator genom att använda en andra Micro USB-kabel. Det är viktigt att använda en hög kvalitets kabel för att säkerställa tillförlitlig kommunikation.

1. Orientera ditt utvecklings paket för antingen den cirkulära eller linjära konfigurationen.

    |Konfiguration av utvecklings paket|Orientering|
    |-----------------------------|------------|
    |Cirkulär|Upprättat, med mikrofoner riktade mot taket|
    |Linjär|På sin sida, med mikrofoner som är riktade mot dig (visas i följande bild)|

    ![Orientering för linjär dev-sats](media/speech-devices-sdk/qsg-2.png)

1. Installera certifikaten och ange behörigheterna för ljud enheten. Skriv följande kommandon i ett kommando tolks fönster:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Dessa kommandon använder Android fel söknings bryggan, `adb.exe` som är en del av den Android Studio installationen. Det här verktyget finns i C:\Users \[ användar namn] \AppData\Local\Android\Sdk\platform-tools. Du kan lägga till den här katalogen i sökvägen så att det blir enklare att anropa `adb` . Annars måste du ange den fullständiga sökvägen till din installation av adb.exe i varje kommando som anropar `adb` .
    >
    > Om du ser ett fel `no devices/emulators found` kan du kontrol lera att USB-kabeln är ansluten och är en högkvalitativ kabel. Du kan använda `adb devices` för att kontrol lera att datorn kan kommunicera med utvecklings paketet eftersom den kommer att returnera en lista över enheter.
    >
    > [!TIP]
    > Stäng av DATORns mikrofon och högtalare för att vara säker på att du arbetar med utvecklings paketets mikrotelefoner. På så sätt kan du inte av misstag utlösa enheten med ljud från datorn.

1. Om du vill koppla en talare till dev-paketet kan du ansluta den till ljud linjen. Du bör välja en bra kvalitets högtalare med en analog kontakt med 3,5 mm.

    ![Vysor-ljud](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Utvecklings information

Mer utvecklings information finns i [utvecklings guiden för Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Ljud

Roobo tillhandahåller ett verktyg som fångar upp allt ljud till Flash-minnet. Det kan hjälpa dig att felsöka ljud problem. En version av verktyget tillhandahålls för varje utvecklings pakets konfiguration. Välj din enhet på  [Roobo-webbplatsen](http://ddk.roobo.com/)och välj sedan länken **Roobo tools** längst ned på sidan.

## <a name="next-steps"></a>Nästa steg

* [Kör appen Android-exempel](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)