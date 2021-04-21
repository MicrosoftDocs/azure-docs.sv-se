---
title: Automatisera distribution av funktionsappresurser till Azure
description: Lär dig hur du skapar Azure Resource Manager en mall som distribuerar din funktionsapp.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834218"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatisera resursdistributionen för funktionsappen i Azure Functions

Du kan använda en Azure Resource Manager för att distribuera en funktionsapp. Den här artikeln beskriver de resurser och parametrar som krävs för att göra det. Du kan behöva distribuera ytterligare resurser, beroende på [utlösare och bindningar](functions-triggers-bindings.md) i funktionsappen.

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md).

Exempelmallar finns i:
- [Funktionsapp i förbrukningsplan]
- [Funktionsapp på Azure App Service plan]

## <a name="required-resources"></a>Nödvändiga resurser

En Azure Functions-distribution består vanligtvis av följande resurser:

| Resurs                                                                           | Krav | Referens för syntax och egenskaper                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| En funktionsapp                                                                     | Obligatorisk    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Ett [Azure Storage konto](../storage/index.yml)                                   | Obligatorisk    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| En [Application Insights komponent](../azure-monitor/app/app-insights-overview.md) | Valfritt    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| En [värdplan](./functions-scale.md)                                             | Valfritt<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> En värdplan krävs bara när du väljer att köra funktionsappen på en [Premium-plan](./functions-premium-plan.md) eller på en [App Service plan](../app-service/overview-hosting-plans.md).

> [!TIP]
> Även om det inte krävs rekommenderar vi starkt att du konfigurerar Application Insights för din app.

<a name="storage"></a>
### <a name="storage-account"></a>Lagringskonto

Ett Azure Storage-konto krävs för en funktionsapp. Du behöver ett konto för generell användning som stöder blobar, tabeller, köer och filer. Mer information finns i Azure Functions [krav för lagringskontot.](storage-considerations.md#storage-account-requirements)

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

Dessutom måste egenskapen anges `AzureWebJobsStorage` som en appinställning i platskonfigurationen. Om funktionsappen inte använder Application Insights övervakning bör den också ange `AzureWebJobsDashboard` som en appinställning.

Den Azure Functions körningen använder `AzureWebJobsStorage` anslutningssträngen för att skapa interna köer.  När Application Insights inte är aktiverat använder körningen anslutningssträngen för att logga till Azure Table Storage och driva `AzureWebJobsDashboard` **fliken** Övervaka i portalen.

Dessa egenskaper anges i `appSettings` samlingen i `siteConfig` -objektet:

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

Application Insights rekommenderas för övervakning av dina funktionsappar. Den Application Insights resursen definieras med typen **Microsoft.Insights/components och** typen **webb**:

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

Dessutom måste instrumenteringsnyckeln tillhandahållas till funktionsappen med hjälp av `APPINSIGHTS_INSTRUMENTATIONKEY` programinställningen. Den här egenskapen anges i `appSettings` samlingen i `siteConfig` -objektet:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Värdplan

Definitionen av värdplanen varierar och kan vara något av följande:
* [Förbrukningsplan](#consumption) (standard)
* [Premiumplan](#premium)
* [App Service-plan](#app-service-plan)

### <a name="function-app"></a>Funktionsapp

Funktionsappresursen definieras med hjälp av en resurs av typen **Microsoft.Web/sites och** kind **functionapp**:

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
> Om du uttryckligen definierar en värdplan behövs ytterligare ett objekt i matrisen dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

En funktionsapp måste innehålla följande programinställningar:

| Inställningsnamn                 | Description                                                                               | Exempelvärden                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | En anslutningssträng till ett lagringskonto som Functions-körningen använder för intern köning | Se [Lagringskonto](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versionen av Azure Functions runtime                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Språkstacken som ska användas för funktioner i den här appen                                   | `dotnet`, `node` , `java` , `python` eller `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Behövs bara om du använder `node` språkstacken och anger vilken version som ska användas              | `10.14.1`                             |

Dessa egenskaper anges i `appSettings` samlingen i `siteConfig` egenskapen :

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

## <a name="deploy-on-consumption-plan"></a>Distribuera i förbrukningsplan

Förbrukningsplanen allokerar automatiskt beräkningskraft när koden körs, skalar ut efter behov för att hantera belastningen och skalar sedan in när koden inte körs. Du behöver inte betala för inaktiva virtuella datorer och du behöver inte reservera kapacitet i förväg. Mer information finns i Azure Functions [skala och vara värd för](consumption-plan.md).

Ett exempel på Azure Resource Manager finns i [Funktionsapp på förbrukningsplan].

### <a name="create-a-consumption-plan"></a>Skapa en förbrukningsplan

En förbrukningsplan behöver inte definieras. En skapas eller väljs automatiskt per region när du skapar själva funktionsappresursen.

Förbrukningsplanen är en särskild typ av "serverfarm"-resurs. För Windows kan du ange det med hjälp av `Dynamic` värdet för egenskaperna och `computeMode` `sku` :

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
> Förbrukningsplanen kan inte definieras explicit för Linux. Den skapas automatiskt.

Om du uttryckligen definierar din förbrukningsplan måste du ange egenskapen för appen så att den pekar på `serverFarmId` resurs-ID:t för planen. Du bör se till att `dependsOn` funktionsappen även har en inställning för planen.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

De inställningar som krävs av en funktionsapp som körs i förbrukningsplanen skjuter upp mellan Windows och Linux. 

#### <a name="windows"></a>Windows

I Windows kräver en förbrukningsplan ytterligare en inställning i platskonfigurationen: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Den här egenskapen konfigurerar lagringskontot där funktionsappens kod och konfiguration lagras.

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
> Ange inte inställningen [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) eftersom den genereras åt dig när webbplatsen först skapas.  

#### <a name="linux"></a>Linux

I Linux måste funktionsappen ha `kind` angetts till `functionapp,linux` , och egenskapen måste vara inställd på `reserved` `true` . 

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

Inställningarna [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) och stöds inte i Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Distribuera på Premium-plan

Premium-planen erbjuder samma skalning som förbrukningsplanen, men innehåller dedikerade resurser och ytterligare funktioner. Mer information finns i [Azure Functions Premium-plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Skapa en Premium-plan

En Premium-plan är en särskild typ av "serverfarm"-resurs. Du kan ange den med hjälp av `EP1` antingen `EP2` , eller för `EP3` `Name` egenskapsvärdet i `sku` [beskrivningsobjektet](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

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

En funktionsapp i en Premium-plan måste ha `serverFarmId` egenskapen inställd på resurs-ID:t för planen som skapades tidigare. Dessutom kräver en Premium-plan ytterligare en inställning i platskonfigurationen: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Den här egenskapen konfigurerar lagringskontot där funktionsappens kod och konfiguration lagras.

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
> Ange inte inställningen [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) eftersom den genereras åt dig när webbplatsen först skapas.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Distribuera på App Service plan

I App Service plan körs funktionsappen på dedikerade virtuella datorer på Basic-, Standard- och Premium-SKU:er, ungefär som webbappar. Mer information om hur App Service plan fungerar finns [i Azure App Service detaljerad översikt över planer.](../app-service/overview-hosting-plans.md)

Ett exempel på Azure Resource Manager finns i [Funktionsapp på Azure App Service plan].

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

En App Service plan definieras av en "serverfarm"-resurs.

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

Om du vill köra appen i Linux måste du också ange `kind` till `Linux` :

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

En funktionsapp på en App Service plan måste ha egenskapen `serverFarmId` inställd på resurs-ID:t för planen som skapades tidigare.

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

Linux-appar bör också innehålla en `linuxFxVersion` -egenskap under `siteConfig` . Om du bara distribuerar kod bestäms värdet för detta av önskad körningsstack i formatet ```runtime|runtimeVersion``` :

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

Om du [distribuerar en anpassad containeravbildning](./functions-create-function-linux-custom-image.md)måste du ange den med och inkludera konfigurationen som gör att avbildningen kan hämtas, som `linuxFxVersion` i [Web App for Containers](../app-service/index.yml). Ställ också `WEBSITES_ENABLE_APP_SERVICE_STORAGE` in på eftersom `false` appinnehållet anges i själva containern:

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

En funktionsapp har många underordnade resurser som du kan använda i distributionen, inklusive appinställningar och alternativ för källkontroll. Du kan också välja att ta bort **den underordnade källresursen** och använda ett annat [distributionsalternativ i](functions-continuous-deployment.md) stället.

> [!IMPORTANT]
> Det är viktigt att förstå hur resurser distribueras i Azure Azure Resource Manager distribuerar programmet med hjälp av en Azure Resource Manager program. I följande exempel tillämpas toppnivåkonfigurationer med hjälp av **siteConfig**. Det är viktigt att ange dessa konfigurationer på den översta nivån, eftersom de förmedlar information till Functions-körnings- och distributionsmotorn. Information på den översta nivån krävs innan de **underordnade källkontrollerna/webbresursen** tillämpas. Även om det är möjligt att konfigurera dessa inställningar i resursen **config/appSettings** på underordnad  nivå, måste din funktionsapp i vissa fall distribueras innan **config/appSettings** tillämpas. När du till exempel använder funktioner med [Logic Apps](../logic-apps/index.yml), är dina funktioner ett beroende av en annan resurs.

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
> Den här mallen använder [inställningsvärdet](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) för projektappen, som anger den baskatalog där Functions-distributionsmotorn (Kudu) söker efter distribuerbar kod. I vår lagringsplats finns våra funktioner i en undermapp i **mappen src.** I föregående exempel anger vi därför värdet för appinställningar till `src` . Om dina funktioner finns i roten på lagringsplatsen, eller om du inte distribuerar från källkontrollen, kan du ta bort värdet för appinställningarna.

## <a name="deploy-your-template"></a>Distribuera mallen

Du kan använda något av följande sätt för att distribuera mallen:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portalen](../azure-resource-manager/templates/deploy-portal.md)
* [REST-API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Distribuera till Azure (knapp)

Ersätt ```<url-encoded-path-to-azuredeploy-json>``` med en [URL-kodad](https://www.bing.com/search?q=url+encode) version av den råa sökvägen till `azuredeploy.json` filen i GitHub.

Här är ett exempel som använder markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Här är ett exempel som använder HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Följande PowerShell-kommandon skapar en resursgrupp och distribuerar en mall som skapar en funktionsapp med de resurser som krävs. Om du vill köra lokalt måste du ha [Azure PowerShell](/powershell/azure/install-az-ps) installerat. Kör [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) för att logga in.

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

Om du vill testa den här distributionen kan du använda en mall som den [här](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) som skapar en funktionsapp i Windows i en förbrukningsplan. Ersätt `<function-app-name>` med ett unikt namn för funktionsappen.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du utvecklar och konfigurerar Azure Functions.

* [Azure Functions, info för utvecklare](functions-reference.md)
* [Så här konfigurerar du inställningar för Azure-funktionsappen](functions-how-to-use-azure-function-app-settings.md)
* [Skapa din första Azure-funktion](./functions-get-started.md)

<!-- LINKS -->

[Funktionsapp i förbrukningsplan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Funktionsapp på Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
