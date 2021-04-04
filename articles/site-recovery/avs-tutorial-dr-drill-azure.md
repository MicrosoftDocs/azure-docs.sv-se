---
title: Köra en haveri beredskap från Azure VMware-lösningen till Azure med Azure Site Recovery
description: Lär dig hur du kör en haveri beredskap från Azure VMware-lösningen privat moln till Azure med Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814589"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Köra en haveri beredskaps visning från Azure VMware-lösningen till Azure

Den här artikeln beskriver hur du kör en haveri beredskap för en virtuell Azure VMware-lösning i Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) . Med ett test kan du verifiera din replikeringsstrategi utan dataförlust.


Det här är den fjärde självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för Azure VMware-lösningar datorer.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]

> * Ställer in ett isolerat nätverk för redundanstestet
> * Förbereder för att ansluta till den virtuella Azure-datorn efter en redundansväxling
> * Kör ett redundanstest för en enskild dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Läs [igenom den här artikeln](site-recovery-test-failover-to-azure.md)om du vill veta mer om haveri beredskap för haveri beredskap i detalj.

## <a name="before-you-start"></a>Innan du börjar

Slutför de föregående självstudierna:

1. Kontrol lera att du har [konfigurerat Azure](avs-tutorial-prepare-azure.md) för haveri beredskap till Azure.
2. Följ [de här stegen](avs-tutorial-prepare-avs.md) för att förbereda distributionen av Azure VMware-lösningen för haveri beredskap till Azure.
3. [Konfigurera](avs-tutorial-replication.md) katastrof återställning för virtuella Azure VMware-lösningar.
 

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör ett redundanstest kontrollerar du egenskaperna för den virtuella datorn och ser till att den  [virtuella VMware-datorn](vmware-physical-azure-support-matrix.md#replicated-machines) uppfyller Azure-kraven.

1. I **Skyddade objekt** klickar du på **Replikerade objekt** > och VM.
2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Klicka på **Egenskaper** för att se mer information.
3. I **beräkning och nätverk** kan du ändra Azure-namn, resurs grupp, mål storlek, tillgänglighets uppsättning och hanterade disk inställningar.
4. Du kan visa och ändra inställningar för nätverk, inklusive det nätverk/undernät där den virtuella Azure-datorn kommer att finnas efter redundansen och den IP-adress som kommer att tilldelas till den.
5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="create-a-network-for-test-failover"></a>Skapa ett nätverk för redundanstest

Vi rekommenderar att du för redundanstest väljer ett nätverk som är isolerat från produktionsnätverkets återställningsplats specifik i inställningarna för **Beräkning och nätverk** för varje virtuella dator. Som standard, när du skapar ett virtuellt Azure-nätverk är det isolerat från andra nätverk. Testnätverket ska efterlikna produktionsnätverket:

- Testnätverket ska ha samma antal undernät som produktionsnätverket. Undernäten ska ha samma namn.
- Test nätverket bör använda samma IP-adress klass och under nätets intervall.
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
> [Köra en redundansväxling](avs-tutorial-failover.md)

