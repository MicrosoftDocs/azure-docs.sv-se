---
title: Konfigurera MPIO för din StorSimple-enhet | Microsoft Docs
description: Beskriver hur du konfigurerar Multipath I/O (MPIO) för din StorSimple-enhet som är ansluten till en värd som kör Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968816"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurera Multipath I/O för din StorSimple-enhet

I den här självstudien beskrivs de steg som du bör följa för att installera och använda funktionen Multipath I/O (MPIO) på en värd som kör Windows Server 2012 R2 och ansluten till en fysisk StorSimple-enhet. Vägledningen i den här artikeln gäller endast fysiska enheter i StorSimple 8000-serien. MPIO stöds för närvarande inte på en StorSimple Cloud Appliance.

Microsoft har byggt support för funktionen Multipath I/O (MPIO) i Windows Server för att hjälpa dig att skapa hög tillgängliga, feltoleranta iSCSI-nätverkskonfigurationer. MPIO använder redundanta fysiska Sök vägs komponenter – kort, kablar och växlar – för att skapa logiska sökvägar mellan servern och lagrings enheten. Om det uppstår ett komponent haveri, vilket leder till att en logisk sökväg Miss lyckas, använder multipath Logic en alternativ sökväg för I/O så att program fortfarande kan komma åt sina data. Beroende på din konfiguration kan MPIO också förbättra prestanda genom att ombalansera belastningen över alla dessa sökvägar. Mer information finns i [MPIO-översikt](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Översikt över MPIO och funktioner").

För hög tillgänglighet för din StorSimple-lösning ska MPIO konfigureras på din StorSimple-enhet. När MPIO är installerat på dina värd servrar som kör Windows Server 2012 R2 kan servrarna tolerera en länk, ett nätverk eller ett gränssnitts haveri.

## <a name="mpio-configuration-summary"></a>Sammanfattning av MPIO-konfiguration

MPIO är en valfri funktion på Windows Server och installeras inte som standard. Den installeras som en funktion via Server Manager.

Följ de här stegen för att konfigurera MPIO på din StorSimple-enhet:

* Steg 1: installera MPIO på Windows Server-värden
* Steg 2: konfigurera MPIO för StorSimple-volymer
* Steg 3: montera StorSimple-volymer på värden
* Steg 4: konfigurera MPIO för hög tillgänglighet och belastnings utjämning

Vart och ett av de föregående stegen beskrivs i följande avsnitt.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Steg 1: installera MPIO på Windows Server-värden

Slutför följande procedur för att installera den här funktionen på Windows Server-värden.

#### <a name="to-install-mpio-on-the-host"></a>Installera MPIO på värden

1. Öppna Serverhanteraren på Windows Server-värden. Som standard startar Serverhanteraren när en medlem i gruppen Administratörer loggar in på en dator som kör Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **Start > Serverhanteraren**.
   
   ![Serverhanteraren](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klicka på **Serverhanteraren > instrument panel > Lägg till roller och funktioner**. Då startas guiden **Lägg till roller och funktioner** .
   
   ![Guiden Lägg till roller och funktioner 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. Utför följande steg i guiden **Lägg till roller och funktioner** :
   
   1. Klicka på **Nästa** på sidan **Innan du börjar**.
   2. På sidan **Välj Installations typ** godkänner du standardinställningen för **rollbaserad eller funktions baserad** installation. Klicka på **Nästa**.
   
       ![Guiden Lägg till roller och funktioner 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. På sidan **Välj mål server** väljer du **Välj en server från** serverpoolen. Värd servern ska identifieras automatiskt. Klicka på **Nästa**.
   4. På sidan **Välj serverroller**, klickar du på **Nästa**.
   5. På sidan **Välj funktioner** väljer du **Multipath I/O** och klickar på **Nästa**.
   
       ![Guiden Lägg till roller och funktioner 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. På sidan **Bekräfta installations inställningarna** bekräftar du valet och väljer sedan **starta om mål servern automatiskt om det behövs**, som du ser nedan. Klicka på **Installera**.
   
       ![Guiden Lägg till roller och funktioner 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Du får ett meddelande när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
   
       ![Guiden Lägg till roller och funktioner 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Steg 2: konfigurera MPIO för StorSimple-volymer

MPIO måste konfigureras för att identifiera StorSimple-volymer. Utför följande steg för att konfigurera MPIO för att identifiera StorSimple-volymer.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurera MPIO för StorSimple-volymer

1. Öppna **MPIO-konfigurationen**. Klicka på **Serverhanteraren > instrument panel > verktyg > MPIO**.
2. I dialog rutan **Egenskaper för MPIO** väljer du fliken **identifiera flera sökvägar** .
3. Välj **Lägg till stöd för iSCSI-enheter** och klicka sedan på **Lägg till**.  
   ![MPIO-egenskaper identifierar flera sökvägar](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Starta om servern när du uppmanas till det.
5. I dialog rutan **Egenskaper för MPIO** klickar du på fliken MPIO- **enheter** . Klicka på **Lägg till**.
    </br>![MPIO-egenskaper MPIO-enheter](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. I dialog rutan **Lägg till MPIO-stöd** under **enhetens maskinvaru-ID** anger du enhetens serie nummer. Hämta enhetens serie nummer genom att komma åt StorSimple Enhetshanteraren-tjänsten. Navigera till **enheter > instrument panelen**. Enhetens serie nummer visas i den högra rutan i **snabb** fönstret på enhetens instrument panel.
    </br>
    ![Lägg till stöd för MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Starta om servern när du uppmanas till det.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Steg 3: montera StorSimple-volymer på värden

När MPIO har kon figurer ATS på Windows Server kan volym (er) som skapats på StorSimple-enheten monteras och kan sedan dra nytta av MPIO för redundans. Utför följande steg för att montera en volym.

#### <a name="to-mount-volumes-on-the-host"></a>Montera volymer på värden

1. Öppna fönstret **Egenskaper för iSCSI-initierare** på Windows Server-värden. Klicka på **Serverhanteraren > instrument panel > verktyg > iSCSI-initierare**.
2. I dialog rutan **Egenskaper för iSCSI-initierare** klickar du på fliken identifiering och klickar sedan på **identifiera mål Portal**.
3. I dialog rutan **identifiera mål Portal** utför du följande steg:
   
   1. Ange IP-adressen för StorSimple-enhetens DATA port (ange till exempel DATA 0).
   2. Klicka på **OK** för att återgå till dialog rutan **Egenskaper för iSCSI-initierare** .
     
      > [!IMPORTANT]
      > **Om du använder ett privat nätverk för iSCSI-anslutningar anger du IP-adressen för den DATA port som är ansluten till det privata nätverket.**
    
4. Upprepa steg 2-3 för ett andra nätverks gränssnitt (till exempel DATA 1) på enheten. Tänk på att dessa gränssnitt ska vara aktiverade för iSCSI. Mer information finns i [ändra nätverks gränssnitt](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Välj fliken **mål** i dialog rutan **Egenskaper för iSCSI-initierare** . Du bör se iSCSI-StorSimple för enhets mål under **identifierade mål**.

   ![Fliken Egenskaper för iSCSI-initierare](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klicka på **Anslut** för att upprätta en iSCSI-session med din StorSimple-enhet. Dialog rutan **Anslut till mål** visas.
7. I dialog rutan **Anslut till mål** markerar du kryss rutan **aktivera flera sökvägar** . Klicka på **Avancerat**.
8. I dialog rutan **Avancerade inställningar** utför du följande steg:
   
   1. Välj **Microsoft iSCSI Initiator** i list rutan **lokalt kort** .
   2. Välj IP-adressen för värden i list rutan **INITIERA IP** -adress.
   3. I list rutan IP-adress för **mål Portal** väljer du IP för enhets gränssnittet.
   4. Klicka på **OK** för att återgå till dialog rutan **Egenskaper för iSCSI-initierare** .
9. Klicka på **Egenskaper**. I dialog rutan **Egenskaper** klickar du på **Lägg till session**.
10. I dialog rutan **Anslut till mål** markerar du kryss rutan **aktivera flera sökvägar** . Klicka på **Avancerat**.
11. I dialog rutan **Avancerade inställningar** :

    1. Välj Microsoft iSCSI Initiator i list rutan **lokalt kort** .
    2. I list rutan **INITIERA IP** väljer du den IP-adress som motsvarar värden. I det här fallet ansluter du två nätverks gränssnitt på enheten till ett enda nätverks gränssnitt på värden. Därför är det här gränssnittet detsamma som det som angavs för den första sessionen.
    3. I list rutan **IP-adress för mål Portal** väljer du IP-adressen för det andra data gränssnittet som är aktiverat på enheten.
    4. Klicka på **OK** för att återgå till dialog rutan Egenskaper för iSCSI-initierare. Du har lagt till en andra session till målet.
12. Öppna **dator hantering** genom att gå till **Serverhanteraren > instrument panel > dator hantering**. I den vänstra rutan klickar du på **lagring > disk hantering**. Volymen som skapades på den StorSimple-enhet som är synlig för den här värden visas under **disk hantering** som nya diskar.
13. Initiera disken och skapa en ny volym. Under format processen väljer du en block storlek på 64 KB.
    
    ![Diskhantering](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. Under **disk hantering** högerklickar du på **disken** och väljer **Egenskaper**.
15. Klicka på fliken **MPIO** i dialog rutan StorSimple modell # # # # **Egenskaper för disk enhet med flera sökvägar** .
    
    ![StorSimple 8100-disk DeviceProp med flera sökvägar.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. I avsnittet **DSM-namn** klickar du på **information** och kontrollerar att parametrarna har angetts till standard parametrarna. Standard parametrarna är:
    
    * Sök vägs kontroll period = 30
    * Antal återförsök = 3
    * SUB-Remove-period = 20
    * Återförsöksintervall = 1
    * Sök vägs verifiering aktive rad = avmarkerad.

> [!NOTE]
> **Ändra inte standard parametrarna.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Steg 4: konfigurera MPIO för hög tillgänglighet och belastnings utjämning

För flera Sök vägs baserade hög tillgänglighet och belastnings utjämning måste flera sessioner läggas till manuellt för att deklarera de olika tillgängliga Sök vägarna. Om värden till exempel har två gränssnitt som är anslutna till iSCSI-nätverket och enheten har två gränssnitt som är anslutna till iSCSI-nätverket, behöver du fyra sessioner som kon figurer ATS med korrekta sökvägs-permutationer (endast två sessioner krävs om varje DATA gränssnitt och värd gränssnitt finns i ett annat IP-undernät och inte kan dirigeras).

**Vi rekommenderar att du har minst 8 aktiva parallella sessioner mellan enheten och program värden.** Detta kan uppnås genom att aktivera 4 nätverks gränssnitt på Windows Server-systemet. Använd fysiska nätverks gränssnitt eller virtuella gränssnitt via teknik för nätverksvirtualisering på maskin-eller operativ system nivå på Windows Server-värden. Med de två nätverks gränssnitten på enheten skulle den här konfigurationen leda till åtta aktiva sessioner. Den här konfigurationen hjälper till att optimera enheten och moln data flödet.

> [!IMPORTANT]
> **Vi rekommenderar att du inte blandar 1 GbE-och 10 GbE-nätverkskort. Om du använder två nätverks gränssnitt ska båda gränssnitten vara av en identisk typ.**

Följande procedur beskriver hur du lägger till sessioner när en StorSimple-enhet med två nätverks gränssnitt är ansluten till en värd med två nätverks gränssnitt. Detta ger bara 4 sessioner. Använd samma procedur med en StorSimple-enhet med två nätverks gränssnitt som är anslutna till en värd med fyra nätverks gränssnitt. Du måste konfigurera 8 i stället för de 4 sessioner som beskrivs här.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurera MPIO för hög tillgänglighet och belastnings utjämning

1. Utför en identifiering av målet: i dialog rutan **Egenskaper för iSCSI-initierare** klickar du på **identifiera Portal** på fliken **identifiering** .
2. I dialog rutan **Anslut till mål** anger du IP-adressen för ett av enhetens nätverks gränssnitt.
3. Klicka på **OK** för att återgå till dialog rutan **Egenskaper för iSCSI-initierare** .
4. I dialog rutan **Egenskaper för iSCSI-initierare** väljer du fliken **mål** , markerar det identifierade målet och klickar sedan på **Anslut**. Dialog rutan **Anslut till mål** visas.
5. I dialog rutan **Anslut till mål** :
   
   1. Låt standardinställningen för den valda mål inställningen **lägga till den här anslutningen** till listan över favorit mål. Detta gör att enheten automatiskt försöker starta om anslutningen varje gång datorn startas om.
   2. Markera kryss rutan **aktivera flera sökvägar** .
   3. Klicka på **Avancerat**.
6. I dialog rutan **Avancerade inställningar** :
   
   1. Välj **Microsoft iSCSI Initiator** i list rutan **lokalt kort** .
   2. I list rutan **INITIERA IP** väljer du den IP-adress som motsvarar det första gränssnittet på värden (iSCSI-gränssnittet).
   3. I list rutan **IP-adress för mål Portal** väljer du IP-adressen för det första data gränssnittet som är aktiverat på enheten.
   4. Klicka på **OK** för att återgå till dialog rutan Egenskaper för iSCSI-initierare.
7. Klicka på **Egenskaper** och i dialog rutan **Egenskaper** klickar du på **Lägg till session**.
8. I dialog rutan **Anslut till mål** markerar du kryss rutan **aktivera flera sökvägar** och klickar sedan på **Avancerat**.
9. I dialog rutan **Avancerade inställningar** :
   
   1. Välj **Microsoft iSCSI Initiator** i list rutan **lokalt kort** .
   2. I list rutan **INITIERA IP** väljer du den IP-adress som motsvarar det andra iSCSI-gränssnittet på värden.
   3. I list rutan **IP-adress för mål Portal** väljer du IP-adressen för det andra data gränssnittet som är aktiverat på enheten.
   4. Klicka på **OK** för att återgå till dialog rutan **Egenskaper för iSCSI-initierare** . Du har nu lagt till en andra session till målet.
10. Upprepa steg 8-10 för att lägga till ytterligare sessioner (sökvägar) till målet. Med två gränssnitt på värden och två på enheten kan du lägga till totalt fyra sessioner.
11. När du har lagt till önskade sessioner (sökvägar) i dialog rutan **Egenskaper för iSCSI-initierare** väljer du målet och klickar på **Egenskaper**. På fliken sessioner i dialog rutan **Egenskaper** noterar du de fyra sessions-ID: n som motsvarar möjliga Sök vägs permutationer. Om du vill avbryta en session markerar du kryss rutan bredvid sessions-ID och klickar sedan på **Koppla från**.
12. Om du vill visa enheter som visas i sessioner väljer du fliken **enheter** . Om du vill konfigurera MPIO-principen för en vald enhet klickar du på **MPIO**. Dialog rutan **enhets information** visas. På fliken **MPIO** kan du välja lämpliga inställningar för **belastnings Utjämnings princip** . Du kan också visa den **aktiva** eller **vänte läges** typen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att använda tjänsten StorSimple Enhetshanteraren för att ändra StorSimple-enhetens konfiguration](storsimple-8000-modify-device-config.md).