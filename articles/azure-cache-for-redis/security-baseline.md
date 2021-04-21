---
title: Azure-säkerhetsbaslinje för Azure Cache for Redis
description: Den Azure Cache for Redis säkerhetsbaslinjen innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: cache
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 0397f53c54f488f6840e35212de3e51624f360d7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830006"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure-säkerhetsbaslinje för Azure Cache for Redis

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Cache for Redis. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Cache for Redis. **Kontroller** som inte är Azure Cache for Redis har undantagits.

 
Information om hur Azure Cache for Redis mappning till Azure Security Benchmark finns i den fullständiga Azure Cache for Redis [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Distribuera din Azure Cache for Redis instans i ett virtuellt nätverk (VNet). Ett VNet är ett privat nätverk i molnet. När en Azure Cache for Redis-instans har konfigurerats med ett virtuellt nätverk är den inte offentligt adresserbar och kan bara nås från virtuella datorer och program i det virtuella nätverket.

Du kan också ange brandväggsregler med start- och slut-IP-adressintervall. När brandväggsregler har konfigurerats kan endast klientanslutningar från de angivna IP-adressintervallen ansluta till cachen.

- [Så här konfigurerar du Virtual Network för en Premium-Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [Så här konfigurerar Azure Cache for Redis brandväggsregler](./cache-configure.md#firewall)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** När Virtual Machines distribueras i samma virtuella nätverk som din Azure Cache for Redis-instans kan du använda nätverkssäkerhetsgrupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Distribution av Azure Virtual Network (VNet) ger förbättrad säkerhet och isolering för din Azure Cache for Redis, samt undernät, principer för åtkomstkontroll och andra funktioner för att ytterligare begränsa åtkomsten. När den distribueras i ett VNet Azure Cache for Redis inte offentligt adresserbar och kan endast nås från virtuella datorer och program i det virtuella nätverket.

Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med dina Azure Cache for Redis instanser för att skydda mot distribuerade DDoS-attacker (Distributed Denial-of-Service). Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du Virtual Network för ett Premium-Azure Cache for Redis](cache-how-to-premium-vnet.md)

- [Hantera Azure DDoS Protection Standard med hjälp av Azure Portal](../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** När virtuella datorer distribueras i samma virtuella nätverk som din Azure Cache for Redis-instans kan du använda nätverkssäkerhetsgrupper (NSG) för att minska risken för data exfiltrering. Aktivera NSG-flödesloggar och skicka loggar till ett Azure Storage-konto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Några fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** När du använder Azure Cache for Redis med dina webbprogram som körs på Azure App Service- eller beräkningsinstanser distribuerar du alla resurser i ett Azure Virtual Network (VNet) och skyddar med en Azure Web Application Firewall (WAF) på Application Gateway. Konfigurera WAF att köras i "skyddsläge". Skyddsläge blockerar intrång och attacker som identifieras av reglerna. Angriparen får undantaget "403 obehörig åtkomst" och anslutningen stängs. Skyddsläge registrerar sådana attacker i WAF-loggarna.

Du kan också välja ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastgranskning och/eller avvikelseidentifiering.

- [Förstå Azure WAF-funktioner](../web-application-firewall/ag/ag-overview.md)

- [Så här distribuerar du Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för webbprogram som körs Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** Använd tjänsttaggar för virtuella nätverk för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper (NSG) eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Du kan också använda programsäkerhetsgrupper (ASG) för att förenkla komplex säkerhetskonfiguration. Med ASG:er kan du konfigurera nätverkssäkerhet som en naturlig förlängning av ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.

- [Tjänsttaggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

- [Programsäkerhetsgrupper](../virtual-network/network-security-groups-overview.md#application-security-groups)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser relaterade till dina Azure Cache for Redis instanser med Azure Policy. Använd Azure Policy alias i namnrymderna "Microsoft.Cache" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Azure Cache for Redis instanser. Du kan också använda inbyggda principdefinitioner som:
- Endast säkra anslutningar till Redis Cache bör vara aktiverade

- DDoS Protection Standard ska vara aktiverat

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, rollbaserad åtkomstkontroll i Azure (Azure RBAC) och principer i en enda skissdefinition. Tillämpa enkelt skissen på nya prenumerationer och miljöer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverksresurser som är associerade Azure Cache for Redis din distribution för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till dina Azure Cache for Redis instanser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen och skicka loggarna till en Log Analytics-arbetsyta, en Azure-händelsehubb eller ett Azure Storage-konto för arkivering. Aktivitetsloggar ger inblick i de åtgärder som utfördes på Azure Cache for Redis instanser på kontrollplansnivå. Med hjälp av Azure-aktivitetsloggdata kan du fastställa "vad, vem och när" för alla skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure Cache for Redis instanser.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen och skicka loggarna till en Log Analytics-arbetsyta, en Azure-händelsehubb eller ett Azure Storage-konto för arkivering. Aktivitetsloggar ger inblick i de åtgärder som utfördes på Azure Cache for Redis instanser på kontrollplansnivå. Med hjälp av Azure-aktivitetsloggdata kan du fastställa "vad, vem och när" för alla skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure Cache for Redis instanser.

Mått är tillgängliga genom att aktivera diagnostikinställningar, men granskningsloggning på dataplanet är ännu inte tillgänglig för Azure Cache for Redis.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor anger du loggens kvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina Azure Cache for Redis-instanser enligt organisationens efterlevnadsregler.

Observera att granskningsloggningen på dataplanet ännu inte är tillgänglig för Azure Cache for Redis.

- [Så här anger du parametrar för loggbevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på aktivitetsloggdata som kan ha samlats in för Azure Cache for Redis.

Observera att granskningsloggningen på dataplanet ännu inte är tillgänglig för Azure Cache for Redis.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här samlar du in och analyserar Azure-aktivitetsloggar på Log Analytics-arbetsytan i Azure Monitor](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Du kan konfigurera för att ta emot aviseringar baserat på mått och aktivitetsloggar relaterade till dina Azure Cache for Redis instanser. Azure Monitor kan du konfigurera en avisering för att skicka ett e-postmeddelande, anropa en webhook eller anropa en Azure-logikapp.

Mått är tillgängliga genom att aktivera diagnostikinställningar, men granskningsloggning på dataplanet är ännu inte tillgänglig för Azure Cache for Redis.

- [Så här konfigurerar du aviseringar för Azure Cache for Redis](./cache-how-to-monitor.md#alerts)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Kontrollplansåtkomst till Azure Cache for Redis styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standardlösenord.

Dataplanets åtkomst till Azure Cache for Redis styrs via åtkomstnycklar. Dessa nycklar används av klienterna som ansluter till ditt cacheminne och kan återskapas när som helst.

Vi rekommenderar inte att du skapar standardlösenord i ditt program. I stället kan du lagra dina lösenord i Azure Key Vault och sedan använda Azure AD för att hämta dem.

- [Så här återskapar du Azure Cache for Redis åtkomstnycklar](./cache-configure.md#settings)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla reda på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare tilldelad till din prenumeration

- Inaktuella konton med ägarbehörighet bör tas bort från prenumerationen

- Externa konton med ägarbehörighet ska tas bort från din prenumeration

Läs mer i följande referenser:

- [Så här använder du Azure Security Center för att övervaka identitet och åtkomst (förhandsversion)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Azure Cache for Redis använder åtkomstnycklar för att autentisera användare och stöder inte enkel inloggning (SSO) på dataplansnivå. Åtkomst till kontrollplanet för Azure Cache for Redis är tillgänglig via REST API och stöder enkel inloggning. Om du vill autentisera anger du auktoriseringshuvudet för dina begäranden till en JSON Web Token som du får från Azure Active Directory (Azure AD).

- [Förstå Azure Cache for Redis REST API](/rest/api/redis/)

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ Azure Security Center rekommendationer för identitets- och åtkomsthantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd PAW (Privileged Access Workstations) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser. 

- [Läs mer om arbetsstationer för privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och avisering om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd dessutom Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

- [Så här distribuerar du Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå Azure AD-riskidentifiering](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Konfigurera namngivna platser i Azure Active Directory (Azure AD) villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD lagrar också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Azure AD-autentisering kan inte användas för direkt åtkomst till Azure Cache for Redis:s dataplan, men Azure AD-autentiseringsuppgifter kan användas för administration på kontrollplansnivå (d.v.s. Azure Portal) för att styra Azure Cache for Redis åtkomstnycklar.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](../active-directory/reports-monitoring/index.yml)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory(Azure AD) inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logga aviseringar i Log Analytics.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** För avvikelse i kontoinloggningsbeteende på kontrollplanet använder du identitetsskydd för Azure Active Directory (Azure AD) och riskidentifieringsfunktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Azure Cache for Redis instanser ska avgränsas med virtuellt nätverk/undernät och taggas på rätt sätt. Du kan också använda Azure Cache for Redis för att definiera regler så att endast klientanslutningar från angivna IP-adressintervall kan ansluta till cachen.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Så här distribuerar Azure Cache for Redis till ett VNet](cache-how-to-premium-vnet.md)

- [Så här konfigurerar Azure Cache for Redis brandväggsregler](./cache-configure.md#firewall)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Inte tillgänglig ännu; funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Cache for Redis.

Microsoft hanterar den underliggande infrastrukturen för Azure Cache for Redis och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Azure Cache for Redis kräver TLS-krypterad kommunikation som standard. TLS-versionerna 1.0, 1.1 och 1.2 stöds för närvarande. TLS 1.0 och 1.1 är dock på väg mot utfasning inom hela branschen, så använd TLS 1.2 om det är möjligt. Om klientbiblioteket eller verktyget inte stöder TLS kan du aktivera okrypterade anslutningar via api:erna för Azure Portal eller hantering. I sådana fall där krypterade anslutningar inte är möjliga bör du placera cacheminnet och klientprogrammet i ett virtuellt nätverk.

- [Förstå kryptering under överföring för Azure Cache for Redis](cache-best-practices.md)

- [Förstå nödvändiga portar som används i scenarier med VNet-cache](./cache-how-to-premium-vnet.md#outbound-port-requirements)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Cache:**

[!INCLUDE [Resource Policy for Microsoft.Cache 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.cache-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Cache for Redis. Tagga instanser som innehåller känslig information som sådan och implementera en lösning från tredje part om det behövs för efterlevnadsändamål.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att styra åtkomsten till Azure Cache for Redis kontrollplanet (d.v.s. Azure Portal).

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Cache for Redis lagrar kunddata i minnet, och även om det skyddas starkt av många kontroller som implementeras av Microsoft krypteras inte minnet som standard. Om det krävs av din organisation krypterar du innehållet innan det lagras i Azure Cache for Redis.

Om du Azure Cache for Redis funktionen "Redis Data Persistence" skickas data till ett Azure Storage konto som du äger och hanterar. Du kan konfigurera beständighet från bladet "Ny Azure Cache for Redis" när cacheminnet skapas och på menyn Resurs för befintliga premiumcacheminnen.

Data i Azure Storage krypteras och dekrypteras transparent med hjälp av 256-bitars AES-kryptering, ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering kan inte inaktiveras. Du kan använda Microsoft-hanterade nycklar för kryptering av ditt lagringskonto eller hantera kryptering med dina egna nycklar.

- [Så här konfigurerar du beständighet i Azure Cache for Redis](cache-how-to-premium-persistence.md)

- [Förstå kryptering för Azure Storage konton](../storage/common/storage-service-encryption.md)

- [Förstå Azure-kunddataskydd](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Cache for Redis och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Följ rekommendationerna från Azure Security Center om att skydda Azure Cache for Redis instanser och relaterade resurser.

Microsoft utför sårbarhetshantering på de underliggande system som stöder Azure Cache for Redis.

- [Förstå Azure Security Center rekommendationer](../security-center/recommendations-reference.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga/identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt (läs) behörigheter i din klientorganisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan Resource Graph via en Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure Cache for Redis instanser och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd också Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Läs mer i följande referenser:

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda resurstaggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Läs mer i följande referenser:

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Konfigurera villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för dina Azure Cache for Redis instanser med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av Azure Cache for Redis instanser. Du kan också använda inbyggda principdefinitioner som är relaterade Azure Cache for Redis instanser, till exempel:

- Endast säkra anslutningar till Redis Cache bör vara aktiverade

Läs mer i följande referenser:

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Om du använder anpassade Azure Policy eller Azure Resource Manager-mallar för dina Azure Cache for Redis-instanser och relaterade resurser använder du Azure Repos för att lagra och hantera din kod på ett säkert sätt.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.Cache" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Använd Azure Policy [audit], [deny] och [deploy if not exist] för att automatiskt framtvinga konfigurationer för dina Azure Cache for Redis-instanser och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service och som används för att få åtkomst till dina Azure Cache for Redis-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cache for Redis hemlighetshantering. Kontrollera Key Vault mjuk borttagning är aktiverat.

- [Så här integrerar du med azure-hanterade identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service och som används för att få åtkomst till dina Azure Cache for Redis-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Cache for Redis hemlighetshantering. Kontrollera Key Vault mjuk borttagning är aktiverat.

Använd hanterade identiteter för att förse Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, Azure Key Vault, utan några autentiseringsuppgifter i koden.

- [Så här konfigurerar du hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Så här integrerar du med azure-hanterade identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska laddas upp till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure Cache for Redis), men den körs inte på kundinnehåll.

Försök allt innehåll som överförs till Icke-beräkningsbaserade Azure-resurser, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Aktivera Redis-beständighet. Med Redis-beständighet kan du bevara data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data, som du kan läsa in i händelse av maskinvarufel. Detta är en enorm fördel jämfört med Basic- eller Standard-nivån där alla data lagras i minnet och det kan finnas potentiell dataförlust vid fel där cachenoderna ligger nere.

Du kan också använda Azure Cache for Redis Exportera. Med Exportera kan du exportera data som lagras i Azure Cache for Redis Till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella dator som är värd för Azure Cache for Redis-serverinstansen och filen laddas upp till det angivna lagringskontot. När exportåtgärden har slutförts med antingen statusen lyckades eller misslyckades tas den tillfälliga filen bort.

- [Så här aktiverar du Redis-beständighet](cache-how-to-premium-persistence.md)

- [Så här använder du Azure Cache for Redis Export](cache-how-to-import-export-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Aktivera Redis-beständighet. Med Redis-beständighet kan du bevara data som lagras i Redis. Du kan också ta ögonblicksbilder och säkerhetskopiera data, som du kan läsa in vid maskinvarufel. Det här är en stor fördel jämfört med Basic- eller Standard-nivån där alla data lagras i minnet och det kan finnas potentiell dataförlust vid ett fel där cachenoderna ligger nere.

Du kan också använda Azure Cache for Redis Exportera. Med Export kan du exportera data som lagras i Azure Cache for Redis till Redis-kompatibla RDB-filer. Du kan använda den här funktionen för att flytta data från en Azure Cache for Redis instans till en annan eller till en annan Redis-server. Under exportprocessen skapas en temporär fil på den virtuella dator som är värd för Azure Cache for Redis-serverinstansen och filen laddas upp till det angivna lagringskontot. När exportåtgärden har slutförts med statusen lyckades eller misslyckades tas den tillfälliga filen bort.

Om du Azure Key Vault att lagra autentiseringsuppgifter för dina Azure Cache for Redis-instanser ska du se till att dina nycklar säkerhetskopieras regelbundet automatiskt.

- [Så här aktiverar du Redis-beständighet](cache-how-to-premium-persistence.md)

- [Så här använder du Azure Cache for Redis Exportera](cache-how-to-import-export-data.md)

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Använd Azure Cache for Redis Importera. Import kan användas för att ta Redis-kompatibla RDB-filer från valfri Redis-server som körs i alla moln eller miljöer, inklusive Redis som körs på Linux, Windows eller någon molnleverantör som Amazon Web Services och andra. Att importera data är ett enkelt sätt att skapa ett cacheminne med förifyllda data. Under importen läser Azure Cache for Redis RDB-filerna från Azure Storage till minnet och infogar sedan nycklarna i cacheminnet.

Testa regelbundet dataåterställning av dina Azure Key Vault hemligheter.

- [Använda Azure Cache for Redis import](cache-how-to-import-export-data.md)

- [Återställa Key Vault hemligheter](/powershell/module/az.keyvault/restore-azkeyvaultsecret?preserve-view=true&view=azps-4.8.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också använda NIST:s guide för hantering av säkerhetsincidenter för att underlätta skapandet av en egen plan för incidenthantering](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt förtroendenivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig eller obehörig part.  Granska incidenter i efterhand för att se till att problemen är lösta.

- [Så här anger du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att intrångstesterna inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
