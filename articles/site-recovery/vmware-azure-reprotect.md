---
title: Skydda virtuella VMware-datorer till en lokal plats med Azure Site Recovery
description: Lär dig hur du skyddar virtuella VMware-datorer efter en redundansväxling till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mayg
ms.openlocfilehash: 6a11e3d0cb41383b44b76975ecbd1c2ae2825015
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89441501"
---
# <a name="reprotect-from-azure-to-on-premises"></a>Skydda datorer igen från Azure till lokalt

Efter [redundansväxlingen](site-recovery-failover.md) av lokala virtuella VMware-datorer eller fysiska servrar till Azure, är det första steget i att växla tillbaka till din lokala plats att skydda de virtuella Azure-datorer som skapades under redundansväxlingen. Den här artikeln beskriver hur du gör detta. 

## <a name="before-you-begin"></a>Innan du börjar

1. Följ stegen i [den här artikeln](vmware-azure-prepare-failback.md) för att förbereda för omskydd och återställning efter fel, inklusive att konfigurera en Processerver i Azure och en lokal huvud mål server, och konfigurera en plats-till-plats-VPN-anslutning, eller ExpressRoute privat peering, för återställning efter fel.
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
    - Virtuella VMware-datorer kan inte återställas till Hyper-V.
    - Virtuella datorer som har migrerats.
    - En virtuell dator som har flyttats till en annan resurs grupp.
    - En virtuell replik av Azure-dator som har tagits bort.
    - En replikerad virtuell Azure-dator som inte är skyddad (replikera till den lokala platsen).
10. [Granska typerna av återställning efter fel](concepts-types-of-failback.md) du kan använda – ursprunglig plats återställning och alternativ plats återställning.


## <a name="enable-reprotection"></a>Aktivera skydd

Aktivera replikering. Du kan återaktivera skyddet för vissa virtuella datorer eller en återställnings plan:

- Om du skyddar en återställnings plan måste du ange värden för varje skyddad dator.
- Om de virtuella datorerna tillhör en replikeringsgrupp för konsekvens för flera virtuella datorer kan de bara skyddas med en återställnings plan. Virtuella datorer i en replikeringsgrupp måste använda samma huvud mål Server

>[!NOTE]
>Mängden data som skickas från Azure till erstwhile-källan under återaktivering kan vara något mellan 0 byte och summan av disk storleken för alla skyddade datorer och kan inte beräknas.

### <a name="before-you-start"></a>Innan du börjar

- När en virtuell dator startar i Azure efter redundansväxlingen tar det lite tid för agenten att registrera sig på konfigurations servern (upp till 15 minuter). Under den här tiden kan du inte återaktivera skyddet och ett fel meddelande anger att agenten inte är installerad. Om detta händer väntar du några minuter och skyddar sedan igen.
- Om du vill återställa den virtuella Azure-datorn till en befintlig lokal virtuell dator, monterar du lokala VM-datalager med Läs-/Skriv behörighet på huvud mål serverns ESXi-värd.
- Om du vill växla tillbaka till en annan plats, till exempel om den lokala virtuella datorn inte finns, väljer du den lagrings enhet och det data lager som har kon figurer ATS för huvud mål servern. När du växlar tillbaka till den lokala platsen använder de virtuella VMware-datorerna i skydds planen för återställning efter fel samma data lager som huvud mål servern. En ny virtuell dator skapas sedan i vCenter.

Aktivera skydd på följande sätt:

1. Välj **valv**  >  **replikerade objekt**. Högerklicka på den virtuella datorn som har redundansväxlats och välj sedan **skydda igen**. Eller välj datorn från kommando knapparna och välj sedan **skydda igen**.
2. Kontrol lera att skydds riktningen för **Azure till lokal** riktning är vald.
3. I **huvud mål server** och **processerver** väljer du den lokala huvud mål servern och processervern.  
4. För **data lager** väljer du det data lager som du vill återställa diskarna till lokalt. Det här alternativet används när den lokala virtuella datorn tas bort och du måste skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste fortfarande ange ett värde.
5. Välj lagrings enhet.
6. Failback-principen väljs automatiskt.
7. Välj **OK** för att starta skyddet.

    ![Dialog rutan för att skydda igen](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Ett jobb börjar replikera den virtuella Azure-datorn till den lokala platsen. Du kan följa förloppet på fliken **Jobb**.
    - När återskydden lyckas övergår den virtuella datorn till ett skyddat tillstånd.
    - Den lokala virtuella datorn stängs av under återaktiveringen av skyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen.
    - Aktivera inte den lokala virtuella datorn efter att skyddet har slutförts.
   

## <a name="next-steps"></a>Nästa steg

- Läs [fel söknings artikeln](vmware-azure-troubleshoot-failback-reprotect.md)om du stöter på problem.
- När de virtuella Azure-datorerna är skyddade kan du [köra en återställning efter fel](vmware-azure-failback.md). Failback stänger av den virtuella Azure-datorn och startar den lokala virtuella datorn. Vänta lite stillestånds tid för programmet och välj en tid för återställning efter fel.


