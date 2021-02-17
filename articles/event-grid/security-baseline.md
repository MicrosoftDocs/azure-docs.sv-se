---
title: Azures säkerhets bas linje för Event Grid
description: Event Grid säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ef0f308c3ea2986301b0247111ef96bb133b57f4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571936"
---
# <a name="azure-security-baseline-for-event-grid"></a>Azures säkerhets bas linje för Event Grid

Azures säkerhets bas linje för Microsoft Azure Event Grid innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution. Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer. Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: du kan använda privata slut punkter för att tillåta ingress av händelser direkt från ditt virtuella nätverk till dina event Grid ämnen och domäner på ett säkert sätt över en privat länk utan att gå via det offentliga Internet. När du skapar en privat slut punkt för Event Grid ämne eller domän ger den säker anslutning mellan klienter i ditt VNet och din Event Grid-resurs. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och Event Grid tjänsten använder en säker privat länk.

Azure Event Grid stöder också offentliga IP-baserade åtkomst kontroller för publicering till ämnen och domäner. Med IP-baserade kontroller kan du begränsa utgivare till ett ämne eller en domän till endast en uppsättning godkända datorer och moln tjänster. Den här funktionen kompletterar de autentiseringsmekanismer som stöds av Event Grid. 

- [Mer information om Event Grid privata slut punkter](network-security.md#private-endpoints)

- [Mer information om Event Grid IP-brandvägg](network-security.md#ip-firewall)

- [Azure Event Grid nätverks säkerhet](network-security.md) 

- [Översikt av Azure Private Link](../private-link/private-link-overview.md)

- [Azure nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ rekommendationer för nätverks skydd för att skydda dina event Grid resurser i Azure. Om du använder Azure Virtual Machines för att få åtkomst till dina Event Grid-resurser, aktiverar du flödes loggar för nätverks säkerhets grupper (NSG) och skickar loggar till ett lagrings konto för trafik

händelse.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: du kan konfigurera IP-brandväggen för din event Grid resurs för att begränsa åtkomsten via det offentliga Internet från endast en Välj uppsättning IP-adresser eller IP-adressintervall.

Du kan konfigurera privata slut punkter för att begränsa åtkomsten från enbart från valda virtuella nätverk.

Aktivera DDoS Protection standard på dessa virtuella nätverk för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser. Mer information finns i följande artiklar: 

- [Konfigurera privata slut punkter för Azure Event Grid ämnen eller domäner](configure-private-endpoints.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Mer information om Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: om du använder Azure Virtual Machines för att få åtkomst till dina event Grid-resurser aktiverar du NSG-flöden (Network Security Group) och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Observera att nätverks principer är inaktiverade som standard när privata slut punkter skapas för Event Grid så att arbets flödet inte fungerar.

Om det behövs för att undersöka avvikande aktivitet aktiverar du Network Watcher paket fångst.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll.  När nytto lasts granskning inte är ett krav kan du använda Azure Firewall Threat intelligence. Azure Firewall Threat Intelligence-baserad filtrering används för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att identifiera och/eller blockera skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser i virtuella nätverk som behöver åtkomst till dina Azure Event Grid-resurser använder du Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel AzureEventGrid) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Så här använder du service tag-koden för Azure Event Grid](network-security.md#service-tags)

- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är kopplade till dina Azure Event Grid namnrum med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. EventGrid" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för dina Event Grid-resurser. 

Du kan också använda inbyggda princip definitioner som är relaterade till Azure Event Grid, t. ex.:-Azure Event Grid domäner ska använda privata länkar-Azure Event Grid ämnen bör använda privata linksAzure
- [inbyggda principer för Event Grid resurser](../governance/policy/samples/built-in-policies.md#event-grid)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är kopplade till dina Azure Event Grid-resurser för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Event Grid. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Event Grid. Använd Log Analytics arbets ytor i Azure Monitor för att fråga och utföra analyser och Använd lagrings konton för långsiktig/lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM).

- [Så här aktiverar du diagnostikloggar för Azure Event Grid](diagnostic-logs.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: diagnostikinställningar gör att event Grid användare kan samla in och Visa fel loggar för publicering och leverans i antingen ett lagrings konto, en Event Hub eller en Log Analytics arbets yta.

- [Aktivera diagnostikloggar för Azure Event Grid-ämnen eller domäner](enable-diagnostic-logs-topic.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperioden för Log Analytics arbets ytor som är kopplade till dina Azure Event Grid resurser enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet från Azure Event Grid. Använd Azure Monitor och en Log Analytics arbets yta för att granska loggar och köra frågor på loggdata.

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part. 

- [Aktivera diagnostikloggar för Azure Event Grid-ämnen eller domäner](enable-diagnostic-logs-topic.md)

- [Så här kör du frågor för Azure Event Grid i Log Analytics arbets ytor](diagnostic-logs.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Komma igång med Log Analytics frågor](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera diagnostikinställningar i händelse rutnätet för att få åtkomst till publicerings-och leverans fel loggar. Aktivitets loggar, som är automatiskt tillgängliga, innehåller händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element. Du kan skicka loggarna till en Log Analytics-arbetsyta. Använd Azure Security Center med Log Analytics för övervakning och aviseringar om avvikande aktivitet som finns i säkerhets loggar och händelser. 

Du kan också skapa aviseringar för Azure Event Grid mått och aktivitets logg åtgärder. Du kan skapa aviseringar för både publicerings-och leverans mått för Azure Event Grid resurser (ämnen och domäner). 

Dessutom kan du publicera din Log Analytics arbets yta till Azure Sentinel eftersom den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

- [Så här aktiverar du diagnostikloggar för Azure Event Grid ämnen eller domäner](enable-diagnostic-logs-topic.md)

- [Ange aviseringar för Azure Event Grid mått och aktivitets loggar](set-alerts.md)

- [Information om Event Grid diagnostiskt logg schema](diagnostic-logs.md)

- [Skapa, Visa och hantera logg aviseringar med Azure Monitor](../azure-monitor/alerts/alerts-log.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure Event Grid bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Event Grid bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Event Grid gör att du kan styra åtkomst nivån som ges till olika användare för att utföra olika hanterings åtgärder, till exempel lista händelse prenumerationer, skapa nya och generera nycklar. Event Grid använder rollbaserad åtkomst kontroll i Azure (Azure RBAC). Event Grid stöder inbyggda roller samt anpassade roller.

Med rollbaserad åtkomst kontroll i Azure (Azure RBAC) kan du hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen.

- [Auktorisera åtkomst till Event Grid resurser](security-authorization.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: åtkomst hantering till Event Grid resurser styrs via Azure Active Directory (AD). Azure AD har inte begreppet standard lösen ord.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton.

Du kan också aktivera en just-in-Time-åtkomst genom att använda Azure AD Privileged Identity Management och Azure Resource Manager.

Event Grid kan aktivera en hanterad tjänst identitet för Azure Event Grid-ämnen eller domäner och använda den för att vidarebefordra händelser till destinationer som stöds, till exempel Service Bus köer och ämnen, Event Hub och lagrings konton. Token för signatur för delad åtkomst (SAS) används för att publicera händelser till Azure Event Grid. Skapa standard operativa procedurer kring händelse åtkomst, vidarebefordran och publicering med dessa konton.

- [Autentisera händelse leverans till händelse hanterare (Azure Event Grid)](security-authentication.md)

- [Autentisera publicerings klienter (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: ej tillämpligt; Event Grid tjänsten stöder inte SSO.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: ej tillämpligt; Event Grid tjänsten använder inte Multi-Factor Authentication

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: ej tillämpligt; inga Event Grid scenarier kräver arbets stationer med privilegie rad åtkomst. 

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: inte tillämpligt. Event Grid använder inte Azure AD för att autentisera händelse publicerings klienter. Det stöder autentisering via SAS-nycklar.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Event Grid kan aktivera en hanterad tjänst identitet för Azure Event Grid-ämnen eller domäner och använda den för att vidarebefordra händelser till destinationer som stöds, till exempel Service Bus köer och ämnen, Event Hub och lagrings konton. Token för signatur för delad åtkomst (SAS) används för att publicera händelser till Azure Event Grid. 

- [Autentisera händelse leverans till händelse hanterare (Azure Event Grid)](security-authentication.md)

- [Autentisera publicerings klienter (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 
 
Använd Azure Active Directory (AD) Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Distribuera Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azure-aktivitets loggar med Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD Identity Protection funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.
 
 
 
- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ej tillämpligt; Event Grid tjänsten har inte stöd för Customer Lockbox för närvarande.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.
 
 
 
- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. Du kan begränsa åtkomst nivån till dina Azure-resurser som dina program och företags miljöer kräver. Du kan styra åtkomsten till Azure-resurser via Azure RBAC.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure Event Grid kräver https för publicering och stöder HTTPS för att leverera händelser till en webhook-slutpunkt. I Azure Global rekommenderar Event Grid både 1,1-och 1,2-versioner av TLS, men vi rekommenderar starkt att du använder 1,2-versionen. I nationella moln, till exempel Azure Government och Azure som drivs av 21Vianet i Kina, Event Grid endast stöd för 1,2-versioner av TLS. 

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Event Grid. Implementera en lösning från tredje part om det behövs för efterlevnad.

För den underliggande plattformen, som hanteras av Microsoft, behandlar Microsoft allt kund innehåll som känsligt och ger fantastiska längder för att skydda mot förlust och exponering av kund information. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Azure Event Grid stöder användning av Azure Active Directory (AD) för att auktorisera begär anden till Event Grid resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst.

- [Auktorisera åtkomst till Event Grid resurser](security-authorization.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Event Grid resurser och andra viktiga eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.
 
 
 
- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

* Otillåtna resurstyper
* Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azures resurs hanteraren genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".
 
 
 
- [ Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för Azure Event Grid tjänsten med Azure policy. Använd Azure Policy alias i namn området "Microsoft. EventGrid" om du vill skapa anpassade principer för granskning eller tillämpa konfigurationen för dina Azure Event Grid-tjänster.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation innan distributioner.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser. Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina event Grid eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure databaser](/azure/devops/repos/index)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. EventGrid" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser. Du kan också använda Azure Policy för att varna och granska Azure-resursfiler.

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Event Grid använder signaturen för signatur för delad åtkomst (SAS) för att publicera händelser till Event Grid ämnen eller domäner. Genererar SAS-token med endast åtkomst till de resurser som behövs i ett begränsat tids fönster.

Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla hemlig hantering för dina moln program.

- [Autentisera publicerings klienter (Azure Event Grid)](security-authenticate-publishing-clients.md)

- [Använda hanterade identiteter för Azure-resurser](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Event Grid kan aktivera en hanterad tjänst identitet för Azure Event Grid-ämnen eller-domäner. Använd den för att vidarebefordra händelser till stödda destinationer som Service Bus köer och ämnen, Event Hub och lagrings konton.

- [Händelse leverans med en hanterad identitet](managed-service-identity.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Event Grid), men det körs inte på kund innehåll.

Det är ditt ansvar att i förväg genomsöka allt innehåll som laddas upp till Azure-resurser som inte är Compute. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Event Grid har en automatisk följande geodr (geo Disaster Recovery) för meta-data som inte bara är till för nya, utan alla befintliga domäner, ämnen och händelse prenumerationer. Om en hel Azure-region stängs av kommer Event Grid redan att ha alla dina säkerhetsrelaterade infrastruktur-metadata synkroniserade med en kopplad region.

- [Geo haveri beredskap på Server sidan i Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Event Grid har en automatisk följande geodr (geo Disaster Recovery) för meta-data som inte bara är till för nya, utan alla befintliga domäner, ämnen och händelse prenumerationer. Om en hel Azure-region stängs av kommer Event Grid redan att ha alla dina säkerhetsrelaterade infrastruktur-metadata synkroniserade med en kopplad region.

För närvarande stöder Event Grid inte Kundhanterade nycklar. 

- [Geo haveri beredskap på Server sidan i Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Event Grid har en automatisk följande geodr (geo Disaster Recovery) för meta-data som inte bara är till för nya, utan alla befintliga domäner, ämnen och händelse prenumerationer. Om en hel Azure-region stängs av kommer Event Grid redan att ha alla dina säkerhetsrelaterade infrastruktur-metadata synkroniserade med en kopplad region.

För närvarande stöder Event Grid inte Kundhanterade nycklar. 

- [Geo haveri beredskap på Server sidan i Azure Event Grid](geo-disaster-recovery.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera mjuk borttagning och rensning av skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. 
 

För närvarande stöder Event Grid inte Kundhanterade nycklar. 

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller den analytiskt som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

 
 
 
 Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov.
 
 
 
- [ NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.
 
 
 
- [ Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöde Azure Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden i Security Center](../security-center/workflow-automation.md)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)