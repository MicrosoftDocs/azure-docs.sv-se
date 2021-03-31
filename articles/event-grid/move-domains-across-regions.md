---
title: Flytta Azure Event Grid domäner till en annan region
description: Den här artikeln visar hur du flyttar Azure Event Grid domäner från en region till en annan region.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89087213"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Flytta Azure Event Grid domäner till en annan region
Du kanske vill flytta dina resurser till en annan region av olika anledningar. Till exempel för att dra nytta av en ny Azure-region för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven. 

Här följer de övergripande steg som beskrivs i den här artikeln: 

- **Exportera domän** resursen till en Azure Resource Manager-mall. 

    > [!IMPORTANT]
    > Domän resursen och ämnena i domänen exporteras till mallen. Prenumerationer på domän ämnen exporteras inte. 
- **Använd mallen för att distribuera domänen** till mål regionen. 
- **Skapa prenumerationer för domän ämnen manuellt** i mål regionen. När du exporterade domänen till en mall i den aktuella regionen exporteras inte prenumerationer för domän ämnen. Därför skapar du dem efter att avsnitten domän och domän har skapats i mål regionen. 
- **Verifiera distributionen**. Skicka en händelse till ett domän ämne i domänen och kontrol lera att händelse hanteraren som är associerad med prenumerationen har anropats. 
- Ta bort domänen från käll regionen för att **slutföra flyttningen**. 

## <a name="prerequisites"></a>Förutsättningar
- Se till att tjänsten Event Grid är tillgänglig i mål regionen. Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall för domänen. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I Sök fältet skriver du **Event Grid domäner** och väljer **Event Grid domäner** i resultat listan. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Sök efter och välj Event Grid domäner":::
3. Välj den **domän** som du vill exportera till en Resource Manager-mall. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Välj domän":::   
4. På sidan **Event Grid domän** väljer du **Exportera mall** under **Inställningar** på den vänstra menyn och väljer sedan **Hämta** i verktygsfältet. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Exportera mall – > hämtning" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Avsnitt om domäner och domäner exporteras. Prenumerationer för avsnitt om domäner exporteras inte. Så du måste skapa prenumerationer för domän ämnen när du har flyttat domän ämnen. 
5. Leta upp **zip** -filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller mallar och parametrar JSON-filer. 
1. Öppna **template.js** i valfritt redigerings program. 
8. Uppdatering `location` för **domän** resursen till mål regionen eller platsen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Spara** mallen. 

## <a name="recreate"></a>Återskapa 
Distribuera mallen för att skapa domän-och domän ämnen i mål regionen. 

1. I Azure Portal väljer du **skapa en resurs**.
2. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.
3. Välj **malldistribution**.
4. Välj **Skapa**.
5. Välj **Bygg en egen mall i redigeraren**.
6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du laddade ned i det sista avsnittet.
7. Spara mallen genom att välja **Spara** . 
8. Följ dessa steg på sidan **Anpassad distribution** :
    1. Välj en Azure- **prenumeration**. 
    1. Välj en befintlig **resurs grupp** i mål regionen eller skapa en. 
    1. För **region** väljer du mål regionen. Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 
    1. Ange ett nytt namn för domänen för **domän namnet**. 
    1. Välj **Granska + skapa**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Distribuera mallen":::        
    1. När verifieringen av mallen lyckas väljer du **skapa** längst ned på sidan för att distribuera resursen. 
    1. När distributionen har slutförts väljer du **gå till resurs grupp** för att navigera till resurs grupp sidan. Bekräfta att det finns en domän i resurs gruppen. Välj domän. Bekräfta att det finns domän ämnen i domänen. 

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Ta bort domänen i käll regionen för att slutföra flyttningen.  

Om du vill börja om tar du bort domänen i mål regionen och upprepar stegen i avsnitten [förbereda](#prepare) och [Återskapa](#recreate) i den här artikeln.

Så här tar du bort en domän med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, skriver du **Event Grid domäner** och väljer **Event Grid domäner** från Sök resultat. 
2. Välj den domän som du vill ta bort och välj **ta bort** från verktygsfältet. 
3. På sidan bekräftelse anger du namnet på resurs gruppen och väljer **ta bort**.  

Ta bort resurs gruppen som innehåller domänen med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, Skriv **resurs grupper** och välj **resurs grupper** från Sök resultat. 
2. Välj den resurs grupp som ska tas bort och välj **ta bort** från verktygsfältet. 
3. På sidan bekräftelse anger du namnet på resurs gruppen och väljer **ta bort**.  

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du flyttar en Event Grid-domän från en region till en annan region. I följande artiklar finns information om hur du flyttar system ämnen, anpassade ämnen och partners namn områden i olika regioner.

- [Flytta system ämnen i flera regioner](move-system-topics-across-regions.md). 
- [Flytta anpassade ämnen mellan regioner](move-custom-topics-across-regions.md). 
- [Flytta namn rymder för partner över regioner](move-partner-namespaces-across-regions.md).

Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i följande artikel: [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).