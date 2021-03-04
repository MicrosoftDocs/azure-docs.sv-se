---
title: Azure Security benchmark v2 – incident svar
description: Incident svar för Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b9295482c2464eb80bc49fa707744f49a2fbebfd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035385"
---
# <a name="security-control-v2-incident-response"></a>Säkerhets kontroll v2: incident svar

Incident svar omfattar kontroller i incident svars livs cykel-förberedelse, identifiering och analys, inne slutning och efter incident aktiviteter. Detta inkluderar användning av Azure-tjänster som Azure Security Center och Sentinel för att automatisera incident svars processen.

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa [mer i information om det inbyggda initiativet för Azure Security benchmark-efterlevnad: incident svar](../../governance/policy/samples/azure-security-benchmark.md#incident-response)

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Se till att din organisation har processer som svarar på säkerhets incidenter, har uppdaterat dessa processer för Azure och utnyttjar dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Konfigurera kontakt information för säkerhets incidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Konfigurera en säkerhetskontakt i Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar av hög kvalitet

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Se till att du har en process för att skapa aviseringar med hög kvalitet och mäta kvaliteten på aviseringar. På så sätt kan du lära dig lektioner från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar genom att slå samman och korrelera olika signalkällor. 

Azure Security Center tillhandahåller aviseringar med hög kvalitet för flera olika typer av Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter, för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar. Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Tillhandahålla en kontext till analytiker på vilka incidenter kan fokusera på den första baserat på aviseringens allvarlighets grad och till gångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatisera manuella återkommande uppgifter för att påskynda svars tiden och minska belastningen på analytiker. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsåtgärder](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentberedskap](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Hot information](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)