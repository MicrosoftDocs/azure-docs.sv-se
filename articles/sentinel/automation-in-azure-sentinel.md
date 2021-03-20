---
title: Introduktion till automatisering i Azure Sentinel | Microsoft Docs
description: Den här artikeln beskriver funktionerna för säkerhets dirigering, automatisering och svar (SOAR) i Azure Sentinel och beskriver dess SOAR-komponenter – Automation-regler och spel böcker.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610096"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Säkerhets dirigering, automatisering och svar (SOAR) i Azure Sentinel

I den här artikeln beskrivs funktionerna för säkerhets dirigering, automatisering och svar (SOAR) i Azure Sentinel, och hur du kan använda Automation-regler och spel böcker som svar på säkerhetshot ökar din SOC effektivitet och sparar tid och resurser.

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel som en SOAR-lösning

### <a name="the-problem"></a>Problemet

SIEM/SOC-team är vanligt vis översvämmas med säkerhets aviseringar och incidenter regelbundet, på volymer så stora att tillgänglig personal är överväldigad. Detta resulterar i en alltför ofta i situationer där många aviseringar ignoreras och många incidenter inte har undersökts, vilket gör att organisationen blir sårbar för angrepp som går vidare.

### <a name="the-solution"></a>Lösningen

Azure Sentinel, förutom att vara ett SIEM-system (Security information and Event Management), är också en plattform för säkerhets dirigering, automation och respons (SOAR). Ett av dess primära syfte är att automatisera alla återkommande och förutsägbara åtgärder, svar och reparationer som är ansvariga för ditt Security Operations Center och personal (SOC/SecOps), vilket frigör tid och resurser för mer djupgående undersökning av och jakt på avancerade hot. Automation tar några olika formulär i Azure Sentinel, från automatiserings regler som centralt hanterar automatisering av incident hantering och-svar, till spel böcker som kör förbestämda sekvenser av åtgärder för att tillhandahålla kraftfull och flexibel avancerad automatisering av dina hot svars uppgifter.

## <a name="automation-rules"></a>Automation-regler

Automation-regler är ett nytt begrepp i Azure Sentinel. Med den här funktionen kan användarna centralt hantera automatisering av incident hantering. Förutom att du kan tilldela spel böcker till incidenter (inte bara aviseringar som tidigare), kan du med automatiserings regler också automatisera svar för flera analys regler samtidigt, tagga, tilldela eller stänga incidenter utan att behöva spel böcker och styra ordningen på de åtgärder som utförs. Automation-regler effektiviserar automatiserings användningen i Azure Sentinel och gör det möjligt att förenkla komplexa arbets flöden för dina incident Dirigerings processer.

Läs mer i den här [fullständiga förklaringen av Automation-regler](automate-incident-handling-with-automation-rules.md).

> [!IMPORTANT]
>
> - Funktionen **automatiserings regler** är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="playbooks"></a>Spelböcker

En Spelbok är en samling åtgärder för svar och reparationer och logik som kan köras från Azure Sentinel som en rutin. En Spelbok kan hjälpa dig att automatisera och dirigera ditt hot svar, det kan integreras med andra system både internt och externt och kan ställas in så att de körs automatiskt som svar på vissa aviseringar eller incidenter, när de utlöses av en analys regel eller en Automation-regel. Det kan också köras manuellt på begäran, som svar på aviseringar på sidan incidenter.

Spel böcker i Azure Sentinel baseras på arbets flöden som skapats i [Azure Logic Apps](../logic-apps/logic-apps-overview.md), en moln tjänst som hjälper dig att schemalägga, automatisera och dirigera uppgifter och arbets flöden mellan system i hela företaget. Det innebär att spel böcker kan dra nytta av alla kraft-och anpassningsbarhet för Logic Apps integrations-och Dirigerings funktioner och lättanvända design verktyg samt skalbarhet, tillförlitlighet och service nivå för en Azure-tjänst på nivå 1.

Lär dig mer med den här [fullständiga förklaringen av spel böcker](automate-responses-with-playbooks.md).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur Azure Sentinel använder Automation för att hjälpa dina SOC att arbeta effektivare och effektivt.

- Information om automatisering av incident hantering finns i [Automatisera incident hantering i Azure Sentinel](automate-incident-handling-with-automation-rules.md).
- Läs mer om avancerade automatiserings alternativ i [Automatisera hot svar med spel böcker i Azure Sentinel](automate-responses-with-playbooks.md).
- Information om hur du implementerar Automation-regler och spel böcker finns i [Självstudier: använda spel böcker för att automatisera hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).
