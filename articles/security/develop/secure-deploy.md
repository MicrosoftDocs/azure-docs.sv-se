---
title: Distribuera säkra program på Microsoft Azure
description: Den här artikeln beskriver metod tips som kan vara bra att tänka på under lanserings-och svars faserna i ditt webb program projekt.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517197"
---
# <a name="deploy-secure-applications-on-azure"></a>Distribuera säkra program på Azure
I den här artikeln presenterar vi säkerhets aktiviteter och kontroller för att tänka på när du distribuerar program för molnet. Säkerhets frågor och koncept som du bör tänka på under lanserings-och svars faserna i Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) omfattas. Målet är att hjälpa dig att definiera aktiviteter och Azure-tjänster som du kan använda för att distribuera ett säkrare program.

Följande SDL-faser beskrivs i den här artikeln:

- Frisläpp
- Svarsåtgärder

## <a name="release"></a>Frisläpp
Fokus för lanserings fasen är redo för ett projekt för offentlig version.
Detta innefattar planerings sätt för att effektivt utföra åtgärder för service efter lansering och åtgärda säkerhets problem som kan uppstå senare.

### <a name="check-your-applications-performance-before-you-launch"></a>Kontrol lera programmets prestanda innan du startar

Kontrol lera programmets prestanda innan du startar det eller distribuera uppdateringar till produktionen. Kör molnbaserade [belastnings test](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) genom att använda Visual Studio för att hitta prestanda problem i ditt program, förbättra distributions kvaliteten, se till att ditt program alltid är igång eller tillgängligt och att ditt program kan hantera trafik för din start.

### <a name="install-a-web-application-firewall"></a>Installera en brand vägg för webbaserade program

Webbprogram blir i allt större utsträckning föremål för attacker där kända svagheter i programmen utnyttjas. Vanliga bland dessa sårbarheter är SQL-injektering-attacker och skript angrepp över flera webbplatser. Det kan vara svårt att förhindra dessa attacker i program koden. Det kan kräva rigoröst underhåll, uppdatering och övervakning på många skikt i programtopologin. En centraliserad WAF hjälper till att förenkla säkerhets hanteringen. En WAF-lösning kan också reagera på ett säkerhetshot genom att korrigera en känd sårbarhet på en central plats och skydda varje enskilt webb program.

[Azure Application Gateway-WAF](../../web-application-firewall/ag/ag-overview.md) ger centraliserat skydd av dina webb program mot vanliga sårbarheter och sårbarheter. WAF baseras på regler från [OWASP Core-regeln uppsättningarna](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,0 eller 2.2.9.

### <a name="create-an-incident-response-plan"></a>Skapa en plan för incidenthantering

Att förbereda en incident svars plan är avgörande för att hjälpa dig att åtgärda nya hot som kan uppstå över tid. Att förbereda en incident plan för incidenter omfattar att identifiera lämpliga kontakter och upprätta säkerhets Service planer för kod som ärvs från andra grupper i organisationen och för licensierad kod från tredje part.

### <a name="conduct-a-final-security-review"></a>Genomför en slutgiltig säkerhets granskning

Avsiktlig granskning av alla säkerhets aktiviteter som har utförts bidrar till att säkerställa att din program varu version eller ditt program är redo. Den slutliga säkerhets granskningen (FSR) omfattar vanligt vis att undersöka hot modeller, verktyg och prestanda för de kvalitets portar och fel fält som definierades i krav fasen.

### <a name="certify-release-and-archive"></a>Certifiera version och Arkiv

Att certifiera program vara före en version säkerställer att kraven på säkerhet och sekretess uppfylls. Arkivering av alla relevanta data är nödvändig för att utföra underhålls uppgifter efter publiceringen. Arkivering bidrar också till lägre långsiktiga kostnader som är kopplade till en hållbar program varu teknik.

## <a name="response"></a>Svarsåtgärder
Fasen Response-release (svar efter lansering) i utvecklings teamet kan och är tillgänglig för att svara korrekt på alla rapporter om nya program varu hot och sårbarheter.

### <a name="execute-the-incident-response-plan"></a>Köra incident svars planen

Att kunna implementera incident svars planen som har inletts i versions fasen är nödvändig för att hjälpa till att skydda kunder från program säkerhet eller säkerhets risker som uppstår.

### <a name="monitor-application-performance"></a>Övervaka programprestanda

Kontinuerlig övervakning av programmet efter det att det har distribuerats kan hjälpa dig att identifiera prestanda problem och säkerhets problem.
Azure-tjänster som hjälper dig med program övervakning är:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) är en utöknings bar APM-tjänst (Application Performance Management) för webbutvecklare på flera plattformar. Du kan använda den för att övervaka ditt live-webbprogram. Application Insights identifierar automatiskt prestanda avvikelser. Den innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå vad användarna faktiskt gör med din app. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) hjälper dig att förhindra, identifiera och svara på hot med ökad insyn i (och kontroll över) säkerheten för dina Azure-resurser, inklusive webb program. Azure Security Center hjälper till att identifiera hot som annars kan gå vidare till. Det fungerar med olika säkerhetslösningar.

Security Centerens kostnads fria nivå erbjuder begränsad säkerhet enbart för dina Azure-resurser. [Security Center standard nivån](../../security-center/security-center-get-started.md) utökar dessa funktioner till lokala resurser och andra moln.
Security Center standard hjälper dig att:

  - Hitta och åtgärda säkerhets problem.
  - Använd åtkomst-och program kontroller för att blockera skadlig aktivitet.
  - Identifiera hot genom att använda analyser och intelligens.
  - Svara snabbt vid angrepp.

## <a name="next-steps"></a>Nästa steg
I följande artiklar rekommenderar vi säkerhets kontroller och aktiviteter som kan hjälpa dig att utforma och utveckla säkra program.

- [Utforma säkra program](secure-design.md)
- [Utveckla säkra program](secure-develop.md)