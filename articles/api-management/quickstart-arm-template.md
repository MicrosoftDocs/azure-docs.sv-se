---
title: Snabb start – skapa en Azure API Management-instans med hjälp av ARM-mall
description: Lär dig hur du skapar en Azure API Management-instans på Developer-nivån med hjälp av en Azure Resource Manager-mall (ARM-mall).
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 1610c51b613712c06410247e2eb673a50a9988b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792250"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>Snabb start: skapa en ny Azure API Management-tjänstinstans med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en Azure API Management-tjänstinstans (APIM). APIM hjälper organisationer att publicera API: er till externa, partner och interna utvecklare för att låsa upp potentiella data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga Server dels tjänster var som helst. Mer information finns i [Översikt](api-management-key-concepts.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-api-management-create/).

:::code language="json" source="~/quickstart-templates/101-azure-api-management-create/azuredeploy.json":::

Följande resurs definieras i mallen:

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

Fler mallar för Azure API Management-mallar finns i [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar en API Management tjänst instans med ett automatiskt genererat namn.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-api-management-create%2Fazuredeploy.json)

    I det här exemplet är instansen konfigurerad på nivån utvecklare, ett ekonomiskt alternativ för att utvärdera Azure API Management. Den här nivån är inte för produktions användning. Mer information om att skala API Management-nivåerna finns i avsnittet [Uppgradera och skala](upgrade-and-scale.md).

1. Välj eller ange följande värden.
    - **Prenumeration**: välj en Azure-prenumeration.
    - **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och välj sedan **OK**.
    - **Region**: Välj en plats för resurs gruppen. Exempel: **centrala USA**.
    - **E-post i Publisher**: Ange en e-postadress för att ta emot meddelanden.
    - **Utgivar namn**: Ange ett namn som du väljer för API-utgivaren.
    - **SKU**: Acceptera standardvärdet för **Developer**.
    - **SKU-antal**: Acceptera standardvärdet.
    - **Plats**: acceptera den genererade platsen för API Managements tjänsten.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="Egenskaper för API Management mall":::

1. Välj **Granska + skapa** och granska sedan de allmänna villkoren. Om du samtycker väljer du **skapa**.

    > [!TIP]
    >  Det kan ta mellan 30 och 40 minuter att skapa och aktivera en API Management-tjänst på Developer-nivån.

1. När instansen har skapats visas ett meddelande:

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="Distributionsmeddelande":::

 Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal för att kontrol lera de distribuerade resurserna eller Använd verktyg som Azure CLI eller Azure PowerShell för att visa en lista över de distribuerade resurserna.

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **API Management tjänster** och väljer den tjänst instans som du skapade.
1. Granska egenskaperna för din tjänst på **översikts** sidan.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="Sidan tjänst översikt":::

När din API Management tjänst instans är online är du redo att använda den. Börja med självstudien för att [Importera och publicera](import-and-publish.md) ditt första API.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande självstudier kanske du vill lämna API Management-instansen på plats. När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

1. I [Azure Portal](https://portal.azure.com)söker du efter och väljer **resurs grupper**. Du kan också välja **resurs grupper** på **Start** sidan.
1. På sidan **resurs grupper** väljer du din resurs grupp.
1. På sidan resurs grupp väljer du **ta bort resurs grupp**.

    :::image type="content" source="media/quickstart-arm-template/delete-resource-group.png" alt-text="Ta bort resursgrupp":::
1. Skriv namnet på resurs gruppen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: importera och publicera ditt första API](import-and-publish.md)
