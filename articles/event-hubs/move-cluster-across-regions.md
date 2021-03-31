---
title: Flytta ett dedikerat kluster i Azure Event Hubs till en annan region | Microsoft Docs
description: Den här artikeln visar hur du flyttar ett dedikerat Azure Event Hubs-kluster från den aktuella regionen till en annan region.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 94be44ee8f9442a3a65e899d7a58524b2570f194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89381089"
---
# <a name="move-an-azure-event-hubs-dedicated-cluster-to-another-region"></a>Flytta ett dedikerat Azure Event Hubs-kluster till en annan region
Den här artikeln visar hur du exporterar en Azure Resource Manager mall för ett befintligt Event Hubs dedikerat kluster och sedan använder mallen för att skapa ett kluster med samma konfigurations inställningar i en annan region. 

Om du har andra resurser, till exempel namn rymder och händelse nav i den Azure-resurs grupp som innehåller Event Hubs-klustret, kanske du vill exportera mallen på resurs grupps nivå så att alla relaterade resurser kan flyttas till den nya regionen i ett enda steg. Stegen i den här artikeln visar hur du exporterar ett **Event Hubs-kluster** till mallen. Stegen för att exportera en **resurs grupp** till mallen liknar varandra. 

## <a name="prerequisites"></a>Förutsättningar
Se till att det dedikerade klustret kan skapas i mål regionen. Det enklaste sättet att ta reda på är att använda Azure Portal för att försöka [skapa ett Event Hubs dedikerat kluster](event-hubs-dedicated-cluster-create-portal.md). Du ser listan över regioner som stöds vid tidpunkten för att skapa klustret. 

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall. Den här mallen innehåller inställningar som beskriver ditt Event Hubs dedikerade kluster.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **alla resurser** och välj sedan Event Hubs dedikerat kluster.
3. Välj   >  **Exportera mall** för > inställningar.
4. Välj **Hämta** på sidan **Exportera mall** .

    :::image type="content" source="./media/move-cluster-across-regions/download-template.png" alt-text="Ladda ned Resource Manager-mall" lightbox="./media/move-cluster-across-regions/download-template.png":::
5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp.

   Den här zip-filen innehåller JSON-filerna som innehåller mallen och skripten för att distribuera mallen.


## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett Event Hubs dedikerat kluster i mål regionen. 


1. I Azure Portal väljer du **skapa en resurs**.
2. I **Sök på Marketplace** skriver du **mall distribution** och väljer **malldistribution (distribuera med anpassade mallar)**.
5. Välj **Bygg en egen mall i redigeraren**.
6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.
1. Uppdatera värdet för `location` egenskapen så att den pekar på den nya regionen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .
1. Spara mallen genom att välja **Spara** . 
1. Följ dessa steg på sidan **Anpassad distribution** : 
    1. Välj en Azure- **prenumeration**. 
    2. Välj en befintlig **resurs grupp** eller skapa en. 
    3. Välj **målplats eller region** . Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 
    4. Utför följande steg i avsnittet **Inställningar** :    
        1. Ange det nya **kluster namnet**. 

            :::image type="content" source="./media/move-cluster-across-regions/deploy-template.png" alt-text="Distribuera Resource Manager-mall":::
    5. Välj **Granska + skapa** längst ned på sidan. 
    1. På sidan **Granska + skapa** granskar du inställningarna och väljer sedan **skapa**.  

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Efter distributionen kan du, om du vill börja om, ta bort **mål Event Hubs dedikerat kluster** och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flytten av ett Event Hubs kluster tar du bort **Event Hubs klustret** i den ursprungliga regionen. 

Ta bort ett Event Hubs kluster (källa eller mål) med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, skriver du **Event Hubs kluster** och väljer **Event Hubs kluster** från Sök resultat. Du ser Event Hubs-klustret i en lista.
2. Välj det kluster som du vill ta bort och välj **ta bort** från verktygsfältet. 
3. Bekräfta borttagningen genom att skriva **kluster namnet** på sidan **ta bort kluster** och välj sedan **ta bort**. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du flyttar ett Event Hubs dedikerat kluster från en region till en annan. 

Mer information om hur du flyttar ett namn område från en region till en annan region finns i artikeln [flytta Event Hubs namn områden i flera regioner](move-across-regions.md) . 

Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:

- [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Migrera virtuella Azure-datorer till en annan region](../site-recovery/azure-to-azure-tutorial-migrate.md)
