---
title: Konfigurera redundans/återställning efter fel till en sekundär Hyper-V-plats med Azure Site Recovery
description: Lär dig hur du växlar över virtuella Hyper-V-datorer till den sekundära lokala platsen och växlar tillbaka till den primära platsen under haveri beredskap med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74082589"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Redundansväxla och återställa virtuella Hyper-V-datorer som replikeras till den sekundära lokala platsen

Tjänsten [Azure Site Recovery](site-recovery-overview.md) hanterar och dirigerar replikering, redundans och återställning efter fel för lokala datorer och virtuella datorer i Azure.

Den här artikeln beskriver hur du växlar över en virtuell Hyper-V-dator som hanteras i ett System Center Virtual Machine Manager-moln (VMM) till en sekundär VMM-plats. När du har redundansväxlat kan du återställa till den lokala platsen när den är tillgänglig. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Redundansväxla en virtuell Hyper-V-dator från ett primärt VMM-moln till ett sekundärt VMM-moln
> * Skydda från den sekundära platsen till den primära platsen och växla tillbaka
> * Du kan också starta replikering från primär till sekundär igen

## <a name="failover-and-failback"></a>Redundans och återställning efter fel

Redundans och återställning efter fel har tre steg:

1. **Redundansväxla till sekundär plats**: redundansväxla datorer från den primära platsen till den sekundära.
2. **Växla tillbaka från den sekundära platsen**: replikera virtuella datorer från sekundär till primär och kör en planerad redundansväxling för att återställa.
3. Efter den planerade redundansväxlingen kan du också starta replikeringen från den primära platsen till den sekundära igen.


## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att du har slutfört en [haveri beredskap](hyper-v-vmm-test-failover.md) för att kontrol lera att allt fungerar som förväntat.
- Slutför återställning efter fel genom att kontrol lera att de primära och sekundära VMM-servrarna är anslutna till Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Köra en redundansväxling från primär till sekundär

Du kan köra en regelbunden eller planerad redundansväxling för virtuella Hyper-V-datorer.

- Använd vanlig redundans för oväntade avbrott. När du kör den här redundansväxlingen skapar Site Recovery en virtuell dator på den sekundära platsen och skapar en säkerhets kopierings plats. Data förlust kan ske beroende på väntande data som inte har synkroniserats.
- En planerad redundansväxling kan användas för underhåll eller vid förväntat avbrott. Det här alternativet ger ingen data förlust. När en planerad redundansväxling utlöses stängs de virtuella käll datorerna. Osynkroniserade data synkroniseras och redundansväxlingen utlöses. 
- 
  Den här proceduren beskriver hur du kör en vanlig redundans.


1. I **Inställningar**  >  **replikerade objekt** klickar du på den virtuella datorn > **redundans**.
1. Välj **Stäng datorn innan du påbörjar redundans** om du vill att Site Recovery försöker stänga av virtuella käll datorer innan du utlöser redundansväxlingen. Site Recovery kommer också att försöka synkronisera lokala data som ännu inte har skickats till den sekundära platsen, innan redundansväxlingen utlöses. Observera att redundansväxlingen fortsätter även om avstängningen Miss lyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.
2. Du bör nu kunna se den virtuella datorn i det sekundära VMM-molnet.
3. När du har verifierat den virtuella datorn **genomför** du redundansväxlingen. Detta tar bort alla tillgängliga återställningspunkter.

> [!WARNING]
> **Avbryt inte en pågående redundansväxling**: Innan redundansen startas så stoppas replikeringen av den virtuella datorn. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.  


## <a name="reverse-replicate-and-failover"></a>Omvänd replikering och redundans

Starta replikeringen från den sekundära platsen till den primära platsen och växla tillbaka till den primära platsen. När de virtuella datorerna körs på den primära platsen igen kan du replikera dem till den sekundära platsen.  

 
1. Klicka på den virtuella datorn > Klicka på **omvänd replikering**.
2. När jobbet har slutförts klickar du på den virtuella datorn >i **redundansväxlingen**, kontrollerar redundansväxlingen (från sekundärt VMM-moln) och väljer käll-och mål platserna. 
4. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
5. I det primära VMM-molnet kontrollerar du att den virtuella datorn är tillgänglig.
6. Om du vill påbörja replikeringen av den primära virtuella datorn till den sekundära platsen igen klickar du på **omvänd replikering**.

## <a name="next-steps"></a>Nästa steg
[Granska steget](hyper-v-vmm-disaster-recovery.md) för att replikera virtuella Hyper-V-datorer till en sekundär plats.
