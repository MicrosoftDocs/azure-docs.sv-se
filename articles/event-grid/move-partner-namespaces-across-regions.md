---
title: Flytta Azure Event Grid partner namn områden till en annan region
description: Den här artikeln visar hur du flyttar Azure Event Grid partner namn områden från en region till en annan region.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89087212"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Flytta Azure Event Grid partner namn områden till en annan region
Du kanske vill flytta dina resurser till en annan region av olika anledningar. Till exempel för att dra nytta av en ny Azure-region för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven. 

Här följer de övergripande steg som beskrivs i den här artikeln: 

- **Exportera partnerns namn områdes** resurs till en Azure Resource Manager-mall. Ta bort definitioner för händelse kanal resurser i mallen. En händelse kanal kan ha en referens till Azure Resource Manager-ID för partner ämnet, som ägs av en kund. De kan därför inte skapas med hjälp av mallen i mål regionen.  
- **Använd mallen för att distribuera partner namn området** till mål regionen. Skapa sedan händelse kanaler i namn området ny partner i mål regionen. 
- Ta bort partner namn området från käll regionen för att **slutföra flyttningen**. 

    > [!NOTE]
    > - Att exportera **partner ämnen** till en Azure Resource Manager-mall stöds inte eftersom kunder inte kan skapa ett partner ämne direkt. 
    > - **Partner registreringar** är globala resurser (inte kopplade till någon speciell region), så att du flyttar dem från en region till en annan region inte är tillämplig. 

## <a name="prerequisites"></a>Förutsättningar
- Se till att tjänsten Event Grid är tillgänglig i mål regionen. Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Förbereda
Kom igång genom att exportera en Resource Manager-mall för partner namn området. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I Sök fältet högst upp skriver du **Event Grid partner namnrum** och väljer **Event Grid partner namn rymder** från resultat listan. 
3. Välj det **partner namn område** som du vill exportera till en Resource Manager-mall. 
4. På sidan **Event Grid partner namn område** väljer du **Exportera mall** under **Inställningar** på den vänstra menyn och väljer sedan **Hämta** i verktygsfältet. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Exportera mall – > hämtning" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Leta upp **zip** -filen som du laddade ned från portalen och zippa upp filen till en valfri mapp. Den här zip-filen innehåller mallar och parametrar JSON-filer. 
1. Öppna **template.js** i valfritt redigerings program. 
8. Uppdatering `location` för **ämnes** resursen till mål regionen eller platsen. För att hämta plats koder, se [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Koden för en region är region namnet utan mellanslag, till exempel `West US` är lika med `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Spara** mallen. 

## <a name="recreate"></a>Återskapa 
Distribuera mallen för att skapa ett namn område för partner i mål regionen. 

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
    1. För **plats** väljer du mål regionen. Om du har valt en befintlig resurs grupp är den här inställningen skrivskyddad. 
    1. För **namnet på partner namn området** anger du ett namn för den nya partnerns namn område. 
    1. För partner registreringens externa ID anger du resurs-ID för partner registreringen i följande format: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Markera kryss rutan **Jag accepterar villkoren som anges ovan** .     
    1. Välj **Granska + skapa** för att starta distributions processen. 
    1. På sidan **Granska + skapa** granskar du inställningarna och väljer **skapa**. 

## <a name="discard-or-clean-up"></a>Ta bort eller rensa
Om du vill slutföra flyttningen tar du bort partner namn området i käll regionen.  

Om du vill börja om tar du bort partner namn området i mål regionen och upprepar stegen i avsnitten [förbereda](#prepare) och [Återskapa](#recreate) i den här artikeln.

Ta bort ett partner namn område med hjälp av Azure Portal:

1. I fönstret Sök högst upp i Azure Portal, skriver du **Event Grid partners namnrum** och väljer **Event Grid partner namn rymder** från Sök resultat. 
2. Välj den partner namn område som ska tas bort och välj **ta bort** från verktygsfältet. 
3. **Bekräfta** borttagningen för att ta bort partner namn området. 

## <a name="next-steps"></a>Nästa steg
Du har lärt dig hur du flyttar ett Event Grid partner namn område från en region till en annan region. I följande artiklar finns information om hur du flyttar system ämnen, anpassade ämnen och domäner i olika regioner.

- [Flytta system ämnen i flera regioner](move-system-topics-across-regions.md). 
- [Flytta anpassade ämnen mellan regioner](move-custom-topics-across-regions.md). 
- [Flytta domäner mellan regioner](move-domains-across-regions.md).

Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i följande artikel: [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).