---
title: Azures säkerhets bas linje för Virtual Network NAT
description: Virtual Network NAT-säkerhetslinjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 887648526c8da5fc829abe48911fd6785c3d856f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576612"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Azures säkerhets bas linje för Virtual Network NAT

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Microsoft Virtual Network NAT. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Virtual Network NAT. **Kontroller** som inte gäller för Virtual Network NAT har uteslutits.

 
Om du vill se hur Virtual Network NAT fullständigt mappar till Azures säkerhets mätning, se den [fullständiga Virtual Network NAT-mappnings fil för NAT-säkerhet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Virtual Network NAT stöder inte kontroll av utgående trafik med nätverks säkerhets grupper (NSG).  Inkommande trafik tillåts bara som svar på ett utgående ursprungligt flöde.

Flödes loggar för nätverks säkerhets grupper (NSG) kan dock användas med NAT-gateway-resurser för att övervaka utgående utgående trafik.

Använd Azure Security Center och följ rekommendationerna för nätverks skydd för att skydda dina Azure-nätverks resurser. Aktivera flödes loggar för nätverks säkerhets grupper och skicka loggarna till ett Azure Storage konto för granskning. Du kan också skicka flödes loggarna till en Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter om trafik mönster i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet, identifiera frekventa fläckar och säkerhetshot, förstå trafikflödes mönster och hitta nätverks-felkonfigurationer. 

- [Översikt över Virtual Network NAT](./nat-overview.md)

- [NAT-gatewayresurs](./nat-gateway-resource.md)

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera Network Watcher paket fångst för att undersöka avvikande aktiviteter. 

- [Så här skapar du en Network Watcher-instans](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för undernät som kon figurer ATS med NAT-gateway-resurser med inbyggda eller anpassade Azure policy definitioner och tilldelningar.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i NAT-gatewayens resurser och virtuella nätverks resurser. Skapa aviseringar i Azure Monitor för att meddela dig när kritiska resurser ändras.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar som är relaterade till Virtual Network NAT via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och inaktivera dessa data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina NAT-gateway-resurser förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/essentials/platform-logs-overview.md) 

Virtual Network NAT ger för närvarande inga ytterligare diagnostikloggar som kan konfigureras av kunder.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center med Log Analytics arbets yta för övervakning och avisering om avvikande aktivitet i säkerhets loggar och händelser. Du kan i stället aktivera och inaktivera data till Azure Sentinel.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md) 

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll o.s.v.) i dina prenumerationer. 

Se till att du har rätt (Läs) behörigheter i din klient och räkna upp alla Azure-prenumerationer och-resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager baserade resurser som går framåt.

- [Azure Resource Graph-frågor](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: tillämpa taggar för hantering av Azure-resurser.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md) 

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md) 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: för närvarande inte tillgänglig

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.
Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 
- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att begränsa vilka tjänster du kan etablera i din miljö.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: ej tillämpligt; Virtual Network NAT saknar säkerhetskonfigurationer.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure policy definitioner, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS. 

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: ej tillämpligt; Virtual Network NAT saknar säkerhetskonfigurationer.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: en incident svars procedur bör skapas för erbjudandet för att säkerställa att lämpliga incident svars processer kan utföras så att de får rätt prioritetsordning till incident lösningarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: en incident bedömnings-och prioriterings procedur bör skapas för erbjudandet för att säkerställa att en lämplig risk-eller hot Poäng kan uppstå så att den får en lämplig prioriterings nivå till incident lösningarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: en säkerhets svars procedur för incidenter bör skapas och testas för erbjudandet för att säkerställa att rätt skydd kan ske med lämplig prioriterings nivå till den prognostiserade incidenten.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: en säkerhets svars procedur för incidenter bör skapas och testas för erbjudandet för att säkerställa att rätt skydd kan ske med lämplig prioriterings nivå till den prognostiserade incidenten.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: en säkerhets svars procedur för incidenter bör skapas och testas för erbjudandet för att säkerställa att rätt skydd kan ske med lämplig prioriterings nivå till den prognostiserade incidenten.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: en säkerhets svars procedur för incidenter bör skapas och testas för erbjudandet för att säkerställa att rätt skydd kan ske med lämplig prioriterings nivå till den prognostiserade incidenten.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)