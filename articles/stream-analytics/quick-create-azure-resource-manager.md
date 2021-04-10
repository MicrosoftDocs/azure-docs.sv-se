---
title: Snabb start – skapa ett Azure Stream Analytics jobb genom Azure Resource Manager mall
description: Den här snabb starten visar hur du använder Azure Resource Manager-mallen för att skapa ett Azure Stream Analytics jobb.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 2e8e7890adcb6b91d79bbf4e119488e365410d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020085"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-an-arm-template"></a>Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall

I den här snabb starten använder du en Azure Resource Manager mall (ARM-mall) för att skapa ett Azure Stream Analytics jobb. När jobbet har skapats verifierar du distributionen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

För att slutföra den här artikeln måste du:

* Ha en Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/).

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json":::

Den Azure-resurs som definierats i mallen är [Microsoft. StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): skapa ett Azure Stream Analytics jobb.

## <a name="deploy-the-template"></a>Distribuera mallen

I det här avsnittet skapar du ett Azure Stream Analytics jobb med ARM-mallen.

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Stream Analytics jobb.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Ange de värden som krävs för att skapa ditt Azure Stream Analytics-jobb.

   ![Skapa Azure Stream Analytics jobb med en Azure Resource Manager-mall](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Skapa Azure Stream Analytics jobb med en Azure Resource Manager-mall")

   Ange följande värden:

   |Egenskap  |Beskrivning  |
   |---------|---------|
   |**Prenumeration**     | I listrutan väljer du din Azure-prenumeration.        |
   |**Resursgrupp**     | Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översikten över Azure-resursgrupper](../azure-resource-manager/management/overview.md). |
   |**Region**     | Välj **USA, östra**. För andra tillgängliga regioner läser du informationen om [Azure-tjänsttillgänglighet per region](https://azure.microsoft.com/regions/services/).        |
   |**Stream Analytics jobbnamn**     | Ange ett namn för ditt Stream Analytics jobb.      |
   |**Antal enheter för strömning**     |  Välj antalet enheter för strömning som du behöver. Mer information finns i [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md).       |

3. Välj **Granska + skapa** och klicka sedan på **Skapa**.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrol lera Azure Stream Analytics jobb eller använda följande Azure CLI-eller Azure PowerShell skript för att visa en lista över resursen.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med efterföljande självstudier kanske du vill lämna resurserna på plats. När de inte längre behövs tar du bort resurs gruppen, som tar bort Azure Stream Analyticss jobbet. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Azure Stream Analytics jobb genom att använda en ARM-mall och validerat distributionen. Fortsätt till nästa artikel och lär dig hur du exporterar en ARM-mall för ett befintligt jobb med VS Code.

> [!div class="nextstepaction"]
> [Exportera en jobb ARM-mall för Azure Stream Analytics](resource-manager-export.md)
