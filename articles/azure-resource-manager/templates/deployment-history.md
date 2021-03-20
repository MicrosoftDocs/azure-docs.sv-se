---
title: Distributions historik
description: Beskriver hur du visar Azure Resource Manager distributions åtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 00e0c51244d5c191d4c9f05f689b90ece81ec5a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91284627"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributions historik med Azure Resource Manager

Med Azure Resource Manager kan du Visa distributions historiken. Du kan undersöka vissa åtgärder i tidigare distributioner och se vilka resurser som har distribuerats. Den här historiken innehåller information om eventuella fel.

Distributions historiken för en resurs grupp är begränsad till 800 distributioner. När du närmar dig gränsen raderas distributionerna automatiskt från historiken. Mer information finns i [automatiska borttagningar från distributions historiken](deployment-history-deletions.md).

Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Hämta distributioner och korrelations-ID

Du kan visa information om en distribution via Azure Portal, PowerShell, Azure CLI eller REST API. Varje distribution har ett korrelations-ID som används för att spåra relaterade händelser. Om du [skapar en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md)kan support be dig om korrelations-ID: t. Support använder korrelations-ID: t för att identifiera åtgärder för den misslyckade distributionen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj den resurs grupp som du vill undersöka.

1. Välj länken under **distributioner**.

   ![Välj distributions historik](./media/deployment-history/select-deployment-history.png)

1. Välj en av distributionerna från distributions historiken.

   ![Välj distribution](./media/deployment-history/select-details.png)

1. En sammanfattning av distributionen visas, inklusive korrelations-ID: t.

    ![Distributionssammanfattning](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa en lista över alla distributioner för en resurs grupp använder du kommandot [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Om du vill hämta en speciell distribution från en resurs grupp lägger du till parametern **DeploymentName** .

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Använd följande för att hämta korrelations-ID:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa en lista över distributionen av en resurs grupp använder du [AZ distributions grupp lista](/cli/azure/group/deployment#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Om du vill hämta en enskild distribution använder du [distributions gruppen AZ](/cli/azure/group/deployment#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Använd följande för att hämta korrelations-ID:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Om du vill visa en lista över distributioner för en resurs grupp använder du följande åtgärd. För det senaste API-versions numret som ska användas i begäran, se  [distributioner – lista efter resurs grupp](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

För att få en speciell distribution. Använd följande åtgärd. Det senaste API-versions numret som ska användas i begäran finns i [distributioner-get](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Svaret innehåller korrelations-ID: t.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Hämta distributions åtgärder och fel meddelande

Varje distribution kan innehålla flera åtgärder. Visa distributions åtgärder om du vill ha mer information om en distribution. När en distribution Miss lyckas innehåller distributions åtgärderna ett fel meddelande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I sammanfattningen för en distribution väljer du **Åtgärds information**.

    ![Välj åtgärds information](./media/deployment-history/get-operation-details.png)

1. Du ser informationen för det steget i distributionen. När ett fel uppstår innehåller informationen fel meddelandet.

    ![Visa åtgärds information](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa distributions åtgärderna för distribution till en resurs grupp använder du kommandot [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) .

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Om du vill visa misslyckade åtgärder filter åtgärder med **felaktigt** tillstånd.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Använd följande kommando för att hämta status meddelandet för misslyckade åtgärder:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa distributions åtgärderna för distribution till en resurs grupp använder du kommandot [AZ distribution operation Group List](/cli/azure/deployment/operation/group#az-deployment-operation-group-list) . Du måste ha Azure CLI-2.6.0 eller senare.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Om du vill visa misslyckade åtgärder filter åtgärder med **felaktigt** tillstånd.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Använd följande kommando för att hämta status meddelandet för misslyckade åtgärder:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Använd följande åtgärd för att hämta distributions åtgärder. Det senaste API-versions numret som ska användas i begäran finns i [distributions åtgärder-lista](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Svaret innehåller ett fel meddelande.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du löser specifika distributions fel finns i [lösa vanliga fel när du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).
* Information om hur distributioner hanteras i historiken finns i [automatiska borttagningar från distributions historik](deployment-history-deletions.md).
* Om du vill verifiera distributionen innan du kör den, se [distribuera en resurs grupp med Azure Resource Manager mall](deploy-powershell.md).

