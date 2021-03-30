---
title: Konfigurera redundans av virtuella Hyper-V-datorer till Azure i Azure Site Recovery
description: Lär dig hur du växlar över virtuella Hyper-V-datorer till Azure med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b46a2ea12a697afde8223cc3595365c1286512c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86132466"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Redundansväxla virtuella Hyper-V-datorer till Azure

I den här självstudien beskrivs hur du växlar över virtuella Hyper-V-datorer till Azure med [Azure Site Recovery](site-recovery-overview.md). När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera att de virtuella Hyper-V-egenskaperna är förenliga med Azure-kraven.
> * Redundansväxla vissa virtuella datorer till Azure.


Detta är den femte självstudien i en serie. Självstudien förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna.    

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)
3. Konfigurera katastrofåterställning för [virtuella Hyper-V datorer](./hyper-v-azure-tutorial.md) eller för [virtuella Hyper-V-datorer som hanterats i System Center VMM-moln](./hyper-v-vmm-azure-tutorial.md)
4. [Köra ett programåterställningstest](tutorial-dr-drill-azure.md)

[Lär dig mer om](failover-failback-overview.md#types-of-failover) olika typer av redundans. Läs [den här artikeln](site-recovery-failover.md)om du vill redundansväxla flera virtuella datorer i en återställnings plan.

## <a name="prepare-for-failover"></a>Förbereda för redundansväxling 
Kontrollera att det inte finns några ögonblicksbilder på den virtuella datorn och att den lokala virtuella datorn stängs av under återställningen. Det hjälper till att säkerställa datakonsekvens vid replikering. Aktivera inte en lokal virtuell dator under återställning efter fel. 

Redundansväxling och återställning efter fel har tre stadier:

1. **Redundansväxling till Azure**: Redundansväxling av virtuella Hyper-V-datorer från den lokala platsen till Azure.
2. **Återställning efter fel till lokal plats**: Redundansväxling av virtuella Azure-datorer till den lokala platsen när den lokala platsen är tillgänglig. Data börjar synkroniseras från Azure till den lokala platsen och när det slutförts blir alla virtuella datorer lokala.  
3. **Omvänd replikering av lokala virtuella datorer**: Efter återställning till lokal plats kan du utföra en omvänd replikering av lokala virtuella datorer för att börja replikera dem till Azure.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan redundansväxlingen utförs kontrollerar du VM-egenskaperna och ser till att den virtuella datorn uppfyller [Azure-kraven](hyper-v-azure-support-matrix.md#replicated-vms).

I **Skyddade objekt** klickar du på **Replikerade objekt** > VM.

1. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.

1. I **Beräkning och nätverk** kan du ändra Azure-namnet, resursgrupp, målstorlek, [tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md) och hanterade diskinställningar.

1. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.

1. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="fail-over-to-azure"></a>Redundansväxla till Azure

1. I **Inställningar**  >  **replikerade objekt** klickar du på den virtuella datorn > **redundans**.
2. I **Redundans** väljer du den **senaste** återställningspunkten. 
3. Välj **Stäng datorn innan du påbörjar redundans**. Site Recovery försöker att stänga av den virtuella källdatorn innan redundansen utlöses. Redundansväxlingen fortsätter även om avstängningen misslyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
4. När du har verifierat redundansväxlingen väljer du **Genomför**. Detta raderar alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundans**: Om du avbryter en pågående redundansväxling så stoppas den, men den virtuella datorn kommer inte att replikera igen.

## <a name="connect-to-failed-over-vm"></a>Anslut till misslyckad virtuell dator

1. Om du vill ansluta till virtuella Azure-datorer efter en redundansväxling med hjälp av Remote Desktop Protocol (RDP) och SSH (Secure Shell) [kontrollerar du att kraven har uppfyllts](failover-failback-overview.md#connect-to-azure-after-failover).
2. Efter redundansväxlingen går du till den virtuella datorn och validerar genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **ändrings återställnings punkt** om du vill använda en annan återställnings punkt efter redundansväxlingen. När du har bekräftat redundansväxlingen i nästa steg kommer det här alternativet inte längre att vara tillgängligt.
4. När du har verifierat väljer du **genomför** för att slutföra återställnings punkten för den virtuella datorn efter redundansväxlingen.
5. När du har bekräftat raderas alla andra tillgängliga återställnings punkter. I det här steget slutförs redundansväxlingen.

>[!TIP]
> Om det uppstår anslutnings problem efter redundansväxlingen följer du [fel söknings guiden](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Nästa steg

Skydda virtuella Azure-datorer igen efter redundansväxlingen, så att de replikeras från Azure till lokala enheter. Efter det att de virtuella datorerna har återskyddats och repliker ATS till den lokala platsen går du tillbaka från Azure när du är klar.
