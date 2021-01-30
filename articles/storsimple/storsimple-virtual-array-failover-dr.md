---
title: Redundansväxling och haveri beredskap för virtuell StorSimple-matris
description: Läs om haveri beredskap för din Microsoft Azure StorSimple virtuella matris, inklusive detaljerade steg för att redundansväxla till en annan virtuell matris.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a321b2eb79ea01bbf94e1a413c189ac7f4614ad
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096431"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Haveriberedskap och redundansväxling av enhet för StorSimple Virtual Array via Azure-portalen

## <a name="overview"></a>Översikt
I den här artikeln beskrivs haveri beredskap för din Microsoft Azure StorSimple virtuella matris, inklusive detaljerade steg för att redundansväxla till en annan virtuell matris. Med en redundansväxling kan du flytta data från en *käll* enhet i data centret till en *målenhet* . Mål enheten kanske finns på samma eller en annan geografisk plats. Redundansväxlingen för hela enheten. Under redundansväxlingen ändrar käll enhetens moln data ägande till den på mål enheten.

Den här artikeln gäller endast för virtuella StorSimple-matriser. Om du vill redundansväxla en enhet med 8000-serien går du till [enhetens redundans och haveri beredskap för din StorSimple-enhet](./storsimple-8000-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Vad är haveri beredskap och enhets växling vid fel?

I en haveri beredskap (DR) slutar den primära enheten att fungera. I det här scenariot kan du flytta de moln data som är kopplade till den felande enheten till en annan enhet. Du kan använda den primära enheten som *källa* och ange en annan enhet som *mål*. Den här processen kallas för *redundans*. Under redundansväxlingen byter alla volymer eller resurser från käll enheten ägarskap och överförs till mål enheten. Ingen filtrering av data är tillåten.

DR modelleras som en fullständig återställnings enhet med hjälp av termisk karta – baserad nivå och spårning. En värme karta definieras genom att ett värme värde tilldelas data baserat på Läs-och skriv mönster. Den här värme kartan utvärderar sedan de lägsta värme data segmenten till molnet och behåller de höga värme data segmenten (mest använda) på den lokala nivån. Under en DR använder StorSimple värme kartan för att återställa och dehydratisera data från molnet. Enheten hämtar alla volymer/resurser i den senaste senaste säkerhets kopian (som fastställs internt) och utför en återställning från den säkerhets kopian. Den virtuella matrisen dirigerar hela DR-processen.

> [!IMPORTANT]
> Käll enheten tas bort i slutet av redundansväxlingen och därför stöds inte återställning efter fel.
> 
> 

Haveri beredskap dirigeras via funktionen enhets växling vid fel och initieras från bladet **enheter** . Det här bladet tabulates alla StorSimple-enheter som är anslutna till din StorSimple Enhetshanteraren-tjänst. För varje enhet kan du se det egna namnet, status, etablerad och maximal kapacitet, typ och modell.

## <a name="prerequisites-for-device-failover"></a>Förhandskrav för enhetsredundans

### <a name="prerequisites"></a>Förutsättningar

Kontrol lera att följande krav är uppfyllda för en enhets växling vid fel:

* Käll enheten måste vara **inaktive rad** .
* Mål enheten måste visas som **klar för konfiguration** i Azure Portal. Etablera en virtuell mål mat ris med samma eller högre kapacitet. Använd det lokala webb gränssnittet för att konfigurera och registrera den virtuella mål matrisen.
  
  > [!IMPORTANT]
  > Försök inte konfigurera den registrerade virtuella enheten via tjänsten. Ingen enhets konfiguration bör utföras via tjänsten.
  > 
  > 
* Mål enheten får inte ha samma namn som käll enheten.
* Käll-och mål enheten måste vara av samma typ. Du kan bara redundansväxla en virtuell matris som kon figurer ATS som en fil server till en annan fil server. Samma sak gäller för en iSCSI-server.
* För en fil server DR rekommenderar vi att du ansluter mål enheten till samma domän som källan. Den här konfigurationen säkerställer att resurs behörigheterna automatiskt löses. Det finns bara stöd för redundans till en mål enhet i samma domän.
* Tillgängliga mål enheter för DR är enheter som har samma eller större kapacitet jämfört med käll enheten. De enheter som är anslutna till din tjänst men inte uppfyller kriterierna för tillräckligt med utrymme är inte tillgängliga som mål enheter.

### <a name="other-considerations"></a>Ytterligare överväganden

* För planerad redundans:
  
  * Vi rekommenderar att du tar alla volymer eller resurser på käll enheten offline.
  * Vi rekommenderar att du gör en säkerhets kopia av enheten och fortsätter sedan med redundansväxlingen för att minimera data förlust.
* För en oplanerad redundansväxling använder enheten den senaste säkerhets kopian för att återställa data.

### <a name="device-failover-prechecks"></a>För-kontroller av enhetens redundans

Innan DR påbörjas utför enheten för-kontroller. Dessa kontroller hjälper till att se till att inga fel inträffar när DR påbörjas. För-incheckningarna är:

* Verifierar lagrings kontot.
* Kontrollerar moln anslutningen till Azure.
* Kontrollerar tillgängligt utrymme på mål enheten.
* Kontrollerar om en iSCSI-servers käll enhets volym har
  
  * giltiga ACR-namn.
  * giltigt IQN (inte mer än 220 tecken).
  * giltiga CHAP-lösenord (12-16 tecken).

Om någon av de föregående för incheckningarna inte kan fortsätta kan du inte fortsätta med DR. Lös problemen och försök sedan med DR igen.

När DR-filen har slutförts överförs ägarskapet för moln data på käll enheten till mål enheten. Käll enheten är sedan inte längre tillgänglig i portalen. Åtkomst till alla volymer/resurser på käll enheten är blockerad och mål enheten blir aktiv.

> [!IMPORTANT]
> Även om enheten inte längre är tillgänglig, kommer den virtuella dator som du har allokerat på värd systemet fortfarande att förbruka resurser. När DR-filen har slutförts kan du ta bort den virtuella datorn från värd systemet.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Redundansväxla till en virtuell matris

Vi rekommenderar att du etablerar, konfigurerar och registrerar en annan virtuell StorSimple-matris med din StorSimple Enhetshanteraren-tjänst innan du kör den här proceduren.

> [!IMPORTANT]
> 
> * Du kan inte redundansväxla från en StorSimple 8000-serie till en virtuell 1200-enhet.
> * Du kan redundansväxla från en Federal Information Processing Standard (FIPS) aktive rad virtuell enhet till en annan FIPS-aktiverad enhet eller till en icke-FIPS-enhet som distribueras i myndighets portalen.


Utför följande steg för att återställa enheten till en virtuell StorSimple-enhet.

1. Etablera och konfigurera en målenhet som uppfyller [kraven för redundans av enhet](#prerequisites). Slutför enhets konfigurationen via det lokala webb gränssnittet och registrera den till din StorSimple Enhetshanteraren-tjänst. Om du skapar en fil server går du till steg 1 av [Konfigurera som fil Server](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Om du skapar en iSCSI-server går du till steg 1 av [Konfigurera som iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Ta volymer/resurser offline på värden. Om du vill ta volymerna/resurserna offline kan du läsa mer i operativ systemets instruktioner för värden. Om du inte redan är offline måste du göra alla volymer/resurser offline på enheten genom att göra följande.
   
    1. Gå till bladet **enheter** och välj din enhet.
   
    2. Gå till **inställningar > hantera > resurser** (eller **inställningar > hantera > volymer**). 
   
    3. Välj en resurs/volym, högerklicka och välj **Koppla från**. 
   
    4. När du uppmanas att bekräfta kontrollerar **du att den tar den här resursen offline.** 
   
    5. Klicka på **Koppla från**.

3. I StorSimple Enhetshanteraren-tjänsten går du till **hantering > enheter**. På bladet **enheter** väljer du och klickar på din käll enhet.

4. I bladet för din **enhets instrument panel** klickar du på **inaktivera**.

5. Du uppmanas att bekräfta i bladet **inaktivera** . Enhets inaktive ring är en *permanent* process som inte kan ångras. Du blir också påmind om att ta dina resurser/volymer offline på värden. Ange enhets namnet som ska bekräftas och klicka på **inaktivera**.
   
    ![Skärm bild av bladet inaktivera. Rutan enhets namn är ifylld och knappen Inaktivera är markerad.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Inaktive ringen startar. Du får ett meddelande när inaktive ringen har slutförts.
   
    ![Skärm bild av en förlopps indikator som visar att enheten inaktive ras.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Enhetens tillstånd kommer nu att ändras till **inaktiverat** på sidan enheter.
    ![Skärm bild av sidan enheter. Egenskaperna för den inaktiverade enheten visas, inklusive status, som visas som inaktive rad.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. I bladet **enheter** väljer du och klickar på den inaktiverade käll enheten för redundans. 
9. I bladet för **enhets instrument panelen** klickar du på **redundans**. 
10. På bladet **redundansväxla över enhet** gör du följande:
    
    1. Fältet käll enhet fylls i automatiskt. Notera den totala data storleken för käll enheten. Data storleken måste vara mindre än den tillgängliga kapaciteten på mål enheten. Granska informationen som är kopplad till käll enheten, till exempel enhets namn, total kapacitet och namnen på de resurser som har redundansväxlats.

    2. Välj en **målenhet** i list rutan med tillgängliga enheter. Endast de enheter som har tillräcklig kapacitet visas i list rutan.

    3. Kontrol lera att **Jag förstår att den här åtgärden kommer att redundansväxla data till mål enheten**. 

    4. Klicka på **redundans**.
    
        ![Skärm bild av bladet redundansväxla över enhet med käll-och mål enheten ifylld, alternativet markerat och knappen Växla över har marker ATS.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Ett jobb för redundans initieras och du får ett meddelande. Gå till **enheter > jobb** för att övervaka redundansväxlingen.
    
     ![Skärm bild av en förlopps indikator som visar att enheten växlar över.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. På bladet **jobb** visas ett jobb för redundans som skapats för käll enheten. Detta jobb utför DR-förbockarna.
    
    ![Skärm bild som visar att ett redundansväxlings jobb har startats.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     När DR-förbockerna har slutförts skapar redundansväxlingen återställnings jobb för varje resurs/volym som finns på käll enheten.
    
    ![Skärm bild som visar information om redundansväxlingen, till exempel status, enhet och varaktighet.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. När redundansväxlingen är klar går du till bladet **enheter** .
    
    1. Markera och klicka på den StorSimple-enhet som användes som mål enheten för redundansväxlingen.
    2. Gå till **inställningar > hanterings > resurser** (eller **volymer** om iSCSI-servern). På bladet **resurser** kan du Visa alla resurser (volymer) från den gamla enheten.
        ![Skärm bild av bladet enheter. Mål enheten visas med statusen online.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Du måste [skapa ett DNS-alias](https://web.archive.org/web/20150307000707/http://support.microsoft.com:80/kb/168322) så att alla program som försöker ansluta kan omdirigeras till den nya enheten.

## <a name="errors-during-dr"></a>Fel under DR

**Moln anslutnings avbrott under DR**

Om moln anslutningen bryts när DR har startat och innan enhets återställningen är klar, kommer DR att Miss lyckas. Du får ett fel meddelande. Mål enheten för DR är markerad som *oanvändbar.* Du kan inte använda samma mål enhet för framtida DRs.

**Inga kompatibla mål enheter**

Om det inte finns tillräckligt med utrymme på mål enheterna visas ett fel meddelande om att det inte finns några kompatibla mål enheter.

**För kontroll felen**

Om någon av de här förbockarna inte uppfylls visas för ininchecknings felen.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Katastrof återställning av affärs kontinuitet (BCDR)

Ett BCDR-scenario (verksamhets kontinuitet haveri beredskap) inträffar när hela Azure-datacenter slutar fungera. Detta kan påverka din StorSimple-Enhetshanteraren-tjänst och tillhör ande StorSimple-enheter.

Om det finns StorSimple enheter som har registrerats precis innan en haveri påträffas kan dessa StorSimple-enheter behöva tas bort. Efter haveriet kan du återskapa och konfigurera enheterna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du [administrerar den virtuella StorSimple-matrisen med hjälp av det lokala webb gränssnittet](storsimple-ova-web-ui-admin.md).