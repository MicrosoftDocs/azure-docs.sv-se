---
title: Så här stoppar du övervakningen av ditt Azure Red Hat OpenShift v3-kluster | Microsoft Docs
description: Den här artikeln beskriver hur du kan stoppa övervakningen av ditt Azure Red Hat OpenShift-kluster med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7e6ab46940ed29a98b3988c00c92d6c691d6e0f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625979"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Så här stoppar du övervakning av Azure Red Hat OpenShift v3-kluster

>[!IMPORTANT]
> Azure Red Hat OpenShift 3,11 kommer att dras tillbaka juni 2022.
>
> Från och med oktober 2020 kommer du inte längre att kunna skapa nya 3,11-kluster.
> Befintliga 3,11-kluster fortsätter att köras fram till den 2022 juni men kommer inte längre att kunna användas efter det datumet.
>
> Följ den här guiden för att [skapa ett Azure Red Hat OpenShift 4-kluster](../../openshift/tutorial-create-cluster.md).
> Om du har frågor kan du [kontakta oss](mailto:aro-feedback@microsoft.com).

När du har aktiverat övervakning av ditt Azure Red Hat OpenShift version 3. x-kluster kan du stoppa övervakningen av klustret med Azure Monitor för behållare om du inte längre vill övervaka det. Den här artikeln visar hur du gör detta med hjälp av Azure Resource Manager-mallen som tillhandahölls.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Det finns två Azure Resource Manager-mall som stöder borttagning av lösnings resurserna konsekvent och flera gånger i resurs gruppen. Det ena är en JSON-mall som anger konfigurationen för att stoppa övervakningen och den andra innehåller parameter värden som du konfigurerar för att ange det OpenShift-kluster resurs-ID och Azure-region som klustret har distribuerats i.

Om du inte känner till konceptet att distribuera resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Om du väljer att använda Azure CLI måste du först installera och använda CLI lokalt. Du måste köra Azure CLI-versionen 2.0.65 eller senare. För att identifiera din version, kör `az --version` . Om du behöver installera eller uppgradera Azure CLI kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-template"></a>Skapa mallen

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Spara den här filen som **OptOutTemplate.jspå** en lokal mapp.

3. Klistra in följande JSON-syntax i filen:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Redigera värdena för **aroResourceId** och **aroResourceLocation** med hjälp av värdena i OpenShift-klustret, som du kan hitta på **egenskaps** sidan för det valda klustret.

    ![Sidan behållar egenskaper](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Spara den här filen som **OptOutParam.jspå** en lokal mapp.

6. Nu är det dags att distribuera den här mallen.

### <a name="remove-the-solution-using-azure-cli"></a>Ta bort lösningen med Azure CLI

Kör följande kommando med Azure CLI i Linux för att ta bort lösningen och rensa konfigurationen på klustret.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Ta bort lösningen med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Kör följande PowerShell-kommandon i mappen som innehåller mallen för att ta bort lösningen och rensa konfigurationen från klustret.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Konfigurations ändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande som innehåller resultatet:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Nästa steg

Om arbets ytan bara har skapats för att stödja övervakning av klustret och den inte längre behövs, måste du ta bort den manuellt. Om du inte är bekant med hur du tar bort en arbets yta, se [ta bort en Azure Log Analytics-arbetsyta](../platform/delete-workspace.md).
