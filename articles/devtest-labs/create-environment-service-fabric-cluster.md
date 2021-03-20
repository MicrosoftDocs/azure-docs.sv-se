---
title: Skapa en Service Fabric kluster miljö i Azure DevTest Labs
description: Lär dig hur du skapar en miljö med ett fristående Service Fabric kluster och hur du startar och stoppar klustret med hjälp av scheman.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85484017"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Skapa en miljö med fristående Service Fabric kluster i Azure DevTest Labs
Den här artikeln innehåller information om hur du skapar en miljö med ett fristående Service Fabric kluster i Azure DevTest Labs. 

## <a name="overview"></a>Översikt
DevTest Labs kan skapa självständiga test miljöer som definieras av Azure Resource Management-mallar. Dessa miljöer innehåller både IaaS-resurser, t. ex. virtuella datorer och PaaS-resurser, t. ex. Service Fabric. Med DevTest Labs kan du hantera virtuella datorer i en miljö genom att tillhandahålla kommandon för att kontrol lera de virtuella datorerna. Dessa kommandon ger dig möjlighet att starta eller stoppa en virtuell dator enligt ett schema. På samma sätt kan DevTest Labs också hjälpa dig att hantera Service Fabric kluster i en miljö. Du kan starta eller stoppa ett Service Fabric kluster i en miljö antingen manuellt eller via ett schema.

## <a name="create-a-service-fabric-cluster"></a>Skapa ett Service Fabric-kluster
Service Fabric kluster skapas med hjälp av miljöer i DevTest Labs. Varje miljö definieras av en Azure Resource Manager-mall på en git-lagringsplats. Den [offentliga git-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) för DevTest Labs innehåller Resource Manager-mallen för att skapa ett Service Fabric kluster i mappen [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) . 

1. Börja med att skapa ett labb i Azure DevTest Labs genom att följa anvisningarna i följande artikel: [skapa ett labb](devtest-lab-create-lab.md). Observera att alternativet **offentliga miljöer** är **aktiverat** som standard. 
2. Bekräfta att Service Fabric-providern har registrerats för din prenumeration genom att följa dessa steg:
    1. Välj **prenumerationer** i den vänstra navigerings menyn och välj din **prenumeration**
    2. På sidan **prenumeration** väljer du **resurs leverantörer** i avsnittet **Inställningar** på den vänstra menyn. 
    3. Om **Microsoft. ServiecFabric** inte är registrerad väljer du **Registrera**. 
3. På **DevTest Lab** -sidan för ditt labb väljer du **+ Lägg till** i verktygsfältet. 
    
    ![Knappen Lägg till i verktygsfältet](./media/create-environment-service-fabric-cluster/add-button.png)
3. På sidan **Välj en bas** väljer du **Service Fabric Lab-kluster** i listan. 

    ![Välj Service Fabric Lab-kluster i listan](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Utför följande steg på sidan **Konfigurera inställningar** : 
    1. Ange ett **namn** för kluster **miljön**. Detta är namnet på resurs gruppen i Azure som Service Fabric klustret ska skapas i. 
    2. Välj **operativ system (OS)** för de virtuella kluster datorerna. Standardvärdet är: **Windows**.
    3. Ange ett namn för klustrets **administratör** . 
    4. Ange ett **lösen ord** för administratören. 
    5. För **certifikatet** anger du certifikat informationen som en Base64-kodad sträng. Gör så här för att skapa ett certifikat:
        1. Ladda ned **Create-ClusterCertificate.ps1** -filen från [git-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Du kan också klona lagrings platsen på din dator. 
        2. Starta **PowerShell**. 
        3. Kör **ps1** -filen med hjälp av kommandot `.\Create-ClusterCertificate.ps1` . Du ser en textfil som öppnats i anteckningar med den information som du behöver för att fylla i de certifikat-relaterade fälten på den här sidan. . 
    6. Ange **lösen ordet för certifikatet**.
    7. Ange **tumavtryck** för certifikatet.
    8. Välj **Lägg till** på sidan **Konfigurera inställningar** . 

        ![Konfigurera kluster inställningar](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. När klustret har skapats visas en resurs grupp med namnet på den miljö som du angav i föregående steg. När du expanderar visas Service Fabric klustret i det. Om statusen för resurs gruppen fastnar vid **skapande** väljer du **Uppdatera** i verktygsfältet. **Service Fabric kluster** miljö skapar ett 5-nod 1-NodeType-kluster på antingen Linux eller Windows.

    I följande exempel är **mysfabricclusterrg** namnet på resurs gruppen som har skapats specifikt för Service Fabric klustret. Det är viktigt att notera att Lab-miljöer är fristående i resurs gruppen där de skapas. Det innebär att mallen som definierar miljön, som endast kan komma åt resurser i den nyligen skapade resurs gruppen eller [virtuella nätverk som har kon figurer ATS för användning av labbet](devtest-lab-configure-vnet.md). Exemplet ovan skapar alla nödvändiga resurser i samma resurs grupp.

    ![Klustret har skapats](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Starta eller stoppa klustret
Du kan starta eller stoppa klustret från antingen själva DevTest-sidan eller från sidan Service Fabric kluster som tillhandahålls av DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Från DevTest Lab-Sidan
Du kan starta eller stoppa klustret på DevTest Lab-sidan för ditt labb. 

1. Välj **tre punkter (...)** för Service Fabric klustret som det visas i följande bild: 

    ![Starta och stoppa kommandon för klustret](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Du ser två kommandon i snabb menyn för att **Starta** och **stoppa** klustret. Start kommandot startar alla noder i ett kluster. Kommandot stoppa stoppar alla noder i ett kluster. När ett kluster har stoppats behålls Service Fabric själva klustret i ett klart tillstånd, men inga noder är tillgängliga förrän Start kommandot utfärdas på klustret i labbet.

    Det finns några saker att tänka på när du använder ett Service Fabric kluster i en test miljö. Det kan ta lite tid innan det Service Fabric klustret har startats om helt efter att noderna har startats om. Om du vill spara data från avstängning till start måste data sparas på en hanterad disk som är ansluten till den virtuella datorn. Det finns prestanda konsekvenser när du använder en ansluten hanterad disk, så det rekommenderas endast för test miljöer. Om disken som används för data lagring inte har säkerhetskopierats försvinner data när kommandot stoppa utfärdas i klustret.

### <a name="from-the-service-fabric-cluster-page"></a>Från sidan Service Fabric kluster 
Det finns ett annat sätt att starta eller stoppa klustret. 

1. Välj ditt Service Fabric-kluster i trädvyn på sidan DevTest Lab. 

    ![Välj ditt kluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. På sidan **Service Fabric kluster** för klustret kan du se kommandon i verktygsfältet för att starta eller stoppa klustret. 

    ![Starta eller stoppa kommandon på sidan Service Fabric kluster](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Konfigurera schema för automatisk start och automatisk avstängning
Service Fabric kluster kan också startas eller stoppas enligt ett schema. Den här upplevelsen liknar upplevelsen för virtuella datorer i ett labb. För att spara pengar stängs som standard varje kluster som skapats i ett labb automatiskt vid den tidpunkt som definieras i labb [stängnings principen](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy). Du kan åsidosätta genom att ange om klustret ska stängas av eller genom att ange tiden då klustret stängs av. 

![Befintliga scheman för automatisk start och automatisk avstängning](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Välj automatiskt start schema
Gör så här för att välja Start schema:

1. Välj **automatisk start** på den vänstra menyn
2. Välj **på** för att **tillåta att det här Service Fabric-klustret schemaläggs för automatisk start**. Den här sidan är endast aktive rad om labb ägaren har tillåtit användare att Autostarta sina virtuella datorer eller Service Fabric kluster.
3. Välj **Spara** i verktygsfältet. 

    ![Sida för automatisk stjärna](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Konfigurera inställningar för automatisk avstängning 
Gör så här om du vill ändra inställningarna för avstängning:

1. Välj **Automatisk avstängning** på den vänstra menyn. 
2. På den här sidan kan du välja automatisk avstängning genom att välja **av** för **aktive rad**. 
3. Följ dessa steg om du **har valt** **aktive rad för aktive rad**:
    1. Ange **tiden** för avstängning.
    2. Ange tids **zonen** för tiden. 
    3. Ange om du vill att DevTest Labs ska skicka **meddelanden** före automatisk avstängning. 
    4. Om du har valt **Ja** för meddelande alternativet anger du **webhook-URL: en** och/eller **e-postadress** för att skicka meddelanden. 
    5. Välj **Spara** i verktygsfältet.

        ![Stäng sida automatiskt](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Visa noder i Service Fabric-klustret
Den Service Fabric kluster sidan som du har sett i stegen tidigare är speciell för sidan med DevTest Labs. Det visar inte noderna i klustret. Följ dessa steg om du vill ha mer information om klustret:

1. På **DevTest Lab** -sidan för ditt labb väljer du **resurs gruppen** i trädvyn i avsnittet **mina virtuella datorer** .

    ![Välj resursgrupp](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. På sidan **resurs grupp** visas alla resurser i resurs gruppen i en lista. Välj **Service Fabric kluster** i listan. 

    ![Välj ditt kluster i listan](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Du ser sidan **Service Fabric kluster** för klustret. Detta är sidan som Service Fabric tillhandahåller. Du ser all information om kluster, till exempel noder, nodtyper osv.

    ![Service Fabric kluster start sida](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Nästa steg
I följande artiklar finns information om miljöer: 

- [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md)
- [Konfigurera och använda offentliga miljöer i Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
