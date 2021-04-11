---
title: Komponenter i en strategi för att hantera DDoS-attacker
description: Lär dig hur du använder Azure DDoS Protection standard för att svara på DDoS-attacker.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: c028185a0753bc4da966ebc56bb792e57cac1f77
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103053"
---
# <a name="components-of-a-ddos-response-strategy"></a>Komponenter i en strategi för att hantera DDoS-attacker

En DDoS-attack som är riktad mot Azure-resurser kräver vanligt vis minimalt ingripande från en användar synpunkt. Att införliva DDoS-minskning som en del av en strategi för incident svar bidrar fortfarande till att minimera påverkan på affärs kontinuitet.

## <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft har ett omfattande hot informations nätverk. I det här nätverket används en samlad kunskap om en utökad säkerhets grupp som stöder Microsoft onlinetjänster, Microsoft-partner och-relationer i Internet Security-communityn. 

Som en kritisk infrastruktur leverantör får Microsoft tidiga varningar om hot. Microsoft samlar in Hot information från dess onlinetjänster och från dess globala kund bas. Microsoft införlivar all den här hot informationen igen i Azure DDoS Protection produkter.

Dessutom utför Microsoft Digital brottslighet-enheten (DCU) stötande strategier mot botnät. Botnät är en gemensam källa för kommando och kontroll för DDoS-attacker.

## <a name="risk-evaluation-of-your-azure-resources"></a>Riskbedömning av dina Azure-resurser

Det är absolut nödvändigt att förstå omfattningen av din risk från en DDoS-attack kontinuerligt. Fråga dig själv med jämna mellanrum:

- Vilka nya offentligt tillgängliga Azure-resurser behöver skydd?

- Finns det en enskild felpunkt i tjänsten? 

- Hur kan tjänster isoleras för att begränsa effekten av ett angrepp samtidigt som tjänster blir tillgängliga för giltiga kunder?

- Finns det virtuella nätverk där DDoS Protection standard ska aktive ras, men är det inte? 

- Är mina tjänster aktiva/aktiva med redundans över flera regioner?

Det är viktigt att du förstår det normala beteendet för ett program och förbereder att agera om programmet inte fungerar som förväntat under en DDoS-attack. Ha Övervakare som kon figurer ATS för affärs kritiska program som efterliknar klient beteende och meddelar dig när relevanta avvikelser upptäcks. Se [metod tips för övervakning och diagnostik](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) för att få insikter om hälso tillståndet för ditt program.

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Använd Application Insights för att övervaka ditt Live-webbprogram. Prestanda avvikelser identifieras automatiskt. Den innehåller analys verktyg som hjälper dig att diagnostisera problem och förstå vad användarna gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

## <a name="customer-ddos-response-team"></a>Kund DDoS-svars team

Att skapa en DDoS-svars grupp är ett viktigt steg i att svara på en attack snabbt och effektivt. Identifiera kontakter i din organisation som ska övervaka både planering och körning. Det här DDoS-svars teamet bör noggrant förstå Azure DDoS Protection standard tjänsten. Se till att teamet kan identifiera och åtgärda ett angrepp genom att koordinera med interna och externa kunder, inklusive Microsoft Support-teamet. 

Vi rekommenderar att du använder simulerings övningar som en normal del av tjänstens tillgänglighet och kontinuitets planering, och de här övningarna bör omfatta skalnings testning. Se [testa genom simuleringar](test-through-simulations.md) och lär dig hur du simulerar DDoS test trafik mot dina offentliga Azure-slutpunkter.

## <a name="alerts-during-an-attack"></a>Aviseringar under en attack

Azure DDoS Protection standard identifierar och minimerar DDoS-attacker utan att användaren behöver vidta några åtgärder. Om du vill få ett meddelande när det finns en aktiv minskning av en skyddad offentlig IP-adress kan du [Konfigurera aviseringar](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>När du ska kontakta Microsoft-supporten

Azure DDoS Protection standard kunder har till gång till DRR-teamet (DDoS Rapid Response), som kan hjälpa till med angrepps undersökningen under en attack och analys efter angrepp. Se [DDoS Rapid Response](ddos-rapid-response.md) för mer information, inklusive när du ska engagera DRR-teamet.

## <a name="post-attack-steps"></a>Steg efter angrepp

Det är alltid en lämplig strategi att göra en Postmortem efter ett angrepp och justera DDoS-svars strategin efter behov. Saker att tänka på:

- Fanns det några avbrott i tjänsten eller användar upplevelsen på grund av brist på skalbar arkitektur?

- Vilka program eller tjänster drabbades mest?

- Hur effektivt var DDoS svars strategi och hur kan den förbättras?

Om du misstänker att du befinner dig under ett DDoS-angrepp kan du eskalera de vanliga support kanalerna för Azure.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en DDoS-skydds plan](manage-ddos-protection.md).