---
title: Återställa virtuella VMware-datorer/fysiska servrar från Azure med Azure Site Recovery
description: Lär dig hur du växlar tillbaka till den lokala platsen efter en redundansväxling till Azure, under haveri beredskap för virtuella VMware-datorer och fysiska servrar till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004202"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Återställa virtuella VMware-datorer till en lokal plats

Den här artikeln beskriver hur du återställer virtuella Azure-datorer till en lokal plats, efter [redundansväxlingen](site-recovery-failover.md) av lokala virtuella datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). Efter återställning efter fel till lokalt aktiverar du replikering så att de lokala virtuella datorerna börjar replikera till Azure.

## <a name="before-you-start"></a>Innan du börjar

1. Lär dig mer om [VMware failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Kontrol lera att du har granskat och slutfört stegen för att [förbereda för återställning efter fel](vmware-azure-prepare-failback.md)och att alla nödvändiga komponenter har distribuerats. Komponenterna innehåller en processerver i Azure, en lokal huvud mål server och en VPN-anslutning från plats till plats (eller ExpressRoute privat peering) för återställning efter fel.
3. Se till att du har slutfört [kraven](vmware-azure-reprotect.md#before-you-begin) för omskydd och återställning efter fel och att du har [aktiverat](vmware-azure-reprotect.md#enable-reprotection) återställningen av virtuella Azure-datorer så att de replikeras från Azure till den lokala platsen. Virtuella datorer måste vara i ett replikerat tillstånd för att återställas.




## <a name="run-a-failover-to-fail-back"></a>Köra en redundansväxling för att växla tillbaka

1. Se till att virtuella Azure-datorer skyddas om och replikeras till den lokala platsen.
    - En virtuell dator måste ha minst en återställnings punkt för att kunna återställas.
    - Om du växlar tillbaka till en återställnings plan bör alla datorer i planen ha minst en återställnings punkt.
2. Välj den virtuella datorn i valvet > **replikerade objekt**. Högerklicka på den virtuella datorn > **oplanerad redundansväxling**.
3. I **Bekräfta redundans** kontrollerar du riktningen för redundans (från Azure).
4. Välj en återställningspunkt som ska användas för redundansen.
    - Vi rekommenderar att du använder den **senaste** återställnings punkten. Den app-konsekventa punkten ligger bakom den senaste tidpunkten och orsakar viss data förlust.
    - **Senaste** är en krasch-konsekvent återställnings punkt.
    - Med den **senaste versionen** växlar en virtuell dator till den senaste tillgängliga tidpunkten. Om du har en replikeringsgrupp för konsekvens för flera virtuella datorer i en återställnings plan växlar varje virtuell dator i gruppen över till den oberoende senaste tidpunkten.
    - Om du använder en programkonsekvent återställnings punkt går varje virtuell dator tillbaka till den senast tillgängliga punkten. Om en återställnings plan har en replikeringsgrupp återställs varje grupp till den gemensamma tillgängliga återställnings punkten.
5. Redundansväxlingen börjar. Site Recovery stänger de virtuella Azure-datorerna.
6. När redundansväxlingen är klar kontrollerar du att allt fungerar som förväntat. Kontrol lera att de virtuella Azure-datorerna är avstängda. 
7. När allting har verifierats högerklickar du på den virtuella datorn > **commit** för att slutföra redundansväxlingen. Commit tar bort den misslyckade virtuella Azure-datorn. 

> [!NOTE]
> Site Recovery inaktiverar VMware-verktygen under redundans för virtuella Windows-datorer. Vid failback för den virtuella Windows-datorn är VMware-verktygen aktiverat igen. 




## <a name="reprotect-from-on-premises-to-azure"></a>Återaktivera skydd från lokala datorer till Azure

När du har beställt failback tas de virtuella Azure-datorerna bort. Den virtuella datorn är tillbaka på den lokala platsen, men den är inte skyddad. Starta replikeringen av virtuella datorer till Azure igen enligt följande:

1. I valvet > **replikerade objekt** väljer du kunde inte återställa virtuella datorer och väljer sedan **skydda igen**.
2. Ange den processerver som används för att skicka data tillbaka till Azure.
3. Välj **OK** för att starta återskydds jobbet.

> [!NOTE]
> När en lokal virtuell dator startas tar det upp till 15 minuter för agenten att registrera sig på konfigurations servern igen. Under den här tiden Miss lyckas skydds åtgärden och returnerar ett fel meddelande om att agenten inte är installerad. Om detta inträffar väntar du några minuter och skyddar sedan igen.

## <a name="next-steps"></a>Nästa steg

När återskydds jobbet är klart replikeras den lokala virtuella datorn till Azure. Vid behov kan du [köra en annan redundansväxling](site-recovery-failover.md) till Azure.

