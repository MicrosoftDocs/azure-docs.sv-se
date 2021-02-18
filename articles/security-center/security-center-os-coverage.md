---
title: Plattformar som stöds av Azure Security Center | Microsoft Docs
description: Det här dokumentet innehåller en lista över plattformar som stöds av Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 4cc8130d34b5f51728fb2e7fb47c1fb0717d2138
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595132"
---
# <a name="supported-platforms"></a>Plattformar som stöds 

På den här sidan visas de plattformar och miljöer som stöds av Azure Security Center.

## <a name="combinations-of-environments"></a>Kombinationer av miljöer <a name="vm-server"></a>

Azure Security Center stöder virtuella datorer och servrar på olika typer av hybrid miljöer:

* Endast Azure
* Azure och lokalt
* Azure och andra moln
* Azure, andra moln och lokalt

För en Azure-miljö aktive rad på en Azure-prenumeration identifierar Azure Security Center automatiskt IaaS-resurser som distribueras i prenumerationen.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Security Center är beroende av [Log Analytics agenten](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Se till att datorerna kör ett av de operativ system som stöds för den här agenten enligt beskrivningen på följande sidor:

* [Log Analytics agent för operativ system som stöds av Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Log Analytics agent för operativ system som stöds av Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Se också till att din Log Analytics-agent är [korrekt konfigurerad för att skicka data till Security Center](security-center-enable-data-collection.md#manual-agent)

Mer information om de Security Center funktioner som är tillgängliga i Windows och Linux finns i [funktions täckning för datorer](security-center-services.md).

> [!NOTE]
> Även om Azure Defender är utformat för att skydda servrar, stöds de flesta funktioner i **Azure Defender för servrar** för Windows 10-datorer. En funktion som inte stöds för närvarande är [Security Center integrerad EDR-lösning: Microsoft Defender för slut punkt](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Hanterade virtuella maskin tjänster <a name="virtual-machine"></a>

Virtuella datorer skapas också i en kund prenumeration som en del av vissa Azure-hanterade tjänster, till exempel Azure Kubernetes (AKS), Azure Databricks med mera. Security Center identifierar de här virtuella datorerna så att Log Analytics-agenten kan installeras och konfigureras om ett operativ system som stöds är tillgängligt.

## <a name="cloud-services"></a>Cloud Services <a name="cloud-services"></a>

Virtuella datorer som körs i en moln tjänst stöds också. Endast webb-och arbets roller för moln tjänster som körs i produktions platser övervakas. Mer information om moln tjänster finns i [Översikt över Azure Cloud Services](../cloud-services/cloud-services-choose-me.md).

Skydd för virtuella datorer som finns i Azure Stack stöds också. Mer information om Security Centers integrering med Azure Stack finns i [publicera dina Azure Stack virtuella datorer i Security Center](quickstart-onboard-machines.md). 

## <a name="next-steps"></a>Nästa steg

- Lär dig hur [Security Center samlar in data med hjälp av Log Analytics-agenten](security-center-enable-data-collection.md).
- Lär dig hur [Security Center hanterar och skyddar data](security-center-data-security.md).
- Lär dig att [planera och förstå design överväganden för att anta Azure Security Center](security-center-planning-and-operations-guide.md).