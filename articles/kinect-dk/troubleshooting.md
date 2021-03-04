---
title: Kända problem och fel sökning i Azure Kinect
description: Lär dig mer om några kända problem och fel söknings tips när du använder sensor-SDK med Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: fel sökning, uppdatering, bugg, Kinect, feedback, återställning, loggning, tips
ms.openlocfilehash: 5f13815b8f8b26f6a08da28181a4a6164b7b89a3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038828"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Kända problem och fel sökning i Azure Kinect

Den här sidan innehåller kända problem och fel söknings tips när du använder sensor-SDK med Azure Kinect DK. Se även [produkt support sidor](./index.yml) för produkter som är specifika för maskin vara.

## <a name="known-issues"></a>Kända problem

- Kompatibilitetsproblem med USB-styrenheter (till exempel ASM1142-chipset)
  - Vissa fall där Microsoft USB-drivrutinen kan avblockera
  - Många datorer har även alternativa värd styrenheter och att ändra USB3-porten kan hjälpa dig

Mer information om sensor-SDK-relaterade problem finns i [GitHub-problem](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Samlar in loggar

Loggning för K4A.dll aktive ras via miljövariabler. Som standard skickas en loggning till stdout och endast fel och kritiska meddelanden genereras. De här inställningarna kan ändras så att loggningen går till en fil. Utförligheten kan också justeras efter behov. Nedan visas ett exempel för Windows för att aktivera loggning till en fil med namnet k4a. log och som kommer att fånga varnings meddelanden och meddelanden på högre nivå.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Kör scenario från kommando tolken (till exempel starta visnings program)
4. Navigera till k4a. log och dela filen.

Mer information finns under klipp från huvud fil:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

Loggningen av SDK-K4ABT.dll för innehålls spårning liknar varandra förutom att användare bör ändra en annan uppsättning miljö variabel namn:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Enheten räknas inte in i enhets hanteraren

- Kontrol lera status LAMPAn bakom enheten, om den blinkar gult kan du få problem med USB-anslutningen och det får inte tillräckligt med ström. Strömförsörjnings kabeln bör vara ansluten till det angivna strömadaptern. Även om ström kabeln har en USB-typ ansluten, kräver enheten mer kraft än en USB-port för datorer kan tillhandahålla. Anslut därför inte till en PC-port eller USB-hubb.
- Kontrol lera att du har ström kabeln ansluten och använder USB3-port för data.
- Försök att ändra USB3-port för data anslutningen (rekommendationen att använda USB-port nära moder kort, till exempel på datorns bak dator).
- Kontrol lera kabeln, skadade eller lägre kvalitets kablar kan orsaka otillförlitligt uppräkning (enheten håller "blinkar" i enhets hanteraren).
- Om du har anslutit till en bärbar dator och har en batteri drift kan den begränsas till porten.
- Starta om värd datorn.
- Om problemet kvarstår kan det finnas kompatibilitetsproblem.
- Om ett problem inträffar under uppdateringen av den inbyggda program varan och enheten inte har återställts av sig, utför [fabriks återställning](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Det går inte att öppna Azure Kinect Viewer

- Kontrol lera först att enheten räknas upp i Windows Enhetshanteraren.

    ![Azure Kinect-kameror i Windows enhets hanteraren](./media/resources/viewer-fails.png)

- Kontrol lera om du har något annat program som använder enheten (till exempel Windows kamera program). Endast ett program i taget kan komma åt enheten.
- Kontrol lera k4aviewer. err-loggen för fel meddelanden.
- Öppna Windows kamera program och kontrol lera att fungerar.
- Strömförsörjnings enhet, vänta på strömnings LAMPAn innan du använder enheten.
- Starta om värd datorn.
- Se till att du använder de senaste grafik driv rutinerna på din dator.
- Om du använder din egen version av SDK kan du prova att använda officiellt släppt version om det löser problemet.
- [Samla in loggar](troubleshooting.md#collecting-logs) och spara feedback om problemet kvarstår.

## <a name="cannot-find-microphone"></a>Det går inte att hitta mikrofon

- Kontrol lera först att Microphone-matrisen räknas upp i Enhetshanteraren.
- Om en enhet har räknats upp och fungerar på fel sätt i Windows, kan problemet bero på att Windows har tilldelat ett annat behållar-ID till en djup kamera när den har uppdaterats.
- Du kan försöka återställa den genom att gå till Enhetshanteraren, högerklicka på "Azure Kinect Microphone array" och välja "Avinstallera enhet". När du är klar kopplar du från och återansluter sensorn.

    ![Azure Kinect MIC-matris](./media/resources/mic-not-found.png)

- Starta om Azure Kinect Viewer och försök igen.

## <a name="device-firmware-update-issues"></a>Problem med uppdatering av inbyggd enhet

- Om rätt versions nummer inte rapporteras efter uppdateringen kan du behöva starta om enheten.
- Om uppdatering av den inbyggda program varan har avbrutits kan den komma in i dåligt tillstånd och Miss lyckas med att räkna upp. Ta bort och Återanslut enheten och vänta 60 sekunder för att se om den kan återställas.
Om du inte sedan utför en [fabriks återställning](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Bild kvalitets problem

- Starta [Azure Kinect Viewer](azure-kinect-viewer.md) och kontrol lera placeringen av enheten för att förhindra störningar, eller om sensorn är smutsig eller lins är smutsig.
- Prova olika operativ lägen för att begränsa om problemet inträffar i ett specifikt läge.
- För att dela bild kvalitets problem med teamet kan du:

1) Ta en paus i visningen av [Azure Kinect Viewer](azure-kinect-viewer.md) och ta en skärm bild eller
2) Ta en inspelning med [Azure Kinect Recorder](azure-kinect-recorder.md), till exempel `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Inkonsekventa eller oväntade tidsstämplar för enheter

Anrop ```k4a_device_set_color_control``` kan tillfälligt inducera ändringar av tid i enheten som kan ta några avbildningar att stabilisera. Undvik att anropa API: et i bild insamlings slingan för att undvika att återställa den interna tids beräkningen med varje ny avbildning. Anropa i stället API: n innan du startar kameran eller bara när du behöver ändra värdet i bild Tagningens slinga. Undvik särskilt anrop ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Kompatibilitet för USB3-värdstyrenhet

Om enheten inte räknas upp under enhets hanteraren kan det bero på att den är ansluten till en USB3-kontrollant som inte stöds. 

För Azure Kinect DK på **Windows, Intel**, **Texas Instruments (TI)** och **Renesas** är de *enda värd styrenheterna som stöds*. Azure Kinect SDK på Windows-plattformar är beroende av ett enhetligt container-ID och det måste omfatta USB 2,0-och 3,0-enheter så att SDK kan hitta de djup, färg och ljud enheter som fysiskt finns på samma enhet. På Linux kan fler värd styrenheter stödjas eftersom plattformen är mindre beroende av container-ID: t och mer på enhetens serie nummer. 

Avsnittet av USB-värdstyrenheter blir ännu mer komplicerat när en dator har fler än en värd styrenhet installerad. När värd styrenheterna är blandade kan en användare uppleva problem där vissa portar fungerar bra och andra inte fungerar alls. Beroende på hur portarna är tråd bundna till fallet kan du se alla front portar som har problem med Azure-Kinect

**Windows:** Ta reda på vilken värd styrenhet du har öppnat Enhetshanteraren

1. Visa > enheter efter typ 
2. Med Azure-Kinect anslutna väljer du kameror->Azure Kinect 4K-kamera
3. Visa > enheter efter anslutning

![Fel sökning av USB-port](./media/resources/usb-troubleshooting.png)

För att bättre förstå vilken USB-port som är ansluten på din dator, upprepa dessa steg för varje USB-port när du ansluter Azure Kinect DK till olika USB-portar på datorn.

## <a name="depth-camera-auto-powers-down"></a>Automatisk avstängning av kamerans djup

Den laser som används av djup kameran för att beräkna bild djups data, har en begränsad livs längd. För att maximera laserns livs längd kommer djup kameran att upptäcka när djupgående data inte förbrukas. Djupet på kameran när enheten strömmas i flera minuter, men värd datorn läser inte data. Det påverkar också synkronisering av flera enheter, där underordnade enheter startar i ett tillstånd där djup kameran är strömmande och djup ramar kan hjälpa till med att vänta på att huvud enheten börjar synkronisera avbildningar. För att undvika det här problemet i scenarier med flera enheter, se till att huvud enheten börjar inom en minut av den första underordnade som startas. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Använda SDK för innehålls spårning med Unreal

Om du vill använda Body tracking SDK med Unreal kontrollerar du att du har lagt `<SDK Installation Path>\tools` till i miljövariabeln `PATH` och kopierat `dnn_model_2_0.onnx` och `cudnn64_7.dll` till `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="using-azure-kinect-on-headless-linux-system"></a>Använda Azure-Kinect på ett konsol löst Linux-system

Azure Kinect djup Engine i Linux använder OpenGL. OpenGL kräver en fönster instans som kräver att en övervakare är ansluten till systemet. En lösning för det här problemet är:

1. Aktivera automatisk inloggning för det användar konto som du planerar att använda. I [den här](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/) artikeln hittar du instruktioner om hur du aktiverar automatisk inloggning.
2. Stäng av systemet, koppla från övervakaren och starta systemet. Med automatisk inloggning framtvingas skapandet av en x-Server-session.
2. Anslut via SSH och ange VISNINGS miljö variabel `export DISPLAY=:0`
3. Starta ditt Azure Kinect-program.

[Xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) -verktyget kan användas för att omedelbart låsa skärmen efter automatisk inloggning. Lägg till följande kommando i Start programmet eller system tjänsten:

`bash -c “xtrlock -b”` 

## <a name="missing-c-documentation"></a>C#-dokumentation som saknas

Sensor SDK C#-dokumentationen finns [här](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html).

Brödtext som spårar SDK C#-dokumentationen finns [här](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html).

## <a name="next-steps"></a>Nästa steg

[Mer supportinformation](support.md)