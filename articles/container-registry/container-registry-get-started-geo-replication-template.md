---
title: Snabbstart – Skapa geo-replikerat register – Azure Resource Manager mall
description: Lär dig hur du skapar ett geo-replikerat Azure-containerregister med hjälp av Azure Resource Manager mall.
services: azure-resource-manager
author: dlepow
ms.author: danlep
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: c59c7897054b2ad65a76353e6d886af46cac91e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537431"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Snabbstart: Skapa ett geo-replikerat containerregister med hjälp av en ARM-mall

Den här snabbstarten visar hur du skapar en Azure Container Registry instans med hjälp av en Azure Resource Manager mall (ARM-mall). Mallen uppsättningar ett [geo-replikerat register](container-registry-geo-replication.md) som automatiskt synkroniserar registerinnehåll i mer än en Azure-region. Geo-replikering ger nätverksåtkomst till avbildningar från regionala distributioner, samtidigt som du får en enda hanteringsupplevelse. Det är en funktion på [premium-registertjänstnivån.](container-registry-skus.md)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Mallen uppsättningar ett register och ytterligare en regional replik.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Följande resurser definieras i mallen:

* **[Microsoft.ContainerRegistry/registries:](/azure/templates/microsoft.containerregistry/registries)** skapa ett Azure-containerregister
* **[Microsoft.ContainerRegistry/registries/replications: skapa](/azure/templates/microsoft.containerregistry/registries/replications)** en containerregisterreplik

Fler Azure Container Registry mallexempel finns i [snabbstartsmallgalleriet](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

 1. Välj följande bild för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Välj eller ange följande värden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp:** välj **Skapa ny,** ange ett unikt namn för resursgruppen och välj sedan **OK.**
    * **Region**: Välj en plats för resursgruppen. Exempel: **USA, centrala.**
    * **Acr Name (Acr-namn):** acceptera det genererade namnet för registret eller ange ett namn. Det måste vara globalt unikt.
    * **Acr Admin User Enabled (Acr Admin-användare** aktiverad): acceptera standardvärdet.
    * **Plats:** acceptera den genererade platsen för registrets hemreplik eller ange en plats, till exempel **USA, centrala.** 
    * **Acr Sku:** acceptera standardvärdet.
    * **Acr Replica Location (Plats** för Acr-replik): Ange en plats för registerrepliken med hjälp av regionens korta namn. Den måste vara annorlunda än hemregistrets plats. Exempel: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Mallegenskaper":::

1. Välj **Granska +** skapa och granska sedan villkoren. Om du godkänner väljer du **Skapa**.

1. När registret har skapats får du ett meddelande:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portalmeddelande":::

 Azure-portalen används för att distribuera mallen. Förutom den här Azure Portal kan du använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../azure-resource-manager/templates/deploy-cli.md)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal eller ett verktyg som Azure CLI för att granska egenskaperna för containerregistret.

1. I portalen söker du efter Containerregister och väljer det containerregister som du skapade.

1. På sidan **Översikt** noterar du **registrets** inloggningsserver. Använd den här URI:n när du använder Docker för att tagga och skicka avbildningar till registret. Mer information finns i [Push your first image using the Docker CLI (Skicka din första avbildning med Docker CLI).](container-registry-get-started-docker-cli.md)

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Registeröversikt":::

1. På sidan **Replikeringar** bekräftar du platserna för hemrepliken och repliken som lagts till via mallen. Om du vill kan du lägga till fler repliker på den här sidan.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Registerreplikering":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resursgruppen, registret och registerrepliken. Det gör du genom att gå till Azure Portal, markera den resursgrupp som innehåller registret och sedan välja Ta **bort resursgrupp.**

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Ta bort resursgrupp":::

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure Container Registry med en ARM-mall och konfigurerade en registerreplik på en annan plats. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry](container-registry-tutorial-prepare-registry.md)

En stegvis självstudiekurs som vägleder dig genom processen med att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
