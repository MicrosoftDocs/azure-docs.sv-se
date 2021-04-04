---
title: Skydda virtuella Azure-datorer till ett privat moln med Azure VMware-lösningar med Azure Site Recovery
description: Lär dig hur du skyddar virtuella datorer i Azure VMware-lösningen efter en redundansväxling till Azure med Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 80ff2f3f3d5fdcf61770889dcdaaf075941b90ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814594"
---
# <a name="reprotect-from-azure-to-azure-vmware-solution-private-cloud"></a>Skydda från Azure till Azures privata moln med VMware-lösning

Efter [redundansväxlingen](avs-tutorial-failover.md) av virtuella datorer i Azure VMware-lösningen till Azure är det första steget för att återställa Azure VMware-lösningens privata moln att återställa de virtuella Azure-datorer som skapades under redundansväxlingen. Den här artikeln beskriver hur du gör detta. 

## <a name="before-you-begin"></a>Innan du börjar

1. Följ stegen i [den här artikeln](vmware-azure-prepare-failback.md) för att förbereda för omskydd och återställning efter fel, inklusive konfiguration av en Processerver i Azure och en Azure VMware-lösning privat moln huvud mål server och konfigurera en plats-till-plats-VPN-anslutning, eller ExpressRoute privat peering, för återställning efter fel.
2. Se till att konfigurations servern för det privata molnet i Azure VMware-lösningen körs och är ansluten till Azure. Under återställning efter fel måste den virtuella datorn finnas i konfigurations serverns databas. Annars Miss lyckas failback.
3. Ta bort alla ögonblicks bilder på Azure VMware-lösningen privat moln huvud mål server. Skyddet fungerar inte om det finns ögonblicks bilder.  Ögonblicks bilderna på den virtuella datorn slås samman automatiskt under ett återskydds jobb.
4. Om du skyddar virtuella datorer som samlats in i en replikeringsgrupp för konsekvens för flera virtuella datorer måste du se till att alla har samma operativ system (Windows eller Linux) och att huvud mål servern som du distribuerar har samma typ av operativ system. Alla virtuella datorer i en replikeringsgrupp måste använda samma huvud mål server.
5. Öppna [de portar som krävs](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) för återställning efter fel.
6. Se till att vCenter Server är ansluten före återställning efter fel. Annars Miss lyckas det att koppla bort diskar och koppla tillbaka dem till den virtuella datorn.
7. Om en vCenter-Server hanterar de virtuella datorer som du kommer att återställa till, ser du till att du har de behörigheter som krävs. Om du utför en skrivskyddad vCenter-identifiering och skyddar virtuella datorer, slutförs skyddet och redundansväxlingen fungerar. Men under återställningen Miss lyckas redundansväxlingen eftersom data lagret inte kan identifieras och inte visas under återskydd. För att lösa det här problemet kan du uppdatera vCenter-autentiseringsuppgifterna med ett [lämpligt konto/behörigheter](avs-tutorial-prepare-avs.md#prepare-an-account-for-automatic-discovery)och sedan försöka utföra jobbet igen. 
8. Om du använde en mall för att skapa dina virtuella datorer måste du kontrol lera att varje virtuell dator har ett eget UUID för diskarna. Om den virtuella datorn i Azure VMware-lösningen är i konflikt med UUID: n för huvud mål servern eftersom båda har skapats från samma mall, Miss lyckas återskydd. Distribuera från en annan mall.
9. Om du växlar tillbaka till en alternativ vCenter Server måste du kontrol lera att den nya vCenter Server och huvud mål servern har identifierats. Vanligt vis om de inte är data lager som inte är tillgängliga eller inte visas i **återaktivera skydd**.
10. Kontrol lera följande scenarier där du inte kan återställa:
    - Om du använder antingen ESXi 5,5 kostnads fri utgåva eller vSphere 6 hypervisor Free Edition. Uppgradera till en annan version.
    - Om du har en fysisk server för Windows Server 2008 R2 SP1.
    - Virtuella VMware-datorer kan inte återställas till Hyper-V.
    - Virtuella datorer som har migrerats.
    - En virtuell dator som har flyttats till en annan resurs grupp.
    - En virtuell replik av Azure-dator som har tagits bort.
    - En replik av en virtuell Azure-dator som inte är skyddad.
10. [Granska typerna av återställning efter fel](concepts-types-of-failback.md) du kan använda – ursprunglig plats återställning och alternativ plats återställning.


## <a name="enable-reprotection"></a>Aktivera skydd

Aktivera replikering. Du kan återaktivera skyddet för vissa virtuella datorer eller en återställnings plan:

- Om du skyddar en återställnings plan måste du ange värden för varje skyddad dator.
- Om de virtuella datorerna tillhör en replikeringsgrupp för konsekvens för flera virtuella datorer kan de bara skyddas med en återställnings plan. Virtuella datorer i en replikeringsgrupp måste använda samma huvud mål Server

>[!NOTE]
>Mängden data som skickas från Azure till erstwhile-källan under återaktivering kan vara något mellan 0 byte och summan av disk storleken för alla skyddade datorer och kan inte beräknas.

### <a name="before-you-start"></a>Innan du börjar

- När en virtuell dator startar i Azure efter redundansväxlingen tar det lite tid för agenten att registrera sig på konfigurations servern (upp till 15 minuter). Under den här tiden kan du inte återaktivera skyddet och ett fel meddelande anger att agenten inte är installerad. Om detta händer väntar du några minuter och skyddar sedan igen.
- Om du vill återställa den virtuella Azure-datorn till en befintlig virtuell Azure VMware-lösning, monterar du VM-Datadata med Läs-/skriv åtkomst på huvud mål serverns ESXi-värd.
- Om du vill växla tillbaka till en annan plats, till exempel om den virtuella Azure VMware-lösningen inte finns, väljer du den lagrings enhet och det data lager som har kon figurer ATS för huvud mål servern. När du växlar tillbaka till Azure VMware-lösningen privat moln använder de virtuella datorerna i skydds planen för återställning efter fel samma data lager som huvud mål servern. En ny virtuell dator skapas sedan i vCenter.

Aktivera skydd på följande sätt:

1. Välj **valv**  >  **replikerade objekt**. Högerklicka på den virtuella datorn som har redundansväxlats och välj sedan **skydda igen**. Eller välj datorn från kommando knapparna och välj sedan **skydda igen**.
2. Kontrol lera att skydds riktningen för **Azure till lokal** riktning är vald.
3. I **huvud mål server** och **processerver** väljer du den lokala huvud mål servern och processervern.  
4. För **data lager** väljer du det data lager som du vill återställa diskarna till i Azure VMware-lösningen. Det här alternativet används när den virtuella Azure VMware-lösningen tas bort och du måste skapa nya diskar. Det här alternativet ignoreras om diskarna redan finns. Du måste fortfarande ange ett värde.
5. Välj lagrings enhet.
6. Failback-principen väljs automatiskt.
7. Välj **OK** för att starta skyddet.

    ![Dialog rutan för att skydda igen](./media/vmware-azure-reprotect/reprotectinputs.png)
    
8. Ett jobb börjar replikera den virtuella Azure-datorn till ett privat moln i Azure VMware-lösningen. Du kan följa förloppet på fliken **Jobb**.
    - När återskydden lyckas övergår den virtuella datorn till ett skyddat tillstånd.
    - Den virtuella Azure VMware-lösningen är inaktive rad under återskyddet. Detta hjälper att säkerställa datakonsekvens vid replikeringen.
    - Aktivera inte den virtuella Azure VMware-lösningen när skyddet har slutförts.
   

## <a name="next-steps"></a>Nästa steg

- Läs [fel söknings artikeln](vmware-azure-troubleshoot-failback-reprotect.md)om du stöter på problem.
- När de virtuella Azure-datorerna är skyddade kan du [köra en återställning efter fel](avs-tutorial-failback.md). Failback stänger av den virtuella Azure-datorn och startar den virtuella Azure VMware-lösningen. Vänta lite stillestånds tid för programmet och välj en tid för återställning efter fel.


