---
title: Självstudie för att köra en granskning av haveri beredskap i Azure VM med Azure Site Recovery
description: I den här självstudien kör du en Azure VM haveri beredskap till en annan region med hjälp av Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395658"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Självstudie: köra en granskning av haveri beredskap för virtuella Azure-datorer

Lär dig hur du kör en haveri beredskaps granskning till en annan Azure-region för virtuella Azure-datorer som replikeras med [Azure Site Recovery](site-recovery-overview.md). Den här artikeln innehåller följande avsnitt:

> [!div class="checklist"]
> * Verifiera kraven
> * Kontrol lera inställningarna för virtuella datorer före detalj nivån
> * Köra ett redundanstest
> * Rensa efter detalj nivån


> [!NOTE]
> Den här självstudien innehåller minimala steg för att köra en haveri beredskaps granskning. Om du vill köra en detalj granskning med fullständig infrastruktur testning kan du läsa mer om Azure VM- [nätverk](azure-to-azure-about-networking.md), [automatisering](azure-to-azure-powershell.md)och [fel sökning](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien måste du aktivera haveri beredskap för en eller flera virtuella Azure-datorer. Det gör du genom att [slutföra den första självstudien](azure-to-azure-tutorial-enable-replication.md) i den här serien.

## <a name="verify-vm-settings"></a>Verifiera VM-inställningar

1. Välj den virtuella datorn i valvet > **replikerade objekt**.

    ![Alternativ för att öppna sidan för haveri beredskap i VM-egenskaper](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. På sidan **Översikt** kontrollerar du att den virtuella datorn är skyddad och felfri.
3. När du kör ett redundanstest väljer du ett virtuellt Azure-nätverk i mål regionen. Den virtuella Azure-datorn som skapades efter redundansväxlingen har placerats i det här nätverket. 

    - I den här självstudien väljer vi ett befintligt nätverk när vi kör redundanstest.
    - Vi rekommenderar att du väljer ett nätverk som inte är för produktion för visningen, så att IP-adresser och nätverks komponenter fortfarande är tillgängliga i produktions nätverk.
   - Du kan också förkonfigurera nätverks inställningar som ska användas för redundanstest. Detaljerade inställningar som du kan tilldela för varje NIC är undernät, privat IP-adress, offentlig IP-adress, belastningsutjämnare och nätverks säkerhets grupp. Vi använder inte den här metoden här, men du kan [läsa den här artikeln](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) om du vill veta mer.


## <a name="run-a-test-failover"></a>Köra ett redundanstest


1. På sidan **Översikt** väljer du **redundanstest**.

    
    ![Knappen Testa redundans för det replikerade objektet](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. I **testa redundans** väljer du en återställnings punkt. Den virtuella Azure-datorn i mål regionen skapas med hjälp av data från den här återställnings punkten.
  
   - **Senast bearbetade**: använder den senaste återställnings punkten som bearbetats av Site Recovery. Tidsstämpeln visas. Ingen tid för bearbetning av data, så det ger ett litet återställnings tids mål (RTO).
   -  **Senaste**: bearbetar alla data som skickas till Site Recovery, så att du kan skapa en återställnings punkt för varje virtuell dator innan du växlar över till den. Tillhandahåller lägsta återställnings punkt mål (återställnings punkt) eftersom alla data replikeras till Site Recovery När redundansväxlingen utlöses.
   - **Senaste appen – konsekvent**: det här alternativet växlar över virtuella datorer till den senaste programkonsekventa återställnings punkten. Tidsstämpeln visas.
   - **Anpassad**: redundansväxla till viss återställnings punkt. Anpassad är endast tillgängligt om du växlar över en enskild virtuell dator och inte använder en återställnings plan.

3. I **Azure Virtual Network** väljer du det mål nätverk där du vill placera virtuella Azure-datorer som skapats efter redundansväxlingen. Välj ett nätverk som inte är för produktion om det är möjligt och inte nätverket som skapades när du aktiverade replikeringen.

    ![Sidan Inställningar för redundanstest](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Starta redundansväxlingen genom att välja **OK**.
5. Övervaka redundanstest i meddelanden.

    ![Avisering om ](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![ slutfört förlopps meddelande](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. När redundansväxlingen är klar visas den virtuella Azure-datorn som skapats i mål regionen i Azure Portal **Virtual Machines**. Kontrol lera att den virtuella datorn körs, har rätt storlek och är ansluten till nätverket som du har valt.

## <a name="clean-up-resources"></a>Rensa resurser

1. På sidan **Essentials** väljer du **rensning av redundanstest**.

    ![Knapp för att starta rensnings processen](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. I **rensnings**  >  **anmärkningar** för redundanstest registrerar och sparar du eventuella observationer som är kopplade till redundanstest. 
3. Välj **testning är slutförd** för att ta bort virtuella datorer som skapades under redundanstest.

    ![Sida med rensnings alternativ](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Övervaka rensnings förloppet i meddelanden.

    ![Rensnings meddelande om rensnings förlopp ](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien körde du en haveri beredskap-granskning för att kontrol lera att redundansväxlingen fungerar som förväntat. Nu kan du prova en fullständig redundans.

> [!div class="nextstepaction"]
> [Köra en produktionsredundans](azure-to-azure-tutorial-failover-failback.md)
