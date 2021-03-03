---
title: Azure-diagnostisk övervakning – Azure-attestering
description: Azure-diagnostisk övervakning av Azure-attestering
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726486"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurera diagnostik med Trusted Platform Module (TPM) slut punkt för Azure-attestering

[Plattforms loggar](../azure-monitor/essentials/platform-logs-overview.md) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. [Plattforms mått](../azure-monitor/essentials/data-platform-metrics.md) samlas in som standard och lagras vanligt vis i Azure Monitor Metrics-databasen. Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattforms mått och plattforms loggar till olika mål. 

TPM-slutpunktsmapparen är aktive rad med diagnostisk inställning och kan användas för att övervaka aktiviteter. Om du vill konfigurera [Azure-övervakning](../azure-monitor/overview.md) för TPM-tjänstens slut punkt med hjälp av PowerShell följer du stegen nedan. 

Konfigurera tjänsten Azure attestering. 

[Konfigurera Azure-attestering med Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Aktivitets loggarna finns i avsnittet behållare i lagrings kontot. Detaljerad information finns i [samla in resurs loggar från en Azure-resurs och analysera med Azure Monitor-Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
