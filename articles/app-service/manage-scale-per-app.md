---
title: Skalning per app för hög densitets värd
description: Skala appar oberoende av App Service planer och optimera de utskalade instanserna i planen.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74672354"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hög densitets värd för Azure App Service med skalning per app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

När du använder App Service kan du skala dina appar genom att skala de [App Service plan](overview-hosting-plans.md) de körs på. När flera appar körs i samma App Service plan körs alla appar i planen av varje utskalad instans.

*Skalning per app* kan aktive ras på App Service plan nivå för att tillåta skalning av en app oberoende av App Service plan som är värd för den. På så sätt kan en App Service plan skalas till 10 instanser, men en app kan ställas in för att endast använda fem.

> [!NOTE]
> Skalning per app är bara tillgängligt för **standard**-, **Premium**-, **Premium v2** -och **isolerade** pris nivåer.
>

Appar tilldelas till tillgängliga App Service plan med hjälp av en metod för bästa prestanda för en jämn fördelning mellan instanser. Även om en jämn fördelning inte är garanterad, ser plattformen till att två instanser av samma app inte är värdbaserade på samma App Service plan instans.

Plattformen förlitar sig inte på mått för att bestämma tilldelning av arbetare. Program är bara ombalanserade när instanser läggs till eller tas bort från App Service plan.

## <a name="per-app-scaling-using-powershell"></a>Per app-skalning med hjälp av PowerShell

Skapa en plan med skalning per app genom att skicka in ```-PerSiteScaling $true``` parametern till ```New-AzAppServicePlan``` cmdleten.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Aktivera skalning per app med en befintlig App Service plan genom att skicka in `-PerSiteScaling $true` parametern till ```Set-AzAppServicePlan``` cmdleten.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

På App-nivå konfigurerar du antalet instanser som appen kan använda i App Service plan.

I exemplet nedan är appen begränsad till två instanser, oavsett hur många instanser den underliggande App Service-planen skalar ut till.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` skiljer sig från `$newapp.MaxNumberOfWorkers` . Skalning per app använder `$newapp.SiteConfig.NumberOfWorkers` för att fastställa appens skalnings egenskaper.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalning per app med hjälp av Azure Resource Manager

Följande Azure Resource Manager mall skapar:

- En App Service plan som skalas ut till 10 instanser
- en app som har kon figurer ATS för skalning till högst fem instanser.

App Service plan anger egenskapen **PerSiteScaling** till True `"perSiteScaling": true` . Appen anger **antalet arbetare** som ska användas till 5 `"properties": { "numberOfWorkers": "5" }` .

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Rekommenderad konfiguration för hög densitets värd

Per app-skalning är en funktion som är aktive rad i både globala Azure-regioner och [App Service miljöer](environment/app-service-app-service-environment-intro.md). Den rekommenderade strategin är dock att använda App Service miljöer för att dra nytta av de avancerade funktionerna och den större App Service plan kapaciteten.  

Följ dessa steg om du vill konfigurera hög densitets värd för dina appar:

1. Ange en App Service plan som hög densitets plan och skala ut den till önskad kapacitet.
1. Ställ in `PerSiteScaling` flaggan på sant för App Service plan.
1. Nya appar skapas och tilldelas till den App Service plan med egenskapen **numberOfWorkers** inställd på **1**.
   - Med den här konfigurationen ger högsta möjliga densitet.
1. Antalet arbetare kan konfigureras oberoende per app för att ge ytterligare resurser vid behov. Exempel:
   - En app med hög användning kan ange **numberOfWorkers** till **3** för att få mer processor kapacitet för den appen.
   - Appar med låg användning anger **numberOfWorkers** till **1**.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure App Service planer i djupet](overview-hosting-plans.md)
- [Introduktion till App Service-miljöer](environment/app-service-app-service-environment-intro.md)
