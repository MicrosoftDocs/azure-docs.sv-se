---
title: Ersätta en enhets styrenhet för StorSimple 8000-serien | Microsoft Docs
description: Förklarar hur du tar bort och ersätter en eller båda Controller-modulerna på din enhet med StorSimple 8000-serien.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85514087"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Ersätta en Controller-modul på din StorSimple-enhet
## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du tar bort och ersätter en eller båda Controller-modulerna på en StorSimple enhet. Den beskriver också den underliggande logiken för ett utbytes scenarier med en och två styrenheter.

> [!NOTE]
> Innan du utför en kontroll enhet rekommenderar vi att du alltid uppdaterar den inbyggda program varan till den senaste versionen.
> 
> För att förhindra skada på din StorSimple-enhet ska du inte mata ut kontroll enheten förrän lysdioderna visas som något av följande:
> 
> * Alla lampor är av.
> * INDIKATORn 3, ![ grön bock ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) och ![ röd kors ikon ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) blinkar och lampan 0 och lampa 7 är **på**.


I följande tabell visas de scenarier som stöds vid utbyte av styrenheter.

| Ärende | Ersättnings scenario | Tillämplig procedur |
|:--- |:--- |:--- |
| 1 |En styrenhet är i ett felaktigt tillstånd, den andra styrenheten är felfri och aktiv. |[Utbyte av en enda styrenhet](#replace-a-single-controller), som beskriver [logiken bakom en utbyte av en enhet](#single-controller-replacement-logic), samt [ersättnings stegen](#single-controller-replacement-steps). |
| 2 |Båda styrenheterna har misslyckats och behöver bytas ut. Chassit, diskarna och disk kabinettet är felfria. |[Utbyte av dubbla styrenheter](#replace-both-controllers), som beskriver [logiken bakom en utbyte av dubbla styrenheter](#dual-controller-replacement-logic), samt [ersättnings steg](#dual-controller-replacement-steps). |
| 3 |Styrenheter från samma enhet eller från olika enheter byts ut. Chassin, diskar och disk höljen är felfria. |Ett varnings meddelande om fel plats fel visas. |
| 4 |En kontrollant saknas och den andra styrenheten Miss lyckas. |[Utbyte av dubbla styrenheter](#replace-both-controllers), som beskriver [logiken bakom en utbyte av dubbla styrenheter](#dual-controller-replacement-logic), samt [ersättnings steg](#dual-controller-replacement-steps). |
| 5 |En eller båda styrenheterna har misslyckats. Du kan inte komma åt enheten via serie konsolen eller Windows PowerShell-fjärrkommunikation. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du vill ha en manuell metod för utbyte av styrenheter. |
| 6 |Kontroll enheterna har en annan version som kan bero på att:<ul><li>Kontrollanter har en annan program varu version.</li><li>Kontrollanter har olika versioner av inbyggd program vara.</li></ul> |Om styrenhetens program varu versioner skiljer sig, identifierar ersättnings logiken att och uppdaterar program versionen på ersättnings styrenheten.<br><br>Om styrenheterna för den inbyggda program varan är olika och den gamla versionen av den inbyggda program varan **inte** kan uppgraderas automatiskt, visas ett varnings meddelande i Azure Portal. Du bör söka efter uppdateringar och installera uppdateringar av inbyggd program vara.</br></br>Om styrenheterna för den inbyggda program varan är olika och den gamla versionen av inbyggd program vara automatiskt kan uppgraderas, identifierar styrenhetens ersättnings logik detta, och när styrenheten startar uppdateras den inbyggda program varan automatiskt. |

Du måste ta bort en Controller-modul om den har misslyckats. En eller båda styrenhets modulerna kan inte köras, vilket kan resultera i en eller flera styrenheter. För ersättnings procedurer och logiken bakom dem, se följande:

* [Ersätta en enskild domänkontrollant](#replace-a-single-controller)
* [Ersätt båda styrenheterna](#replace-both-controllers)
* [Ta bort en kontrollant](#remove-a-controller)
* [Infoga en kontrollant](#insert-a-controller)
* [Identifiera den aktiva kontrollanten på enheten](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Innan du tar bort och ersätter en kontrollant bör du gå igenom säkerhets informationen i [StorSimple för maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Ersätta en enskild domänkontrollant
Om någon av de två styrenheterna på den Microsoft Azure StorSimple enheten har misslyckats, fungerar dåligt eller saknas, måste du ersätta en enda kontroll enhet.

### <a name="single-controller-replacement-logic"></a>Ersättnings logik för enskild styrenhet
Du bör först ta bort den misslyckade styrenheten. (Den återstående styrenheten i enheten är den aktiva styrenheten.) När du infogar en ersättnings kontroll utförs följande åtgärder:

1. Ersättnings styrenheten börjar direkt kommunicera med StorSimple-enheten.
2. En ögonblicks bild av den virtuella hård disken (VHD) för den aktiva styrenheten kopieras till ersättnings styrenheten.
3. Ögonblicks bilden ändras så att när den nya styrenheten startar från den här virtuella hård disken identifieras den som en vänte läges styrenhet.
4. När ändringarna har slutförts startar ersättnings styrenheten som vänte läge.
5. När båda kontroll enheterna körs kommer klustret att vara online.

### <a name="single-controller-replacement-steps"></a>Ersättnings steg för en kontroll
Utför följande steg om en av styrenheterna i Microsoft Azure StorSimple enheten Miss lyckas. (Den andra kontrollanten måste vara aktiv och körs. Om båda styrenheterna inte fungerar eller inte fungerar kan du gå till [åtgärder med dubbla styrenheter](#dual-controller-replacement-steps).)

> [!NOTE]
> Det kan ta 30 – 45 minuter för styrenheten att starta om och helt återställa från den enskilda styrenhetens ersättnings procedur. Den totala tiden för hela proceduren, inklusive anslutning av kablar, är cirka 2 timmar.


#### <a name="to-remove-a-single-failed-controller-module"></a>Ta bort en enskild felande Controller-modul
1. I Azure Portal går du till tjänsten StorSimple Enhetshanteraren, klickar på **enheter** och klickar sedan på namnet på den enhet som du vill övervaka.
2. Gå till **övervaka > maskin varu hälsa**. Status för antingen Controller 0 eller Controller 1 måste vara röd, vilket tyder på ett haveri.
   
   > [!NOTE]
   > Den felande styrenheten i en ersättning för en enda styrenhet är alltid en reserv styrenhet.
   
3. Använd bild 1 och följande tabell för att leta reda på modulen för styrenheten som misslyckades.
   
    ![Bakplanering av enhetens primära inne slutnings moduler](./media/storsimple-controller-replacement/IC740994.png)
   
    **Bild 1** Tillbaka till StorSimple-enheten
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
4. Ta bort alla anslutna nätverks kablar från data portarna på den felande styrenheten. Om du använder en 8600-modell tar du även bort SAS-kablar som ansluter styrenheten till EBOD-styrenheten.
5. Följ stegen i [ta bort en kontrollant](#remove-a-controller) för att ta bort den misslyckade styrenheten.
6. Installera fabriks ersättningen på samma plats som den felande styrenheten togs bort från. Detta utlöser den enskilda styrenhetens ersättnings logik. Mer information finns i [en ersättnings logik för enskilda styrenheter](#single-controller-replacement-logic).
7. När den enskilda styrenhetens ersättnings logik går ut i bakgrunden ansluter du kablarna igen. Var noga med att ansluta alla kablar exakt på samma sätt som de var anslutna före ersättningen.
8. När kontrollanten har startats om kontrollerar du **styrenhetens status** och **kluster status** i Azure Portal för att kontrol lera att styrenheten är tillbaka till felfritt tillstånd och är i vänte läge.

> [!NOTE]
> Om du övervakar enheten via serie konsolen kan du se flera omstarter medan styrenheten återställs från ersättnings proceduren. När menyn för serie konsolen visas, vet du att ersättningen är klar. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om menyn inte visas inom två timmar efter att du börjat byta kontrollant.
>
> Om du startar uppdatering 4 kan du också använda-cmdleten `Get-HCSControllerReplacementStatus` i Windows PowerShell-gränssnittet på enheten för att övervaka status för styrenhetens ersättnings process.
> 

## <a name="replace-both-controllers"></a>Ersätt båda styrenheterna
När båda styrenheterna på den Microsoft Azure StorSimple enheten har misslyckats, fungerar dåligt eller saknas, måste du ersätta båda styrenheterna. 

### <a name="dual-controller-replacement-logic"></a>Ersättnings logik med dubbla styrenheter
I en byte med dubbla styrenheter tar du först bort både felande kontrollanter och infogar sedan ersättningar. När de två ersättnings styrenheterna infogas inträffar följande:

1. Ersättnings styrenheten i fack 0 kontrollerar följande:
   
   1. Används de aktuella versionerna av den inbyggda program varan och program varan?
   2. Är det en del av klustret?
   3. Körs peer-styrenheten och klustras den?
      
      Om inget av dessa villkor är uppfyllt söker kontrollanten efter den senaste dagliga säkerhets kopieringen (som finns i **nonDOMstorage** på enhet S). Kontrollanten kopierar den senaste ögonblicks bilden av den virtuella hård disken från säkerhets kopian.
2. Kontrollanten i fack 0 använder ögonblicks bilden för att själva bilden.
3. Under tiden väntar kontrollanten i fack 1 på att styrenhet 0 ska slutföra avbildningen och starta.
4. När styrenhet 0 startar identifierar kontrollanten det kluster som skapats av Controller 0, som utlöser den enskilda styrenhetens ersättnings logik. Mer information finns i [en ersättnings logik för enskilda styrenheter](#single-controller-replacement-logic).
5. Därefter körs båda styrenheterna och klustret kommer att anslutas.

> [!IMPORTANT]
> Efter en utbyte av dubbla styrenheter när StorSimple-enheten har kon figurer ATS är det viktigt att du tar en manuell säkerhets kopiering av enheten. Säkerhets kopiering av daglig enhets konfiguration utlöses inte förrän 24 timmar har förflutit. Arbeta med [Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att göra en manuell säkerhets kopiering av enheten.


### <a name="dual-controller-replacement-steps"></a>Steg för utbyte av dubbla styrenheter
Det här arbets flödet krävs när båda styrenheterna i Microsoft Azure StorSimples enheten har misslyckats. Detta kan inträffa i ett Data Center där kyl systemet slutar fungera, och därför går det inte att köra styrenheterna inom en kort tids period. Beroende på om StorSimple-enheten är avstängd eller inte, och om du använder en 8600-eller 8100-modell, krävs en annan uppsättning steg.

> [!IMPORTANT]
> Det kan ta 45 minuter till 1 timme för kontrollanten att startas om och helt återställas från en utbytes process med dubbla styrenheter. Den totala tiden för hela proceduren, inklusive anslutning av kablar, är cirka 2,5 timmar.

#### <a name="to-replace-both-controller-modules"></a>Så här ersätter du båda Controller-modulerna
1. Om enheten är avstängd hoppar du över det här steget och fortsätter till nästa steg. Om enheten är aktive rad stänger du av enheten.
   
   1. Om du använder en 8600-modell stänger du först av den primära inne slutningen och stänger sedan av EBOD-höljet.
   2. Vänta tills enheten har stängts av helt. Alla lysdioder på bak sidan av enheten kommer att inaktive ras.
2. Ta bort alla nätverks kablar som är anslutna till data portarna. Om du använder en 8600-modell tar du även bort SAS-kablar som ansluter den primära inne slutningen till EBOD-höljet.
3. Ta bort båda styrenheterna från StorSimple-enheten. Mer information finns i [ta bort en kontrollant](#remove-a-controller).
4. Sätt fabriks ersättningen för Controller 0 först och sätt sedan in Controller 1. Mer information finns i [Infoga en kontrollant](#insert-a-controller). Detta utlöser den utbytes logiken med dubbla styrenheter. Mer information finns i en [ersättnings logik för dubbla styrenheter](#dual-controller-replacement-logic).
5. När styrenhetens ersättnings logik går ut i bakgrunden ansluter du kablarna igen. Var noga med att ansluta alla kablar exakt på samma sätt som de var anslutna före ersättningen. Se de detaljerade anvisningarna för din modell i avsnittet Kontakta din enhet för att [installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
6. Aktivera StorSimple-enheten. Om du använder en 8600-modell:
   
   1. Se till att EBOD-kammaren är aktive rad först.
   2. Vänta tills EBOD-kabinettet körs.
   3. Aktivera den primära inne slutningen.
   4. När den första styrenheten har startats om och är i felfritt tillstånd kommer systemet att köras.
      
      > [!NOTE]
      > Om du övervakar enheten via serie konsolen kan du se flera omstarter medan styrenheten återställs från ersättnings proceduren. När menyn serie konsol visas, vet du att ersättningen är klar. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md)om menyn inte visas inom 2,5 timmar efter att du har startat om kontrollanten.
     
## <a name="remove-a-controller"></a>Ta bort en kontrollant
Använd följande procedur för att ta bort en felkod från din StorSimple-enhet.

> [!NOTE]
> Följande illustrationer gäller för Controller 0. För kontrollant 1 återförs dessa.


#### <a name="to-remove-a-controller-module"></a>Ta bort en Controller-modul
1. Grepp i modulen mellan skjutreglaget och pekfingret.
2. Håll försiktigt upp ditt tumm-och pekfingret för att frisläppa styrenhets spärren.
   
    ![Frigör lås för styrenhet](./media/storsimple-controller-replacement/IC741047.png)
   
    **Bild 2** Frigör lås för styrenhet
3. Använd lås som en referens för att dra ut styrenheten från chassit.
   
    ![Ta bort styrenhet utanför chassit](./media/storsimple-controller-replacement/IC741048.png)
   
    **Bild 3** Skjuta ut styrenheten från chassit

## <a name="insert-a-controller"></a>Infoga en kontrollant
Använd följande procedur för att installera en modul som är en fabriks kontroll när du tar bort en felaktig modul från din StorSimple-enhet.

#### <a name="to-install-a-controller-module"></a>Så här installerar du en Controller-modul
1. Kontrol lera om det finns någon skada på gränssnitts kopplingarna. Installera inte modulen om något av anslutnings stiften är skadade eller böjda.
2. Dra Controller-modulen till chassit medan låsningen är helt fri.
   
    ![Glidande styrenhet i chassit](./media/storsimple-controller-replacement/IC741053.png)
   
    **Bild 4** Glidande styrenhet i chassit
3. När modulen Controller har infogats börjar du stänga spärren och fortsätter att skicka modulen Controller till chassit. Låsningen kommer att leda kontrollanten på plats.
   
    ![Stänger styrenhets spärr](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figur 5** Stänger styrenhets spärren
4. Du är klar när låsningen fästs på plats. Indikatorn **OK** bör nu vara på.
   
   > [!NOTE]
   > Det kan ta upp till 5 minuter för styrenheten och LYSDIODen att aktivera.
  
5. Verifiera att ersättningen lyckades genom att gå till din enhet i Azure Portal och sedan gå till **övervaka**  >  **maskin varu hälsa** och kontrol lera att både styrenhet 0 och styrenhet 1 är felfria (status är grön).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiera den aktiva kontrollanten på enheten
Det finns många situationer, till exempel första enhets registrering eller styrenhets ersättning, som kräver att du hittar den aktiva styrenheten på en StorSimple-enhet. Den aktiva styrenheten bearbetar all inbyggd disk-och nätverks åtgärder. Du kan använda någon av följande metoder för att identifiera den aktiva styrenheten:

* [Använd Azure Portal för att identifiera den aktiva kontrollanten](#use-the-azure-portal-to-identify-the-active-controller)
* [Använd Windows PowerShell för StorSimple för att identifiera den aktiva kontrollanten](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Kontrol lera den fysiska enheten för att identifiera den aktiva styrenheten](#check-the-physical-device-to-identify-the-active-controller)

Var och en av dessa procedurer beskrivs härnäst.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Använd Azure Portal för att identifiera den aktiva kontrollanten
I Azure Portal navigerar du till enheten och **övervakar**  >  **maskin varu hälsan** och bläddrar till avsnittet **kontrollanter** . Här kan du kontrol lera vilken kontrollant som är aktiv.

![Identifiera aktiv kontrollant i Azure Portal](./media/storsimple-controller-replacement/IC752072.png)

**Bild 6** Azure Portal som visar den aktiva kontrollanten

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Använd Windows PowerShell för StorSimple för att identifiera den aktiva kontrollanten
När du ansluter till enheten via serie konsolen visas ett informations meddelande. Informations meddelandet innehåller grundläggande enhets information, till exempel modell, namn, installerad program version och status för den kontroll enhet som du ansluter till. Följande bild visar ett exempel på ett informations meddelande:

![Serie informations meddelande](./media/storsimple-controller-replacement/IC741098.png)

**Figur 7** Informations meddelande som visar styrenhet 0 som aktiv

Du kan använda informations meddelandet för att avgöra om den enhet som du är ansluten till är aktiv eller passiv.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Kontrol lera den fysiska enheten för att identifiera den aktiva styrenheten
Identifiera den aktiva kontrollanten på enheten genom att leta upp den blå INDIKATORn ovanför DATA 5-porten på bak sidan av den primära inne slutningen.

Om den här INDIKATORn blinkar är kontrollanten aktiv och den andra kontrollanten är i vänte läge. Använd följande diagram och tabell som ett stöd.

![Enhetens primära hölje-omplanering med dataportar](./media/storsimple-controller-replacement/IC741055.png)

**Figur 8** Tillbaka till primärt hölje med data portar och övervaknings indikatorer

| Etikett | Beskrivning |
|:--- |:--- |
| 1-6 |DATA 0 – 5 nätverks portar |
| 7 |Blå indikator |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple av maskin varu komponenter](storsimple-8000-hardware-component-replacement.md).

