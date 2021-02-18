---
title: Azures säkerhets bas linje för Azure Kognitiv sökning
description: Azure Kognitiv sökning Security-datalinjen ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0618e4445629893fbe623df4bc937698bd307c7c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096562"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azures säkerhets bas linje för Azure Kognitiv sökning

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 1,0](../security/benchmarks/overview.md) till Azure kognitiv sökning. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure kognitiv sökning. **Kontroller** som inte är tillämpliga på Azure kognitiv sökning eller kunden har uteslutits.

Om du vill se hur Azure Kognitiv sökning fullständigt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga Azure kognitiv sökning säkerhets bas linje mappnings filen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: kontrol lera att alla distributioner av Microsoft Azure Virtual Network undernät har en nätverks säkerhets grupp som tillämpas med regler för att implementera åtkomst schema med minst privilegier. Tillåt endast åtkomst till programmets betrodda portar och IP-adressintervall. Distribuera Azure Kognitiv sökning med en privat Azure-slutpunkt, där det är möjligt, för att aktivera privat åtkomst till dina tjänster från det virtuella nätverket.

Kognitiv sökning stöder också ytterligare funktioner för nätverks säkerhet för hantering av åtkomst kontrol listor i nätverket. Konfigurera Sök tjänsten så att den endast tillåter kommunikation med betrodda källor genom att begränsa åtkomsten från vissa offentliga IP-adressintervall med hjälp av dess brand Väggs funktion.

- [Konfigurera privata slut punkter för Azure Kognitiv sökning](./service-create-private-endpoint.md)

- [Så här konfigurerar du Azure Kognitiv sökning-brandväggen](./service-configure-firewall.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

**Vägledning**: kognitiv sökning kan inte distribueras direkt till ett virtuellt nätverk. Men om ditt klient program eller data källor finns i ett virtuellt nätverk kan du övervaka och logga trafik för dessa komponenter i nätverket, inklusive förfrågningar som skickas till en Sök tjänst i molnet. Standard rekommendationer är att aktivera en nätverks säkerhets grupp flödes logg och skicka loggar till antingen Azure Storage eller en Log Analytics arbets yta. Du kan också använda Trafikanalys för insikter i trafik mönster.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: kognitiv sökning tillhandahåller inte en speciell funktion för att bekämpa en distribuerad denial-of-service-attack, men du kan aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till din kognitiv sökning tjänst för allmänt skydd.

- [Så här konfigurerar du DDoS-skydd](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera nätverks säkerhets grupp flödes loggar för nätverks säkerhets grupper som skyddar Azure-Virtual Machines (VM) som ska ansluta till din kognitiv sökning-tjänst. Skicka loggar till ett Azure Storage-konto för trafik granskning. 

Aktivera Network Watcher paket fångst om det behövs för att undersöka avvikande aktivitet.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar du Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: kognitiv sökning har inte stöd för identifiering av nätverks intrång, men som en avbrotts minskning kan du konfigurera brand Väggs regler för att ange de IP-adresser som accepteras av kognitiv söknings tjänsten. Konfigurera en privat slut punkt för att hålla Sök trafiken bort från det offentliga Internet.

- [Så här konfigurerar du Kundhanterade nycklar för data kryptering](./search-security-manage-encryption-keys.md)

- [Så här hämtar du kundhanterad viktig information från index och synonym Maps](./search-security-get-encryption-keys.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd service märken om du utnyttjar indexerare och färdighetsuppsättningar i kognitiv sökning för att representera ett intervall med IP-adresser som har behörighet att ansluta till externa resurser. 

Tillåt eller neka trafik till resurser genom att ange service tag-namnet (till exempel AzureCognitiveSearch) i lämpligt käll-eller mål fält för en regel. 

- [Tjänst taggar för virtuellt nätverk](../virtual-network/service-tags-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: kognitiv sökning inte har eller är beroende av nätverks resurser efter design. Klient program och data källor som är relaterade till ditt sökprogram kan finnas i ett virtuellt nätverk, men Sök tjänsten distribueras inte själv i nätverket. 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: du kan konfigurera kognitiv sökning med en privat Azure-slutpunkt för att integrera din Sök tjänst med ett virtuellt nätverk.  Använd resurs taggar för nätverks säkerhets grupper och andra resurser som rör nätverks säkerhets-och trafikflödet. Använd fältet Beskrivning för enskilda regler för nätverks säkerhets grupper för att dokumentera de regler som tillåter trafik till/från ett nätverk. 

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, t. ex. "Kräv tagg och dess värde", för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser. 

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar. 

- [Så här skapar du en privat slut punkt för Kognitiv sökning](./service-create-private-endpoint.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en Azure-Virtual Network](../virtual-network/quick-create-portal.md)

- [Filtrera nätverks trafik med regler för nätverks säkerhets grupper](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: kognitiv sökning inte har eller är beroende av nätverks komponenter, så konfigurationerna för dessa resurser kan inte övervakas.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: kognitiv sökning har inte stöd för att konfigurera dina egna synkroniserings källor för tid. Sök tjänsten förlitar sig på Microsoft Time Sync-källor och exponeras inte för kunder för konfiguration.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar som är relaterade till Kognitiv sökning via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.
Du kan också aktivera och inaktivera dessa data till Azure Sentinel eller en SIEM från tredje part.

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: diagnostik-och drift loggar ger insikt i de detaljerade åtgärderna i kognitiv sökning och är användbara för att övervaka tjänsten och för arbets belastningar som har åtkomst till din tjänst.  Om du vill samla in diagnostikdata aktiverar du loggning genom att ange var loggnings information lagras.

- [Samla in och analysera loggdata för Azure Kognitiv sökning](./search-monitor-logs.md)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: historiska data som matas in i diagnostiska mått bevaras av kognitiv sökning i 30 dagar som standard. För längre kvarhållning, se till att aktivera inställningen som anger ett lagrings alternativ för att spara loggade händelser och mått.

I Azure Monitor anger du din Log Analytics bevarande period för arbets ytan enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig lagring och arkivering. 

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar från din kognitiv sökning-tjänst för avvikande beteende. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad information till Azure Sentinel eller en SIEM från tredje part.

- [Samla in och analysera loggdata för Kognitiv sökning](./search-monitor-logs.md)

- [Visualisera Sök logg data i Power BI](./search-monitor-logs-powerbi.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Läs mer om Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Security Center med Log Analytics arbets yta för övervakning och avisering om avvikande aktivitet i säkerhets loggar och händelser. Du kan också aktivera och inaktivera data till Azure Sentinel.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: gäller inte för kognitiv sökning. Microsoft hanterar lösningen mot skadlig kod för den underliggande plattformen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: gäller inte för kognitiv sökning. Den skapar eller använder inte DNS-loggar.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: gäller inte för kognitiv sökning. Kommando rads granskning är inte tillgängligt för Kognitiv sökning.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure rollbaserad åtkomst kontroll (Azure RBAC) gör att du kan hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen.

Kognitiv sökning roller är associerade med behörigheter som stöder aktiviteter på service nivå hantering.  Dessa roller beviljar inte åtkomst till tjänstens slut punkt. Åtkomst till åtgärder mot slut punkten, (till exempel index hantering, index ifyllning och frågor om Sök data), använder API-nycklar för att autentisera begäran.

- [Ange roller för administrativ åtkomst till Azure Kognitiv sökning](./search-security-rbac.md)

- [Skapa och hantera API-nycklar för en Azure Kognitiv sökning-tjänst](./search-security-api-keys.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)
- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: gäller inte för kognitiv sökning. Det finns inget begrepp för standard lösen ord.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: kognitiv sökning har inte konceptet alla lokala eller Azure Active Directory-administratörs konton (Azure AD) som kan användas för att hantera index och åtgärder. 

Använd de inbyggda Azure AD-rollerna som måste tilldelas explicit för hanterings åtgärder. Anropa Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här använder du roller för administrativ åtkomst i Kognitiv sökning](./search-security-rbac.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: Använd SSO-autentisering med Azure Active Directory (Azure AD) för att få åtkomst till Sök tjänst information för hanterings åtgärder som stöds via Azure Resource Manager. 

Upprätta en process för att minska antalet identiteter och autentiseringsuppgifter genom att aktivera SSO för tjänsten med din organisations befintliga identiteter.

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: Aktivera Azure Active Directorys funktionen (Azure AD) Multi-Factor Authentication (MFA) och följ Security Center identitets-och åtkomst rekommendationer.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md) 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Multi-Factor Authentication (MFA) som kon figurer ATS för att logga in på och komma åt Azure-resurser.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 

- [Så här aktiverar du Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: gäller inte för kognitiv sökning. Den har inte stöd för användning av godkänd plats som villkor för åtkomst.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central-autentiserings-och auktoriserings system för hanterings uppgifter på tjänst nivå i Azure kognitiv sökning. Azure AD-identiteter ger inte åtkomst till Sök tjänstens slut punkt.  Åtkomst till åtgärder som index hantering, index ifyllning och frågor om Sök data är tillgängliga via API-nycklar.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Skapa och hantera API-nycklar för en Azure Kognitiv sökning-tjänst](./search-security-api-keys.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Använd Azure AD: s identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

Granska diagnostikloggar från Kognitiv sökning för aktivitet i Sök tjänstens slut punkt, till exempel index hantering, index ifyllning och frågor.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning](./search-monitor-usage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: åtkomst till Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor, så att du kan integrera med alla Siem-eller övervaknings verktyg.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azure-aktivitets loggar med Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (Azure AD) Identity Protection-funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder relaterade till användar identiteter. Mata in data i Azure Sentinel för ytterligare undersökning, efter behov.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md) 

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: gäller inte för kognitiv sökning. Customer Lockbox stöder inte Kognitiv sökning.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Resurser bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp eller Azure-brandvägg. Resurser som lagrar eller bearbetar känsliga data bör isoleras. Använd privat länk om du vill konfigurera en privat slut punkt till Kognitiv sökning.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md) 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Så här skapar du en privat slut punkt för Kognitiv sökning](./service-create-private-endpoint.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Använd en lösning från tredje part från Azure Marketplace i nätverks omkretser för att övervaka obehörig överföring av känslig information och blockera sådana överföringar samtidigt som du varnar information om informations säkerhet.

Microsoft hanterar den underliggande plattformen och hanterar allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md) 

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kognitiv sökning krypterar data under överföring med Transport Layer Security 1,2 och använder kryptering (SSL/TLS) hela tiden för alla anslutningar. Detta säkerställer att alla data krypteras "under överföring" mellan klienten och tjänsten.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för kognitiv sökning. Implementera en lösning från tredje part om det behövs för efterlevnad. 

Microsoft hanterar den underliggande plattformen och hanterar allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: för tjänst administration använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till nycklar och konfiguration. För innehålls åtgärder, till exempel indexering och frågor, använder Kognitiv sökning nycklar i stället för en identitets baserad åtkomst kontroll modell. Använd Azure RBAC för att kontrol lera åtkomst till nycklar.
- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

 
- [Så här använder du roller för administrativ åtkomst till Kognitiv sökning](./search-security-rbac.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: gäller inte för kognitiv sökning. Den här rikt linjen är avsedd för beräknings resurser. 

Microsoft hanterar den underliggande infrastrukturen för Kognitiv sökning och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: kognitiv sökning automatiskt krypterat indexerat innehåll i vila med Microsoft-hanterade nycklar. Om du behöver ytterligare skydd kan du lägga till standard kryptering med ett andra krypterings lager med hjälp av nycklar som du skapar och hanterar i Azure Key Vault.

- [Konfigurera Kundhanterade nycklar för data kryptering i Azure Kognitiv sökning](./search-security-manage-encryption-keys.md)

- [Förstå kryptering vid vila i Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av kognitiv sökning och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

- [Skapa aviseringar för Kognitiv sökning aktiviteter](./search-monitor-logs.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: för närvarande inte tillgänglig för kognitiv sökning.  För kluster som lagrar search service-innehåll ansvarar Microsoft för sårbarhets hantering av dessa kluster.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera en automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: gäller inte för kognitiv sökning. Microsoft utför sårbarhets hantering på underliggande system som stöder Kognitiv sökning tjänster.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: gäller inte för kognitiv sökning. Det finns inga standard risk-eller Poäng system i stället för sårbarhets öknings resultat.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar, protokoll och så vidare) i dina prenumerationer.  

Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.  

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription) 

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser med metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.
- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md) 

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md) 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md) 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: definiera en lista över godkända Azure-resurser relaterade till indexering och färdigheter bearbetning i kognitiv sökning.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Vi rekommenderar att du definierar en inventering av Azure-resurser som har godkänts för användning enligt organisationens principer och standarder tidigare, och sedan övervaka för icke godkända Azure-resurser med Azure policy eller Azure Resource Graph.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: gäller inte för kognitiv sökning. Den här vägledningen är avsedd för beräknings resurser.

Vi rekommenderar att du har en inventering av program vara som har bedömts som godkända enligt organisationens principer och säkerhets standarder, och övervaka för alla ej godkända program varu titlar som är installerade på dina Azure Compute-resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: gäller inte för kognitiv sökning. Det exponerar inte några beräknings resurser eller gör det möjligt att installera program vara på någon av dess resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att placera begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga eller identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md) 

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för program som körs på beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: för Service Management använder du villkorlig Azure-åtkomst för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". 

Kontrol lera åtkomsten till nycklarna som används för att autentisera begär Anden för alla andra åtgärder, särskilt de som är relaterade till innehåll med Kognitiv sökning.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fysiskt eller logiskt särskiljande program med hög risk

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för webb program som körs på Azure App Service-eller beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. search" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för Azure kognitiv sökning-resurser. Du kan också använda inbyggda Azure Policy definitioner för Kognitiv sökning tjänster som:

- Aktivera gransknings loggning för Azure-resurser

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation. 

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser. 

- [Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning](./security-controls-policy.md)

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar för dina kognitiv sökning tjänst resurser. 

Azure Resource Manager mallar kan användas för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning](./security-controls-policy.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner använder du Azure DevOps eller Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation om Azure databaser](/azure/devops/repos/index)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina kognitiv sökning tjänst resurser med Azure policy. 

Använd alias för att skapa anpassade principer för att granska eller framtvinga nätverkskonfigurationer. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser. 

Du kan också använda Azure Automation för att distribuera konfigurations ändringar och hantera princip undantag. 

- [Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning](./security-controls-policy.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Security Center för att utföra bas linjes ökningar av kognitiv sökning tjänst resurser.  Du kan också använda Azure Policy för att varna och granska dina resurspooler. 

- [Så här åtgärdar du rekommendationer i Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Azure Policy kontroll av efterlevnad för Azure Kognitiv sökning](./security-controls-policy.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Använd Azure Managed identiteter tillsammans med Azure Key Vault för att förenkla den hemliga hanteringen för dina moln program.
- [Använda hanterade identiteter för Azure-resurser](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Så här skapar du en Key Vault](../key-vault/general/quick-create-portal.md) 

- [Så här ger Key Vault autentisering med en hanterad identitet](../key-vault/general/assign-access-policy-portal.md) 

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Använd en Azure-hanterad identitet för att ge kognitiv sökning åtkomst till andra Azure-tjänster, till exempel Key Vault-och indexerare data källor med hjälp av en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Azure Key Vault utan autentiseringsuppgifter i din kod. 

- [Konfigurera en indexerare-anslutning till en data källa med hjälp av en hanterad identitet](./search-howto-managed-identities-data-sources.md)

- [Konfigurera Kundhanterade nycklar för data kryptering med hjälp av en hanterad identitet](./search-security-manage-encryption-keys.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: gäller inte för kognitiv sökning. Den är inte värd för kod och har inga autentiseringsuppgifter att identifiera.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: Använd centralt hanterad program vara för program mot skadlig kod

**Vägledning**: gäller inte för kognitiv sökning. Den här rekommendationen är avsedd för beräknings resurser.

Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Kognitiv sökning), men det körs inte på kund innehåll.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvars område**: inte tillämpligt

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: för skanning av allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel Kognitiv sökning, Blob Storage, Azure SQL Database och så vidare. 

Det är ditt ansvar att i förväg genomsöka allt innehåll som laddas upp till Azure-resurser som inte är Compute. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för program mot skadlig kod uppdateras

**Vägledning**: gäller inte för kognitiv sökning. Den tillåter inte att lösningar mot skadlig kod installeras på resurserna. För den underliggande plattformen hanterar Microsoft uppdateringar av program vara och signaturer mot skadlig kod. 

För alla beräknings resurser som ägs av din organisation och används i din Sök lösning följer du rekommendationer i Security Center, beräknar &amp; program för att se till att alla slut punkter är uppdaterade med de senaste signaturerna. För Linux använder du en lösning mot skadlig program vara från tredje part.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: innehåll som lagras i en Sök tjänst kan inte säkerhets kopie ras via Azure Backup eller någon annan inbyggd mekanism, men du kan återskapa ett index från program käll koden och de primära data källorna, eller skapa ett anpassat verktyg för att hämta och lagra indexerat innehåll.

- [GitHub-index – exempel på säkerhets kopierings återställning](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: kognitiv sökning för närvarande inte har stöd för automatisk säkerhets kopiering för data i en Sök tjänst och måste säkerhets kopie ras via en manuell process.  Du kan också säkerhetskopiera Kundhanterade nycklar i Azure Key Vault. 

- [Säkerhetskopiera och återställa ett Azure Kognitiv sökning-index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Säkerhetskopiera Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: kognitiv sökning för närvarande inte har stöd för automatisk säkerhets kopiering för data i en Sök tjänst och måste säkerhets kopie ras och återställas via en manuell process.  Regelbundet utföra Data återställning av innehåll som du säkerhetskopierade manuellt för att säkerställa den kompletta integriteten för säkerhets kopieringen.

- [Säkerhetskopiera och återställa ett Azure Kognitiv sökning-index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Så här återställer du Key Vault nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: kognitiv sökning för närvarande inte har stöd för automatisk säkerhets kopiering för data i en Sök tjänst och måste säkerhets kopie ras via en manuell process.  Du kan också säkerhetskopiera Kundhanterade nycklar i Azure Key Vault. 

Aktivera mjuk borttagning och tömning av skydd i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning. Om Azure Storage används för att lagra manuella säkerhets kopieringar aktiverar du mjuk borttagning för att spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort. 

- [Säkerhetskopiera och återställa ett Azure Kognitiv sökning-index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Aktivera mjuk borttagning och rensningsskydd i Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Mjuk borttagning för Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller den analytiskt som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Dessutom kan du markera prenumerationer med taggar och skapa ett namngivnings system för att identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data. Det är ditt ansvar att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST-publikation, "guide till test, utbildning och övnings program för IT-planer och-funktioner"](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller regelbundet. Du kan använda Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.
- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)