---
title: Azure säkerhets kontroll – incident svar
description: Incident svar för Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fb4c4c5a0cf6610af17aabc562c42d2e0eb4e6a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409101"
---
# <a name="security-control-incident-response"></a>Säkerhets kontroll: incident svar

Skydda organisationens information, samt dess rykte, genom att utveckla och implementera en infrastruktur för incident svar (t. ex. planer, definierade roller, utbildning, kommunikation, hanterings övervakning) för att snabbt upptäcka ett angrepp och sedan effektivt ta med skadan, utrota angriparens närvaro och återställa nätverkets och systemens integritet.

## <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10.1 | 19,1, 19,2, 19,3 | Kund |

Bygg ut en incident svars guide för din organisation. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.  

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10,2 | 19,8 | Kund |

Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md)

## <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10,3 | 19 | Kund |

Genomför övningar för att testa dina Systems svar på incident hantering på en vanlig takt för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10,4 | 19,5 | Kund |

Kontakt information om säkerhets incidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har öppnats av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../../security-center/security-center-provide-security-contact-details.md)

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10.5 | 19,6 | Kund |

Exportera dina Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker med Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

## <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

| Azure-ID | CIS-ID: n | Ligger |
|--|--|--|
| 10,6 | 19 | Kund |

Använd funktionen för automatisering av arbets flöde i Azure Security Center att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../../security-center/workflow-automation.md)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [inträngande tester och röda team övningar](security-control-penetration-tests-red-team-exercises.md)