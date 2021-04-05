---
title: Snabb start – skapa geo-replikerad register-Azure Resource Manager mall
description: Lär dig hur du skapar ett geo-replikerat Azure Container Registry med hjälp av en Azure Resource Manager-mall.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/06/2020
ms.openlocfilehash: 97b556e0329644b973def8333ddb5e70e370b0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826973"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>Snabb start: skapa ett geo-replikerat behållar register med en ARM-mall

Den här snabb starten visar hur du skapar en Azure Container Registry-instans med hjälp av en Azure Resource Manager-mall (ARM-mall). Mallen konfigurerar ett [geo-replikerat](container-registry-geo-replication.md) register, som automatiskt synkroniserar register innehåll i mer än en Azure-region. Geo-replikering ger nätverks nära åtkomst till bilder från regionala distributioner, samtidigt som en enda hanterings upplevelse tillhandahålls. Det är en funktion i tjänst nivån [Premium](container-registry-skus.md) Registry.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/). Mallen konfigurerar ett register och en ytterligare regional replik.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

Följande resurser definieras i mallen:

* **[Microsoft. ContainerRegistry/register](/azure/templates/microsoft.containerregistry/registries)**: skapa ett Azure Container Registry
* **[Microsoft. ContainerRegistry/register/replikeringar](/azure/templates/microsoft.containerregistry/registries/replications)**: skapa en behållare register replik

Du hittar fler Azure Container Registry mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

 1. Välj följande bild för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 1. Välj eller ange följande värden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och välj sedan **OK**.
    * **Region**: Välj en plats för resurs gruppen. Exempel: **centrala USA**.
    * **ACR namn**: acceptera det genererade namnet för registret eller ange ett namn. Det måste vara globalt unikt.
    * **ACR admin-användare aktive rad**: Acceptera standardvärdet.
    * **Plats**: acceptera den genererade platsen för registrets hem replik eller ange en plats som t. ex. **Central USA**. 
    * **ACR SKU**: Acceptera standardvärdet.
    * **ACR-replik plats**: Ange en plats för register repliken med regionens korta namn. Det måste skilja sig från start register platsen. Exempel: **westeurope**.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Mallegenskaper":::

1. Välj **Granska + skapa** och granska sedan de allmänna villkoren. Om du samtycker väljer du **skapa**.

1. När du har skapat registret får du ett meddelande:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portal meddelande":::

 Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal eller ett verktyg som Azure CLI för att granska egenskaperna för behållar registret.

1. I portalen söker du efter behållar register och väljer det behållar register som du har skapat.

1. Notera **inloggnings servern** för registret på sidan **Översikt** . Använd den här URI: n när du använder Docker för att tagga och skicka avbildningar till registret. Mer information finns i [skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="Register översikt":::

1. På sidan **replikeringar** bekräftar du platserna för hem repliken och repliken som lagts till via mallen. Om du vill kan du lägga till fler repliker på den här sidan.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Register-replikeringar":::

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resurs gruppen, registret och register repliken. Om du vill göra det går du till Azure Portal, väljer den resurs grupp som innehåller registret och väljer sedan **ta bort resurs grupp**.

:::image type="content" source="media/container-registry-get-started-geo-replication-template/delete-resource-group.png" alt-text="Ta bort resursgrupp":::

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container Registry med en ARM-mall och konfigurerade en register replik på en annan plats. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Självstudier för Azure Container Registry](container-registry-tutorial-prepare-registry.md)

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
