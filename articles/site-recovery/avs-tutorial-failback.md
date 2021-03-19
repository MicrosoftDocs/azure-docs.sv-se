---
title: Återställa Azure VMware-lösningen VMsfrom Azure med Azure Site Recovery
description: Lär dig hur du återställer det privata molnet i Azure VMware-lösningen efter en redundansväxling till Azure under haveri beredskap.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814588"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Redundansväxla virtuella datorer till Azure VMware-lösningen privat moln

I den här artikeln beskrivs hur du återställer virtuella Azure-datorer till ett privat Azure VMware-lösnings privata moln, efter [redundansväxlingen](avs-tutorial-failover.md) av virtuella datorer i Azure VMware-lösningen till azure med [Azure Site Recovery](site-recovery-overview.md). Efter failback aktiverar du replikering så att de virtuella datorerna i Azure VMware-lösningen börjar replikera till Azure.

## <a name="before-you-start"></a>Innan du börjar

1. Lär dig mer om [VMware failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Kontrol lera att du har granskat och slutfört stegen för att [förbereda för återställning efter fel](vmware-azure-prepare-failback.md)och att alla nödvändiga komponenter har distribuerats. Komponenterna innehåller en processerver i Azure, en huvud mål server och en VPN-anslutning från plats till plats (eller ExpressRoute privat peering) för återställning efter fel.
3. Se till att du har slutfört [kraven](avs-tutorial-reprotect.md#before-you-begin) för omskydd och återställning efter fel och att du har [aktiverat](avs-tutorial-reprotect.md#enable-reprotection) återställningen av virtuella Azure-datorer så att de replikeras från Azure till Azure VMware-lösningen privat moln. Virtuella datorer måste vara i ett replikerat tillstånd för att återställas.




## <a name="run-a-failover-to-fail-back"></a>Köra en redundansväxling för att växla tillbaka

1. Se till att virtuella Azure-datorer skyddas om och replikeras till Azure VMware-lösningens privata moln.
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




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Återaktivera skyddet från Azure VMware-lösningen till Azure

När du har beställt failback tas de virtuella Azure-datorerna bort. Den virtuella datorn är tillbaka i det privata molnet Azure VMware-lösning, men den är inte skyddad. Starta replikeringen av virtuella datorer till Azure igen enligt följande:

1. I valvet > **replikerade objekt** väljer du kunde inte återställa virtuella datorer och väljer sedan **skydda igen**.
2. Ange den processerver som används för att skicka data tillbaka till Azure.
3. Välj **OK** för att starta återskydds jobbet.

> [!NOTE]
> När en virtuell Azure VMware-lösning startar tar det upp till 15 minuter för agenten att registrera sig på konfigurations servern igen. Under den här tiden Miss lyckas skydds åtgärden och returnerar ett fel meddelande om att agenten inte är installerad. Om detta inträffar väntar du några minuter och skyddar sedan igen.

## <a name="next-steps"></a>Nästa steg

När återskydds jobbet är klart replikeras den virtuella Azure VMware-lösningen till Azure. Vid behov kan du [köra en annan redundansväxling](avs-tutorial-failover.md) till Azure.

