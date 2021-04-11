---
title: Azure-diagnostisk övervakning av Azure-attestering
description: Azure-diagnostisk övervakning av Azure-attestering
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168356"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Konfigurera diagnostik med en Trusted Platform Module-slutpunkt (TPM) för Azure-attestering

Den här artikeln hjälper dig att skapa och konfigurera diagnostikinställningar för att skicka plattforms mått och plattforms loggar till olika mål. [Plattforms loggar](/azure/azure-monitor/platform/platform-logs-overview) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och den Azure-plattform som de är beroende av. [Plattforms mått](/azure/azure-monitor/platform/data-platform-metrics) samlas in som standard och lagras i Azure Monitor Metrics-databasen.

Innan du börjar ska du kontrol lera att du har [konfigurerat Azure-attestering med Azure PowerShell](quickstart-powershell.md).

Slut punkts tjänsten Trusted Platform Module (TPM) är aktive rad i diagnostikinställningar och kan användas för att övervaka aktiviteter. Konfigurera [Azure-övervakning](/azure/azure-monitor/overview) för TPM-tjänstens slut punkt med hjälp av följande kod.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Aktivitets loggar finns i avsnittet **behållare** i lagrings kontot. Mer information finns i [samla in och analysera resurs loggar från en Azure-resurs](/azure/azure-monitor/learn/tutorial-resource-logs).
