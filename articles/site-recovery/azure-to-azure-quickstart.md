---
title: Konfigurera haveri beredskap för virtuella Azure-datorer till en sekundär region med Azure Site Recovery
description: Konfigurera snabbt haveri beredskap till en annan Azure-region för en virtuell Azure-dator med hjälp av tjänsten Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135701"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Snabb start: Konfigurera haveri beredskap till en sekundär Azure-region för en virtuell Azure-dator

[Azure Site Recoverys](site-recovery-overview.md) tjänsten bidrar till din strategi för affärs kontinuitet och haveri beredskap (BCDR) genom att hålla dina företags program online under planerade och oplanerade drifts avbrott. Site Recovery hanterar och dirigerar haveri beredskap för lokala datorer och virtuella Azure-datorer (VM), inklusive replikering, redundans och återställning.

I den här snabb starten beskrivs hur du konfigurerar haveri beredskap för en virtuell Azure-dator genom att replikera den till en sekundär Azure-region. I allmänhet används standardinställningar för att aktivera replikering.

## <a name="prerequisites"></a>Förutsättningar

Du behöver en Azure-prenumeration och en virtuell dator för att kunna slutföra den här självstudien.

- Om du inte har ett Azure-konto med en aktiv prenumeration kan du [skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En virtuell dator med minst 1 GB RAM-minne rekommenderas. [Läs mer](../virtual-machines/windows/quick-create-portal.md) om hur du skapar en virtuell dator.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Aktivera replikering för virtuella Azure-datorer

Följande steg aktiverar VM-replikering till en sekundär plats.

1. På sidan Azure Portal väljer du   >  en virtuell dator som ska replikeras från menyn Start **virtuella datorer** .
1. I **åtgärder** väljer du **haveri beredskap**.
1.   >  Välj mål region i avsnittet grundläggande **mål**.
1. Om du vill visa inställningarna för replikering väljer du **Granska + starta replikering**. Om du behöver ändra standardvärden väljer du **Avancerade inställningar**.
1. Starta jobbet som aktiverar VM-replikering genom att välja **starta replikering**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Aktivera replikering.":::

## <a name="verify-settings"></a>Kontrollera inställningarna

När replikeringen är klar kan du kontrol lera replikeringsstatus, ändra replikeringsinställningar och testa distributionen.

1. På Azure Portal-menyn väljer du **virtuella datorer** och väljer den virtuella dator som du replikerade.
1. I **åtgärder** väljer du **haveri beredskap**.
1. Om du vill visa replikeringsinformation från **översikten** väljer du **Essentials**. Mer information visas i **tillståndet hälsa och status**, **redundans** och **infrastruktur** översikt.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replikeringsstatus.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill stoppa replikeringen av den virtuella datorn i den primära regionen måste du inaktivera replikering:

- Källans replikeringsinställningar rensas automatiskt.
- Site Recovery tillägget som har installerats på den virtuella datorn under replikeringen tas inte bort.
- Site Recovery faktureringen för den virtuella datorn stoppas.

Utför följande steg för att inaktivera replikering:

1. På Azure Portal-menyn väljer du **virtuella datorer** och väljer den virtuella dator som du replikerade.
1. I **åtgärder** väljer du **haveri beredskap**.
1. Välj **inaktivera replikering** i **översikten**.
1. Om du vill avinstallera Site Recovery-tillägget går du till **inställnings** tillägg för den virtuella datorn  >  .

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Inaktivera replikering.":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten replikerar du en enda virtuell dator till en sekundär region. Konfigurera sedan replikering för flera virtuella Azure-datorer.

> [!div class="nextstepaction"]
> [Konfigurera katastrof återställning för virtuella Azure-datorer](azure-to-azure-tutorial-enable-replication.md)
