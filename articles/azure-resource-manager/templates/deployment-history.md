---
title: Distributionshistorik
description: Beskriver hur du visar Azure Resource Manager distributionsåtgärder med portalen, PowerShell, Azure CLI och REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e7ed2096a696efdc9a2654a8fd0c294c82cbd4f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781873"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Visa distributionshistorik med Azure Resource Manager

Azure Resource Manager kan du visa distributionshistoriken. Du kan undersöka vissa åtgärder i tidigare distributioner och se vilka resurser som har distribuerats. Den här historiken innehåller information om eventuella fel.

Distributionshistoriken för en resursgrupp är begränsad till 800 distributioner. När du närmar dig gränsen tas distributioner automatiskt bort från historiken. Mer information finns i Automatiska [borttagningar från distributionshistoriken.](deployment-history-deletions.md)

Hjälp med att lösa specifika distributionsfel finns i Lösa vanliga fel när [du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Hämta distributioner och korrelations-ID

Du kan visa information om en distribution via Azure Portal, PowerShell, Azure CLI eller REST API. Varje distribution har ett korrelations-ID som används för att spåra relaterade händelser. Om du [skapar en Azure-supportbegäran](../../azure-portal/supportability/how-to-create-azure-support-request.md)kan supporten be dig om korrelations-ID:t. Stödet använder korrelations-ID:t för att identifiera åtgärderna för den misslyckade distributionen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj den resursgrupp som du vill undersöka.

1. Välj länken under **Distributioner**.

   ![Välj distributionshistorik](./media/deployment-history/select-deployment-history.png)

1. Välj en av distributionerna i distributionshistoriken.

   ![Välj distribution](./media/deployment-history/select-details.png)

1. En sammanfattning av distributionen visas, inklusive korrelations-ID:t.

    ![Distributionssammanfattning](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa en lista över alla distributioner för en resursgrupp använder du [kommandot Get-AzResourceGroupDeployment.](/powershell/module/az.resources/Get-AzResourceGroupDeployment)

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Om du vill hämta en specifik distribution från en resursgrupp lägger du till **parametern DeploymentName.**

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Om du vill hämta korrelations-ID:t använder du:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa en lista över distributionen för en resursgrupp använder [du az deployment group list](/cli/azure/group/deployment#az_deployment_group_list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Om du vill hämta en specifik distribution använder [du az deployment group show](/cli/azure/group/deployment#az_deployment_group_show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Om du vill hämta korrelations-ID:t använder du:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Om du vill visa en lista över distributioner för en resursgrupp använder du följande åtgärd. Det senaste API-versionsnumret som ska användas i begäran finns [i Distributioner – lista efter resursgrupp.](/rest/api/resources/deployments/listbyresourcegroup)

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

För att få en specifik distribution. använd följande åtgärd. Det senaste API-versionsnumret som ska användas i begäran finns [i Distributioner – Hämta](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

Svaret innehåller korrelations-ID:t.

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

## <a name="get-deployment-operations-and-error-message"></a>Hämta distributionsåtgärder och felmeddelande

Varje distribution kan innehålla flera åtgärder. Om du vill se mer information om en distribution kan du visa distributionsåtgärder. När en distribution misslyckas innehåller distributionsåtgärder ett felmeddelande.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I sammanfattningen för en distribution väljer du **Åtgärdsinformation.**

    ![Välj åtgärdsinformation](./media/deployment-history/get-operation-details.png)

1. Information om det steget i distributionen visas. När ett fel inträffar innehåller informationen felmeddelandet.

    ![Visa åtgärdsinformation](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill visa distributionsåtgärder för distribution till en resursgrupp använder du kommandot [Get-AzResourceGroupDeploymentOperation.](/powershell/module/az.resources/get-azdeploymentoperation)

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Om du vill visa misslyckade åtgärder filtrerar du åtgärder med **tillståndet Misslyckad.**

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Använd följande kommando för att hämta statusmeddelandet för misslyckade åtgärder:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill visa distributionsåtgärder för distribution till en resursgrupp använder du [kommandot az deployment operation group list.](/cli/azure/deployment/operation/group#az_deployment-operation-group-list) Du måste ha Azure CLI 2.6.0 eller senare.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Om du vill visa misslyckade åtgärder filtrerar du åtgärder med **tillståndet Misslyckad.**

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Använd följande kommando för att hämta statusmeddelandet för misslyckade åtgärder:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Använd följande åtgärd för att hämta distributionsåtgärder. Det senaste API-versionsnumret som ska användas i begäran finns i [Distributionsåtgärder – lista](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

Svaret innehåller ett felmeddelande.

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

* Information om hur du löser vissa distributionsfel finns i Lösa vanliga fel när [du distribuerar resurser till Azure med Azure Resource Manager](common-deployment-errors.md).
* Mer information om hur distributioner hanteras i historiken finns i [Automatiska borttagningar från distributionshistoriken.](deployment-history-deletions.md)
* Information om hur du verifierar distributionen innan du kör den [finns i Distribuera en resursgrupp med Azure Resource Manager mall](deploy-powershell.md).
