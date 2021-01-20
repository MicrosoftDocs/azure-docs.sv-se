---
title: Azure-diagnostisk övervakning – Azure-attestering
description: Azure-diagnostisk övervakning av Azure-attestering
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606123"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurera diagnostik med Trusted Platform Module (TPM) slut punkt för Azure-attestering

[Plattforms loggar](/azure/azure-monitor/platform/platform-logs-overview) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. [Plattforms mått](/azure/azure-monitor/platform/data-platform-metrics) samlas in som standard och lagras vanligt vis i Azure Monitor Metrics-databasen. Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattforms mått och plattforms loggar till olika mål. 

TPM-slutpunktsmapparen är aktive rad med diagnostisk inställning och kan användas för att övervaka aktiviteter. Om du vill konfigurera [Azure-övervakning](/azure/azure-monitor/overview) för TPM-tjänstens slut punkt med hjälp av PowerShell följer du stegen nedan. 

Konfigurera tjänsten Azure attestering. 

[Konfigurera Azure-attestering med Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Aktivitets loggarna finns i avsnittet behållare i lagrings kontot. Detaljerad information finns i [samla in resurs loggar från en Azure-resurs och analysera med Azure Monitor-Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
