---
title: Skapa en Azure Notification Hub med Azure Resource Manager-mall
description: Lär dig hur du skapar en Azure Notification Hub med hjälp av en Azure Resource Manager-mall (ARM-mall).
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sethm
ms.date: 08/04/2020
ms.reviewer: thsomasu
ms.lastreviewed: 05/15/2020
ms.openlocfilehash: 2165351bee4ee260e768c10b23e26bf095779cad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88684651"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Snabb start: skapa en Notification Hub med en ARM-mall

Azure Notification Hubs tillhandahåller en lättanvänd och skalad push-motor som gör att du kan skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle osv.) från alla Server delar (moln eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

I den här snabb starten används en Azure Resource Manager-mall (ARM-mall) för att skapa ett Azure Notification Hubs-namnområde och en Notification-hubb med namnet **MyHub** inom det namn området.

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft. NotificationHubs/namnrymder](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft. NotificationHubs/Namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen använder ett Notification Hubs namn områdes namn som parameter. Mallen skapar sedan ett namn område med det namnet och en Notification Hub med namnet **MyHub** inom denna namnrymd.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrol lera de distribuerade resurserna eller använda Azure CLI eller Azure PowerShell skript för att visa en lista över det distribuerade Notification Hubs namn området och hubben:

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzNotificationHub -Namespace "nhtestns123" -ResourceGroup "ContosoNotificationsGroup"
Get-AzNotificationHubsNamespace -Namespace "nhtestns123"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az notification-hub show --resource-group ContosoNotificationsGroup --namespace-name nhtestns123 --name MyHub
az notification-hub namespace show --resource-group ContosoNotificationsGroup --name nhtestns123
```

---
The output looks similar to: null
':::image type="content" source="media/create-notification-hub-template/verify-deploy.png" alt-text="Verify deployment"::': null
---

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, som tar bort resurserna i resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
