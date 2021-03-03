---
title: Region mappningar för container Insights
description: Beskriver de region mappningar som stöds mellan behållar insikter, Log Analytics arbets yta och anpassade mått.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728883"
---
# <a name="region-mappings-supported-by-container-insights"></a>Region mappningar som stöds av container Insights

 När du aktiverar container Insights, stöds bara vissa regioner för att länka en Log Analytics-arbetsyta och ett AKS-kluster och samla in anpassade mått som skickas till Azure Monitor.

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

Om du vill börja övervaka ditt AKS-kluster läser du [så här aktiverar du behållar insikter](container-insights-onboard.md) för att förstå krav och tillgängliga metoder för att aktivera övervakning.  
