---
title: Felkoder för Azure Virtual Machines-och skalnings uppsättnings instanser
description: Lär dig mer om felkoder som kan uppstå när du använder Azure-Virtual Machines och skalnings uppsättnings instanser.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 6d43935365580233063deb0e523d222351c22d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670612"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Fel meddelanden för Azures Virtual Machines-och skalnings uppsättningar

Här följer några möjliga fel koder som du kan få när du använder Azure-Virtual Machines och skalnings uppsättningar.


| Nyckel | Meddelande | Beskrivning |
|-----|---------|-------------|
| SkuNotAvailable | Den begärda nivån för resursen \<resource\> är för närvarande inte tillgänglig på platsen \<location\> för prenumerationen \<subscriptionID\> . Försök med en annan nivå eller distribuera till en annan plats. | Det finns inte tillräckligt med kapacitet för virtuella Azure-datorer på den här platsen för att skapa en VM-eller skalnings uppsättnings instans. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Borttagnings principen kan bara ställas in på Azure-Virtual Machines. | Den här virtuella datorn är inte en virtuell Azure-dator, så du kan inte ange principen för borttagning. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  En virtuell Azure-dator stöds inte i tillgänglighets uppsättningen. | Du måste välja att antingen använda en virtuell dator med Azure-platsen eller använda en virtuell dator i en tillgänglighets uppsättning. |
| AzureSpotFeatureNotEnabledForSubscription  |  Prenumerationen är inte aktive rad med funktionen för virtuell Azure-dator. | Använd en prenumeration som har stöd för Azure-Virtual Machines. |
| VMPriorityCannotBeApplied  |  Det angivna prioritet svärdet {0} kan inte tillämpas på den virtuella datorn {1} eftersom ingen prioritet angavs när den virtuella datorn skapades. | Ange prioritet när den virtuella datorn skapas. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Det går inte att utföra åtgärden {0} eftersom det angivna Max priset {1} USD är lägre än det aktuella dekor priset {2} USD för Azures virtuella dator storlek {3} . | Välj ett högre pris. Mer information finns i pris information för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Ogiltigt max pris värde. De enda värden som stöds för högsta pris är-1 eller en decimal som är större än noll. Max pris värde på-1 anger att den virtuella Azure-datorn inte tas bort av pris skäl. | Ange ett giltigt max pris. Mer information finns i avsnittet om priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) eller [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | Maximal pris ändring är inte tillåten när den virtuella datorn {0} har allokerats för tillfället. Frigör och försök igen. | Stop\Deallocate den virtuella datorn så att du kan ändra det högsta priset. |
| MaxPriceChangeNotAllowed | Max pris ändring är inte tillåten. | Du kan inte ändra det högsta priset för den här virtuella datorn. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Virtuell Azure-dator stöds inte för den här API-versionen. | API-versionen måste vara 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Virtuell Azure-dator stöds inte för den här virtuella dator storleken {0} . | Välj en annan VM-storlek. Mer information finns i [Virtual Machines för Azure-plats](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Högsta pris stöds bara för Azure-Virtual Machines. | Mer information finns i [Virtual Machines för Azure-plats](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Begäran om att flytta resurser innehåller en virtuell Azure-dator. Stöds inte. Läs fel informationen om ID för virtuella datorer. | Det går inte att flytta Virtual Machines på Azure-platsen. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Begäran om att flytta resurser innehåller en skalnings uppsättning för en virtuell Azure-dator. Stöds inte. Kontrol lera fel informationen för skalnings uppsättnings-ID: n för virtuella datorer. | Det går inte att flytta instanser av skalnings uppsättningar för virtuella Azure-datorer. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Virtuell dator i Azure-platsen stöds inte i skalnings uppsättningen för virtuella datorer med den virtuella datorns Orchestration-läge. | Ange Orchestration-läget till skalnings uppsättningen för den virtuella datorn för att kunna använda Azures virtuella dator instanser. |


**Nästa steg** Mer information finns på [plats Virtual Machines](./spot-vms.md).