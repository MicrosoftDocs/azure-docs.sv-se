---
title: Azures säkerhets bas linje för Event Hubs
description: Azures säkerhets bas linje för Event Hubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 3f56fc1b321396c5cc6aa93e3f47fcaafcbe4ae4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202342"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azures säkerhets bas linje för Event Hubs

Azures säkerhets bas linje för Event Hubs innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: integreringen av händelse hubbar med tjänst slut punkter i virtuella nätverk ger säker åtkomst till meddelande funktioner från arbets belastningar, till exempel virtuella datorer som är kopplade till virtuella nätverk, med den nätverks trafik väg som skyddas i båda ändar.

En gång som har bundits till minst en tjänst slut punkt för ett virtuellt nätverk, accepterar respektive Event Hubs namn området inte längre trafik från var som helst men auktoriserade undernät i virtuella nätverk. I det virtuella nätverkets perspektiv binder du Event Hubs namn området till en tjänst slut punkt konfigurerar en isolerad nätverks tunnel från det virtuella nätverkets undernät till meddelande tjänsten. 

Du kan också skapa en privat slut punkt, vilket är ett nätverks gränssnitt som ansluter privat och säkert till Azure Event Hubs-tjänsten med hjälp av tjänsten Azure Private Link. Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute-eller VPN-anslutningar eller offentliga IP-adresser krävs. 

Du kan också skydda ditt Azure Event Hubs-namnområde med hjälp av brand väggar. Azure Event Hubs stöder IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Du kan ange brand Väggs regler med hjälp av Azure Portal, Azure Resource Manager mallar eller via Azure CLI eller Azure PowerShell.

Så här använder du tjänst slut punkter i virtuella nätverk med Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Mer information finns i integrera Azure-Event Hubs med Azures privata länk: https://docs.microsoft.com/azure/event-hubs/private-link-service .

Aktivera integrering av virtuella nätverk och brand väggar på Event Hubs namnrymd: https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Så här konfigurerar du IP-brandväggens regler för Azure Event Hubs-namnrymder: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och följ rekommendationer för nätverks skydd för att skydda dina Event Hubs resurser i Azure. Om du använder Azure Virtual Machines för att få åtkomst till dina Event Hub, aktiverar du NSG-flödes loggar (Network Security Group) och skickar loggar till ett lagrings konto för trafik granskning.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till dina Event Hub för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du DDoS-skydd: [https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)

Mer information om Azure Security Center integrerad Hot information: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: om du använder Azure Virtual Machines för att komma åt dina Event Hub, aktiverar du NSG-flöden (Network Security Group) och skickar loggar till ett lagrings konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Om det behövs för att undersöka avvikande aktivitet aktiverar du Network Watcher paket fångst.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar och använder du Trafikanalys: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Så här aktiverar du Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: om du använder Azure Virtual Machines för att komma åt dina Event Hub, väljer du ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last granskning. Om intrångs identifiering och/eller skydd som baseras på nytto Last kontroll inte krävs för din organisation kan du använda Azure Event Hubs inbyggd brand Väggs funktion. Du kan begränsa åtkomsten till Event Hubs namn område för ett begränsat antal IP-adresser eller en viss IP-adress med hjälp av brand Väggs regler.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Så här lägger du till en brand Väggs regel i Event Hubs för en angiven IP-adress:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center övervakning**: inte tillgänglig än

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: ej tillämpligt, den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är kopplade till dina Azure Event Hubs-namnområden med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. EventHub" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för dina Event Hubs-namnområden. Du kan också använda inbyggda princip definitioner som är relaterade till Azure Event Hubs, till exempel:

- Event Hub bör använda en tjänst slut punkt för virtuellt nätverk.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inbyggd Azure-princip för Event Hubs namnrymd:  https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy exempel för nätverk: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Så här skapar du en Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för virtuella nätverk och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet som är associerade med dina Event Hub.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Event Hubs. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: ej tillämpligt; Microsoft underhåller den tids källa som används för Azure-resurser, till exempel Azure Event Hubs, för tidsstämplar i loggarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: i Azure Monitor konfigurerar du loggar som är relaterade till händelse nav i diagnostikinställningar för aktivitets logg och händelsehubben för att skicka loggar till en Log Analytics arbets yta som ska frågas eller till ett lagrings konto för långsiktig lagring.

Så här konfigurerar du diagnostikinställningar för Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Förstå Azure aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för ditt Azure Event Hubs-namnområde. Det finns tre kategorier av diagnostikinställningar för Azure Event Hubs: Arkiv loggar, operativa loggar och autoskalning. Aktivera drift loggar för att samla in information om vad som händer under Event Hubs åtgärder, närmare bestämt åtgärds typen, inklusive skapande av Event Hub, använda resurser och status för åtgärden.

Dessutom kan du aktivera diagnostiska inställningar för Azure aktivitets logg och skicka dem till ett Azure Storage konto, en Event Hub eller en Log Analytics arbets yta. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure Event Hubs och andra resurser. Med aktivitets loggar kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, posta och ta bort) på dina Azure Event Hubs-namnområden.

Så här aktiverar du diagnostikinställningar för Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Så här aktiverar du diagnostikinställningar för Azure aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad för att samla in och granska händelser som rör Event Hub.

Så här ställer du in logg lagrings parametrar för Log Analytics arbets ytor: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat som rör dina Event Hub. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.
 

Mer information om arbets ytan Log Analytics: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: i Azure Monitor konfigurerar du loggar som är relaterade till Azure Event Hubs i aktivitets loggen och Event Hubs diagnostikinställningar för att skicka loggar till en Log Analytics arbets yta som ska frågas eller till ett lagrings konto för långsiktig lagring. Använd Log Analytics arbets yta för att skapa aviseringar för avvikande aktivitet i säkerhets loggar och händelser.

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel. 

Förstå Azures aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Så här konfigurerar du diagnostikinställningar för Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Så här varnar du på Log Analytics arbets ytans loggdata: https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center övervakning**: inte tillgänglig än

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Event Hub bearbetar inte loggning mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Event Hubs bearbetar eller skapar inte DNS-relaterade loggar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper. 

Så här hämtar du en katalog roll i Azure AD med PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: kontroll Plans åtkomst till Event Hubs styrs via Azure Active Directory (AD). Azure AD har inte begreppet standard lösen ord.

Data Plans åtkomst till Event Hubs styrs via Azure AD med hanterade identiteter eller Appregistreringar samt signaturer för delad åtkomst. Signaturer för delad åtkomst används av klienterna som ansluter till dina Event Hub och kan återskapas när som helst.

Förstå signaturer för delad åtkomst för Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration

- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version): https://docs.microsoft.com/azure/security-center/security-center-identity-access

Använda Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Microsoft Azure ger integrerad åtkomst kontroll hantering för resurser och program baserat på Azure Active Directory (AD). En viktig fördel med att använda Azure AD med Azure Event Hubs är att du inte behöver lagra dina autentiseringsuppgifter i koden längre. I stället kan du begära en OAuth 2,0-åtkomsttoken från Microsoft Identity Platform. Resurs namnet för att begära en token är https: \/ /eventhubs.Azure.net/. Azure AD autentiserar säkerhets objekt (en användare, grupp eller tjänstens huvud namn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD en åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäran till Azure Event Hubs-resurser.

Så här autentiserar du ett program med Azure AD för att få åtkomst till Event Hubs resurser: https://docs.microsoft.com/azure/event-hubs/authenticate-application

Förstå SSO med Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication (MFA) och följ Azure Security Center identitets-och åtkomst hanterings rekommendationer som hjälper dig att skydda dina Event Hub-aktiverade resurser.

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Event Hub-aktiverade resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst: https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden. Om du vill ha ytterligare loggning kan du skicka aviseringar om Azure Security Center risk identifiering till Azure Monitor och konfigurera anpassade aviseringar/meddelanden med hjälp av åtgärds grupper.

Så här distribuerar du Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå identifieringar av Azure AD-risker: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Konfigurera åtgärds grupper för anpassad avisering och avisering: https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.



Så här konfigurerar du namngivna platser i Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AD) som central autentiserings-och auktoriserings system för Azure-resurser som Event Hubs. Detta möjliggör Azure-rollbaserad åtkomst kontroll (Azure RBAC) till administrativa känsliga resurser.

 Så här skapar och konfigurerar du en Azure AD-instans: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Information om hur Azure Event Hubs integreras med Azure Active Directory (AAD) finns i bevilja åtkomst till Event Hubs resurser med Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Rotera regelbundet dina Event Hubs signaturer för delad åtkomst i ytterligare.

Förstå Azure AD-rapportering: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du granskningar av Azure Identity Access: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Förstå signaturer för delad åtkomst för Event Hubs: https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: du har åtkomst till Azure Active Directory (AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

Så här integrerar du Azure-aktivitets loggar i Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Ge åtkomst till Event Hubs resurser med Azure Active Directory: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directorys funktioner för identitets skydd och identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina Event Hubs-aktiverade resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

Så här visar du Azure AD-riskfyllda inloggningar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för tillfället. Customer Lockbox stöds ännu inte för Event Hubs.

Lista över Customer Lockbox tjänster som stöds: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för resurser som är relaterade till din Event Hubs för att hjälpa till att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Event Hubs-namnrymder bör åtskiljas av ett virtuellt nätverk med tjänstens slut punkter aktiverade och taggas på lämpligt sätt.

Du kan också skydda ditt Azure Event Hubs-namnområde med hjälp av brand väggar. Azure Event Hubs stöder IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Du kan ange brand Väggs regler med hjälp av Azure Portal, Azure Resource Manager mallar eller via Azure CLI eller Azure PowerShell.

Så här skapar du ytterligare Azure-prenumerationer: https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper: https://docs.microsoft.com/azure/governance/management-groups/create

Konfigurera IP brand Väggs regler för Azure Event Hubs-namnrymder: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Så här skapar och använder du Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du en Virtual Network: https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: när du använder virtuella datorer för att få åtkomst till dina Event Hub, använder du virtuella nätverk, tjänst slut punkter, Event Hubs brand vägg, nätverks säkerhets grupper och service märken för att minimera risken för data exfiltrering.

Microsoft hanterar den underliggande infrastrukturen för Azure Event Hubs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Konfigurera IP brand Väggs regler för Azure Event Hubs-namnrymder: https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Förstå Virtual Network tjänst slut punkter med Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrera Azure Event Hubs med en privat Azure-länk: https://docs.microsoft.com/azure/event-hubs/private-link-service

Förstå nätverks säkerhets grupper och service märken: https://docs.microsoft.com/azure/virtual-network/security-overview

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure Event Hubs framtvingar TLS-krypterad kommunikation som standard. TLS-versionerna 1,0, 1,1 och 1,2 stöds för närvarande. TLS 1,0 och 1,1 finns dock på en sökväg till föråldrad bransch, så Använd TLS 1,2 om det är möjligt.

Information om säkerhetsfunktioner i Event Hubs finns i nätverks säkerhet:  https://docs.microsoft.com/azure/event-hubs/network-security

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Event Hubs. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Azure Event Hubs stöder användning av Azure Active Directory (AD) för att auktorisera begär anden till Event Hubs resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst.

Lär dig mer om Azure RBAC och tillgängliga roller för Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Event Hubs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Event Hubs stöder möjligheten att kryptera data i vila med antingen Microsoft-hanterade nycklar eller Kundhanterade nycklar. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till de Kundhanterade nycklar som används för att kryptera Azure Event Hubs data i vila.

Så här konfigurerar du Kundhanterade nycklar för kryptering av Azure-Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Event Hubs och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure aktivitets logg händelser: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Event Hubs.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; Microsoft utför korrigerings hantering på de underliggande system som har stöd för Event Hubs.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; Benchmark är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Event Hubs.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Event Hubs.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive Azure Event Hubs namnrum) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Så här skapar du frågor med Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra Azure Event Hubs-namnrymder och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Så här skapar du ytterligare Azure-prenumerationer: https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper: https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript</div>

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Azure Event Hubs-distributioner. Använd Azure Policy alias i namn området "Microsoft. EventHub" för att skapa anpassade principer för att granska eller tillämpa konfigurationer. Du kan också använda inbyggda princip definitioner för Azure Event Hubs som:

- Diagnostikloggar i Händelsehubben måste vara aktive rad

- Event Hub bör använda en tjänst slut punkt för virtuellt nätverk

Inbyggd Azure-princip för Event Hubs namnrymd: https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Så här visar du tillgängliga Azure Policy alias: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att genomdriva säkra inställningar för dina Event Hubs-aktiverade resurser. 

Så här konfigurerar och hanterar du Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Mer information om Azure Policys effekter:  https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Event Hubs eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. EventHub" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. EventHub" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure Event Hubs-distributioner och relaterade resurser.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för virtuella Azure-datorer eller webb program som körs på Azure App Service används för att få åtkomst till dina Event Hub, använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och säkra hantering av signaturer för delad åtkomst för dina Azure Event Hubs-distributioner. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Konfigurera Kundhanterade nycklar för Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Så här integrerar du med Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: för virtuella Azure-datorer eller webb program som körs på Azure App Service används för att komma åt dina Event Hub, använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure-Event Hubs. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Azure Key Vault utan autentiseringsuppgifter i din kod.

Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser: https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Konfigurera Kundhanterade nycklar för Event Hubs: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Så här konfigurerar du hanterade identiteter: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure Managed Identities: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

Konfigurera inloggnings skannern: https://secdevtools.azurewebsites.net/helpcredscan.html

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure App Service), men det körs inte på kund innehållet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: för skanning av allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure cache för Redis), men det körs inte på kund innehåll.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Konfigurera geo-katastrof återställning för Azure Event Hubs. När hela Azure-regioner eller data Center (om inga tillgänglighets zoner används) upplever drift stopp, är det viktigt att data bearbetningen fortsätter att fungera i en annan region eller data Center. Därför är geo-haveri beredskap och geo-replikering viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveri återställning och geo-replikering på namn områdes nivå. 

Förstå geo-katastrof återställning för Azure Event Hubs: https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure Event Hubs tillhandahåller kryptering av data i vila med Azure Storage Service Encryption (Azure SSE). Event Hubs förlitar sig på Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med hjälp av Microsoft-hanterade nycklar. Om du använder Azure Key Vault för att lagra Kundhanterade nycklar, se till att vanliga automatiserade säkerhets kopieringar av dina nycklar är vanliga.

Säkerställ vanliga automatiserade säkerhets kopieringar av Key Vault hemligheter med följande PowerShell-kommando: Backup-AzKeyVaultSecret

Så här konfigurerar du Kundhanterade nycklar för kryptering av Azure Event Hubs-data i vila: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Säkerhetskopiera Key Vault hemligheter: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade nycklar som hanteras av kunden.

 

Så här återställer du Key Vault-nycklar i Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Azure Event Hubs kräver att Kundhanterade nycklar har mjuk borttagning och inte har kon figurer ATS.

Konfigurera mjuk borttagning för Azure Storage konto som används för att samla in Event Hubs data. Observera att den här funktionen inte stöds för Azure Data Lake Storage gen 2 ännu.

Så här aktiverar du mjuk borttagning i Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Konfigurera ett nyckel valv med nycklar: https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Mjuk borttagning för Azure Storage blobbar: https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

Konfigurera automatisering av arbets flöden i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta. 

Så här ställer du in Azure Security Center säkerhets kontakt: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

Så här konfigurerar du kontinuerlig export: https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 .
Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud Infrastructure, tjänster och program, här: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)