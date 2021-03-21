---
title: Hantera StorSimple 8000-seriens enhets styrenheter | Microsoft Docs
description: Lär dig att stoppa, starta om, stänga av eller återställa StorSimple-enhetens styrenheter.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94957001"
---
# <a name="manage-your-storsimple-device-controllers"></a>Hantera dina StorSimple enhets styrenheter

## <a name="overview"></a>Översikt

I den här självstudien beskrivs de olika åtgärder som kan utföras på dina StorSimple enhets styrenheter. Kontrollanterna i din StorSimple-enhet är redundanta (peer-) kontrollanter i en aktiv-passiv konfiguration. Vid en specifik tidpunkt är bara en kontrollant aktiv och bearbetar alla disk-och nätverks åtgärder. Den andra styrenheten är i ett passivt läge. Om den aktiva styrenheten Miss lyckas blir den passiva styrenheten automatiskt aktiv.

Den här självstudien innehåller stegvisa instruktioner för att hantera enhetens styrenheter med hjälp av:

* **Kontrollant** -bladet för din enhet i StorSimple Enhetshanteraren-tjänsten.
* Windows PowerShell för StorSimple.

Vi rekommenderar att du hanterar enhets styrenheterna via tjänsten StorSimple Enhetshanteraren. Om en åtgärd bara kan utföras med hjälp av Windows PowerShell för StorSimple, gör självstudien en anteckning.

När du har läst den här självstudien kommer du att kunna:

* Starta om eller stänga av en StorSimple enhets styrenhet
* Stänga av en StorSimple-enhet
* Återställa StorSimple-enheten till fabriks inställningarna

## <a name="restart-or-shut-down-a-single-controller"></a>Starta om eller stänga av en enskild styrenhet
En omstart eller avstängning av styrenhet krävs inte som en del av den normala system åtgärden. Avstängnings åtgärder för en enda enhets styrenhet är vanligt vis bara i fall där en felande enhets maskin varu komponent måste bytas ut. En omstart av styrenheten kan också krävas i en situation där prestanda påverkas av överdriven minnes användning eller en felaktig styrenhet. Du kan också behöva starta om en styrenhet efter en lyckad utbyte av styrenheten om du vill aktivera och testa den ersatta styrenheten.

Omstart av en enhet stör inte anslutna initierare, förutsatt att den passiva styrenheten är tillgänglig. Om en passiv styrenhet inte är tillgänglig eller är inaktiverad kan omstart av den aktiva styrenheten leda till avbrott i tjänsten samt driftstopp.

> [!IMPORTANT]
> * **En körnings kontroll ska aldrig tas bort fysiskt eftersom detta skulle leda till en förlust av redundans och en ökad risk för stillestånds tid.**
> * Följande procedur gäller endast för den fysiska StorSimple-enheten. Information om hur du startar, stoppar och startar om StorSimple Cloud Appliance finns i [arbeta med moln](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance)installationen.

Du kan starta om eller stänga av en enskild enhets styrenhet via Azure Portal av tjänsten StorSimple Enhetshanteraren eller Windows PowerShell för StorSimple.

Utför följande steg för att hantera dina enhets styrenheter från Azure Portal.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Starta om eller stänga av en kontrollant i Azure Portal
1. I StorSimple Enhetshanteraren-tjänsten går du till **enheter**. Välj din enhet i listan med enheter. 

    ![Välj en enhet](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Gå till **inställningar > styrenheter**.
   
    ![Kontrol lera att StorSimple enhets styrenheter är felfria](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. På bladet **kontrollanter** kontrollerar du att statusen för båda styrenheterna på enheten är **felfri**. Välj en kontrollant, högerklicka på och välj sedan **starta om** eller **Stäng av**.

    ![Välj Starta om eller Stäng av StorSimple enhets styrenheter](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Ett jobb skapas för att starta om eller stänga av styrenheten och du visas med tillämpliga varningar, om det finns några. Om du vill övervaka omstart eller avstängning går du till **tjänst > aktivitets loggar** och filtrerar sedan efter parametrar som är speciella för din tjänst. Om en kontroll enhet stängts av måste du trycka på strömbrytaren för att aktivera kontroll enheten.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Starta om eller stänga av en kontrollant i Windows PowerShell för StorSimple
Utför följande steg för att stänga av eller starta om en enskild styrenhet på din StorSimple-enhet från Windows PowerShell för StorSimple.

1. Få åtkomst till enheten via serie konsolen eller en Telnet-session från en fjärran sluten dator. Om du vill ansluta till styrenhet 0 eller styrenhet 1 följer du stegen i [använda SparaTillFil för att ansluta till enhetens serie konsol](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**.
3. I informations meddelandet noterar du den styrenhet som du är ansluten till (kontroll enhet 0 eller styrenhet 1) och om den är aktiv eller passiv (standby) styrenhet.
   
   * Om du vill stänga av en enda styrenhet skriver du följande i prompten:
     
       `Stop-HcsController`
     
       Detta stänger av den styrenhet som du är ansluten till. Om du stoppar den aktiva kontroll enheten växlar enheten över till den passiva styrenheten.

   * Om du vill starta om en styrenhet skriver du följande i prompten:
     
       `Restart-HcsController`
     
       Detta startar om den styrenhet som du har anslutning till. Om du startar om den aktiva styrenheten växlar den över till den passiva styrenheten innan omstarten.

## <a name="shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet

I det här avsnittet beskrivs hur du stänger av en pågående eller misslyckad StorSimple-enhet från en fjärrdator. En enhet stängs av när båda styrenheterna för enheten har stängts av. En avstängning av enheten görs när enheten flyttas fysiskt eller tas ur bruk.

> [!IMPORTANT]
> Kontrol lera hälso tillståndet för enhets komponenterna innan du stänger av enheten. Navigera till din enhet och klicka sedan på **inställningar > maskin varu hälsa**. På bladet **status och maskin varu hälsa** kontrollerar du att indikator status för alla komponenter är grön. Endast en felfria enhet har en grön status. Om enheten stängs av för att ersätta en fel komponent visas en misslyckad (röd) eller degraderad (gul) status för respektive komponent (er).


#### <a name="to-shut-down-a-storsimple-device"></a>Stänga av en StorSimple-enhet

1. Använd proceduren [starta om eller Stäng av en styrenhet](#restart-or-shut-down-a-single-controller) för att identifiera och stänga av den passiva styrenheten på enheten. Du kan utföra den här åtgärden i Azure Portal eller i Windows PowerShell för StorSimple.
2. Upprepa det här steget för att stänga av den aktiva styrenheten.
3. Du måste nu titta på enhetens bak plan. När de två styrenheterna är helt avstängda måste status lamporna på båda styrenheterna blinka. Om du behöver stänga av enheten helt och hållet, vänder du tillbaka ström växlarna i både Power-och kylnings moduler (PCMs). Detta bör stänga av enheten.

## <a name="reset-the-device-to-factory-default-settings"></a>Återställa enheten till fabriksinställningarna

> [!IMPORTANT]
> Kontakta Microsoft Support om du behöver återställa enheten till fabriks inställningarna. Proceduren som beskrivs nedan bör endast användas tillsammans med Microsoft Support.

Den här proceduren beskriver hur du återställer Microsoft Azure StorSimple-enheten till fabriks inställningarna med hjälp av Windows PowerShell för StorSimple.
Att återställa en enhet tar bort alla data och inställningar från hela klustret som standard.

Utför följande steg för att återställa Microsoft Azure StorSimple-enheten till fabriks inställningarna:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Återställa enheten till standardinställningarna i Windows PowerShell för StorSimple
1. Få åtkomst till enheten via dess serie konsol. Kontrol lera informations meddelandet för att se till att du är ansluten till den **aktiva** kontrollanten.
2. I menyn serie konsol väljer du alternativ 1, **loggar in med fullständig åtkomst**.
3. Skriv följande kommando i kommando tolken för att återställa hela klustret, ta bort alla data, metadata och styrenhets inställningar:
   
    `Reset-HcsFactoryDefault`
   
    För att i stället återställa en enda kontrollant använder du cmdleten  [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) med `-scope` parametern.)
   
    Systemet startas om flera gånger. Du får ett meddelande när återställningen har slutförts. Beroende på system modellen kan det ta 45-60 minuter för en 8100-enhet och 60-90 minuter för en 8600 för att slutföra den här processen.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Frågor och svar om hantering av enhets styrenheter
I det här avsnittet har vi sammanfattat några vanliga frågor om hantering av StorSimple enhets styrenheter.

**C.** Vad händer om båda styrenheterna på enheten är felfria och påslagna och jag startar om eller stänger av den aktiva styrenheten?

**En.** Om båda styrenheterna på enheten är felfria och påslagna, uppmanas du att bekräfta. Du kan välja att:

* **Starta om den aktiva styrenheten** – du får ett meddelande om att en aktiv kontroll enhet har orsakat att enheten växlar över till den passiva styrenheten. Kontroll enheten startas om.
* **Stänga av en aktiv kontrollant** – du får ett meddelande om att avstängning av en aktiv kontroll enhet leder till stillestånds tid. Du måste också trycka på strömbrytaren på enheten för att aktivera styrenheten.

**C.** Vad händer om den passiva styrenheten på enheten är otillgänglig eller inaktive rad och jag startar om eller stänger av den aktiva styrenheten?

**En.** Om den passiva styrenheten på enheten inte är tillgänglig eller inaktive rad och du väljer att:

* **Starta om den aktiva styrenheten** – du får ett meddelande om att åtgärden fortsätter att leda till ett tillfälligt avbrott i tjänsten och du uppmanas att bekräfta.
* **Stänga av en aktiv kontrollant** – du får ett meddelande om att åtgärden resulterar i stillestånds tid. Du måste också trycka på strömbrytaren på en eller båda styrenheterna för att aktivera enheten. Du uppmanas att bekräfta.

**C.** När går det inte att starta om eller stänga av styrenheten?

**En.** Det går inte att starta om eller stänga av en styrenhet om:

* En enhets uppdatering pågår.
* En omstart av enheten pågår redan.
* Det pågår redan en avstängning av kontrollanten.

**C.** Hur kan du ta reda på om en kontroll enhet startats om eller stängas av?

**En.** Du kan kontrol lera styrenhets status på bladet kontrollant. Status för styrenheten anger om en kontroll enhet håller på att startas om eller stängs av. **Aviserings** bladet innehåller dessutom en informations avisering om kontroll enheten startas om eller stängs av. Åtgärderna för omstart och avstängning av styrenheten registreras också i aktivitets loggarna. Mer information om aktivitets loggar finns i [Visa aktivitets loggar](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**C.** Påverkas eventuell påverkan på I/O på grund av kontrollantens redundans?

**En.** TCP-anslutningarna mellan initierare och aktiv kontroll enhet återställs till följd av kontrollantens redundans, men kommer att återupprättas när den passiva styrenheten antar åtgärd. Det kan finnas en tillfällig (mindre än 30 sekunder) i i/O-aktivitet mellan initierare och enheten under den här åtgärden.

**C.** Hur gör jag för att återställa min styrenhet till tjänsten när den har stängts och tagits bort?

**En.** Om du vill returnera en styrenhet till tjänsten måste du infoga den i chassit enligt beskrivningen i [Ersätt en Controller-modul på din StorSimple-enhet](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Nästa steg
* Om du stöter på problem med dina StorSimple enhets styrenheter som du inte kan lösa med hjälp av procedurerna i den här själv studie kursen [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Om du vill veta mer om hur du använder StorSimple Enhetshanteraren-tjänsten går du till [använda StorSimple Enhetshanteraren-tjänsten för att administrera StorSimple-enheten](storsimple-8000-manager-service-administration.md).