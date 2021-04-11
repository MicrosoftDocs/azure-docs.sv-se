---
title: Konfigurera redundans och återställning efter fel för fysiska servrar med Site Recovery
description: Lär dig hur du växlar över fysiska servrar till Azure och växlar tillbaka till den lokala platsen för haveri beredskap med Azure Site Recovery
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.openlocfilehash: 68427d264b6d2eea1a38e3b8bc5273409221c126
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579909"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Redundansväxla och återställa fysiska servrar som replikeras till Azure

I den här självstudien beskrivs hur du växlar över lokala fysiska servrar som replikeras till Azure med [Azure Site Recovery](site-recovery-overview.md). När du har redundansväxlats kan du växla tillbaka från Azure till den lokala platsen när den är tillgänglig.

## <a name="before-you-start"></a>Innan du börjar

- [Läs mer](failover-failback-overview.md) om redundansväxlingen i haveri beredskap.
- Om du vill redundansväxla flera datorer kan du [lära dig](recovery-plan-overview.md) hur du samlar ihop datorer i en återställnings plan.
- Innan du gör en fullständig redundansväxling kan du köra en [haveri beredskap](site-recovery-test-failover-to-azure.md) för att se till att allt fungerar som förväntat.
- Följ [de här anvisningarna](site-recovery-failover.md#prepare-to-connect-after-failover) för att förbereda för att ansluta till virtuella Azure-datorer efter en redundansväxling.



## <a name="run-a-failover"></a>Köra en redundansväxling

### <a name="verify-server-properties"></a>Verifiera Server egenskaper

Kontrol lera Server egenskaperna och se till att de uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines) för virtuella Azure-datorer.

1. I **skyddade objekt** klickar du på **replikerade objekt** och väljer datorn.
2. I fönstret **replikerat objekt** finns en sammanfattning av dator information, hälso status och de senaste tillgängliga återställnings punkterna. Klicka på **Egenskaper** för att se mer information.
3. I **beräkning och nätverk** kan du ändra Azure-namn, resurs grupp, mål storlek, [tillgänglighets uppsättning](../virtual-machines/windows/tutorial-availability-sets.md)och hanterade disk inställningar
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **diskar** kan du se information om datorns operativ system och data diskar.

### <a name="fail-over-to-azure"></a>Redundansväxla till Azure

1. I **Inställningar**  >  **replikerade objekt** klickar du på datorn > **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   - **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. De ger det lägsta målet för återställningspunkten eftersom Azure VM skapas efter att redundansen har fått alla data som replikerades till Site Recovery när redundansen utlöstes.
   - **Senast bearbetad**: det här alternativet växlar över datorn till den senaste återställnings punkten som bearbetas av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
   - **Senaste program – konsekvent**: det här alternativet växlar över datorn till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery.
   - **Anpassad**: Ange en återställningspunkt.

3. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery ska försöka stänga käll datorn innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
4. Om du har förberett att ansluta till den virtuella Azure-datorn, ansluter du för att kontrollera den efter redundansen.
5. När du är klar väljer du **Genomför** för redundansen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> Avbryt inte en pågående redundansväxling. Innan redundansväxlingen börjar stoppas dator replikeringen. Om du avbryter redundansväxlingen stoppas den, men datorn kommer inte att replikeras igen.
> För fysiska servrar kan ytterligare redundansväxling ta cirka åtta till tio minuter att slutföra.

## <a name="automate-actions-during-failover"></a>Automatisera åtgärder under redundans

Du kanske vill automatisera åtgärder under redundansväxlingen. Om du vill göra detta kan du använda skript eller Azure Automation-runbooks i återställnings planer.

- [Lär dig mer](site-recovery-create-recovery-plans.md) om att skapa och anpassa återställnings planer, inklusive att lägga till skript.
- [Lär dig](site-recovery-runbook-automation.md) om verifieringen att lägga till Azure Automation runbooks i återställnings planer.

## <a name="configure-settings-after-failover"></a>Konfigurera inställningar efter redundans

Efter redundansväxlingen måste du [Konfigurera Azure-inställningar](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) för att ansluta till de replikerade virtuella Azure-datorerna. Dessutom kan du konfigurera [interna och offentliga](site-recovery-failover.md#set-up-ip-addressing) IP-adresser.

## <a name="prepare-for-reprotection-and-failback"></a>Förbered för skydd och återställning efter fel

När du har växlat till Azure igen kan du skydda virtuella Azure-datorer genom att replikera dem till den lokala platsen. Efter att de har repliker ATS kan du återställa dem till lokalt genom att köra en redundansväxling från Azure till din lokala plats.

1. Fysiska servrar som replikeras till Azure med hjälp av Site Recovery kan bara återställas som virtuella VMware-datorer. Du behöver en VMware-infrastruktur för att återställa igen. Följ stegen i [den här artikeln](vmware-azure-prepare-failback.md) för att förbereda för omskydd och återställning efter fel, inklusive att konfigurera en Processerver i Azure och en lokal huvud mål server, och konfigurera en plats-till-plats-VPN-anslutning, eller ExpressRoute privat peering, för återställning efter fel.
2. Kontrol lera att den lokala konfigurations servern körs och är ansluten till Azure. Vid redundansväxling till Azure kanske den lokala platsen inte är tillgänglig och konfigurations servern kanske inte är tillgänglig eller avstängd. Under återställning efter fel måste den virtuella datorn finnas i konfigurations serverns databas. Annars Miss lyckas failback.
3. Ta bort alla ögonblicks bilder på den lokala huvud mål servern. Skyddet fungerar inte om det finns ögonblicks bilder.  Ögonblicks bilderna på den virtuella datorn slås samman automatiskt under ett återskydds jobb.
4. Om du skyddar virtuella datorer som samlats in i en replikeringsgrupp för konsekvens för flera virtuella datorer måste du se till att alla har samma operativ system (Windows eller Linux) och att huvud mål servern som du distribuerar har samma typ av operativ system. Alla virtuella datorer i en replikeringsgrupp måste använda samma huvud mål server.
5. Öppna [de portar som krävs](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) för återställning efter fel.
6. Se till att vCenter Server är ansluten före återställning efter fel. Annars Miss lyckas det att koppla bort diskar och koppla tillbaka dem till den virtuella datorn.
7. Om en vCenter-Server hanterar de virtuella datorer som du kommer att återställa till, ser du till att du har de behörigheter som krävs. Om du utför en skrivskyddad vCenter-identifiering och skyddar virtuella datorer, slutförs skyddet och redundansväxlingen fungerar. Men under återställningen Miss lyckas redundansväxlingen eftersom data lagret inte kan identifieras och inte visas under återskydd. För att lösa det här problemet kan du uppdatera vCenter-autentiseringsuppgifterna med ett [lämpligt konto/behörigheter](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)och sedan försöka utföra jobbet igen. 
8. Om du använde en mall för att skapa dina virtuella datorer måste du kontrol lera att varje virtuell dator har ett eget UUID för diskarna. Om den lokala virtuella datorns UUID är i konflikt med UUID: n för huvud mål servern, eftersom båda har skapats från samma mall, Miss lyckas återaktivering. Distribuera från en annan mall.
9. Om du växlar tillbaka till en alternativ vCenter Server måste du kontrol lera att den nya vCenter Server och huvud mål servern har identifierats. Vanligt vis om de inte är data lager som inte är tillgängliga eller inte visas i **återaktivera skydd**.
10. Kontrol lera följande scenarier där du inte kan återställa:
    - Om du använder antingen ESXi 5,5 kostnads fri utgåva eller vSphere 6 hypervisor Free Edition. Uppgradera till en annan version.
    - Om du har en fysisk server för Windows Server 2008 R2 SP1.
    - Virtuella datorer som har migrerats.
    - En virtuell dator som har flyttats till en annan resurs grupp.
    - En virtuell replik av Azure-dator som har tagits bort.
    - En replikerad virtuell Azure-dator som inte är skyddad (replikera till den lokala platsen).
10. [Granska typerna av återställning efter fel](concepts-types-of-failback.md) du kan använda – ursprunglig plats återställning och alternativ plats återställning.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Skydda virtuella Azure-datorer till en annan plats

Den här proceduren förutsätter att den lokala virtuella datorn inte är tillgänglig.

1. I valvet > **Inställningar**  >  **replikerade objekt** högerklickar du på den dator som har redundansväxlats > **skydda igen**.
2. I **Återaktivera skydd** kontrollerar du att **Azure till lokal plats** är valt.
3. Ange den lokala huvudmålservern och processervern.
4. I **Datalager** välj du det huvudmåldatalager som du vill återställa diskarna till lokalt.
       - Använd det här alternativet om den lokala virtuella datorn har tagits bort eller inte finns och du måste skapa nya diskar.
       - Den här inställningen ignoreras om diskarna redan finns, men du måste ange ett värde.
5. Välj kvarhållningsenhetens huvudmål. Failback-principen väljs automatiskt.
6. Klicka på **OK** ska börja återaktivera skyddet. Ett jobb börjar replikera den virtuella Azure-datorn till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.

> [!NOTE]
> Om du vill återställa den virtuella Azure-datorn till en befintlig lokal VM, monterar du den lokala virtuella datorns datalager med läs-/skrivåtkomst på huvudmålserverns ESXi-värd.


## <a name="fail-back-from-azure"></a>Redundansväxla från Azure

Kör en redundans enligt följande:

1. På sidan **Replikerade objekt** högerklickar du på datorn > **Oplanerad redundans**.
2. I **Bekräfta redundans** kontrollerar du att redundansriktningen är från Azure.
3. Välj den återställnings punkt som du vill använda för redundansväxlingen.
    - Vi rekommenderar att du använder den **senaste** återställnings punkten. Den app-konsekventa punkten ligger bakom den senaste tidpunkten och orsakar viss data förlust.
    - **Senaste** är en krasch-konsekvent återställnings punkt.
    - När redundansen körs stänger Site Recovery av de virtuella Azure-datorerna och startar den lokala virtuella datorn. Det kommer att bli vissa avbrott, så du bör välja en lämplig tidpunkt.
4. Högerklicka på datorn och klicka på **Genomför**. Detta utlöser ett jobb som tar bort de virtuella Azure-datorerna.
5. Kontrollera att de virtuella Azure-datorerna har stängts av som förväntat.


## <a name="reprotect-on-premises-machines-to-azure"></a>Återaktivera skyddet av lokala datorer till Azure

Dina data bör nu finnas lokalt igen, men de replikeras inte till Azure. Du kan påbörja replikeringen till Azure igen på följande sätt:

1. I valvet > **Inställningar** >**Replikerade objekt** väljer du de återställda virtuella datorerna och klickar på **Återaktivera skydd**.
2. Välj den processerver som används för att skicka replikerade data till Azure och klicka på **OK**.


## <a name="next-steps"></a>Nästa steg

När återskydds jobbet är klart replikeras den lokala virtuella datorn till Azure. Vid behov kan du [köra en annan redundansväxling](site-recovery-failover.md) till Azure.
