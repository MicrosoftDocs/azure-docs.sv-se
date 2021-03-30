---
title: Flytta en Service Bus-namnrymd till en annan region | Microsoft Docs
description: Den här artikeln visar hur du flyttar ett Azure Service Bus-namnområde från den aktuella regionen till en annan region.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88861258"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Flytta en Azure Service Bus-namnrymd till en annan region
Det finns olika scenarier där du vill flytta ditt befintliga Service Bus-namnområde från en region till en annan. Du kanske till exempel vill skapa ett namn område med samma konfiguration för testning. Du kanske också vill skapa ett sekundärt namn område i en annan region som en del av [Disaster Recovery-planeringen](service-bus-geo-dr.md).

Här följer de övergripande stegen:

1. Exportera Service Bus-namnrymden i den aktuella regionen till en Azure Resource Manager-mall. 
1. Uppdaterings plats för resurser i mallen. Ta också bort standard prenumerations filtret från mallen eftersom du inte kan skapa en standard regel eftersom den skapas automatiskt åt dig. 
1. Använd mallen för att distribuera Service Bus namn området till mål regionen. 
1. Kontrol lera distributionen för att säkerställa att namn området, köer, ämnen och prenumerationer för ämnen skapas i mål regionen. 
1. Slutför flyttningen genom att ta bort namn området från käll regionen när alla meddelanden har bearbetats. 

## <a name="prerequisites"></a>Förutsättningar
Se till att Azure Service Bus och funktioner som ditt konto använder stöds i mål regionen.
 
## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall. Den här mallen innehåller inställningar som beskriver ditt Service Bus namn område.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** och välj sedan din Service Bus namn område.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **Hämta** på sidan **Exportera mall** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Ladda ned Resource Manager-mall":::
5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller JSON-filer för mall och parametrar. 
1. Öppna filen template.jsi den extraherade mappen. 
1. Sök efter `location` och Ersätt värdet för egenskapen med det nya namnet för regionen eller platsen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .
1. Ta bort definitioner av resurser av typen: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Glöm inte att ta bort kommatecken ( `,` ) som föregår det här avsnittet för att behålla JSON-giltig.  

    > [!NOTE]
    > Du kan inte skapa en standard regel för en prenumeration med hjälp av en Resource Manager-mall. Standard regeln skapas automatiskt när prenumerationen skapas i mål regionen. 

## <a name="move"></a>Flytta
Distribuera mallen för att skapa ett Service Bus-namnområde i mål regionen. 

1. I Azure Portal väljer du **skapa en resurs**.
2. I **Sök på Marketplace** skriver du **mall distribution** för sök texten, väljer **malldistribution (distribuera med anpassade mallar)** och trycker sedan på **RETUR**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Distribution av ny mall":::    
1. På sidan **malldistribution** väljer du **skapa**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Ny mall distribution-knappen Skapa":::        
1. På sidan **Anpassad distribution** väljer du **Bygg en egen mall i redigeraren**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Bygg en egen mall i redigeraren – länk":::            
1. På sidan **Redigera mall** väljer du **Läs in fil** i verktygsfältet och följer sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Välj mall":::                
1. Spara mallen genom att välja **Spara** . 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Spara mall":::                    
1. Följ dessa steg på sidan **Anpassad distribution** : 
    1. Välj en Azure- **prenumeration**. 
    2. Välj en befintlig **resurs grupp** eller skapa en. 
    3. Välj **målplats eller region** . Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 
    4. Ange ett nytt **namn för namn området**.
    1. Välj **Granska + skapa**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Distribuera Resource Manager-mall":::
    1. På sidan **Granska + skapa** väljer du **skapa** längst ned på sidan. 
    
## <a name="verify"></a>Verifiera
1. När distributionen har slutförts väljer **du gå till resurs grupp**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Gå till resurs grupps länk":::    
1. På sidan **resurs grupp** väljer du Service Bus namn området. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Välj Service Bus namnrymd":::    
1. På sidan **Service Bus namn område** kontrollerar du att du ser köer, ämnen och prenumerationer från käll regionen. 
    1. Du ser **köer** i namn området längst ned i den högra rutan.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Köer i namn området":::
    2. Växla till fliken **ämnen** för att se ämnen i namn området
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Ämnen i namn området":::
    3. Välj ämnet för att verifiera att prenumerationer skapas. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Ämnes prenumerationer":::      
    
    

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Efter distributionen kan du, om du vill börja om, ta bort **mål Service Bus namn området** och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flyttningen av en Service Bus-namnrymd tar du bort **käll Service Bus namn området**. Se till att bearbeta alla meddelanden innan du tar bort namn området. 

Så här tar du bort ett Service Bus-namnområde (källa eller mål) med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal skriver du **Service Bus** och väljer **Service Bus** från Sök resultat. Du ser Service Bus namnrum i en lista.
2. Välj det mål namn område som ska tas bort och välj **ta bort** från verktygsfältet. 

    ![Ta bort namn område – knapp](./media/move-across-regions/delete-namespace-button.png)
3. På sidan **ta bort resurser** kontrollerar du de markerade resurserna och bekräftar borttagningen genom att skriva **Ja** och väljer sedan **ta bort**. 

    Annat alternativ är att ta bort resurs gruppen som har Service Bus-namnområdet. På sidan **resurs grupp** väljer du **ta bort resurs grupp** i verktygsfältet och bekräftar sedan borttagningen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure Service Bus-namnområde från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:

- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
