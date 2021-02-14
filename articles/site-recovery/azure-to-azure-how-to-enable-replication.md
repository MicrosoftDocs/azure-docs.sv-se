---
title: Konfigurera replikering för virtuella Azure-datorer i Azure Site Recovery
description: Lär dig hur du konfigurerar replikering till en annan region för virtuella Azure-datorer med hjälp av Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 49929cfe0abc634dc4b704aba1c7b11a5d7dd777
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383593"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replikera virtuella Azure-datorer till en annan Azure-region


I den här artikeln beskrivs hur du aktiverar replikering av virtuella Azure-datorer, från en Azure-region till en annan.

## <a name="before-you-start"></a>Innan du börjar

Den här artikeln förutsätter att du har för berett för Site Recovery distribution, enligt beskrivningen i [självstudien om Azure till Azure haveri beredskap](azure-to-azure-tutorial-enable-replication.md).

Förutsättningarna bör vara på plats och du måste ha skapat ett Recovery Services-valv.


## <a name="enable-replication"></a>Aktivera replikering

Aktivera replikering. Den här proceduren förutsätter att den primära Azure-regionen är Asien, östra och att den sekundära regionen är Asien, sydöstra.

1. Klicka på **+ Replikera** i valvet.
2. Observera följande fält:
   - **Källa**: den virtuella datorns start punkt, som i det här fallet är **Azure**.
   - **Käll plats**: Azure-regionen från vilken du vill skydda dina virtuella datorer. I den här bilden är käll platsen "Asien, östra"
   - **Distributions modell**: Azures distributions modell för käll datorerna.
   - **Käll prenumeration**: den prenumeration som de virtuella käll datorerna tillhör. Detta kan vara en prenumeration i samma Azure Active Directory-klientorganisation som dina valv i återställningstjänsten finns i.
   - **Resurs grupp**: resurs gruppen som de virtuella käll datorerna tillhör. Alla virtuella datorer under den valda resurs gruppen visas som skydd i nästa steg.

     ![Skärm bild som visar de fält som behövs för att konfigurera replikering.](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. I **Virtual Machines > Välj virtuella datorer** klickar du på och väljer varje virtuell dator som du vill replikera. Du kan bara välja datorer som stöder replikering. Klicka sedan på **OK**.
    ![Skärm bild som visar var du väljer virtuella datorer.](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. I **Inställningar** kan du välja att konfigurera mål plats inställningar:

   - **Målplats**: den plats där dina virtuella käll dator data kommer att replikeras. Beroende på vilken plats du har valt för datorer visas en lista över lämpliga mål regioner i Site Recovery. Vi rekommenderar att du behåller mål platsen samma som platsen för Recovery Services valv.
   - **Målprenumeration**: Målprenumerationen som används för haveriberedskap. Som standard är målprenumerationen samma som källprenumerationen.
   - **Mål resurs grupp**: resurs gruppen som alla dina replikerade virtuella datorer tillhör.
       - Som standard skapar Site Recovery en ny resurs grupp i mål regionen med suffixet "ASR" i namnet.
       - Om resurs gruppen som skapats av Site Recovery redan finns återanvänds den.
       - Du kan anpassa resurs grupps inställningarna.
       - Platsen för mål resurs gruppen kan vara vilken Azure-region som helst, förutom den region där de virtuella käll datorerna finns.
   - **Virtuellt mål nätverk**: Site Recovery skapar som standard ett nytt virtuellt nätverk i mål regionen med ett "ASR"-suffix i namnet. Detta mappas till ditt käll nätverk och används för alla framtida skydd. [Läs mer](./azure-to-azure-network-mapping.md) om nätverks mappning.
   - **Mål lagrings konton (den virtuella käll datorn använder inte hanterade diskar)**: som standard skapar Site Recovery ett nytt mål lagrings konto mimicking din käll konfiguration för VM-lagring. Om lagrings kontot redan finns återanvänds det.
   - **Replik-hanterade diskar (virtuell käll dator använder Managed Disks)**: Site Recovery skapar nya replikbaserade diskar i mål regionen för att spegla den virtuella käll datorns hanterade diskar med samma lagrings typ (standard eller Premium) som den virtuella käll datorn för den virtuella datorn.
   - **Cache-lagrings konton**: Site Recovery behöver ett extra lagrings konto som heter cache Storage i käll regionen. Alla ändringar som sker på de virtuella käll datorerna spåras och skickas till cache Storage-kontot innan de replikeras till mål platsen. Det här lagrings kontot bör vara standard.
   - **Tillgänglighets uppsättningar för mål**: som standard skapar Site Recovery en ny tillgänglighets uppsättning i mål regionen med suffixet "Azure Site Recovery" i namnet, för virtuella datorer som ingår i en tillgänglighets uppsättning i käll regionen. Om tillgänglighets uppsättningen som skapats av Site Recovery redan finns återanvänds den.
     >[!NOTE]
     >När du konfigurerar mål tillgänglighets uppsättningarna konfigurerar du olika tillgänglighets uppsättningar för virtuella datorer med olika storlek. 
     >
   - **Tillgänglighetszoner för mål**: som standard tilldelar Site Recovery samma zonnummer som källregionen i målregionen om målregionen har stöd för tillgänglighetszoner.

     Om målregionen inte har stöd för tillgänglighetszoner konfigureras de virtuella måldatorerna som enskilda instanser som standard. Om det behövs kan du konfigurera sådana virtuella datorer att bli en del av tillgänglighetsuppsättningarna i målregionen genom att klicka på ”Anpassa”.

     >[!NOTE]
     >Du kan inte ändra tillgänglighetstypen enskild instans, tillgänglighetsuppsättningen eller tillgänglighetszonen efter att du har aktiverat replikering. Du måste inaktivera och aktivera replikering för att ändra tillgänglighetstypen.
     >

   - **Replikeringsprincip**: den definierar inställningarna för kvarhållning av återställnings punkts historik och frekvens för programkonsekventa ögonblicks bilder. Som standard skapar Azure Site Recovery en ny replikeringsprincip med standardinställningar på 24 timmar för kvarhållning av återställnings punkter och 4 timmar för en konsekvent frekvens för programkonsekventa ögonblicks bilder.

     ![Aktivera replikering](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Aktivera replikering för tillagda diskar

Om du lägger till diskar till en virtuell Azure-dator för vilken replikering är aktive rad inträffar följande:
-   Hälso tillståndet för den virtuella datorn visar en varning och en anteckning informerar dig om att en eller flera diskar är tillgängliga för skydd.
-   Om du aktiverar skydd för de tillagda diskarna försvinner varningen efter den inledande replikeringen av disken.
-   Om du väljer att inte aktivera replikering för disken kan du välja att ignorera varningen.


    ![Ny disk har lagts till](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Gör så här om du vill aktivera replikering för en tillagd disk:

1.  I valvet > **replikerade objekt** klickar du på den virtuella dator som du har lagt till disken i.
2.  Klicka på **diskar** och välj sedan den datadisk som du vill aktivera replikering för (diskarna har statusen **inte skyddad** ).
3.  I **disk information** klickar du på **Aktivera replikering**.

    ![Aktivera replikering för tillagd disk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

När jobbet aktivera replikering har körts och den inledande replikeringen har slutförts, tas varningen för replikeringen bort från disk problemet.



## <a name="customize-target-resources"></a>Anpassa mål resurser

Du kan ändra inställningarna för standard mål som används av Site Recovery.

1. Klicka på **Anpassa:** bredvid ' mål prenumeration ' om du vill ändra standard mål prenumerationen. Välj prenumerationen i listan över alla prenumerationer som är tillgängliga i samma Azure Active Directory-klient (AAD).

2. Klicka på **Anpassa:** om du vill ändra standardinställningarna:
    - I **mål resurs grupp** väljer du resurs gruppen i listan över alla resurs grupper på mål platsen för prenumerationen.
    - I **virtuellt mål nätverk** väljer du nätverket från en lista över alla virtuella nätverk på mål platsen.
    - I **tillgänglighets uppsättning** kan du lägga till inställningar för tillgänglighets uppsättningar på den virtuella datorn, om de är en del av en tillgänglighets uppsättning i käll regionen.
    - I **mål lagrings konton** väljer du det konto som du vill använda.

        ![Skärm bild som visar hur du anpassar inställningarna för mål prenumerationen.](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klicka på **Anpassa:** om du vill ändra inställningarna för replikering.
4. I **konsekvens för flera virtuella datorer** väljer du de virtuella datorer som du vill replikera tillsammans.
    - Alla maskiner i en replikeringsgrupp har delade kraschkonsekventa och appkonsekventa återställningspunkter när de redundansväxlas.
    - Att aktivera konsekvens för flera virtuella datorer kan påverka arbets belastnings prestanda (eftersom det är processor intensiv). Den bör bara aktive ras om datorer kör samma arbets belastning och du behöver konsekvens på flera datorer.
    - Om ett program t. ex. har två SQL Server virtuella datorer och två webb servrar, ska du bara lägga till de SQL Server virtuella datorerna i en replikeringsgrupp.
    - Du kan välja att ha högst 16 virtuella datorer i en replikeringsgrupp.
    - Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
    - Se till att det inte finns någon brand Väggs utrustning som blockerar den interna kommunikationen mellan de virtuella datorerna via port 20004.
    - Om du vill att virtuella Linux-datorer ska ingå i en replikeringsgrupp ser du till att utgående trafik på port 20004 öppnas manuellt enligt vägledning för den aktuella Linux-versionen.
![Skärm bild som visar konsekvens inställningar för flera virtuella datorer.](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klicka på **skapa mål resurs**  >  **Aktivera replikering**.
6. När de virtuella datorerna har Aktiver ATS för replikering kan du kontrol lera statusen för VM-hälsa under **replikerade objekt**

>[!NOTE]
>
> - Under den inledande replikeringen kan statusen ta lite tid att uppdatera, utan förlopp. Klicka på knappen **Uppdatera** för att hämta den senaste statusen.
> - Om en återställnings punkt inte har skapats under de senaste 60 minuterna kommer den virtuella datorns replikeringsstatus att bli kritiskt.

## <a name="next-steps"></a>Nästa steg

[Läs mer](site-recovery-test-failover-to-azure.md) om att köra ett redundanstest.
