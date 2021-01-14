---
title: Azures säkerhets bas linje för Azure cache för Redis
description: Azures säkerhets bas linje för Azure cache för Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 10277f18dd2a7ddaa436794839669e34f0ee95c3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98195917"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azures säkerhets bas linje för Azure cache för Redis

Azures säkerhets bas linje för Azure cache för Redis innehåller rekommendationer som hjälper dig att förbättra distributionens säkerhets position.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Distribuera Azure-cachen för Redis-instansen i ett virtuellt nätverk (VNet). Ett VNet är ett privat nätverk i molnet. När en Azure-cache för Redis-instans har kon figurer ATS med ett VNet, är den inte offentligt adresserad och kan endast nås från virtuella datorer och program i VNet.

Du kan också ange brand Väggs regler med start-och slut-IP-adressintervall. När brand Väggs regler har kon figurer ATS kan endast klient anslutningar från de angivna IP-adressintervall ansluta till cacheminnet.

Så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Så här konfigurerar du Azure cache för brand Väggs regler för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: när Virtual Machines distribueras i samma virtuella nätverk som din Azure cache för Redis-instans kan du använda nätverks säkerhets grupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar och använder du Trafikanalys:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Azure Virtual Network (VNet)-distribution ger förbättrad säkerhet och isolering för Azure-cache för Redis, samt undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst. När det distribueras i ett virtuellt nätverk, är Azure cache för Redis inte offentligt adresserat och kan endast nås från virtuella datorer och program i VNet.

Aktivera DDoS Protection standard på virtuella nätverk som är kopplade till Azure-cachen för Redis-instanser för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

Så här konfigurerar du Virtual Network stöd för en Premium Azure-cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Hantera Azure DDoS Protection standard med hjälp av Azure Portal:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: när virtuella datorer distribueras i samma virtuella nätverk som din Azure cache för Redis-instans kan du använda nätverks säkerhets grupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar och använder du Trafikanalys:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: när du använder Azure cache för Redis med dina webb program som körs på Azure App Service-eller beräknings instanser, distribuerar du alla resurser i en Azure-Virtual Network (VNet) och skyddar med en Azure Web Application-brandvägg (WAF) på webb Application Gateway. Konfigurera WAF att köras i "skydds läge". I förebyggande läge blockeras intrång och attacker som reglerna identifierar. Angriparen får ett undantag för "403 obehörig åtkomst" och anslutningen stängs. I skydds läget registreras sådana attacker i WAF-loggarna.

Alternativt kan du välja ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med nytto last-och/eller avvikelse identifierings funktioner.

Förstå Azure WAF-funktioner:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Så här distribuerar du Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG) eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Du kan också använda program säkerhets grupper (grupperna) för att förenkla komplex säkerhets konfiguration. Med program säkerhets grupper kan du konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverks säkerhets principer baserat på dessa grupper.

Taggar för virtuella nätverks tjänster:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Program säkerhets grupper:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till Azure cache för Redis-instanser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. cache" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för Azure cache för Redis-instanser. Du kan också använda inbyggda princip definitioner som:

Endast säkra anslutningar till din Redis Cache ska vara aktiverade

DDoS Protection standard ska vara aktive rad

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager ARM-mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är kopplade till Azure cache för Redis-distribution för att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure cache för Redis-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft underhåller den tids källa som används för Azure-resurser, till exempel Azure cache för Redis för tidsstämplar i loggarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure-cachen för Redis-instanser på kontroll planet nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för Azure cache för Redis-instanser.

Så här aktiverar du diagnostikinställningar för Azure aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på Azure-cachen för Redis-instanser på kontroll planet nivå. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för Azure cache för Redis-instanser.

Även om mått är tillgängliga genom att aktivera diagnostikinställningar, är gransknings loggning på data planet ännu inte tillgängligt för Azure cache för Redis.

Så här aktiverar du diagnostikinställningar för Azure aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till Azure cache för Redis-instanser enligt organisationens regler för efterlevnad.

Observera att gransknings loggning på data planet ännu inte är tillgängligt för Azure cache för Redis.

Så här ställer du in parametrar för logg kvarhållning:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på aktivitets logg data som kan ha samlats in för Azure cache för Redis.

Observera att gransknings loggning på data planet ännu inte är tillgängligt för Azure cache för Redis.

Så här aktiverar du diagnostikinställningar för Azure aktivitets logg: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: du kan konfigurera för att ta emot aviseringar baserat på mått och aktivitets loggar som är relaterade till Azure cache för Redis-instanser. Med Azure Monitor kan du konfigurera en avisering för att skicka ett e-postmeddelande, anropa en webhook eller anropa en Azure Logic-app.

Även om mått är tillgängliga genom att aktivera diagnostikinställningar, är gransknings loggning på data planet ännu inte tillgängligt för Azure cache för Redis.

Så här konfigurerar du aviseringar för Azure cache för Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure cache för Redis bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure cache för Redis bearbetar eller skapar inte DNS-relaterade loggar.

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

**Vägledning**: kontroll Plans åtkomst till Azure cache för Redis styrs via Azure Active Directory (AD). Azure AD har inte begreppet standard lösen ord. 

Data Plans åtkomst till Azure cache för Redis kontrol leras via åtkomst nycklar. Dessa nycklar används av klienterna som ansluter till din cache och kan återskapas när som helst.

Vi rekommenderar inte att du skapar standard lösen ord i ditt program. I stället kan du lagra dina lösen ord i Azure Key Vault och sedan använda Azure Active Directory för att hämta dem.

Återskapa Azure cache för Redis-åtkomst nycklar: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

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

**Vägledning**: Azure cache för Redis använder åtkomst nycklar för att autentisera användare och stöder inte enkel inloggning på data planet nivå. Åtkomst till kontroll planet för Azure cache för Redis är tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till ett JSON Web Token som du får från Azure Active Directory.

Förstå Azure cache för Redis REST API: https://docs.microsoft.com/rest/api/redis/

Förstå SSO med Azure AD: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (AD) Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Azure-resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

Så här distribuerar du Privileged Identity Management (PIM): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Förstå identifieringar av Azure AD-risker: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Konfigurera namngivna platser i Azure Active Directory (AD) villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Så här konfigurerar du namngivna platser i Azure: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure AD-autentisering kan inte användas för direkt åtkomst till Azure cache för Redis-dataplan, men autentiseringsuppgifter för Azure AD kan användas för administration på kontroll Plans nivån (d.v.s. Azure Portal) för att kontrol lera Azure cache för Redis åtkomst nycklar.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Förstå Azure AD-rapportering: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du granskningar av Azure Identity Access: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: du har åtkomst till Azure Active Directory (AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en Siem från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

Så här integrerar du Azure-aktivitets loggar i Azure Monitor: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Så här gör du Azure-kontroll på kort: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: för konto inloggning beteende avvikelse i kontroll planet använder du Azure Active Directory (AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

Så här visar du Azure AD-riskfyllda inloggningar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Publicera Azure Sentinel: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ännu inte tillgänglig; Customer Lockbox stöds ännu inte för Azure cache för Redis.

Lista över Customer Lockbox tjänster som stöds:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure cache för Redis-instanser ska avgränsas av det virtuella nätverket/under nätet och taggas på lämpligt sätt. Du kan också använda Azure-cachen för Redis-brandväggen för att definiera regler så att endast klient anslutningar från angivna IP-adressintervall kan ansluta till cacheminnet.

Så här skapar du ytterligare Azure-prenumerationer:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du Hanteringsgrupper:

https://docs.microsoft.com/azure/governance/management-groups/create

Så här distribuerar du Azure cache för Redis till ett VNet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Så här konfigurerar du Azure cache för brand Väggs regler för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: ännu inte tillgänglig; funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure cache för Redis.

Microsoft hanterar den underliggande infrastrukturen för Azure cache för Redis och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Azure cache för REDIS kräver TLS-krypterad kommunikation som standard. TLS-versionerna 1,0, 1,1 och 1,2 stöds för närvarande. TLS 1,0 och 1,1 finns dock på en sökväg till föråldrad bransch, så Använd TLS 1,2 om det är möjligt. Om klient biblioteket eller verktyget inte stöder TLS kan du göra det genom att aktivera okrypterade anslutningar via API: erna för Azure Portal eller hantering. I sådana fall där det inte går att kryptera anslutningar kan du rekommendera att placera cacheminnet och klient programmet i ett virtuellt nätverk.

Förstå kryptering i överföring för Azure cache för Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Förstå nödvändiga portar som används i scenarier med VNet-cache:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure cache för Redis. Tagga instanser som innehåller känslig information som sådan och implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till Azure-cachen för Redis kontroll plan (d.v.s. Azure Portal). 

Så här konfigurerar du Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Azure cache för Redis och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

Förstå kundens data skydd i Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure cache för Redis lagrar kund information i minnet och som är starkt skyddad av många kontroller som implementeras av Microsoft, är minnet inte krypterat som standard. Kryptera innehåll innan du lagrar i Azure cache för Redis, om det krävs av din organisation.

Om du använder Azure cache för Redis-funktionen "Redis data Persistes" skickas data till ett Azure Storage-konto som du äger och hanterar. Du kan konfigurera persistence från bladet "nytt Azure-cache för Redis" under skapandet av cacheminnet och på resurs-menyn för befintliga Premium-cacheminnen.

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering kan inte inaktive ras. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.

Så här konfigurerar du persistence i Azure cache för Redis: https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Förstå kryptering för Azure Storage-konton: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Förstå Azures data skydd för kunder: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure cache för Redis och andra kritiska eller relaterade resurser.

Så här skapar du aviseringar för Azure aktivitets logg händelser:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om hur du skyddar Azure-cachen för Redis-instanser och relaterade resurser.

Microsoft utför sårbarhets hantering på de underliggande systemen som stöder Azure cache för Redis.

Förstå Azure Security Center rekommendationer: https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande systemen som stöder Azure cache för Redis.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Förstå Azure RBAC: https://docs.microsoft.com/azure/role-based-access-control/overview

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, i förekommande fall, för att organisera och spåra Azure cache för Redis-instanser och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

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

Otillåtna resurstyper

Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

Otillåtna resurstyper

Tillåtna resurstyper

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med Azures resurs hanterare via skript

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager (arm) genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till ARM:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Vägledning**: definiera och implementera standardkonfigurationer för Azure cache för Redis-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. cache" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för Azure-cachen för Redis-instanser. Du kan också använda inbyggda princip definitioner som är relaterade till Azure cache för Redis-instanser, till exempel:

Endast säkra anslutningar till din Redis Cache ska vara aktiverade

Så här visar du tillgängliga Azure Policy alias: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner eller Azure Resource Manager mallar för Azure cache för Redis-instanser och relaterade resurser använder du Azure databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. cache" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. cache" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt genomdriva konfigurationer för Azure cache för Redis-instanser och relaterade resurser.

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för virtuella Azure-datorer eller-webb program som körs på Azure App Service används för att få åtkomst till Azure-cache för Redis-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure-cachen för Redis Secret Management. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Så här integrerar du med Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: för virtuella Azure-datorer eller-webb program som körs på Azure App Service används för att få åtkomst till Azure-cache för Redis-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure-cachen för Redis Secret Management. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hanterade identiteter kan du autentisera till alla tjänster som stöder AAD-autentisering, inklusive Azure Key Vault, utan autentiseringsuppgifter i din kod.

Så här konfigurerar du hanterade identiteter:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Så här integrerar du med Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure cache för Redis), men det körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som stöder Azure-tjänster (till exempel Azure cache för Redis), men det körs inte på kund innehåll.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Aktivera Redis persistence. Med Redis beständighet kan du spara data som lagras i Redis. Du kan också ta ögonblicks bilder och säkerhetskopiera data som du kan läsa in i händelse av maskin varu problem. Detta är en enorm fördel jämfört med Basic-eller standard-nivån där alla data lagras i minnet och det kan finnas potentiell data förlust vid ett haveri där cache-noderna är nere.

Du kan också använda Azure cache för Redis-export. Med export kan du exportera data som lagras i Azure cache för Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure-cache för Redis-instans till en annan eller till en annan redis-server. Under export processen skapas en temporär fil på den virtuella datorn som är värd för Azure cache för Redis-Server-instansen och filen laddas upp till det angivna lagrings kontot. När export åtgärden har slutförts med statusen lyckad eller misslyckad tas den temporära filen bort.

Så här aktiverar du Redis persistence:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Så här använder du Azure cache för Redis-export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Aktivera Redis persistence. Med Redis beständighet kan du spara data som lagras i Redis. Du kan också ta ögonblicks bilder och säkerhetskopiera data som du kan läsa in i händelse av maskin varu problem. Detta är en enorm fördel jämfört med Basic-eller standard-nivån där alla data lagras i minnet och det kan finnas potentiell data förlust vid ett haveri där cache-noderna är nere.

Du kan också använda Azure cache för Redis-export. Med export kan du exportera data som lagras i Azure cache för Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure-cache för Redis-instans till en annan eller till en annan redis-server. Under export processen skapas en temporär fil på den virtuella datorn som är värd för Azure cache för Redis-Server-instansen och filen laddas upp till det angivna lagrings kontot. När export åtgärden har slutförts med statusen lyckad eller misslyckad tas den temporära filen bort.

Om du använder Azure Key Vault för att lagra autentiseringsuppgifter för Azure cache för Redis-instanser, se till att vanliga automatiserade säkerhets kopieringar av nycklar används.

Så här aktiverar du Redis persistence:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Så här använder du Azure cache för Redis-export:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Så här säkerhetskopierar du Key Vault nycklar:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Använd Azure cache för Redis-import. Import kan användas för att ta Redis-kompatibla RDB-filer från en Redis-server som körs i molnet eller i miljön, inklusive Redis som körs på Linux, Windows eller någon annan moln leverantör, till exempel Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa en cache med förifyllda data. Under importen läser Azure cache för Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cachen.

Testa data återställningen regelbundet för dina Azure Key Vault hemligheter.

Så här använder du Azure cache för Redis-import:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Så här återställer du Key Vault hemligheter:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Azure cache för Redis-säkerhetskopieringar från Redis export och Redis persistence lagras i det valda Azure Storage kontot. Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES-kryptering, en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering kan inte inaktive ras. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.

Förstå kryptering för Azure Storage-konton: https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure Security Center-övervakning**: Ja

**Ansvar**: Microsoft

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

Konfigurera automatisering av arbets flöden i Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du skapar en egen svars process för säkerhets incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center: s uppbyggnad av en incident:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

Så här ställer du in Azure Security Center säkerhets kontakt:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

Så här konfigurerar du kontinuerlig export:

https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program, här: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)