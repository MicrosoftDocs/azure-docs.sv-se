---
title: Skapa en Azure-meddelandehubb med Azure Resource Manager mall
description: Lär dig hur du skapar en Azure-meddelandehubb med hjälp Azure Resource Manager mall (ARM-mall).
services: notification-hubs
author: sethmanheim
ms.author: sethm
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 05/15/2020
ms.topic: quickstart
ms.service: notification-hubs
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: a328d6b8942c3209e13dc91a2fb892e98e3016f6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533482"
---
# <a name="quickstart-create-a-notification-hub-using-an-arm-template"></a>Snabbstart: Skapa en meddelandehubb med hjälp av en ARM-mall

Azure Notification Hubs tillhandahåller en lättanvänd och utskalade push-motor som gör att du kan skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle osv.) från valfri backend (i molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs](notification-hubs-push-notification-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

I den här snabbstarten används en Azure Resource Manager mall (ARM-mall) för att skapa ett Azure Notification Hubs-namnområde och en meddelandehubb med namnet **MyHub** inom det namnområdet.

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-notification-hub/).

:::code language="json" source="~/quickstart-templates/101-notification-hub/azuredeploy.json":::

* [Microsoft.NotificationHubs/namespaces](/azure/templates/microsoft.notificationhubs/namespaces)
* [Microsoft.NotificationHubs/namespaces/notificationHubs](/azure/templates/microsoft.notificationhubs/namespaces/notificationhubs)

## <a name="deploy-the-template"></a>Distribuera mallen

Välj följande bild för att logga in på Azure och öppna en mall. Mallen tar ett Notification Hubs namnområdesnamn som en parameter. Mallen skapar sedan ett namnområde med det namnet och en meddelandehubb med **namnet MyHub** inom det namnområdet.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-notification-hub%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrollera de distribuerade resurserna eller använda Azure CLI eller Azure PowerShell-skript för att visa en lista över Notification Hubs-namnområdet och hubben:

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

Ta bort resursgruppen när den inte längre behövs, vilket tar bort resurserna i resursgruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

En stegvis självstudiekurs som vägleder dig genom processen med att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: Skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
