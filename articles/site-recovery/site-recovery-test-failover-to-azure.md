---
title: Köra ett redundanstest (haveri beredskap) till Azure i Azure Site Recovery
description: Lär dig mer om att köra ett redundanstest från en lokal plats till Azure med hjälp av tjänsten Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 815a35c167bbcd3ac03dfdaaf6d699e58a791f33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369394"
---
# <a name="run-a-test-failover-disaster-recovery-drill-to-azure"></a>Köra ett redundanstest (haveri beredskap) till Azure 


Den här artikeln beskriver hur du kör en haveri beredskap på Azure med hjälp av en Site Recovery redundanstest.  

Du kör ett redundanstest för att verifiera din strategi för replikering och haveri beredskap, utan data förlust eller stillestånds tid. En redundanstest påverkar inte pågående replikering eller produktions miljön. Du kan köra ett redundanstest på en enskild virtuell dator (VM) eller en [återställnings plan](site-recovery-create-recovery-plans.md) som innehåller flera virtuella datorer.


## <a name="run-a-test-failover"></a>Köra ett redundanstest
Den här proceduren beskriver hur du kör ett redundanstest för en återställnings plan. Om du vill köra ett redundanstest för en enskild virtuell dator följer du stegen som beskrivs [här](tutorial-dr-drill-azure.md#run-a-test-failover-for-a-single-vm)

![Skärm bild av sidan testa redundans i Azure Portal.](./media/site-recovery-test-failover-to-azure/TestFailover.png)


1. I Site Recovery i Azure Portal klickar du på **återställnings planer**  >  *recoveryplan_name*  >  **testa redundans**.
2. Välj en **återställnings punkt** som redundansen ska utföras på. Du kan välja något av följande alternativ:
    - **Senast bearbetad**: det här alternativet växlar över alla virtuella datorer i planen till den senaste återställnings punkten som bearbetats av Site Recovery. Om du vill se den senaste återställnings punkten för en viss virtuell dator kontrollerar du de **senaste återställnings punkterna** i VM-inställningarna. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste program – konsekvent**: det här alternativet växlar över alla virtuella datorer i planen till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery. Om du vill se den senaste återställnings punkten för en viss virtuell dator kontrollerar du de **senaste återställnings punkterna** i VM-inställningarna.
    - **Senaste**: det här alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställnings punkt för varje virtuell dator innan den växlar över till den. Det här alternativet ger lägsta återställnings punkt mål (återställnings punkt mål), eftersom den virtuella datorn som skapades efter redundansväxlingen har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.
    - **Senaste bearbetade multi-VM**: det här alternativet är tillgängligt för återställnings planer med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer med inställningen aktive rad redundans till den senaste vanliga återställnings punkten med flera virtuella datorer. Andra virtuella datorer växlar över till den senaste bearbetade återställnings punkten.  
    - **Senaste app med flera virtuella datorer – konsekvent**: det här alternativet är tillgängligt för återställnings planer med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga programkonsekventa multi-VM-programkonsekventa återställnings punkt. Andra virtuella datorer växlar över till den senaste programkonsekventa återställnings punkten.
    - **Anpassad**: Använd det här alternativet om du vill redundansväxla en specifik virtuell dator till en viss återställnings punkt.
3. Välj ett virtuellt Azure-nätverk där de virtuella test datorerna ska skapas.

    - Site Recovery försöker skapa virtuella test datorer i ett undernät med samma namn och samma IP-adress som anges i **beräknings-och nätverks** inställningarna för den virtuella datorn.
    - Om ett undernät med samma namn inte är tillgängligt i det virtuella Azure-nätverket som används för redundanstest, skapas den virtuella test datorn i det första under nätet i alfabetisk ordning.
    - Om samma IP-adress inte är tillgänglig i under nätet får den virtuella datorn en annan tillgänglig IP-adress i under nätet. [Läs mer](#create-a-network-for-test-failover).
4. Om du växlar över till Azure och data kryptering har Aktiver ATS i **krypterings nyckel** väljer du det certifikat som utfärdades när du aktiverade kryptering under installationen av providern. Du kan ignorera det här steget om kryptering inte är aktiverat.
5. Spåra förlopp för redundansväxling på fliken **jobb** . Du bör kunna se test replik datorn i Azure Portal.
6. Om du vill initiera en RDP-anslutning till den virtuella Azure-datorn måste du [lägga till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) i nätverks gränssnittet för den misslyckade virtuella datorn.
7. När allt fungerar som förväntat klickar du på **rensning** av redundanstest. Detta tar bort de virtuella datorer som skapades under redundanstest.
8. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.


![Skärm bild av fliken testa jobb för redundans.](./media/site-recovery-test-failover-to-azure/TestFailoverJob.png)

När ett redundanstest utlöses inträffar följande:

1. **Krav**: en krav kontroll körs för att säkerställa att alla villkor som krävs för redundans är uppfyllda.
2. **Redundans**: redundansväxlingen och för beredda data, så att en virtuell Azure-dator kan skapas från den.
3. **Senaste**: om du har valt den senaste återställnings punkten skapas en återställnings punkt utifrån de data som har skickats till tjänsten.
4. **Starta**: det här steget skapar en virtuell Azure-dator med hjälp av de data som bearbetades i föregående steg.

### <a name="failover-timing"></a>Tids inställning för redundans

I följande fall kräver redundans ett extra mellanliggande steg som vanligt vis tar cirka 8 till 10 minuter att slutföra:

* Virtuella VMware-datorer som kör en version av mobilitets tjänsten som är äldre än 9,8
* Fysiska servrar
* Virtuella VMware Linux-datorer
* Virtuella Hyper-V-datorer som fysiska servrar
* VMware VM där följande driv rutiner inte startar driv rutiner:
    * storvsc
    * VMBus
    * storflt
    * intelide
    * ATAPI
* Virtuella VMware-datorer som inte har DHCP aktiverat, oavsett om de använder DHCP eller statiska IP-adresser.

I alla andra fall krävs inget mellanliggande steg, och redundansväxlingen tar betydligt kortare tid.


## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).


## <a name="test-failover-to-a-production-network-in-the-recovery-site"></a>Testa redundans till ett produktions nätverk på återställnings platsen

Även om vi rekommenderar att du använder ett test nätverk separat från produktions nätverket, bör du tänka på följande om du vill testa en haveri beredskap i produktions nätverket:

- Kontrol lera att den primära virtuella datorn är avstängd när du kör redundanstest. Annars kommer det att finnas två virtuella datorer med samma identitet, som körs i samma nätverk på samma gång. Detta kan leda till oväntade konsekvenser.
- Ändringar av virtuella datorer som har skapats för redundanstest går förlorade när du rensar redundansväxlingen. Dessa ändringar replikeras inte tillbaka till den primära virtuella datorn.
- Testning i produktions miljön leder till en stillestånds tid för ditt produktions program. Användarna bör inte använda appar som körs på virtuella datorer när redundanstestning pågår.  



## <a name="prepare-active-directory-and-dns"></a>Förbereda Active Directory och DNS

Om du vill köra ett redundanstest för program testning behöver du en kopia av din produktions Active Directory miljö i din test miljö. Läs mer [om testning av redundans för Active Directory](site-recovery-active-directory.md#test-failover-considerations) .

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling

Om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans följer du de krav som sammanfattas i tabellen.

**Redundans** | **Plats** | **Åtgärder**
--- | --- | ---
**Virtuell Azure-dator som kör Windows** | Lokal dator före redundans | För att få åtkomst till den virtuella Azure-datorn via Internet aktiverar du RDP och kontrollerar att TCP-och UDP-regler har lagts till för **offentlig** och att RDP tillåts för alla profiler i **Windows-brandväggen**  >  **tillåtna appar**.<br/><br/> För att komma åt den virtuella Azure-datorn via en plats-till-plats-anslutning aktiverar du RDP på datorn och ser till att RDP tillåts i **Windows-brandväggen**  ->  **tillåtna appar och funktioner** för **domän nätverk och privata** nätverk.<br/><br/>  Kontrol lera att SAN-principen för operativ systemet är inställd på **OnlineAll**. [Läs mer](https://support.microsoft.com/kb/3031135).<br/><br/> Se till att inga Windows-uppdateringar väntar på den virtuella datorn när du aktiverar en redundansväxling. Windows Update kan starta när du växlar över och du kan inte logga in på den virtuella datorn förrän uppdateringen är klar.
**Virtuell Azure-dator som kör Windows** | Virtuell Azure-dator efter redundans |  [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till RDP-porten.<br/><br/> Kontrol lera **startdiagnostik** för att verifiera en skärm bild av den virtuella datorn.<br/><br/> Om du inte kan ansluta kontrollerar du att den virtuella datorn körs och läser igenom de här [fel söknings tipsen](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Virtuell Azure-dator som kör Linux** | Lokal dator före redundans | Kontrol lera att Secure Shell-tjänsten på den virtuella datorn är inställd på att starta automatiskt vid system start.<br/><br/> Kontrollera att brandväggsreglerna tillåter en SSH-anslutning till tjänsten.
**Virtuell Azure-dator som kör Linux** | Virtuell Azure-dator efter redundans | Reglerna för nätverks säkerhets gruppen på den misslyckade virtuella datorn (och det Azure-undernät som den är ansluten till) måste tillåta inkommande anslutningar till SSH-porten.<br/><br/> [Lägg till en offentlig IP-adress](/archive/blogs/srinathv/how-to-add-a-public-ip-address-to-azure-vm-for-vm-failed-over-using-asr) för den virtuella datorn.<br/><br/> Kontrol lera **startdiagnostiken** för en skärm bild av den virtuella datorn.<br/><br/>

Följ stegen som beskrivs [här](site-recovery-failover-to-azure-troubleshoot.md) för att felsöka eventuella anslutningsproblem efter redundans.

## <a name="next-steps"></a>Nästa steg
När du har slutfört en haveri beredskaps granskning kan du läsa mer om andra typer av [redundans](site-recovery-failover.md).