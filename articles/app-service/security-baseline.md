---
title: Azure-säkerhetsbaslinje för App Service
description: Säkerhetsbaslinjen App Service procedurvägledning och resurser för implementering av säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark, devx-track-azurepowershell
ms.openlocfilehash: d3e9d7d8cd8e5ffad74b02179994e299a59c8894
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833948"
---
# <a name="azure-security-baseline-for-app-service"></a>Azure-säkerhetsbaslinje för App Service

Azures säkerhetsbaslinje för App Service innehåller rekommendationer som hjälper dig att förbättra säkerhetspositionen för distributionen. Baslinjen för den här tjänsten hämtas från [Azure Security Benchmark version 1.0,](../security/benchmarks/overview-v1.md)som ger rekommendationer om hur du kan skydda dina molnlösningar i Azure med våra metodtips. Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för App Service. **Kontroller** som inte är App Service har undantagits.

Information om hur App Service mappning helt till Azure Security Benchmark finns i den fullständiga App Service [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** När du använder App Service på prisnivån Isolerad, även kallad en App Service-miljön (ASE) kan du distribuera direkt till ett undernät i azure-Virtual Network. Använd nätverkssäkerhetsgrupper för att skydda Azure App Service-miljön genom att blockera inkommande och utgående trafik till resurser i ditt virtuella nätverk eller begränsa åtkomsten till appar i en App Service-miljön.

Som standard innehåller nätverkssäkerhetsgrupper en implicit neka-regel med lägst prioritet och kräver att du lägger till explicita tillåt-regler. Lägg till tillåt-regler för nätverkssäkerhetsgruppen baserat på en nätverksmetod med lägsta privilegier. De underliggande virtuella datorer som används som värd för App Service-miljön är inte direkt tillgängliga eftersom de finns i en Microsoft-hanterad prenumeration.

Skydda en App Service-miljön genom att dirigera trafik via en Web Application Firewall (WAF) aktiverad Azure Application Gateway. Använd tjänstslutpunkter tillsammans med Application Gateway för att skydda inkommande publiceringstrafik till din app.  

I flera klientorganisationer (App Service app som inte är på isolerad nivå) använder du nätverkssäkerhetsgrupper för att blockera utgående trafik från din app. Ge dina appar åtkomst till resurser i eller via en Virtual Network med funktionen Virtual Network integrering. Den här funktionen kan också användas för att blockera utgående trafik till offentliga adresser från appen. Virtual Network integration kan inte användas för att ge inkommande åtkomst till en app.  

Skydda inkommande trafik till din app med:
- Åtkomstbegränsningar – en serie regler för att tillåta eller neka som styr inkommande åtkomst
- Tjänstslutpunkter – kan neka inkommande trafik utanför angivna virtuella nätverk eller undernät
- Privata slutpunkter – gör din app exponerad för Virtual Network med en privat IP-adress. När de privata slutpunkterna är aktiverade i din app är den inte längre tillgänglig via Internet

När du Virtual Network integreringsfunktionen med virtuella nätverk i samma region ska du använda nätverkssäkerhetsgrupper och vägtabeller med användardefinierade vägar. Användardefinierade vägar kan placeras i integreringsundernätet för att skicka utgående trafik som avsett.  

Överväg att implementera Azure Firewall för att centralt skapa, framtvinga och logga principer för program- och nätverksanslutning i dina prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för virtuella nätverksresurser, vilket gör att externa brandväggar kan identifiera trafik som kommer från ditt virtuella nätverk. 

- [Låsa en App Service-miljön](environment/firewall-integration.md)

- [Open Web Application Security Project (OWASP) Skydd mot de 10 främsta säkerhetsriskerna](https://owasp.org/www-project-top-ten/)

- [Nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md)

- [Integrera en app med ett virtuellt Azure-nätverk](web-sites-integrate-with-vnet.md)

- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Så här skapar du en extern ASE](environment/create-external-ase.md)

- [Så här skapar du en intern ASE](environment/create-ilb-ase.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Implementera rekommendationer för nätverksskydd från Azure Security Center för att skydda nätverksresurser och konfigurationer relaterade till dina App Service och API:er.

Använd Azure Firewall för att skicka trafik och centralt skapa, framtvinga och logga principer för program- och nätverksanslutning mellan prenumerationer och virtuella nätverk. Azure Firewall använder en statisk offentlig IP-adress för dina virtuella nätverksresurser, vilket gör att externa brandväggar kan identifiera trafik som kommer från din Virtual Network. Tjänsten Azure Firewall är också helt integrerad med Azure Monitor för loggning och analys.

- [Azure Firewall översikt](../firewall/overview.md)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Aktivera övervakning och skydd av App Service](../security-center/defender-for-app-service-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Skydda en internettillgänglig app i en App Service-miljön (ASE) genom att:
- Distribuera en Web Application Firewall (WAF) med Azure Application Gateway framför en Internetuppriktad app
- Använd åtkomstbegränsningar för att skydda inkommande trafik till Application Gateway
- Skydda appen med Azure Active Directory (Azure AD) för att säkerställa autentisering
- Ange den lägsta TLS-versionen till 1.2
- Ange endast HTTPS för appen

Kör all utgående programtrafik via en Azure Firewall enhet och övervaka loggarna. 

För att skydda en app som är tillgänglig via Internet i App Service klientorganisation, (till exempel inte på den isolerade nivån)
- Distribuera en Web Application Firewall-aktiverad enhet framför en app
- Använd åtkomstbegränsningar eller tjänstslutpunkter för att skydda inkommande trafik till Web Application Firewall (WAF)-enheten
- Skydda appen med Azure AD för att säkerställa autentisering
- Ange den lägsta TLS-versionen till 1.2
- Ange endast HTTPS för appen
- Använd Integrering av virtuella nätverk och appinställningen för WEBSITE_VIRTUAL NETWORK_ROUTE_ALL att all utgående trafik ska omfattas av nätverkssäkerhetsgrupper och användardefinierade vägar i integreringsundernätet.

På samma sätt som med appen Application Service Environment kan du köra all utgående programtrafik Azure Firewall en enhet och övervaka loggarna i appen.

Granska och följ dessutom rekommendationerna i dokumentet Låsa ett App Service-miljön bort.

- [Låsa en App Service-miljön](environment/firewall-integration.md)

- [Azure Web Application Firewall på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service åtkomstbegränsningar](app-service-ip-restrictions.md)

- [Spåra WAF-aviseringar och övervaka enkelt trender med Azure Monitor ](../azure-monitor/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 1.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-1-3.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Skydda App Service-miljön enligt beskrivningen i dokumentationen låsa en App Service-miljön data. Använd funktionen Integrated Threat Intelligence i Azure Security Center att neka kommunikation med kända skadliga eller oanvända offentliga IP-adresser. Använd åtkomstbegränsningar för att skydda inkommande trafik till Application Gateway. 

Skydda flera klientorganisations-App Service (en app som inte är på en isolerad nivå) med en offentlig Internetuppkopplad slutpunkt. Den tillåter endast trafik från ett specifikt undernät inom Virtual Network och blockerar allt annat. Använd åtkomstbegränsningar för att konfigurera nätverksbegränsningar Access Control (IP-begränsningar) för att låsa tillåten inkommande trafik.

Definiera prioritet bland den sorterade listan tillåt eller neka för att hantera nätverksåtkomst till din app. Den här listan kan innehålla IP-adresser Virtual Network undernät. En implicit "neka alla"-regel finns i slutet av listan när den innehåller en eller flera poster. Den här funktionen fungerar med alla App Service värdindelade arbetsbelastningar, inklusive, Web Apps, API Apps, Linux-appar, Linux-containerappar och Functions. 

Använd tjänstslutpunkter för att begränsa åtkomsten till din webbapp från en Azure-Virtual Network. Begränsa åtkomsten till en App Service klientorganisation (en app som inte är på en isolerad nivå) från valda undernät med tjänstslutpunkter. 

- [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application Firewall på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Så här konfigurerar du Web Application Firewall (WAF) för App Service-miljön](environment/app-service-app-service-environment-web-application-firewall.md)

- [Skydda ASE enligt beskrivningen i Låsa en App Service-miljön](environment/firewall-integration.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Övervakar begäranden och svar som skickas till och från App Service med Security Center. Attacker mot ett webbprogram kan övervakas med hjälp av en realtids-Application Gateway som har Web Application Firewall, aktiverat med integrerad loggning från Azure Monitor för att spåra Web Application Firewall aviseringar och enkelt övervaka trender.

- [Azure Web Application Firewall på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Hantera trafik för en app i en App Service-miljön:

- Skydda App Service-miljön enligt beskrivningen i Låsa en App Service-miljön
- Distribuera en Application Gateway som har en Azure Web Application Firewall framför dina Internet-appar
- Ange att appen endast ska vara tillgänglig via HTTPS

Hantera trafik för en app som är tillgänglig via Internet i App Service klientorganisation (inte på den isolerade nivån): 

- Distribuera en Application Gateway som har Azure Web Application Firewall aktiverat framför dina Internet-appar
- Använd åtkomstbegränsningar eller tjänstslutpunkter för att skydda inkommande trafik till Web Application Firewall. Funktionen för åtkomstbegränsningar fungerar med alla App Service värdindelade arbetsbelastningar, inklusive Web Apps, API Apps, Linux-appar, Linux-containerappar och Functions.

- Ange att appen endast ska vara tillgänglig via HTTPS
- Begränsa åtkomsten till din App Service med statiska IP-begränsningar så att den bara tar emot trafik från VIP på en programgateway som den enda adressen med åtkomst.

Mer information finns i de refererade länkarna.

- [Statiska IP-begränsningar för Azure App Service](app-service-ip-restrictions.md)

- [Azure Web Application Firewall på Azure Application Gateway](../web-application-firewall/ag/ag-overview.md)

- [Så här konfigurerar du TLS från Application Gateway med portalen](../application-gateway/end-to-end-ssl-portal.md)

- [Skydda ASE enligt beskrivningen i Låsa en App Service](/azure/app-service/environment/firewall-integration)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** App Service har ett antal slutpunkter som används för att hantera tjänsten. Dessa slutpunktsadresser ingår också i IP-tjänsttaggen AppServiceManagement. Taggen AppServiceManagement används endast med en App Service-miljön för att tillåta sådan trafik. 

Du kan tillåta eller neka trafik för motsvarande tjänst genom att ange tjänsttaggnamnet i lämpligt käll- eller målfält för en regel. App Service inkommande adresser spåras i apptjänstens IP-tjänsttagg. Det finns ingen IP-tjänsttagg som innehåller de utgående adresser som används av App Service.

Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

- [Tjänsttaggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksinställningar relaterade till dina App Service appar. 

Underhålla säkerhetskonfigurationer med Azure Policy alias i namnrymderna "Microsoft.Web" och "Microsoft.Network". Skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina App Service appar. 

Använd inbyggda principdefinitioner för App Service, till exempel:
- Appen ska använda en tjänstslutpunkt för virtuellt nätverk
- Appen ska endast vara tillgänglig via HTTPS
- Ange den lägsta TLS-versionen till den aktuella versionen

Mer information finns i de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här konfigurerar du TLS från Application Gateway med portalen](../application-gateway/end-to-end-ssl-portal.md)

- [Skydda ASE enligt beskrivningen i Låsa en App Service](/azure/app-service/environment/firewall-integration)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper och andra relaterade resurser, inklusive trafikflöde i App Service.

Ange affärs behov, varaktighet och så vidare, med fältet "Beskrivning" för alla regler som tillåter trafik till eller från ett nätverk för regler för enskilda nätverkssäkerhetsgrupper.

Använd någon av de inbyggda Azure Policy-definitioner som rör taggningseffekter, till exempel "Kräv tagg och dess värde", för att säkerställa att alla resurser skapas med taggar och för att meddela dig om eventuella befintliga otaggade resurser. Använd Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

- [Azure App Service åtkomstbegränsningar](/azure/app-service/app-service-ip-restrictions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure-aktivitetsloggen för att övervaka nätverksresurskonfigurationer och identifiera ändringar i nätverksinställningar och alla resurser som rör App Service. 

Tillämpa en av de Azure Policy inbyggda definitionerna för App Service, till exempel en princip som granskar appar för användning av tjänst för slutpunkter för virtuellt nätverk. Skapa aviseringar inom Azure Monitor utlösas när ändringar av kritiska nätverksinställningar eller resurser sker. 

Granska detaljerade säkerhetsaviseringar och rekommendationer i Security Center, i portalen eller via programmatiska verktyg. Exportera den här informationen eller skicka den till andra övervakningsverktyg i din miljö. Det finns verktyg för att exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Med dessa verktyg kan du:
 
- Exportera kontinuerligt till en Log Analytics-arbetsyta
- Exportera kontinuerligt till Azure Event Hubs (för integreringar med SIEM från tredje part)
- Exportera till en CSV-fil (en gång)

Vi rekommenderar att du skapar en process med automatiserade verktyg för att övervaka nätverksresurskonfigurationer och snabbt identifiera ändringar.

- [Så här visar och hämtar du Händelser i Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Exportera säkerhetsaviseringar och rekommendationer](../security-center/continuous-export.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i Azure Security Benchmark: Logging and Monitoring ( [Prestandatest för Azure-säkerhet: Loggning och övervakning).](../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Integrera din App Service-miljön (ASE) med Azure Monitor för att skicka loggar till Azure Storage, Azure Event Hubs eller Log Analytics. Aktivera diagnostikinställningar för Azure-aktivitetslogg för kontrollplansgranskningsloggning. Säkerhetsaviseringar från Security Center publiceras i Azure-aktivitetsloggen. Granska Azure-aktivitetsloggdata, där du kan avgöra "vad, vem och när" för skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå för Azure App Service och andra Azure-resurser. Spara dina frågor för framtida användning, fäst frågeresultat på Azure-instrumentpaneler och skapa logga aviseringar. Du kan också använda dataåtkomsten REST API i Application Insights för att komma åt din telemetri programmatiskt.

Använd Microsoft Azure Sentinel, en skalbar, molnbaserad händelsehantering för säkerhetsinformation (SIEM) som är tillgänglig för att ansluta till olika datakällor och anslutningsappar, baserat på dina affärsbehov. Du kan också aktivera och ta med data till ett SIEM-system (Security Information Event Management) från tredje part, till exempel Barracuda i Azure Marketplace.

- [Logga ASE-aktivitet](./environment/using-an-ase.md#logging)

- [Så här aktiverar du diagnostikinställningar för Azure App Service](troubleshoot-diagnostic-logs.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Exportera telemetri från Application Insights](../azure-monitor/app/export-telemetry.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Aktivera diagnostikinställningar för Azure-aktivitetslogg för kontrollplansgranskningsloggning för App Service. Skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller ett Azure Storage konto.

"Vad, vem och när" för skrivåtgärder (PUT, POST, DELETE) som utförs på kontrollplansnivå kan fastställas med hjälp av Azure-aktivitetsloggdata för App Service och andra Azure-resurser.

Dessutom ger Azure Key Vault centraliserad hemlighetshantering med åtkomstprinciper och granskningshistorik. 

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du diagnostikinställningar för Azure App Service](troubleshoot-diagnostic-logs.md)

- [Resource Manager åtgärder](../role-based-access-control/resource-provider-operations.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.web-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** I Azure Monitor anger du loggens kvarhållningsperiod för Log Analytics-arbetsytor som är associerade med dina App Service-resurser enligt organisationens efterlevnadsregler.
- [Så här anger du parametrar för loggbevarande](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** Granska diagnostikinställningarna för Azure-aktivitetsloggen i dina App Service med loggarna som skickas till en Log Analytics-arbetsyta. Utför frågor i Log Analytics för att söka efter termer, identifiera trender, analysera mönster och ge många andra insikter baserat på insamlade data.

Använd Application Insights för dina App Service appar och för att samla in logg-, prestanda- och feldata. Visa telemetridata som samlas in av Application Insights i Azure Portal.

Om du har distribuerat en Web Application Firewall (WAF) kan du övervaka attacker mot dina App Service-appar med hjälp av en Web Application Firewall logg. Loggen är integrerad med Azure Monitor för att spåra Web Application Firewall aviseringar och enkelt övervaka trender.

Använd Azure Sentinel, en skalbar och molnbaserad säkerhetsinformationshändelsehantering (SIEM) för att integrera med olika datakällor och anslutningsappar, enligt kraven. Du kan också aktivera och ta med data till en händelsehanteringslösning för säkerhetsinformation från tredje part i Azure Marketplace.

- [Så här aktiverar du diagnostikinställningar för Azure-aktivitetsloggen](../azure-monitor/essentials/activity-log.md)

- [Så här aktiverar du Application Insights](../azure-monitor/app/app-insights-overview.md)

- [Så här integrerar du App Service-miljön med Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Så här Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** Konfigurera Security Center i din Azure-prenumeration och granska de genererade aviseringarna. Använd Azure Monitor för att hämta aktivitetsloggdata till en händelsehubb där de kan läsas av en SIEM-lösning (Security Information Event Management), till exempel Azure Sentinel. 

Övervaka attacker mot dina App Service-appar med hjälp av en Web Application Firewall logg med en distribuerad Azure Web Application Firewall (WAF). Loggen är integrerad med Azure Monitor för att spåra Web Application Firewall (WAF)-aviseringar och enkelt övervaka trender.

- [Så här integrerar du App Service-miljön med Azure Application Gateway (WAF)](environment/integrate-with-application-gateway.md)

- [Exportera säkerhetsaviseringar och rekommendationer](../security-center/continuous-export.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och kunna användas för frågor. Använd Azure AD PowerShell-modulen för att köra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Hämta medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Använda hanterade identiteter för App Service och Azure Functions](./overview-managed-identity.md?tabs=dotnet&context=azure%2factive-directory%2fmanaged-identities-azure-resources%2fcontext%2fmsi-context)

- [Tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord om tillämpligt

**Vägledning:** Azure Active Directory (Azure AD) har inte begreppet standardlösenord. Det ger kontrollplansåtkomst till App Service.

Undvik i allmänhet att implementera standardlösenord för användaråtkomst när du skapar egna appar. Använd en av de identitetsproviders som är tillgängliga som standard för App Service, till exempel Azure AD, Microsoft-konto, Facebook, Google eller Twitter.

Inaktivera anonym åtkomst, såvida du inte behöver stödja det. 

- [Identitetsproviders är tillgängliga som Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Autentisering och auktorisering i Azure App Service och Azure Functions](overview-authentication-authorization.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton. Använd funktionerna identitets- och åtkomsthantering i Security Center för att övervaka och spåra antalet administrativa konton. 

Använd rekommendationer från Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare tilldelad till din prenumeration. 
- Inaktuella konton med ägarbehörighet ska tas bort från prenumerationen
- Externa konton med ägarbehörighet ska tas bort från din prenumeration

Skapa en process för att övervaka nätverksresurskonfigurationer och identifiera ändringar i administrativa konton.

- [Så här använder du Azure Security Center för att övervaka identitet och åtkomst](../security-center/security-center-identity-access.md)

- [Så här använder du Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Läs mer om att bevilja användare åtkomst till program](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning:** Autentisera App Service via Azure Active Directory (Azure AD). Den tillhandahåller en OAuth 2.0-tjänst för din identitetsprovider och ger auktoriserad åtkomst till mobil- och webbprogram. 

App Service-appar använder federerad identitet, där en tredjeparts-identitetsprovider hanterar användaridentiteter och autentiseringsflödet åt dig. Dessa identitetsproviders är tillgängliga som standard:

- Azure AD
- Microsoft-konto

- Facebook

- Google

- Twitter

När du aktiverar autentisering och auktorisering med någon av dessa providers är inloggningsslutpunkten tillgänglig för användarautentisering och för validering av autentiseringstoken från providern.

- [Förstå autentisering och auktorisering i Azure App Service](./overview-authentication-authorization.md#identity-providers)

- [Läs mer om autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera funktionen för multifaktorautentisering i Azure Active Directory (Azure AD) och följ rekommendationerna för identitets- och åtkomsthantering i Security Center.

Implementera multifaktorautentisering för Azure AD. Administratörer måste se till att prenumerationskontona i portalen är skyddade. Prenumerationen är sårbar för attacker eftersom den hanterar de resurser som du har skapat. 

- [Multifaktorautentisering för Azure-säkerhet](/previous-versions/azure/security/develop/secure-aad-app)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använda PAW (Privileged Access Workstations) med multifaktorautentisering som konfigurerats för att logga in på och konfigurera Azure-resurser. 

- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Använd Privileged Identity Management (PIM) i Azure Active Directory (Azure AD) för generering av loggar och aviseringar när misstänkta eller osäkra aktiviteter inträffar i miljön.

Använd dessutom Azure AD-riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

Skydd mot hot i Security Center ger omfattande skydd för din miljö, vilket omfattar hotskydd för Azure-beräkningsresurser som Windows-datorer, Linux-datorer, App Service och Azure-containrar.

- [Så här distribuerar du Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå Azure AD-riskidentifiering](../active-directory/identity-protection/overview-identity-protection.md)

- [Hotskydd för Azure-beräkningsresurser](../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att tillåta åtkomst till Azure Portal endast från specifika logiska grupper av IP-adressintervall, länder eller regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för dina App Service appar. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Så här konfigurerar du dina Azure App Service att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Identifiera inaktuella konton med loggarna från Azure Active Directory (Azure AD). Använd Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram, samt rolltilldelningar. Granska användaråtkomsten regelbundet för att se till att endast de avsedda användarna har fortsatt åtkomst. 

- [Förstå Azure AD-rapportering](../active-directory/reports-monitoring/index.yml)

- [Så här använder du Azure Identity Access Reviews](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt autentiserings- och auktoriseringssystem för dina App Service appar. Azure AD skyddar data med hjälp av stark kryptering för data i vila och under överföring, salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Med åtkomst till azure AD-inloggningsaktiviteter, gransknings- och riskhändelseloggkällor kan du integrera med Azure Sentinel eller en SIEM-lösning (Security Information Event Management) från tredje part. Effektivisera processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings- och inloggningsloggarna till en Log Analytics-arbetsyta. Önskade logga aviseringar kan konfigureras i Log Analytics.

- [Så här konfigurerar du Azure App Service för att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Så här Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontots beteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering för App Service appar. 

Använd Azure AD Identity Protection för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användaridentiteter, till exempel beteendeavvikelse för kontoinloggning på kontrollplanet med Azure Portal. Du kan också mata in data i Azure Sentinel för ytterligare undersökning. 

- [Så här konfigurerar du din Azure App Service att använda Azure AD-inloggning](configure-authentication-provider-aad.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** Inte tillgängligt; Customer Lockbox stöds inte för Azure App Service.

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra App Service som lagrar eller bearbetar känslig information.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** För en App Service-miljön du implementera separata prenumerationer, hanteringsgrupper eller båda för utvecklings-, test- och produktionsmiljöer. Du kan isolera appar som bearbetar känslig information från andra appar på samma sätt. Distribuera din App Service till en Virtual Network. Använd nätverkssäkerhetsgrupper och undernät för ytterligare programisolering. 

Det finns två distributionstyper för en App Service miljö (ASE). Båda gör att du kan isolera trafiken baserat på dina affärsbehov.

- Extern programtjänstmiljö – Exponerar App Service-miljön värdbaserade appar på en INTERNET-tillgänglig IP-adress.

-  intern lastbalanserare (ILB) Application Service Environment – Exponerar de App Service-miljön värdbaserade apparna på en IP-adress i Virtual Network. Den interna slutpunkten är en intern lastbalanserare (ILB), vilket är anledningen till att den kallas för en ILB ASE. 

För flera klientorganisation App Service sklienter (en app som inte är på nivån Isolerad) använder du Virtual Network-integrering för appens åtkomst till resurser i ditt virtuella nätverk.  Använd åtkomst till privat webbplats för att göra en app tillgänglig endast från ett privat nätverk, till exempel en från ett virtuellt Azure-nätverk. Virtual Network-integrering används endast för att göra utgående anrop från din app till Virtual Network. Funktionen Virtual Network integration fungerar annorlunda när den används med ett virtuellt nätverk i samma region och med virtuella nätverk i andra regioner. 
 
- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Så här skapar du en extern ASE](environment/create-external-ase.md)

- [Så här skapar du en intern ASE](environment/create-ilb-ase.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** Även om funktioner för dataidentifiering, klassificering och förlustskydd ännu inte är tillgängliga för App Service kan du minska risken för data exfiltrering från det virtuella nätverket genom att ta bort alla regler där målet använder en "tagg" för Internet- eller Azure-tjänster. 

Microsoft hanterar den underliggande infrastrukturen för App Service och har implementerat strikta kontroller för att förhindra förlust eller exponering av dina data.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Använd den lägsta standardversionen av TLS 1.2, konfigurerad i TLS/SSL-inställningar, för kryptering av all information under överföring. Kontrollera också att alla HTTP-anslutningsbegäranden omdirigeras till HTTPS.

- [Förstå kryptering under överföring Azure App Service webbappar](security-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Web**:

[!INCLUDE [Resource Policy for Microsoft.Web 4.4](../../includes/policy/standards/asb/rp-controls/microsoft.web-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** För närvarande inte tillgänglig. Funktioner för dataidentifiering, klassificering och förlustskydd är ännu inte tillgängliga för App Service. 

Tagga App Service appar som kan bearbeta känslig information. Implementera en lösning från tredje part, om det behövs för efterlevnadsändamål.

Microsoft hanterar den underliggande plattformen och behandlar alla kunddata som känsliga och tar lång tid att skydda mot förlust och exponering av kunddata. För att säkerställa att kunddata i Azure förblir säkra har Microsoft implementerat och upprätthåller en uppsättning robusta dataskyddskontroller och -funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Använd Azure RBAC för att styra åtkomsten till resurser 

**Vägledning:** Använd rollbaserad åtkomstkontroll i Azure (Azure RBAC) i Azure Active Directory (Azure AD) för att styra åtkomsten till App Service-kontrollplanet på Azure Portal.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Webbplatsinnehåll i en App Service-app, till exempel filer, lagras i Azure Storage, vilket automatiskt krypterar innehållet i vila. Välj att lagra programhemligheter Key Vault och hämta dem vid körning.

De hemligheter som kunden har angett krypteras i vila medan de lagras App Service konfigurationsdatabaser.

Observera att även om lokalt anslutna diskar kan användas av webbplatser som tillfällig lagring kan de (till exempel D:\local och %TMP %) inte krypteras i vila.

- [Förstå dataskyddskontroller för Azure App Service](./security-recommendations.md#data-protection)

- [Förstå Azure Storage kryptering i vila](../storage/common/storage-service-encryption.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar om ändringar i App Service-appar och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Händelser i Azure-aktivitetsloggen](../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Kör automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Anta en DevSecOps-praxis för att säkerställa att dina App Service-appar är säkra och förblir skyddade under hela livscykeln. DevSecOps införlivar din organisations säkerhetsteam och deras funktioner i dina DevOps-metoder, vilket gör att alla i teamet ansvarar för säkerheten.

Granska och följ rekommendationerna från Security Center för att skydda dina App Service appar.

- [Så här lägger du till kontinuerlig säkerhetsverifiering i CI/CD-pipelinen](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&preserve-view=true)

- [Så här implementerar du Azure Security Center rekommendationer för sårbarhetsbedömning](../security-center/deploy-vulnerability-assessment-vm.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder vid identifierade säkerhetsrisker

**Vägledning:** Microsoft utför sårbarhetshantering på de underliggande system som stöder App Service. Du kan dock använda allvarlighetsgraden för rekommendationerna i Security Center samt Säkerhetspoäng för att mäta risker i din miljö. Din säkerhetspoäng baseras på hur många Security Center du har minimerat. Om du vill prioritera rekommendationerna att lösa först bör du överväga allvarlighetsgraden för var och en.

- [Referensguide för säkerhetsrekommendationer](../security-center/recommendations-reference.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Benchmark för Azure-säkerhet: Inventering och tillgångshantering.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph att fråga efter eller identifiera alla resurser (till exempel beräkning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer. Se till att lämpliga behörigheter tillämpas på din klientorganisation och du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan Resource Graph via en Resource Graph rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser i framtiden.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på Azure-resurser med hjälp av metadata för att logiskt ordna dem i en taxonomi.

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer efter behov för att organisera och spåra Azure-resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från dina prenumerationer som en del av den här processen.

Välj Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Mer information finns i de refererade länkarna.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Skapa en förteckning över godkända Azure-resurser och godkänd programvara för beräkningsresurser baserat på organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph för att fråga efter eller identifiera resurser i sina prenumerationer.  Se till att alla Azure-resurser som finns i miljön godkänns. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Skapa frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Övervaka program som inte är godkända i beräkningsresurser

**Vägledning:** Använd Azure Resource Graph att köra frågor mot eller identifiera resurser i dina prenumerationer och se till att de identifierade Azure-resurserna godkänns baserat på organisationens principer.

Använd WebJobs i App Service för att övervaka program som inte är godkända och som distribueras inom beräkningsresurser. Använd WebJobs för att köra ett program eller skript i samma instans som en webbapp, en API-app eller en mobilapp. Definiera webbjobbskonfigurationer och övervakning med loggar. På sidan Körningsinformation för webbjobb väljer du Växla utdata för att se texten i logginnehållet. Observera att WebJobs ännu inte stöds för App Service på Linux.

- [Köra bakgrundsaktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabbstart – Kör din första fråga Resource Graph med hjälp av Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Se till att alla Azure-resurser som finns i miljön godkänns. Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer. Ta bort alla distribuerade program som inte har godkänts enligt organisationens principer.

- [Köra bakgrundsaktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabbstart – Kör din första fråga Resource Graph med hjälp av Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="68-use-only-approved-applications"></a>6.8: Använd endast godkända program

**Vägledning:** Se till att alla Azure-resurser som finns i miljön godkänns. Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer. Ta bort alla distribuerade program som inte har godkänts enligt organisationens principer. 

- [Köra bakgrundsaktiviteter med WebJobs i Azure App Service](webjobs-create.md)

- [Självstudie – Skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Snabbstart – Kör din första Resource Graph med hjälp av Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Skapa en process för att regelbundet granska obehöriga Azure-tjänster för att se till att endast auktoriserade Azure-tjänster används i dina prenumerationer.

Använd Azure Resource Graph i den här processen för att fråga efter eller identifiera resurser i sina prenumerationer. Se till att alla Azure-resurser som finns i miljön godkänns.

Konfigurera Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i dina prenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd WebJobs i App Service för att övervaka program som inte är godkända och som distribuerats i datorresurser. Använd WebJobs för att köra ett program eller skript i samma instans som en webbapp, API-app eller mobilapp. Definiera webbjobbskonfigurationer och övervakning med loggar. På sidan Information om webbjobbskörning väljer du Växla utdata för att se texten i logginnehållet. Observera att WebJobs ännu inte stöds för App Service på Linux.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Köra bakgrundsaktiviteter med WebJobs i Azure App Service](webjobs-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Upprätthålla en förteckning över godkända programvarutitlar

**Vägledning:** Implementera en process för att inventera och granska programvarutitlar i dina prenumerationer regelbundet för att se till att endast auktoriserade Azure-tjänster används i dina prenumerationer.

Använd Azure Resource Graph i den här processen för att köra frågor mot eller identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som identifieras i miljön godkänns.

Konfigurera Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

På samma sätt kan du använda webbjobb i App Service för att inventera program som inte är godkända och som distribuerats i datorresurser. Definiera deras konfiguration och övervakning med loggar. På sidan Körningsinformation för webbjobb väljer du Växla utdata för att se texten i logginnehållet. Observera att WebJobs ännu inte stöds för App Service på Linux.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Konfigurera villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Begränsa användarnas möjlighet att köra skript inom beräkningsresurser

**Vägledning:** Webbjobb i App Service kunder kan köra ett program eller skript i samma instans som en webbapp, en API-app eller en mobilapp. Du ansvarar för att definiera konfigurationen för att begränsa eller begränsa skript som inte tillåts av organisationen. App Service inte en mekanism för att begränsa skriptkörningen inbyggt. Observera att WebJobs ännu inte stöds för App Service på Linux.

- [Köra bakgrundsaktiviteter med WebJobs i Azure App Service](webjobs-create.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fysiskt eller logiskt avsegrera högriskprogram

**Vägledning:** Implementera separata prenumerationer eller hanteringsgrupper för att ge isolering för hög risk App Service appar. Distribuera en app med högre risk till en Virtual Network app, eftersom perimetersäkerhet i App Service uppnås genom användning av virtuella nätverk. Den App Service-miljön är en distribution av App Service till ett undernät i azure-Virtual Network. 

Det finns två typer av Programtjänstmiljö, Extern programtjänstmiljö och ILB(Internal Load Balancer) Application Service Environment. Välj den bästa arkitekturen baserat på dina krav.

- [Nätverksöverväganden för en App Service-miljö](environment/network-info.md)

- [Skapa en extern App Service miljö](environment/create-external-ase.md)

- [Skapa och använda en intern Load Balancer App Service-miljön](environment/create-ilb-ase.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för dina App Service distribuerade appar med Azure Policy.

Använd Azure Policy alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av App Service Web Apps.

Använd inbyggda principdefinitioner som:

- App Service bör använda en tjänstslutpunkt för virtuellt nätverk

- Webbprogram bör endast vara tillgängliga via HTTPS

- Använda den senaste TLS-versionen i dina appar

Vi rekommenderar att du dokumenterar processen för att tillämpa de inbyggda principdefinitionerna för standardiserad användning.   

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Underhålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] effekter för att tillämpa säkra inställningar i Azure App Service appar.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policy effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Välj Azure DevOps eller Azure Repos för att lagra och hantera din kod på ett säkert sätt när du använder anpassade Azure Policy definitioner.

Använd din befintliga pipeline för kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för att distribuera en känd säker konfiguration.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Dokumentation om Azure Repos](/azure/devops/repos/?view=azure-devops&preserve-view=true)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. Utveckla en process och pipeline för hantering av principundantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatiserad konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd inbyggda Azure Policy-definitioner samt Azure Policy-alias i namnområdet "Microsoft.Web" för att skapa anpassade principer för att varna, granska och framtvinga systemkonfigurationer. 

Tillämpa Azure Policy [audit], [neka] och [distribuera om det inte finns], effekter för att automatiskt framtvinga konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Använda hanterade identiteter för att ge dina App Service-appar en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan dina appar autentisera mot alla tjänster som stöder Azure AD-autentisering, Key Vault, utan autentiseringsuppgifter i koden. Se till att mjuk borttagning är aktiverat i Azure Key Vault.

- [Så här aktiverar du mjuk borttagning i Azure Key Vault](../key-vault/general/key-vault-recovery.md)

- [Använda hanterade identiteter för App Service](overview-managed-identity.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Använd hanterade identiteter för att App Service distribuerade appar med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan dina appar autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan autentiseringsuppgifter i koden.

- [Använda hanterade identiteter för App Service](overview-managed-identity.md)

- [Så här tillhandahåller du Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md)

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

**Vägledning:** Med funktionen Säkerhetskopiering och återställning i App Service du enkelt skapa appsäkerhetskopior manuellt eller enligt ett schema. Du kan konfigurera säkerhetskopiorna så att de bevaras upp till obestämd tid. Du kan återställa appen till en ögonblicksbild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app.

App Service kan konfigurerar din app för att använda följande information till ett Azure-lagringskonto och en container:
- Appkonfiguration
- Filinnehåll
- Databas ansluten till din app

Se till att regelbundna och automatiserade säkerhetskopieringar sker med en frekvens som definieras av organisationens principer.

- [Förstå Azure App Service funktioner för säkerhetskopiering](manage-backup.md)

- [Kund-hanterade nycklar för Azure Storage kryptering](../storage/common/customer-managed-keys-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** Använd funktionen för säkerhetskopiering och återställning App Service att säkerhetskopiera dina program. Säkerhetskopieringsfunktionerna kräver ett Azure Storage-konto för att lagra programmets säkerhetskopieringsinformation.

- Azure Storage kryptering i vila – Använd systemtilldelade nycklar eller dina egna, kund hanterade nycklar. Det är här dina programdata lagras när de inte körs i en webbapp i Azure.
- Att köra från ett distributionspaket är en distributionsfunktion i App Service. Det gör att du kan distribuera ditt webbplatsinnehåll från ett Azure Storage-konto med hjälp av en SAS-URL (signatur för delad åtkomst).

- Key Vault referenser är en säkerhetsfunktion i App Service. Det gör att du kan importera hemligheter vid körning som programinställningar. Använd detta för att kryptera SAS-URL:en för ditt Azure Storage konto.

Mer information finns på de refererade länkarna.

- [Säkerhetskopiera din app i Azure](manage-backup.md)

- [Återställa en app som körs i Azure App Service](web-sites-restore.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [Krypteringsmodell och nyckelhanteringstabell](../security/fundamentals/encryption-atrest.md)

- [Kryptering i vila med kund hanterade nycklar](configure-encrypt-at-rest-using-cmk.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Testa regelbundet återställningsprocessen för säkerhetskopior av dina App Service program.

- [Säkerhetskopiera din app i Azure](manage-backup.md)

- [Återställa en Azure App Service webbapp](web-sites-restore.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** App Service säkerhetskopior lagras i ett Azure Storage konto. Data i Azure Storage krypteras och dekrypteras transparent med hjälp av 256-bitars AES-kryptering, ett av de starkaste blockchiffreringarna som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering i Windows.

Azure Storage är aktiverat för alla lagringskonton, inklusive både Resource Manager och klassiska lagringskonton. Azure Storage kryptering kan inte inaktiveras. Eftersom dina data skyddas som standard behöver du inte ändra din kod eller dina program för att dra nytta av Azure Storage kryptering.

Som standard krypteras data i ett lagringskonto med Microsoft-hanterade nycklar. Du kan använda Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Se till att mjuk borttagning är aktiverat i Azure Key Vault.

- [Förstå Azure Storage kryptering för vilodata](../storage/common/storage-service-encryption.md)

- [Aktivera mjuk borttagning i Azure Key Vault](../key-vault/general/key-vault-recovery.md)

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

Markera dessutom prenumerationer tydligt (till exempel produktion, icke-produktion) och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets funktioner för incidenter. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST:s publikation - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktinformation om säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har använts av en obehörig eller obehörig part.  Granska incidenter efter fakta för att se till att problemen är lösta.

- [Så här anger du Azure Security Center säkerhetskontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Använd anslutningsappen Security Center data för att strömma aviseringarna till Azure Sentinel efter affärsbehov.

- [Så här konfigurerar du löpande export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer.

- [Så här konfigurerar du arbetsflödesautomation och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsoft Cloud Penetration Testing Rules of Engagement för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)
