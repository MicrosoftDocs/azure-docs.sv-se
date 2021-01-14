---
title: Azures säkerhets bas linje för batch
description: Bas linje för grupp säkerhet ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c27ef1af3e439b22f00f9247b5270118bbe9ca89
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197718"
---
# <a name="azure-security-baseline-for-batch"></a>Azures säkerhets bas linje för batch

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets prestandatest version 1,0](../security/benchmarks/overview-v1.md) till batch. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för batch. **Kontroller** som inte gäller för batch har uteslutits.

 
Om du vill se hur batch fullständigt mappar till Azures säkerhets benchmark kan du läsa mer i den [fullständiga mappnings filen för batch-säkerhet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Distribuera Azure Batch pool (er) i ett virtuellt nätverk. Om du vill tillåta att datornoder kommunicerar säkert med andra virtuella datorer eller med ett lokalt nätverk kan du etablera poolen i ett undernät i ett virtuellt Azure-nätverk. Genom att distribuera poolen i ett virtuellt nätverk får du också kontroll över nätverks säkerhets gruppen (NSG) som används för att skydda enskilda noders nätverks gränssnitt (NIC), samt under nätet. Konfigurera NSG så att trafik från endast betrodda IP-/locations på Internet tillåts.

Vid behov inaktiverar du offentlig nätverks åtkomst genom att använda Azures privata länk för att ansluta till det Azure Batch kontot via en privat slut punkt. Tjänsten Azure Private Link är skyddad och accepterar bara anslutningar från autentiserade och auktoriserade privata slut punkter. Du kan också begränsa anslutning och identifiering genom att inaktivera offentligt exponerade RDP/SSH-slutpunkter för Compute-noder i en batch-pool.

- [Så här skapar du en Azure Batch pool i en Virtual Network](batch-virtual-network.md)

- [Så här skapar du ett Azure Batch konto med nätverks åtkomst inaktiverat](private-connectivity.md)

- [Så här skapar du en privat slut punkt](../private-link/create-private-endpoint-portal.md)

- [Så här nekar du åtkomst till RDP/SSH-trafik](pool-endpoint-configuration.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Använd Azure Security Center och åtgärda nätverks skydds rekommendationer som är relaterade till den virtuella nätverks-/nätverks säkerhets gruppen (NSG) som är associerad med din batch-pool. Aktivera flödes loggar på NSG som används för att skydda batch-poolen och skicka loggar till ett Azure Storage konto för trafik granskning. Du kan också skicka NSG Flow-loggar till en Azure Log Analytics-arbetsyta och använda Azure Trafikanalys för att ge insikter om trafikflöde i Azure-molnet. Några av fördelarna med Azure Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks fel konfigurationer.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Aktivera standard skydd för Azure-DDoS (distributed denial-of-Service) på det virtuella nätverket som skyddar Azure Batch-poolen för skydd mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du DDoS-skydd](/azure/virtual-network/manage-ddos-protection)

- [Förstå Azure Security Center integrerad Hot information](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera flödes loggar i nätverks säkerhets gruppen (NSG) som används för att skydda Azure Batch-poolen och skicka loggar till ett Azure Storage konto för trafik granskning.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: om det behövs för kompatibilitet väljer du en virtuell nätverks installation från Azure Marketplace som stöder intrångs identifierings system (ID) och IP-funktioner (intrångs skydd system) med funktioner för nytto lasts granskning.

Om intrångs identifiering och/eller skydd som baseras på nytto lasts granskning inte är ett krav kan du använda Azure-brandväggen med hot information. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

Distribuera Azure-brandväggen med en offentlig IP-adress i samma virtuella nätverk som Azure Batch pool-noderna. Konfigurera Network Address Translation (NAT) regler mellan betrodda platser på Internet och de privata IP-adresserna för dina noder i poolen. I Azure-brandväggen, under hot information, konfigurerar du "varning och neka" för att blockera aviseringar och blockera trafik till/från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna kommer från Microsoft Threat Intelligence-flödet och endast de högsta konfidens posterna ingår. 

- [Så här skapar du en Azure Batch pool i en Virtual Network](batch-virtual-network.md)

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: Använd taggar för virtuella nätverks tjänster för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper eller Azure-brandväggar som är associerade med Azure Batch pool (er). Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardkonfigurationer för nätverks resurser som är kopplade till Azure Batch pool (er) med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft.BatCH" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Batch-pooler.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks tjänst grupper (NSG: er) och andra resurser som rör nätverks säkerhets-och trafikflöden som är associerade med dina Azure Batch-pooler. För enskilda NSG-regler använder du fältet Beskrivning för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure Policy definitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att söka efter eller utföra åtgärder på resurser baserat på deras taggar.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Så här skapar du ett virtuellt nätverk](../virtual-network/quick-create-portal.md)

- [Så här skapar du en NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till Azure Batch pooler. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log-view) 

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: onboard Azure Batch-konto som ska Azure Monitors för att samla in säkerhets data som genereras av kluster enheterna. Använd anpassade frågor för att identifiera och svara på hot i miljön.  För Azure Batch övervakning på resurs nivå använder du batch-API: erna för att övervaka eller fråga efter status för dina resurser, inklusive jobb, aktiviteter, noder och pooler.

- [Så här publicerar du ett Azure Batch-konto i Azure Monitor](batch-diagnostics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: om du vill övervaka Azure Batch på konto nivå övervakar du varje batch-konto med hjälp av funktionerna i Azure Monitor. Azure Monitor samlar in Mät värden och eventuella diagnostiska loggar för resurser som omfattas på samma nivå som ett batch-konto, till exempel pooler, jobb och uppgifter. Samla in och Använd dessa data manuellt eller program mässigt för att övervaka aktiviteter i ditt batch-konto och för att diagnosticera problem.

För övervakning av Azure Batch på resurs nivå använder du Azure Batch-API: er för att övervaka eller fråga efter status för dina resurser, inklusive jobb, aktiviteter, noder och pooler.

- [Så här konfigurerar du Azure Batch övervakning och loggning på konto nivå](monitoring-overview.md)

- [Förstå batch-övervakning på resurs nivå](monitoring-overview.md#batch-resource-monitoring)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

#### <a name="azure-policy-built-in-definitions"></a>Azure Policy inbyggda definitioner

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system

**Vägledning**: Azure Monitor samlar in Mät värden och diagnostikloggar för resurser i ditt Azure Batch-konto. Samla in och använda dessa data på flera olika sätt för att övervaka ditt Azure Batch-konto och diagnostisera problem. Du kan också konfigurera mått varningar så att du får meddelanden när ett mått når ett angivet värde.

Om det behövs kan du ansluta till dina noder för enskilda pooler via SSH (Secure Shell) eller Remote Desktop Protocol (RDP) för att få åtkomst till lokala operativ system loggar.

- [Så här samlar du in diagnostikloggar från ditt Azure Batch-konto](batch-diagnostics.md#batch-diagnostics)

- [Fjärrans luta till Azure Batch pool-noder](/azure/batch/batch-api-basics#error-handling)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: publicera Azure Batch konto till Azure Monitor. Kontrol lera att den Azure Log Analytics-arbetsyta som används har den logg kvarhållningsperiod som angetts enligt organisationens regler för efterlevnad

- [Så här konfigurerar du Azure Batch övervakning och loggning](monitoring-overview.md)

- [Så här konfigurerar du lagrings perioden för Azure Log Analytics-arbetsytan](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: Skapa Azure Batch mått varningar som utlöses när värdet för ett angivet mått överskrider ett visst tröskelvärde.

- [Så här konfigurerar du Azure Batch mått aviseringar](batch-diagnostics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Skapa Azure Batch mått varningar som utlöses när värdet för ett angivet mått överskrider ett visst tröskelvärde.

- [Så här konfigurerar du Azure Batch mått aviseringar](batch-diagnostics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod

**Vägledning**: Använd Windows Defender på dina individuella batch-noder när det gäller Windows-operativsystem eller ange en egen lösning för program mot skadlig kod om du använder Linux.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor

**Vägledning**: implementera en lösning från tredje part för DNS-loggning.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning

**Vägledning**: Konfigurera konsol loggning och PowerShell-avskrift manuellt på varje nod-basis.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla posten för det lokala administratörs kontot som skapas vid etablering av Azure Batch-poolen samt andra konton som du skapar. Om Azure Active Directory-integrering används, har Azure AD dessutom inbyggda roller som måste tilldelas explicit och som därför kan frågas. Använd Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: när du konfigurerar en Azure Batch pool får du möjlighet att skapa lokala dator konton. Det finns inga standard lösen ord att ändra, men du kan ange olika lösen ord för SSH (Secure Shell) och Remote Desktop Protocol (RDP)-åtkomst. När Azure Batch poolen har kon figurer ATS kan du generera en slumpmässig användare för enskilda noder i Azure Portal eller via Azure Resource Manager API.

- [Så här lägger du till en användare till en speciell Compute-nod](/rest/api/batchservice/computenode/adduser)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: integrera autentisering för Azure Batch program med Azure Active Directory. Skapa principer och procedurer kring användningen av dedikerade administrativa konton.

Dessutom kan du använda rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Så här autentiserar du batch-program med Azure Active Directory](batch-aad-auth.md)

- [Övervaka identitet och åtkomst med Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: integrera autentisering för Azure Batch program med Azure Active Directory. Aktivera Azure AD multifaktor-autentisering och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering.

 

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd Paw (arbets stationer med privilegie rad åtkomst) med multifaktorautentisering konfigurerad för att logga in på och konfigurera dina Azure Batch-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: om du har integrerat autentisering för Azure Batch program med Azure Active Directory använder du Azure Active Directory säkerhets rapporter för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: om du har integrerat autentisering för Azure Batch program med Azure Active Directory kan du använda villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory som central autentiserings-och auktoriserings system och integrera autentisering för Azure Batch program med Azure AD. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

- [Så här autentiserar du batch-program med Azure AD](batch-aad-auth.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarnas åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: skapa diagnostikinställningar för Azure Active Directory användar konton, skicka gransknings loggar och inloggnings loggar till en Azure Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Azure Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory risk identifieringar och identitets skydds funktion för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier  

**Vägledning**: inte tillgänglig; Customer Lockbox ännu inte stöd för Azure Batch.
 
- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure Batch pooler bör åtskiljas av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas med nätverks säkerhets grupper (NSG). Azure Batch data ska finnas i ett skyddat Azure Storage-konto.

- [Så här skapar du en Azure Batch pool i en Virtual Network](batch-virtual-network.md)

- [Skydda Azure Storage-konton](/azure/storage/common/storage-security-guide)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: för Azure Storage konton som är kopplade till dina Azure Batch-pooler som innehåller känslig information, markerar du dem som känsliga med taggar och skyddar dem med Azures bästa praxis.

Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

- [Skydda Azure Storage-konton](/azure/storage/common/storage-security-guide)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Microsoft Azure resurser förhandlar TLS 1,2 som standard. Se till att alla klienter som ansluter till Azure Batch pooler eller data lager (Azure Storage-konton) kan förhandla TLS 1,2 eller senare.

Se till att HTTPS krävs för åtkomst till det lagrings konto som innehåller dina Azure Batch data.

- [Förstå Azure Storage konto kryptering vid överföring](../storage/blobs/security-recommendations.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: för Azure Storage konton som är kopplade till dina Azure Batch-pooler som innehåller känslig information, markerar du dem som känsliga med taggar och skyddar dem med Azures bästa praxis.

Funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

- [Skydda Azure Storage-konton](/azure/storage/common/storage-security-guide)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till hanterings planet för Azure-resurser, inklusive batch-konto, batch-pool (-er) och lagrings konton.

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: Använd värdbaserade data förlust skydd för att genomdriva åtkomst kontroll

**Vägledning**: funktionerna för data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Storage-eller beräknings resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: för lagrings konton som är kopplade till ditt Azure Batch-konto rekommenderas att Microsoft kan hantera krypterings nycklar, men du har möjlighet att hantera dina egna nycklar om det behövs.

Azure Disk Encryption kan användas för att skydda och skydda dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Alla hanterade diskar, ögonblicks bilder, avbildningar och data som skrivs till befintliga diskar krypteras automatiskt i vila med plattforms hanterade nycklar.

- [Hantera krypterings nycklar för Azure Storage konton](/azure/storage/common/storage-encryption-keys-portal)

- [Så här konfigurerar du Kundhanterade krypterings nycklar](/azure/storage/common/storage-encryption-keys-portal)

- [Så här skapar du en pool med disk kryptering aktiverat](disk-encryption.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser som är relaterade till eller kopplade till dina Azure Batch konton/pooler.

Konfigurera diagnostikinställningar för lagrings konton som är kopplade till Azure Batch pool för att övervaka och logga alla CRUD-åtgärder mot data från poolen.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/platform/alerts-activity-log.md)

- [Så här aktiverar du ytterligare loggning/granskning för ett Azure Storage konto](../storage/common/storage-monitor-storage-account.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: för Azure Batch pool-noder ansvarar du för hantering av lösningen för sårbarhets hantering.

Om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du manuellt installera sårbarhets bedömnings agenter på noder i batch-pooler och hantera noder via respektive Portal.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: Distribuera automatiserad hanterings lösning för operativ system

**Vägledning**: Microsoft för att underhålla och uppdatera Base Azure Batch pool Node-avbildningar. Se till att operativ systemet för Azure Batch pooler fortfarande har uppdaterats under giltighets tiden för klustrets livstid, vilket kan kräva att automatiska uppdateringar, övervakning av noderna eller regelbunden omstarter utförs.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Distribuera automatiserad korrigerings hanterings lösning för program varu titlar från tredje part

**Vägledning**: se till att Azure Batch Pools program från tredje part fortfarande har uppdaterats under giltighets tiden för klustrets livs längd, vilket kan kräva att automatiska uppdateringar, övervakning av noder eller regelbunden omstarter utförs.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: jämför sökningar efter säkerhets risker

**Vägledning**: om du har en Rapid7, Qualys eller någon annan plattforms prenumeration för sårbarhets hantering kan du använda leverantörens Portal för att visa och jämföra säkerhets risker för säkerhets kopiering.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av genomsöknings verktyget från tredje part.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga/identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk osv.) i din prenumeration (er). Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](/azure/governance/management-groups/create)

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: definiera en lista över godkända Azure-resurser och godkänd program vara för beräknings resurser

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd Azure Resource Graph för att fråga/identifiera resurser i dina prenumerationer. Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: övervaka för program som inte godkänts i beräknings resurser

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att övervaka klusternoder för program som inte godkänts.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: ta bort icke godkända Azure-resurser och program

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att övervaka klusternoder för program som inte godkänts.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="68-use-only-approved-applications"></a>6,8: Använd endast godkända program

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att förhindra att obehöriga program körs.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner: 
- Otillåtna resurstyper 
- Tillåtna resurstyper 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: underhåll en inventering av godkända program varu titlar

**Vägledning**: för Azure Batch pool-noder implementerar du en lösning från tredje part för att förhindra att obehöriga filtyper körs.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.BatCH" om du vill skapa anpassade principer för att granska eller tillämpa konfigurationen av dina Azure Batch-konton och pooler.

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: upprätta säkra konfigurationer för operativ system

**Vägledning**: upprätta säkra konfigurationer för dina noder i batch-poolen.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar för Azure-resurser som är relaterade till ditt batch-konto och pooler (till exempel virtuella nätverk, undernät, Azure-brandväggar, Azure Storage-konton osv.). Du kan använda Azure Policy alias från följande namn rymder för att skapa anpassade principer:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: underhåll säkra konfigurationer för operativ system

**Vägledning**: Azure Batch pool operativ Systems avbildningar som hanteras och underhålls av Microsoft. Du ansvarar för att implementera tillstånds konfigurationen på operativ system nivå.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Azure Batch konton, pooler eller relaterade resurser använder du Azure-databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentation om Azure databaser](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: lagra anpassade operativ Systems avbildningar på ett säkert sätt

**Vägledning**: om du använder anpassade avbildningar för Azure Batch pooler använder du rollbaserad åtkomst kontroll (RBAC) för att se till att endast behöriga användare kan komma åt avbildningarna.

- [Förstå RBAC i Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner för att varna, granska och genomdriva Azure Batch-relaterade datorkonfigurationer.  Använd Azure Policy alias i namn rymden "Microsoft.BatCH" för att skapa anpassade principer för dina Azure Batch-konton och pooler. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: Distribuera konfigurations hanterings verktyg för operativ system

**Vägledning**: implementera en lösning från tredje part för att bibehålla önskat tillstånd för dina Azure Batch-poolens noders operativ system.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.BatCH" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för din Azure Batch-instans. Du kan också använda inbyggda principer som skapats specifikt för Azure Batch eller de resurser som används av Azure Batch, till exempel:
- Undernät ska associeras med en nätverks säkerhets grupp-lagrings konton bör använda en tjänst slut punkt för virtuellt nätverk
- Diagnostikloggar i batch-konton måste vara aktiverade

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: implementera automatisk konfigurations övervakning för operativ system

**Vägledning**: implementera en lösning från tredje part för att övervaka status för dina Azure Batch-poolens noders operativ system.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Azure Key Vault kan användas med Azure Batch distributioner för att hantera nycklar för lagring av pooler inom Azure Storage konton.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Azure Key Vault](../key-vault/general/quick-create-portal.md)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)
- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: Använd en centralt hanterad program vara mot skadlig kod

**Vägledning**: Använd Windows Defender på dina individuella Azure Batch pool-noder i fallet med Windows-operativsystem eller ange en egen lösning för program mot skadlig kod om du använder Linux.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som stöder Azure-tjänster (till exempel Azure Batch), men det körs inte på kund innehåll.

Genomsök alla filer som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage osv. Microsoft kan inte komma åt kund information i dessa instanser.

- [Förstå Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: se till att program vara och signaturer för skadlig program vara uppdateras

**Vägledning**: Använd Windows Defender på dina individuella Azure Batch pool-noder när det gäller Windows-operativsystem och se till att automatisk uppdatering är aktiverat. Ange din egen lösning för program mot skadlig kod om du använder Linux.

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: när du använder ett Azure Storage konto för data lagret för Azure Batch-poolen väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS). 

- [Så här konfigurerar du redundans för Azure Storage konton](../storage/common/storage-redundancy.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: när du använder ett Azure Storage konto för data lagret för Azure Batch-poolen väljer du lämpligt alternativ för REDUNDANS (LRS, ZRS, GRS, RA-GRS). Se till att nycklarna säkerhets kopie ras om du använder Azure Key Vault för någon del av din Azure Batch-distribution.

- [Så här konfigurerar du redundans för Azure Storage konton](../storage/common/storage-redundancy.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om du hanterar dina egna nycklar för Azure Storage konton eller andra resurser som är relaterade till din Azure Batch implementering, kan du regelbundet testa återställning av säkerhetskopierade nycklar.

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Så här återställer du en kundhanterad nyckel med PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: om Azure Key Vault används för att lagra nycklar som är relaterade till Azure Batch pool lagrings konton, aktiverar du Soft-Delete i Azure Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Så här aktiverar du mjuk borttagning i Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: kontrol lera att det finns skriftliga svars planer för incidenter som definierar personal roller och faser för incident hantering/hantering.

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till aviseringar, som hjälper dig att prioritera i vilken ordning du deltar i varje avisering, så att när en resurs komprometteras kan du komma åt den direkt. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olaglig eller obehörig part.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: – [Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft Managed Cloud-infrastruktur, tjänster och program, nedan: 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
