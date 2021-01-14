---
title: Azures säkerhets bas linje för Container Instances
description: Azures säkerhets bas linje för Container Instances
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0f0c771b11dfc029f18681e7a4c11b8ee271252c
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197701"
---
# <a name="azure-security-baseline-for-container-instances"></a>Azures säkerhets bas linje för Container Instances

Azures säkerhets bas linje för Container Instances innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Azure Virtual Network ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Integrera dina behållar grupper i Azure Container Instances med ett virtuellt Azure-nätverk. 

* [Scenarier och resurser för virtuella nätverk – Azure Container Instances](./container-instances-virtual-network-concepts.md)

* [Distribuera containerinstanser i ett virtuellt Azure-nätverk](./container-instances-vnet.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för att skydda dina nätverks resurser i Azure. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Skydda dina nätverks resurser](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Distribuera Azure Web Application FIREWALL (WAF) framför kritiska webb program som finns i Azure Container instances för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

* [Så här distribuerar du Azure-WAF](../web-application-firewall/ag/create-waf-policy-ag.md)



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS standard skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser. Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik. Använd Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period. Använd Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information. 

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Förstå Azure Security Center integrerad Hot information](../security-center/azure-defender.md)

* [Förstå Azure Security Center anpassad nätverks härdning](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center just-in-Time-nätverket Access Control](../security-center/security-center-just-in-time.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: om du använder nätverks säkerhets grupper (NSG: er) med den virtuella nätverks implementeringen aktiverar du NSG flödes loggar för den NSG som är kopplad till under nätet delegerad till Azure Container instances. Registrera NSG flödes loggar i ett Azure Storage-konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamlingen av Azure Network Watcher-paket.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll. Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Konfigurera aviseringar med Azure-brandväggen](../firewall/threat-intel.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md) 



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat.

* [Så här distribuerar du Application Gateway](../application-gateway/quick-create-portal.md)

* [Så här konfigurerar du Application Gateway att använda HTTPS](../application-gateway/create-ssl-portal.md) 

* [Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway](../application-gateway/overview.md)

* [Exponera en statisk IP-adress för en behållar grupp](./container-instances-application-gateway.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md)



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. 

Du kan också använda program säkerhets grupper för att förenkla komplex säkerhets konfiguration. Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper. 

* [Förstå och använda service märken](../virtual-network/service-tags-overview.md) 

* [Förstå och använda program säkerhets grupper](../virtual-network/network-security-groups-overview.md#application-security-groups)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resources Manager-mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition. Du kan använda skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering. 

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

* [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för NSG: er och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda definitioner av Azure-principer som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina behållar instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för dina beräknings resurser. Du kan till exempel köra kommandot tidssynkronisering i en behållare som körs.

* [Så här konfigurerar du tidssynkronisering för Azure Compute-resurser](../virtual-machines/windows/time-sync.md)

* [Köra ett kommando i en Azure Container instance som körs](./container-instances-exec.md)



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av en Azure Container grupp. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

* [Behållar grupp och instans loggning med Azure Monitor loggar](./container-instances-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure Monitor samlar in resurs loggar (tidigare kallade diagnostikloggar) för användar drivna händelser i registret. Azure Container Instances innehåller inbyggt stöd för att skicka behållar grupp loggar och händelse data och behållar loggar till Azure Monitor loggar.

* [Behållar grupp och instans loggning med Azure Monitor loggar](../container-registry/container-registry-diagnostics-audit-logs.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: inte tillämpligt. Den här rikt linjen är avsedd för IaaS beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

* [Ange logg lagrings parametrar för Log Analytics arbets ytor](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. 

* [Behållar grupp och instans loggning med Azure Monitor loggar](./container-instances-log-analytics.md)

* [Förstå Log Analytics arbets yta](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: Använd Log Analytics arbets yta för att övervaka och Avisera om avvikande aktivitet i säkerhets loggar och händelser. 

* [Behållar grupp och instans loggning med Azure Monitor loggar](./container-instances-log-analytics.md)

* [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Ange en egen lösning för program mot skadlig kod och händelse insamling om det behövs för att kunna köras i en behållare. 


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: Ange din egen lösning om det behövs för att fråga DNS-loggar i en behållare.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: om det behövs kan du konfigurera konsol loggning i en behållare som kör behållar instansen.

* [Köra ett kommando i en Azure Container instance som körs](./container-instances-exec.md)



**Azure Security Center övervakning**: inte tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Om du använder ett Azure Container Registry med Azure Container Instances, för varje Azure Container Registry, spåra om det inbyggda administratörs kontot är aktiverat eller inaktiverat. Inaktivera kontot när det inte används.

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Container Registry administratörs konto](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord kräver att ett lösen ord skapas med krav på komplexitet och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

Om du använder ett Azure Container Registry med Azure Container Instances, om standard administratörs kontot för ett Azure Container Registry är aktiverat, skapas komplexa lösen ord automatiskt och bör roteras. Inaktivera kontot när det inte används.

* [Azure Container Registry administratörs konto](../container-registry/container-registry-authentication.md#admin-account)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Om du använder ett Azure Container Registry med Azure Container Instances skapar du procedurer för att aktivera det inbyggda administratörs kontot för ett behållar register. Inaktivera kontot när det inte används.

* [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md)

* [Azure Container Registry administratörs konto](../container-registry/container-registry-authentication.md#admin-account)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: där det är möjligt använder Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ rekommendationerna för identitets-och åtkomst hantering för Azure Security Center.

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

* [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

* [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

* [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

* [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla/Monitoring-verktyg för säkerhets information och händelse hantering (Siem).

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter.

* [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för närvarande. Customer Lockbox stöds för närvarande inte för Azure Container Instances.

* [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd resurs taggar för att spåra Azure Container instances som lagrar eller bearbetar känslig information. 

Tagg-och versions behållar avbildningar för att hjälpa till att spåra bilder som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Rekommendationer för taggning och versions behållar avbildningar](../container-registry/container-registry-image-tag-version.md)


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av VNet/undernät, taggas på lämpligt sätt och skyddas av en NSG eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade.

* [Köra ett kommando i en Azure Container instance som körs](./container-instances-exec.md)

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md) 
* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Så här konfigurerar du aviseringen eller aviseringen och nekar med Azure-brandväggen](../firewall/threat-intel.md)


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Distribuera ett automatiserat verktyg på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar vid avisering av information säkerhets tekniker. Övervaka och blockera obehörig informations överföring från Azure-filresurser och andra volymer monterade till container instances.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md) 

* [Montera en Azure-filresurs i Azure Container Instances](./container-instances-volume-azure-files.md)


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: se till att alla klienter som ansluter till dina Azure Container Groups kan förhandla TLS 1,2 eller senare. Microsoft Azure resurser förhandlar TLS 1,2 som standard.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

* [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är inte tillgängliga för Azure Container instances. Tagga behållar grupper som kan bearbeta känslig information som sådan och implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Container instances data och resurser. 

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: som standard krypteras alla distributions data i Azure Container instances i vila med hjälp av Microsoft-hanterade nycklar. Du kan också hantera kryptering med din egen nyckel (kundhanterad nyckel).

* [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

* [Kryptera distributions data med Azure Container Instances](./container-instances-encrypt-data.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i behållar grupperna och behållar instanser. 

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/platform/alerts-activity-log.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: implementera lösningar för att genomsöka behållar avbildningar i ett privat register och identifiera potentiella sårbarheter. Följ rekommendationerna från Azure Security Center om att utföra sårbarhets bedömningar på behållar avbildningar som lagras i Azure Container Registry. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra problem utvärdering av avbildningar.

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integration med Security Center](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Microsoft utför korrigerings hantering på de underliggande system som stöder körning av behållare.

Använd en anpassad eller tredje parts lösning för att korrigera behållar avbildningar. Om du lagrar behållar avbildningar i Azure Container Registry kör Azure Container Registry aktiviteter för att automatisera uppdateringar av program avbildningar i ett behållar register baserat på säkerhets korrigeringar eller andra uppdateringar i Base OS-avbildningar.

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)

* [Om bas avbildnings uppdateringar för Azure Container Registry uppgifter](../container-registry/container-registry-tasks-base-images.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: Använd en anpassad eller tredje parts lösning för att korrigera behållar avbildningar. Om du lagrar behållar avbildningar i Azure Container Registry kör Azure Container Registry aktiviteter för att automatisera uppdateringar av program avbildningar i ett behållar register baserat på säkerhets korrigeringar eller andra uppdateringar i Base OS-avbildningar.

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)

* [Om bas avbildnings uppdateringar för ACR-uppgifter](../container-registry/container-registry-tasks-base-images.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: exportera bild skannings resultat med jämna mellanrum och jämför resultaten för att verifiera att sårbarheter har åtgärd ATS. Om du lagrar behållar avbildningar i Azure Container Registry integrerar du registret med Azure Security Center för att möjliggöra regelbunden genomsökning av behållar avbildningar för sårbarheter. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna avbildnings sårbarhets genomsökningar.

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integration med Security Center](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: om du lagrar behållar avbildningar i Azure Container Registry integrerar du registret med Azure Security Center för att möjliggöra regelbunden genomsökning av behållar avbildningar för sårbarheter och klassificera risker. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna avbildnings sårbarheter och risk klassificering.

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integration med Security Center](../security-center/defender-for-container-registries-introduction.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för att Azure Container instances och relaterade resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: du måste skapa en inventering av godkända Azure-resurser enligt organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

* [Granska kompatibilitet för Azure Container register med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: implementera en egen lösning eller en lösning från tredje part för inventering av program vara för godkända program i behållare. 

Implementera lösningar för att skanna behållar avbildningar i ett privat register och identifiera potentiella sårbarheter. Följ rekommendationerna från Azure Security Center för att utföra sårbarhets bedömningar på behållar avbildningar som lagras i Azure Container Registry. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra problem utvärdering av avbildningar.

Övervaka Azure Container Instances loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

* [Behållar grupp och instans loggning med Azure Monitor loggar](./container-instances-log-analytics.md)

* [Förstå Log Analytics arbets yta](../azure-monitor/log-query/log-analytics-tutorial.md)

* [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

* [Säkerhets överväganden för Azure Container Instances](./container-instances-image-security.md)
* [Azure Container Registry integration med Security Center](../security-center/defender-for-container-registries-introduction.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser. Du kan implementera din egen lösning för att ta bort obehöriga Azure-resurser och program.

* [En introduktion till Azure Automation](../automation/automation-intro.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: tagg-och versions behållar avbildningar, som hjälper dig att spåra bilder som kör godkända program.
* [Rekommendationer för taggning och versions behållar avbildningar](../container-registry/container-registry-image-tag-version.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att begränsa vilka tjänster du kan etablera i din miljö.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: tagg-och versions behållar avbildningar, som hjälper dig att spåra bilder som kör godkända program.
* [Rekommendationer för taggning och versions behållar avbildningar](../container-registry/container-registry-image-tag-version.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6,11: <div>Begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript<br></div>

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". 

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: alla användare med åtkomst till Azure Container instances kan köra skript i behållare.

Hantera och granska åtkomst till Azure Container Instances resurser genom att använda olika Azure-prenumerationer eller hanterings grupper eller isolera resurser med hjälp av virtuella nätverk och NSG: er eller Azure-brandväggen.

* [Köra ett kommando i en Azure Container instance som körs](./container-instances-exec.md)

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program vara som krävs för affärs åtgärder, men som kan ådra sig högre risk för organisationen, bör isoleras inom sitt eget virtuella nätverk och tillräckligt säkra med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

* [Distribuera i ett virtuellt nätverk – Azure Container Instances](./container-instances-vnet.md) 

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: underhåll en godkänd behållare grupps konfiguration med hjälp av en Azure Resource Manager mall eller exportera till en yaml-fil. Använd Azure Policy för att underhålla säkerhetskonfigurationer för relaterade Azure-resurser.

* [Behållargrupper i Azure Container Instances](container-instances-container-groups.md#deployment)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Använd en anpassad eller tredje parts lösning för att korrigera behållar avbildningar. Om du lagrar behållar avbildningar i Azure Container Registry kör Azure Container Registry aktiviteter för att automatisera uppdateringar av program avbildningar i ett behållar register baserat på säkerhets korrigeringar eller andra uppdateringar i Base OS-avbildningar. 

* [Om bas avbildnings uppdateringar för Azure Container Registry uppgifter](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: implementera lösningar för att genomsöka behållar avbildningar i ett privat register och identifiera potentiella sårbarheter i OS-konfigurationer. Följ rekommendationerna från Azure Security Center för att utföra sårbarhets bedömningar på behållar avbildningar som lagras i Azure Container Registry. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra problem utvärdering av avbildningar.

Använd en anpassad eller tredje parts lösning för att korrigera behållar avbildningar. Om du lagrar behållar avbildningar i Azure Container Registry kör Azure Container Registry aktiviteter för att automatisera uppdateringar av program avbildningar i ett behållar register baserat på säkerhets korrigeringar eller andra uppdateringar i Base OS-avbildningar. 

* [Säkerhets rekommendationer för övervakning och genomsökning av behållare för Azure Container Instances](./container-instances-image-security.md)

* [Azure Container Registry integration med Security Center](../security-center/defender-for-container-registries-introduction.md)
* [Om bas avbildnings uppdateringar för Azure Container Registry uppgifter](../container-registry/container-registry-tasks-base-images.md)



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: lagra och hantera arm-mallar, yaml-filer och anpassade Azure policy-definitioner på ett säkert sätt i käll kontroll.

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: lagra behållar avbildningar i Azure Container Registry och utnyttja Azure RBAC för att se till att endast behöriga användare får åtkomst till avbildningarna.

* [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Lär dig mer om Azure RBAC för Container Registry](../container-registry/container-registry-roles.md)

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: använda Azure policy för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för IaaS beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser.

Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

* [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

* [Granska kompatibilitet för Azure Container register med hjälp av Azure Policy](../container-registry/container-registry-azure-policy.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: om du använder Azure Container Registry för att lagra behållar avbildningar använder du Azure Security Center för att utföra bas linje genomsökningar för operativ system och Docker-inställningar för behållare.

* [Förstå rekommendationer för Azure Security Center-container](../security-center/container-security.md)


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

* [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

* [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

* [Använda hanterade identiteter med Azure Container Instances](./container-instances-managed-identity.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

* [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Använda hanterade identiteter med Azure Container Instances](./container-instances-managed-identity.md)



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för IaaS beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Container instances), men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv. 


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Container Instances), men det körs inte på kund innehållet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Aktivera Azure Backup och konfigurera säkerhets kopierings källan (till exempel en fil resurs som är monterad till behållar grupper), samt önskad frekvens och kvarhållningsperiod. 

* [Så här aktiverar du Azure Backup](../backup/index.yml)

* [Montera en Azure-filresurs i Azure Container Instances](./container-instances-volume-azure-files.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: säkerhetskopiera Kundhanterade nycklar i Azure Key Vault med hjälp av Azures kommando rads verktyg eller SDK: er.

Du kan också säkerhetskopiera behållar avbildningar genom att importera från ett register till ett annat.
* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Importera behållar avbildningar till ett behållar register](../container-registry/container-registry-import-images.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade Kundhanterade nycklar i Azure Key Vault med hjälp av kommando rads verktyg eller SDK: er för Azure.

* [Så här återställer du Azure Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Kryptera distributions data – Azure Container Instances](./container-instances-encrypt-data.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: du kan aktivera Soft-Delete i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

* [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NISTs guide till hantering av säkerhetsincidenter](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (till exempel. produktion, icke-produktion) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

* [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviserings indikatorn.

* [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

* [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

* [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

* [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)