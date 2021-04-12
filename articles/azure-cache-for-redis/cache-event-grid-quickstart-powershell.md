---
title: 'Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med PowerShell'
description: Använd Azure Event Grid för att prenumerera på Azure cache för Redis-händelser, skicka händelser till en webhook och hantera händelserna i ett webb program.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 6c3b433a8e433f39b723a7155bb6de116857efca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508171"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Snabb start: dirigera Azure cache efter Redis-händelser till webb slut punkt med PowerShell

Azure Event Grid är en händelsetjänst för molnet. I den här snabb starten ska du använda Azure PowerShell för att prenumerera på Azure cache för Redis-händelser, utlösa en händelse och visa resultatet. 

Normalt kan du skicka händelser till en slutpunkt som bearbetar informationen om händelsen och utför åtgärder. För att förenkla den här snabb starten kommer du dock att skicka händelser till en webbapp som samlar in och visar meddelandena. När du slutför stegen som beskrivs i den här snabb starten ser du att händelse data har skickats till webbappen.

## <a name="setup"></a>Konfiguration

Den här snabb starten kräver att du kör den senaste versionen av Azure PowerShell. Om du behöver installera eller uppgradera kan du läsa [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

```powershell
Connect-AzAccount
```

I det här exemplet används **westus2** och lagras urvalet i en variabel som används i hela.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Event Grid ämnen distribueras som enskilda Azure-resurser och måste tillhandahållas under en Azure-resurs grupp. En resurs grupp är en logisk samling där Azure-resurser distribueras och hanteras.

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
Mer information om hur du skapar en cache-instans i PowerShell finns i [referensen Azure PowerShell](/powershell/module/az.rediscache/new-azrediscache). 

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

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Töm Event Grid Viewer-webbplatsen.":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Prenumerera på Azure cache för Redis-evenemang

I det här steget ska du prenumerera på ett ämne för att berätta Event Grid vilka händelser du vill spåra. I följande exempel prenumererar du på den cachelagrade instans som du skapade och skickar URL: en från din webbapp som slut punkt för händelse avisering. Slutpunkten för ditt webbprogram måste innehålla suffixet `/api/updates/`.

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

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Utlösa en händelse från Azure cache för Redis

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
Mer information om hur du importerar i PowerShell finns [Azure PowerShell referens](/powershell/module/az.rediscache/import-azrediscache). 

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
Om du planerar att fortsätta arbeta med den här Azure-cachen för Redis-instansen och händelse prenumerationen ska du inte rensa resurserna som skapades i den här snabb starten. Om du inte planerar att fortsätta använder du följande kommando för att ta bort de resurser som du skapade i den här snabb starten.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du skapar ämnen och händelse prenumerationer kan du läsa mer om Azure cache för Redis-händelser och vilka Event Grid kan hjälpa dig:

- [Reagerar på Azure cache för Redis-händelser](cache-event-grid.md)
- [Om Event Grid](../event-grid/overview.md)