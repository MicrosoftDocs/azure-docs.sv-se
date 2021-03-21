---
title: Köra en redundansväxling vid haveri beredskap med Azure Site Recovery
description: Så här växlar du över virtuella datorer/fysiska servrar till Azure med Azure Site Recovery.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 6737f64773f91ede1631d42cd7f28c7d961c0454
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368629"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Köra en redundans från en lokal plats till Azure

Den här artikeln beskriver hur du växlar över lokala datorer till Azure i [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Innan du börjar

- [Läs mer](failover-failback-overview.md) om redundansväxlingen i haveri beredskap.
- Om du vill redundansväxla flera datorer kan du [lära dig](recovery-plan-overview.md) hur du samlar ihop datorer i en återställnings plan.
- Innan du gör en fullständig redundansväxling kan du köra en [haveri beredskap](site-recovery-test-failover-to-azure.md) för att se till att allt fungerar som förväntat.

## <a name="prepare-to-connect-after-failover"></a>Förbered för att ansluta efter redundans

För att se till att du kan ansluta till de virtuella Azure-datorer som skapas efter redundansväxlingen, så är det ett antal saker du behöver göra lokalt före redundansväxlingen.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Förbered lokalt för anslutning efter redundans

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans, finns det ett antal saker du behöver göra lokalt före redundansväxlingen.

**Efter redundans** | **Plats** | **Åtgärder**
--- | --- | ---
**Virtuell Azure-dator som kör Windows** | Lokal dator före redundans | För att få åtkomst till den virtuella Azure-datorn via Internet aktiverar du RDP och kontrollerar att TCP-och UDP-regler har lagts till för **offentlig** och att RDP tillåts för alla profiler i **Windows-brandväggen**  >  **tillåtna appar**.<br/><br/> För att komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning aktiverar du RDP på datorn och ser till att RDP tillåts i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.<br/><br/> <br/><br/> Ta bort alla statiska permanenta vägar och WinHTTP-proxy. Kontrol lera att SAN-principen för operativ systemet är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Se till att inga Windows-uppdateringar väntar på den virtuella datorn när du aktiverar en redundansväxling. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringen är klar.
**Virtuell Azure-dator som kör Linux** | Lokal dator före redundans | Kontrol lera att Secure Shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid system start.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.


## <a name="run-a-failover"></a>Köra en redundansväxling

Den här proceduren beskriver hur du kör en redundansväxling för en [återställnings plan](site-recovery-create-recovery-plans.md). Om du vill köra en redundansväxling för en enskild virtuell dator följer du anvisningarna för en [virtuell VMware-dator](vmware-azure-tutorial-failover-failback.md), en [fysisk server](physical-to-azure-failover-failback.md)eller en [virtuell Hyper-V-dator](hyper-v-azure-failover-failback-tutorial.md).


Kör återställnings planens redundans på följande sätt:

1. I Site Recovery-valvet väljer du **återställnings planer**  >  *recoveryplan_name*.
2. Klicka på **redundans**.

    ![Skärm bild från Azure Site Recovery som visar ADRP-fönstret med redundans valt från menyn mer.](./media/site-recovery-failover/Failover.png)

3.   >  Lämna standard **riktningen i redundansväxlingen** om du replikerar till Azure.
4. I **redundans** väljer du en **återställnings punkt** att redundansväxla.

    - **Senaste**: Använd den senaste punkten. Detta bearbetar alla data som har skickats till Site Recovery-tjänsten och skapar en återställnings punkt för varje dator. Det här alternativet ger det lägsta återställnings punkt målet, eftersom den virtuella datorn som skapades efter redundansväxlingen har alla data som har repliker ATS till Site Recovery När redundansväxlingen utlöstes.
    Observera att det inte går att utföra mer logg bearbetning när käll regionen slutar fungera. Det innebär att du måste redundansväxla till den senaste bearbetade återställnings punkten. Mer information finns i nästa steg.
   - **Senast bearbetade**: Använd det här alternativet om du vill redundansväxla virtuella datorer till den senaste återställnings punkten som redan bearbetats av Site Recovery. Du kan se den senast bearbetade återställnings punkten i den **senaste återställnings** punkten för den virtuella datorn. Det här alternativet ger en låg RTO eftersom ingen tid ägnas åt att bearbeta obearbetade data
   - **Senaste appen – konsekvent**: Använd det här alternativet för att redundansväxla virtuella datorer till den senaste programkonsekventa återställnings punkten som bearbetats av Site Recovery.
   - Den **senaste Multi-VM-bearbetningen**: med det här alternativet kan virtuella datorer som ingår i en replikeringsgrupp redundansväxla till den senaste vanliga återställnings punkten med flera virtuella datorer. Andra virtuella datorer växlar över till den senaste bearbetade återställnings punkten. Det här alternativet är endast för återställnings planer som har minst en virtuell dator med konsekvens för flera virtuella datorer aktiverade.
   - **Senaste program med flera virtuella datorer – konsekvent**: med det här alternativet kan virtuella datorer som ingår i en replikeringsgrupp redundansväxla till den senaste vanliga programkonsekventa återställnings punkten med flera virtuella datorer. Andra virtuella datorer redundansväxlas till den senaste programkonsekventa återställnings punkten. Endast för återställnings planer som har minst en virtuell dator med konsekvens för flera virtuella datorer aktiverade.
   - **Anpassad**: inte tillgänglig för återställnings planer. Det här alternativet gäller endast för redundans för enskilda virtuella datorer.

5. Välj **Stäng datorn innan du påbörjar redundans** om du vill Site Recovery stänga av virtuella käll datorer innan du påbörjar redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen misslyckas.  

    > [!NOTE]
    > Om du växlar över virtuella Hyper-V-datorer försöker avstängning synkronisera och replikera lokala data som ännu inte har skickats till tjänsten innan redundansväxlingen utlöses. 

6. Följ förloppet för redundans på **jobb** sidan. Även om fel inträffar körs återställnings planen tills den är klar.
7. Efter redundansväxlingen loggar du in på den virtuella datorn för att verifiera den. 
8. Använd **ändrings återställnings punkt** om du vill växla till en annan återställnings punkt som ska användas för redundansväxlingen.
9. När du är klar kan du genomföra redundansväxlingen. Åtgärden **commit** tar bort alla återställnings punkter som är tillgängliga med tjänsten. Alternativet **ändra återställnings punkt** är inte längre tillgängligt.

## <a name="run-a-planned-failover-hyper-v"></a>Köra en planerad redundansväxling (Hyper-V)

Du kan köra en planerad redundansväxling för virtuella Hyper-V-datorer.

- En planerad redundans är inget alternativ för redundans av data förlust.
- När en planerad redundansväxling utlöses stängs först de virtuella käll datorerna, den senaste informationen synkroniseras och sedan utlöses redundansväxlingen.
- Du kör en planerad redundansväxling med alternativet **Planerad redundans** . Den körs på liknande sätt som en vanlig redundansväxling.
 
## <a name="track-failovers"></a>Spåra redundans

Det finns ett antal jobb som är kopplade till redundans.

![Skärm bild av sidan jobb som visar en lista med jobb med grupp 1: Start (1) expanderad i kolumnen namn. Raden för SQLServer-jobbet är markerad.](./media/site-recovery-failover/FailoverJob.png)

- **Krav kontroll**: säkerställer att alla villkor som krävs för redundans är uppfyllda.
- **Redundans**: bearbetar data så att en virtuell Azure-dator kan skapas från den. Om du har valt den **senaste** återställnings punkten skapas en återställnings punkt utifrån de data som har skickats till tjänsten.
- **Start**: skapar en virtuell Azure-dator med hjälp av de data som bearbetades i föregående steg.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: innan redundansväxlingen startas stoppades replikeringen för den virtuella datorn. Om du avbryter ett pågående jobb stoppas redundansväxlingen, men den virtuella datorn kommer inte att börja replikera. Det går inte att starta replikeringen igen.


### <a name="extra-failover-time"></a>Extra växlings tid

I vissa fall kräver redundansväxlingen mellanliggande steg som vanligt vis tar cirka åtta till 10 minuter att slutföra. Detta är de datorer som påverkas av detta ytterligare steg/tid:

* Virtuella VMware-datorer som kör en mobilitets tjänst version som är äldre än 9,8.
* Fysiska servrar och virtuella Hyper-V-datorer som skyddas som fysiska servrar.
* Virtuella VMware Linux-datorer.
* Virtuella VMware-datorer där driv rutinerna inte finns som start driv rutiner:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* Virtuella VMware-datorer som inte har DHCP aktiverat, oavsett om de använder DHCP eller statiska IP-adresser.


## <a name="automate-actions-during-failover"></a>Automatisera åtgärder under redundans

Du kanske vill automatisera åtgärder under redundansväxlingen. Om du vill göra detta kan du använda skript eller Azure Automation-runbooks i återställnings planer.

- [Lär dig mer](site-recovery-create-recovery-plans.md) om att skapa och anpassa återställnings planer, inklusive att lägga till skript.
- [Lär dig mer](site-recovery-runbook-automation.md) om att lägga till Azure Automation runbooks i återställnings planer.


## <a name="configure-settings-after-failover"></a>Konfigurera inställningar efter redundans

### <a name="retain-drive-letters-after-failover"></a>Behåll enhets beteckningar efter redundans

Site Recovery hanterar kvarhållning av enhets beteckningar. Om du exkluderar diskar under VM-replikeringen kan du [läsa ett exempel](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) på hur det fungerar.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Förbereda Azure för att ansluta efter redundansväxlingen

Om du vill ansluta till virtuella Azure-datorer som skapas efter en redundansväxling med RDP eller SSH följer du de krav som sammanfattas i tabellen.

**Redundans** | **Plats** | **Åtgärder**
--- | --- | ---
**Virtuell Azure-dator som kör Windows** | Virtuell Azure-dator efter redundans |  [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrol lera **startdiagnostik** för att verifiera en skärm bild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och läser igenom de här [fel söknings tipsen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuell Azure-dator som kör Linux** | Virtuell Azure-dator efter redundans | Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Kontrol lera **startdiagnostiken** för en skärm bild av den virtuella datorn.<br/><br/>

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="set-up-ip-addressing"></a>Konfigurera IP-adressering

- **Interna IP-adresser**: om du vill ange den interna IP-adressen för en virtuell Azure-dator efter redundansväxlingen har du ett par alternativ:
    - Behåll samma IP-adress: du kan använda samma IP-adress på den virtuella Azure-datorn som den som du allokerat till den lokala datorn.
    - Använd en annan IP-adress: du kan använda en annan IP-adress för den virtuella Azure-datorn.
    - [Läs mer](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) om hur du konfigurerar interna IP-adresser.
- **Externa IP-adresser**: du kan behålla offentliga IP-adresser vid redundans. Virtuella Azure-datorer som har skapats som en del av redundansväxlingen måste tilldelas en offentlig Azure-IP-adress som är tillgänglig i Azure-regionen. Du kan tilldela en offentlig IP-adress antingen manuellt eller genom att automatisera processen med en återställnings plan. [Läs mer](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Nästa steg

När du har växlat över måste du skydda igen för att starta replikeringen av virtuella Azure-datorer till den lokala platsen. När replikeringen är igång kan du växla tillbaka lokalt när du är klar.

- [Läs mer](failover-failback-overview.md#reprotectionfailback) om skydd och återställning efter fel.
- [Förbered](vmware-azure-reprotect.md) för VMware-återskydd och återställning efter fel.
- [Växla tillbaka](hyper-v-azure-failback.md) Virtuella Hyper-V-datorer.
- [Läs mer om](physical-to-azure-failover-failback.md) processen för redundans och återställning efter fel för fysiska servrar.