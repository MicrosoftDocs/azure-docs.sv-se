---
title: Azures säkerhets bas linje för Cognitive Services
description: Azures säkerhets bas linje för Cognitive Services
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 97bcf0bedb3ed175f8fdb5d4dc361c069ae0327d
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805217"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Azures säkerhets bas linje för Cognitive Services

Azures säkerhets bas linje för Cognitive Services innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Azure Cognitive Services tillhandahåller en skiktad säkerhets modell. Med den här modellen kan du begränsa åtkomsten till dina Cognitive Services-konton till utvalda nätverk. När du har konfigurerat nätverksregler kan endast program som begär data över den angivna uppsättningen nätverk komma åt kontot. Du kan begränsa åtkomsten till dina resurser med filtrering av förfrågningar, så att endast begär Anden som kommer från angivna IP-adresser, IP-adressintervall eller från en lista över undernät i virtuella Azure-nätverk.

Stöd för virtuella nätverk och tjänst slut punkter för Cognitive Services är begränsat till en viss uppsättning regioner.

* [Så här konfigurerar du virtuella Azure Cognitive Services-nätverk](./cognitive-services-virtual-networks.md?tabs=portal)

* [Översikt över virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: när Virtual Machines distribueras i samma virtuella nätverk som din Azure Cognitive Services-behållare kan du använda nätverks säkerhets grupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: om du använder Cognitive Services i en behållare kan du utöka behållar distributionen med en frontend-lösning för webb program som filtrerar skadlig trafik och stöder kryptering från slut punkt till slut punkt, så att containerns slut punkt hålls privat och säker.

Tänk på att Cognitive Services behållare krävs för att skicka in avläsnings information i fakturerings syfte. Det enda undantaget är offline-behållare när de följer en annan fakturerings metod. Det gick inte att tillåta en lista över olika nätverks kanaler som Cognitive Services behållare förlitar sig på förhindrar att behållaren fungerar. Värden ska tillåta List port 443 och följande domäner:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observera också att du måste inaktivera djup paket inspektion för brand Väggs lösningen på de säkra kanaler som Cognitive Services behållare skapar till Microsoft-servrar. Om du inte gör det kommer behållaren inte att fungera korrekt.

* [Förstå Azure Cognitive Services container Security](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: när virtuella datorer distribueras i samma virtuella nätverk som din Azure Cognitive Services-behållare, definiera och implementera standardkonfigurations inställningar för relaterade nätverks resurser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. CognitiveServices" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för Azure cache för Redis-instanser. Du kan också använda inbyggda princip definitioner som:
- DDoS Protection standard ska vara aktive rad

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

Om du använder Cognitive Services i en behållare kan du utöka behållar distributionen med en frontend-lösning för webb program som filtrerar skadlig trafik och stöder kryptering från slut punkt till slut punkt, så att behållarens slut punkt hålls privat och säker.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

* [Förstå Azure Cognitive Services container Security](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: när virtuella datorer distribueras i samma virtuella nätverk som din Azure Cognitive Services-behållare, kan du använda nätverks säkerhets grupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

* [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: om du använder Cognitive Services i en behållare kan du utöka behållar distributionen med en frontend-lösning för webb program som filtrerar skadlig trafik och stöder kryptering från slut punkt till slut punkt, och behållar slut punkten privat och säker. Du kan välja ett erbjudande från Azure Marketplace som stöder ID/IP-funktioner med möjligheten att inaktivera nytto Last granskning.

Tänk på att Cognitive Services behållare krävs för att skicka in avläsnings information i fakturerings syfte. Det enda undantaget är offline-behållare när de följer en annan fakturerings metod. Det gick inte att tillåta en lista över olika nätverks kanaler som Cognitive Services behållare förlitar sig på förhindrar att behållaren fungerar. Värden ska tillåta List port 443 och följande domäner:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observera också att du måste inaktivera djup paket inspektion för brand Väggs lösningen på de säkra kanaler som Cognitive Services behållare skapar till Microsoft-servrar. Om du inte gör det kommer behållaren inte att fungera korrekt.

* [Förstå Azure Cognitive Services container Security](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: om du använder Cognitive Services i en behållare kan du utöka behållar distributionen med en frontend-lösning för webb program som filtrerar skadlig trafik och stöder kryptering från slut punkt till slut punkt, och behållar slut punkten privat och säker.

Tänk på att Cognitive Services behållare krävs för att skicka in avläsnings information i fakturerings syfte. Det enda undantaget är offline-behållare när de följer en annan fakturerings metod. Det gick inte att tillåta en lista över olika nätverks kanaler som Cognitive Services behållare förlitar sig på förhindrar att behållaren fungerar. Värden ska tillåta List port 443 och följande domäner:
- *. cognitive.microsoft.com
- *. cognitiveservices.azure.com

Observera också att du måste inaktivera djup paket inspektion för brand Väggs lösningen på de säkra kanaler som Cognitive Services behållare skapar till Microsoft-servrar. Om du inte gör det kommer behållaren inte att fungera korrekt.

* [Förstå Azure Cognitive Services container Security](./cognitive-services-container-support.md#azure-cognitive-services-container-security)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper (NSG) eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Du kan också använda program säkerhets grupper (grupperna) för att förenkla komplex säkerhets konfiguration. Med program säkerhets grupper kan du konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverks säkerhets principer baserat på dessa grupper.

* [Tjänst taggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

* [Program säkerhets grupper](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser som är relaterade till din Azure Cognitive Services-behållare med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. CognitiveServices" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för Azure cache för Redis-instanser.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer, i en enda skiss definition. Använd enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är associerade med din Azure Cognitive Services-behållare för att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till din Azure Cognitive Services-behållare. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

* [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel Azure Cognitive Services för tidsstämplar i loggarna.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utfördes på din Azure Cognitive Services-behållare på kontroll Plans nivån. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för Azure cache för Redis-instanser.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Azure Cognitive Services skickar dessutom diagnostiska händelser som kan samlas in och användas i syfte att analysera, Avisera och rapportera. Du kan konfigurera diagnostikinställningar för en Cognitive Services behållare via Azure Portal. Du kan skicka en eller flera diagnostiska händelser till ett lagrings konto, en Event Hub-eller en Log Analytics-arbetsyta.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

* [Använda diagnostikinställningar till för Azure Cognitive Services](./diagnostic-logging.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor ställer du in Log Analytics arbets ytans kvarhållningsperiod enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

* [Ange logg lagrings parametrar för Log Analytics arbets ytor](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Aktivera diagnostiska inställningar i Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problem identifiering och fel sökning. Utför frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Azure Cognitive Services.

* [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/activity-log.md)

* [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/activity-log.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: du kan generera aviseringar om mått som stöds i Azure Cognitive Services genom att gå till &amp; avsnittet aviserings mått i Azure Monitor.

Konfigurera diagnostikinställningar för Cognitive Services container och skicka loggar till en Log Analytics arbets yta. I arbets ytan Log Analytics konfigurerar du aviseringar så att de äger rum när en fördefinierad uppsättning villkor inträffar. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

* [Skapa, Visa och hantera logg aviseringar med Azure Monitor](../azure-monitor/platform/alerts-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: ej tillämpligt; Azure Cognitive Services bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: ej tillämpligt; Azure Cognitive Services bearbetar eller skapar inte DNS-relaterade loggar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

* [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: kontroll Plans åtkomst till Azure Cognitive Services styrs via Azure Active Directory (AD). Azure AD har inte begreppet standard lösen ord.

Data Plans åtkomst till Azure Cognitive Services kontrol leras via åtkomst nycklar. Dessa nycklar används av klienterna som ansluter till din cache och kan återskapas när som helst.

Vi rekommenderar inte att du skapar standard lösen ord i ditt program. I stället kan du lagra dina lösen ord i Azure Key Vault och sedan använda Azure Active Directory för att hämta dem.

* [Återskapa Azure cache för Redis-åtkomst nycklar](../azure-cache-for-redis/cache-configure.md#settings)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

* [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

* [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Azure Cognitive Services använder åtkomst nycklar för att autentisera användare och stöder inte enkel inloggning på data planet nivå. Åtkomst till kontroll planet för Azure Cognitive Services är tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till ett JSON Web Token som du får från Azure Active Directory.

* [Förstå Azure Cognitive Services REST API](/rest/api/cognitiveservices/)

* [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (AD) Multi-Factor Authentication (MFA) och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

* [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Azure-resurser.

* [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

* [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Konfigurera namngivna platser i Azure Active Directory (AD) villkorlig åtkomst för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

* [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt. Om ditt användnings fall stöder AD-autentisering använder du Azure AD för att autentisera begär anden till din Cognitive Services-API.

För närvarande är det bara API för visuellt innehåll, Ansikts-API, API för textanalys, avancerad läsare, formulär igenkänning, avvikelse detektor och alla Bing-tjänster utom Anpassad Bing-sökning stöd för autentisering med Azure AD.

* [Autentisera begär anden till Cognitive Services](./authentication.md#authenticate-with-azure-active-directory)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan kunden använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Kunden ska underhålla inventeringen av API Management användar konton, stämma av åtkomsten efter behov. I API Management är utvecklare användare av de API: er som du exponerar med API Management. Som standard är nya skapade utvecklarresurser aktiva och kopplade till gruppen utvecklare. De utvecklar konton som är i ett aktivt tillstånd kan användas för att få åtkomst till alla API: er som de har prenumerationer för.

* [Hantera användarkonton i Azure API Management](../api-management/api-management-howto-create-or-invite-developers.md)

* [Hämta en lista över API Management användare](/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till Azure Active Directory (AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en Siem från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

* [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: för konto inloggning beteende avvikelse i kontroll planet använder du Azure Active Directory (AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

* [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

* [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: ännu inte tillgänglig; Customer Lockbox stöds ännu inte för Azure Cognitive Services.

* [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av VNet/undernät, taggas på lämpligt sätt och skyddas av en NSG eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör vara tillräckligt isolerade. För Virtual Machines lagring eller bearbetning av känsliga data implementerar du principer och procedurer för att inaktivera dem när de inte används.

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

* [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

* [Så här skapar du en NSG med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

* [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

* [Så här konfigurerar du aviseringen eller aviseringen och nekar med Azure-brandväggen](../firewall/threat-intel.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: ännu inte tillgänglig; funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Cognitive Services.

Microsoft hanterar den underliggande infrastrukturen för Azure Cognitive Services och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: alla Cognitive Services-slutpunkter som exponeras över http genomdriver TLS 1,2. Med ett framtvingat säkerhets protokoll bör användare som försöker anropa en Cognitive Services slut punkt följa dessa rikt linjer:
- Klient operativ systemet (OS) måste ha stöd för TLS 1,2.
- Det språk (och den plattform) som används för att göra HTTP-anropet måste ange TLS 1,2 som en del av begäran. (Beroende på språk och plattform är det antingen implicit eller explicit att ange TLS.)

* [Förstå Transport Layer Security för Azure Cognitive Services](./cognitive-services-security.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Cognitive Services. Tagga instanser som innehåller känslig information som sådan och implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Cognitive Services kontroll planet (d.v.s. Azure Portal).

* [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Cognitive Services och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

* [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: kryptering i vila för Cognitive Services är beroende av den aktuella tjänsten som används. I de flesta fall krypteras och dekrypteras data med hjälp av FIPS 140-2-kompatibla 256-bitars AES-kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att utnyttja kryptering.

Du kan också använda Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan antingen skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda Azure Key Vault API: er för att generera nycklar.

* [Lista över tjänster som krypterar information i vila](./encryption/cognitive-services-encryption-keys-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Cognitive Services och andra kritiska eller relaterade resurser.

* [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Cognitive Services.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Cognitive Services.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

* [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

* [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, i förekommande fall, för att organisera och spåra Azure cache för Redis-instanser och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

* [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

* [Så här skapar du hanterings grupper](../governance/management-groups/create-management-group-portal.md)

* [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser och Azure som helhet.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

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

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

* [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

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

**Vägledning**: definiera och implementera standardkonfigurationer för en Azure Cognitive Services-behållare med Azure policy. Använd Azure Policy alias i namn området "Microsoft. CognitiveServices" om du vill skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure-cachen för Redis-instanser.

* [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

* [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rikt linjen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner eller Azure Resource Manager mallar för dina Azure Cognitive Services-behållare och relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. cache" för att skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. CognitiveServices" för att skapa anpassade Azure policy definitioner för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt genomdriva konfigurationer för Azure cache för Redis-instanser och relaterade resurser.

* [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för virtuella Azure-datorer eller-webb program som körs på Azure App Service används för att få åtkomst till ditt Azure COGNITIVE Services-API använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cognitive Services Key Management. Se till att Key Vault mjuk borttagning har Aktiver ATS.

* [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

* [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

* [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: för virtuella Azure-datorer eller-webb program som körs på Azure App Service används för att få åtkomst till ditt Azure COGNITIVE Services-API använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cognitive Services Key Management. Se till att Key Vault mjuk borttagning har Aktiver ATS.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Azure Key Vault utan autentiseringsuppgifter i din kod.

* [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

* [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Cognitive Services), men det körs inte på kund innehåll.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure cache för Redis), men det körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Cognitive Services), men det körs inte på kund innehåll.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: data i ditt Microsoft Azure lagrings konto replikeras alltid automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Du kan välja att replikera dina data inom samma data Center, över zonindelade Data Center inom samma region eller mellan geografiskt åtskilda regioner.

Du kan också använda funktionen för livs cykel hantering för att säkerhetskopiera data till Arkiv nivån. Aktivera dessutom mjuk borttagning för dina säkerhets kopior som lagras i lagrings kontot.

* [Förstå Azure Storage redundans och Service-Level avtal](../storage/common/storage-redundancy.md)

* [Hantera Azure Blob Storage-livscykeln](../storage/blobs/storage-lifecycle-management-concepts.md)

* [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd Azure Resource Manager för att distribuera Cognitive Services och relaterade resurser. Azure Resource Manager ger dig möjlighet att exportera mallar, vilket gör att du kan distribuera om lösningen under utvecklings livs cykeln och vara säker på att resurserna distribueras i ett konsekvent tillstånd. Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet. Säkerhetskopiera i förväg delade nycklar inom Azure Key Vault.

* [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

* [Så här skapar du en Cognitive Services resurs med hjälp av en Azure Resource Manager-mall](./create-account-resource-manager-template.md?tabs=portal)

* [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

* [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

* [Introduktion till Azure Automation](../automation/automation-intro.md)

* [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra distribution av Azure Resource Manager mallar regelbundet till en isolerad prenumeration om det behövs. Testa återställning av säkerhetskopierade nycklar som har fördelats i förväg.

* [Distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

* [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera dina Azure Resource Manager-mallar på ett säkert sätt. För att skydda resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.  Använd rollbaserad åtkomst kontroll i Azure för att skydda kundens hanterade nycklar. Aktivera Soft-Delete och rensa skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. 

* [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

* [Aktivera Soft-Delete och rensa skydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

* [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

* [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Du kan också använda NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

* [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

* [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: * [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)