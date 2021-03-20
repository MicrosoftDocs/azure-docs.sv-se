---
title: Konfigurera haveri beredskap för Hyper-V med hjälp av Azure Site Recovery
description: Lär dig hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med hjälp av Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5ce98c785700301bba92926d7d5a243b614eca7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87504236"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Konfigurera haveriberedskap för lokala virtuella Hyper-V-datorer till Azure

[Azure Site Recoverys](site-recovery-overview.md) tjänsten bidrar till din strategi för katastrof återställning genom att hantera och samordna replikering, redundans och återställning efter fel för lokala datorer och virtuella datorer i Azure.

Detta är den tredje självstudien i en serie. Det visar hur du konfigurerar haveri beredskap för lokala virtuella Hyper-V-datorer till Azure. I den här självstudien används virtuella Hyper-V-datorer som inte hanteras av Microsoft System Center Virtual Machine Manager (VMM).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Väljer replikeringskälla och mål.
> * Konfigurera käll miljön för replikering, inklusive lokala Site Recovery-komponenter och mål miljön för replikering.
> * Skapar en replikeringsprincip.
> * Aktiverar replikering för en virtuell dator.

> [!NOTE]
> Självstudier visar den enklaste distributions vägen för ett scenario. De använder standardalternativ där så är möjligt och visar inte alla möjliga inställningar och sökvägar. Detaljerade anvisningar finns i artiklarna i avsnittet om **att gå till guider** i [Site Recovery-dokumentationen](./index.yml).



## <a name="before-you-begin"></a>Innan du börjar

Detta är den tredje självstudien i en serie. Det förutsätter att du redan har slutfört uppgifterna i de föregående självstudierna:

1. [Förbereda Azure](tutorial-prepare-azure.md)
2. [Förbereda lokala Hyper-V](./hyper-v-prepare-on-premises-tutorial.md)

## <a name="select-a-replication-goal"></a>Väljer ett replikeringsmål

1. I Azure Portal går du till **Recovery Services valv** och väljer valvet. Vi för beredde valvet **ContosoVMVault** i föregående självstudie.
2. I **komma igång** väljer du **Site Recovery** och väljer sedan **Förbered infrastruktur**.
3. I **skydds mål**  >  **där finns datorerna?** väljer du **lokalt**.
4. I **var vill du replikera dina datorer?** väljer **du till Azure**.
5. **Är dina datorer virtualiserade? väljer du** **Ja, med Hyper-V**.
6. I **använder du System Center VMM för att hantera Hyper-V-värdar?** väljer du **Nej**.
7. Välj **OK**.

    ![Skärm bild av skydds mål alternativen i förbereda infrastruktur.](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bekräfta distributionsplanering

1. Om du planerar en stor distribution i **planerings planeringen**, laddar du ned distributions planeraren för Hyper-V från länken på sidan. [Läs mer](hyper-v-deployment-planner-overview.md) om planering av distribution av Hyper-V.
2. I den här självstudien behöver vi inte distributions planeraren. **Har du slutfört distributions planeringen?** väljer du **Jag vill göra det senare** och väljer sedan **OK**.

    ![Skärm bild av alternativen för distributions planering i förbereda infrastrukturen.](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Om du vill konfigurera käll miljön skapar du en Hyper-V-plats och lägger till den på den platsen de Hyper-V-värdar som innehåller de virtuella datorer som du vill replikera. Sedan laddar du ned och installerar Azure Site Recovery-providern och Azure Recovery Services-agenten på varje värd och registrerar Hyper-V-platsen i valvet.

1. Under **Förbered infrastruktur** väljer du **källa**.
2. I **Förbered källa** väljer du **+ Hyper-V-plats**.
3. Ange plats namnet i **skapa Hyper-V-webbplats**. Vi använder **ContosoHyperVSite**.

    ![Skärm bild av val av Hyper-V-plats i Förbered infrastruktur.](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. När platsen har skapats går du till **Förbered källa**  >  **steg 1: Välj Hyper-V-plats** och välj den plats som du skapade.
5. Välj **+ Hyper-V-server**.

    ![Skärm bild av val av Hyper-V-server i Förbered infrastruktur.](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Hämta installations programmet för Microsoft Azure Site Recovery-providern.
7. Ladda ned valvregistreringsnyckeln. Du behöver den här nyckeln för att installera providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Skärm bild av alternativen för att ladda ned providern och registrerings nyckeln.](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installera providern

Installera den nedladdade installations filen (AzureSiteRecoveryProvider.exe) på varje Hyper-V-värd som du vill lägga till på Hyper-V-platsen. Installations programmet installerar Azure Site Recovery-providern och Recovery Services agenten på varje Hyper-V-värd.

1. Kör installations filen.
2. I installationsguiden för Azure Site Recovery-providern > **Microsoft Update** väljer du att använda Microsoft Update för att söka efter uppdateringar för providern.
3. I **installationen** accepterar du standard installations platsen för providern och agenten och väljer **Installera**.
4. Efter installationen går du till guiden Microsoft Azure Site Recovery registrering > **valv inställningar**, väljer **Bläddra** och i **nyckel fil** väljer du den valv nyckel fil som du laddade ned.
5. Ange Azure Site Recovery-prenumerationen, valvnamnet (**ContosoVMVault**) och Hyper-V-platsen (**ContosoHyperVSite**) som Hyper-V-servern tillhör.
6. I **Proxyinställningar** väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
7. I **registreringen**, efter att servern har registrerats i valvet, väljer du **Slutför**.

Metadata från Hyper-V-servern hämtas av Azure Site Recovery och servern visas i **Site Recovery infrastruktur**  >  **Hyper-v-värdar**. Processen kan ta upp till 30 minuter.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Installera providern på en Hyper-V Core-server

Om du kör en Hyper-V Core-server laddar du ned installations filen och följer de här stegen:

1. Extrahera filerna från AzureSiteRecoveryProvider.exe till en lokal katalog genom att köra det här kommandot:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Kör `.\setupdr.exe /i`. Resultaten loggas till%Programdata%\ASRLogs\DRASetupWizard.log.

3. Registrera servern genom att köra det här kommandot:

    ```
    cd  "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj och verifiera mål resurser:

1. Välj **Förbered infrastruktur**  >  **mål**.
2. Välj den prenumeration och den resurs grupp **conto sorg** som de virtuella Azure-datorerna ska skapas i efter redundansväxlingen.
3. Välj **Resource Manager**-distributionsmodellen.

Site Recovery kontrollerar att du har ett eller flera kompatibla Azure-lagringskonton och Azure-nätverk.

## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

1. Välj **Förbered replikering av infrastruktur**  >  **Inställningar**  >  **+ skapa och koppla**.
2. I **skapa och associera princip** anger du ett princip namn. Vi använder **ContosoReplicationPolicy**.
3. I den här självstudien lämnar vi standardinställningarna:
    - **Kopierings frekvensen** anger hur ofta delta data (efter den inledande replikeringen) kommer att replikeras. Standard frekvensen är var femte minut.
    - **Kvarhållning av återställnings** punkter anger att återställnings punkter kommer att behållas i två timmar. Det högsta tillåtna värdet för kvarhållning när du skyddar virtuella datorer som finns på Hyper-V-värdar är 24 timmar.
    - **Frekvens för appkonsekvent ögonblicksbild** anger att återställningspunkterna som innehåller appkonsekventa ögonblicksbilder skapas varje timme.
    - **Start tiden för inledande replikering** anger att den inledande replikeringen kommer att starta omedelbart.
4. När principen har skapats väljer du **OK**. När du skapar en ny princip associeras den automatiskt med den angivna Hyper-V-platsen. I vår själv studie kurs är det **ContosoHyperVSite**.

    ![Replikeringsprincip](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Aktivera replikering

1. I **Replikera program** väljer du **källa**.
2. I **Källa** väljer du platsen **ContosoHyperVSite**. Välj sedan **OK**.
3. I **mål** kontrollerar du målet (Azure), valv prenumerationen och distributions modellen för **Resource Manager** .
4. Om du använder själv studie inställningarna väljer du det lagrings konto för **contosovmsacct1910171607** som skapades i föregående självstudie för replikerade data. Välj också det **ContosoASRnet** -nätverk där virtuella Azure-datorer ska finnas efter redundansväxlingen.
5. I **virtuella datorer**  >  **väljer** du den virtuella dator som du vill replikera. Välj sedan **OK**.

   Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skydd** är klart slutförs den inledande replikeringen och den virtuella datorn är klar för redundans.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Köra ett programåterställningstest](tutorial-dr-drill-azure.md)
