---
title: Replikera virtuella Azure-datorer som kör Lagringsdirigering med Azure Site Recovery
description: Lär dig hur du replikerar virtuella Azure-datorer som kör Lagringsdirigering med Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424834"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikera virtuella Azure-datorer som kör Lagringsdirigering till en annan region

I den här artikeln beskrivs hur du aktiverar haveri beredskap för virtuella Azure-datorer som kör lagrings dirigering.

>[!NOTE]
>Endast kraschbaserade återställnings punkter stöds för lagrings Dirigerings kluster.
>

[Lagrings dirigering (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) är ett Programdefinierat lagrings utrymme som gör det möjligt att skapa [gäst kluster](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) i Azure.  Ett gäst kluster i Microsoft Azure är ett kluster för växling vid fel som består av virtuella IaaS-datorer. Den gör det möjligt för värdar för virtuella datorer att redundansväxla över gäst kluster, vilket ger service avtal med hög tillgänglighet för program, än en enda virtuell Azure-dator kan tillhandahålla. Det är användbart i scenarier där en virtuell dator är värd för ett kritiskt program som SQL eller skalbar fil server.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Haveri beredskap med lagrings dirigering

I ett typiskt scenario kan du ha virtuella datorer i Azure för högre återhämtning av ditt program som skalbar fil server. Även om detta kan ge ditt program högre tillgänglighet, vill du skydda dessa program med hjälp av Site Recovery för ett region nivå haveri. Site Recovery replikerar data från en region till en annan Azure-region och tar upp klustret i haveri beredskaps region i händelse av redundans.

I diagrammet nedan visas ett Azure VM-redundanskluster med två noder som använder lagrings dirigering.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Två virtuella Azure-datorer i ett Windows-redundanskluster och varje virtuell dator har två eller flera data diskar.
- S2D synkroniserar data på data disken och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenteras som en klusterdelad volym (CSV) till redundansklustret.
- Kluster för växling vid fel använder CSV för data enheterna.

**Överväganden vid haveri beredskap**

1. När du ställer in [moln vittne](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) för klustret ska du behålla vittnet i haveri beredskaps regionen.
2. Om du växlar över de virtuella datorerna till under nätet i den DR-region som skiljer sig från käll regionen måste kluster-IP-adressen ändras efter redundansväxlingen.  Om du vill ändra IP-adress för klustret måste du använda [skriptet Site Recovery återställnings plan.](./site-recovery-runbook-automation.md)</br>
[Exempel skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) för att köra kommando i virtuell dator med anpassat skript tillägg 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Aktivera Site Recovery för S2D-kluster:

1. I Recovery Services-valvet klickar du på "+ replikera"
1. Markera alla noder i klustret och gör dem till en del av en [konsekvens grupp för flera virtuella datorer](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Välj replikeringsprincip med Application konsekvens av * (det finns bara stöd för krasch konsekvens)
1. Aktivera replikering

   ![Skärm bild som visar var du konfigurerar replikeringsinställningar.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Gå till replikerade objekt så kan du se både statusen för den virtuella datorn.
3. Båda de virtuella datorerna skyddas och visas också som en del av konsekvens gruppen för flera virtuella datorer.

   ![Skärm bild som visar att de virtuella datorerna är skyddade och en del av en konsekvens grupp för flera virtuella datorer.](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Skapa en återställnings plan
En återställnings plan stöder sekvenseringen av olika nivåer i ett program med flera nivåer under en redundansväxling. Sekvenseringen bidrar till att upprätthålla programmets konsekvens. När du skapar en återställnings plan för ett webb program med flera nivåer slutför du stegen som beskrivs i [skapa en återställnings plan med hjälp av Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för redundans

1.  Skapa en återställnings plan genom att lägga till de virtuella datorerna.
2.  Klicka på Anpassa för att gruppera de virtuella datorerna. Som standard ingår alla virtuella datorer i "grupp 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Lägga till skript i återställnings planen
För att dina program ska fungera korrekt kan du behöva utföra några åtgärder på de virtuella Azure-datorerna efter redundansväxlingen eller under ett redundanstest. Du kan automatisera vissa åtgärder efter redundans. Här lägger vi till exempel till belastningsutjämnare och ändrar kluster-IP.


### <a name="failover-of-the-virtual-machines"></a>Redundansväxling av de virtuella datorerna 
Både noderna för de virtuella datorerna måste redundansväxla med Site Recovery [återställnings plan](./site-recovery-create-recovery-plans.md) 

![storagespacesdirect skydd](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Köra ett redundanstest
1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du har skapat.
3.  Välj **Testa redundans**.
4.  Starta processen för redundanstest genom att välja återställnings punkten och det virtuella Azure-nätverket.
5.  Utföra verifieringar när den sekundära miljön är igång.
6.  När verifieringen är klar kan du rensa redundansväxlingen genom att välja **rensning** av redundanstest.

Mer information finns i [testa redundans till Azure i Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Köra en redundansväxling

1.  I Azure Portal väljer du Recovery Services valvet.
2.  Välj den återställnings plan som du skapade för SAP-program.
3.  Välj **Redundans**.
4.  Starta redundansväxlingen genom att välja återställnings punkten.

Mer information finns i [redundansväxlingen i Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Nästa steg

[Läs mer](./azure-to-azure-tutorial-failover-failback.md) om att köra återställning efter fel.
