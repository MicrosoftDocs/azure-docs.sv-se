---
title: Så här stoppar du övervakningen av Azure Kubernetes Service kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan avbryta övervakningen av ditt Azure AKS-kluster med Container Insights.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 619b6fc4cce860e5869fd0b31e303b4a474f8428
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774033"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>Så här stoppar du övervakningen av Azure Kubernetes Service (AKS) med Container Insights

När du har aktivera övervakning av ditt AKS-kluster kan du sluta övervaka klustret om du inte längre vill övervaka det. Den här artikeln visar hur du gör detta med hjälp av Azure CLI eller med de Azure Resource Manager mallarna.  


## <a name="azure-cli"></a>Azure CLI

Använd kommandot [az aks disable-addons för](/cli/azure/aks#az_aks_disable_addons) att inaktivera Container insights. Kommandot tar bort agenten från klusternoderna, den tar inte bort lösningen eller de data som redan har samlats in och lagrats i Azure Monitor resurs.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Om du vill återaktivera övervakning för klustret kan du [se Aktivera övervakning med Azure CLI.](container-insights-enable-new-cluster.md#enable-using-azure-cli)

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Det finns två Azure Resource Manager för att stödja borttagning av lösningsresurserna konsekvent och upprepade gånger i resursgruppen. En är en JSON-mall som anger konfigurationen för att stoppa övervakningen och den andra innehåller parametervärden som du konfigurerar för att ange AKS-klustrets resurs-ID och resursgrupp som klustret distribueras i.

Om du inte är bekant med begreppet att distribuera resurser med hjälp av en mall kan du se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>Mallen måste distribueras i samma resursgrupp i klustret. Om du utelämnar andra egenskaper eller tillägg när du använder den här mallen kan det leda till att de tas bort från klustret. Till exempel *enableRBAC* för Kubernetes RBAC-principer som implementeras i klustret, eller *aksResourceTagValues* om taggar har angetts för AKS-klustret.  
>

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. Kör för att identifiera din `az --version` version. Om du behöver installera eller uppgradera Azure CLI kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

### <a name="create-template"></a>Skapa mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Spara den här filen **OptOutTemplate.jstill** en lokal mapp.

3. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Redigera värdena för **aksResourceId** och **aksResourceLocation** med hjälp av värdena för AKS-klustret, som du hittar på **sidan** Egenskaper för det valda klustret.

    ![Sidan Containeregenskaper](media/container-insights-optout/container-properties-page.png)

    När du är på sidan **Egenskaper kopierar** du även arbetsytans **resurs-ID.** Det här värdet krävs om du vill ta bort Log Analytics-arbetsytan senare. Borttagning av Log Analytics-arbetsytan utförs inte som en del av den här processen.

    Redigera värdena för **aksResourceTagValues så att de** matchar de befintliga taggvärden som angetts för AKS-klustret.

5. Spara den här filen **OptOutParam.jstill** en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med Azure CLI

Kör följande kommando med Azure CLI på Linux för att ta bort lösningen och rensa konfigurationen i ditt AKS-kluster.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Ta bort lösningen med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kör följande PowerShell-kommandon i mappen som innehåller mallen för att ta bort lösningen och rensa konfigurationen från ditt AKS-kluster.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Konfigurationsändringen kan ta några minuter att slutföra. När det är klart returneras ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Nästa steg

Om arbetsytan endast har skapats för att stödja övervakning av klustret och det inte längre behövs måste du ta bort det manuellt. Om du inte vet hur du tar bort en arbetsyta kan du gå [till Ta bort en Azure Log Analytics-arbetsyta med Azure Portal](../logs/delete-workspace.md). Glöm inte **resurs-ID:t för** arbetsytan som du kopierade tidigare i steg 4. Du behöver det.