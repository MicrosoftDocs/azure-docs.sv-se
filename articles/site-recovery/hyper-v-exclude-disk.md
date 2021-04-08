---
title: Exkludera återställning av virtuella Hyper-V-diskar från haveri beredskap till Azure med Azure Site Recovery
description: Så här undantar du virtuella Hyper-V-diskar från replikering till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86131249"
---
# <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

Den här artikeln beskriver hur du undantar diskar när du replikerar virtuella Hyper-V-datorer till Azure. Du kanske vill undanta diskar från replikeringen av flera orsaker:

- Se till att det inte går att replikera viktiga data som är sammanslagna på den uteslutna disken.
- Optimera bandbredden för förbrukad replikering eller resurser på mål sidan genom att utesluta diskar som du inte behöver replikera.
- Spara lagrings-och nätverks resurser genom att inte replikera data som du inte behöver.

Innan du utesluter diskar från replikering:

- [Läs mer](exclude-disks-replication.md) om att utesluta diskar.
- Granska [typiska undantags scenarier](exclude-disks-replication.md#typical-scenarios) och [exempel](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) som visar hur exkludera en disk påverkar replikering, redundans och återställning efter fel.

## <a name="before-you-start"></a>Innan du börjar

Observera följande innan du börjar:

- **Replikering**: som standard replikeras alla diskar på en dator.
- **Disktyp**:
    - Du kan undanta grundläggande diskar från replikering.
    - Du kan inte undanta operativsystemdiskar.
    - Vi rekommenderar att du inte undantar dynamiska diskar. Site Recovery kan inte identifiera vilken virtuell hård disk som är Basic eller Dynamic på den virtuella gäst datorn.  Om du inte undantar alla beroende dynamiska volym diskar blir den skyddade dynamiska disken en felaktig disk på en misslyckad virtuell dator och data på disken är inte tillgängliga.
- **Lägg till/ta bort/exkludera diskar**: när du har aktiverat replikering kan du inte lägga till/ta bort/exkludera diskar för replikering. Om du vill lägga till/ta bort eller undanta en disk måste du inaktivera skyddet för den virtuella datorn och sedan aktivera det igen.
- **Redundans**: efter redundansväxlingen, om misslyckade över appar behöver utesluta diskar för att kunna fungera måste du skapa dessa diskar manuellt. Du kan också integrera Azure Automation i en återställnings plan för att skapa disken under redundansväxlingen av datorn.
- **Återställning efter fel**: när du växlar tillbaka till din lokala plats efter en redundansväxling återställs inte diskar som du skapade manuellt i Azure. Om du till exempel växlar över tre diskar och skapar två diskar direkt på en virtuell Azure-dator, återställs endast tre diskar som växlades över. Du kan inte ta med diskar som har skapats manuellt i failback eller i omvänd replikering av virtuella datorer.

## <a name="exclude-disks"></a>Uteslut diskar

1. Om du vill utesluta diskar när du [aktiverar replikering](./hyper-v-azure-tutorial.md) för en virtuell Hyper-V-dator, när du har valt de virtuella datorer som du vill replikera,  >    >  granskar du kolumnen **diskar som ska replikeras** i kolumnen Aktivera egenskaper för replikering på sidan Aktivera **Egenskaper** för replikering. Som standard är alla diskar markerade för replikering.
2. Om du inte vill replikera en speciell disk går du **till diskar för att replikera** rensa urvalet för diskar som du vill undanta. 

    ![Undanta diskar från replikering](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.
