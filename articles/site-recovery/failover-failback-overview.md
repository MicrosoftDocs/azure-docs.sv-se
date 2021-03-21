---
title: Om redundans och återställning efter fel i Azure Site Recovery
description: Läs om redundans och fel i Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: b900655d6fdf1143d430ac842bfd84eb1dfdf34c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070752"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Om återställning av redundans/återställning vid fel på plats

Den här artikeln innehåller en översikt över redundansväxling och återställning efter fel vid haveri beredskap på lokala datorer till Azure med [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Återställnings steg

Redundans och återställning efter fel i Site Recovery har fyra steg:

- **Steg 1: redundansväxla från lokal** plats: när du har konfigurerat replikering till Azure för lokala datorer och den lokala platsen kraschar, kan du inte återställa datorerna till Azure. Efter redundansväxlingen skapas virtuella Azure-datorer från replikerade data.
- **Steg 2: skydda virtuella datorer** i Azure: i Azure skyddar du de virtuella Azure-datorerna så att de börjar replikera tillbaka till den lokala platsen. Den lokala virtuella datorn (om tillgänglig) är inaktive rad under återskydd för att säkerställa data konsekvens.
- **Steg 3: redundansväxla från Azure**: när den lokala platsen körs som vanligt igen kör du en annan redundansväxling, den här gången för att återställa virtuella Azure-datorer till din lokala plats. Du kan växla tillbaka till den ursprungliga platsen från vilken du växlade över, eller till en annan plats.
- **Steg 4: återaktivera skydd för lokala datorer**: när du har återställt igen aktiverar du replikeringen av de lokala datorerna till Azure igen.

## <a name="failover"></a>Redundans

Du utför en redundansväxling som en del av din strategi för affärs kontinuitet och haveri beredskap (BCDR).

- Som ett första steg i din BCDR-strategi replikerar du dina lokala datorer till Azure kontinuerligt. Användare får åtkomst till arbets belastningar och appar som körs på lokala käll datorer.
- Om behovet uppstår, till exempel om det uppstår ett avbrott lokalt, kan du inte replikera datorerna till Azure. Virtuella Azure-datorer skapas med hjälp av replikerade data.
- För affärs kontinuitet kan användarna fortsätta att komma åt appar på virtuella Azure-datorer.

Redundans är en aktivitet i två faser:

- **Redundans**: redundansväxlingen som skapar och hämtar en virtuell Azure-dator med den valda återställnings punkten.
- **Genomför**: efter redundans verifierar du den virtuella datorn i Azure:
    - Du kan sedan allokera redundansväxlingen till den valda återställnings punkten eller välja en annan punkt för incheckningen.
    - När redundansen har genomförts går det inte att ändra återställnings punkten.


## <a name="connect-to-azure-after-failover"></a>Ansluta till Azure efter redundans

Det finns ett antal krav för att ansluta till virtuella Azure-datorer som skapats efter redundansväxlingen med RDP/SSH.

**Redundans** | **Plats** | **Åtgärder**
--- | --- | ---
**Virtuell Azure-dator som kör Windows** | På den lokala datorn före redundans | **Åtkomst via Internet**: aktivera RDP. Kontrol lera att TCP-och UDP-regler har lagts till för **offentliga** och att RDP tillåts för alla profiler i **Windows-brandväggen**  >  **tillåtna appar**.<br/><br/> **Åtkomst över plats-till-plats-VPN**: aktivera RDP på datorn. Kontrol lera att RDP tillåts i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.<br/><br/>  Kontrol lera att SAN-principen för operativ systemet är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Se till att inga Windows-uppdateringar väntar på den virtuella datorn när du aktiverar en redundansväxling. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringarna är klara.
**Virtuell Azure-dator som kör Windows** | På den virtuella Azure-datorn efter redundans |  [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrol lera **startdiagnostik** för att verifiera en skärm bild av den virtuella datorn. Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och granskar [fel söknings tips](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuell Azure-dator som kör Linux** | På den lokala datorn före redundans | Kontrol lera att Secure Shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid system start.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Virtuell Azure-dator som kör Linux** | På den virtuella Azure-datorn efter redundans | Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Kontrol lera **startdiagnostiken** för en skärm bild av den virtuella datorn.<br/><br/>

## <a name="types-of-failover"></a>Typer av redundans

Site Recovery tillhandahåller olika alternativ för redundans.

**Redundans** | **Information** | **Återställande** | **Workflow**
--- | --- | --- | ---
**Redundanstest** | Används för att köra en detalj granskning som validerar din BCDR-strategi utan data förlust eller stillestånds tid.| Skapar en kopia av den virtuella datorn i Azure, utan påverkan på pågående replikering eller i produktions miljön. | 1. kör ett redundanstest på en enskild virtuell dator eller flera virtuella datorer i en återställnings plan.<br/><br/> 2. Välj en återställnings punkt som ska användas för redundanstest.<br/><br/> 3. Välj ett Azure-nätverk där den virtuella Azure-datorn ska finnas när den har skapats efter redundansväxlingen. Nätverket används endast för redundanstest.<br/><br/> 4. kontrol lera att granskningen fungerade som förväntat. Site Recovery rensar automatiskt virtuella datorer som skapats i Azure under detalj nivån.
**Planerad redundans-Hyper-V**  | Används vanligt vis för planerad stillestånds tid.<br/><br/> Virtuella käll datorer stängs av. Den senaste informationen synkroniseras innan redundansväxlingen initieras. | Ingen data förlust för det planerade arbets flödet. | 1. planera en underhålls period för drift stopp och meddela användarna.<br/><br/> 2. ta appar som riktas mot användare offline.<br/><br/> 3. Starta en planerad redundansväxling med den senaste återställnings punkten. Redundansväxlingen körs inte om datorn inte är avstängd eller om fel påträffas.<br/><br/> 4. efter redundansväxlingen kontrollerar du att repliken för den virtuella Azure-datorn är aktiv i Azure.<br/><br/> 5. genomför redundansväxlingen genom att slutföra. Åtgärden commit tar bort alla återställnings punkter.
**Redundans-Hyper-V** | Kör vanligt vis om det uppstår ett oplanerat avbrott eller om den primära platsen inte är tillgänglig.<br/><br/> Du kan också stänga av den virtuella datorn och synkronisera de slutgiltiga ändringarna innan du initierar redundansväxlingen.  | Minimal data förlust för appar. | 1. initiera din BCDR-plan. <br/><br/> 2. Starta en redundansväxling. Ange om Site Recovery ska stänga av den virtuella datorn och synkronisera/replikera de senaste ändringarna innan redundansväxlingen utlöses.<br/><br/> 3. du kan växla över till ett antal alternativ för återställnings punkt som sammanfattas i tabellen nedan.<br/><br/> Om du inte aktiverar alternativet att stänga av den virtuella datorn, eller om Site Recovery inte kan stänga av den, används den senaste återställnings punkten.<br/>Redundansväxlingen körs även om datorn inte kan stängas av.<br/><br/> 4. efter redundansväxlingen kontrollerar du att repliken för den virtuella Azure-datorn är aktiv i Azure.<br/> Om det behövs kan du välja en annan återställnings punkt i fönstret kvarhållning på 24 timmar.<br/><br/> 5. genomför redundansväxlingen genom att slutföra. Åtgärden commit tar bort alla tillgängliga återställnings punkter.
**Redundans – VMware** | Kör vanligt vis om det uppstår ett oplanerat avbrott eller om den primära platsen inte är tillgänglig.<br/><br/> Du kan också ange att Site Recovery ska försöka utlösa en avstängning av den virtuella datorn och synkronisera och replikera de slutliga ändringarna innan du initierar redundansväxlingen.  | Minimal data förlust för appar. | 1. initiera din BCDR-plan. <br/><br/> 2. Starta en redundansväxling från Site Recovery. Ange om Site Recovery ska försöka utlösa VM-avstängning och synkronisera innan du kör redundansväxlingen.<br/> Redundansväxlingen körs även om datorerna inte kan stängas av.<br/><br/> 3. efter redundansväxlingen kontrollerar du att repliken för den virtuella Azure-datorn är aktiv i Azure. <br/>Om det behövs kan du välja en annan återställnings punkt i fönstret kvarhållning på 72 timmar.<br/><br/> 5. genomför redundansväxlingen genom att slutföra. Åtgärden commit tar bort alla återställnings punkter.<br/> Site Recovery inaktiverar VMware-verktygen under redundans för virtuella Windows-datorer.

## <a name="failover-processing"></a>Redundansväxling

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kan lägga märke till längre redundanstest för:

* Virtuella VMware-datorer som kör en mobilitets tjänst version som är äldre än 9,8.
* Fysiska servrar.
* Virtuella VMware Linux-datorer.
* Virtuella Hyper-V-datorer som är skyddade som fysiska servrar.
* Virtuella VMware-datorer som inte har DHCP-tjänsten aktive rad.
* Virtuella VMware-datorer som inte har följande start driv rutiner: storvsc, VMBus, storflt, intelide, ATAPI.

## <a name="recovery-point-options"></a>Alternativ för återställnings punkt

Under redundans kan du välja ett antal alternativ för återställnings punkt.

**Alternativ** | **Information**
--- | ---
**Senaste (lägsta återställnings punkt)** | Det här alternativet ger lägsta återställnings punkt mål. Först bearbetar den alla data som har skickats till Site Recovery-tjänsten, för att skapa en återställnings punkt för varje virtuell dator, innan de växlar över till den. Den här återställnings punkten har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.
**Senast bearbetade**  | Det här alternativet växlar över virtuella datorer till den senaste återställnings punkten som bearbetats av Site Recovery. Om du vill se den senaste återställnings punkten för en viss virtuell dator kontrollerar du de **senaste återställnings punkterna** i VM-inställningarna. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
**Senaste appar – konsekvent** |  Det här alternativet växlar över virtuella datorer till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery, om programkonsekventa återställnings punkter är aktiverade. Kontrol lera den senaste återställnings punkten i VM-inställningarna.
**Senaste bearbetade multi-VM** | Det här alternativet är tillgängligt för återställnings planer med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer med inställningen aktive rad redundans till den senaste vanliga återställnings punkten med flera virtuella datorer. Alla andra virtuella datorer i planen växlar över till den senaste bearbetade återställnings punkten.
**Senaste app med flera virtuella datorer – konsekvent** |  Det här alternativet är tillgängligt för återställnings planer med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga programkonsekventa multi-VM-programkonsekventa återställnings punkt. Andra virtuella datorer växlar över till den senaste programkonsekventa återställnings punkten.
**Anpassad** | Använd det här alternativet för att redundansväxla en viss virtuell dator till en viss återställnings punkt i tid. Det här alternativet är inte tillgängligt för återställnings planer.

> [!NOTE]
> Återställnings punkter kan inte migreras till ett annat Recovery Services-valv.

## <a name="reprotectionfailback"></a>Skydd/återställning efter fel

Efter redundansväxlingen till Azure är de replikerade virtuella Azure-datorerna i ett oskyddat tillstånd.

- Som ett första steg för att växla tillbaka till din lokala plats måste du starta virtuella Azure-datorer som replikeras till lokalt. Återställnings processen beror på vilken typ av datorer du har växlat över.
- När datorerna har repliker ATS från Azure till lokalt kan du köra en redundansväxling från Azure till din lokala plats.
- När datorerna är igång lokalt kan du aktivera replikering så att de replikeras till Azure för haveri beredskap.

Återställning efter fel fungerar på följande sätt:

- Om en virtuell dator ska återställas krävs minst en återställnings punkt för att återställas. I en återställnings plan behöver alla virtuella datorer i planen minst en återställnings punkt.
- Vi rekommenderar att du använder den **senaste** återställnings punkten för att växla tillbaka (detta är en krasch-konsekvent punkt).
    - Det finns ett programkonsekvent återställnings punkt alternativ. I det här fallet återställer en enskild virtuell dator till den senaste tillgängliga programkonsekventa återställnings punkten. För en återställnings plan med en replikeringsgrupp återställs varje replikeringsgrupp till den gemensamma tillgängliga återställnings punkten.
    - Programkonsekventa återställnings punkter kan ligga efter i tiden och data kan gå förlorade.
- Vid redundansväxling från Azure till den lokala platsen stänger Site Recovery av virtuella Azure-datorer. När du genomför redundansväxlingen tar Site Recovery bort de återställda virtuella Azure-datorerna i Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/fysisk skydd/återställning

För att skydda och redundansväxla VMware-datorer och fysiska servrar från Azure till lokala måste du ha en infrastruktur för återställning efter fel och det finns ett antal krav.

- **Tillfällig processerver i Azure**: om du vill återställa från Azure konfigurerar du en virtuell Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
- **VPN-anslutning**: om du vill återställa behöver du en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
- **Separat huvud mål server**: som standard hanterar huvud mål servern som installerades med konfigurations servern på den lokala virtuella VMware-datorn återställning efter fel. Om du behöver återställa stora mängder trafik måste du konfigurera en separat lokal huvud mål server för det här ändamålet.
- **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från lokal plats till Azure.
    - Den här principen associeras automatiskt med konfigurations servern.
    - Du kan inte redigera den här principen.
    - Princip värden: återställnings tröskel – 15 minuter; Kvarhållning av återställnings punkt – 24 timmar; Frekvens för programkonsekventa ögonblicks bilder – 60 minuter.

Läs mer om VMware/fysisk skydd och återställning efter fel:
- [Granska](vmware-azure-reprotect.md#before-you-begin) ytterligare krav för skydd och återställning efter fel.
- [Distribuera](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) en Processerver i Azure.
- [Distribuera](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) en separat huvud mål server.

När du skyddar virtuella Azure-datorer till lokalt kan du ange att du vill växla tillbaka till den ursprungliga platsen eller till en annan plats.

- **Återställning av ursprunglig plats**: detta Miss lyckas tillbaka från Azure till samma lokala käll dator om den finns. I det här scenariot replikeras endast ändringar tillbaka till lokala platser.
- **Återställning av alternativ plats**: om den lokala datorn inte finns kan du växla tillbaka från Azure till en annan plats. När du skyddar den virtuella Azure-datorn till lokalt skapas den lokala datorn. Fullständig datareplikering sker från Azure till lokala enheter. -- [Granska](concepts-types-of-failback.md) kraven och begränsningarna för plats återställning efter fel.



## <a name="hyper-v-reprotectionfailback"></a>Skydd/återställning av Hyper-V

Återställa och återställa virtuella Hyper-V-datorer från Azure till lokal plats:

- Du kan bara återställa virtuella Hyper-V-datorer som replikeras med ett lagrings konto. Det finns inte stöd för återställning efter fel för virtuella Hyper-V-datorer som replikeras med hanterade diskar.
- Lokala Hyper-V-värdar (eller System Center VMM om det används) bör vara anslutna till Azure.
- Du kör en planerad återställning efter fel från Azure till en lokal plats.
- Inga enskilda komponenter måste konfigureras för återställning av Hyper-V VM.
- Under planerad redundansväxling kan du välja alternativ för att synkronisera data före återställning efter fel:
    - **Synkronisera data före redundans**: med det här alternativet minimeras stillestånds tiden för virtuella datorer när den synkroniserar datorer utan att stänga av dem.
        - Fas 1: tar en ögonblicks bild av den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
        - Fas 2: stänger av den virtuella Azure-datorn så att inga nya ändringar sker där. Den slutliga uppsättningen delta ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    - **Synkronisera endast data under redundans**: det här alternativet är snabbare eftersom vi förväntar dig att det mesta av disken har ändrats och därför inte utför beräkning av kontroll summa. Den utför en nedladdning av disken. Vi rekommenderar att du använder det här alternativet om den virtuella datorn har körts i Azure en stund (en månad eller mer) eller om den lokala virtuella datorn har tagits bort.

[Läs mer](hyper-v-azure-failback.md) om Hyper-V-skydd och återställning efter fel.

När du skyddar virtuella Azure-datorer till lokalt kan du ange att du vill växla tillbaka till den ursprungliga platsen eller till en annan plats.

- **Återställning av ursprunglig plats**: detta Miss lyckas tillbaka från Azure till samma lokala käll dator om den finns. I det här scenariot väljer du ett av de synkroniseringsalternativ som beskrivs i föregående procedur.
- **Återställning av alternativ plats**: om den lokala datorn inte finns kan du växla tillbaka från Azure till en annan plats. När du skyddar den virtuella Azure-datorn till lokalt skapas den lokala datorn. Med det här alternativet rekommenderar vi att du väljer alternativet för att synkronisera data före redundans
- [Granska](hyper-v-azure-failback.md) kraven och begränsningarna för plats återställning efter fel.


När du har växlat tillbaka till den lokala platsen aktiverar du **omvänd replikering** för att starta replikeringen av den virtuella datorn till Azure och slutför cykeln.




## <a name="next-steps"></a>Nästa steg
- Redundansväxla [vissa virtuella VMware-datorer](vmware-azure-tutorial-failover-failback.md)
- Redundansväxla [vissa virtuella Hyper-V-datorer](hyper-v-azure-failover-failback-tutorial.md).
- [Skapa](site-recovery-create-recovery-plans.md) en återställnings plan.
- Redundansväxla [virtuella datorer i en återställnings plan](site-recovery-failover.md).
- [Förbered för](vmware-azure-failback.md) VMware-skydd och återställning efter fel.
- Återställa [virtuella Hyper-V-datorer](hyper-v-azure-failback.md).
