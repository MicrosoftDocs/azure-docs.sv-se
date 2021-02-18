---
title: Azure Monitor för områdes mappningar för behållare
description: Beskriver de region mappningar som stöds mellan Azure Monitor för behållare, Log Analytics arbets yta och anpassade mått.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9b77242de3e7845a97874b663266103bf00a8e66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624905"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Region mappningar som stöds av Azure Monitor för behållare

 När du aktiverar Azure Monitor för behållare, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett AKS-kluster och samla in anpassade mått som skickas till Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappningar som stöds av Log Analytics Workspace

AKS-regioner som stöds visas i [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). Arbets ytan Log Analytics måste finnas i samma region utom de regioner som anges i följande tabell. Se [AKS-versions anmärkningar](https://github.com/Azure/AKS/releases) för uppdateringar.


|**AKS-kluster region** | **Region för Log Analytics arbets yta** |
|-----------------------|------------------------------------|
|**Afrika** | |
|SouthAfricaNorth |Västeuropa |
|SouthAfricaWest |Västeuropa |
|**Australien** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brasilien** | |
|Centrala | Usasödracentrala |
|**Kanada** ||
|Indien |Indiensödra |
|**Europa** | |
|FranceSouth |FranceCentral |
|Västrastorbritannien |UKSouth |
|**Indien** | |
|Australienöstra |Kanada |
|Usavästracentrala |Kanada |
|**Japan** | |
|Japanvästra |Japanöstra |
|**Korea** | |
|Koreasödra |Centrala |
|**USA** | |
|WestCentralUS<sup>1</sup>|Östra USA<sup>1</sup>|


<sup>1</sup> på grund av kapacitets begränsningar är regionen inte tillgänglig när du skapar nya resurser. Detta inkluderar en Log Analytics-arbetsyta. Befintliga länkade resurser i regionen bör dock fortsätta att fungera.

## <a name="custom-metrics-supported-regions"></a>Regioner som stöds för anpassade mått

Insamling av mått från Azure Kubernetes Services (AKS)-kluster noder och poddar stöds endast för publicering som anpassade mått i följande [Azure-regioner](../essentials/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt AKS-kluster läser du [så här aktiverar du Azure Monitor för behållare](container-insights-onboard.md) för att förstå kraven och tillgängliga metoder för att aktivera övervakning.  
