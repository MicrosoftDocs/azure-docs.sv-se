---
title: Aktivera replikering för en tillagd virtuell Azure-disk i Azure Site Recovery
description: I den här artikeln beskrivs hur du aktiverar replikering för en disk som har lagts till i en virtuell Azure-dator som är aktive rad för haveri beredskap med Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75973799"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Aktivera replikering för en disk som lagts till i en virtuell Azure-dator


Den här artikeln beskriver hur du aktiverar replikering för data diskar som läggs till i en virtuell Azure-dator som redan har Aktiver ATS för haveri beredskap till en annan Azure-region med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Att aktivera replikering för en disk som du lägger till i en virtuell dator stöds för virtuella Azure-datorer med hanterade diskar.

När du lägger till en ny disk till en virtuell Azure-dator som replikeras till en annan Azure-region inträffar följande:

-   Hälso tillståndet för den virtuella datorn visar en varning och en anteckning i portalen informerar dig om att en eller flera diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den inledande replikeringen av disken.
-   Om du väljer att inte aktivera replikering för disken kan du välja att ignorera varningen.

![Ny disk har lagts till](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du redan har konfigurerat en katastrof återställning för den virtuella dator som du lägger till disken i. Om du inte har gjort det följer du [själv studie kursen om att återställa Azure till Azure](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Aktivera replikering för en tillagd disk

Gör så här om du vill aktivera replikering för en tillagd disk:

1. I valvet > **replikerade objekt** klickar du på den virtuella dator som du har lagt till disken i.
2. Klicka på **diskar** och välj sedan den datadisk som du vill aktivera replikering för (diskarna har statusen **inte skyddad** ).
3.  I **disk information** klickar du på **Aktivera replikering**.

    ![Aktivera replikering för tillagd disk](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

När jobbet aktivera replikering har körts och den inledande replikeringen har slutförts, tas varningen för replikeringen bort från disk problemet.



## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra ett redundanstest.
