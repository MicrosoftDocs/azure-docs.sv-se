---
title: Självstudie för att återställa virtuella Azure-datorer till en primär region vid haveri beredskap med Azure Site Recovery.
description: Självstudie för att lära dig att återställa virtuella Azure-datorer till en primär region med Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393944"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Självstudie: återställa virtuella Azure-datorer till den primära regionen

När du har misslyckats över en virtuell Azure-dator till en sekundär Azure-region, följer du den här självstudien för att redundansväxla den virtuella datorn till den primära Azure-regionen med [Azure Site Recovery](site-recovery-overview.md).  I den här artikeln kan du se hur du:

> [!div class="checklist"]
> 
> * Granska kraven.
> * Återställa den virtuella datorn i den sekundära regionen.
> * Skydda de primära virtuella datorerna tillbaka till den sekundära regionen.
> 
> [!NOTE]
> Den här självstudien visar hur du växlar tillbaka med minimala steg. Om du vill köra en redundansväxling med fullständiga inställningar kan du läsa mer om Azure VM- [nätverk](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md)och [fel sökning](azure-to-azure-troubleshoot-errors.md).



## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien bör du ha:

1. [Konfigurera replikering](azure-to-azure-tutorial-enable-replication.md) för minst en virtuell Azure-dator och försök utföra en [granskning av haveri beredskap](azure-to-azure-tutorial-dr-drill.md) för den.
2. [Det gick inte att överföra den virtuella datorn](azure-to-azure-tutorial-failover-failback.md) från den primära regionen till en sekundär region och skyddade den så att den replikeras från den sekundära regionen till den primära. 
3. Kontrol lera att den primära regionen är tillgänglig och att du kan skapa och komma åt nya resurser i den.

## <a name="fail-back-to-the-primary-region"></a>Växla tillbaka till den primära regionen

När de virtuella datorerna har återskyddats kan du växla tillbaka till den primära regionen efter behov.

1. Välj den virtuella datorn i valvet > **replikerade objekt**.

2. På sidan Översikt över virtuell dator kontrollerar du att den virtuella datorn är felfri och att synkroniseringen är klar innan du kör en redundansväxling. Den virtuella datorn ska vara i ett *skyddat* tillstånd.

    ![Sidan översikt över virtuell dator med skyddad status](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. På sidan Översikt väljer du **redundans**. Eftersom vi inte utför en redundanstest den här gången uppmanas vi att verifiera.

    [Sida som visar att vi samtycker till att köra redundans utan redundanstest](./media/azure-to-azure-tutorial-failback/no-test.png)

4. I **redundans** noterar du riktningen från sekundär till primär och väljer en återställnings punkt. Den virtuella Azure-datorn i målet (primär region) skapas med hjälp av data från den här platsen.
   - **Senast bearbetade**: använder den senaste återställnings punkten som bearbetats av Site Recovery. Tidsstämpeln visas. Ingen tid för bearbetning av data, så det ger ett litet återställnings tids mål (RTO).
   -  **Senaste**: bearbetar alla data som skickas till Site Recovery, så att du kan skapa en återställnings punkt för varje virtuell dator innan du växlar över till den. Tillhandahåller lägsta återställnings punkt mål (återställnings punkt) eftersom alla data replikeras till Site Recovery När redundansväxlingen utlöses.
   - **Senaste appen – konsekvent**: det här alternativet växlar över virtuella datorer till den senaste programkonsekventa återställnings punkten. Tidsstämpeln visas.
   - **Anpassad**: redundansväxla till viss återställnings punkt. Anpassad är endast tillgängligt om du växlar över en enskild virtuell dator och inte använder en återställnings plan.

    > [!NOTE]
    > Om du växlar över en virtuell dator som du har lagt till en disk efter att du har aktiverat replikering för den virtuella datorn, visar replikerings platserna de diskar som är tillgängliga för återställning. Till exempel visas en plats för replikering som skapades innan du lade till en andra disk som "1 av 2 diskar".

4. Välj **Stäng datorn innan du påbörjar redundans** om du vill Site Recovery försöka stänga av de virtuella käll datorerna innan du påbörjar redundans. Avstängning bidrar till att förhindra data förlust. Redundansväxlingen fortsätter även om avstängningen misslyckas. 

    ![Sidan Inställningar för redundans](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Starta redundansväxlingen genom att välja **OK**.
4. Övervaka redundansväxlingen i meddelanden.

    ![Meddelande om förlopp för växling vid fel](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Avisering om redundansväxling lyckades](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Skydda virtuella datorer på nytt

När du har återställt de virtuella datorerna till den primära regionen måste du skydda dem igen, så att de börjar replikera till den sekundära regionen igen.

1. På sidan **Översikt** för den virtuella datorn väljer du **skydd igen**.

    ![För att skydda från den primära regionen](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Granska mål inställningarna för den primära regionen. Resurser som marker ATS som nya skapas av Site Recovery som en del av återskydds åtgärden.
3. Klicka på **OK** för att starta återskydds processen. Processen skickar inledande data till mål platsen och replikerar delta informationen för de virtuella datorerna till målet.

     ![Sidan visar replikeringsinställningar](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Övervaka återställnings förloppet i aviseringar. 

    ![Skydds ](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png) meddelande om att skydda förloppet
    
  

## <a name="clean-up-resources"></a>Rensa resurser

För virtuella datorer med hanterade diskar, efter att återställning efter fel har slutförts och virtuella datorer skyddas om för replikering från primär till sekundär, Site Recovery rensar automatiskt datorer i den sekundära katastrof återställnings regionen. Du behöver inte manuellt ta bort virtuella datorer och nätverkskort i den sekundära regionen. Virtuella datorer med ohanterade diskar rensas inte.

Om du inaktiverar replikeringen helt efter att du har återställt, Site Recovery rensar datorer som skyddas av den. I det här fallet rensar den också diskar för virtuella datorer som inte använder hanterade diskar. 
 
## <a name="next-steps"></a>Nästa steg

I den här självstudien har du misslyckats med att återställa virtuella datorer från den sekundära regionen till den primära. Det här är det sista steget i processen som inkluderar aktivering av replikering för en virtuell dator, försök att utföra en haveri beredskap, redundansväxla från den primära regionen till den sekundära och slutligen återställa.

> [!div class="nextstepaction"]
> Prova nu haveri beredskap till Azure för en [lokal virtuell dator](vmware-azure-tutorial-prepare-on-premises.md)

