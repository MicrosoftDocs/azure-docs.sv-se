---
title: Etablera webbapp med Azure Cache for Redis
description: Använd Azure Resource Manager för att distribuera webbapp med Azure Cache for Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 089d7d7990f0f94135f629a5cd7a461700aa3433
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830780"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Skapa en webbapp och Azure Cache for Redis med hjälp av en mall

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet får du lära dig hur du skapar en Azure Resource Manager som distribuerar en Azure-webbapp med Azure Cache for Redis. Du får lära dig hur du definierar vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns [i Redigera Azure Resource Manager-mallar.](../azure-resource-manager/templates/template-syntax.md) Mer information om JSON-syntaxen och JSON-egenskaperna för cacheresurstyper finns [i Microsoft.Cache-resurstyper.](/azure/templates/microsoft.cache/allversions)

Den fullständiga mallen finns i [Web App with Azure Cache for Redis template (Webbapp med Azure Cache for Redis mall).](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)

## <a name="what-you-will-deploy"></a>Vad du ska distribuera
I den här mallen distribuerar du:

* Azure Web App
* Azure Cache for Redis

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parametrar som ska anges
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Variabler för namn
Den här mallen använder variabler för att konstruera namn för resurserna. Den använder [funktionen uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) för att skapa ett värde baserat på resursgruppens ID.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Resurser som ska distribueras
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Skapar Azure Cache for Redis som används med webbappen. Namnet på cacheminnet anges i **variabeln cacheName.**

Mallen skapar cachen på samma plats som resursgruppen.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Webbapp
Skapar webbappen med det namn som anges i **variabeln webSiteName.**

Observera att webbappen har konfigurerats med appinställningsegenskaper som gör att den kan fungera med Azure Cache for Redis. De här appinställningarna skapas dynamiskt baserat på värden som anges under distributionen.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
