---
title: 'Snabbstart: Dirigera Azure Cache for Redis händelser till webbslutpunkten med PowerShell'
description: Använd Azure Event Grid för att prenumerera Azure Cache for Redis händelser, skicka händelserna till en webhook och hantera händelserna i en webbapp.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fa1ae9a7a2200944bd5da0211be88ba4955e3d03
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833894"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Snabbstart: Dirigera Azure Cache for Redis händelser till webbslutpunkten med PowerShell

Azure Event Grid är en händelsetjänst för molnet. I den här snabbstarten använder du Azure PowerShell för att prenumerera Azure Cache for Redis händelser, utlösa en händelse och visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. Men för att förenkla den här snabbstarten skickar du händelser till en webbapp som samlar in och visar meddelandena. När du slutför stegen som beskrivs i den här snabbstarten ser du att händelsedata har skickats till webbappen.

## <a name="setup"></a>Konfiguration

Den här snabbstarten kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

I det här **exemplet används westus2** och valet lagras i en variabel för användning i hela .

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid distribueras som enskilda Azure-resurser och måste etableras under en Azure-resursgrupp. En resursgrupp är en logisk samling där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

I följande exempel skapas en resursgrupp med namnet **gridResourceGroup** på platsen **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Mer information om hur du skapar en cacheinstans i PowerShell finns i [Azure PowerShell referens.](/powershell/module/az.rediscache/new-azrediscache) 

## <a name="create-a-message-endpoint"></a>Skapa en slutpunkt för meddelanden

Innan du prenumererar på ämnet ska vi ska slutpunkten för händelsemeddelandet. Slutpunkten utför vanligtvis åtgärder baserat på informationen om händelsen. För att förenkla den här snabbstarten kan du distribuera en [förskapad webbapp](https://github.com/Azure-Samples/azure-event-grid-viewer) som visar meddelanden om händelser. Den distribuerade lösningen innehåller en App Service-plan,en webbapp för App Service och källkod från GitHub.

Ersätt `<your-site-name>` med ett unikt namn för din webbapp. Webbappnamnet måste vara unikt eftersom det är en del av DNS-posten.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Det kan ta några minuter att slutföra distributionen. Efter distributionen har slutförts kan du visa webbappen för att kontrollera att den körs. I en webbläsare navigerar du till: `https://<your-site-name>.azurewebsites.net`

Webbplatsen bör visas utan några meddelanden.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Tom Event Grid Viewer-webbplats.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Prenumerera på din Azure Cache for Redis händelse

I det här steget prenumererar du på ett ämne för att Event Grid vilka händelser du vill spåra. I följande exempel prenumererar vi på den cacheinstans som du skapade och URL:en från webbappen skickas som slutpunkt för händelseavisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Visa ditt webbprogram igen och observera att en händelse för verifieringen av prenumerationen har skickats till den. Välj ögonikonen för att utöka informationen om händelsen. Händelserutnätet skickar valideringshändelsen så att slutpunkten kan bekräfta att den vill ta emot händelsedata. Webbappen inkluderar kod för att verifiera prenumerationen.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid Viewer.":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Utlösa en händelse från Azure Cache for Redis

Nu ska vi utlösa en händelse och se hur Event Grid distribuerar meddelandet till slutpunkten.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Mer information om hur du importerar i PowerShell finns i [Azure PowerShell referens](/powershell/module/az.rediscache/import-azrediscache). 

Du har utlöst händelsen och Event Grid skickade meddelandet till den slutpunkt som du konfigurerade när du prenumererade. Visa din webbapp om du vill se händelsen som du har skickat.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Rensa resurser
Om du planerar att fortsätta arbeta med den här Azure Cache for Redis instansen och händelseprenumerationen ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda följande kommando för att ta bort de resurser som du skapade i den här snabbstarten.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och händelseprenumerationer kan du läsa mer om Azure Cache for Redis händelser och vad Event Grid kan hjälpa dig att göra:

- [Reagera på Azure Cache for Redis händelser](cache-event-grid.md)
- [Om Event Grid](../event-grid/overview.md)
