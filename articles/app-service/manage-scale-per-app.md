---
title: Skalning per app för högdensitetsvärd
description: Skala appar oberoende av App Service och optimera de utskalade instanserna i din plan.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 756117a2a231fcb406fd3e3102a16c318c621aa0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832616"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Högdensitetsvärd på Azure App Service med skalning per app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du App Service kan du skala dina appar genom att skala App Service plan [de](overview-hosting-plans.md) körs på. När flera appar körs i samma App Service plan kör varje utskalade instans alla appar i planen.

*Skalning per app* kan aktiveras på App Service plan för att tillåta skalning av en app oberoende av de App Service plan som är värdar för den. På så sätt kan en App Service plan skalas till 10 instanser, men en app kan anges till att endast använda fem.

> [!NOTE]
> Skalning per app är endast tillgängligt för **prisnivåerna Standard,** **Premium,** **Premium V2** **och** Isolerad.
>

Appar allokeras till tillgängliga App Service plan med en metod för bästa möjliga resultat för en jämn fördelning mellan instanser. Även om en jämn distribution inte garanteras ser plattformen till att två instanser av samma app inte finns på samma App Service plan instans.

Plattformen förlitar sig inte på mått för att välja arbetsallokering. Program balanseras bara om när instanser läggs till eller tas bort från App Service plan.

## <a name="per-app-scaling-using-powershell"></a>Skalning per app med PowerShell

Skapa en plan med skalning per app genom att skicka ```-PerSiteScaling $true``` in parametern till ```New-AzAppServicePlan``` cmdleten .

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivera skalning per app med en befintlig App Service genom att skicka `-PerSiteScaling $true` parametern till ```Set-AzAppServicePlan``` cmdleten .

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

På appnivå konfigurerar du antalet instanser som appen kan använda i App Service plan.

I exemplet nedan är appen begränsad till två instanser oavsett hur många instanser den underliggande App Service-planen skalar ut till.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` skiljer sig från `$newapp.MaxNumberOfWorkers` . Skalning per app använder `$newapp.SiteConfig.NumberOfWorkers` för att fastställa appens skalningsegenskaper.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalning per app med hjälp av Azure Resource Manager

Följande mall Azure Resource Manager skapar:

- En App Service plan som skalas ut till 10 instanser
- en app som är konfigurerad för att skalas till högst fem instanser.

Den App Service plan anger **egenskapen PerSiteScaling** till true `"perSiteScaling": true` . Appen anger antalet arbetare **som ska** användas till 5 `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderad konfiguration för högdensitetsvärd

Skalning per app är en funktion som är aktiverad i både globala Azure-regioner [App Service miljöer.](environment/app-service-app-service-environment-intro.md) Den rekommenderade strategin är dock att använda App Service miljöer för att dra nytta av deras avancerade funktioner och den större App Service plan kapaciteten.  

Följ de här stegen för att konfigurera högdensitetsvärdtjänster för dina appar:

1. Ange en App Service plan som högdensitetsplan och skala ut den till önskad kapacitet.
1. Ange flaggan `PerSiteScaling` till true på App Service plan.
1. Nya appar skapas och tilldelas till App Service plan med **egenskapen numberOfWorkers** inställd på **1**.
   - Den här konfigurationen ger högsta möjliga densitet.
1. Antalet arbetare kan konfigureras separat per app för att bevilja ytterligare resurser efter behov. Exempel:
   - En app med hög användning kan ange **numberOfWorkers till** **3 för** att få mer bearbetningskapacitet för den appen.
   - Appar med låg användning skulle ange **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Azure App Service detaljerad översikt över planer](overview-hosting-plans.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)
