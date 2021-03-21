---
title: Fortanix-konfidentiell dator hanterare i ett Azure-hanterat program
description: Lär dig hur du distribuerar Fortanix-hanteraren för konfidentiella data behandling (CCM) i ett hanterat program i Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563429"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix-konfidentiell dator hanterare i ett Azure-hanterat program

Den här artikeln visar hur du distribuerar ett program som hanteras av Fortanix-hanteraren för konfidentiella data behandling i Azure Portal.

Fortanix är en program varu leverantör från tredje part med produkter och tjänster som bygger på Azure-infrastrukturen. Det finns andra leverantörer från tredje part som erbjuder liknande tjänster för konfidentiell behandling på Azure.

> [!NOTE]
>De produkter som refereras i detta dokument är inte understyrda av Microsoft. Microsoft tillhandahåller den här informationen endast som en bekvämlighet, och referensen till dessa produkter från andra tillverkare än Microsoft kräver inte Microsoft.

## <a name="prerequisites"></a>Förutsättningar

- Ett privat Docker-register för att skicka konverterade program avbildningar.
- Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) innan du börjar.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Distribuera en konfidentiell data behandlings hanterare via ett Azure-hanterat program

1. Gå till [Azure-portalen](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure Portal.":::

2. I Sök fältet söker du efter "Fortanix konfidentiell dator ansvarig" och du hittar Marketplace-listan för Fortanix CCM. Välj **Fortanix-konfidentiell dator hanterare på Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace-lista.":::

3. Sidan där du skapar CCM-hanterat program öppnas. Välj **skapa**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Skapa program.":::

4. Fyll i alla obligatoriska fält.
   1. I avsnittet information om hanterade program kommer fältet för den **hanterade resurs gruppen** att ha ett standardvärde som användaren kan ändra om de behöver.
   2. I fältet **region** väljer du antingen **östra Australien**, sydöstra **Australien**, **östra USA**, **västra USA 2**, Västeuropa, **Nord Europa** **,** **centrala** Kanada, **Östra Kanada** eller **östra USA 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Obligatoriska fält":::

   Välj **Granska + skapa** för att skapa Fortanix CCM-hanterat program.

5. Granska informationen och när verifieringen är klar markerar du kryss rutan **Jag godkänner villkoren ovan** och väljer sedan **skapa** för att skapa det hanterade programmet.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Granska information.":::

6. Fortanix CCM-distributionen startar och meddelar att distributionen pågår.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Distributions förlopp.":::

7. När distributionen är klar väljer du **gå till resurs** för att gå till sidan Översikt över distribuerat CCM-hanterade program för att registrera Compute-noden.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Skärm bild som visar en lyckad distribution i Azure Portal.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Skärm bild som visar en översikt över den konfidentiella dator resursen i Azure Portal.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Registrera Compute-noden i Fortanix CCM

1. Välj **konfidentiell dator hantering** i den vänstra navigerings menyn. Logga in på Fortanix CCM och skapa ett konto som du ser i **bild 9**.

    Mer information om hur du registrerar dig, loggar in och skapar ett konto i CCM finns i [ccm komma igång](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Skärm bild som visar inloggningen Fortanix konfidentiell dator hantering.":::
    
2. Om du vill hämta anslutnings-token från hanterings konsolen för CCM väljer du först knappen **Registrera nod** . I fönstret registrera nod väljer du sedan knappen **Kopiera** för att kopiera kopplings-token.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Skärm bild som visar Hämta kopplings-token.":::

3. Nu när du vill registrera en Node-agent väljer du fliken **konfidentiellt data behandling Node agent** och väljer **Lägg** till för att lägga till en CCM-Node-agent.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Skärm bild som visar hur du lägger till Node-agenten.":::

4.  Fyll i alla obligatoriska fält i formuläret agent för CCM-Node. Klistra in den kopplings-token som du kopierade i steg 2 i **kopplings-token**. Välj **Granska och skicka** för att bekräfta.

    Mer information om hur du registrerar en CCM-datornod finns i [Registrera Compute Node](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Skärm bild som visar hur du registrerar Compute-noden.":::
    
5. När verifieringen är klar väljer du **Skicka** för att slutföra skapandet av Node-agenten.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Skärm bild som visar att Node-agenten har skapats.":::

6. Om du vill kontrol lera distributions statusen går du till fliken **Översikt** och väljer **hanterad resurs grupp** länk.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Skärm bild som visar noden registreras.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Skärm bild som visar kontrollerar distributionens status.":::

7. Nu kommer du att märka att distributions statusen fortfarande pågår och tar några minuter innan nodens agent registreras.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Skärm bild som visar pågående distribution.":::

8. När registreringen av Node-agenten lyckas ändras statusen till "lyckades".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Skärm bild som visar distributionen lyckades.":::

9. Nu i det CCM-hanterade programmet går du till sidan Compute Nodes och du ser att noden är i ett **aktivt** tillstånd och har registrerats.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Skärm bild som visar att noden har registrerats.":::

## <a name="clean-up-resources"></a>Rensa resurser

Användaren kan också ta bort en CCM-Node-agent från sidan med agenten för konfidentiell indata-noden. Om du vill ta bort Node-agenten väljer du Node-agenten och klickar på knappen **ta bort** i det översta fältet.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Skärm bild som visar borttagning av Node-agenten.":::

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du registrerat en nod med hjälp av en Azure-hanterad app till Fortanix-hanteraren för konfidentiell bearbetning. Med noden registrering kan du konvertera program avbildningen så att den körs ovanpå en virtuell dator med konfidentiella data behandling. Mer information om konfidentiell dator användning av virtuella datorer i Azure finns i [lösningar på Virtual Machines](virtual-machine-solutions.md).

Mer information om Azures tjänster för konfidentiell behandling finns i [Azures konfidentiella data behandling](overview.md).

Lär dig hur du utför liknande uppgifter med andra tredjepartsprogram i Azure, t. ex. [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) och [Scone](https://sconedocs.github.io).

