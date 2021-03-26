---
title: Azures säkerhets bas linje för Cloud Shell
description: Cloud Shell säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b0045eacea7fdc13abdad35b5567eba3e994dcf9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558881"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Azures säkerhets bas linje för Cloud Shell

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Cloud Shell. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Cloud Shell. **Kontroller** som inte är tillämpliga på Cloud Shell har uteslutits.

 
Om du vill se hur Cloud Shell helt mappar till Azures säkerhets mätning, se den [fullständiga Cloud Shell mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: kunderna kan distribuera Azure Cloud Shell till en kund som äger Virtual Network.

När du distribuerar Azure Cloud Shell till en kund som ägs Virtual Network måste du skapa eller använda ett befintligt virtuellt nätverk. Se till att det valda virtuella nätverket har en nätverks säkerhets grupp som tillämpas på dess undernät och nätverks åtkomst kontroller som kon figurer ATS specifika för programmets betrodda portar och källor.

- [Distribuera Cloud Shell till ett virtuellt Azure-nätverk](private-vnet.md)

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../virtual-network/tutorial-filter-network-traffic.md)

- [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som använder samma auktorisering som används för att komma åt Azure Portal, i det här fallet kan en enkel inloggning i Azure Portal också autentisera dig med Cloud Shell.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som använder samma auktorisering som används för att komma åt Azure Portal, i det här fallet måste alla multifaktorautentisering som krävs för att ansluta till Azure Portal också krävas för Cloud Shell.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningarna övervakas och uppdateras av Cloud Shells teamet.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna ansvarar för att köra automatiserade sårbarhets sökverktyg mot program som körs i miljön.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningarna övervakas och uppdateras av Cloud Shells teamet.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna ansvarar för hantering av program varu korrigeringar som körs i sin miljö.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningarna övervakas och uppdateras av Cloud Shells teamet.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att åtgärda sårbarheter som upptäcks genom genomsökning av program varans sårbarhet. Exportera genomsöknings resultat med jämna mellanrum och jämför resultaten med tidigare genomsökningar för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationer för sårbarhets hantering som föreslås av Azure Security Center kan du pivotera till den valda lösningens Portal för att visa historiska skannings data.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningarna övervakas och uppdateras av Cloud Shells teamet.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att åtgärda sårbarheter som upptäcks genom genomsökning av program varans sårbarhet.  Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringar som tillhandahålls av ditt genomsöknings verktyg från tredje part. 

- [NIST-publicering – vanliga sårbarhets bedömnings system](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet.  Kunden kan installera egna verktyg i sin egen avbildning enligt organisationens behov och verktygen kräver inte `sudo` behörigheter under installationen.

Kunderna rekommenderas att skapa en inventering av godkänd program vara som installeras via Azure Cloud Shell enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: Azure Cloud Shell är en kostnads fri tjänst utan kund ägda till gångar.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet. 

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att övervaka program vara som körs i miljön för att se till att de är godkända per organisations princip.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Cloud Shell är en kostnads fri tjänst utan kund ägda till gångar.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet. 

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att övervaka program vara som körs i miljön för att se till att program som inte godkänts hanteras per organisations princip.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: Azure Cloud Shell är en kostnads fri tjänst utan kund ägda till gångar.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet.  Det går inte att ta bort specifika verktyg från kunden.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att övervaka program som körs i miljön för att se till att de är godkända per organisations princip.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningarna övervakas och uppdateras av Cloud Shells teamet.

Azure Cloud Shell gör det möjligt för kunder att installera egna verktyg eller program vara i sin egen avbildning enligt organisationens behov.

Kunderna är ansvariga för att underhålla en inventering av godkänd program vara som körs i miljön för att se till att de är godkända program per organisations princip.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Åtgärder som vidtas i Cloud Shell fungerar på samma sätt som åtgärder som vidtas från samma verktyg eller språk som körs i en lokal miljö.  Åtgärder från enskilda verktyg och språk bör vara begränsade, kunderna kan inte begränsa åtkomsten till Cloud Shell eller begränsa vad som är tillgängligt för en användare.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: Azure Cloud Shell kan isoleras i ett virtuellt kund nätverk.

- [Distribuera Cloud Shell till ett virtuellt Azure-nätverk](private-vnet.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Cloud Shell tillåter att skript körs i, redige ras och överförs till Cloud Shells miljön.  Vi rekommenderar att du flyttar autentiseringsuppgifter till Azure Key Vault.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet.  Kunden kan installera egna verktyg i sin egen avbildning enligt organisationens behov och verktygen kräver inte `sudo` behörigheter under installationen.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: Azure Cloud Shell är en webbläsarbaserad kommando rads upplevelse som används för interaktiv hantering av moln resurser.  Varje kund behållare är tillfällig en ny behållare används för varje session.  Behållar avbildningar och verktyg övervakas och uppdateras av Cloud Shells teamet.  Kunden kan installera egna verktyg i sin egen avbildning enligt organisationens behov och verktygen kräver inte `sudo` behörigheter under installationen.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller den analytiskt som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md) 

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)