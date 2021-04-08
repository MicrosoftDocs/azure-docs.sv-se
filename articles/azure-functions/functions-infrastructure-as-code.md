---
title: Automatisera distributionen av Function-app-resurser till Azure
description: Lär dig hur du skapar en Azure Resource Manager mall som distribuerar din Function-app.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 9df4c62a65fd133c6ea8dc84e33d7c7b02d94cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99494047"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resurs distributionen för din Function-app i Azure Functions

Du kan använda en Azure Resource Manager-mall för att distribuera en Function-app. Den här artikeln beskriver de resurser och parametrar som krävs för att göra detta. Du kan behöva distribuera ytterligare resurser, beroende på [utlösare och bindningar](functions-triggers-bindings.md) i din Function-app.

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md).

Exempel på mallar finns i:
- [Function app i förbruknings plan]
- [Function-app på Azure App Service plan]

## <a name="required-resources"></a>Nödvändiga resurser

En Azure Functions distribution består vanligt vis av följande resurser:

| Resurs                                                                           | Krav | Syntax och egenskaper-referens                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| En Function-app                                                                     | Obligatorisk    | [Microsoft. Web/Sites](/azure/templates/microsoft.web/sites)                             |
| Ett [Azure Storage](../storage/index.yml) konto                                   | Obligatorisk    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| En [Application Insights](../azure-monitor/app/app-insights-overview.md) -komponent | Valfritt    | [Microsoft. Insights/komponenter](/azure/templates/microsoft.insights/components)         |
| En [värd plan](./functions-scale.md)                                             | Valfria<sup>1</sup>    | [Microsoft. Web/Server grupper](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> En värd plan krävs bara när du väljer att köra din Function-app i en [Premium-plan](./functions-premium-plan.md) eller på en [App Service plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> Även om det inte krävs rekommenderar vi att du konfigurerar Application Insights för din app.

<a name="storage"></a>
### <a name="storage-account"></a>Lagringskonto

Ett Azure Storage-konto krävs för en Function-app. Du behöver ett allmänt syftes konto som stöder blobbar, tabeller, köer och filer. Mer information finns i [Azure Functions lagrings konto krav](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Dessutom `AzureWebJobsStorage` måste egenskapen anges som en app-inställning i plats konfigurationen. Om Function-appen inte använder Application Insights för övervakning bör den också anges `AzureWebJobsDashboard` som en app-inställning.

Azure Functions runtime använder `AzureWebJobsStorage` anslutnings strängen för att skapa interna köer.  När Application Insights inte är aktive rad använder körningen `AzureWebJobsDashboard` anslutnings strängen för att logga in på Azure Table Storage och Power The **Monitor** -fliken i portalen.

Dessa egenskaper anges i `appSettings` samlingen i `siteConfig` objektet:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights rekommenderas för övervakning av dina funktions appar. Application Insights resursen definieras med typen **Microsoft. Insights/komponenter** och typen **webb**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Dessutom måste instrument ställnings nyckeln tillhandahållas till Function-appen med hjälp av `APPINSIGHTS_INSTRUMENTATIONKEY` program inställningen. Den här egenskapen anges i `appSettings` samlingen i `siteConfig` objektet:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Värd plan

Definitionen av värd planen varierar och kan vara något av följande:
* [Förbruknings plan](#consumption) (standard)
* [Premiumplan](#premium)
* [App Service-plan](#app-service-plan)

### <a name="function-app"></a>Funktionsapp

Function app-resursen definieras genom att använda en resurs av typen **Microsoft. Web/Sites** och Natura **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Om du uttryckligen definierar en värd plan behövs ytterligare ett objekt i dependsOn-matrisen: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

En Function-app måste innehålla följande program inställningar:

| Inställningsnamn                 | Beskrivning                                                                               | Exempelvärden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | En anslutnings sträng till ett lagrings konto som Functions runtime använder för intern kö | Se [lagrings konto](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versionen av Azure Functions runtime                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Språk stacken som ska användas för funktioner i den här appen                                   | `dotnet`, `node` , `java` , `python` eller `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Krävs endast om du använder `node` språk stacken, anger vilken version som ska användas              | `10.14.1`                             |

Dessa egenskaper anges i `appSettings` samlingen i `siteConfig` egenskapen:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Distribuera förbruknings plan

Förbruknings planen allokerar automatiskt beräknings kraft när koden körs, skalar vid behov för att hantera belastningen och skalar sedan i när koden inte körs. Du behöver inte betala för inaktiva virtuella datorer och du behöver inte reservera kapacitet i förväg. Läs mer i [Azure Functions skala och vara värd](consumption-plan.md).

Ett exempel på en Azure Resource Manager mall finns i [funktions program i förbruknings plan].

### <a name="create-a-consumption-plan"></a>Skapa en förbruknings plan

En förbruknings plan behöver inte definieras. Den ena skapas eller väljs automatiskt baserat på varje region när du skapar själva Function-appens resurs.

Förbruknings planen är en särskild typ av "Server klustret"-resurs. För Windows kan du ange det med hjälp av `Dynamic` värdet för `computeMode` `sku` egenskaperna och:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Förbruknings planen kan inte uttryckligen definieras för Linux. Den kommer att skapas automatiskt.

Om du uttryckligen definierar förbruknings planen måste du ange `serverFarmId` egenskapen för appen så att den pekar på planens resurs-ID. Du bör se till att Function-appen också har en `dependsOn` inställning för planen.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

Inställningarna som krävs av en Function-app som körs i förbruknings planen skjuts upp mellan Windows och Linux. 

#### <a name="windows"></a>Windows

I Windows kräver en förbruknings plan ytterligare en inställning i plats konfigurationen: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Den här egenskapen konfigurerar lagrings kontot där programmets kod och konfiguration lagras.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Ange inte [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) inställningen när den skapas åt dig när platsen först skapas.  

#### <a name="linux"></a>Linux

I Linux måste Function-appen ha sin `kind` set to `functionapp,linux` och måste ha `reserved` egenskapen inställd på `true` . 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)Inställningarna och [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) stöds inte i Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Distribuera i Premium-plan

Premium-planen ger samma skalning som förbruknings planen men innehåller dedikerade resurser och ytterligare funktioner. Läs mer i [Azure Functions Premium-plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Skapa en Premium-plan

En Premium-plan är en särskild typ av "Server klustret"-resurs. Du kan ange det genom att använda antingen `EP1` , `EP2` eller `EP3` för `Name` egenskap svärdet i `sku` [objektet Description](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

En Function-app i en Premium-plan måste ha `serverFarmId` egenskapen inställd på resurs-ID för den plan som skapades tidigare. Dessutom kräver en Premium-prenumeration ytterligare en inställning i plats konfigurationen: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Den här egenskapen konfigurerar lagrings kontot där programmets kod och konfiguration lagras.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Ange inte [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) inställningen när den skapas åt dig när platsen först skapas.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Distribuera på App Service plan

I App Service plan körs Function-appen på dedikerade virtuella datorer på SKU: er för Basic, standard och Premium, som liknar webbappar. Mer information om hur App Service plan fungerar finns [i Översikt över Azure App Service planer](../app-service/overview-hosting-plans.md).

Ett exempel på en Azure Resource Manager mall finns i [Function app i Azure App Service plan].

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En App Service plan definieras av en "Server klustret"-resurs.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Om du vill köra din app på Linux måste du också ställa in `kind` på `Linux` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Skapa en funktionsapp

En Function-app på en App Service plan måste ha `serverFarmId` egenskapen inställd på resurs-ID för den plan som skapades tidigare.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux-appar bör också innehålla en `linuxFxVersion` egenskap under `siteConfig` . Om du bara distribuerar kod bestäms värdet för detta av den önskade körnings stacken i formatet ```runtime|runtimeVersion``` :

| Stack            | Exempelvärde                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

Om du [distribuerar en anpassad behållar avbildning](./functions-create-function-linux-custom-image.md)måste du ange den med `linuxFxVersion` och inkludera konfiguration som gör att avbildningen kan hämtas, som i [Web App for containers](../app-service/index.yml). Ställ även in `WEBSITES_ENABLE_APP_SERVICE_STORAGE` på `false` , eftersom ditt appdata finns i själva behållaren:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Anpassa en distribution

En Function-app har många underordnade resurser som du kan använda i distributionen, inklusive inställningar för appar och käll kontroll. Du kan också välja att ta bort den underordnade resursen **sourcecontrols** och använda ett annat [distributions alternativ](functions-continuous-deployment.md) i stället.

> [!IMPORTANT]
> För att kunna distribuera programmet med hjälp av Azure Resource Manager är det viktigt att förstå hur resurser distribueras i Azure. I följande exempel tillämpas konfigurationer på den översta nivån med hjälp av **siteConfig**. Det är viktigt att ställa in dessa konfigurationer på högsta nivån, eftersom de förmedlar information till Functions-körningen och distributions motorn. Information på den översta nivån krävs innan den underordnade **sourcecontrols/webb-** resursen tillämpas. Även om det är möjligt att konfigurera de här inställningarna i en **konfiguration/appSettings** -resurs på den underordnade nivån, i vissa fall måste din funktions app distribueras *innan* **config/appSettings** används. Om du till exempel använder funktioner med [Logic Apps](../logic-apps/index.yml), är funktionerna ett beroende av en annan resurs.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Den här mallen använder värdet [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) App Settings, som anger bas katalogen där Function Deployment Engine (kudu) söker efter distributions bara kod. I vårt lager finns våra funktioner i en undermapp till **src** -mappen. I föregående exempel ställer vi in värdet för App Settings på `src` . Om dina funktioner finns i roten för din lagrings plats, eller om du inte distribuerar från käll kontroll, kan du ta bort värdet för appens inställningar.

## <a name="deploy-your-template"></a>Distribuera mallen

Du kan använda något av följande sätt för att distribuera mallen:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portalen](../azure-resource-manager/templates/deploy-portal.md)
* [REST-API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Distribuera till Azure (knapp)

Ersätt ```<url-encoded-path-to-azuredeploy-json>``` med en [URL-kodad](https://www.bing.com/search?q=url+encode) version av filens RAW-sökväg `azuredeploy.json` i GitHub.

Här är ett exempel som använder MARKDOWN:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Här är ett exempel som använder HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Följande PowerShell-kommandon skapar en resurs grupp och distribuerar en mall som skapar en Function-app med nödvändiga resurser. Om du vill köra lokalt måste du ha [Azure PowerShell](/powershell/azure/install-az-ps) installerat. Kör [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) för att logga in.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Om du vill testa den här distributionen kan du använda en [mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) som en sådan som skapar en Function-app i Windows i en förbruknings plan. Ersätt `<function-app-name>` med ett unikt namn för din Function-app.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du utvecklar och konfigurerar Azure Functions.

* [Azure Functions, info för utvecklare](functions-reference.md)
* [Så här konfigurerar du inställningar för Azure Function-appar](functions-how-to-use-azure-function-app-settings.md)
* [Skapa din första Azure-funktion](./functions-get-started.md)

<!-- LINKS -->

[Function app i förbruknings plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Function-app på Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
