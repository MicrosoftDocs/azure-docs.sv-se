---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f5ebd05177abf5fb3468b3ce935b7d004b414424
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100552290"
---
NFS stöds i **alla** 30 + regioner där Premium Storage-lagring är tillgängligt.

Vi lägger kontinuerligt till regioner. I den senaste listan använder du exemplet nedan för att fråga listan över regioner med stöd för NFS. Du kan också söka efter ditt regions stöd på [Azure-produkter som är tillgängliga på sidan region](https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all) under **Premium Files-lagring**.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Provide specific subscription id if you want  list for a different subscription
$subscription = $azContext.Subscription.Id

# Invoke the REST API
$restUri = "https://management.azure.com/subscriptions/$subscription/providers/Microsoft.Storage/skus?api-version=2019-06-01"
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader

# List of all regions that has NFS support.
$response.value| Where-Object -FilterScript {$_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'}}| Select-Object locations, kind, name

# List of regions that support NFS Zonal redundancy.
$response.value| Where-Object -FilterScript {($_.name -EQ 'Premium_ZRS') -and ($_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'})}| Select-Object locations
```

Exempelsvar
```
List of regions that support NFS Zonal redundancy
locations
---------
{eastus}
{eastus2}
{westeurope}
{southeastasia}
{japaneast}
{northeurope}
{australiaeast}
{westus2}
{uksouth}
{eastus2euap}
{francecentral}
```
