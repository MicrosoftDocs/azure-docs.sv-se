---
title: Azure-säkerhetsbaslinje för Azure Storage
description: Säkerhetsbaslinjen Azure Storage innehåller procedurvägledning och resurser för att implementera säkerhetsrekommendationerna som anges i Azure Security Benchmark.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589456"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-säkerhetsbaslinje för Azure Storage

Den här säkerhetsbaslinjen tillämpar riktlinjer [från Azure Security Benchmark version 1.0](../../security/benchmarks/overview-v1.md) Azure Storage. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de säkerhetskontroller **som definierats** av Azure Security Benchmark och tillhörande vägledning som gäller för Azure Storage. **Kontroller** som inte är Azure Storage har undantagits.

 
Information om hur Azure Storage mappning helt till Azure Security Benchmark finns i den fullständiga Azure Storage [säkerhetsbaslinjemappningsfilen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

**Vägledning:** Konfigurera lagringskontots brandvägg genom att begränsa åtkomsten till klienter från specifika offentliga IP-adressintervall, välja virtuella nätverk eller specifika Azure-resurser.  Du kan också konfigurera privata slutpunkter så att trafik till lagringstjänsten från ditt företag endast färdas över privata nätverk.

Obs! Klassiska lagringskonton stöder inte brandväggar och virtuella nätverk.

- [Så här konfigurerar du Azure Storage brandväggen](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Så här konfigurerar du privata slutpunkter för Azure Storage](storage-private-endpoints.md)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Övervaka och logga konfiguration och trafik för virtuella nätverk, undernät och nätverksgränssnitt

**Vägledning:** Azure Storage tillhandahåller en säkerhetsmodell med flera lager. Du kan begränsa åtkomsten till ditt lagringskonto till begäranden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i ett Azure Virtual Network. Du kan använda Azure Security Center och följa rekommendationer för nätverksskydd för att skydda dina nätverksresurser i Azure. Aktivera även flödesloggar för nätverkssäkerhetsgruppen för virtuella nätverk eller undernät som konfigurerats för lagringskontona via brandväggen för lagringskonton och skicka loggar till ett lagringskonto för trafikgranskning. 

 
Observera att om du har privata slutpunkter kopplade till ditt lagringskonto kan du inte konfigurera regler för nätverkssäkerhetsgrupp för undernät. 
 

 
- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md) 
 

 
- [Så här aktiverar du NSG-flödesloggar](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md) 
 

 
- [Förstå privata slutpunkter för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

**Vägledning:** Inte tillämpligt; rekommendation är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

**Vägledning:** Aktivera Azure Defender för Storage för ditt Azure Storage konto. Azure Defender för Storage ger ytterligare ett lager med säkerhet information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton.  Azure Security Center integrerade aviseringar baseras på aktiviteter där nätverkskommunikationen kopplades till en IP-adress som löstes korrekt, oavsett om IP-adressen är en känd riskabel IP-adress (till exempel en känd kryptografier) eller en IP-adress som tidigare inte har identifierats som riskabel. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. 

- [Konfigurera Azure Defender för Storage](azure-defender-storage-configure.md) 

- [Förstå Azure Security Center Integrated Threat Intelligence](../../security-center/azure-defender.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="15-record-network-packets"></a>1.5: Registrera nätverkspaket

**Vägledning:** Network Watcher paketinfångst kan du skapa aviseringssessioner för att spåra trafik mellan lagringskontot och en virtuell dator. Filter tillhandahålls för avfångstsessionen för att säkerställa att du endast samlar in den trafik du vill ha. Paketfångst hjälper till att diagnostisera nätverksavvikelser, både reaktivt och proaktivt. Andra användningsområden är att samla in nätverksstatistik, få information om nätverksintrång, felsöka klient-server-kommunikation och mycket mer. Att kunna fjärrutlösa paketinfångningar gör det enklare att köra en paketfångst manuellt på en önskad virtuell dator, vilket sparar värdefull tid. 

- [ Hantera infångade paket med Azure Network Watcher med hjälp av portalen](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsidentifiering/intrångsskydd (IDS/IPS)

**Vägledning:** Azure Defender storage ger ytterligare ett lager med säkerhetsinformation som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med Azure Security Center och skickas även via e-post till prenumerationsadministratörer med information om misstänkt aktivitet samt rekommendationer om hur de undersöker och åtgärdar hot. 

- [Konfigurera Azure Defender för Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbprogram

**Vägledning:** Ej tillämpligt; rekommendation är avsedd för webbprogram som körs på Azure App Service eller beräkningsresurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexitet och administrativa kostnader för nätverkssäkerhetsregler

**Vägledning:** För resurser i virtuella nätverk som behöver åtkomst till ditt lagringskonto använder du Virtual Network-tjänsttaggar för den konfigurerade Virtual Network för att definiera nätverksåtkomstkontroller för nätverkssäkerhetsgrupper eller Azure Firewall. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet (till exempel Storage) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras. 

När nätverksåtkomst måste vara begränsad till specifika lagringskonton ska du använda Virtual Network för tjänstslutpunkter.

- [Mer information om hur du använder tjänsttaggar](../../virtual-network/service-tags-overview.md)

- [Mer information om tjänstslutpunktsprinciper för virtuellt nätverk för Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhålla standardsäkerhetskonfigurationer för nätverksenheter

**Vägledning:** Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser som är associerade med ditt Azure Storage konto med Azure Policy. Använd Azure Policy alias i namnrymderna "Microsoft.Storage" och "Microsoft.Network" för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationen för dina Lagringskontoresurser. 

Du kan också använda inbyggda principdefinitioner som är relaterade till lagringskontot, till exempel: Lagringskonton bör använda en tjänstslutpunkt för virtuellt nätverk 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy exempel för Storage](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [Azure Policy exempel för Nätverk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Så här skapar du en Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentera trafikkonfigurationsregler

**Vägledning:** Använd taggar för nätverkssäkerhetsgrupper och andra resurser som rör nätverkssäkerhet och trafikflöde. Med taggning kan du associera inbyggda och anpassade namn/värde-par med en viss nätverksresurs, vilket hjälper dig att organisera nätverksresurser och relatera Azure-resurser tillbaka till din nätverksdesign.

Använd någon av de inbyggda Azure Policy-definitioner som rör taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser. 

Nätverkssäkerhetsgrupper stöder taggar, men inte enskilda säkerhetsregler. Säkerhetsregler har ett **beskrivningsfält** som du kan använda för att lagra viss information som du normalt skulle placera i en tagg.

- [Skapa och använda resurstaggar](../../azure-resource-manager/management/tag-resources.md)

- [Så här filtrerar du nätverkstrafik med en nätverkssäkerhetsgrupp](../../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

**Vägledning:** Använd Azure Policy för att logga konfigurationsändringar för nätverksresurser.  Skapa aviseringar inom Azure Monitor som utlöses när ändringar av kritiska nätverksresurser sker.  

 
- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Skapa aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [Prestandatest för Azure-säkerhet: Loggning och övervakning.](../../security/benchmarks/security-control-logging-monitoring.md)*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurera central hantering av säkerhetsloggar

**Vägledning:** Mata in loggar via Azure Monitor för att aggregera säkerhetsdata som genereras av slutpunktsenheter, nätverksresurser och andra säkerhetssystem. Inom Azure Monitor kan du använda Log Analytics-arbetsytor för att köra frågor mot och utföra analyser och använda Azure Storage-konton för långsiktig lagring/arkiveringslagring, om du vill med säkerhetsfunktioner som oföränderlig lagring och framtvingad kvarhållning.

 
- [Samla in plattformsloggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivera granskningsloggning för Azure-resurser

**Vägledning:** Azure-lagringsanalys innehåller loggar för blobar, köer och tabeller. Du kan använda Azure Portal för att konfigurera vilka loggar som registreras för ditt konto.   

 
- [Konfigurera övervakning för ditt Azure Storage konto](manage-storage-analytics-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurera kvarhållning av säkerhetslogglagring

**Vägledning:** När du lagrar säkerhetshändelseloggar i Azure Storage-kontot eller Log Analytics-arbetsytan kan du ange kvarhållningsprincipen enligt organisationens krav.  

 
- [Så här konfigurerar du bevarandeprincip för Azure Storage-kontologgar](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Ändra kvarhållningsperioden för data i Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="26-monitor-and-review-logs"></a>2.6: Övervaka och granska loggar

**Vägledning:** För att granska Azure Storage loggar finns det vanliga alternativ som frågor via Log Analytics-erbjudandet samt ett unikt alternativ för att visa loggfilerna direkt. I Azure Storage lagras loggarna i blobar som måste nås direkt på (Loggningsmappen är dold som standard, så du måste `http://accountname.blob.core.windows.net/$logs` navigera direkt. Den visas inte i Listkommandon) 

 
Aktivera även Azure Defender storage för ditt lagringskonto. Azure Defender för Storage ger ytterligare ett lager med säkerhet information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagringskonton. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med Azure Security Center och skickas även via e-post till prenumerationsadministratörer med information om misstänkt aktivitet samt rekommendationer om hur de undersöker och åtgärdar hot. 
 

 
- [Logga och granska data](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Konfigurera Azure Defender för Storage](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivera aviseringar för avvikande aktiviteter

**Vägledning:** I Azure Security Center aktiverar du Azure Defender för lagringskontot. Aktivera diagnostikinställningar för lagringskontot och skicka loggar till en Log Analytics-arbetsyta. Registrera Log Analytics-arbetsytan för Azure Sentinel eftersom den tillhandahåller en soar-lösning (Security Orchestration Automated Response). På så sätt kan spelböcker (automatiserade lösningar) skapas och användas för att åtgärda säkerhetsproblem.  

 
- [Registrera Azure Sentinel](../../sentinel/quickstart-onboard.md)  

 
- [Hantera aviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Så här varnar du om loggdata i Log Analytics](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Analysloggning i Azure Storage](storage-analytics-logging.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralisera loggning mot skadlig kod

**Vägledning:** Använd Azure Security Center och aktivera Azure Defender for Storage för att identifiera uppladdning av skadlig kod till Azure Storage med hjälp av hash-ryktesanalys och misstänkt åtkomst från en aktiv Tor-avslutsnod (en maskeringsproxy). 

- [Konfigurera Azure Defender för Storage](azure-defender-storage-configure.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivera DNS-frågeloggning

**Vägledning:** Azure DNS Analytics-lösning (förhandsversion) Azure Monitor samlar in insikter om DNS-infrastrukturen om säkerhet, prestanda och åtgärder.  För närvarande stöder detta inte Azure Storage-konton, men du kan använda dns-loggningslösning från tredje part.  

 
- [Samla in insikter om din DNS-infrastruktur med hjälp DNS-analys förhandsversionslösningen](../../azure-monitor/insights/dns-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security Benchmark: Identity and Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Upprätthålla en förteckning över administrativa konton

**Vägledning:** Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här får du en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalogroll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord i förekommande fall

**Vägledning:** Azure Storage eller Azure Active Directory (Azure AD) har begreppet standardlösenord eller tomma lösenord. Azure Storage implementerar en åtkomstkontrollmodell som stöder rollbaserad åtkomstkontroll i Azure (Azure RBAC) samt signaturer för delad nyckel och delad åtkomst (SAS). En egenskap hos delad nyckel och SAS-autentisering är att ingen identitet är associerad med anroparen och därför går det inte att utföra behörighetsbaserad behörighetsbaserad behörighet för säkerhetsobjekt.

- [Auktorisera åtkomst till data i Azure Storage](storage-auth.md)

- [Förstå säkerhetsobjekt och åtkomstkontroll för Azure Storage konto](storage-introduction.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd dedikerade administrativa konton

**Vägledning:** Skapa standardprocedurer för användning av dedikerade administrativa konton som har åtkomst till ditt lagringskonto. Använd Azure Security Center identitets- och åtkomsthantering för att övervaka antalet administrativa konton.

Du kan också aktivera just-in-time/just-enough-åtkomst med hjälp av Azure Active Directory (Azure AD) Privileged Identity Management Privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

- [Förstå Azure Security Center identitet och åtkomst](../../security-center/security-center-identity-access.md)

- [Privileged Identity Management översikt](/azure/active-directory/privileged-identity-management/)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory för enkel inloggning (SSO)

**Vägledning:** Använd vid behov Azure Active Directory enkel inloggning (Azure AD) i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center rekommendationer för identitets- och åtkomsthantering.
 

 
- [Förstå enkel inloggning med Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Auktorisera åtkomst till data i Azure Storage](storage-auth.md)
 

 
- [Auktorisera åtkomst till blobar och köer med hjälp av Azure AD](storage-auth-aad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använd multifaktorautentisering för Azure Active Directory åtkomstbaserad åtkomst

**Vägledning:** Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och följ rekommendationerna Azure Security Center identitets- och åtkomsthantering för att skydda dina lagringskontoresurser.

- [Så här aktiverar du multifaktorautentisering i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst inom Azure Security Center](../../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Använd säkra, Azure-hanterade arbetsstationer för administrativa uppgifter

**Vägledning:** Använda ARBETSSTATIONer (arbetsstationer för privilegierad åtkomst) med multifaktorautentisering konfigurerad för att logga in på och konfigurera lagringskontoresurser.  

 
- [Läs mer om arbetsstationer med privilegierad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Så här aktiverar du multifaktorautentisering i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna om misstänkta aktiviteter från administrativa konton

**Vägledning:** Skicka Azure Security Center riskidentifieringsaviseringar till Azure Monitor konfigurera anpassade aviseringar/meddelanden med hjälp av åtgärdsgrupper. Aktivera Azure Defender lagringskonto för att generera aviseringar för misstänkt aktivitet. Du kan också använda Azure Active Directory (Azure AD) riskidentifiering för att visa aviseringar och rapporter om riskfyllda användarbeteenden.

- [Konfigurera Azure Defender för Storage](azure-defender-storage-configure.md)
 

- [Förstå Azure AD-riskidentifiering](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [Så här konfigurerar du åtgärdsgrupper för anpassade aviseringar och meddelanden](../../azure-monitor/alerts/action-groups.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

**Vägledning:** Använd namngivna platser för villkorsstyrd åtkomst för att endast tillåta åtkomst från specifika logiska gruppningar av IP-adressintervall eller länder/regioner. 

- [Så här konfigurerar du namngivna platser i Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

**Vägledning:** Använd Azure Active Directory (Azure AD) som centralt system för autentisering och auktorisering. Azure tillhandahåller rollbaserad åtkomstkontroll i Azure (Azure RBAC) för mer fullständig kontroll över en klients åtkomst till resurser i ett lagringskonto.   Använd Azure AD-autentiseringsuppgifter när det är möjligt som bästa praxis för säkerhet, i stället för att använda kontonyckeln, vilket är enklare att komprometteras. När din programdesign kräver signaturer för delad åtkomst för åtkomst till Blob Storage använder du Azure AD-autentiseringsuppgifter för att skapa signaturer för delad åtkomst för användardelegering (SAS) när det är möjligt för överordnad säkerhet.

- [Skapa och konfigurera en Azure AD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Använda resursprovidern Azure Storage åtkomst till hanteringsresurser](authorization-resource-provider.md)

- [Så här konfigurerar du åtkomst till Azure Blob och ködata med Azure RBAC i Azure Portal](storage-auth-aad-rbac-portal.md)

- [Auktorisera åtkomst till data i Azure Storage](storage-auth.md)

- [Bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Granska och stämma av användaråtkomst regelbundet

**Vägledning:** Granska loggarna Azure Active Directory Azure AD för att identifiera inaktuella konton som kan innehålla konton med administrativa roller för lagringskonton. Använd dessutom Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram som kan användas för att få åtkomst till lagringskontoresurser och rolltilldelningar. Användaråtkomst bör regelbundet granskas för att se till att endast rätt användare har fortsatt åtkomst. 
 

 
Du kan också använda signatur för delad åtkomst (SAS) för att ge säker delegerad åtkomst till resurser i ditt lagringskonto utan att äventyra säkerheten för dina data. Du kan bland annat styra vilka resurser som klienten kan komma åt, vilka behörigheter de har för resurserna och hur länge SAS är giltig.
 

 
Granska även anonym läsåtkomst till containrar och blobar. Som standard kan en container och alla blobar i den endast nås av användare som har fått rätt behörighet. Du kan använda Azure Monitor för att varna om anonym åtkomst för lagringskonton med hjälp av anonyma autentiseringsvillkor.
 

 
Ett effektivt sätt att minska risken för intet ont anande användarkontoåtkomst är att begränsa varaktigheten för åtkomst som du beviljar till användare. Tidsbegränsade SAS-URI:er är ett effektivt sätt att automatiskt upphöra med användaråtkomst till ett lagringskonto. Dessutom är roterande lagringskontonycklar ofta ett sätt att säkerställa att oväntad åtkomst via lagringskontonycklar har en begränsad varaktighet.
 

 
- [Förstå Azure AD-rapportering](/azure/active-directory/reports-monitoring/) 
 

 
- [Visa och ändra åtkomst på Azure Storage kontonivå](storage-auth-aad-rbac-portal.md)
 

 
- [Bevilja begränsad åtkomst till Azure Storage resurser med hjälp av signaturer för delad åtkomst (SAS)](storage-sas-overview.md)
 

 
- [Hantera anonym läsåtkomst till containrar och blob-objekt](../blobs/anonymous-read-access-configure.md)
 

 
- [Övervaka ett lagringskonto i Azure-portalen](manage-storage-analytics-logs.md)
 

 
- [Hantera åtkomstnycklar för lagringskonto](storage-account-keys-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning:** Använd Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Alla loggar lagras i blockblobar i en container med namnet $logs, som skapas automatiskt när Lagringsanalys aktiveras för ett lagringskonto.
 

Skapa diagnostikinställningar för Azure Active Directory-användarkonton (Azure AD) och skicka granskningsloggar och inloggningsloggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics-arbetsytan.

Om du vill övervaka autentiseringsfel mot Azure Storage-konton kan du skapa aviseringar som meddelar dig när vissa tröskelvärden har nåtts för mått för lagringsresurser. Du kan också använda Azure Monitor för att varna om anonym åtkomst för lagringskonton med hjälp av anonyma autentiseringsvillkor.

- [Analysloggning i Azure Storage](storage-analytics-logging.md)
 

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Konfigurera måttaviseringar för Azure Storage konton](manage-storage-analytics-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Avisering om kontobeteendeavvikelse vid inloggning

**Vägledning:** Använd Azure Active Directory (Azure AD) risk- och identitetsskyddsfunktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till dina lagringskontoresurser. Du bör aktivera automatiserade svar Azure Sentinel att implementera organisationens säkerhetssvar.

- [Så visar du riskfyllda inloggningar för Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du riskprinciper för Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Registrera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

**Vägledning:** I supportscenarier där Microsoft behöver åtkomst till kunddata tillhandahåller Customer Lockbox (förhandsversion för lagringskonto) ett gränssnitt där kunder kan granska och godkänna eller avvisa begäranden om kunddataåtkomst. Microsoft varken kräver eller begär åtkomst till organisationens hemligheter som lagras i lagringskontot.

- [Förstå Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Upprätthålla en förteckning över känslig information

**Vägledning:** Använd taggar för att spåra lagringskontoresurser som lagrar eller bearbetar känslig information. 

- [Skapa och använda taggar](../../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolera system som lagrar eller bearbetar känslig information

**Vägledning:** Implementera isolering med separata prenumerationer, hanteringsgrupper och lagringskonton för enskilda säkerhetsdomäner, till exempel miljö och datakänslighet.  Du kan begränsa ditt lagringskonto för att styra åtkomstnivån till dina lagringskonton som dina program och företagsmiljöer kräver, baserat på typ och delmängd av de nätverk som används. När nätverksregler har konfigurerats kan endast program som begär data över den angivna uppsättningen nätverk komma åt ett lagringskonto. Du kan styra åtkomsten till Azure Storage via Azure RBAC (Azure RBAC).

Du kan också konfigurera privata slutpunkter för att förbättra säkerheten när trafiken mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponeringen från det offentliga Internet.

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md)

- [Tjänstslutpunkter för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Övervaka och blockera obehörig överföring av känslig information

**Vägledning:** För lagringskontoresurser som lagrar eller bearbetar känslig information markerar du resurserna som känsliga med hjälp av Taggar. Om du vill minska risken för dataförlust via exfiltrering begränsar du utgående nätverkstrafik för Azure Storage-konton med Azure Firewall.  

Använd dessutom tjänstslutpunktsprinciper för virtuellt nätverk för att filtrera utgående virtuell nätverkstrafik till Azure Storage-konton via tjänstslutpunkt och tillåta data exfiltrering till endast specifika Azure Storage konton.

- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Principer för tjänstslutpunkt för virtuellt nätverk för Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Förstå skydd av kunddata i Azure](../../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Kryptera all känslig information under överföring

**Vägledning:** Du kan framtvinga användningen av HTTPS genom att aktivera Säker överföring krävs för lagringskontot. Anslutningar som använder HTTP avvisas när detta har aktiverats.  Du kan också använda Azure Security Center och Azure Policy för att framtvinga säker överföring för ditt lagringskonto.

- [Så här kräver du säker överföring i Azure Storage](storage-require-secure-transfer.md)

- [Azure-säkerhetsprinciper som övervakas av Security Center](../../security-center/policy-reference.md)

**Ansvar**: Delad

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna](/azure/security-center/security-center-recommendations). De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Använd ett aktivt identifieringsverktyg för att identifiera känsliga data

**Vägledning:** Dataidentifieringsfunktioner är ännu inte tillgängliga för Azure Storage och relaterade resurser. Implementera en lösning från tredje part om det behövs i efterlevnadssyfte. 

- [Förstå skydd av kunddata i Azure](../../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Använd rollbaserad åtkomstkontroll för att styra åtkomsten till resurser

**Vägledning:** Azure Active Directory (Azure AD) auktoriserar åtkomsträttigheter till skyddade resurser via rollbaserad åtkomstkontroll i Azure (Azure RBAC). Azure Storage definierar en uppsättning inbyggda RBAC-roller i Azure som omfattar vanliga uppsättningar med behörigheter som används för åtkomst till blob- eller ködata.

- [Tilldela Azure RBAC-roller för Azure Storage konto](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Använda resursprovidern Azure Storage åtkomst till hanteringsresurser](authorization-resource-provider.md)

- [Så här konfigurerar du åtkomst till Azure Blob och ködata med Azure RBAC i Azure Portal](storage-auth-aad-rbac-portal.md)

- [Skapa och konfigurera en Azure AD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Auktorisera åtkomst till data i Azure Storage](storage-auth.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Kryptera känslig information i vila

**Vägledning:** Azure Storage kryptering har aktiverats för alla lagringskonton och kan inte inaktiveras. Azure Storage krypterar automatiskt dina data när de sparas i molnet. När du läser data från Azure Storage krypteras dem av Azure Storage innan de returneras. Azure Storage kryptering kan du skydda dina vilodata utan att behöva ändra kod eller lägga till kod i några program. 

- [Förstå Azure Storage kryptering i vila](storage-service-encryption.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logga och avisering om ändringar av viktiga Azure-resurser

**Vägledning:** Använd Azure Monitor med Azure-aktivitetsloggen för att skapa aviseringar för när ändringar sker i lagringskontoresurser.  Du kan också aktivera Azure Storage för att spåra hur varje begäran som gjorts mot Azure Storage har auktoriserats. Loggarna anger om en begäran har gjorts anonymt, med hjälp av en OAuth 2.0-token, med hjälp av delad nyckel eller med hjälp av en signatur för delad åtkomst (SAS).  Du kan också använda Azure Monitor för att varna om anonym åtkomst för lagringskonton med hjälp av anonyma autentiseringsvillkor. 

 
- [Så här skapar du aviseringar för Azure-aktivitetslogghändelser](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Analysloggning i Azure Storage](storage-analytics-logging.md) 

 
- [Konfigurera måttaviseringar för Azure Storage konton](manage-storage-analytics-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security Benchmark: Vulnerability Management](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Köra automatiserade sårbarhetsgenomsökningsverktyg

**Vägledning:** Följ rekommendationerna från Azure Security Center att kontinuerligt granska och övervaka konfigurationen av dina lagringskonton.  

- [Säkerhetsrekommendationer – en referensguide](../../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Jämför tillbaka till bakåt-sårbarhetsgenomsökningar

**Vägledning:** Ej tillämpligt; Microsoft utför sårbarhetshantering på de underliggande system som stöder lagringskonton.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Använd en riskklassificeringsprocess för att prioritera åtgärder vid identifierade säkerhetsrisker

**Vägledning:** Använd standardriskklassificeringar (säkerhetspoäng) som tillhandahålls av Azure Security Center. 

- [Förstå Azure Security Center säkerhetspoäng](/azure/security-center/security-center-secure-score)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security Benchmark: Inventory and Asset Management](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Använd automatisk tillgångsidentifiering

**Vägledning:** Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (inklusive lagringskonton) i dina prenumerationer. Se till att du har rätt (läsbehörighet) i din klientorganisation och att du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="62-maintain-asset-metadata"></a>6.2: Underhålla tillgångsmetadata

**Vägledning:** Tillämpa taggar på lagringskontoresurser som ger metadata för att organisera dem logiskt i en taxonomi. 

- [Skapa och använda taggar](../../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ta bort obehöriga Azure-resurser

**Vägledning:** Använd taggning, hanteringsgrupper och separata prenumerationer när det är lämpligt för att organisera och spåra lagringskonton och relaterade resurser. Stämma av inventeringen regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid. 

Använd även Azure Defender Storage för att identifiera obehöriga Azure-resurser. 

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md) 

- [Så här skapar du Hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda taggar](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurera Azure Defender för Storage](azure-defender-storage-configure.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definiera och underhålla inventering av godkända Azure-resurser

**Vägledning:** Du måste skapa en inventering av godkända Azure-resurser enligt organisationens behov.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Övervaka för ej godkända Azure-resurser

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner: 

 
 - Otillåtna resurstyper 
 
 - Tillåtna resurstyper 
 

 
Dessutom kan du använda Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna. Detta kan vara till hjälp i miljöer med hög säkerhet, till exempel de med Lagringskonton. 
 

 
- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Skapa frågor med Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Ta bort ej godkända Azure-resurser och program

**Vägledning:** Kunden kan förhindra att resurser skapas eller används med Azure Policy som krävs av kundens företagsprinciper. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="69-use-only-approved-azure-services"></a>6.9: Använd endast godkända Azure-tjänster

**Vägledning:** Använd Azure Policy att ange begränsningar för vilken typ av resurser som kan skapas i kundprenumerationer med hjälp av följande inbyggda principdefinitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Ytterligare information finns på de refererade länkarna.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en viss resurstyp med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning:** [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark) är standardprincipinitiativ för Security Center och är grunden för Security Center [rekommendationerna.](/azure/security-center/security-center-recommendations) De Azure Policy som är relaterade till den här kontrollen aktiveras automatiskt av Security Center. Aviseringar som rör den här kontrollen kan kräva [en Azure Defender](/azure/security-center/azure-defender) plan för de relaterade tjänsterna.

**Azure Policy inbyggda definitioner – Microsoft.ClassicStorage:**

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy inbyggda definitioner – Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning:** Använd villkorsstyrd åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "Blockera åtkomst" för appen "Microsoft Azure Management". Detta kan förhindra att resurser skapas och ändras i en miljö med hög säkerhet, till exempel de med Lagringskonton. 

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security Benchmark: Secure Configuration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning:** Använd Azure Policy-alias i **namnområdet Microsoft.Storage** för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina lagringskontoinstanser. Du kan också använda inbyggda Azure Policy för Azure Storage konto, till exempel:

- Granska obegränsad nätverksåtkomst till lagringskonton
- Distribuera Azure Defender för Storage
- Lagringskonton bör migreras till nya Azure Resource Manager resurser
- Säker överföring till lagringskonton ska vara aktiverat

Använd rekommendationer från Azure Security Center som en säker konfigurationsbaslinje för dina lagringskonton.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Upprätthålla säkra Azure-resurskonfigurationer

**Vägledning:** Använd Azure Policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för alla dina lagringskontoresurser. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md) 

- [Förstå Azure Policy effekter](../../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Lagra konfiguration av Azure-resurser på ett säkert sätt

**Vägledning:** Använd Azure Repos för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager, Desired State Configuration skript osv. För att komma åt de resurser som du hanterar i Azure DevOps kan du bevilja eller neka behörigheter till specifika användare, inbyggda säkerhetsgrupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Azure AD om de är integrerade med TFS.

- [Lagra kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Distribuera konfigurationshanteringsverktyg för Azure-resurser

**Vägledning:** Använd Azure Policy för att avisering, granskning och framtvinga systemkonfigurationer för lagringskonto. Utveckla dessutom en process och pipeline för hantering av principundantag. 

- [ Så här konfigurerar och hanterar du Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementera automatisk konfigurationsövervakning för Azure-resurser

**Vägledning:** Använd Azure Security Center för att utföra baslinjesökningar för dina Azure Storage-kontoresurser. 

- [Så här åtgärdar du rekommendationer i Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="711-manage-azure-secrets-securely"></a>7.11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning:** Azure Storage krypterar automatiskt dina data när de sparas i molnet. Du kan använda Microsoft-hanterade nycklar för kryptering av lagringskontot eller hantera kryptering med egna nycklar. Om du använder nycklar som tillhandahålls av kunden kan du använda Azure Key Vault för att lagra nycklarna på ett säkert sätt. 

Rotera dessutom lagringskontonycklar regelbundet för att begränsa effekten av förlust eller avslöjande av lagringskontonycklar.

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)

- [Hantera åtkomstnycklar för lagringskonto](storage-account-keys-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Hantera identiteter på ett säkert och automatiskt sätt

**Vägledning:** Auktorisera åtkomst till blobar och köer i Azure Storage-konton med Azure Active Directory (Azure AD) och hanterade identiteter. Azure Blob och Queue Storage stöder Azure AD-autentisering med hanterade identiteter för Azure-resurser. 

Hanterade identiteter för Azure-resurser kan ge åtkomst till blob- och ködata med hjälp av Azure AD-autentiseringsuppgifter från program som körs i Azure Virtual Machines r, funktionsappar, VM-skalningsuppsättningar och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet.

- [Så här beviljar du åtkomst till Blob- och ködata i Azure med hjälp av en hanterad identitet](storage-auth-aad-rbac-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning:** Implementera skanner för autentiseringsuppgifter för att identifiera autentiseringsuppgifter i koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [ Konfigurera skanner för autentiseringsuppgifter](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security Benchmark: Malware Defense.](../../security/benchmarks/security-control-malware-defense.md)*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Skanna filer i förväg som ska överföras till Icke-beräkningsbaserade Azure-resurser

**Vägledning:** Använd Azure Defender for Storage för att identifiera uppladdning av skadlig kod till Azure Storage med hjälp av hash-ryktesanalys och misstänkt åtkomst från en aktiv Tor-avslutsnod (en anonymiseringsproxy). 
 

 
Du kan också försöka innehåll efter skadlig kod innan du överför till Azure-resurser som inte är beräkningsbaserade, till exempel App Service, Data Lake Storage, Blob Storage och andra för att uppfylla organisationens krav.
 

 
- [Konfigurera Azure Defender för Storage](azure-defender-storage-configure.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Prestandatest för Azure-säkerhet: Dataåterställning.](../../security/benchmarks/security-control-data-recovery.md)*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Säkerställ regelbundna automatiserade säkerhetskopieringar

**Vägledning:** Data i ditt Microsoft Azure-lagringskonto replikeras alltid automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverks- eller strömavbrott och omfattande naturkatastrofer. Du kan välja att replikera dina data inom samma datacenter, över zonindelade datacenter inom samma region eller mellan geografiskt avgränsade regioner. 

Du kan också aktivera Azure Automation för att ta regelbundna ögonblicksbilder av blobarna.

- [Förstå Azure Storage redundans och Service-Level avtal](storage-redundancy.md)

- [Skapa en ögonblicksbild av en blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation översikt](../../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Utför fullständiga systemsäkerhetskopior och säkerhetskopiera kund hanterade nycklar

**Vägledning:** För att kopiera data från lagringskontotjänster som stöds finns det flera tillgängliga metoder, inklusive att använda azcopy eller verktyg från tredje part. Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärskritiska dataobjekt i ett WORM-tillstånd (Write Once, Read Many). Det här tillståndet gör att data inte kan raderas och inte kan ändras under ett användarangivet intervall.
 

Kund-hanterade/tillhandahållna nycklar kan backas upp inom Azure Key Vault hjälp av Azure CLI eller PowerShell. 

 
- [Kom igång med AzCopy](storage-use-azcopy-v10.md)  

- [Ange och hantera oföränderlighetsprinciper för Blob Storage](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Säkerhetskopiera nyckelvalvsnycklar i Azure](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Verifiera alla säkerhetskopior, inklusive kund hanterade nycklar

**Vägledning:** Utför regelbundet dataåterställning av Key Vault certifikat, nycklar, hanterade lagringskonton och hemligheter med följande PowerShell-kommandon:

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Återställa Key Vault certifikat](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Så här återställer du Key Vault nycklar](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Så här återställer du Key Vault hanterade lagringskonton](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Återställa Key Vault hemligheter](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy är ett kommandoradsverktyg som du kan använda för att kopiera blobar, filer och tabelldata till eller från ett lagringskonto](storage-use-azcopy-v10.md)

Obs! Om du vill kopiera data till och från Azure Table Storage-tjänsten installerar du AzCopy version 7.3.

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Skydda säkerhetskopior och kund hanterade nycklar

**Vägledning:** Om du vill aktivera kund hanterade nycklar på ett lagringskonto måste du använda Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna Mjuk borttagning och Rensa inte i nyckelvalvet.  Key Vault funktionen mjuk borttagning gör det möjligt att återställa borttagna valv och valvobjekt som nycklar, hemligheter och certifikat.  Om du säkerhetskopierar Storage-kontodata till Azure Storage-blobar aktiverar du mjuk borttagning för att spara och återställa data när blobar eller blobögonblicksbilder tas bort.  Du bör behandla dina säkerhetskopior som känsliga data och tillämpa relevanta åtkomst- och dataskyddskontroller som en del av den här baslinjen.  För bättre skydd kan du dessutom lagra affärskritiska dataobjekt i ett WORM-tillstånd (Skriv en gång, Läs många).

- [Så här använder Azure Key Vault mjuk borttagning](../../key-vault/general/key-vault-recovery.md)

- [Mjuk borttagning för Azure Storage-blobar](../blobs/soft-delete-blob-overview.md)

- [Lagra affärskritiska blobdata med oföränderlig lagring](../blobs/storage-blob-immutable-storage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Skapa en guide för incidentsvar

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen process för svar på säkerhetsincidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center är en incidents uppbyggnad](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST:s guide för hantering av säkerhetsincidenter](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Skapa en procedur för incidentbedömning och prioritering

**Vägledning:** Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som ska undersökas först. Allvarlighetsgraden baseras på hur säker Security Center är i hitta eller analys som används för att utfärda aviseringen samt konfidensnivån för att det fanns skadlig avsikt bakom aktiviteten som ledde till aviseringen. 

 
Markera dessutom prenumerationer tydligt (t.ex. produktion, icke-produktion) med hjälp av taggar och skapa ett namngivningssystem för att tydligt identifiera och kategorisera Azure-resurser, särskilt de som bearbetar känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.
 

 
- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)
 

 
- [Använda taggar för att organisera dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="103-test-security-response-procedures"></a>10.3: Testa procedurer för säkerhetssvar

**Vägledning:** Genomför övningar för att regelbundet testa systemets incidentsvarsfunktioner för att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST:s publikation – Guide till test-, tränings- och övningsprogram för IT-planer och IT-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Ange kontaktuppgifter för säkerhetsincidenter och konfigurera aviseringsmeddelanden för säkerhetsincidenter

**Vägledning:** Kontaktinformation om säkerhetsincident används av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en obehörig part. Granska incidenter i efterhand för att säkerställa att problemen är lösta.

- [Ställa in Azure Security Center säkerhetskontakt](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Införliva säkerhetsaviseringar i systemet för incidentsvar

**Vägledning:** Exportera dina Azure Security Center och rekommendationer med hjälp av funktionen Kontinuerlig export för att identifiera risker för Azure-resurser. Med löpande export kan du exportera aviseringar och rekommendationer manuellt eller kontinuerligt. Du kan använda anslutningsappen Azure Security Center data för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisera svaret på säkerhetsaviseringar

**Vägledning:** Använd funktionen Arbetsflödesautomation i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" för säkerhetsaviseringar och rekommendationer för att skydda dina Azure-resurser.
    

- [ Så här konfigurerar du arbetsflödesautomation och Logic Apps](../../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: Ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [Azure Security Benchmark: Penetration Tests (Intrångstester) och Red Team Exercises (Red Team-övningar).](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Genomför regelbundna intrångstester av dina Azure-resurser och se till att alla viktiga säkerhetsresultat åtgärdas

**Vägledning:** Följ Microsofts regler för engagemang för att säkerställa att dina intrångstester inte bryter mot Microsofts principer. Använd Microsofts strategi och körning av Red Teaming och intrångstester på live-webbplatser mot Microsoft-hanterad molninfrastruktur, -tjänster och -program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: Ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
