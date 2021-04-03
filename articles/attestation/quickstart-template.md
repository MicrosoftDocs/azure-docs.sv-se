---
title: Skapa ett Azure attesterings certifikat med hjälp av Azure Resource Manager mall
description: Lär dig hur du skapar ett Azure attesterings certifikat med hjälp av Azure Resource Manager mall.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 10/16/2020
ms.openlocfilehash: 8c56a37ebcc799b0170785666212eb4e3b00a5f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144973"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Snabb start: skapa en Azure attesterings-Provider med en ARM-mall

[Microsoft Azure attestering](overview.md) är en lösning för att intyga betrodda körnings miljöer (tees). Den här snabb starten fokuserar på processen att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa en Microsoft Azure attesterings princip.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

:::code language="json" source="~/quickstart-templates/101-attestation-provider-create/azuredeploy.json":::

Azure-resurser som definierats i mallen:

- Microsoft. attestering/attestationProviders

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna mallen.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

1. Välj eller ange följande värden.

    Om den inte har angetts använder du standardvärdet för att skapa en attesterings leverantör.

    - **Leverantörs namn för attestering**: Välj ett namn för din Azure attesterings-Provider.
    - **Plats**: Välj en plats. Välj till exempel **USA, centrala**.
    - **Taggar**: Välj en plats. Välj till exempel **USA, centrala**.

1. Välj **Köp**. När du har distribuerat attesterings resursen får du ett meddelande.

Azure-portalen används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan använda Azure Portal för att kontrol lera attesterings resursen.

## <a name="clean-up-resources"></a>Rensa resurser

Andra Azure-attesteringar bygger på den här snabb starten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.

När de inte längre behövs tar du bort resurs gruppen, som tar bort attesterings resursen. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en attesterings resurs med en ARM-mall och validerade distributionen. Mer information om Azure-attestering finns i [Översikt över Azure-attestering](overview.md).
