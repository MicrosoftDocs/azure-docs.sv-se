---
title: Konfigurera haveri beredskap för Hyper-V (med VMM) med Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer i System Center VMM-moln till Azure med hjälp av Site Recovery.
ms.topic: tutorial
ms.date: 03/19/2020
ms.custom: MVC
ms.openlocfilehash: c806f968bc6530879f64ddbf6fd4c7d45aa7a8d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89442828"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer i VMM-moln till Azure

I den här självstudien beskrivs hur du aktiverar replikering för lokala virtuella Hyper-V-datorer (VM) som hanteras av System Center Virtual Machine Manager (VMM) till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md). Om du inte använder VMM [följer du den här självstudien](hyper-v-azure-tutorial.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurera käll miljön för replikering, inklusive lokala Site Recovery-komponenter och mål miljön för replikering.
> * Konfigurera nätverks mappning mellan virtuella VMM-nätverk och virtuella Azure-nätverk.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artiklarna i avsnittet om **att gå till guider** i [Site Recovery-dokumentationen](./index.yml).

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har slutfört följande Självstudier:

1. [Förbereda Azure](tutorial-prepare-azure.md)
1. [Förbereda lokala Hyper-V-servrar](hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. I Azure Portal går du till **Recovery Services valv** och väljer det **ContosoVMVault** -valv som skapades i guiden [Förbered Azure](tutorial-prepare-azure.md#create-a-recovery-services-vault) .
1. Från **komma igång** väljer du **Site Recovery**  >  **Förbered infrastruktur** och konfigurerar följande inställningar:
    1. **Skydds mål**  >  **Var finns dina datorer?**, Välj **lokalt**.
    1. **Var vill du replikera dina datorer?**, Välj **till Azure**.
    1. **Är dina datorer virtualiserade?**, Välj **Ja, med Hyper-V**.
    1. **Använder du System Center VMM för att hantera dina Hyper-V-värdar? väljer du** **Ja**.
1. Välj **OK**.

   ![Replikeringsmål](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. Om du planerar en stor distribution i **planerings planeringen**, laddar du ned distributions planeraren för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om planering av distribution av Hyper-V.
1. I den här självstudien behöver vi inte distributions planeraren. **Har du slutfört distributions planeringen?** väljer du **Jag vill göra det senare** och väljer sedan **OK**.

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

När du konfigurerar käll miljön installerar du Azure Site Recovery-providern på VMM-servern och registrerar servern i valvet. Du installerar Azure Recovery Services-agenten på varje Hyper-V-värd.

1. **Förbered infrastruktur**. Välj **källa**.
1. **Förbered källa**. Välj **+ VMM** för att lägga till en VMM-Server. I **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp**.
1. Hämta installations programmet för Microsoft Azure Site Recovery-providern.
1. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln när du kör installations programmet för providern. Nyckeln är giltig i fem dagar efter att du har genererat den.
1. Hämta installations programmet för Microsoft Azure Recovery Services agenten.

   ![Hämta Provider, registrerings nyckel och agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installera providern på VMM-servern

1. I installations guiden för Azure Site Recovery Provider **Microsoft Update**. Välj att använda Microsoft Update för att söka efter leverantörs uppdateringar.
1. **Installation**. Godkänn standard installations platsen för providern och välj **Installera**.
1. Efter installationen, i guiden Microsoft Azure Site Recovery registrering väljer du **valv inställningar**, **bläddrar** och i **nyckel fil**, väljer du den valv nyckel fil som du laddade ned.
1. Ange Azure Site Recovery prenumerationen och valv namnet (**ContosoVMVault**). Ange ett eget namn på VMM-servern för att identifiera den i valvet.
1. **Proxyinställningar**. Välj **Anslut direkt till Azure Site Recovery utan proxyserver**.
1. Acceptera standard platsen för certifikatet som används för att kryptera data. Krypterade data dekrypteras när du växlar över.
1. **Synkronisera molnets metadata**. Välj **Sync Cloud metadata-data för att Site Recovery portalen**. Den här åtgärden behöver bara inträffa en gång på varje server. Välj sedan **Registrera**.
1. När servern har registrerats i valvet väljer du **Slutför**.

När registreringen är klar hämtas metadata från servern av Azure Site Recovery och VMM-servern visas i **Site Recovery-infrastruktur**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installera Recovery Services-agenten på Hyper-V-värdar

Installera agenten på varje Hyper-V-värd som innehåller de virtuella datorer som du vill replikera.

Konfigurera de här inställningarna i installations guiden för Microsoft Azure Recovery Services agent:

1. **Krav kontroll**. Välj **Nästa**. Alla nödvändiga komponenter som saknas kommer att installeras automatiskt.
1. **Installations inställningar**. Acceptera installations platsen. Markera **Installera**.

    >[!NOTE]
    >För Azure Site Recovery krävs inte **cachelagring location** .

1. **Installation**. När installationen är klar väljer du **Stäng** för att slutföra guiden.

   ![Installera agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

1. Välj **Förbered infrastruktur**  >  **mål**.
1. Välj den prenumeration och resurs grupp (**conto sorg**) som de virtuella Azure-datorerna ska skapas i efter redundansväxlingen.
1. Välj distributions modell för **Resource Manager** .

Site Recovery kontrollerar att det finns ett eller flera kompatibla Azure Storage-konton och-nätverk.

## <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

1. **Site Recovery infrastruktur**  >  **Nätverks mappningar**  >  **Nätverks mappning**. Välj ikonen **+ nätverks mappning** .
1. **Lägg till nätverks mappning**. Välj **käll System Center VMM** -Server. För **målet** väljer du Azure.
1. Kontrollera prenumerationen och distributionsmodellen efter redundansväxling.
1. **Käll nätverk**. Välj det lokala virtuella dator nätverket.
1. **Mål nätverk**. Välj det Azure-nätverk där replikerade virtuella Azure-datorer ska placeras när de skapas efter en redundansväxling. Välj sedan **OK**.

   ![Nätverksmappning](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Välj **Förbered replikering av infrastruktur**  >  **Inställningar**  >  **+ skapa och koppla**.
1. I **skapa och associera princip** anger du ett princip namn. Vi använder **ContosoReplicationPolicy**.
1. Godkänn standardinställningarna och välj **OK**:
   - **Kopierings frekvensen** visar att delta data kommer att replikeras var femte minut efter den inledande replikeringen.
   - **Kvarhållning av återställnings punkter** anger att varje återställnings punkt kommer att behållas i två timmar.
   - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
   - **Start tiden för inledande replikering** anger att den inledande replikeringen kommer att starta omedelbart.
   - **Kryptering av data som lagras på Azure** är inställt på standard (**av**) och indikerar att data i vila-data i Azure inte är krypterade.
1. När principen har skapats väljer du **OK**. När du skapar en ny princip associeras den automatiskt med VMM-molnet.

## <a name="enable-replication"></a>Aktivera replikering

1. **Replikera program**. Välj **källa**.
1. **Källa**. Välj VMM-molnet. Välj sedan **OK**.
1. **Mål**. Verifiera målet (Azure), valv prenumerationen och välj **Resource Manager** -modellen.
1. Välj lagrings konto för **contosovmsacct1910171607** och **ContosoASRnet** Azure-nätverket.
1. **Virtuella datorer**  >  **Välj**. Välj den virtuella dator som du vill replikera. Välj sedan **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** är klart slutförs den inledande replikeringen och den virtuella datorn är klar för redundans.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Köra ett programåterställningstest](tutorial-dr-drill-azure.md)
