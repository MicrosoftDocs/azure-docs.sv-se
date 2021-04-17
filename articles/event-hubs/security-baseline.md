---
title: Azure-säkerhetsbaslinje för Event Hubs
description: Säkerhetsbaslinjen Event Hubs innehåller procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: event-hubs
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33417a9bda9ad4ce36dd6e14f74a53911f3c3473
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587161"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-säkerhetsbaslinje för Event Hubs

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Event Hubs. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas av de säkerhetskontroller **som definieras** av Azure Security Benchmark och tillhörande vägledning som gäller för Event Hubs. **Kontroller** som inte är Event Hubs inte har undantagits.

 
Om du vill Event Hubs mappa helt till Azure Security Benchmark kan du se den fullständiga [Event Hubs säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Integreringen av Azure Event Hubs med tjänstslutpunkter för virtuellt nätverk ger säker åtkomst till meddelandefunktioner från arbetsbelastningar, till exempel virtuella datorer som är bundna till virtuella nätverk, och nätverkstrafiksökvägen skyddas i båda ändar.

När de har bundets till minst en tjänstslutpunkt för virtuellt nätverk Event Hubs respektive namnområde inte längre trafik från någon plats utan auktoriserade undernät i virtuella nätverk. Ur ett virtuellt nätverksperspektiv konfigurerar en isolerad nätverkstunnel från det virtuella nätverkets undernät till meddelandetjänsten genom att Event Hubs till en tjänstslutpunkt. 

Du kan också skapa en privat slutpunkt, vilket är ett nätverksgränssnitt som ansluter dig privat och säkert till Event Hubs-tjänsten med hjälp av Azure Private Link tjänsten. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt tar tjänsten till det virtuella nätverket. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gatewayer, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. 

Du kan också skydda Azure Event Hubs med hjälp av brandväggar. Event Hubs har stöd för IP-baserade åtkomstkontroller för inkommande brandväggsstöd. Du kan ange brandväggsregler med hjälp av Azure Portal, Azure Resource Manager mallar eller via Azure CLI eller Azure PowerShell.

- [Använda tjänstslutpunkter för virtuellt nätverk med Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integrera Azure Event Hubs med Azure Private Link](private-link-service.md)

- [Konfigurera IP-brandväggsregler för Azure Event Hubs namnområden](event-hubs-ip-filtering.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center och följ rekommendationer för nätverksskydd för att skydda Event Hubs resurser i Azure. Om du använder virtuella Azure-datorer för att få åtkomst till dina händelsehubbbar aktiverar du flödesloggar för nätverkssäkerhetsgruppen och skickar loggar till ett lagringskonto för trafikgranskning.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS Protection Standard på de virtuella nätverk som är associerade med dina händelsehubbb för att skydda mot distribuerade DDoS-attacker (Denial-of-Service). Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Mer information om Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Om du använder virtuella Azure-datorer för att få åtkomst till dina händelsehubbbar aktiverar du flödesloggar för nätverkssäkerhetsgruppen och skickar loggar till ett lagringskonto för trafikgranskning. Du kan också skicka flödesloggar för nätverkssäkerhetsgruppen till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

Om det krävs för att undersöka avvikande aktivitet aktiverar du Network Watcher infångade paket.

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserad intrångsidentifiering/intrångsskyddssystem (IDS/IPS)

**Vägledning:** Om du använder virtuella Azure-datorer för att få åtkomst till dina händelsehubbbar väljer du ett erbjudande från Azure Marketplace som har stöd för IDS/IPS-funktioner med funktioner för nyttolastgranskning. Om intrångsidentifiering och/eller skydd baserat på nyttolastgranskning inte krävs för din organisation kan du använda Azure Event Hubs inbyggda brandväggsfunktionen. Du kan begränsa åtkomsten till ditt Event Hubs för ett begränsat intervall med IP-adresser eller en specifik IP-adress med hjälp av brandväggsregler.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Så här lägger du till en brandväggsregel i Event Hubs för en angiven IP-adress](event-hubs-ip-filtering.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är Azure Event Hubs nätverksnamnrymder med Azure Policy. Använd Azure Policy alias i namnrymderna **Microsoft.EventHub** och **Microsoft.Network** för att skapa anpassade principdefinitioner för att granska eller framtvinga nätverkskonfigurationen för Event Hubs namnområden. Du kan också använda inbyggda principdefinitioner relaterade till Azure Event Hubs, till exempel:

- Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk.

Ytterligare information finns på de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Inbyggd Azure-princip för Event Hubs namnområde](../governance/policy/samples/built-in-policies.md#event-hub)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för virtuella nätverk och andra resurser som rör nätverkssäkerhet och trafikflöde som är associerade med dina händelsehubbbar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksresurser relaterade till Azure Event Hubs. Skapa aviseringar i Azure Monitor som utlöses när ändringar av viktiga nätverksresurser sker.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Inom Azure Monitor konfigurerar du loggar relaterade till händelsehubbb i diagnostikinställningarna för aktivitetsloggen och händelsehubben för att skicka loggar till en Log Analytics-arbetsyta som ska efterfrågas eller till ett lagringskonto för långsiktig arkiveringslagring.

- [Så här konfigurerar du diagnostikinställningar för Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Förstå Azure-aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar för Azure Event Hubs namnområdet. Det finns tre kategorier av diagnostikinställningar för Azure Event Hubs: Arkivloggar, Driftloggar och Autoskalningsloggar. Aktivera driftloggar för att samla in information om vad som händer under Event Hubs-åtgärder, särskilt åtgärdstyp, inklusive skapande av händelsehubb, resurser som används och status för åtgärden.

Dessutom kan du aktivera diagnostikinställningar för Azure-aktivitetsloggen och skicka dem till Azure Storage ett konto, en händelsehubb eller en Log Analytics-arbetsyta. Aktivitetsloggar ger inblick i de åtgärder som har utförts på Azure Event Hubs och andra resurser. Med hjälp av aktivitetsloggar kan du bestämma "vad, vem och när" för skrivåtgärder (PUT, POST, DELETE) som ska vidtas Azure Event Hubs namnrymder.

- [Så här aktiverar du diagnostikinställningar för Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.EventHub:**

[!INCLUDE [Resource Policy for Microsoft.EventHub 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.eventhub-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** Inom Azure Monitor du kvarhållningsperioden för Log Analytics-arbetsytan enligt organisationens efterlevnadsregler för att samla in och granska händelsehubbrelaterade incidenter.

- [Så här anger du parametrar för loggbevarande för Log Analytics-arbetsytor](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Analysera och övervaka loggar för avvikande beteende och granska regelbundet resultat som är relaterade till dina händelsehubbbar. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor på loggdata. Du kan också aktivera och ta med data för att Azure Sentinel eller en systeminformation och händelsehanteringslösning från tredje part.

- [Mer information om Log Analytics-arbetsytan](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Inom Azure Monitor konfigurerar du loggar relaterade till Azure Event Hubs i aktivitetsloggen och Event Hubs-diagnostikinställningar för att skicka loggar till en Log Analytics-arbetsyta som ska efterfrågas eller till ett lagringskonto för långsiktig arkiveringslagring. Använd Log Analytics-arbetsytan för att skapa aviseringar för avvikande aktivitet som finns i säkerhetsloggar och händelser.

Du kan också aktivera och ta med data för att Azure Sentinel. 

- [Förstå Azure-aktivitetsloggen](../azure-monitor/essentials/platform-logs-overview.md)

- [Så här konfigurerar du diagnostikinställningar för Azure Event Hubs](event-hubs-diagnostic-logs.md)

- [Så här varnar du om loggdata för Log Analytics-arbetsytor](../azure-monitor/alerts/tutorial-response.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och kan frågas. Använd Azure AD PowerShell-modulen för att köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord om tillämpligt

**Vägledning:** Kontrollplansåtkomst till Event Hubs styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standardlösenord.

Dataplansåtkomst till Event Hubs styrs via Azure AD med hanterade identiteter eller Appregistreringar samt signaturer för delad åtkomst. Signaturer för delad åtkomst används av klienter som ansluter till dina händelsehubbbar och kan återskapas när som helst.

- [Förstå signaturer för delad åtkomst för Event Hubs](authenticate-shared-access-signature.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center Identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla reda på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare tilldelad till din prenumeration

- Inaktuella konton med ägarbehörighet bör tas bort från prenumerationen

- Externa konton med ägarbehörighet ska tas bort från din prenumeration

Ytterligare information finns på de refererade länkarna.

- [Så här använder du Azure Security Center för att övervaka identitet och åtkomst (förhandsversion)](../security-center/security-center-identity-access.md)

- [Så här använder du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory enkel inloggning (SSO)

**Vägledning:** Microsoft Azure ger integrerad hantering av åtkomstkontroll för resurser och program baserat på Azure Active Directory (Azure AD). En viktig fördel med att använda Azure AD med Azure Event Hubs är att du inte längre behöver lagra dina autentiseringsuppgifter i koden. I stället kan du begära en OAuth 2.0-åtkomsttoken från Microsoft Identity-plattformen. Resursnamnet för att begära en token är https: \/ /eventhubs.azure.net/. Azure AD autentiserar säkerhetsobjekt (en användare, grupp eller tjänstens huvudnamn) som kör programmet. Om autentiseringen lyckas returnerar Azure AD en åtkomsttoken till programmet och programmet kan sedan använda åtkomsttoken för att auktorisera begäran till Azure Event Hubs resurser.

- [Så här autentiserar du ett program med Azure AD för att få åtkomst Event Hubs resurser](authenticate-application.md)

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory multifaktorautentisering (Azure AD) och följ Azure Security Center rekommendationerna för identitets- och åtkomsthantering för att skydda dina Event Hub-aktiverade resurser.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

**Vägledning:** Använd arbetsstationer med privilegierad åtkomst (PAW) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Event Hub-aktiverade resurser.

- [Läs mer om arbetsstationer med privilegierad åtkomst](/security/compass/privileged-access-devices)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden. För ytterligare loggning skickar du Azure Security Center riskidentifieringsaviseringar till Azure Monitor och konfigurerar anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper.

- [Förstå Azure AD-riskidentifiering](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar du åtgärdsgrupper för anpassade aviseringar och meddelanden](../azure-monitor/alerts/action-groups.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska grupper av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för Azure-resurser, till exempel Event Hubs. Detta möjliggör rollbaserad åtkomstkontroll i Azure (Azure RBAC) till administrativa känsliga resurser.

- [ Så här skapar och konfigurerar du en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Auktorisera åtkomst Event Hubs resurser med hjälp av Azure AD](authorize-access-azure-active-directory.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Rotera dessutom regelbundet dina Event Hubs signaturer för delad åtkomst.

- [Förstå Azure AD-rapportering](../active-directory/reports-monitoring/index.yml)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

- [Förstå signaturer för delad åtkomst för Event Hubs](authenticate-shared-access-signature.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Du har åtkomst till Azure Active Directory-inloggningsaktivitet (Azure AD), gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med systeminformation från tredje part och händelsehanteringslösning eller ett övervakningsverktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logga aviseringar i Log Analytics.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Auktorisera åtkomst Event Hubs resurser med hjälp av Azure AD](authorize-access-azure-active-directory.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontobeteendeavvikelse vid inloggning

**Vägledning:** Använd identity protection- och riskidentifieringsfunktioner i Azure Active Directory (Azure AD) för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till dina Event Hubs-aktiverade resurser. Du bör aktivera automatiserade svar Azure Sentinel att implementera organisationens säkerhetssvar.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** För närvarande inte tillgänglig; Customer Lockbox stöds inte ännu för Event Hubs.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för resurser som är relaterade till Event Hubs för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera separata prenumerationer, om du vill, med hanteringsgrupper för utveckling, testning och produktion. Event Hubs ska avgränsas med virtuella nätverk med tjänstslutpunkter aktiverade och taggade på rätt sätt.

Du kan också skydda Azure Event Hubs med hjälp av brandväggar. Azure Event Hubs har stöd för IP-baserade åtkomstkontroller för inkommande brandväggsstöd. Du kan ange brandväggsregler med hjälp av Azure Portal, Azure Resource Manager mallar eller via Azure CLI eller Azure PowerShell.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Konfigurera IP-brandväggsregler för Azure Event Hubs namnområden](event-hubs-ip-filtering.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../virtual-network/quick-create-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Använd virtuella nätverk, tjänstslutpunkter, Event Hubs-brandvägg, nätverkssäkerhetsgrupper och tjänsttaggar för att minska risken för datafiltrering när du använder virtuella datorer för att få åtkomst till dina händelsehubbbar.

Microsoft hanterar den underliggande infrastrukturen för Azure Event Hubs och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

- [Konfigurera IP-brandväggsregler för Azure Event Hubs namnområden](event-hubs-ip-filtering.md)

- [Förstå Virtual Network tjänstslutpunkter med Azure Event Hubs](event-hubs-service-endpoints.md)

- [Integrera Azure Event Hubs med Azure Private Link](private-link-service.md)

- [Förstå nätverkssäkerhetsgrupper och tjänsttaggar](../virtual-network/network-security-groups-overview.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för Azure Event Hubs. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och går mycket långt för att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Azure Event Hubs stöder användning Azure Active Directory (Azure AD) för att auktorisera begäranden till Event Hubs resurser. Med Azure AD kan du använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att bevilja behörigheter till ett säkerhetsobjekt, som kan vara en användare eller ett huvudnamn för programtjänsten.

- [Förstå Azure RBAC och tillgängliga roller för Azure Event Hubs](authorize-access-azure-active-directory.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Event Hubs stöder alternativet att kryptera vilodata med antingen Microsoft-hanterade nycklar eller kund hanterade nycklar. Med den här funktionen kan du skapa, rotera, inaktivera och återkalla åtkomst till de kundbaserade nycklar som används för att kryptera Azure Event Hubs vilodata.

- [Så här konfigurerar du kund hanterade nycklar för kryptering Azure Event Hubs](configure-customer-managed-key.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsinstanser av Azure Event Hubs och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd lösningen för automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (Azure Event Hubs namnområden) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser som ger metadata för att organisera dem logiskt i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure Event Hubs och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Dessutom kan du använda Azure Resource Graph för att köra frågor mot/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Ytterligare information finns på de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Konfigurera villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för Azure Event Hubs distributioner. Använd Azure Policy alias i namnområdet **Microsoft.EventHub** för att skapa anpassade principer för att granska eller framtvinga konfigurationer. Du kan också använda inbyggda principdefinitioner för Azure Event Hubs till exempel:

- Diagnostikloggar i händelsehubben ska vara aktiverade

- Händelsehubben bör använda en tjänstslutpunkt för virtuellt nätverk

Ytterligare information finns på de refererade länkarna.

- [Inbyggd Azure-princip för Event Hubs namnområde](../governance/policy/samples/built-in-policies.md#event-hub)

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] effekter för att framtvinga säkra inställningar för Event Hubs-aktiverade resurser. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

 
- [Mer information om Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.EventHub" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Policy alias i namnområdet "Microsoft.EventHub" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för Azure Event Hubs distributioner och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service och som används för att komma åt händelsehubben använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda hanteringen av signaturer för delad åtkomst för dina Azure Event Hubs-distributioner. Se Key Vault har konfigurerats med mjuk borttagning aktiverat.

- [Autentisera en hanterad identitet med Azure Active Directory (Azure AD) för att få åtkomst Event Hubs resurser](./authenticate-managed-identity.md?tabs=latest)

- [Konfigurera kund hanterade nycklar för Event Hubs](configure-customer-managed-key.md)

- [Så här integrerar du med azure-hanterade identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** För virtuella Azure-datorer eller webbprogram som körs på Azure App Service och som används för att få åtkomst till dina händelsehubbb använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och skydda Azure Event Hubs. Se Key Vault har konfigurerats med mjuk borttagning aktiverat.

Använd hanterade identiteter för att förse Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Azure Key Vault, utan några autentiseringsuppgifter i koden.

- [Autentisera en hanterad identitet med Azure AD för att få åtkomst Event Hubs resurser](./authenticate-managed-identity.md?tabs=latest)

- [Konfigurera kund hanterade nycklar för Event Hubs](configure-customer-managed-key.md)

- [Så här konfigurerar du hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Så här integrerar du med hanterade Azure-identiteter](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

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

**Vägledning:** Skanna innehåll som överförs i förväg till Icke-beräkningsbaserade Azure-resurser, till exempel Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

Microsofts program mot skadlig kod är aktiverat på den underliggande värd som stöder Azure-tjänster (till exempel Azure Cache for Redis), men den körs inte på kundinnehåll.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Konfigurera geo-haveriberedskap för Azure Event Hubs. När hela Azure-regioner eller datacenter (om inga tillgänglighetszoner används) drabbas av driftstopp är det viktigt att databehandlingen fortsätter att fungera i en annan region eller ett annat datacenter. Därför är geo-haveriberedskap och geo-replikering viktiga funktioner för alla företag. Azure Event Hubs stöder både geo-haveriberedskap och geo-replikering på namnområdesnivå. 

- [Förstå geo-haveriberedskap för Azure Event Hubs](./event-hubs-geo-dr.md#availability-zones)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Azure Event Hubs tillhandahåller kryptering av vilodata med Azure Storage Service Encryption (Azure SSE). Event Hubs använder Azure Storage för att lagra data och som standard krypteras alla data som lagras med Azure Storage med hjälp av Microsoft-hanterade nycklar. Om du använder Azure Key Vault för att lagra kund hanterade nycklar ska du se till att dina nycklar säkerhetskopieras regelbundet automatiskt.

Se till att regelbundna automatiserade säkerhetskopieringar av dina Key Vault Secrets med följande PowerShell-kommando: Backup-AzKeyVaultSecret

- [Så här konfigurerar du kund hanterade nycklar för kryptering Azure Event Hubs vilodata](configure-customer-managed-key.md)

- [Säkerhetskopiera Key Vault hemligheter](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Återställa nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Aktivera mjuk borttagning i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Azure Event Hubs kräver att kund hanterade nycklar har mjuk borttagning och Rensa inte konfigurerat.

Konfigurera mjuk borttagning för Det Azure-lagringskonto som används för att samla Event Hubs data. Observera att den här funktionen inte stöds för Azure Data Lake Storage Gen 2 ännu.

- [Så här aktiverar du mjuk borttagning i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Konfigurera ett nyckelvalv med nycklar](configure-customer-managed-key.md)

- [Mjuk borttagning för Azure Storage-blobar](/azure/storage/blobs/soft-delete-blob-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning:** Se till att det finns skriftliga planer för incidenthantering som definierar roller för personal samt faser för incidenthantering/incidenthantering.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar aviseringar en allvarlighetsgrad för att hjälpa dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt förtroendenivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig part.  Granska incidenter i efterhand för att säkerställa att problemen är lösta. 

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" om säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att intrångstesterna inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstester på live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
