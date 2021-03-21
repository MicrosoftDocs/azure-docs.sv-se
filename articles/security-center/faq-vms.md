---
title: Azure Security Center vanliga frågor och svar om virtuella datorer
description: Vanliga frågor och svar om virtuella datorer i Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 32023fca15de9d3f9258bc05166c3cff4184ea15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96499857"
---
# <a name="faq---questions-about-virtual-machines"></a>Vanliga frågor och svar om virtuella datorer


## <a name="what-types-of-virtual-machines-are-supported"></a>Vilka typer av virtuella datorer stöds?

Övervakning och rekommendationer är tillgängliga för virtuella datorer som skapats med hjälp av både [klassiska distributions modeller för och Resource Manager](../azure-resource-manager/management/deployment-models.md).

Se [plattformar som stöds i Azure Security Center](security-center-os-coverage.md) för en lista över plattformar som stöds.


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Varför är inte Azure Security Center identifiera program mot skadlig kod som körs på min virtuella Azure-dator?

Azure Security Center har insyn i program mot skadlig kod installerat via Azure-tillägg. Security Center kan till exempel inte identifiera program mot skadlig kod som har förinstallerats på en avbildning som du har angett eller om du har installerat program mot skadlig kod på dina virtuella datorer med hjälp av dina egna processer (till exempel konfigurations hanterings system).


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Varför visas meddelandet "det saknas skannings data" för min virtuella dator?

Det här meddelandet visas när det inte finns några genomsökningsdata för en virtuell dator. Det kan ta lite tid (mindre än en timme) för genomsökningsdata att fyllas i när datainsamling har aktiverats i Azure Security Center. Efter den första ifyllningen av genomsökningsdata får du det här meddelandet eftersom det inte finns några genomsökningsdata alls eller det finns inga nyliga genomsökningsdata. Genomsökningar fyller inte i för en virtuell dator i ett stoppat tillstånd. Det här meddelandet kan också visas om genomsöknings data inte har fyllts i nyligen (i enlighet med bevarande principen för Windows-agenten, som har ett standardvärde på 30 dagar).


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hur ofta Security Center sökning efter problem med operativ system, system uppdateringar och slut punkts skydd?

Nedan visas svars tiderna för Security Center genomsökningar av sårbarheter, uppdateringar och problem:

- Säkerhets konfiguration för operativ system – data uppdateras inom 48 timmar
- System uppdateringar – data uppdateras inom 24 timmar
- Endpoint Protection problem – data uppdateras inom 8 timmar

Security Center vanligt vis söker efter nya data varje timme och uppdaterar rekommendationerna i enlighet med detta. 

> [!NOTE]
> Security Center använder Log Analytics-agenten för att samla in och lagra data. Läs mer i [Azure Security Center plattforms migrering](./security-center-enable-data-collection.md).


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Varför visas meddelandet "VM-agenten saknas?"

Den virtuella dator agenten måste installeras på virtuella datorer för att data insamling ska kunna användas. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Information om hur du installerar VM-agenten på andra virtuella datorer finns i blogg inlägget [VM-agent och tillägg](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).