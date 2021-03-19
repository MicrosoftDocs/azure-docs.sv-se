---
title: Redundansväxla virtuella datorer i Azure VMware-lösningen till Azure med Site Recovery
description: Lär dig hur du växlar över virtuella datorer med Azure VMware-lösningen till Azure i Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 60c268ba837540eda86a4cbaf6e0ab1c425d90b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814583"
---
# <a name="fail-over--azure-vmware-solution-vms"></a>Redundansväxla virtuella datorer i Azure VMware-lösningen

Den här artikeln beskriver hur du växlar över en virtuell Azure VMware-lösning till Azure med [Azure Site Recovery](site-recovery-overview.md).

Det här är den femte självstudien i en serie som visar hur du konfigurerar haveri beredskap till Azure för virtuella Azure VMware-lösningar.

I den här guiden får du lära dig att:

> [!div class="checklist"]

> * Kontrol lera att Azure VMware-lösningens VM-egenskaper överensstämmer med Azure-kraven.
> * Redundansväxla vissa virtuella datorer till Azure.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standard alternativ där det är möjligt och visar inte alla möjliga inställningar och sökvägar. Om du vill veta mer om redundans i detalj, se [redundansväxla virtuella datorer](site-recovery-failover.md).

[Lär dig mer om](failover-failback-overview.md#types-of-failover) olika typer av redundans. Läs [den här artikeln](site-recovery-failover.md)om du vill redundansväxla flera virtuella datorer i en återställnings plan.

## <a name="before-you-start"></a>Innan du börjar

Slutför de föregående självstudierna:

1. Kontrol lera att du har [konfigurerat Azure](avs-tutorial-prepare-azure.md) för haveri beredskap till Azure.
2. Följ [de här stegen](avs-tutorial-prepare-avs.md) för att förbereda distributionen av Azure VMware-lösningen för haveri beredskap till Azure.
3. [Konfigurera](avs-tutorial-replication.md) katastrof återställning för virtuella Azure VMware-lösningar.
4. Kör en [granskning av haveri beredskap](avs-tutorial-dr-drill-azure.md) för att se till att allt fungerar som förväntat.

## <a name="verify-vm-properties"></a>Kontrollera VM-egenskaperna

Innan du kör en redundansväxling kontrollerar du VM-egenskaperna för att se till att de virtuella datorerna uppfyller [Azure-kraven](vmware-physical-azure-support-matrix.md#replicated-machines).

Verifiera egenskaperna enligt följande:

1. I **skyddade objekt** väljer du **replikerade objekt** och väljer sedan den virtuella dator som du vill verifiera.

2. I fönstret **Replikerade objekt** finns det en sammanfattning av VM-informationen, hälsostatus och de senaste tillgängliga återställningspunkterna. Välj **Egenskaper** om du vill visa mer information.

3. I **beräkning och nätverk** kan du ändra dessa egenskaper efter behov:
    * Azure-namn
    * Resursgrupp
    * Mål storlek
    * [Tillgänglighetsuppsättning](../virtual-machines/windows/tutorial-availability-sets.md)
    * Inställningar för hanterade diskar

4. Du kan visa och ändra nätverks inställningar, inklusive:

    * Nätverket och under nätet där den virtuella Azure-datorn finns efter redundansväxlingen.
    * Den IP-adress som ska tilldelas till den.

5. I **Diskar** kan du se information om operativsystemet och vilka datadiskar som finns på den virtuella datorn.

## <a name="run-a-failover-to-azure"></a>Köra en redundans i Azure

1. I **Inställningar**  >  **replikerade objekt** väljer du den virtuella dator som du vill redundansväxla och väljer sedan **redundans**.
2. I **Redundans** väljer du en **återställningspunkt** att redundansväxla till. Du kan välja något av följande alternativ:
   * **Senaste**: Det här alternativet bearbetar först alla data som skickas till Site Recovery. Det ger det lägsta återställnings punkt målet, eftersom den virtuella Azure-datorn som skapas efter redundansväxlingen har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.
   * **Senaste bearbetning**: det här alternativet Miss lyckas den virtuella datorn över till den senaste återställnings punkten som bearbetades av Site Recovery. Det här alternativet ger en låg RTO (återställnings tid) eftersom ingen tid har använts för bearbetning av obearbetade data.
   * **Senaste program – konsekvent**: det här alternativet Miss lyckas den virtuella datorn till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery.
   * **Anpassad**: med det här alternativet kan du ange en återställnings punkt.

3. Välj **Stäng datorn innan du påbörjar redundans** för att försöka stänga av virtuella käll datorer innan du utlöser redundansväxlingen. Redundansväxlingen fortsätter även om avstängningen Miss lyckas. Du kan följa förloppet för redundansväxlingen på **jobb** sidan.

I vissa fall kräver redundans ytterligare bearbetning som tar cirka 8 till 10 minuter att slutföra. Du kan lägga märke till längre redundanstest för:

* Virtuella VMware-datorer som kör en mobilitets tjänst version som är äldre än 9,8.
* Virtuella VMware Linux-datorer.
* Virtuella VMware-datorer som inte har DHCP-tjänsten aktive rad.
* Virtuella VMware-datorer som inte har följande start driv rutiner: storvsc, VMBus, storflt, intelide, ATAPI.

> [!WARNING]
> Avbryt inte en pågående redundansväxling. Innan redundans startas stoppas den virtuella datorreplikeringen. Om du avbryter en pågående redundans så stoppas redundansen, men den virtuella datorn kommer inte att replikeras igen.

## <a name="connect-to-failed-over-vm"></a>Anslut till misslyckad virtuell dator

1. Om du vill ansluta till virtuella Azure-datorer efter en redundansväxling med hjälp av Remote Desktop Protocol (RDP) och SSH (Secure Shell) [kontrollerar du att kraven har uppfyllts](failover-failback-overview.md#connect-to-azure-after-failover).
2. Efter redundansväxlingen går du till den virtuella datorn och validerar genom att [ansluta](../virtual-machines/windows/connect-logon.md) till den.
3. Använd **ändrings återställnings punkt** om du vill använda en annan återställnings punkt efter redundansväxlingen. När du har bekräftat redundansväxlingen i nästa steg kommer det här alternativet inte längre att vara tillgängligt.
4. När du har verifierat väljer du **genomför** för att slutföra återställnings punkten för den virtuella datorn efter redundansväxlingen.
5. När du har bekräftat raderas alla andra tillgängliga återställnings punkter. I det här steget slutförs redundansväxlingen.

>[!TIP]
> Om det uppstår anslutnings problem efter redundansväxlingen följer du [fel söknings guiden](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Nästa steg

Skydda virtuella Azure-datorer till Azure VMware-lösningen privat moln efter redundansväxlingen. Efter att de virtuella datorerna har återskyddats och repliker ATS till Azure VMware-lösningens privata moln, kan du återställa från Azure när du är redo.


> [!div class="nextstepaction"]
> [Skydda virtuella Azure-datorer](avs-tutorial-reprotect.md) 
>  [Återställning efter fel från Azure](avs-tutorial-failback.md)
