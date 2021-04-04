---
title: Skapa ditt Azure Maps-konto med en ARM-mall | Microsoft Azure Maps
description: Lär dig hur du skapar ett Azure Maps konto med en Azure Resource Manager ARM-mall (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525188"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Skapa ditt Azure Maps-konto med en ARM-mall

Du kan skapa ditt Azure Maps-konto med en Azure Resource Manager ARM-mall (ARM). När du har ett konto kan du implementera API: erna på din webbplats eller i ett mobil program.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

För att slutföra den här artikeln:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

Azure Maps konto resursen definieras i den här mallen:

* [**Microsoft. Maps/konton**](/azure/templates/microsoft.maps/accounts): skapa ett Azure Maps-konto.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Maps-konto.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    ![ARM-mall distribuera Portal](./media/how-to-create-template/create-account-using-template-portal.png)

    Om den inte anges använder du standardvärdet för att skapa ditt Azure Maps-konto.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats. Till exempel **USA, västra 2**.
    * **Konto namn**: Ange ett namn för ditt Azure Maps konto, som måste vara globalt unikt.
    * **Pris nivå**: Välj lämplig pris nivå. standardvärdet för mallen är S0.

3. Välj **Granska + skapa**. 
4. Bekräfta inställningarna på sidan Granska och klicka på **skapa**. När du har distribuerat Azure Maps får du ett meddelande:

    ![ARM-mall distribuera Portal meddelande](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal används för att distribuera mallen. Du kan också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att kontrol lera ditt Azure Maps konto och Visa dina nycklar. Du kan också använda följande Azure CLI-skript för att lista dina konto nycklar.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som också tar bort det Azure Maps kontot. Ta bort resurs gruppen med hjälp av Azure CLI:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Maps och Azure Resource Manager fortsätter du till artiklarna nedan.

- Skapa ett Azure Maps [demo program](quick-demo-map-app.md)
- Lär dig mer om [arm-mallar](../azure-resource-manager/templates/overview.md)