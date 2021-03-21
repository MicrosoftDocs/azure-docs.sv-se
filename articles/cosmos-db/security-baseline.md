---
title: Azures säkerhets bas linje för Azure Cosmos DB
description: Azure Cosmos DB säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5f9cf6dba0a1e670e3a27cb3546de9237466e7ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659434"
---
# <a name="azure-security-baseline-for-azure-cosmos-db"></a>Azures säkerhets bas linje för Azure Cosmos DB

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure Cosmos dB. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Cosmos dB. **Kontroller** som inte är tillämpliga på Azure Cosmos DB har uteslutits.

 
Om du vill se hur Azure Cosmos DB helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Cosmos DB mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: med hjälp av en privat Azure-länk kan du ansluta till ett Azure Cosmos-konto via en privat slut punkt. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan också minska risken för data exfiltrering genom att konfigurera en strikt uppsättning utgående regler i en nätverks säkerhets grupp (NSG) och associera NSG med ditt undernät.

Du kan också använda tjänstens slut punkter för att skydda ditt Azure Cosmos-konto. Genom att aktivera en tjänst slut punkt kan du konfigurera Azure Cosmos-konton för att tillåta åtkomst från ett speciellt undernät i ett virtuellt Azure-nätverk. När Azure Cosmos DB tjänstens slut punkt har Aktiver ATS kan du begränsa åtkomsten till ett Azure Cosmos-konto med anslutningar från ett undernät i ett virtuellt nätverk.

Du kan också skydda data som lagras i ditt Azure Cosmos-konto med hjälp av IP-brandväggar. Azure Cosmos DB stöder IP-baserade åtkomst kontroller för inkommande brand Väggs stöd. Du kan ange en IP-brandvägg på Azure Cosmos-kontot med hjälp av Azure Portal, Azure Resource Manager mallar eller via Azure CLI eller Azure PowerShell.

- [Översikt av Azure Private Link](../private-link/private-link-overview.md)

- [Så här konfigurerar du en privat slut punkt för Azure Cosmos DB](how-to-configure-private-endpoints.md) 

- [Så här skapar du en nätverks säkerhets grupp med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

- [Konfigurera IP-brandvägg i Cosmos DB](how-to-configure-firewall.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft.DocumentDB**:

[!INCLUDE [Resource Policy for Microsoft.DocumentDB 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.documentdb-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverks gränssnitt

**Vägledning**: Använd Azure Security Center och följ rekommendationer för nätverks skydd för att skydda nätverks resurser som är relaterade till ditt Azure Cosmos-konto.

När virtuella datorer distribueras i samma virtuella nätverk som ditt Azure Cosmos-konto kan du använda en nätverks säkerhets grupp (NSG) för att minska risken för data exfiltrering. Aktivera NSG Flow-loggar och skicka loggar till ett Azure Storage-konto för trafik granskningar. Du kan också skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflöde i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Förstå nätverks säkerhet som tillhandahålls av Azure Security Center](../security-center/security-center-network-recommendations.md)

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

**Vägledning**: Använd funktionen för resurs delning mellan ursprung (CORS) för att aktivera ett webb program som körs under en domän för att få åtkomst till resurser i en annan domän. Webbläsare implementerar en säkerhets begränsning som kallas samma-Origin-princip som förhindrar att en webb sida anropar API: er i en annan domän. CORS är dock ett säkert sätt att tillåta att ursprungs domänen anropar API: er i en annan domän. När du har aktiverat CORS-stöd för ditt Azure Cosmos-konto utvärderas bara autentiserade begär Anden för att avgöra om de är tillåtna enligt de regler som du har angett.

- [Konfigurera resurs delning mellan ursprung](how-to-configure-cross-origin-resource-sharing.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd avancerat skydd (ATP) för Azure Cosmos dB. ATP för Azure Cosmos DB ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Det här skydds lagret gör att du kan åtgärda hot och integrera dem med centrala säkerhets övervaknings system.

Aktivera DDoS Protection standard på de virtuella nätverk som är kopplade till dina Azure Cosmos DB instanser för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga eller oanvända Internet-IP-adresser.

- [Så här konfigurerar du Azure Cosmos DB Avancerat skydd](cosmos-db-advanced-threat-protection.md)

- [Så här konfigurerar du DDoS-skydd](/azure/virtual-network/manage-ddos-protection)

- [Förstå Azure Security Center integrerad Hot information](/azure/security-center/security-center-alerts-service-layer)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="15-record-network-packets"></a>1,5: registrera nätverks paket

**Vägledning**: Aktivera flödes loggar för nätverks säkerhets grupper (NSG) och skicka loggar till ett lagrings konto för trafik granskning. Du kan skicka NSG Flow-loggar till en Log Analytics arbets yta och använda Trafikanalys för att ge insikter i trafikflödet i Azure-molnet. Några av fördelarna med Trafikanalys är möjligheten att visualisera nätverks aktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödes mönster och hitta nätverks problem.

- [Så här aktiverar du NSG Flow-loggar](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera nätverksbaserade intrångs identifiering/system för skydd mot intrång (ID/IP-adresser)

**Vägledning**: Använd avancerat skydd (ATP) för Azure Cosmos dB. ATP för Azure Cosmos DB ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Det här skydds lagret gör att du kan åtgärda hot och integrera dem med centrala säkerhets övervaknings system. 

- [Så här konfigurerar du Cosmos DB Avancerat skydd](cosmos-db-advanced-threat-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

**Vägledning**: för resurser som behöver åtkomst till ditt Azure Cosmos-konto använder du Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. AzureCosmosDB) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Mer information om att använda service märken](../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser med Azure policy. Använd Azure Policy alias i namn områdena "Microsoft.DocumentDB" och "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Cosmos DB instanser. Du kan också använda inbyggda princip definitioner för Azure Cosmos DB, till exempel:

- Distribuera Avancerat skydd för Cosmos DB-konton

- Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, rollbaserad åtkomst kontroll i Azure (Azure RBAC) och principer i en enda skiss definition. Du kan enkelt använda skissen för nya prenumerationer, miljöer och finjustera kontroll och hantering genom versions hantering.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

**Vägledning**: Använd taggar för nätverks resurser som är associerade med din Azure Cosmos DB-distribution för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka konfigurationer av nätverks resurser och identifiera ändringar för nätverks resurser som är relaterade till dina Azure Cosmos DB-instanser. Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska nätverks resurser sker. 

- [Visa och hämta Azure aktivitets logg händelser](/azure/azure-monitor/platform/activity-log-view)

- [Så här skapar du aviseringar i Azure Monitor](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genereras av Azure Cosmos dB. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda lagrings konton för långsiktig/lagring. Du kan också använda data på kort till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM). 

- [Så här aktiverar du diagnostikloggar för Azure Cosmos DB](/azure/cosmos-db/logging)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för Azure Cosmos DB och skicka loggarna till en Log Analytics arbets yta eller ett Azure Storage-konto. Diagnostikinställningar i Azure Cosmos DB används för att samla in resurs loggar. Dessa loggar samlas in per begäran och kallas även för **data Plans loggar**. Några exempel på data Plans åtgärderna är ta bort, infoga och läsa. 

Du kan också aktivera autentiseringsinställningar för Azure aktivitets logg och skicka loggarna till samma Log Analytics arbets yta som du använder för Azure Cosmos DB loggar.

- [Så här aktiverar du diagnostikinställningar för Azure Cosmos DB](/azure/cosmos-db/logging)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperioden för Log Analytics arbets ytor som är kopplade till dina Azure Cosmos DB instanser enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: du kan utföra frågor i Log Analytics en arbets yta för att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de Azure Cosmos DB loggar som du har samlat in.

- [Så här kör du frågor för Azure Cosmos DB i Log Analytics arbets ytor](monitor-cosmos-db.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: i Azure Security Center aktiverar du Avancerat skydd för Azure Cosmos dB för att övervaka avvikande aktiviteter i säkerhets loggar och händelser. Aktivera diagnostikinställningar i Azure Cosmos DB och skicka loggar till en Log Analytics arbets yta.

 

Du kan också publicera din Log Analytics-arbetsyta till Azure Sentinel eftersom den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem. Dessutom kan du skapa anpassade logg aviseringar i Log Analytics arbets ytan med Azure Monitor.

- [Lista med aviseringar för skydd mot hot för Azure Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Skapa, Visa och hantera logg aviseringar med Azure Monitor](/azure/azure-monitor/platform/alerts-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: du kan använda fönstret identitets-och åtkomst kontroll (IAM) i Azure Portal för att konfigurera rollbaserad åtkomst kontroll (RBAC) och underhålla inventeringen på Azure Cosmos DB resurser. Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Azure Active Directory (Azure AD). Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper.

Azure Cosmos DB tillhandahåller inbyggd Azure RBAC för vanliga hanterings scenarier i Azure Cosmos DB. En person som har en profil i Azure AD kan tilldela dessa Azure-roller till användare, grupper, tjänstens huvud namn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB resurser.

Du kan också använda Azure AD PowerShell-modulen för att utföra adhoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

Dessutom kan vissa åtgärder i Azure Cosmos DB styras med Azure AD och konto-/regionsspecifika huvud nycklar. Använd konto inställningen ' disableKeyBasedMetadataWriteAccess ' för att kontrol lera åtkomst till nyckeln.

- [Förstå rollbaserad åtkomst kontroll i Azure Cosmos DB](role-based-access-control.md)

- [Bygg egna anpassade roller med hjälp av Azure Cosmos DB åtgärder (Microsoft.DocumentDB-namnrymd)](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb)

- [Skapa en ny roll i Azure AD](../role-based-access-control/custom-roles.md)

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Begränsa användaråtkomst till endast dataåtgärder](how-to-restrict-user-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: begreppet standard eller tomma lösen ord finns inte i relation till Azure Active Directory (Azure AD) eller Azure Cosmos dB. I stället använder Azure Cosmos DB två typer av nycklar för att autentisera användare och ge åtkomst till dess data och resurser. huvud nycklar och resurs-token. Nycklarna kan återskapas när som helst.

- [Förstå säker åtkomst till data i Azure Cosmos DB](secure-access-to-data.md)

- [Återskapa Azure Cosmos DB nycklar](https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys)

- [Få åtkomst till nycklar via programmering med hjälp av Azure AD](certificate-based-authentication.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: ej tillämpligt; Azure Cosmos DB stöder inte administratörs konton. All åtkomst är integrerad med Azure Active Directory (Azure AD) och rollbaserad åtkomst kontroll i Azure (Azure RBAC).

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: Azure Cosmos DB använder två typer av nycklar för att auktorisera användare och har inte stöd för Single Sign-On (SSO) på data planet nivå. Åtkomst till kontroll planet för Cosmos DB är tillgänglig via REST API och stöder SSO. Du autentiserar genom att ange Authorization-huvudet för dina begär anden till en JSON Web Token som du får från Azure Active Directory (Azure AD).

- [Förstå Azure Database för Cosmos DB REST API](/rest/api/cosmos-db/)

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Privileged Access-arbetsstationer (Paw) med multifaktorautentisering konfigurerad för att logga in på och konfigurera Azure-resurser. 

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](/security/compass/privileged-access-devices)
 
- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd avancerat skydd (ATP) för Azure Cosmos dB. ATP för Azure Cosmos DB ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Det här skydds lagret gör att du kan åtgärda hot och integrera dem med centrala säkerhets övervaknings system.

Dessutom kan du använda Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](/azure/active-directory/reports-monitoring/concept-risk-events)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Konfigurera plats villkoret för en princip för villkorlig åtkomst och hantera dina namngivna platser. Med namngivna platser kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner. Du kan begränsa åtkomsten till känsliga resurser, till exempel Azure Cosmos DB-instanser, till dina konfigurerade namngivna platser.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

- [Så här konfigurerar och hanterar du Azure AD-autentisering med Azure SQL](/azure/sql-database/sql-database-aad-authentication-configure)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du kan skapa diagnostikinställningar för Azure Active Directory (Azure AD)-användar konton, skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta där du kan konfigurera önskade aviseringar.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd avancerat skydd (ATP) för Azure Cosmos dB. ATP för Azure Cosmos DB ger ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos-konton. Det här skydds lagret gör att du kan åtgärda hot och integrera dem med centrala säkerhets övervaknings system.

Du kan också använda Azure Active Directory (Azure AD) identitets skydd och risk identifierings funktion för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Dessutom kan du mata in loggar i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att under lätta spårning Azure Cosmos DB instanser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Azure Cosmos DB instanser separeras av ett virtuellt nätverk/undernät, taggas på lämpligt sätt och skyddas inom en nätverks säkerhets grupp (NSG) eller Azure-brandvägg. Azure Cosmos DB instanser som lagrar känsliga data bör isoleras. Med hjälp av en privat Azure-länk kan du ansluta till ett Azure Cosmos DB instans konto via en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i ett undernät i det virtuella nätverket. Du kan sedan begränsa åtkomsten till de valda privata IP-adresserna. 

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du hanterings grupper](/azure/governance/management-groups/create)

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Så här konfigurerar du en privat slut punkt för Azure Cosmos DB](how-to-configure-private-endpoints.md)

- [Så här skapar du en nätverks säkerhets grupp med en säkerhets konfiguration](../virtual-network/tutorial-filter-network-traffic.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: du kan distribuera Avancerat skydd för Azure Cosmos DB, vilket kommer att identifiera avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser. Den kan för närvarande utlösa följande aviseringar:

- Åtkomst från ovanliga platser

- Ovanlig data extrahering

När du använder virtuella datorer för att komma åt dina Azure Cosmos DB-instanser använder du dessutom privata länkar, brand väggar, nätverks säkerhets grupper och service märken för att minimera risken för data exfiltrering. Microsoft hanterar den underliggande infrastrukturen för Azure Cosmos DB och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information.

- [Så här konfigurerar du Cosmos DB Avancerat skydd](cosmos-db-advanced-threat-protection.md)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktioner för automatisk data identifiering, klassificering och förlust av förlust är ännu inte tillgängliga för Azure Cosmos dB. Du kan dock använda Azure Kognitiv sökning-integrering för klassificering och data analys. Du kan också implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och går till fantastiska längder för att skydda mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Indexera Azure Cosmos DB data med Azure Kognitiv sökning](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json)

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: Använd rollbaserad åtkomst kontroll för att kontrol lera åtkomst till resurser

**Vägledning**: Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för vanliga hanterings scenarier i Azure Cosmos dB. En person som har en profil i Azure Active Directory (Azure AD) kan tilldela dessa Azure-roller till användare, grupper, tjänstens huvud namn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB resurser. Roll tilldelningar är begränsade till kontroll – endast plan åtkomst, vilket omfattar åtkomst till Azure Cosmos-konton, databaser, behållare och erbjudanden (data flöde).

- [Implementera Azure RBAC i Azure Cosmos DB](role-based-access-control.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: alla användar data som lagras i Cosmos DB krypteras som standard. Det finns inga kontroller att inaktivera. Azure Cosmos DB använder AES-256-kryptering i alla regioner där kontot körs.

Som standard hanterar Microsoft de nycklar som används för att kryptera data i ditt Azure Cosmos-konto. Du kan välja att lägga till ett andra lager av kryptering med dina egna nycklar.

- [Förstå kryptering i vila med Azure Cosmos DB](database-encryption-at-rest.md)

- [Förstå nyckel hantering för kryptering i vila med Azure Cosmos DB](/azure/cosmos-db/cosmos-db-security-controls)

- [Så här konfigurerar du Kundhanterade nycklar för ditt Azure Cosmos DB-konto](how-to-setup-cmk.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Cosmos dB.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center för dina Azure Cosmos DB-instanser. 

Microsoft utför system korrigering och sårbarhets hantering på de underliggande värdar som har stöd för dina Azure Cosmos DB-instanser. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Funktioner som stöds i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Portal eller Azures resurs diagram för att identifiera alla resurser (inte begränsat till Azure Cosmos DB, utan även även resurser som beräkning, annan lagring, nätverk, portar och protokoll osv.) i dina prenumerationer. Se till att du har rätt behörigheter i din klient organisation och att du kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via resurs diagram, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Så här skapar du frågor med Azure Resource Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Förstå rollbaserad åtkomst kontroll i Azure](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar för Azure Cosmos DB instanser och relaterade resurser med metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

- [Vilka Azure Cosmos DB resurser stöder Taggar](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra till gångar, inklusive men inte begränsat till Azure Cosmos DB resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](/azure/billing/billing-create-subscription)

- [Så här skapar du Hanteringsgrupper](/azure/governance/management-groups/create)

- [Skapa och använda Taggar](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga efter och identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:
- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för dina Cosmos DB-instanser med Azure policy. Använd Azure Policy alias i namn området **Microsoft.DocumentDB** om du vill skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Cosmos DB instanser. Du kan också använda inbyggda princip definitioner för Azure Cosmos DB, till exempel:
- Distribuera Avancerat skydd för Cosmos DB-konton
- Cosmos DB bör använda en tjänst slut punkt för virtuellt nätverk

- [Visa tillgängliga Azure Policy alias](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policys definitioner för dina Cosmos DB eller relaterade resurser använder du Azure-databaser för att lagra och hantera dina anpassade princip definitioner som kod.

- [Arbetsflöden för att utforma princip som kod](../governance/policy/concepts/policy-as-code.md)

- [Dokumentation om Azure databaser](/azure/devops/repos/git/gitworkflow)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.DocumentDB" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Dessutom kan du utveckla en process och pipeline för att hantera princip undantag.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn rymden "Microsoft.DocumentDB" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure Cosmos DB-instanser och relaterade resurser. 

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: för virtuella Azure-datorer eller webb program som körs på Azure App Service används för att komma åt dina Azure Cosmos DB-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och säkra hanteringen av Azure Cosmos DB Secret. Se till att Key Vault mjuk borttagning har Aktiver ATS.

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Så här skapar du en Key Vault](/azure/key-vault/quick-create-portal)

- [Så här autentiserar du till Key Vault](../key-vault/general/authentication.md)

- [Så här tilldelar du en Key Vault åtkomst princip](../key-vault/general/assign-access-policy-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: för virtuella Azure-datorer eller webb program som körs på Azure App Service används för att komma åt dina Azure Cosmos DB-instanser använder du hanterad tjänstidentitet tillsammans med Azure Key Vault för att förenkla och säkra hanteringen av Azure Cosmos DB Secret.

Använd hanterade identiteter för att tillhandahålla Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Med hanterade identiteter kan du autentisera till vilken tjänst som helst som stöder Azure AD-autentisering, inklusive Key Vault utan autentiseringsuppgifter i din kod.

- [Konfigurera hanterade identiteter](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

- [Integrera med Azure Managed Identities](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Så här konfigurerar du en inloggnings skanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsoft Antimalware är aktiverat på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure CosmosDB), men det körs inte på kund innehåll.

Det är ditt ansvar att i förväg genomsöka filer som laddas upp till Azure-resurser som inte är computea, inklusive Azure Cosmos DB. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Azure Cosmos DB automatiskt tar säkerhets kopior av dina data med jämna mellanrum. Om databasen eller containern tas bort kan du använda ett support ärende eller kontakta Azure-supporten för att återställa data från automatiska säkerhets kopieringar online. Support för Azure är tillgängligt för valda planer, till exempel standard, utvecklare och planer som är högre än dem. För att återställa en speciell ögonblicks bild av säkerhets kopieringen kräver Azure Cosmos DB att data är tillgängliga under tiden för säkerhets kopierings cykeln för ögonblicks bilden. 

Om du använder Key Vault för att lagra autentiseringsuppgifter för dina Cosmos DB-instanser bör du se till att vanliga automatiserade säkerhets kopieringar av nycklar används.

- [Förstå Azure Cosmos DB automatiserade säkerhets kopieringar](online-backup-and-restore.md)

- [Så här återställer du data i Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Säkerhetskopiera Key Vault nycklar](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: om en databas eller container tas bort kan du använda ett support ärende eller kontakta Azure-supporten för att återställa data från automatiska säkerhets kopieringar online. Support för Azure är tillgängligt för valda planer, till exempel standard, utvecklare och planer som är högre än dem. För att återställa en speciell ögonblicks bild av säkerhets kopieringen kräver Azure Cosmos DB att data är tillgängliga under tiden för säkerhets kopierings cykeln för ögonblicks bilden.

Testa återställningen av dina hemligheter lagrade i Azure Key Vault med PowerShell. Restore-AzureKeyVaultKey cmdlet skapar en nyckel i det angivna nyckel valvet. Den här nyckeln är en replik av den säkerhetskopierade nyckeln i indatafilen och har samma namn som den ursprungliga nyckeln.

- [Förstå Azure Cosmos DB automatiserade säkerhets kopieringar](online-backup-and-restore.md)

- [Så här återställer du data i Azure Cosmos DB](/azure/cosmos-db/how-to-backup-and-restore)

- [Så här återställer du Azure Key Vault hemligheter](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: eftersom alla användar data som lagras i Cosmos DB krypteras i vila och under transport behöver du inte vidta några åtgärder. Ett annat sätt att göra detta är att kryptering i vila är "on" som standard. Det finns inga kontroller att inaktivera eller sätta på. Azure Cosmos DB använder AES-256-kryptering i alla regioner där kontot körs.

Aktivera Soft-Delete i Key Vault för att skydda nycklar mot oavsiktlig eller skadlig borttagning.

- [Förstå data kryptering i Azure Cosmos DB](database-encryption-at-rest.md)

- [Så här aktiverar du Soft-Delete i Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Du kan också använda NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är att söka efter eller analysen som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt för, bakom den aktivitet som ledde till aviseringen.

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="penetration-tests-and-red-team-exercises"></a>Intrångstester och Red Team-övningar (rött lag)

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft. 

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
