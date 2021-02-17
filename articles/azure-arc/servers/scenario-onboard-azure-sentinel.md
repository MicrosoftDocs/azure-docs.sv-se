---
title: Publicera Azure Arc-aktiverad server till Azure Sentinel
description: Lär dig hur du lägger till dina Azure Arc-aktiverade servrar i Azure Sentinel och övervaka deras säkerhets status proaktivt.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584745"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Publicera Azure Arc-aktiverade servrar till Azure Sentinel

Den här artikeln är avsedd att hjälpa dig att publicera din Azure Arc-aktiverade server till [Azure Sentinel](../../sentinel/overview.md) och börja samla in säkerhetsrelaterade händelser. Azure Sentinel tillhandahåller en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar i hela företaget.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar kontrollerar du att du uppfyller följande krav:

- En [Log Analytics-arbetsyta](../../azure-monitor/logs/data-platform-logs.md). Mer information om Log Analytics-arbetsytor finns i [utforma distributioner av Azure Monitors loggar](../../azure-monitor/logs/design-logs-deployment.md).

- Azure Sentinel [aktiverat i din prenumeration](../../sentinel/quickstart-onboard.md).

- Du är dator eller Server ansluten till Azure Arc-aktiverade servrar.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Publicera Azure Arc-aktiverade servrar till Azure Sentinel

Azure Sentinel har ett antal anslutningar för Microsoft-lösningar som är tillgängliga direkt och ger real tids integrering. För fysiska och virtuella datorer kan du installera Log Analytics-agenten som samlar in loggarna och vidarebefordrar dem till Azure Sentinel. Arc-aktiverade servrar stöder distribution av Log Analytics agenten på följande sätt:

- Använda ramverket för VM-tillägg.

    Med den här funktionen i Azure Arc-aktiverade servrar kan du distribuera Log Analytics-agentens VM-tillägg till en icke-Azure Windows-och/eller Linux-server. VM-tillägg kan hanteras med hjälp av följande metoder på dina hybrid datorer eller servrar som hanteras av Arc-aktiverade servrar:

    - [Azure Portal](manage-vm-extensions-portal.md)
    - [Azure CLI](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager-mallar](manage-vm-extensions-template.md)

- Använda Azure Policy.

    Med den här metoden använder du Azure Policy [distribuera Log Analytics agent till Linux-eller Windows Azure Arc-datorer](../../governance/policy/samples/built-in-policies.md#monitoring) inbyggda principer för att granska om den aktiverade servern har Log Analytics-agenten installerad. Om agenten inte är installerad distribueras den automatiskt med hjälp av en reparations uppgift. Alternativt, om du planerar att övervaka datorerna med Azure Monitor for VMs, använder du i stället alternativet för [att aktivera Azure Monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) för att installera och konfigurera Log Analytics agenten.

Vi rekommenderar att du installerar Log Analytics agent för Windows eller Linux med Azure Policy.

När dina Arc-aktiverade servrar är anslutna börjar dina data strömma till Azure Sentinel och är redo att börja arbeta med. Du kan visa loggarna i de [inbyggda arbets böckerna](../../sentinel/quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Nästa steg

Kom igång [med att identifiera hot med Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).