---
title: Azures säkerhets bas linje för Data Lake Analytics
description: Data Lake Analytics säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 33196af2f3544733715ece501cceec0366d2a47c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716266"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Azures säkerhets bas linje för Data Lake Analytics

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till data Lake Analytics. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för data Lake Analytics. **Kontroller** som inte är tillämpliga på data Lake Analytics har uteslutits.

 
Om du vill se hur Data Lake Analytics helt mappar till Azures säkerhets mätning, se den [fullständiga data Lake Analytics mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Använd brand Väggs inställningar för data Lake Analytics för att begränsa externa IP-adressintervall för att tillåta åtkomst från dina lokala klienter och tjänster från tredje part. Konfiguration av brand Väggs inställningar är tillgängligt via portalen, REST API: er eller PowerShell.

- [Översikt över brand Väggs regler](/rest/api/datalakeanalytics/firewallrules) 

- [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

**Vägledning**: Använd brand Väggs inställningar för data Lake Analytics för att begränsa externa IP-adressintervall för att tillåta åtkomst från dina lokala klienter och tjänster från tredje part.  Konfiguration av brand Väggs inställningar är tillgängligt via portalen, REST API: er eller PowerShell.

- [Översikt över brand Väggs regler](/rest/api/datalakeanalytics/firewallrules) 

- [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som data Lake Analytics gransknings-och begär ande-diagnostik. I Azure Monitor använder du en Log Analytics arbets yta för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring eller lagring, om du vill ha säkerhetsfunktioner som oföränderlig lagring och tvingande kvarhållning. 

Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en system informations-och händelse hanterings lösning från tredje part.

- [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md) 

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](/azure/azure-monitor/learn/quick-collect-azurevm) 

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera diagnostikinställningar för data Lake Analytics för att få åtkomst till gransknings-och förfrågnings loggar. Detta inkluderar data som händelse källa, datum, användare, tidsstämpel och andra användbara element. 

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings) 

- [Förstå loggning och olika logg typer i Azure](/azure/azure-monitor/platform/platform-logs-overview)

**Ansvar**: Kund

**Azure Security Center övervakning**: [Azures säkerhets benchmark](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) är standard policy initiativ för Security Center och är grunden för [Security Center rekommendationer](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md). De Azure Policy-definitioner som är relaterade till den här kontrollen aktive ras automatiskt av Security Center. Aviseringar som är relaterade till den här kontrollen kan kräva en [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) -plan för de relaterade tjänsterna.

**Azure policy inbyggda definitioner – Microsoft. DataLakeAnalytics**:

[!INCLUDE [Resource Policy for Microsoft.DataLakeAnalytics 2.3](../../includes/policy/standards/asb/rp-controls/microsoft.datalakeanalytics-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor anger Log Analytics du arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig lagring och arkivering.

- [Ändra data lagrings perioden i Log Analytics](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) 

- [Konfigurera bevarande princip för Azure Storage konto loggar](/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet för dina data Lake Analytics-resurser. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en system informations-och händelse hanterings lösning från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Mer information om arbets ytan Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial)

- [Så här utför du anpassade frågor i Azure Monitor](/azure/azure-monitor/log-query/get-started-queries)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Aktivera diagnostikinställningar för data Lake Analytics och skicka loggar till en Log Analytics arbets yta. Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem. 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Så här aviserar du om Log Analytics-loggdata](/azure/azure-monitor/learn/tutorial-response)  

- [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure Active Directory (Azure AD) har inbyggda roller som måste tilldelas explicit och som kan frågas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: data Lake Analytics saknar begreppet standard lösen ord som autentisering medföljer Azure Active Directory (Azure AD) och skyddas av rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton.

Du kan också aktivera en just-in-Time-åtkomst genom att använda Azure Active Directory (Azure AD) Privileged Identity Management och Azure Resource Manager.

- [Läs mer om Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: där det är möjligt använder du Azure Active Directory (Azure AD) SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center identitets-och åtkomst rekommendationer.

- [Förstå SSO med Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) multifaktorautentisering och Azure Security Center Följ rekommendationerna för identitets-och åtkomst hantering som hjälper dig att skydda dina data Lake Analytics resurser.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera Azure Active Directory (Azure AD) multifaktorautentisering](/security/compass/privileged-access-devices)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) säkerhets rapporter för generering av loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd Azure Active Directory (Azure AD) med namnet platser för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du Azure AD-namngivna platser](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Rollbaserad åtkomst kontroll i Azure (Azure RBAC) ger detaljerad kontroll över en klients åtkomst till Data Lake Analytics resurser.

 

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](/azure/active-directory/reports-monitoring/)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Aktivera diagnostikinställningar för Data Lake Analytics och Azure Active Directory (Azure AD) och skicka alla loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar (till exempel försök att komma åt inaktiverade hemligheter) i Log Analytics.

- [Integrera Azure AD-loggar med Azure Monitor loggar](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör dina data Lake Analytics resurser. Du bör aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra data Lake Analytics resurser som lagrar eller bearbetar känslig information. 

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer, hanterings grupper för enskilda säkerhets domäner, till exempel miljö, data känslighet. Du kan begränsa din Data Lake Analytics för att kontrol lera åtkomst nivån till dina Data Lake Analytics resurser som dina program och företags miljöer kräver. När brand Väggs regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt dina Data Lake Analytics-resurser. Du kan styra åtkomsten till Azure Data Lake Analytics via rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

- [Hantera Azure Role-baserade Access Control](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-azure-role-based-access-control)

- [Brand Väggs regler](/rest/api/datalakeanalytics/firewallrules)

- [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: funktioner för data förlust skydd är ännu inte tillgängliga för Azure Data Lake Analytics-resurser. Implementera en lösning från tredje part om det krävs för efterlevnad.

För den underliggande plattform som hanteras av Microsoft behandlar Microsoft allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure förblir skyddade har Microsoft implementerat och underhåller en svit med robusta data skydds kontroller och-funktioner.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

- [Skydda Azure Storage](../storage/blobs/security-recommendations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Microsoft Azure resurser förhandlar TLS 1,2 som standard. Se till att alla klienter som ansluter till Data Lake Analytics kan förhandla med TLS 1,2 eller senare.

- [Exempel på åtgärds lista](/rest/api/datalakeanalytics/operations/list)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: Använd ett aktivt identifierings verktyg för att identifiera känsliga data

**Vägledning**: funktionerna för data identifiering är ännu inte tillgängliga för Azure Data Lake Analytics-resurser. Implementera en lösning från tredje part om det krävs för efterlevnad. 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra hur användare interagerar med tjänsten.

- [Hantera Azure RBAC](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-azure-role-based-access-control)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: data lagras på standard data Lake Storage Gen1s kontot.  För data i vila stöder Data Lake Storage Gen1 "på som standard" transparent kryptering.

- [Kryptering av data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av Azure Data Lake Analytics resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [Azure Security benchmark: sårbarhet Management](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: Följ rekommendationer från Azure Security Center om hur du skyddar dina Azure Data Lake Analytics-resurser.

Microsoft utför sårbarhets hantering på de underliggande system som har stöd för Azure Data Lake Analytics.

- [Förstå Azure Security Center rekommendationer](../security-center/recommendations-reference.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: Använd en risk klassificerings process för att prioritera reparation av identifierade säkerhets risker

**Vägledning**: Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringarna som tillhandahålls av ditt genomsöknings verktyg från tredje part.

- [NIST-publicering – vanliga sårbarhets bedömnings system](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (t. ex. data bearbetning, lagring, nätverk, portar och protokoll o.s.v.) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer, rekommenderar vi starkt att du skapar och använder Azure Resource Manager resurser som går framåt.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att organisera och spåra Azure Data Lake Analytics resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Ytterligare information finns på de länkar som refereras till.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Ytterligare information finns på de länkar som refereras till

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DataLakeAnalytics" för att skapa anpassade principer som ska granskas eller tillämpa konfigurationen för din Azure Data Lake Analytics-tjänst. Du kan också använda inbyggda princip definitioner som är relaterade till din Azure Data Lake Analytics, till exempel:

- Diagnostikloggar i Data Lake Analytics ska vara aktive rad

Ytterligare information finns på de länkar som refereras till

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns]-effekter för att framtvinga säkra inställningar i dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-databaser för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar, önskade tillstånds konfigurations skript osv. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om de är integrerade med Azure DevOps eller Azure AD om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DataLakeAnalytics" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [granskning], [neka] och [distribuera om det inte finns]-effekter för att automatiskt genomdriva konfigurationer för dina Azure Data Lake Analytics-resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Data Lake Analytics), men det körs inte på kund innehåll.

Förskanna allt innehåll som överförs till Azure-resurser, till exempel App Service, Data Lake Analytics, Blob Storage osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: data Lake Analytics jobb loggar och data utdata lagras i den underliggande data Lake Storage Gen1s tjänsten.  Du kan använda olika metoder för att kopiera data, inklusive ADLCopy, Azure PowerShell eller Azure Data Factory.  Du kan också använda Azure Automation för att regelbundet säkerhetskopiera data.

- [Hantera Azure Data Lake Storage Gen1 resurser med Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

- [Översikt över Azure Automation](../automation/automation-intro.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: data Lake Analytics jobb loggar och data utdata lagras i den underliggande data Lake Storage Gen1s tjänsten.  Du kan använda olika metoder för att kopiera data, inklusive ADLCopy, Azure PowerShell eller Azure Data Factory.  

- [Hantera Azure Data Lake Storage Gen1 resurser med Storage Explorer](../data-lake-store/data-lake-store-in-storage-explorer.md)

- [Kopiera data från Azure Storage blobbar till Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: regelbundet utföra Data återställning av dina säkerhets kopierings data för att testa data integriteten.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: data Lake Analytics säkerhets kopior som lagras i Data Lake Storage Gen1 eller Azure Storage stöd för kryptering som standard och inte kan inaktive ras. Du bör behandla dina säkerhets kopior som känsliga data och tillämpa relevanta åtkomst-och data skydds kontroller som en del av den här bas linjen.  

- [Skydda data som lagras i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md)

- [Auktorisera åtkomst till data i Azure Storage](../storage/common/storage-auth.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST för dator säkerhets incident hanterings guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov. 

- [NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. Granska incidenter när du är säker på att problemen är lösta.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

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
