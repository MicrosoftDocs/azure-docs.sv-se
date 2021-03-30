---
title: Undanta virtuella VMware-diskar från haveri beredskap till Azure med Azure Site Recovery
description: Så här undantar du virtuella VMware-diskar från replikering till Azure med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129875"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Exkludera diskar från VMware VM-replikering till Azure

Den här artikeln beskriver hur du undantar diskar när du replikerar virtuella VMware-datorer till Azure för haveri beredskap. Du kanske vill undanta diskar från replikeringen av flera orsaker:

- Se till att det inte går att replikera viktiga data som är sammanslagna på den uteslutna disken.
- Optimera bandbredden för förbrukad replikering eller resurser på mål sidan genom att utesluta diskar som du inte behöver replikera.
- Spara lagrings-och nätverks resurser genom att inte replikera data som du inte behöver.

Innan du utesluter diskar från replikering:

- [Läs mer](exclude-disks-replication.md) om att utesluta diskar.
- Granska [typiska undantags scenarier](exclude-disks-replication.md#typical-scenarios) och [exempel](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) som visar hur exkludera en disk påverkar replikering, redundans och återställning efter fel.

## <a name="before-you-start"></a>Innan du börjar

 Observera följande innan du börjar:

- **Replikering**: som standard replikeras alla diskar på en dator.
- **Disktyp**: endast standard diskar kan undantas från replikering. Du kan inte undanta operativsystemdiskar eller dynamiska diskar.
- **Mobilitets tjänst**: om du vill undanta en disk från replikeringen måste du installera mobilitets tjänsten manuellt på datorn innan du aktiverar replikering. Du kan inte använda push-installation eftersom den här metoden endast installerar mobilitets tjänsten på en virtuell dator när replikering har Aktiver ATS.  
- **Lägg till/ta bort/exkludera diskar**: när du har aktiverat replikering kan du inte lägga till/ta bort/exkludera diskar för replikering. Om du vill lägga till/ta bort eller exkludera diskar måste du inaktivera skyddet för datorn och sedan aktivera det igen.
- **Redundans**: efter redundansväxlingen, om misslyckade över appar behöver exkluderade diskar för att fungera, måste du skapa diskarna manuellt. Du kan också integrera Azure Automation i en återställnings plan för att skapa disken under redundansväxlingen av datorn.
- **Återställning efter fel – Windows**: när du växlar tillbaka till din lokala plats efter en redundansväxling återställs inte Windows-diskar som du skapar manuellt i Azure. Om du till exempel växlar över tre diskar och skapar två diskar direkt på virtuella Azure-datorer kommer bara de tre diskar som växlades över att återställas.
- **Failback – Linux**: för återställning efter fel för Linux-datorer går det inte att återställa diskar som du skapar manuellt i Azure. Om du till exempel växlar över tre diskar och skapar två diskar direkt på virtuella Azure-datorer, kommer alla fem att återställas. Du kan inte undanta diskar som har skapats manuellt i failback, eller i återställningen av virtuella datorer.



## <a name="exclude-disks-from-replication"></a>Undanta diskar från replikering

1. När du [aktiverar replikering](./hyper-v-azure-tutorial.md) för en virtuell VMware-dator efter att du har valt de virtuella datorer som du vill replikera ,  >    >  granskar du kolumnen **diskar som ska replikeras** i kolumnen Aktivera egenskaper för replikering på sidan **Konfigurera egenskaper** för replikering. Som standard är alla diskar markerade för replikering.
2. Om du inte vill replikera en speciell disk går du **till diskar för att replikera** rensa urvalet för diskar som du vill undanta. 

    ![Undanta diskar från replikering](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Nästa steg
När du har konfigurerat och fått igång distributionen kan du [läsa mer](failover-failback-overview.md) om olika typer av redundansväxlingar.
