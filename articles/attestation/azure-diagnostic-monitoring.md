---
title: Azure-diagnostikövervakning för Azure Attestation
description: Azure-diagnostikövervakning för Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833642"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurera diagnostik med en Trusted Platform Module (TPM)-slutpunkt för Azure Attestation

Den här artikeln hjälper dig att skapa och konfigurera diagnostikinställningar för att skicka plattformsmått och plattformsloggar till olika mål. [Plattformsloggar](/azure/azure-monitor/platform/platform-logs-overview) i Azure, inklusive Azure-aktivitetsloggar och resursloggar, ger detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform som de är beroende av. [Plattformsmått](/azure/azure-monitor/platform/data-platform-metrics) samlas in som standard och lagras i Azure Monitor Metrics.

Innan du börjar bör du kontrollera att du [har Azure Attestation med Azure PowerShell](quickstart-powershell.md).

Slutpunktstjänsten Trusted Platform Module (TPM) är aktiverad i diagnostikinställningarna och kan användas för att övervaka aktivitet. Konfigurera [Azure Monitoring för](/azure/azure-monitor/overview) TPM-tjänstslutpunkten med hjälp av följande kod.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Aktivitetsloggar finns i **avsnittet Containrar** i lagringskontot. Mer information finns i Samla [in och analysera resursloggar från en Azure-resurs.](/azure/azure-monitor/learn/tutorial-resource-logs)
