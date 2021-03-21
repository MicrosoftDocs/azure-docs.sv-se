---
title: Synkronisera flera Azure Kinect DK-enheter
description: I den här artikeln lär du dig fördelarna med synkronisering av flera enheter samt hur du konfigurerar enheterna som ska synkroniseras.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, specifikationer, maskin vara, DK, funktioner, djup, färg, RGB, IMU, matris, djup, multi, synkronisering
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039962"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Synkronisera flera Azure Kinect DK-enheter

Varje Azure Kinect DK-enhet omfattar 3,5-mm-synkroniseringsproblem (**Synkronisera in** och **Synkronisera**) som du kan använda för att länka flera enheter tillsammans. När du har anslutit enheterna kan program varan koordinera utlösnings tiden mellan dem. 

Den här artikeln hur du ansluter och synkroniserar enheterna.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Fördelar med att använda flera Azure Kinect DK-enheter

Det finns många skäl att använda flera Azure Kinect DK-enheter, inklusive följande:

- Fyll i Occlusions. Även om Azure Kinect DK-dataomvandlingar skapar en enda bild, är de två kamerorna (djup och RGB) i själva verket ett litet avstånd från varandra. Förskjutningen gör Occlusions möjlig. En ocklusion inträffar när ett förgrunds objekt blockerar vyn av ett bakgrunds objekt för en av de två kamerorna på en enhet. I den resulterande färg bilden förefaller förgrundsobjektet att omvandla en skugga på bakgrunds objektet.  
   I följande diagram ser den vänstra kameran den grå pixeln "P2". Det vita förgrunds objektet blockerar dock den högra kamerans IR-balk. Den högra kameran har inga data för "P2".  
   ![Diagrammet visar två kameror riktade mot samma punkt med en av dem blockerad.](./media/occlusion.png)  
   Ytterligare synkroniserade enheter kan tillhandahålla Occluded-data.
- Skanna objekt i tre dimensioner.
- Öka den effektiva bild frekvensen till ett värde som är större än 30 bild rutor per sekund (FPS).
- Fånga flera 4K-färgers bilder av samma scen, alla justerade inom 100 mikrosekunder &mu; i mitten av exponeringen.
- Öka kamera täckningen inom utrymmet.

## <a name="plan-your-multi-device-configuration"></a>Planera konfigurationen för flera enheter

Innan du börjar ska du kontrol lera att du har granskat [maskin varu specifikationerna för Azure Kinect DK](hardware-specification.md) och [Azure Kinect DK djup](depth-camera.md).

> [!NOTE]  
> Ta bort det yttre plast locket för att exponera synkroniseringen i och synkronisera ut-uttag.

### <a name="select-a-device-configuration"></a>Välj en enhets konfiguration

Du kan använda någon av följande metoder för enhets konfigurationen:

- **Seriekopplade-kedje konfiguration**. Synkronisera en huvud enhet och upp till åtta underordnade enheter.  
   ![Diagram som visar hur du ansluter Azure Kinect DK-enheter i en seriekopplade-kedje konfiguration.](./media/multicam-sync-daisychain.png)
- **Stjärn konfiguration**. Synkronisera en huvud enhet och upp till två underordnade enheter.  
   ![Diagram som visar hur du konfigurerar flera Azure DK-enheter i en Star-konfiguration.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Använda en extern Sync-utlösare

I båda konfigurationerna tillhandahåller huvud enheten den Utlös ande signalen för de underordnade enheterna. Du kan dock använda en anpassad extern källa för utlösaren för synkronisering. Du kan till exempel använda det här alternativet för att synkronisera avbildnings dum par med annan utrustning. I antingen seriekopplade-eller Star-konfigurationen ansluter den externa utlösarens källa till huvud enheten.

Den externa utlösnings källan måste fungera på samma sätt som huvud enheten. Den måste leverera en sync-signal med följande egenskaper:

- Aktiv hög
- Puls bredd: större än 8 &mu; s
- 5V TTL/CMOS
- Högsta kapacitet för drivande kapacitet: minst 8 milliamps (mA)
- Frekvens stöd: exakt 30 FPS, 15 FPS och 5 FPS (frekvensen för färg kamerans huvud VSYNC-signal)

Utlösarens källa måste leverera signalen till den **synkroniserade** huvud enheten i porten med hjälp av en 3,5-mm-ljudkabel. Du kan använda en stereo-eller mono-kabel. Azure Kinect DK förkortar alla höljen och signalerna i ljud kabel kopplingen och motiverar dem. Som du ser i följande diagram tar enheten emot den synkroniserade signalen från anslutnings tipset.

![Kabelanslutna konfigurationer för en extern utlösnings signal](./media/resources/camera-trigger-signal.jpg)

Mer information om hur du arbetar med extern utrustning finns i [använda Azure Kinect Recorder med externa synkroniserade enheter](record-external-synchronized-units.md)

> [!NOTE]  
> Sync out är VSync för RGB-kameran. Tidsstämplar för alla enheter är inställda på noll och räknas upp. Microsoft har inte karakteriserat den minsta och största bredden på synkroniseringsåtgärden och rekommenderar att mimicking-pulsen genereras av en synkronisering från en Azure Kinect DK.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planera kamera inställningar och program varu konfiguration

Information om hur du konfigurerar din program vara för att kontrol lera kameror och använder bilddata finns i [Azure Kinect sensor SDK](about-sensor-sdk.md).

I det här avsnittet beskrivs flera faktorer som påverkar synkroniserade enheter (men inte enskilda enheter). Program varan bör tänka på dessa faktorer.

#### <a name="exposure-considerations"></a>Exponerings överväganden
Om du vill kontrol lera den exakta tids inställningen för varje enhet rekommenderar vi att du använder en manuell exponerings inställning. Under inställningen för automatisk exponering kan varje färg kamera dynamiskt ändra den faktiska exponeringen. Eftersom exponeringen påverkar tids inställningen kan sådana ändringar snabbt pusha kamerornas synkronisering.

Undvik att ange samma exponerings inställning flera gånger i bild Tagningens slinga. Anropa bara API en gång när det behövs.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Undvika störningar mellan flera djup kameror

När flera djup kameror är bild överlappande fält i vyn, måste varje kamera en bild av sin egen associerade laser. För att förhindra att lasererna stör varandra bör kamera avbildningarna förskjutas från varandra med 160 μs eller mer.

För varje djup kamera avbildning blir lasern på nio gånger och aktiv för endast 125 &mu; s varje gång. Lasern är sedan inaktiv i antingen 14505 &mu; s eller 23905 &mu; s, beroende på drifts läge. Det här beteendet innebär att start punkten för förskjutnings beräkningen är 125 &mu; s.

Dessutom ökar skillnaderna mellan kamera klockan och den inbyggda enhetens timer den minsta förskjutningen till 160 &mu; s. Om du vill beräkna en mer exakt förskjutning för konfigurationen noterar du det djupgående läge som du använder och läser [tabellen djup sensorns rå tids inställning](hardware-specification.md#depth-sensor-raw-timing). Genom att använda data från den här tabellen kan du beräkna den minsta förskjutningen (exponerings tiden för varje kamera) med hjälp av följande ekvation:

> *Exponerings tid* = (*IR Pulse* &times; *puls bredd*) + (*inaktiv* &times; *tid* i vilo läge)

När du använder en förskjutning på 160 &mu; s kan du konfigurera upp till nio ytterligare djup kameror så att varje laser blir aktiv medan de andra lasererna är inaktiva.

I program varan använder ```depth_delay_off_color_usec``` eller ```subordinate_delay_off_master_usec``` för att se till att varje IR-laser utlöses i ett eget 160- &mu; fönster eller har ett annat fält i vyn.

> [!NOTE]  
> Den faktiska puls bredden är 125us men vi anger 160us för vissa Leeway. Om du tar NFOV UNBINNED som ett exempel följs varje 125us puls av 1450 inaktiv. Total summan – (9 x 125) + (8 x 1450) – ger exponerings tiden på 12,8 ms. Garderoben du kan överlåta exponeringen för 2 enheter är att den första pulsen för den andra kameran ska falla under den första inaktiva tiden för den första kameran. Fördröjningen mellan den första och den andra kamerorna kan vara så lite som 125us (bredden på en puls) men vi rekommenderar att vissa Leeway därför är 160us. Med den angivna 160us kan du översätta exponerings perioder på högst 10 kameror.

## <a name="prepare-your-devices-and-other-hardware"></a>Förbered dina enheter och annan maskin vara

Förutom flera Azure Kinect DK-enheter kan du behöva hämta ytterligare värddatorer och annan maskin vara för att stödja den konfiguration som du vill skapa. Använd informationen i det här avsnittet för att se till att alla enheter och maskin vara är klara innan du börjar konfigurera.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK-enheter

Gör följande för varje Azure Kinect DK-enhet som du vill synkronisera:

- Kontrol lera att den senaste inbyggda program varan är installerad på enheten. Mer information om hur du uppdaterar dina enheter finns i [Uppdatera den inbyggda program varan för Azure Kinect DK](update-device-firmware.md). 
- Ta bort enhets locket för att visa de synkroniserade portarna.
- Notera serie numret för varje enhet. Du kommer att använda det här numret senare i installations processen.

### <a name="host-computers"></a>Värddatorer

Normalt använder varje Azure Kinect DK den egna värddatorn. Du kan använda en särskild värd styrenhet, beroende på hur du använder enheten och hur mycket data som överförs via USB-anslutningen. 

Se till att Azure Kinect sensor SDK är installerat på varje värddator. Mer information om hur du installerar sensor-SDK finns i [snabb start: Konfigurera din Azure Kinect DK](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux-datorer: USB-minne på Ubuntu

Som standard allokerar Linux-baserade värddatorer bara USB-styrenheten 16 MB kernel-minne för att hantera USB-överföringar. Den här mängden är oftast tillräckligt för att stödja en enda Azure Kinect DK. Men för att stödja flera enheter måste USB-styrenheten ha mer minne. Följ dessa steg om du vill öka minnet:

1. Redigera/**etc/standard/grub**.
1. Leta reda på följande rad:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Ersätt det med den här raden:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > De här kommandona ställer in USB-minnet på 32 MB. Detta är en exempel inställning vid två gånger standardvärdet. Du kan ange ett mycket större värde, efter vad som är lämpligt för din lösning.
1. Kör **sudo Update-grub**.
1. Starta om datorn.

### <a name="cables"></a>Kablar

Om du vill ansluta enheterna till en annan dator och till värddatorerna måste du använda 3,5-mm hane-to-hane-kablar (kallas även 3,5-mm ljud kabel). Kablarna bör vara mindre än 10 meter långa och kan vara stereo eller mono.

Antalet kablar som du måste ha beror på antalet enheter som du använder och även den speciella enhets konfigurationen. Azure Kinect DK-rutan omfattar inte kablar. Du måste köpa dem separat.

Om du ansluter enheterna i Star-konfigurationen måste du också ha en hörlurs delning.

## <a name="connect-your-devices"></a>Anslut dina enheter

**Så här ansluter du Azure Kinect DK-enheter i en seriekopplade-kedje konfiguration**

1. Anslut varje Azure Kinect DK till Power.
1. Anslut varje enhet till värd datorn. 
1. Välj en enhet som ska vara huvud enhet och Anslut en 3,5-mm-ljudkabel till den **synkroniserade** porten.
1. Koppla den andra änden av kabeln till den **synkroniserade porten i** den första underordnade enheten.
1. Om du vill ansluta en annan enhet ansluter du en annan kabel till den **synkroniserade** porten för den första underordnade enheten och till den **synkroniserade porten i** nästa enhet.
1. Upprepa föregående steg tills alla enheter är anslutna. Den sista enheten bör bara ha en kabel anslutning. Den **synkroniserade** porten ska vara tom.

**Så här ansluter du Azure Kinect DK-enheter i en Star-konfiguration**

1. Anslut varje Azure Kinect DK till Power.
1. Anslut varje enhet till värd datorn. 
1. Välj en enhet som ska vara huvud enhet och koppla bort den ena änden av hörlurs delningen i dess **utgående** port.
1. Anslut 3,5-mm-ljudkablar till delnings ändarna för hörlurs delningen.
1. Koppla den andra änden av varje kabel till **synkroniseringen i** porten för en av de underordnade enheterna.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Kontrol lera att enheterna är anslutna och kommunicerar

Använd [Azure Kinect Viewer](azure-kinect-viewer.md)för att kontrol lera att enheterna är korrekt anslutna. Upprepa den här proceduren vid behov för att testa varje underordnad enhet i kombination med huvud enheten

> [!IMPORTANT]  
> I den här proceduren måste du känna till serie numret för varje Azure Kinect DK.

1. Öppna två instanser av Azure Kinect Viewer.
1. Under **Öppna enhet** väljer du serie numret för den underordnade enhet som du vill testa.  
   ![Öppna enhet](./media/open-devices.png)
   > [!IMPORTANT]  
   > Om du vill få exakt bild hämtnings justering mellan alla enheter måste du starta huvud enheten sist.  
1. Under **extern synkronisering** väljer du **Sub**.  
   ![Start av underordnad kamera](./media/sub-device-start.png)
1.  Välj **Starta**.  
    > [!NOTE]  
    > Eftersom det här är en underordnad enhet visar inte Azure Kinect Viewer en avbildning när enheten har startats. Ingen bild visas förrän den underordnade enheten tar emot en synkroniseringsanslutning från huvud enheten.
1. När den underordnade enheten har startat använder du den andra instansen av Azure Kinect Viewer för att öppna huvud enheten.
1. Under **extern synkronisering** väljer du **huvud**.
1. Välj **Starta**.

När Azure Kinect-enheten startar ska båda instanserna av Azure Kinect Viewer Visa bilder.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Kalibrera enheterna som en synkroniserad uppsättning

När du har kontrollerat att enheterna kommunicerar korrekt är du redo att kalibrera dem för att skapa avbildningar i en enda domän.

På en enda enhet kalibreras djup-och RGB-kamerorna för att fungera tillsammans. Men när flera enheter måste fungera tillsammans måste de kalibreras för att avgöra hur en avbildning ska transformeras från den mapp i kameran som avbildade den till den kamera som du vill använda för att bearbeta avbildningar.

Det finns flera alternativ för att korsa kalibrerings enheter. Microsoft tillhandahåller [exemplet på GitHub-grön skärm kod](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), som använder opencv-metoden. Readme-filen för det här kod exemplet innehåller mer information och instruktioner för att kalibrera enheterna.

Mer information om kalibrering finns i [använda Azure Kinect kalibrerings funktioner](use-calibration-functions.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat synkroniserade enheter kan du också lära dig hur du använder
> [!div class="nextstepaction"]
> [Azure Kinect sensor SDK-post och uppspelnings-API](record-playback-api.md)

## <a name="related-topics"></a>Relaterade ämnen

- [Om Azure Kinect sensor SDK](about-sensor-sdk.md)
- [Maskinvaruspecifikationer för Azure Kinect DK](hardware-specification.md) 
- [Snabb start: Konfigurera din Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [Uppdatera inbyggd Azure Kinect DK-programvara](update-device-firmware.md) 
- [Återställa Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect Viewer](azure-kinect-viewer.md) 
