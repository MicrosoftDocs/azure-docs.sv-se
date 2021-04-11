---
title: Självstudie – konfigurera haveri beredskap för virtuella Windows-datorer med Azure Site Recovery
description: Lär dig hur du aktiverar haveri beredskap för virtuella Windows-datorer till en annan Azure-region med hjälp av tjänsten Azure Site Recovery.
author: rayne-wiselman
ms.service: virtual-machines
ms.collection: windows
ms.subservice: recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: fd5d8c3e2c6e4ee5556568ebd23ac99b48300e9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382038"
---
# <a name="tutorial-enable-disaster-recovery-for-windows-vms"></a>Självstudie: Aktivera haveri beredskap för virtuella Windows-datorer

Den här självstudien visar hur du konfigurerar haveri beredskap för virtuella Azure-datorer som kör Windows. I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Aktivera haveri beredskap för en virtuell Windows-dator
> * Kör en haveri beredskap för att kontrol lera att den fungerar som förväntat
> * Stoppa replikeringen av den virtuella datorn efter detalj nivån

När du aktiverar replikering för en virtuell dator installeras Site Recovery mobilitets tjänst tillägget på den virtuella datorn och registrerar det med [Azure Site Recovery](../../site-recovery/site-recovery-overview.md). Vid replikering skickas de virtuella dator diskarna till ett cache Storage-konto i käll regionen. Data skickas därifrån till mål regionen och återställnings punkterna genereras från data.  När du växlar över en virtuell dator under haveri beredskap används en återställnings punkt för att skapa en virtuell dator i mål regionen.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

1. Kontrol lera att din Azure-prenumeration låter dig skapa en virtuell dator i mål regionen. Om du precis har skapat ditt kostnads fria Azure-konto är du administratör för prenumerationen och du har de behörigheter som du behöver.
2. Om du inte är prenumerations administratör kan du samar beta med administratören för att tilldela dig:
    - Antingen den inbyggda rollen för den virtuella datorn eller vissa behörigheter för att:
        - Skapa en virtuell dator i det valda virtuella nätverket.
        - Skriv till ett Azure Storage-konto.
        - Skriv till en Azure-hanterad disk.
    - Den inbyggda rollen Site Recovery Contributor för att hantera Site Recovery åtgärder i valvet. 
3. Vi rekommenderar att du använder en virtuell Windows-dator som kör Windows Server 2012 eller senare. Den virtuella dator disken bör inte vara krypterad med den här självstudien.
4. Om utgående anslutningar för virtuella datorer använder en URL-baserad proxy, se till att den har åtkomst till dessa URL: er. Det finns inte stöd för att använda en autentiserad proxy.

    **Namn** | **Offentligt moln** | **Myndighets moln** | **Information**
    --- | --- | --- | ---
    Storage | `*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`| Skriv data från den virtuella datorn till cache Storage-kontot i käll regionen. 
    Azure AD  | `login.microsoftonline.com` | `login.microsoftonline.us`| Auktorisera och autentisera till Site Recovery tjänst-URL: er. 
    Replikering | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`  |VM-kommunikation med tjänsten Site Recovery. 
    Service Bus | `*.servicebus.windows.net` | `*.servicebus.usgovcloudapi.net` | VM skriver till Site Recovery övervakning och diagnostikdata. 

4. Om du använder nätverks säkerhets grupper (NSG: er) för att begränsa nätverks trafiken för virtuella datorer skapar du NSG-regler som tillåter utgående anslutning (HTTPS 443) för den virtuella datorn med hjälp av dessa service märken (grupper av IP-adresser). Testa reglerna på en test-NSG först.

    **Tag** | **Tillåt** 
    --- | --- 
    Lagrings tag gen | Tillåter att data skrivs från den virtuella datorn till cache Storage-kontot.
    Azure AD-tagg | Tillåter åtkomst till alla IP-adresser som motsvarar Azure AD.
    EventsHub-tagg | Ger åtkomst till Site Recovery övervakning.
    AzureSiteRecovery-tagg | Tillåter åtkomst till tjänsten Site Recovery i vilken region som helst.
    GuestAndHybridManagement | Använd om du vill uppgradera den Site Recovery mobilitets agent som körs på virtuella datorer som är aktiverade för replikering.
5.  På virtuella Windows-datorer installerar du de senaste Windows-uppdateringarna för att se till att de virtuella datorerna har de senaste rot certifikaten.

## <a name="create-a-vm-and-enable-disaster-recovery"></a>Skapa en virtuell dator och aktivera haveri beredskap

Du kan också aktivera haveri beredskap när du skapar en virtuell dator.

1. [Skapa en virtuell dator](quick-create-portal.md).
2. På fliken **hantering** väljer du **Aktivera haveri beredskap**.
3. I **sekundär region** väljer du den mål region dit du vill replikera en virtuell dator för haveri beredskap.
4. I **sekundär prenumeration** väljer du den mål prenumeration som den virtuella mål datorn ska skapas i. Den virtuella mål datorn skapas när du växlar över den virtuella käll datorn från käll regionen till mål regionen.
5. I **Recovery Services valv** väljer du det valv som du vill använda för replikeringen. Om du inte har något valv väljer du **Skapa nytt**. Välj en resurs grupp där du vill placera valvet och ett valv namn.
6. I **Site Recovery princip** lämnar du standard principen eller väljer **Skapa ny** för att ange anpassade värden.

    - Återställnings punkter skapas från ögonblicks bilder av virtuella dator diskar som tas vid en viss tidpunkt. När du växlar över en virtuell dator använder du en återställnings punkt för att återställa den virtuella datorn i mål regionen. 
    - En kraschad återställnings punkt skapas var femte minut. Den här inställningen kan inte ändras. En krasch-konsekvent ögonblicks bild fångar upp data som fanns på disken när ögonblicks bilden togs. Det innehåller inte något i minnet. 
    - Som standard är Site Recovery krasch-konsekventa återställnings punkter i 24 timmar. Du kan ange ett anpassat värde mellan 0 och 72 timmar.
    - En programkonsekvent ögonblicks bild tas var fjärde timme. En programkonsekvent ögonblicks bild 
    - Som standard lagrar Site Recovery återställnings punkter i 24 timmar.

7. I **tillgänglighets alternativ** anger du om den virtuella datorn ska distribueras som fristående, i en tillgänglighets zon eller i en tillgänglighets uppsättning.

    ::: bildtyp = "content" source = "./Media/tutorial-Disaster-Recovery/create-vm.png" Alt-text = "Aktivera replikering på egenskaps sidan för VM-hantering."

8. Slutför skapandet av den virtuella datorn.

## <a name="enable-disaster-recovery-for-an-existing-vm"></a>Aktivera haveri beredskap för en befintlig virtuell dator

Använd den här proceduren om du vill aktivera haveri beredskap på en befintlig virtuell dator i stället för en ny virtuell dator.

1. Öppna sidan Egenskaper för virtuell dator i Azure Portal.
2. I **Åtgärder** väljer du **Haveriberedskap**.

    :::image type="content" source="./media/tutorial-disaster-recovery/existing-vm.png" alt-text="Öppna Disaster Recovery-alternativ för en befintlig virtuell dator.":::

3. I **grunderna**, om den virtuella datorn har distribuerats i en tillgänglighets zon, kan du välja haveri beredskap mellan tillgänglighets zoner.
4. I **mål region** väljer du den region som du vill replikera den virtuella datorn till. Käll-och mål regionerna måste vara i samma Azure Active Directory-klient.

    :::image type="content" source="./media/tutorial-disaster-recovery/basics.png" alt-text="Ange grundläggande katastrof återställnings alternativ för en virtuell dator.":::

5. Välj **Nästa: avancerade inställningar**.
6. I **Avancerade inställningar** kan du granska inställningar och ändra värden till anpassade inställningar. Site Recovery speglar som standard käll inställningarna för att skapa mål resurser.

    - **Mål prenumeration**. Den prenumeration som den virtuella mål datorn skapas i efter redundansväxlingen.
    - **Mål resurs grupp för virtuell dator**. Resurs gruppen där den virtuella mål datorn skapas efter en redundansväxling.
    - **Mål för virtuellt nätverk**. Det virtuella Azure-nätverket där den virtuella mål datorn finns när den har skapats efter redundansväxlingen.
    - **Tillgänglighet för mål**. När den virtuella mål datorn skapas som en enskild instans, i en tillgänglighets uppsättning eller tillgänglighets zon.
    - **Närhets placering**. Om det är tillämpligt väljer du den närhets placerings grupp där den virtuella mål datorn finns efter redundansväxlingen.
    - **Lagrings inställningar – cache Storage-konto**. Återställningen använder ett lagrings konto i käll regionen som ett tillfälligt data lager. Ändringar i den virtuella käll datorn cachelagras i det här kontot innan de replikeras till mål platsen.
        - Som standard skapas ett cache Storage-konto per valv och återanvänds.
        - Du kan välja ett annat lagrings konto om du vill anpassa cache-kontot för den virtuella datorn.
    - **Lagrings inställningar – replik hanterad disk**. Som standard skapar Site Recovery replik Managed disks i mål regionen.
        -  Som standard hanteras den hanterade disk speglingen för den virtuella käll datorn med samma lagrings typ (standard hård disk/SSD eller Premium SSD).
        - Du kan anpassa lagrings typen efter behov.
    - **Replikeringsinställningar**. Visar valvet som den virtuella datorn finns i och den replikeringsprincip som används för den virtuella datorn. Som standard behålls återställnings punkter som skapats av Site Recovery för den virtuella datorn i 24 timmar.
    - **Inställningar för tillägg**. Anger att Site Recovery hanterar uppdateringar av Site Recovery Mobility Service-tillägget som är installerat på virtuella datorer som du replikerar.
        - Det angivna Azure Automation-kontot hanterar uppdaterings processen.
        - Du kan anpassa Automation-kontot.

    :::image type="content" source="./media/tutorial-disaster-recovery/settings-summary.png" alt-text="Sida som visar en sammanfattning av inställningarna för mål och replikering.":::


6. Välj **Granska + starta replikering**.

7. Välj **starta replikering**. Distributionen startar och Site Recovery börjar skapa mål resurser. Du kan övervaka replikeringens förlopp i meddelandena.

    :::image type="content" source="./media/tutorial-disaster-recovery/notifications.png" alt-text="Meddelande om replikeringens förlopp.":::

## <a name="check-vm-status"></a>Kontrol lera VM-status

När replikeringen är klar kan du kontrol lera statusen för VM-replikeringen.

1. Öppna sidan Egenskaper för virtuell dator.
2. I **Åtgärder** väljer du **Haveriberedskap**.
3. Expandera avsnittet **Essentials** om du vill granska standardinställningarna för valvet, replikeringsprincipen och mål inställningarna.
4. I **tillståndet hälsa och status** får du information om replikeringstillståndet för den virtuella datorn, agent versionen, redundansen och de senaste återställnings punkterna. 

    :::image type="content" source="./media/tutorial-disaster-recovery/essentials.png" alt-text="Vyn Essentials för återställning av virtuella datorer.":::

5. I **vyn infrastruktur** får du en visuell översikt över käll-och mål datorer, hanterade diskar och cache Storage-kontot.

    :::image type="content" source="./media/tutorial-disaster-recovery/infrastructure.png" alt-text="infrastrukturens visuella karta för haveri beredskap för virtuella datorer.":::


## <a name="run-a-drill"></a>Kör en detalj granskning

Kör en detalj granskning för att se till att katastrof återställning fungerar som förväntat. När du kör ett redundanstest skapas en kopia av den virtuella datorn, utan påverkan på pågående replikering eller i produktions miljön. 

1. På sidan VM haveri beredskap väljer du **redundanstest**.
2. Lämna den **senast förädlade** inställningen för återställnings punkten i **redundanstestning**.

   Det här alternativet ger det lägsta återställnings punkt målet (återställnings punkt) och vanligt vis den snabbaste rotationen av den virtuella mål datorn. Först bearbetar den alla data som har skickats till Site Recovery-tjänsten, för att skapa en återställnings punkt för varje virtuell dator, innan de växlar över till den. Den här återställnings punkten har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.

3. Välj det virtuella nätverk där den virtuella datorn ska placeras efter redundansväxlingen. 

     :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-settings.png" alt-text="Sidan för att ange alternativ för redundanstest.":::

4. Processen för redundanstest börjar. Du kan övervaka förloppet i meddelanden.

    :::image type="content" source="./media/tutorial-disaster-recovery/test-failover-notification.png" alt-text="Meddelanden om redundanstest."::: 
    
   När redundanstestning har slutförts är den virtuella datorn i *vänte* läge för rensning av redundanstest väntar på sidan **Essentials** . 



## <a name="clean-up-resources"></a>Rensa resurser

Den virtuella datorn rensas automatiskt av Site Recovery efter granskningen.

1. Om du vill starta automatisk rensning väljer du **rensning** av redundanstest.

    :::image type="content" source="./media/tutorial-disaster-recovery/start-cleanup.png" alt-text="Starta rensning på sidan Essentials."::: 

2. I **rensning av redundanstest** skriver du in de anteckningar som du vill registrera för redundansväxlingen och väljer sedan **testningen är klar. Ta bort den virtuella datorn för redundanstest**. Välj sedan **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test.png" alt-text="För att registrera anteckningar och ta bort den virtuella test datorn."::: 

7. Borttagnings processen påbörjas. Du kan övervaka förloppet i aviseringar.

    :::image type="content" source="./media/tutorial-disaster-recovery/delete-test-notification.png" alt-text="Aviseringar för att övervaka ta bort virtuell test dator."::: 

### <a name="stop-replicating-the-vm"></a>Stoppa replikering av den virtuella datorn

När du har slutfört en haveri beredskaps granskning rekommenderar vi att du fortsätter med en fullständig redundansväxling. Om du inte vill göra en fullständig redundans kan du inaktivera replikering. Det här gör följande:

- Tar bort den virtuella datorn från Site Recovery listan över replikerade datorer.
- Stoppar Site Recovery debitering för den virtuella datorn.
- Rensar automatiskt inställningarna för käll replikering.

Stoppa replikering på följande sätt:

1. På sidan VM haveri beredskap väljer du **inaktivera replikering**.
2. I **inaktivera replikering** väljer du de orsaker som du vill inaktivera replikeringen. Välj sedan **OK**.

    :::image type="content" source="./media/tutorial-disaster-recovery/disable-replication.png" alt-text="Sidan om du vill inaktivera replikering och ange en orsak."::: 


Site Recovery tillägget som har installerats på den virtuella datorn under replikeringen tas inte bort automatiskt. Om du inaktiverar replikering för den virtuella datorn och du inte vill replikera den igen vid ett senare tillfälle kan du ta bort Site Recovery tillägget manuellt, enligt följande: 

1. Gå till tillägg för VM > **Inställningar**  >  .
2. På sidan **tillägg** väljer du alla *Microsoft. Azure. RecoveryServices* -poster för Linux.
3. På sidan Egenskaper för tillägget väljer du **Avinstallera**.

   :::image type="content" source="./media/tutorial-disaster-recovery/uninstall-extension.png" alt-text="För att avinstallera Site Recovery VM-tillägget.":::



## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du haveri beredskap för en virtuell Azure-dator och körde en haveri beredskaps granskning. Nu kan du utföra en fullständig redundans för den virtuella datorn.

> [!div class="nextstepaction"]
> [Redundansväxla en virtuell dator till en annan region](../../site-recovery/azure-to-azure-tutorial-dr-drill.md)
