---
title: Självstudie för att redundansväxla virtuella Azure-datorer till en sekundär region för haveri beredskap med Azure Site Recovery.
description: Självstudie för att lära dig hur du växlar över och skyddar virtuella Azure-datorer som replikeras till en sekundär Azure-region för haveri beredskap med tjänsten Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392868"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Självstudie: redundansväxla virtuella Azure-datorer till en sekundär region

Lär dig hur du växlar över virtuella Azure-datorer som är aktiverade för haveri beredskap med [Azure Site Recovery](site-recovery-overview.md)till en sekundär Azure-region. Efter redundansväxlingen kan du skydda virtuella datorer i mål regionen så att de replikeras tillbaka till den primära regionen. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Kontrollera krav
> * Verifiera VM-inställningar
> * Utför en redundansväxling till den sekundära regionen
> * Påbörja replikeringen av den virtuella datorn till den primära regionen.


> [!NOTE]
> Den här självstudien visar hur du växlar över virtuella datorer med minimala steg. Om du vill köra en redundansväxling med fullständiga inställningar kan du läsa mer om Azure VM- [nätverk](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md)och [fel sökning](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien bör du ha:

1. Konfigurera replikering för en eller flera virtuella Azure-datorer. Om du inte har gjort det går du [igenom den första självstudien](azure-to-azure-tutorial-enable-replication.md) i den här serien.
2. Vi rekommenderar att du [kör en katastrof återställnings granskning](azure-to-azure-tutorial-dr-drill.md) för replikerade virtuella datorer. Genom att köra en detalj granskning innan du kör en fullständig redundans ser du till att allt fungerar som förväntat, utan att påverka produktions miljön. 


## <a name="verify-the-vm-settings"></a>Verifiera VM-inställningarna

1. Välj den virtuella datorn i valvet > **replikerade objekt**.

    ![Alternativ för att öppna VM-egenskaperna på översikts Sidan](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. På sidan **Översikt över** virtuell dator kontrollerar du att den virtuella datorn är skyddad och felfri innan du kör en redundansväxling.
    ![För att verifiera VM-egenskaper och tillstånd](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Innan du växlar över kontrollerar du att:
    - Den virtuella datorn kör ett [Windows](azure-to-azure-support-matrix.md#windows) -eller [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) -operativsystem som stöds.
    - Den virtuella datorn uppfyller kraven på [beräknings](azure-to-azure-support-matrix.md#replicated-machines---compute-settings)-, [lagrings](azure-to-azure-support-matrix.md#replicated-machines---storage)-och [nätverks](azure-to-azure-support-matrix.md#replicated-machines---networking) krav.

## <a name="run-a-failover"></a>Köra en redundansväxling


1. På sidan VM- **Översikt** väljer du **redundans**.

    ![Knappen redundans för det replikerade objektet](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. I **redundans** väljer du en återställnings punkt. Den virtuella Azure-datorn i mål regionen skapas med hjälp av data från den här återställnings punkten.
  
   - **Senast bearbetade**: använder den senaste återställnings punkten som bearbetats av Site Recovery. Tidsstämpeln visas. Ingen tid för bearbetning av data, så det ger ett litet återställnings tids mål (RTO).
   -  **Senaste**: bearbetar alla data som skickas till Site Recovery, så att du kan skapa en återställnings punkt för varje virtuell dator innan du växlar över till den. Tillhandahåller lägsta återställnings punkt mål (återställnings punkt) eftersom alla data replikeras till Site Recovery När redundansväxlingen utlöses.
   - **Senaste appen – konsekvent**: det här alternativet växlar över virtuella datorer till den senaste programkonsekventa återställnings punkten. Tidsstämpeln visas.
   - **Anpassad**: redundansväxla till viss återställnings punkt. Anpassad är endast tillgängligt om du växlar över en enskild virtuell dator och inte använder en återställnings plan.

    > [!NOTE]
    > Om du har lagt till en disk till en virtuell dator efter att du har aktiverat replikering visar replikerings platser diskar som är tillgängliga för återställning. Till exempel visas en plats för replikering som skapats innan du lade till en andra disk som "1 av 2 diskar".

4. Välj **Stäng datorn innan du påbörjar redundans** om du vill Site Recovery försöka stänga av de virtuella käll datorerna innan du påbörjar redundans. Avstängning bidrar till att förhindra data förlust. Redundansväxlingen fortsätter även om avstängningen misslyckas. 

    ![Sidan Inställningar för redundans](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Starta redundansväxlingen genom att välja **OK**.
4. Övervaka redundansväxlingen i meddelanden.

    ![Avisering om ](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![ slutfört förlopps meddelande](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Efter redundansväxlingen visas den virtuella Azure-datorn som skapades i mål regionen i **Virtual Machines**. Kontrol lera att den virtuella datorn körs och att den passar. Om du vill använda en annan återställnings punkt för den virtuella datorn väljer du **ändra återställnings punkt** på sidan **Essentials** .
6. När du är nöjd med den misslyckade virtuella datorn väljer du **genomför** på sidan Översikt för att slutföra redundansväxlingen.

    ![Knappen genomför](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. I **Bekräfta** väljer du **OK** för att bekräfta. Commit tar bort alla tillgängliga återställnings punkter för den virtuella datorn i Site Recovery och du kan inte ändra återställnings punkten.

8. Övervaka genomförande förloppet i meddelanden.

    ![Bekräfta meddelande ](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![ Bekräfta lyckad avisering](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. Site Recovery rensar inte den virtuella käll datorn efter redundansväxlingen. Du måste göra det manuellt.


## <a name="reprotect-the-vm"></a>Återaktivera skyddet för den virtuella datorn

Efter redundansväxlingen skyddar du den virtuella datorn i den sekundära regionen så att den replikeras tillbaka till den primära regionen. 

1. Kontrol lera att VM- **statusen** är *redundans* innan du börjar.
2. Kontrol lera att du kan komma åt den primära regionen och att du har behörighet att skapa virtuella datorer i den.
3. På sidan **Översikt över** virtuell dator väljer du **skydd igen**.

   ![Om du vill aktivera skydd för en virtuell dator för en virtuell dator.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. I **skydda igen** kontrollerar du replikeringens riktning (sekundär till primär region) och granskar mål inställningarna för den primära regionen. Resurser som marker ATS som nya skapas av Site Recovery som en del av återskydds åtgärden.

     ![Sidan skydds inställningar](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Klicka på **OK** för att starta återskydds processen. Processen skickar inledande data till mål platsen och replikerar delta informationen för de virtuella datorerna till målet.
7. Övervaka återställnings processen i aviseringarna. 

    ![Skydds åtgärder för att skydda förloppet aviseringen igen ](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du växlat över från den primära regionen till den sekundära och startade replikeringen av virtuella datorer till den primära regionen. Nu kan du växla tillbaka från den sekundära regionen till den primära.

> [!div class="nextstepaction"]
> [Växla tillbaka till den primära regionen](azure-to-azure-tutorial-failback.md)
