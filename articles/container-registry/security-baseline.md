---
title: Azures säkerhets bas linje för Azure Container Registry
description: Azures säkerhets bas linje för Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ccb287a63cf372ff78c750a4690188de7ccc59e6
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807945"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azures säkerhets bas linje för Azure Container Registry

Azures säkerhets bas linje för Azure Container Registry innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Azure Virtual Network ger säker, privat nätverk för dina Azure-resurser och lokala resurser. Genom att begränsa åtkomsten till ditt privata Azure Container Registry från ett virtuellt Azure-nätverk ser du till att endast resurser i det virtuella nätverket har åtkomst till registret. Du kan också konfigurera brand Väggs regler för att tillåta att registret endast kommer åt från vissa IP-adresser för olika scenarier med olika platser. Från bakom en brand vägg konfigurerar du brand Väggs åtkomst regler och service märken för att få åtkomst till ditt behållar register.

Begränsa åtkomsten till ett Azure Container Registry med hjälp av ett virtuellt Azure-nätverk eller brand Väggs regler: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: Använd Azure Security Center och åtgärda rekommendationer för nätverks skydd för att skydda dina nätverks resurser i Azure. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Skydda dina nätverks resurser: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: inte tillämpligt. Benchmark är avsett för Azure App Service eller beräknings resurser som är värdar för webb program.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera DDoS standard skydd i dina virtuella nätverk för skydd mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.  Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för "varning och neka" för skadlig nätverks trafik.

Du kan använda Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period. Använd också Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

Så här konfigurerar du DDoS-skydd:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Så här distribuerar du Azure-brand väggen: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Förstå Azure Security Center integrerad Hot information: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Förstå Azure Security Center anpassad nätverks härdning: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center just-in-Time-nätverk Access Control: https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: Aktivera flödes loggar för nätverks säkerhets grupper (NSG) för NSG som är kopplade till det undernät som används för att skydda ditt Azure Container Registry. Du kan registrera NSG flödes loggar i ett Azure Storage-konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamlingen av Azure Network Watcher-paket.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar du Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Välj ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med funktioner för nytto Last kontroll. Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera den brand Väggs lösning som du väljer för var och en av organisationens nätverks gränser för att upptäcka och/eller neka skadlig trafik.

Azure Marketplace:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Så här distribuerar du Azure-brand väggen: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera aviseringar med Azure-brand väggen: https://docs.microsoft.com/azure/firewall/threat-intel


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: inte tillämpligt. Benchmark är avsett för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till ditt behållar register använder du tjänst taggar för virtuella nätverk för Azure Container Registrys tjänsten för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet "AzureContainerRegistry" i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Tillåt åtkomst per service tag-tagg: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer för nätverks resurser som är associerade med dina Azure Container register med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. ContainerRegistry" och "Microsoft. Network" om du vill skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina behållar register. 

Du kan använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure RBAC-kontroller och principer, i en enda skiss definition. Använd enkelt skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här skapar du en Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: kunden kan använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, Azure RBAC-kontroller och principer, i en enda skiss definition. Använd enkelt skissen för nya prenumerationer och finjustera kontroll och hantering genom versions hantering.

Så här skapar du en Azure Blueprint: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina behållar register. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

Så här visar och hämtar du Azure aktivitets logg händelser:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Så här skapar du aviseringar i Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar tids källor för Azure-resurser, men du har möjlighet att hantera tidssynkroniserings inställningarna för dina beräknings resurser.

Så här konfigurerar du tidssynkronisering för Azure Compute-resurser: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av ett Azure Container Registry. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Azure Container Registry loggar för diagnostisk utvärdering och granskning:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure Monitor samlar in resurs loggar (tidigare kallade diagnostikloggar) för användar drivna händelser i registret. Samla in och Använd dessa data för att granska händelser för klientautentisering och tillhandahålla en fullständig aktivitets spårning på register artefakter som pull-och push-händelser så att du kan diagnostisera säkerhets problem med ditt register.

Azure Container Registry loggar för diagnostisk utvärdering och granskning: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: inte tillämpligt. Benchmark är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

Så här ställer du in logg lagrings parametrar för Log Analytics arbets ytor: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka Azure Container Registry loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

Azure Container Registry loggar för diagnostisk utvärdering och granskning:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Förstå Log Analytics arbets yta: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till ditt Azure Container Registry.

Azure Container Registry loggar för diagnostisk utvärdering och granskning: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Så här aviserar du om Log Analytics-loggdata:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: inte tillämpligt. Azure Container Registry bearbetar eller skapar inte relaterade loggar mot skadlig kod.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: inte tillämpligt. Azure Container Registry är en slut punkt och initierar inte kommunikation, och tjänsten frågar inte DNS.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: inte tillämpligt. Benchmark är avsett för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Spåra om det inbyggda administratörs kontot är aktiverat eller inaktiverat för varje Azure Container Registry. Inaktivera kontot när det inte används.

Så här hämtar du en katalog roll i Azure AD med PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry administratörs konto:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord kräver att ett lösen ord skapas med krav på komplexitet och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

Om standard administratörs kontot för ett Azure Container Registry är aktiverat skapas komplexa lösen ord automatiskt och bör roteras. Inaktivera kontot när det inte används.

Azure Container Registry administratörs konto: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

Skapa också procedurer för att aktivera det inbyggda administratörs kontot för ett behållar register. Inaktivera kontot när det inte används.

Förstå Azure Security Center identitet och åtkomst:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry administratörs konto:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: där det är möjligt använder Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering.

För enskild åtkomst till behållar registret använder du enskilda inloggningar som är integrerade med Azure Active Directory.

Förstå SSO med Azure AD:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Individuell inloggning till ett behållar register:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ rekommendationerna för identitets-och åtkomst hantering för Azure Security Center.

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Övervaka identitet och åtkomst i Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

Lär dig mer om arbets stationer med privilegie rad åtkomst:  https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Så här aktiverar du MFA i Azure: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center övervakning**: ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Så här övervakar du användarnas identitets-och åtkomst aktiviteter i Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

Så här konfigurerar du namngivna platser i Azure:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Så här skapar och konfigurerar du en Azure AD-instans: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Förstå Azure AD-rapportering:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Så här använder du granskningar av Azure Identity Access:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: du har åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla/Monitoring-verktyg för säkerhets information och händelse hantering (Siem).

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

Så här integrerar du Azure-aktivitets loggar i Azure Monitor:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. 

Så här visar du Azure AD-riskfyllda inloggningar: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Så här konfigurerar och aktiverar du principer för identitets skydds risker: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig; Customer Lockbox stöds för närvarande inte för Azure Container Registry.

Lista över Customer Lockbox tjänster som stöds: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd resurs taggar för att spåra Azure Container register som lagrar eller bearbetar känslig information.

Tagg-och versions behållar avbildningar eller andra artefakter i ett register, och lås bilder eller databaser, för att spåra bilder som lagrar eller bearbetar känslig information.

Skapa och använda Taggar:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Rekommendationer för taggning och versions behållar avbildningar:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Lås en behållar avbildning i ett Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata behållar register, prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade.

Resurser bör åtskiljas av ett virtuellt nätverk eller undernät, taggas på lämpligt sätt och skyddas av en nätverks säkerhets grupp (NSG) eller Azure-brandvägg.

Så här skapar du ytterligare Azure-prenumerationer:  https://docs.microsoft.com/azure/billing/billing-create-subscription

Så här skapar du hanterings grupper:  https://docs.microsoft.com/azure/governance/management-groups/create

Skapa och använda Taggar: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Begränsa åtkomsten till ett Azure Container Registry med hjälp av ett virtuellt Azure-nätverk eller brand Väggs regler: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Så här skapar du en NSG med en säkerhets konfiguration: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Så här distribuerar du Azure-brand väggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Så här konfigurerar du aviseringen eller aviseringen och nekar med Azure-brand väggen:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Distribuera ett automatiserat verktyg på nätverks-perimeter som övervakar för obehörig överföring av känslig information och blockerar sådana överföringar vid avisering av information säkerhets tekniker.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: se till att alla klienter som ansluter till Azure Container Registry kan förhandla TLS 1,2 eller senare. Microsoft Azure resurser förhandlar TLS 1,2 som standard.

Följ Azure Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

Förstå kryptering i överföring med Azure:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Container Registry. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till data och resurser i ett Azure Container Registry. 

Så här konfigurerar du Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry roller och behörigheter:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: om det krävs för att använda beräknings resurser implementerar du ett verktyg från tredje part, till exempel en automatiserad värdbaserade lösning för data förlust skydd, för att genomdriva åtkomst kontroller till data även när data kopieras av ett system.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

Förstå kundens data skydd i Azure:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Använd kryptering i vila på alla Azure-resurser. Som standard krypteras alla data i ett Azure Container Registry i vila med hjälp av Microsoft-hanterade nycklar.

Förstå kryptering i vila i Azure: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Kundhanterade nycklar i Azure Container Registry:  https://aka.ms/acr/cmk



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Azure Monitor samlar in resurs loggar (tidigare kallade diagnostikloggar) för användar drivna händelser i registret. Samla in och Använd dessa data för att granska händelser för klientautentisering och tillhandahålla en fullständig aktivitets spårning på register artefakter som pull-och pull-händelser så att du kan diagnostisera drifts problem med ditt register.

Azure Container Registry loggar för diagnostisk utvärdering och granskning: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om att utföra sårbarhets bedömningar i behållar avbildningar. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra problem utvärdering av avbildningar.

Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry integration med Security Center (för hands version):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Microsoft utför korrigerings hantering på de underliggande system som har stöd för Azure Container Registry.

Automatisera uppdateringar av behållar avbildningar när uppdateringar av bas avbildningar från operativ systemet och andra korrigeringar upptäcks.

Om bas avbildnings uppdateringar för Azure Container Registry uppgifter:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk hanterings lösning för program uppdatering från tredje part

**Vägledning**: du kan använda en lösning från tredje part för att korrigera program avbildningar.  Du kan också köra Azure Container Registry uppgifter för att automatisera uppdateringar av program avbildningar i ett behållar register baserat på säkerhets korrigeringar eller andra uppdateringar i bas avbildningar.

Om bas avbildnings uppdateringar för ACR-aktiviteter:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: integrera Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden genomsökning av behållar avbildningar för sårbarheter. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna avbildnings sårbarhets genomsökningar.

Azure Container Registry integration med Security Center (för hands version):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: integrera Azure Container Registry (ACR) med Azure Security Center för att möjliggöra regelbunden genomsökning av behållar avbildningar för sårbarheter och för att klassificera risker. Du kan också distribuera lösningar från tredje part från Azure Marketplace för att utföra regelbundna avbildnings sårbarheter och risk klassificering.

Azure Container Registry integration med Security Center (för hands version):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center övervakning**: ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er).  Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

Så här skapar du frågor med Azure Resource Graph:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Så här visar du dina Azure-prenumerationer:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Förstå Azure RBAC:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Azure Container Registry hanterar metadata, inklusive Taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

Om register, databaser och avbildningar: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Rekommendationer för taggning och versions behållar avbildningar: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Azure Container Registry hanterar metadata, inklusive Taggar och manifest för avbildningar i ett register. Följ rekommenderade metoder för att tagga artefakter.

Om register, databaser och avbildningar: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Rekommendationer för taggning och versions behållar avbildningar:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll en inventering av godkända Azure-resurser och program varu titlar

**Vägledning**: du måste skapa en inventering av godkända Azure-resurser enligt organisationens behov.  

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön är godkända.

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här konfigurerar och hanterar du Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här skapar du frågor med Azure Graph:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: analysera och övervaka Azure Container Registry loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

Azure Container Registry loggar för diagnostisk utvärdering och granskning:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Förstå Log Analytics arbets yta:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Så här utför du anpassade frågor i Azure Monitor:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: Azure Automation ger fullständig kontroll under distribution, åtgärder och inaktive ring av arbets belastningar och resurser.  Du kan implementera din egen lösning för att ta bort obehöriga Azure-resurser. En introduktion till Azure Automation:  https://docs.microsoft.com/azure/automation/automation-intro


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: inte tillämpligt. Benchmark är utformat för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: utnyttja Azure policy för att begränsa vilka tjänster du kan etablera i din miljö.

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Så här nekar du en speciell resurs typ med Azure Policy:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: inte tillämpligt. Benchmark är utformat för beräknings resurser.



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med AzureResources Manager via skript

**Vägledning**: Använd konfigurationer för operativ system eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: Använd operativ systemets speciella konfigurationer eller resurser från tredje part för att begränsa användarnas möjlighet att köra skript i Azure Compute-resurser.

Till exempel hur du styr körning av PowerShell-skript i Windows-miljöer:  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: program vara som krävs för affärs åtgärder, men som kan ådra sig högre risk för organisationen, bör isoleras inom den egna virtuella datorn och/eller det virtuella nätverket och tillräckligt säkert med antingen en Azure-brandvägg eller en nätverks säkerhets grupp.

Så här skapar du ett virtuellt nätverk:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhets konfiguration:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: använda Azure Policy eller Azure Security Center för att underhålla säkerhetskonfigurationer för alla Azure-resurser.

Så här konfigurerar och hanterar du Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: Använd Azure Security Center rekommendation "åtgärda sårbarheter i säkerhetskonfigurationer på din Virtual Machines" för att underhålla säkerhetskonfigurationer på alla beräknings resurser.

Så här övervakar du Azure Security Center rekommendationer:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Så här åtgärdar du Azure Security Center rekommendationer:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här konfigurerar och hanterar du Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Förstå Azure Policys effekter:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: inte tillämpligt. Benchmark är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

Så här lagrar du kod i Azure DevOps:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentation om Azure databaser:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: inte tillämpligt. Benchmark gäller för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: använda Azure policy för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Så här konfigurerar och hanterar du Azure Policy:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: inte tillämpligt. Benchmark gäller för beräknings resurser.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: Använd Azure Security Center för att utföra bas linjes ökningar för dina Azure-resurser.

Använd Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Så här åtgärdar du rekommendationer i Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Granska kompatibilitet för Azure Container register med Azure Policy:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: inte tillämpligt. Benchmark gäller för beräknings resurser.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hemlig hantering för dina moln program.

Så här integrerar du med Azure Managed Identities:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Så här skapar du en Key Vault: https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Så här autentiserar du till Key Vault: https://docs.microsoft.com/azure/key-vault/general/authentication

Så här tilldelar du en Key Vault åtkomst princip: https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

Använd en Azure-hanterad identitet i Azure Container Registry uppgifter:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

Så här konfigurerar du hanterade identiteter:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Använd en hanterad identitet för att autentisera till ett Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

Konfigurera inloggnings skannern:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: Använd Microsoft Antimalware för Azure Cloud Services och Virtual Machines för att kontinuerligt övervaka och försvara dina resurser. För Linux använder du en lösning från tredje part mot skadlig kod.

Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Container Registry), men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: inte tillämpligt. Benchmark är avsett för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: data i ditt Microsoft Azure Container Registry replikeras alltid automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Container Registry kopierar dina data så att de skyddas från planerade och oplanerade händelser

Alternativt geo-replikera ett behållar register för att underhålla register repliker i flera Azure-regioner. 

Geo-replikering i Azure Container Registry:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: om du vill kan du säkerhetskopiera behållar avbildningar genom att importera från ett register till ett annat.

Säkerhetskopiera Kundhanterade nycklar i Azure Key Vault med hjälp av kommando rads verktyg eller SDK: er för Azure.

Importera behållar avbildningar till ett behållar register:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Så här säkerhetskopierar du nyckel valv nycklar i Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: testa återställning av säkerhetskopierade Kundhanterade nycklar i Azure Key Vault med hjälp av kommando rads verktyg eller SDK: er för Azure.

Så här återställer du Azure Key Vault nycklar i Azure:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: du kan aktivera Soft-Delete i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

Så här aktiverar du Soft-Delete i Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

Konfigurera automatisering av arbets flöden i Azure Security Center:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Vägledning om hur du skapar en egen svars process för säkerhets incident:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center: s uppbyggnad av en incident:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer:  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Azure Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner:  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

Så här ställer du in Azure Security Center säkerhets kontakt:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

Så här konfigurerar du kontinuerlig export:  https://docs.microsoft.com/azure/security-center/continuous-export

Strömma aviseringar till Azure Sentinel:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

Konfigurera automatisering av arbets flöde och Logic Apps:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att alla kritiska säkerhets brister upptäcks inom 60 dagar

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer:  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program, här:  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../security/benchmarks/security-baselines-overview.md)
