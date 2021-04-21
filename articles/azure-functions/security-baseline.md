---
title: Azure-säkerhetsbaslinje för Azure Functions
description: Säkerhetsbaslinjen Azure Functions procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: 90ae774a4e243b854758c1c7a6cc10a882cd584f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831086"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure-säkerhetsbaslinje för Azure Functions

Den här säkerhetsbaslinjen tillämpar vägledning [från Azure Security Benchmark version 1.0](../security/benchmarks/overview-v1.md) för Azure Functions. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas av de säkerhetskontroller **som definieras** av Azure Security Benchmark och de relaterade riktlinjer som gäller för Azure Functions. **Kontroller** som inte är Azure Functions inte har undantagits.

 
Information om hur Azure Functions mappning helt till Azure Security Benchmark finns i den fullständiga [mappningsfilen Azure Functions säkerhetsbaslinje.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Integrera dina Azure Functions med ett virtuellt Azure-nätverk. Funktionsappar som körs i Premium-planen har samma värdfunktioner som webbappar i Azure App Service, vilket omfattar funktionen "VNet-integrering".  Med virtuella Azure-nätverk kan du placera många av dina Azure-resurser, till Azure Functions, i ett dirigerbart nätverk som inte är internet.

- [Så här integrerar du Functions med en Azure-Virtual Network](functions-create-vnet.md)

- [Förstå VNet-integrering för Azure Functions och Azure App Service](../app-service/web-sites-integrate-with-vnet.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Använd Azure Security Center och följ nätverksskyddsrekommendationerna för att skydda nätverksresurser och nätverkskonfigurationer relaterade till dina Azure Functions appar.

Om du använder nätverkssäkerhetsgrupper med din Azure Functions-implementering aktiverar du flödesloggar för nätverkssäkerhetsgrupper och skickar loggar till ett Azure Storage-konto för trafikgranskningar. Du kan också skicka flödesloggar för nätverkssäkerhetsgrupper till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Några fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Så här aktiverar du NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** För att skydda Azure Functions slutpunkter i produktion bör du överväga att implementera något av följande säkerhetsalternativ på funktionsappnivå:

- Aktivera App Service autentisering eller auktorisering för funktionsappen

- Använda Azure API Management (APIM) för att autentisera begäranden

- Distribuera funktionsappen till en Azure App Service-miljön.

Kontrollera dessutom att fjärrfelsökning har inaktiverats för din Azure Functions. Dessutom bör resursdelning för korsande ursprung (CORS) inte tillåta att alla domäner får åtkomst till din funktionsapp i Azure. Tillåt endast att obligatoriska domäner interagerar med funktionsappen.

Överväg att distribuera Azure Web Application Firewall (WAF) som en del av nätverkskonfigurationen för ytterligare kontroll av inkommande trafik. Aktivera diagnostikinställning för WAF och mata in loggar till ett lagringskonto, en händelsehubb eller en Log Analytics-arbetsyta. 

- [Så här skyddar Azure Functions slutpunkter i produktion](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Så här distribuerar du Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera DDoS Protection Standard i de virtuella nätverk som är associerade med dina funktionsappar för att skydda mot DDoS-attacker. Använd hotinformationsfunktionerna i Azure Security Center Integrated för att neka kommunikation med kända skadliga eller oanvända offentliga IP-adresser.

Konfigurera dessutom en frontend-gateway, till exempel Azure Web Application Firewall, för att autentisera alla inkommande begäranden och filtrera bort skadlig trafik. Azure Web Application Firewall kan skydda funktionsappen genom att granska inkommande webbtrafik för att blockera SQL- injections, skript över flera webbplatser, uppladdning av skadlig kod och DDoS-attacker. Introduktion av en Web Application Firewall kräver antingen App Service-miljön eller användning av privata slutpunkter. Du kan använda privata slutpunkter för dina funktioner som finns i premium- och App Service planer.

- [Nätverksalternativ för Azure Functions](functions-networking-options.md)

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

- [Förstå Azure Security Center Integrated Threat Intelligence](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Om du använder nätverkssäkerhetsgruppen med din Azure Functions ska du aktivera flödesloggar för nätverkssäkerhetsgruppen och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka flödesloggar till en Log Analytics-arbetsyta och använda Trafikanalys för att ge insikter om trafikflödet i ditt Azure-moln. Några fördelar med Trafikanalys är möjligheten att visualisera nätverksaktivitet och identifiera hotpunkter, identifiera säkerhetshot, förstå trafikflödesmönster och hitta felkonfigurationer i nätverk.

- [Aktivera flödesloggar för nätverkssäkerhetsgrupp](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivera och använda Trafikanalys](../network-watcher/traffic-analytics.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Konfigurera en frontend-gateway, till exempel Azure Web Application Firewall autentisera alla inkommande begäranden och filtrera bort skadlig trafik. Azure Web Application Firewall kan skydda dina funktionsappar genom att granska inkommande webbtrafik för att blockera SQL- injections, skript över flera webbplatser, uppladdning av skadlig kod och DDoS-attacker. Introduktion av en Web Application Firewall kräver antingen App Service-miljön eller användning av privata slutpunkter. Du kan använda privata slutpunkter för dina funktioner i Premium- och App Service planer.

Det finns också flera marketplace-alternativ som Barracuda Web Application Firewall for Azure som är tillgängliga på Azure Marketplace som innehåller funktioner för intrångsidentifiering eller skydd.

- [Nätverksalternativ för Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-plan](functions-premium-plan.md)

- [Introduktion till Azure App Service-miljöer](../app-service/environment/intro.md)

- [Nätverksöverväganden för en App Service-miljö](../app-service/environment/network-info.md) 

- [Förstå Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Använda privata slutpunkter för Azure Functions](../app-service/networking/private-endpoint.md)

- [Förstå Barracuda WAF-molntjänsten](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Konfigurera en frontend-gateway för ditt nätverk, till exempel Azure Web Application Firewall med TLS-kryptering från slutpunkt till slutpunkt. Introduktion av en Web Application Firewall kräver antingen App Service-miljön eller användning av privata slutpunkter. Du kan använda privata slutpunkter för dina funktioner i Premium- och App Service planer.

- [Nätverksalternativ för Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-plan](functions-premium-plan.md)

- [Introduktion till Azure App Service-miljöer](../app-service/environment/intro.md)

- [Nätverksöverväganden för en App Service-miljö](../app-service/environment/network-info.md) 

- [Förstå Azure Web Application Firewall](../web-application-firewall/overview.md)

- [Så här konfigurerar du TLS från Application Gateway med portalen](../application-gateway/end-to-end-ssl-portal.md)

- [Använda privata slutpunkter för Azure Functions](../app-service/networking/private-endpoint.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och det administrativa arbetet med nätverkssäkerhetsregler

**Vägledning:** Använd Virtual Network för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupp eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (t.ex. AzureAppService) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Mer information om hur du använder tjänsttaggar](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksinställningar relaterade till Azure Functions. Använd Azure Policy alias i namnrymderna "Microsoft.Web" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för Azure Functions. Du kan också använda inbyggda principdefinitioner för Azure Functions, till exempel:

- CORS bör inte tillåta att alla resurser får åtkomst till dina funktionsappar

- Funktionsappen bör endast vara tillgänglig via HTTPS

- Den senaste TLS-versionen ska användas i funktionsappen

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, rollbaserad åtkomstkontroll i Azure (Azure RBAC) och principer i en enda skissdefinition. Du kan enkelt tillämpa skissen på nya prenumerationer, miljöer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Om du använder nätverkssäkerhetsgruppen med din Azure Functions ska du använda taggar för nätverkssäkerhetsmålen och andra resurser som rör nätverkssäkerhet och trafikflöde. För enskilda regler för nätverkssäkerhetsgrupp använder du fältet "Beskrivning" för att ange affärs behov och/eller varaktighet och så vidare för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure-principdefinitionerna som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar för nätverksinställningar och resurser som är relaterade Azure Functions distributioner. Skapa aviseringar Azure Monitor som utlöses när viktiga nätverksinställningar eller resurser ändras. 

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen för kontrollplansgranskning och skicka loggarna till en Log Analytics-arbetsyta, en Azure-händelsehubb eller ett Azure Storage-konto för arkivering. Med hjälp av Azure-aktivitetsloggdata kan du avgöra "vad, vem och när" för skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure-resurser.

Azure Functions även inbyggd integrering med Azure Application Insights för att övervaka funktioner. Application Insights samlar in logg-, prestanda- och feldata. Den identifierar automatiskt prestandaavvikelser och innehåller kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används.

Om du har inbyggd anpassad säkerhets-/granskningsloggning i funktionsappen aktiverar du diagnostikinställningen "FunctionAppLogs" och skickar loggarna till en Log Analytics-arbetsyta, En Azure-händelsehubb eller ett Azure Storage-konto för arkivering. 

Alternativt kan du aktivera och ta med data för att Azure Sentinel eller en systeminformations- och händelsehanteringslösning från tredje part. 

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här ställer du Azure Functions med Azure Application Insights](functions-monitoring.md)

- [Så här aktiverar du diagnostikinställningar (användargenererade loggar) för Azure Functions](functions-monitor-log-analytics.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen för kontrollplansgranskning och skicka loggarna till en Log Analytics-arbetsyta, en Azure-händelsehubb eller ett Azure Storage-konto för arkivlagring. Med hjälp av Azure-aktivitetsloggdata kan du bestämma "vad, vem och när" för skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för dina Azure-resurser.

Om du har inbyggd anpassad säkerhets-/granskningsloggning i funktionsappen aktiverar du diagnostikinställningen "FunctionAppLogs" och skickar loggarna till en Log Analytics-arbetsyta, En Azure-händelsehubb eller ett Azure Storage-konto för arkivering. 

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar (användargenererade loggar) för Azure Functions](functions-monitor-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Samla in säkerhetsloggar från operativsystem

**Vägledning:** Ej tillämpligt; den här riktlinjen är avsedd för IaaS-beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor du loggens kvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina funktionsappar enligt organisationens efterlevnadsregler.

- [Så här anger du parametrar för loggbevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen samt diagnostikinställningar för funktionsappen och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på insamlade data.

Aktivera Application Insights för dina funktionsappar för att samla in logg-, prestanda- och feldata. Du kan visa telemetridata som samlas in av Application Insights i Azure Portal.

Om du har inbyggd anpassad säkerhets-/granskningsloggning i funktionsappen aktiverar du diagnostikinställningen "FunctionAppLogs" och skickar loggarna till en Log Analytics-arbetsyta, En Azure-händelsehubb eller ett Azure Storage-konto för arkivering. 

Alternativt kan du aktivera och ta med data för att Azure Sentinel eller en systeminformations- och händelsehanteringslösning från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Functions](functions-monitor-log-analytics.md)

- [Konfigurera en Azure Functions med Azure Application Insights och visa telemetridata](functions-monitoring.md)

- [Registrera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetsloggen samt diagnostikinställningarna för din funktionsapp och skicka loggarna till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på insamlade data. Du kan skapa aviseringar baserat på dina Log Analytics-arbetsytefrågor.

Aktivera Application Insights för dina funktionsappar för att samla in logg-, prestanda- och feldata. Du kan visa telemetridata som samlas in av Application Insights och skapa aviseringar i Azure Portal.

Du kan också aktivera och ta med data för att Azure Sentinel eller en systeminformation och händelsehanteringslösning från tredje part.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure Functions](functions-monitor-log-analytics.md)

- [Så här aktiverar du Application Insights för Azure Functions](./configure-monitoring.md#enable-application-insights-integration)

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

**Vägledning:** Kontrollplansåtkomst till Azure Functions styrs via Azure Active Directory (Azure AD). Azure AD har inte begreppet standardlösenord.

Åtkomst till dataplanet kan styras på flera sätt, inklusive auktoriseringsnycklar, nätverksbegränsningar och validering av en Azure AD-identitet. Auktoriseringsnycklar används av klienter som ansluter till Azure Functions HTTP-slutpunkter och kan återskapas när som helst. De här nycklarna genereras för nya HTTP-slutpunkter som standard.

Flera distributionsmetoder är tillgängliga för funktionsappar, varav vissa kan använda en uppsättning genererade autentiseringsuppgifter. Granska de distributionsmetoder som ska användas för ditt program.

- [Skydda en HTTP-slutpunkt](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Så här hämtar och återskapar du auktoriseringsnycklar](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Distributionstekniker i Azure Functions](functions-deployment-technologies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla reda på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare tilldelad till din prenumeration

- Inaktuella konton med ägarbehörighet ska tas bort från prenumerationen

- Externa konton med ägarbehörighet ska tas bort från din prenumeration

Ytterligare information finns på de refererade länkarna.

- [Så här använder du Azure Security Center för att övervaka identitet och åtkomst ](../security-center/security-center-identity-access.md)

- [Så här använder du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory enkel inloggning (SSO)

**Vägledning:** Använd om möjligt Azure Active Directory enkel inloggning (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter för dataåtkomst till funktionsappen. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering. Implementera enkel inloggning för dina funktionsappar med hjälp av funktionen App Service autentisering/auktorisering.

- [Förstå autentisering och auktorisering i Azure Functions](../app-service/overview-authentication-authorization.md#identity-providers)

- [Förstå enkel inloggning med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ rekommendationer Azure Security Center identitets- och åtkomsthantering.

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

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd dessutom Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

- [Så här distribuerar du Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå Azure AD-riskidentifiering](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att tillåta åtkomst till Azure Portal endast från specifika logiska grupper av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering för dina funktionsappar. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring. Azure AD saltar, hashar och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Så här konfigurerar du din funktionsapp för att använda Azure AD-inloggning](../app-service/configure-authentication-provider-aad.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Azure Active Directory (Azure AD) innehåller loggar som hjälper dig att identifiera inaktuella konton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD-rapportering](../active-directory/reports-monitoring/index.yml)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för dina funktionsappar. Azure AD skyddar data med hjälp av stark kryptering för vilodata och under överföring. Azure AD lagrar också salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggningsaktivitet, gransknings- och riskhändelseloggkällor, vilket gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logga aviseringar i Log Analytics.

- [Så här konfigurerar du din funktionsapp för att använda Azure AD-inloggning](../app-service/configure-authentication-provider-aad.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontobeteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för dina funktionsappar. För avvikelse i kontoinloggningsbeteende på kontrollplanet (Azure Portal) använder du Azure AD Identity Protection och riskidentifieringsfunktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

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

**Vägledning:** Implementera separata prenumerationer och/eller hanteringsgrupper för utveckling, testning och produktion. Funktionsappar ska avgränsas med virtuella nätverk (VNet)/undernät och taggas på lämpligt sätt.

Du kan också använda privata slutpunkter för att utföra nätverksisolering. En privat Azure-slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst (till exempel funktionsappens HTTPs-slutpunkt) som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet. Du kan använda privata slutpunkter för dina funktioner som finns i premium- och App Service planer.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Nätverksalternativ för Azure Functions](functions-networking-options.md)

- [Azure Functions Premium-plan](functions-premium-plan.md)

- [Förstå privat slutpunkt](../private-link/private-endpoint-overview.md)

- [Använda privata slutpunkter för Azure Functions](../app-service/networking/private-endpoint.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** I Azure Portal för dina funktionsappar, under "Plattformsfunktioner: Nätverk: SSL", aktiverar du inställningen "Endast HTTPs" och anger den lägsta TLS-versionen till 1.2.

- [Kräv HTTPS för funktionsappar](./security-concepts.md#require-https)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Inte tillgängligt för närvarande; funktioner för dataidentifiering, klassificering och förlustskydd är för närvarande inte tillgängliga för Azure Functions. Tagga funktionsappar som kan bearbeta känslig information som sådan och implementera tredjepartslösningar om det behövs i efterlevnadssyfte.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kundinnehåll som känsligt och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Använda rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att hantera åtkomsten till funktionsappens kontrollplan (Azure Portal).

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Använd värdbaserad dataförlustskydd för att framtvinga åtkomstkontroll

**Vägledning:** Ej tillämpligt; den här rekommendationen är avsedd för IaaS-beräkningsresurser.

Microsoft hanterar den underliggande infrastrukturen för Azure Functions och har implementerat strikta kontroller för att förhindra förlust eller exponering av kunddata.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** När du skapar en funktionsapp måste du skapa eller länka till ett allmänt Azure Storage som stöder blob-, kö- och tabellagring. Det beror på att Functions Azure Storage för åtgärder som att hantera utlösare och logga funktionskörningar. Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kund hanterade nycklar för kryptering av blob- och fildata. Dessa nycklar måste finnas i Azure Key Vault för att funktionsappen ska kunna komma åt lagringskontot.

- [Förstå lagringsöverväganden för Azure Functions](storage-considerations.md)

- [Förstå Azure Storage-kryptering för vilodata](../storage/common/storage-service-encryption.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i produktionsfunktionsappar samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Anta en DevSecOps-praxis för att säkerställa att dina funktionsappar är säkra och förblir så säkra som möjligt under hela livscykeln. DevSecOps införlivar din organisations säkerhetsteam och deras funktioner i dina DevOps-metoder, vilket gör säkerheten till ett ansvar för alla i teamet.

Följ dessutom rekommendationer från Azure Security Center för att skydda dina funktionsappar.

- [Så här lägger du till kontinuerlig säkerhetsverifiering i CI/CD-pipelinen](/azure/devops/migrate/security-validation-cicd-pipeline?preserve-view=true&view=azure-devops)

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder vid identifierade säkerhetsrisker

**Vägledning:** Microsoft utför sårbarhetshantering på de underliggande system som stöder Azure Functions, men du kan använda allvarlighetsgraden för rekommendationerna i Azure Security Center samt Säkerhetspoäng för att mäta risker i din miljö. Din säkerhetspoäng baseras på hur många Security Center du har minimerat. Om du vill prioritera rekommendationerna att lösa först bör du överväga allvarlighetsgraden för var och en.

- [Referensguide för säkerhetsrekommendationer](../security-center/recommendations-reference.md)

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

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra Azure-resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Ytterligare information finns på de refererade länkarna.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Definiera godkända Azure-resurser och godkänd programvara för beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer. 

Använd Azure Resource Graph att fråga efter/identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön godkänns. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationerna med hjälp av följande inbyggda principdefinitioner:

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

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för funktionsappen med Azure Policy. Använd Azure Policy alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina functions-appar. Du kan också använda inbyggda principdefinitioner som:

- Hanterad identitet ska användas i funktionsappen

- Fjärrfelsökning ska vara inaktiverat för funktionsappar

- funktionsappen ska endast vara tillgänglig via HTTPS

Ytterligare information finns på de refererade länkarna.

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

**Vägledning:** Lagra och hantera ARM-mallar och anpassade Azure-principdefinitioner på ett säkert sätt i källkontrollen.

- [Vad är infrastruktur som kod?](/azure/devops/learn/what-is-infrastructure-as-code)

- [Utforma princip som kodarbetsflöden](../governance/policy/concepts/policy-as-code.md)

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla dessutom en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Använd Azure Policy [audit], [deny] och [deploy if not exist] för att automatiskt framtvinga konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använd hanterade identiteter tillsammans med Azure Key Vault för att förenkla och skydda hemlighetshantering för dina molnprogram. Med hanterade identiteter kan funktionsappen autentisera mot alla tjänster som stöder Azure Active Directory-autentisering (Azure AD), inklusive Key Vault, utan autentiseringsuppgifter i koden.

- [Så här skapar du en Key Vault](../key-vault/secrets/quick-create-portal.md)

- [Använda hanterade identiteter för App Service och Azure Functions](../app-service/overview-managed-identity.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Tilldela en Key Vault åtkomstprincip](../key-vault/general/assign-access-policy-portal.md)

- [Använd Key Vault referenser för App Service och Azure Functions](../app-service/app-service-key-vault-references.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använda hanterade identiteter för att ge funktionsappen en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan några autentiseringsuppgifter i koden.

- [Använda hanterade identiteter för App Service och Azure Functions](../app-service/overview-managed-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 7.12](../../includes/policy/standards/asb/rp-controls/microsoft.web-7-12.md)]

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Använd funktionen Säkerhetskopiering och återställning för att schemalägga regelbundna säkerhetskopieringar av din app. Funktionsappar som körs i Premium-planen har samma värdfunktioner som webbappar i Azure App Service, vilket omfattar funktionen "Säkerhetskopiering och återställning".

Använd också en källkontrolllösning som Azure Repos och Azure DevOps för att lagra och hantera din kod på ett säkert sätt. Azure DevOps Services utnyttjar många av funktionerna i Azure Storage för att säkerställa att data är tillgängliga i händelse av maskinvaruproblem, avbrott i tjänsten eller haveri. Azure DevOps-teamet följer dessutom procedurer för att skydda data från oavsiktlig eller skadlig borttagning.

- [Säkerhetskopiera din app i Azure](../app-service/manage-backup.md)

- [Förstå datatillgänglighet i Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Använd funktionen Säkerhetskopiering och återställning för att schemalägga regelbundna säkerhetskopieringar av din app. Funktionsappar som körs i Premium-planen har samma värdfunktioner som webbappar i Azure App Service, vilket omfattar funktionen "Säkerhetskopiering och återställning". Säkerhetskopiera kund hanterade nycklar inom Azure Key Vault.

Använd också en källkontrolllösning som Azure Repos och Azure DevOps för att lagra och hantera din kod på ett säkert sätt. Azure DevOps Services utnyttjar många av funktionerna i Azure Storage för att säkerställa att data är tillgängliga i händelse av maskinvaruproblem, avbrott i tjänsten eller haveri. Azure DevOps-teamet följer dessutom procedurer för att skydda data från oavsiktlig eller skadlig borttagning.

- [Säkerhetskopiera din app i Azure](../app-service/manage-backup.md)

- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Förstå datatillgänglighet i Azure DevOps](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Dokumentation om Azure Repos](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Säkerställ möjligheten att regelbundet utföra återställning från funktionen Säkerhetskopiering och återställning. Om du använder en annan offlineplats för att säkerhetskopiera din kod ska du regelbundet se till att du kan utföra fullständiga återställningar. Testa återställning av säkerhetskopierade kund hanterade nycklar.

- [Återställa en app i Azure från en säkerhetskopia](../app-service/web-sites-restore.md)

- [Återställa en app i Azure från en ögonblicksbild](../app-service/app-service-web-restore-snapshots.md)

- [Så här återställer du nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Säkerhetskopieringar från funktionen Säkerhetskopiering och återställning använder ett Azure Storage konto i din prenumeration. Azure Storage krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kund hanterade nycklar för kryptering av lagringsdata.

Om du använder kund-hanterade nycklar, se till Soft-Delete i Key Vault är aktiverat för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Azure Storage-kryptering i vila](../storage/common/storage-service-encryption.md)

- [Så här aktiverar du Soft-Delete i Key Vault](../storage/blobs/soft-delete-blob-overview.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Så här konfigurerar du arbetsflödesautomation inom Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [NIST:s guide för hantering av säkerhetsincidenter](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för bedömning och prioritering av incidenter

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är vid upptäckten eller analysmetoden som användes för att utfärda aviseringen samt konfidensnivån för att det fanns en skadlig avsikt bakom aktiviteten som ledde till aviseringen.

Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincidenter används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig part.  Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidenter

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar på säkerhetsaviseringar och rekommendationer med Logic Apps.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Prestandatest för Azure-säkerhet: Intrångstester och Red Team-övningar.](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstestning på live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
