---
title: Azures säkerhets bas linje för Azure Storage
description: Azures säkerhets bas linje för Azure Storage
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5367fbcb88e19253eead58d8f99933fdadc41898
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714722"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azures säkerhets bas linje för Azure Storage

Azures säkerhets bas linje för Azure Storage innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

**Vägledning**: Konfigurera ditt lagrings kontos brand vägg genom att begränsa åtkomsten till klienter från vissa offentliga IP-adressintervall, Välj virtuella nätverk (virtuella nätverk) på Azure eller till vissa Azure-resurser. Du kan också konfigurera privata slut punkter så att trafik till lagrings tjänsten från företaget överförs exklusivt över privata nätverk.

Obs! klassiska lagrings konton stöder inte brand väggar och virtuella nätverk.

- [Så här konfigurerar du Azure Storage brand väggen](./storage-network-security.md#change-the-default-network-access-rule)

- [Konfigurera privata slut punkter för Azure Storage](./storage-private-endpoints.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: övervaka och logga VNet, undernät och NIC-konfiguration och trafik

**Vägledning**: Azure Storage ger en skiktad säkerhets modell. Du kan begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i ett Azure-Virtual Network (VNet). Du kan använda Azure Security Center och följa rekommendationer för nätverks skydd för att skydda dina nätverks resurser i Azure. Du kan också aktivera NSG flödes loggar för virtuella nätverk/undernät som har kon figurer ATS för lagrings konton via lagrings kontots brand vägg och skicka loggar till ett lagrings konto för trafik granskning. 

Observera att om du har privata slut punkter som är kopplade till ditt lagrings konto kan du inte konfigurera regler för nätverks säkerhets grupper (NSG) för undernät. 

- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](./storage-network-security.md)

- [Så här aktiverar du NSG Flow-loggar](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../../security-center/security-center-network-recommendations.md)

- [Förstå privata slut punkter för Azure Storage](./storage-private-endpoints.md#known-issues)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: ej tillämpligt; rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera avancerat skydd för ditt Azure Storage-konto. Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Azure Security Center integrerade aviseringar baseras på aktiviteter för vilka nätverkskommunikation har associerats med en IP-adress som har lösts, oavsett om IP-adressen är en känd riskfylld IP-adress (till exempel en känd cryptominer) eller en IP-adress som inte redan har identifierats som riskfylld. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. 

- [Aktivera avancerat skydd](./azure-defender-storage-configure.md?tabs=azure-portal)

- [Förstå Azure Security Center integrerad Hot information](../../security-center/azure-defender.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

**Vägledning**: med Network Watcher paket insamling kan du skapa avbildnings sessioner för att spåra trafiken mellan lagrings kontot och en virtuell dator. Filter tillhandahålls för insamlingsbufferten för att se till att du bara fångar den trafik som du vill använda. Med paket fångst kan du diagnostisera nätverks avvikelser, både återaktivt och proaktivt. Andra användnings områden är insamling av nätverks statistik, insamling av information om nätverks intrång, fel sökning av klient server kommunikation och mycket mer. Att kunna fjärrutlös paket fångster, underlättar belastningen på att köra en paket registrering manuellt på en önskad virtuell dator, vilket sparar värdefull tid. 

- [Hantera paket fångster med Azure Network Watcher med hjälp av portalen](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: Distribuera Network-baserad intrångs identifiering/system för skydd mot intrång

**Vägledning**: Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med Azure Security Center och skickas även via e-post till prenumerationsadministratörer med information om misstänkt aktivitet samt rekommendationer om hur de undersöker och åtgärdar hot. 

- [Konfigurera Avancerat skydd för Azure Storage](./azure-defender-storage-configure.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: hantera trafik till dina webb program

**Vägledning**: ej tillämpligt; rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurs i virtuella nätverk som behöver åtkomst till ditt lagrings konto använder du Virtual Network Service-taggar för det konfigurerade virtuella nätverket för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på service tag gen (t. ex. lagring) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. 

Använd Virtual Network tjänst slut punkts principer när nätverks åtkomst måste begränsas till vissa lagrings konton.

- [Mer information om att använda service märken](../../virtual-network/service-tags-overview.md)

- [Mer information om tjänst slut punkts principer för virtuella nätverk för Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: tjänst

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer för nätverks resurser som är kopplade till ditt Azure Storage-konto med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft. Storage" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller tillämpa nätverks konfigurationen för dina lagrings konto resurser. 

Du kan också använda inbyggda princip definitioner som är relaterade till lagrings kontot, t. ex.: lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för lagring](../../governance/policy/samples/built-in-policies.md#storage)

- [Azure Policy exempel för nätverk](../../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../../governance/blueprints/create-blueprint-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks säkerhets grupper (NSG) och andra resurser som rör nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk. Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser. Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar. 

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Virtual Network](../../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG med en säkerhets konfiguration](../../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure policy för att logga konfigurations ändringar för nätverks resurser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: Använd godkänd tids resurs för synkronisering

**Vägledning**: ej tillämpligt; Microsoft hanterar tids källor för Azure Storage-konton.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av slut punkter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor (er) för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring eller lagrings utrymme, om du vill ha säkerhetsfunktioner som till exempel oföränderlig lagring och framtvingade kvarhållning.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure-lagringsanalys innehåller loggar för blobbar, köer och tabeller. Du kan använda Azure Portal för att konfigurera vilka loggar som registreras för ditt konto. 

- [Så här konfigurerar du övervakning för ditt Azure Storage-konto](./manage-storage-analytics-logs.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: när du lagrar säkerhets händelse loggar i Azure Storage konto eller Log Analytics arbets yta kan du ange bevarande principen enligt organisationens krav. 

- [Konfigurera bevarande princip för Azure Storage konto loggar](./manage-storage-analytics-logs.md#configure-logging)

- [Ändra data lagrings perioden i Log Analytics](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: om du vill granska Azure Storage loggar finns det vanliga alternativ, till exempel frågor genom Log Analytics-erbjudandet och ett unikt alternativ för att Visa loggfilerna direkt. I Azure Storage lagras loggarna i blobbar som måste nås direkt `http://accountname.blob.core.windows.net/$logs` (loggnings mappen är dold som standard, så du måste navigera direkt. Den visas inte i list kommandon) 

Aktivera också Avancerat skydd för ditt Azure Storage-konto. Avancerat skydd för Azure Storage ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Säkerhetsaviseringar utlöses när avvikelser i en aktivitet inträffar. Dessa säkerhetsaviseringar är integrerade med Azure Security Center och skickas även via e-post till prenumerationsadministratörer med information om misstänkt aktivitet samt rekommendationer om hur de undersöker och åtgärdar hot. 

- [Logga och granska data](./storage-analytics-logging.md#how-logs-are-stored)

- [Aktivera avancerat skydd](./azure-defender-storage-configure.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet

**Vägledning**: i Azure Security Center aktiverar du Avancerat skydd för lagrings konto. Aktivera diagnostikinställningar för lagrings kontot och skicka loggar till en Log Analytics-arbetsyta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem. 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../../azure-monitor/alerts/tutorial-response.md)

- [Analysloggning i Azure Storage](./storage-analytics-logging.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Använd Azure Security Center och aktivera skydd mot hot för Azure Storage för att identifiera överföringar av skadlig kod till Azure Storage att använda hash ryktes analys och misstänkt åtkomst från en aktiv Tor-stängningsmodul (en maskera-proxy). 

- [Konfigurera Avancerat skydd för Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: Azure DNS Analytics-lösning (för hands version) i Azure Monitor samlar in insikter om säkerhet, prestanda och åtgärder i DNS-infrastrukturen. För närvarande stöds inte Azure Storage konton, men du kan inte använda en DNS-loggnings lösning från tredje part. 

- [Samla in insikter om din DNS-infrastruktur med DNS-analys för hands versions lösning](../../azure-monitor/insights/dns-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: ej tillämpligt; Benchmark är avsett för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och Access Control](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: underhåll inventering av administrativa konton

**Vägledning**: Azure AD har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper. 

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Storage konton eller Azure Active Directory har begreppet standard-eller tomt lösen ord. Azure Storage implementerar en modell för åtkomst kontroll som stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC) samt delade nycklar och signaturer för delad åtkomst (SAS). En egenskap för delad nyckel och SAS-autentisering är att ingen identitet är associerad med anroparen och därför kan inte behörigheten för säkerhets objekts behörighet utföras. 

- [Auktorisera åtkomst till data i Azure Storage](./storage-auth.md)

- [Förstå säkerhets objekt och åtkomst kontroll för Azure Storage konto](./storage-introduction.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som har åtkomst till ditt lagrings konto. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton. 

Du kan också aktivera en just-in-Time/bara-tillräcklig-åtkomst genom att använda Azure AD Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure-ARM. 

- [Förstå Azure Security Center identitet och åtkomst](../../security-center/security-center-identity-access.md)

- [Översikt över Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory Single Sign-On (SSO)

**Vägledning**: där det är möjligt använder Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd rekommendationer för Azure Security Center identitets-och åtkomst hantering. 

- [Förstå SSO med Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Auktorisera åtkomst till data i Azure Storage](./storage-auth.md)

- [Ge åtkomst till blobbar och köer med hjälp av Azure Active Directory](./storage-auth-aad.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd multifaktor-autentisering för all Azure Active Directory baserad åtkomst.

**Vägledning**: Aktivera Azure Active Directory multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering som hjälper dig att skydda dina lagrings konto resurser. 

- [Aktivera MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med MFA konfigurerat för att logga in på och konfigurera lagrings konto resurser. 

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkt aktivitet från administrativa konton

**Vägledning**: skicka Azure Security Center risk identifierings aviseringar till Azure Monitor och konfigurera anpassade aviseringar/aviseringar med hjälp av åtgärds grupper. Aktivera avancerat skydd för Azure Storage konto för att generera aviseringar för misstänkt aktivitet. Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden. 

- [Konfigurera Avancerat skydd för Azure Storage konto](./azure-defender-storage-configure.md)

- [Förstå identifieringar av Azure AD-risker](../../active-directory/identity-protection/overview-identity-protection.md)

- [Konfigurera åtgärds grupper för anpassad avisering och avisering](../../azure-monitor/alerts/action-groups.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner. 

- [Så här konfigurerar du namngivna platser i Azure](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure ger Azure rollbaserad åtkomst kontroll (Azure RBAC) för detaljerad kontroll över en klients åtkomst till resurser i ett lagrings konto.  Använd Azure AD-autentiseringsuppgifter när det är möjligt som en säkerhets metod, i stället för att använda konto nyckeln, vilket kan vara svårare att avslöja. När program designen kräver delade åtkomst-signaturer för åtkomst till blob-lagring, använder du autentiseringsuppgifter för Azure AD för att skapa en användar Delegerings-signaturer för delad åtkomst (SAS) när det är möjligt för överlägsen säkerhet.

- [Skapa och konfigurera en Azure AD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Använd Azure Storage Resource Provider för att få åtkomst till hanterings resurser](./authorization-resource-provider.md)

- [Så här konfigurerar du åtkomst till Azure blob och Queue data med Azure RBAC i Azure Portal](./storage-auth-aad-rbac-portal.md)

- [Auktorisera åtkomst till data i Azure Storage](./storage-auth.md)

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](./storage-sas-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: granska Azure Active Directory loggar för att identifiera inaktuella konton som kan omfatta de administrativa rollerna för lagrings kontot. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program som kan användas för att komma åt lagrings konto resurser och roll tilldelningar. Användar åtkomsten bör granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.  

Du kan också använda signaturen för delad åtkomst (SAS) för att tillhandahålla säker delegerad åtkomst till resurser i ditt lagrings konto utan att kompromissa med säkerheten för dina data. Du kan kontrol lera vilka resurser som klienten kan komma åt, vilka behörigheter de har på dessa resurser och hur länge SAS är giltig, bland andra parametrar.

Granska även anonym Läs behörighet till behållare och blobbar. Som standard kan en container och alla blobar i den endast nås av användare som har fått rätt behörighet. Du kan använda Azure Monitor för att varna vid anonym åtkomst för lagrings konton med anonymt autentiserings villkor.

Ett effektivt sätt att minska risken för obehörig åtkomst till användar konton är att begränsa varaktigheten för åtkomst som du beviljar användare. Tidsbegränsade SAS-URI: er är ett effektivt sätt att automatiskt upphöra användarens åtkomst till ett lagrings konto. Dessutom är det enkelt att rotera lagrings konto nycklar på ett sätt som garanterar att oväntad åtkomst via lagrings konto nycklar är en begränsad varaktighet.

- [Förstå Azure AD repor ting](../../active-directory/reports-monitoring/index.yml)

- [Visa och ändra åtkomst på Azure Storage konto nivå](./storage-auth-aad-rbac-portal.md)

- [Bevilja begränsad åtkomst till Azure Storage resurser med signaturer för delad åtkomst (SAS)](./storage-sas-overview.md)

- [Hantera anonym läsåtkomst till containrar och blob-objekt](../blobs/anonymous-read-access-configure.md)

- [Övervaka ett lagringskonto i Azure-portalen](./manage-storage-analytics-logs.md)

- [Hantera åtkomst nycklar för lagrings konton](./storage-account-keys-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: övervaka försök att komma åt inaktiverade konton

**Vägledning**: Använd Lagringsanalys för att logga detaljerad information om lyckade och misslyckade förfrågningar till en lagrings tjänst. Alla loggar lagras i block-blobar i en behållare med namnet $logs, som skapas automatiskt när Lagringsanalys har Aktiver ATS för ett lagrings konto.

Skapa diagnostikinställningar för Azure Active Directory användar konton, skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan. Om du vill övervaka autentiseringsfel mot Azure Storage-konton kan du skapa aviseringar som meddelar dig när vissa tröskelvärden har nåtts för lagrings resurs mått. Använd dessutom Azure Monitor för att varna vid anonym åtkomst för lagrings konton med anonymt autentiserings villkor.

- [Analysloggning i Azure Storage](./storage-analytics-logging.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurera mått varningar för Azure Storage konton](./manage-storage-analytics-logs.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directorys risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina lagrings konto resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar. 

- [Så visar du riskfyllda inloggningar för Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information, är Customer lockbox (för hands version av lagrings konto) ett gränssnitt för kunder att granska och godkänna eller avvisa förfrågningar om kund data åtkomst. Microsoft kommer inte att behöva eller begära åtkomst till din organisations hemligheter som lagras i lagrings kontot.

- [Förstå Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra lagrings konto resurser som lagrar eller bearbetar känslig information. 

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer, hanterings grupper och lagrings konton för enskilda säkerhets domäner, till exempel miljö, data känslighet.  Du kan begränsa ditt lagrings konto för att kontrol lera åtkomst nivån till dina lagrings konton som dina program och företags miljöer kräver, baserat på typ och delmängd av nätverk som används. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt ett lagrings konto. Du kan styra åtkomsten till Azure Storage via Azure RBAC. Du kan också konfigurera privata slut punkter för att förbättra säkerheten som trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnät nätverk, vilket eliminerar exponering från det offentliga Internet. 

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](./storage-network-security.md)

- [Virtual Network tjänst slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information.

**Vägledning**: för lagrings konto resurser som lagrar eller bearbetar känslig information markerar du resurserna som känsliga med taggar. Begränsa utgående nätverks trafik för Azure Storage-konton med hjälp av Azure-brandväggen för att minska risken för data förlust via exfiltrering. 

Använd dessutom tjänst slut punkts principer för virtuella nätverk för att filtrera utgående virtuell nätverks trafik till Azure Storage konton över tjänstens slut punkt och tillåta data exfiltrering enbart till vissa Azure Storage-konton.

- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Principer för tjänstslutpunkt för virtuellt nätverk för Azure Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Förstå skydd av kunddata i Azure](../../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: du kan framtvinga användningen av https genom att aktivera säker överföring som krävs för lagrings kontot. Anslutningar som använder HTTP avvisas när detta har aktiverats. Använd dessutom Azure Security Center och Azure Policy för att framtvinga säker överföring av ditt lagrings konto.

- [Så här kräver du säker överföring i Azure Storage](./storage-require-secure-transfer.md)

- [Azure säkerhets principer övervakas av Security Center](../../security-center/policy-reference.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering är ännu inte tillgängliga för Azure Storage konto och relaterade resurser. Implementera en lösning från tredje part om det krävs för efterlevnad. 

- [Förstå skydd av kunddata i Azure](../../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via rollbaserad åtkomst kontroll i Azure (Azure RBAC). Azure Storage definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättningar behörigheter som används för att få åtkomst till BLOB-eller Queue-data. 

- [Så här tilldelar du Azure-roller för Azure Storage konto](./storage-auth-aad-rbac-portal.md#assign-azure-roles-using-the-azure-portal)

- [Använd Azure Storage Resource Provider för att få åtkomst till hanterings resurser](./authorization-resource-provider.md)

- [Så här konfigurerar du åtkomst till Azure blob och Queue data med Azure RBAC i Azure Portal](./storage-auth-aad-rbac-portal.md)

- [Så här skapar och konfigurerar du en AAD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Auktorisera åtkomst till data i Azure Storage](./storage-auth.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Storage kryptering har Aktiver ATS för alla lagrings konton och inte kan inaktive ras. Azure Storage krypterar dina data automatiskt när de sparas i molnet. När du läser data från Azure Storage krypteras dem av Azure Storage innan de returneras. Med Azure Storage kryptering kan du skydda dina data i vila utan att behöva ändra kod eller lägga till kod i några program. 

- [Förstå Azure Storage kryptering i vila](./storage-service-encryption.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i lagrings konto resurser. Du kan också aktivera Azure Storage loggning för att spåra hur varje begäran som görs mot Azure Storage auktoriserades. Loggarna anger om en begäran har gjorts anonymt, genom att använda en OAuth 2,0-token med hjälp av delad nyckel eller genom att använda en signatur för delad åtkomst (SAS). Använd dessutom Azure Monitor för att varna vid anonym åtkomst för lagrings konton med anonymt autentiserings villkor.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../../azure-monitor/alerts/alerts-activity-log.md)

- [Analysloggning i Azure Storage](./storage-analytics-logging.md)

- [Konfigurera mått varningar för Azure Storage konton](./manage-storage-analytics-logs.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center för att kontinuerligt granska och övervaka konfigurationen av dina lagrings konton. 

- [Säkerhetsrekommendationer – en referensguide](../../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Distribuera automatisk lösning för Third Party Software korrigerings hantering

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: ej tillämpligt; Microsoft utför sårbarhets hantering på de underliggande system som har stöd för lagrings konton.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker.

**Vägledning**: Använd standard risk klassificeringarna (säkra poäng) som tillhandahålls av Azure Security Center. 

- [Förstå Azure Security Center säkra Poäng](../../security-center/secure-score-security-controls.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Använd Azure Asset Discovery

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla resurser (inklusive lagrings konton) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer. 

- [Så här skapar du frågor med Azure Graph](../../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för lagrings konto resurser som ger metadata till att logiskt organisera dem i en taxonomi. 

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra lagrings konton och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid. 

Använd också Avancerat skydd för Azure Storage för att identifiera oauktoriserade Azure-resurser. 

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../../azure-resource-manager/management/tag-resources.md)

- [Konfigurera Avancerat skydd för Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: underhåll av inventering av godkända Azure-resurser och program varu titlar.

**Vägledning**: du måste skapa en inventering av godkända Azure-resurser enligt organisationens behov. 


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 

 - Otillåtna resurstyper 
 - Tillåtna resurstyper 

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Detta kan hjälpa till med hög säkerhetsbaserade miljöer, till exempel de med lagrings konton. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: kunden kan förhindra att resurser skapas eller används med Azure policy som krävs av kundens företags principer. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 

- Otillåtna resurstyper 
- Tillåtna resurstyper 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="610-implement-approved-application-list"></a>6,10: implementera lista över godkända program

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager via skript

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö, t. ex. med lagrings konton. 

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till ARM](../../role-based-access-control/conditional-access-azure-management.md)

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

*Mer information finns i [säkerhets kontroll: säker konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. Storage" för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina lagrings konto instanser. Du kan också använda inbyggda Azure Policy definitioner för Azure Storage konto, till exempel: 

Granska obegränsad nätverks åtkomst till lagrings konton  
Distribuera Avancerat skydd på lagrings konton  
Lagrings konton ska migreras till nya Azure Resource Manager resurser  
Säker överföring till lagringskonton ska vara aktiverat  

Använd rekommendationer från Azure Security Center som en säker konfigurations bas linje för dina lagrings konton. 

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: upprätta säkra konfigurationer för ditt operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: underhåll säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar över dina lagrings konto resurser. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-databaser för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar, önskade tillstånds konfigurations skript osv. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Distribuera hanterings verktyg för system konfiguration

**Vägledning**: utnyttja Azure policy för att varna, granska och genomdriva system konfigurationer för lagrings kontot. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag. 

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: distribuera system konfigurations hanterings verktyg för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: implementera automatisk konfigurations övervakning för Azure-tjänster

**Vägledning**: utnyttja Azure Security Center för att utföra bas linjes ökningar för dina Azure Storage konto resurser. 

- [Så här åtgärdar du rekommendationer i Azure Security Center](../../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-securely-manage-azure-secrets"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure Storage krypterar dina data automatiskt när de sparas i molnet. Du kan använda Microsoft-hanterade nycklar för kryptering av lagrings kontot eller för att hantera kryptering med sina egna nycklar. Om du använder kundspecifika nycklar kan du utnyttja Azure Key Vault för att lagra nycklarna på ett säkert sätt. 

Dessutom kan du rotera lagrings konto nycklar regelbundet för att begränsa effekten av förlust eller avslöjande av lagrings konto nycklar.

- [Azure Storage-kryptering av vilande data](./storage-service-encryption.md)

- [Hantera åtkomst nycklar för lagrings konton](./storage-account-keys-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: säker och hantera identiteter automatiskt

**Vägledning**: ge åtkomst till blobbar och köer i Azure Storage konton med Azure Active Directory och hanterade identiteter. Azure blob-och Queue Storage-stöd Azure Active Directory (Azure AD)-autentisering med hanterade identiteter för Azure-resurser. Hanterade identiteter för Azure-resurser kan ge åtkomst till blob-och Queue-data med hjälp av Azure AD-autentiseringsuppgifter från program som körs i virtuella Azure-datorer, Function-appar, skalnings uppsättningar för virtuella datorer och andra tjänster. Genom att använda hanterade identiteter för Azure-resurser tillsammans med Azure AD-autentisering kan du undvika att lagra autentiseringsuppgifter med dina program som körs i molnet. 

- [Så här beviljar du åtkomst till Azure blob och Queue data med hjälp av en hanterad identitet](./storage-auth-aad-rbac-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd centralt hanterat program mot skadlig kod

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Använd skydd mot hot för Azure Storage för att identifiera överföringar av skadlig kod till Azure Storage att använda hash ryktes analys och misstänkt åtkomst från en aktiv Tor-nod (en maskera proxy). 

Du kan också förskanna allt innehåll för skadlig kod innan du laddar upp till icke-beräkning av Azure-resurser, till exempel App Service, Data Lake Storage, Blob Storage, och så att de uppfyller organisationens krav.

- [Konfigurera Avancerat skydd för Azure Storage](./azure-defender-storage-configure.md?tabs=azure-portal)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser. Microsoft hanterar skydd mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: data i ditt Microsoft Azure lagrings konto replikeras alltid automatiskt för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att de skyddas från planerade och oplanerade händelser, inklusive tillfälliga maskin varu haverier, nätverks-eller strömavbrott, och massiv natur katastrofer. Du kan välja att replikera dina data inom samma data Center, över zonindelade Data Center inom samma region eller mellan geografiskt åtskilda regioner. 

Du kan också aktivera Azure Automation för att ta regelbundna ögonblicks bilder av blobarna.

- [Förstå Azure Storage redundans och Service-Level avtal](./storage-redundancy.md)

- [Skapa en ögonblicks bild av en BLOB](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Översikt över Azure Automation](../../automation/automation-intro.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: för att säkerhetskopiera data från lagrings konto tjänster som stöds finns det flera metoder som är tillgängliga, inklusive användning av AzCopy eller verktyg från tredje part. Oföränderlig lagring för Azure Blob Storage gör det möjligt för användare att lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många). Det här läget gör att data inte kan raderas och inte kan ändras för ett användardefinierat intervall.

- [Kom igång med AzCopy](./storage-use-azcopy-v10.md)

- [Ange och hantera oföränderlighets-principer för Blob Storage](../blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

Kundhanterade/tillhandahållna nycklar kan säkerhets kopie ras i Azure Key Vault med Azure CLI eller PowerShell. 

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av Key Vault certifikat, nycklar, hanterade lagrings konton och hemligheter, med följande PowerShell-kommandon: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Återställa Key Vault-certifikat](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

- [Återställa Key Vault nycklar](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

- [Återställa Key Vault hanterade lagrings konton](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Så här återställer du Key Vault hemligheter](/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret)

- [AzCopy är ett kommando rads verktyg som du kan använda för att kopiera blobbar, filer och tabell data till eller från ett lagrings konto](./storage-use-azcopy-v10.md)

Obs: om du vill kopiera data till och från Azure Table Storage-tjänsten installerar du AzCopy version 7,3.


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om du vill aktivera Kundhanterade nycklar på ett lagrings konto måste du använda en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den mjuka borttagningen och rensa inte egenskaperna i nyckel valvet. Key Vault funktionen för mjuk borttagning kan återställa borttagna valv och valv objekt, till exempel nycklar, hemligheter och certifikat. Om du säkerhetskopierar lagrings konto data till Azure Storage blobbar, aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort. Du bör behandla dina säkerhets kopior som känsliga data och tillämpa relevanta åtkomst-och data skydds kontroller som en del av den här bas linjen. För förbättrat skydd kan du dessutom lagra affärs kritiska data objekt i en mask (Skriv en gång, läsa många).

- [Använda Azure Key Vault ' mjuk borttagning '](../../key-vault/general/key-vault-recovery.md)

- [Mjuk borttagning för Azure Storage-blobar](../blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Lagra affärskritiska blobdata med oföränderlig lagring](../blobs/storage-blob-immutable-storage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: Skapa incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: Skapa incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: utför vanlig inträngande testning av dina Azure-resurser

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../security/benchmarks/overview.md)
- Läs mer om [Azures säkerhets bas linjer](../../security/benchmarks/security-baselines-overview.md)
