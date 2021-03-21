---
title: Ändra enhets konfigurationen för StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder tjänsten StorSimple Enhetshanteraren för att konfigurera om en StorSimple-enhet som redan har distribuerats.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 5eb5c351462279fe5c4f790e052f73201d211cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94961183"
---
# <a name="use-the-storsimple-device-manager-service-to-modify-your-storsimple-device-configuration"></a>Använd tjänsten StorSimple Enhetshanteraren för att ändra StorSimple-enhetens konfiguration

## <a name="overview"></a>Översikt

Avsnittet Azure Portal **enhets inställningar** i bladet **Inställningar** innehåller alla enhets parametrar som du kan konfigurera på en StorSimple-enhet som hanteras av en StorSimple Enhetshanteraren-tjänst. I den här självstudien beskrivs hur du kan använda bladet **Inställningar** för att utföra följande uppgifter på enhets nivå:

* Ändra eget namn på enhet
* Ändra inställningar för enhets tid
* Tilldela en sekundär DNS
* Ändra nätverks gränssnitt
* Byta eller omtilldela IP-adresser

## <a name="modify-device-friendly-name"></a>Ändra eget namn på enhet

Du kan använda Azure Portal för att ändra enhetens namn och tilldela det ett unikt eget namn som du själv väljer. Använd bladet **allmänna inställningar** på enheten för att ändra enhetens egna namn. Det egna namnet får innehålla tecken och får innehålla högst 64 tecken.

> [!NOTE] 
> Du kan bara ändra enhets namnet i Azure Portal innan installationen av enheten är klar. När den lägsta enhets konfigurationen är klar kan du inte ändra enhetens namn.

![Enhets namn i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings3.png)

En StorSimple-enhet som är ansluten till StorSimple Enhetshanteraren-tjänsten tilldelas ett standard namn. Standard namnet återspeglar vanligt vis enhetens serie nummer. Ett standardenhets namn som är 15 tecken långt, till exempel 8600-SHX0991003G44HT, anger följande:

* **8600**  – anger enhets modellen.
* **SHX** – anger tillverknings platsen.
* **0991003** -visar en speciell produkt.
* **G44HT**– de sista 5 siffrorna ökar för att skapa unika serie nummer. Det kanske inte är en sekventiell uppsättning.

## <a name="modify-device-description"></a>Beskrivning av ändra enhet

Använd bladet **allmänna inställningar** på enheten för att ändra enhets beskrivningen.

![Enhets beskrivning i allmänna inställningar](./media/storsimple-8000-modify-device-config/modify-general-settings4.png)

En enhets Beskrivning hjälper vanligt vis att identifiera ägaren och den fysiska platsen för enheten. Beskrivnings fältet måste innehålla färre än 256 tecken.

## <a name="modify-time-settings"></a>Ändra tids inställningar

Enheten måste synkronisera tid för att kunna autentisera med din moln lagrings tjänst leverantör. Använd bladet **allmänna inställningar** på enheten för att ändra inställningarna för enhets tid.

![Enhets beskrivning i allmänna inställningar 2](./media/storsimple-8000-modify-device-config/modify-general-settings2.png)

 Välj din tidszon i list rutan. Du kan ange upp till två NTP-servrar (Network Time Protocol):

 - **Primär NTP-server** – konfigurationen är obligatorisk och anges när du använder Windows PowerShell för StorSimple för att konfigurera enheten. Du kan ange standard- **Time.Windows.com** för Windows Server som NTP-server. Du kan visa den primära NTP-serverkonfigurationen via Azure Portal, men du måste använda Windows PowerShell-gränssnittet för att ändra det. Använd `Set-HcsNTPClientServerAddress` cmdleten för att ändra enhetens primära NTP-server. Mer information finns i syntax för cmdleten [set-HcsNTPClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) .

- **Sekundär NTP-server** – konfigurationen är valfri. Du kan använda portalen för att konfigurera en sekundär NTP-server.

När du konfigurerar NTP-servern måste du kontrol lera att ditt nätverk tillåter att NTP-trafik skickas från ditt data Center till Internet. När du anger en offentlig NTP-server måste du kontrol lera att dina nätverks brand väggar och andra säkerhetsenheter har kon figurer ATS för att tillåta att NTP-trafik färdas till och från det externa nätverket. Om dubbelriktad NTP-trafik inte är tillåten måste du använda en intern NTP-server (en Windows-domänkontrollant tillhandahåller den här funktionen). Om enheten inte kan synkronisera tid kan det hända att den inte kan kommunicera med din moln lagrings leverantör.

Om du vill se en lista över offentliga NTP-servrar går du till [webbplatsen NTP-servrar](https://support.ntp.org/bin/view/Servers/WebHome).

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Vad händer om enheten distribueras i en annan tidszon?

Om enheten distribueras i en annan tidszon kommer enhetens tidszon att ändras. Under förutsättning att alla säkerhets kopierings principer använder enhetens tidszon justeras säkerhets kopierings principerna automatiskt i enlighet med den nya tids zonen. Ingen åtgärd från användaren krävs.

## <a name="modify-dns-settings"></a>Ändra DNS-inställningar

En DNS-server används när enheten försöker kommunicera med din moln lagrings tjänst leverantör. Använd bladet **nätverks inställningar** på din enhet för att visa och ändra de konfigurerade DNS-inställningarna. 

![DNS-inställningar i nätverks inställningar](./media/storsimple-8000-modify-device-config/modify-network-settings1.png)

För hög tillgänglighet måste du konfigurera både den primära och sekundära DNS-servern under den första distributionen av enheten.

**Primär DNS-Server** – du använder Windows PowerShell för StorSimple för att först ange den primära DNS-servern under den första installationen. Du kan bara konfigurera om den primära DNS-servern via Windows PowerShell-gränssnittet. Använd `Set-HcsDNSClientServerAddress` cmdleten för att ändra enhetens primära DNS-server. Mer information finns i syntax för cmdleten [set-HcsDNSClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) .

**Sekundär DNS-Server** – om du vill ändra den sekundära DNS-servern använder du `Set-HcsDNSClientServerAddress` cmdleten i Windows PowerShell-gränssnittet på bladet enhet eller **nätverks inställningar** i StorSimple-enheten i Azure Portal.

Om du vill ändra den sekundära DNS-servern i Azure Portal utför du följande steg.

1. Gå till StorSimple Device Manager-tjänsten. I listan med enheter väljer du och klickar på enheten.

2. I bladet **Inställningar** går du till **enhets inställningar > nätverk**. Då öppnas bladet **nätverks inställningar** . Klicka på panelen **DNS-inställningar** . Ändra IP-adressen för den sekundära DNS-servern.

    ![Ändra IP-adress för sekundär DNS-Server](./media/storsimple-8000-modify-device-config/modify-secondary-dns1.png)

4. I kommando fältet klickar du på **Spara** och när du uppmanas att bekräfta klickar du på **OK**.

    ![Spara och bekräfta ändringar](./media/storsimple-8000-modify-device-config/modify-secondary-dns-2.png)



## <a name="modify-network-interfaces"></a>Ändra nätverks gränssnitt

Enheten har sex enhets nätverks gränssnitt, varav fyra är 1 GbE och två av som är 10 GbE. Dessa gränssnitt är märkta som DATA 0 – DATA 5. DATA 0, DATA 1, DATA 4 och DATA 5 är 1 GbE, medan DATA 2 och DATA 3 är 10 GbE-nätverkskort.

Använd bladet **nätverks inställningar** för att konfigurera var och en av de gränssnitt som ska användas.

![Konfigurera nätverks gränssnitt via nätverks inställningar](./media/storsimple-8000-modify-device-config/modify-network-settings3.png)

För att säkerställa hög tillgänglighet rekommenderar vi att du har minst två iSCSI-gränssnitt och två moln aktiverade gränssnitt på enheten. Vi rekommenderar men kräver inte att oanvända gränssnitt inaktive ras.

Följande parametrar visas för varje nätverks gränssnitt:

* **Hastighet** – inte en användar konfigurerbar parameter. DATA 0, DATA 1, DATA 4 och DATA 5 är alltid 1 GbE, medan DATA 2 och DATA 3 är 10 GbE-gränssnitt.
  
  > [!NOTE]
  > Hastighet och duplex är alltid automatiskt förhandlade. Jumboramar-ramar stöds inte.
  
* **Gränssnitts tillstånd** – ett gränssnitt kan aktive ras eller inaktive ras. Om aktive rad kommer enheten att försöka använda gränssnittet. Vi rekommenderar att endast de gränssnitt som är anslutna till nätverket och används är aktiverade. Inaktivera alla gränssnitt som du inte använder.
* **Gränssnitts typ** – med den här parametern kan du isolera iSCSI-trafik från moln lagrings trafik. Den här parametern kan vara något av följande:
  
  * **Moln aktiverat** – när det här alternativet är aktiverat använder enheten det här gränssnittet för att kommunicera med molnet.
  * **iSCSI aktiverat** – när den här inställningen är aktive rad använder enheten det här gränssnittet för att kommunicera med iSCSI-värden.
    
    Vi rekommenderar att du isolerar iSCSI-trafik från moln lagrings trafik. Observera också om din värd är i samma undernät som din enhet, behöver du inte tilldela en gateway. men om värden finns i ett annat undernät än din enhet måste du tilldela en gateway.
* **IP-adress** – när du konfigurerar något av nätverks gränssnitten måste du konfigurera en virtuell IP-adress (VIP). Detta kan vara IPv4 eller IPv6 eller både och. Både IPv4-och IPv6-adress familjer stöds för enhetens nätverks gränssnitt. När du använder IPv4 anger du en 32-bitars IP-adress (*xxx.xxx.xxx.xxx*) i punkt-Decimal form. När du använder IPv6 kan du bara ange ett fyrsiffrigt prefix, och en 128-bitars adress genereras automatiskt för enhetens nätverks gränssnitt baserat på det prefixet.
* **Undernät** – detta avser nät masken och konfigureras via Windows PowerShell-gränssnittet.
* **Gateway** – det här är standard-gatewayen som ska användas av det här gränssnittet när den försöker kommunicera med noder som inte är inom samma IP-adressutrymme (undernät). Standard-gatewayen måste vara i samma adress utrymme (undernät) som gränssnittets IP-adress, som definieras av nät masken.
* **Fast IP-adress** – det här fältet är endast tillgängligt när du konfigurerar data 0-gränssnittet. För åtgärder som uppdateringar eller fel sökning av enheten kan du behöva ansluta direkt till enhets styrenheten. Den fasta IP-adressen kan användas för att få åtkomst till både den aktiva och den passiva styrenheten på enheten.

> [!NOTE]
> * För att säkerställa en korrekt åtgärd kontrollerar du gränssnittets hastighet och duplex på den växel som varje enhets gränssnitt är anslutet till. Växel gränssnitt bör antingen förhandlas med eller konfigureras för Gigabit Ethernet (1000 Mbit/s) och vara full duplex. Gränssnitt som körs med långsammare hastigheter eller i halv duplex leder till prestanda problem.
> * För att minimera avbrott och drift stopp rekommenderar vi att du aktiverar PortFast på var och en av de växel portar som iSCSI-nätverkskortet på enheten ska ansluta till. Detta säkerställer att nätverks anslutningen kan upprättas snabbt i händelse av en redundansväxling.

### <a name="configure-data-0"></a>Konfigurera DATA 0

DATA 0 är Cloud-aktiverat som standard. När du konfigurerar DATA 0 måste du också konfigurera två fasta IP-adresser, en för varje kontrollant. Dessa fasta IP-adresser kan användas för att få åtkomst till enhetens styrenheter direkt och är användbara när du installerar uppdateringar på enheten, för att skräp insamling ska fungera korrekt eller när du får åtkomst till kontrollanterna i fel söknings syfte.

Du kan konfigurera om de fasta IP-styrenheterna via bladet DATA 0 inställningar.

![Konfigurera nätverks gränssnitt – DATA 0](./media/storsimple-8000-modify-device-config/modify-network-settings2.png)

> [!NOTE]
> De fasta IP-adresserna för styrenheten används för att betjäna uppdateringar av enheten och för att utrymmes återtagnings algoritmer (skräp insamling) ska fungera korrekt. Därför måste de fasta IP-adresserna vara dirigerbara och kunna ansluta till Internet.

### <a name="configure-data-1---data-5"></a>Konfigurera DATA 1 – DATA 5

För DATA 1 – DATA 5 nätverks gränssnitt kan du konfigurera alla nätverks inställningar som visas på följande skärm bild:

![Konfigurera nätverks gränssnitt DATA 1 – DATA 5](./media/storsimple-8000-modify-device-config/modify-network-settings4.png)


## <a name="swap-or-reassign-ips"></a>Byta eller omtilldela IP-adresser

Om ett nätverks gränssnitt på styrenheten har tilldelats ett virtuellt nätverk som används (av samma enhet eller någon annan enhet i nätverket), redundansväxlas kontrollanten. Om du byter eller omtilldelar VIP för ett enhets nätverks gränssnitt måste du följa en lämplig procedur för att skapa en dubblett av IP-situationen.

Utför följande steg för att byta ut eller omtilldela VIP för något av nätverks gränssnitten:

#### <a name="to-reassign-ips"></a>Tilldela om IP-adresser

1. Rensa IP-adressen för båda gränssnitten.
2. När IP-adresserna har rensats tilldelar du de nya IP-adresserna till respektive gränssnitt.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [konfigurerar MPIO för din StorSimple-enhet](storsimple-8000-configure-mpio-windows-server.md).
* Lär dig hur du [använder tjänsten StorSimple Enhetshanteraren för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).