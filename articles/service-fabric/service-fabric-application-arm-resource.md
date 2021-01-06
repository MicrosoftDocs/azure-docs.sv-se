---
title: Distribuera och uppgradera med Azure Resource Manager
description: Lär dig hur du distribuerar program och tjänster till ett Service Fabric kluster med en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ed6bc7d96cb3ea0934929e6543c5e637a9f42c1f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930845"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Hantera program och tjänster som Azure Resource Manager resurser

Du kan distribuera program och tjänster till ditt Service Fabric-kluster via Azure Resource Manager. Det innebär att i stället för att distribuera och hantera program via PowerShell eller CLI när du har väntat tills klustret är klart kan du nu uttrycka program och tjänster i JSON och distribuera dem i samma Resource Manager-mall som klustret. Processen för program registrering, etablering och distribution sker i ett enda steg.

Detta är det rekommenderade sättet att distribuera konfigurations-, styrnings-eller kluster hanterings program som du behöver i klustret. Detta omfattar [program för uppdaterings dirigering](service-fabric-patch-orchestration-application.md), övervaknings agenter eller program som måste köras i klustret innan andra program eller tjänster distribueras. 

Vid behov hanterar du dina program som Resource Manager-resurser för att förbättra:
* Gransknings logg: resurs hanteraren granskar varje åtgärd och bevarar en detaljerad *aktivitets logg* som kan hjälpa dig att spåra ändringar som gjorts i dessa program och klustret.
* Rollbaserad åtkomst kontroll i Azure (Azure RBAC): hantering av åtkomst till kluster och program som distribueras i klustret kan göras via samma Resource Manager-mall.
* Azure Resource Manager (via Azure Portal) blir en One-avsluta-shoppa för hantering av kluster och kritiska program distributioner.

I följande kodfragment visas olika typer av resurser som kan hanteras via en mall:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

## <a name="add-a-new-application-to-your-resource-manager-template"></a>Lägg till ett nytt program i Resource Manager-mallen

1. Förbered klustrets Resource Manager-mall för distribution. Mer information finns i [skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) .
2. Tänk på några av de program som du planerar att distribuera i klustret. Finns det några som alltid kommer att köra andra program som kan ta beroenden? Planerar du att distribuera kluster styrning eller installations program? Dessa sorters program hanteras bäst via en Resource Manager-mall, enligt beskrivningen ovan. 
3. När du har avvisat vilka program du vill distribuera på det här sättet måste programmen paketeras, zippas och placeras på en lagrings resurs. Resursen måste vara tillgänglig via en REST-slutpunkt för Azure Resource Manager att använda under distributionen. Mer information finns i [skapa ett lagrings konto](service-fabric-concept-resource-model.md#create-a-storage-account) .
4. Beskriv varje programs egenskaper i din Resource Manager-mall under kluster deklarationen. Dessa egenskaper inkluderar replik-eller instans antal och eventuella beroende kedjor mellan resurser (andra program eller tjänster). Observera att detta inte ersätter program-eller tjänst manifesten, utan beskriver i stället några av vad som ingår i klustrets Resource Manager-mall. Här är en exempel-mall som inkluderar distribution av en tillstånds lös tjänst *Service1* och en tillstånds känslig tjänst *service2* som en del av *application1*:

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > Se Service Fabric [Azure Resource Managers referens](/azure/templates/microsoft.servicefabric/clusters/applicationtypes) för att hitta användning och information om enskilda mallegenskaper.

5. Distribuera! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Ta bort program resursen Service Fabric Resource Provider
Följande aktiverar app-paketet som ska tas bort från klustret och detta rensar disk utrymmet som används:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Att bara ta bort Microsoft. ServiceFabric/kluster/program från ARM-mallen avetablerar inte programmet

>[!NOTE]
> När borttagningen är klar bör du inte se paket versionen i SFX eller ARM längre. Du kan inte ta bort den program typ versions resurs som programmet kör med; ARM/SFRP förhindrar detta. Om du försöker avetablera det paket som körs förhindrar SF-körningen det.


## <a name="manage-an-existing-application-via-resource-manager"></a>Hantera ett befintligt program via Resource Manager

Om klustret redan är igång och vissa program som du vill hantera som Resource Manager-resurser redan har distribuerats på den, kan du använda ett anrop med samma API: er för att få programmen att bekräftas som Resource Manager-resurser. Mer information finns i [Vad är Service Fabric-programmets resurs modell?](./service-fabric-concept-resource-model.md)

> [!NOTE]
> Om du vill tillåta en kluster uppgradering att ignorera ohälsosama appar kan kunden ange "maxPercentUnhealthyApplications: 100" i avsnittet "upgradeDescription/healthPolicy". detaljerade beskrivningar för alla inställningar finns i [service fabric REST API dokumentation om kluster uppgraderings principer](/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Nästa steg

* Använd [Service Fabric CLI](service-fabric-cli.md) eller [PowerShell](service-fabric-deploy-remove-applications.md) för att distribuera andra program till klustret. 
* [Uppgradera Service Fabric-klustret](service-fabric-cluster-upgrade.md)
