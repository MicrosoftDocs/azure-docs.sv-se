---
title: Flytta en Azure Relay-namnrymd till en annan region
description: Den här artikeln visar hur du flyttar ett Azure Relay-namnområde från den aktuella regionen till en annan region.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89463827"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Flytta en Azure Relay-namnrymd till en annan region
Den här artikeln visar hur du flyttar ett Azure Relay-namnområde från en region till en annan region. Här följer de övergripande stegen:

1. **Exportera** relä namn området till en Azure Resource Manager-mall.
1. **Uppdaterings plats (region)** för resurser i mallen. Ta också bort alla **dynamiska** WCF-reläer från mallen. 

    WCF-reläer har två lägen. I det första läget skapas WCF Relay explicit med hjälp av Azure Portal-eller Azure Resource Manager-mallen. På sidan **WCF-reläer** i Azure Portal kan du se egenskapen **isDynamic** inställd på **false** för ett relä i det här läget. 

    I det andra läget genereras WCF Relay automatiskt när en lyssnare (Server) ansluter för en specifik slut punkts adress. Så länge lyssnaren är ansluten till reläet visas reläet i listan över WCF-reläer i Azure Portal. För ett relä i det här läget anges egenskapen **isDynamic** till **True** eftersom den genereras dynamiskt. Det dynamiska WCF-reläet försvinner när lyssnaren kopplas från. 
1. **Distribuera** resurser med hjälp av mallen i mål regionen.

## <a name="prerequisites"></a>Förutsättningar
Se till att Azure Relay-tjänsten är tillgänglig i mål regionen. Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall. Den här mallen innehåller inställningar som beskriver ditt Azure Relay namn område.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** och välj sedan din Azure Relay namn område.
3. Välj **Exportera mall** under **Inställningar** på den vänstra menyn.
4. Välj **Hämta** på sidan **Exportera mall** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Ladda ned Resource Manager-mall":::
5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller JSON-filer för mall och parametrar. 
1. Öppna filen **template.js** från den extraherade mappen i valfritt redigerings program.
1. Sök efter `location` och Ersätt värdet för egenskapen med det nya namnet för regionen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .
1. Ta bort definitioner av **dynamiska WCF Relay** -resurser (typ: `Microsoft.Relay/namespaces/WcfRelays` ). Dynamiska WCF-reläer är de som har egenskapen **isDynamic** inställd på **True** på sidan **reläs** . I följande exempel är **echoservice** ett dynamiskt WCF-relä och dess definition bör tas bort från mallen. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Dynamiska reläer":::

## <a name="move"></a>Flytta
Distribuera mallen för att skapa ett relä namn område i mål regionen. 

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
1. På sidan **resurs grupp** väljer du Azure Relay namn området. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Välj Azure Relay namnrymd":::    
1. På sidan **Azure Relay namn område** väljer du **hybridanslutningar** eller **WCF-reläer** på den vänstra menyn för att kontrol lera att hybrid anslutningar och WCF-reläer skapas. Om du har glömt att ta bort definitioner för dynamiska WCF-reläer innan du importerar mallen tar du bort dem på sidan **WCF-reläer** . De dynamiska WCF-reläerna skapas automatiskt när klienter ansluter till relä namn området. 

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Om du vill börja om efter distributionen kan du ta bort **mål Azure Relay namn området** och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flyttningen av ett namn område tar du bort **Azure Relay namn området** i käll regionen. 

Så här tar du bort ett Azure Relay-namnområde (källa eller mål) med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal skriver du **reläer** och väljer **reläer** från **tjänster** i Sök resultaten. Du ser alla Azure Relay namn rymder i en lista.
2. Välj det mål namn område som ska tas bort för att öppna sidan **relä** . 
1. På sidan **vidarebefordra** väljer du **ta bort** från verktygsfältet. 

    ![Ta bort namn område – knapp](./media/move-across-regions/delete-namespace-button.png)
3. På sidan **ta bort namn område** skriver du namnet på Azure Relay namn området för att bekräfta borttagningen och väljer sedan **ta bort**. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien flyttade du ett Azure Relay-namnområde från en region till en annan region. Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:

- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
