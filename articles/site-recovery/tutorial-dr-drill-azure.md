---
title: Kör en haveri beredskaps granskning till Azure med Azure Site Recovery
description: Lär dig hur du kör en haveri beredskap från en lokal plats till Azure, med Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84699625"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Köra ett programåterställningstest till Azure

Den här artikeln beskriver hur du kör en haveri beredskaps granskning för en lokal dator till Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) . Med ett test kan du verifiera din replikeringsstrategi utan dataförlust.


Det här är den fjärde självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för lokala datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Ställer in ett isolerat nätverk för redundanstestet
> * Förbereder för att ansluta till den virtuella Azure-datorn efter en redundansväxling
> * Kör ett redundanstest för en enskild dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Läs [igenom den här artikeln](site-recovery-test-failover-to-azure.md)om du vill veta mer om haveri beredskap för haveri beredskap i detalj.

## <a name="before-you-start"></a>Innan du börjar

Slutför de föregående självstudierna:

1. Se till att du har [konfigurerat Azure](tutorial-prepare-azure.md) för lokal katastrof återställning av virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska datorer till Azure.
2. Förbered din lokala [VMware](vmware-azure-tutorial-prepare-on-premises.md) [-eller Hyper-V-](hyper-v-prepare-on-premises-tutorial.md) miljö för haveri beredskap. Om du konfigurerar haveri beredskap för fysiska servrar granskar du [support mat ris](vmware-physical-secondary-support-matrix.md).
3. Konfigurera katastrof återställning för [virtuella VMware-datorer](vmware-azure-tutorial.md), [virtuella Hyper-V-datorer](hyper-v-azure-tutorial.md)eller [fysiska datorer](physical-azure-disaster-recovery.md).
 

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Kontrollera den virtuella datorns egenskaper innan du kör ett redundanstest, och se till att din [virtuella Hyper-V-dator](hyper-v-azure-support-matrix.md#replicated-vms) eller [virtuella VMware-dator](vmware-physical-azure-support-matrix.md#replicated-machines) uppfyller kraven för Azure.

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > och VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **beräkning och nätverk** kan du ändra Azure-namn, resurs grupp, mål storlek, tillgänglighets uppsättning och hanterade disk inställningar.
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Textnätverket ska använda samma IP-adressintervall.
- Uppdatera DNS för testnätverket med IP-adressen som anges för DNS-VM i inställningarna i **Beräkning och nätverk**. Mer information finns i [Saker att tänka på vid redundanstestning för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

## <a name="run-a-test-failover-for-a-single-vm"></a>Köra ett redundanstest för en enstaka virtuell dator

När du kör ett redundanstest händer följande:

1. En kravkontroll körs för att säkerställa att alla de villkor som krävs för redundans är på plats.
2. Redundansprocessen bearbetar data så att du kan skapa en virtuell Azure-dator. Om du väljer den senaste återställningspunkten, skapas en återställningspunkt från data.
3. En virtuell Azure-dator skapas med hjälp av de data som behandlas i föregående steg.

Kör redundanstestet på följande sätt:

1. I **Inställningar**  >  **replikerade objekt** klickar du på den virtuella datorn > **+ testa redundansväxlingen**.
2. Välj återställningspunkten **Senast bearbetade** för den här självstudien. Då redundansväxlar den virtuella datorn till den senast tillgängliga tidpunkten. Tidsstämpeln visas. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt RTO (mål för återställningstid).
3. I **Redundanstest** väljer du det Azure-målnätverk som de virtuella Azure-datorerna ska ansluta till efter redundans.
4. Starta redundansväxlingen genom att klicka på **OK**. Du kan följa förloppet genom att klicka på den virtuella datorn för att öppna dess egenskaper. Du kan också klicka på jobbet **Testa redundans** i valvnamnet > **Inställningar** > **Jobb** >
   **Platsåterställningsjobb**.
5. När redundansväxlingen är klar visas repliken av den virtuella Azure-datorn i Azure-portalen > **Virtual Machines**. Kontrollera att den virtuella datorn har rätt storlek, att den är ansluten till rätt nätverk och att den körs.
6. Du bör nu kunna ansluta till den replikerade virtuella datorn i Azure.
7. Vill du ta bort virtuella Azure-datorer som skapades under redundanstestningen klickar du på **Rensa redundanstestning** på den virtuella datorn. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kanske märker att redundanstiden är längre för VMware Linux-datorer, virtuella VMware-datorer som inte har aktiverat DHCP-tjänsten och virtuella VMware-datorer som inte har följande startdrivrutiner: storvsc, vmbus, storflt, intelide, atapi.

## <a name="connect-after-failover"></a>Anslut efter redundans

[Förbered för anslutning](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)om du vill ansluta till virtuella Azure-datorer med RDP/SSH efter redundans. Om det uppstår anslutnings problem efter redundansväxlingen följer du [fel söknings](site-recovery-failover-to-azure-troubleshoot.md) guiden.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra en redundansväxling och återställning efter fel för virtuella VMware-datorer](vmware-azure-tutorial-failover-failback.md) 
>  [Köra en redundansväxling och återställning efter fel för virtuella Hyper-V-datorer](hyper-v-azure-failover-failback-tutorial.md) 
>  [Köra en redundansväxling och återställning efter fel för fysiska datorer](physical-to-azure-failover-failback.md)
