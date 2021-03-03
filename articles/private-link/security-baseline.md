---
title: Azures säkerhets bas linje för privat Azure-länk
description: Säkerhets bas linjen för Azure privat länk ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: private-link
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7dacec1521787e55ee4c7fd5f08b9e56d9dd4842
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736006"
---
# <a name="azure-security-baseline-for-azure-private-link"></a>Azures säkerhets bas linje för privat Azure-länk

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark](../security/benchmarks/overview.md) till Azures privata länk. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azures privata länk. **Kontroller** som inte gäller för en privat Azure-länk har uteslutits. Om du vill se hur Azures privata länk fullständigt mappar till Azures säkerhets benchmark, se den [fullständiga Azure-Virtual Network säkerhets bas linje mappnings fil](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

**Vägledning**: Använd Azure aktivitets logg för att övervaka datorkonfigurationer och identifiera ändringar i nätverks resurser som är relaterade till privat länk. 

Skapa aviseringar inom Azure Monitor som ska utlösas när ändringar av kritiska resurser sker.

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Så här skapar du aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av nätverks resurser som privata länk slut punkter, virtuella nätverk och nätverks säkerhets grupper. 

I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM, baserat på organisationens affärs behov.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Loggning och övervakning för privat länk](private-link-overview.md#logging-and-monitoring)

- [Diagnostisk loggning för en nätverks säkerhets grupp](../virtual-network/virtual-network-nsg-manage-log.md)

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Aktivera Azure Monitor aktivitets loggar, som loggar åtgärder som utförs på privata länk resurser, t. ex. vem som startade åtgärden, när åtgärden utfördes, status för åtgärden och annan användbar gransknings information. 

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Visa och hämta Azure aktivitets logg händelser](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Loggning och övervakning för privat länk](private-link-overview.md#logging-and-monitoring)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: för loggar som är relaterade till en privat länk ställer du in Log Analytics kvarhållningsperiod för arbets ytor enligt organisationens regler för efterlevnad i Azure Monitor. Använd Azure Storage konton för långsiktig lagring av loggar.

- [Ändra data lagrings perioden i Log Analytics](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurera bevarande princip för Azure Storage konto loggar](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggdata.

Ett annat alternativ är att aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Förstå Log Analytics arbets yta](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Security Center som kon figurer ATS med en Log Analytics arbets yta för övervakning och aviseringar om avvikande aktivitet i säkerhets loggar och händelser.

Aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM utifrån organisationens affärs behov.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Hantera aviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Så här aviserar du om Log Analytics-loggdata](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Använd Azure Active Directory (Azure AD) inbyggda administratörs roller som kan tilldelas explicit och efter frågas. Kör Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Security Center identitets-och åtkomst hanterings funktioner för att övervaka antalet administrativa konton.

Du kan också aktivera just-in-Time/just-Access genom att använda Azure Active Directory (Azure AD) Privileged Identity Management privilegierade roller för Microsoft-tjänster och Azure Resource Manager.

- [Läs mer om Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Använd Azure Active Directory enkel inloggning (SSO)

**Vägledning**: där det är möjligt kan du använda enkel inloggning med Azure Active Directory i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst.

- [Enkel inloggning till program i Azure Active Directory](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) och följ rekommendationerna för identitets-och åtkomst hantering för Security Center.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: Använd säkra, Azure-hanterade arbets stationer för administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Multi-Factor Authentication konfigurerad för att logga in på och konfigurera Azure nätverks resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: använda Azure Active Directory (Azure AD) risk identifierings funktion för att visa aviseringar och rapporter om riskfyllda användar beteenden. Hämta Security Center risk identifierings aviseringar till Azure Monitor och konfigurera anpassade aviseringar/aviseringar med hjälp av åtgärds grupper.

- [Förstå Azure Security Center risk identifieringar (misstänkt aktivitet)](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurera åtgärds grupper för anpassad avisering och avisering](../azure-monitor/alerts/action-groups.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: använda Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system. Azure AD skyddar data med stark kryptering för data i vila och under överföring och även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.  

- [Skapa och konfigurera en Azure AD-instans](../active-directory-domain-services/tutorial-create-instance.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory innehåller loggar som hjälper till att identifiera inaktuella konton. Använd också granskning av Azure Identity Access för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure Active Directory (Azure AD) inloggnings aktivitet, gransknings-och risk händelse logg källor för att integrera med alla Siem/övervaknings verktyg.

Effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directory (Azure AD) risk-och identitets skydds funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter för dina nätverks resurser. 

Mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../security/benchmarks/security-control-data-protection.md).*

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera isolering med separata prenumerationer och hanterings grupper för enskilda säkerhets domäner, till exempel miljö typ och data känslighets nivå. 

Begränsa åtkomst nivån till dina Azure-resurser med dina program och företags miljöer baserat på affärs krav. 

Kontrol lera åtkomsten till Azure-resurser via rollbaserad åtkomst kontroll i Azure (Azure RBAC).

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: kryptera all känslig information under överföring. Se till att alla klienter som ansluter till dina Azure-resurser i virtuella nätverk kan förhandla TLS 1,2 eller senare. Privat länk stör inte underliggande protokoll. Använd metod tips för andra erbjudanden som används av kunder.

Följ Security Center rekommendationer för kryptering i vila och kryptering under överföring, i förekommande fall.

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att kontrol lera åtkomsten till data och resurser, Använd annars tjänstens speciella metoder för åtkomst kontroll.

- [Bekanta dig med en kort beskrivning och det unika ID: t för inbyggda Azure-roller här](../role-based-access-control/built-in-roles.md)

Anropa PowerShell cmd "Get-AzRoleDefinition" eller "AZ Role definition List" för att hämta en lista över roller i din miljö.

Granska dina alternativ för att kontrol lera exponeringen för tjänsten via "synlighet"-inställningen. i privat länk. De här Synlighets inställningarna avgör om en konsument kan ansluta till din tjänst. 

Gör din tjänst privat för användning från dina andra virtuella nätverk (endast Azure RBAC-behörigheter). Begränsa exponeringen till en begränsad uppsättning betrodda prenumerationer eller gör den offentlig så att alla Azure-prenumerationer kan begära anslutningar i den privata länk tjänsten.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Egenskaper för privat länk-tjänst](private-link-service-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure Monitor aktivitets logg aviseringar för att skapa aviseringar för när ändringar sker i kritiska Azure-resurser som privata länk tjänster och slut punkter. 

- [Diagnostisk loggning för en nätverks säkerhets grupp](private-link-overview.md#logging-and-monitoring)

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga och identifiera alla nätverks resurser som privata länk tjänster och slut punkter i dina prenumerationer. 

Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd taggar till Azure-resurser med hjälp av metadata för att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra privat länk och relaterade resurser. 

Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och program vara för beräknings resurser utifrån organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd också Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna. Detta kan hjälpa till med hög säkerhets-baserade miljöer, till exempel de med lagrings konton.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="67-remove-unapproved-azure-resources"></a>6,7: ta bort icke godkända Azure-resurser

**Vägledning**: kunden kan förhindra att resurser skapas eller används med Azure policy som krävs av kundens företags principer. Du kan implementera en egen process för att ta bort obehöriga resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller framtvinga konfigurationen av dina Azure-nätverks resurser tillsammans med de inbyggda Azure policy definitionerna.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON). Den här artefakten bör granskas för att säkerställa att konfigurationerna uppfyller eller överskrider organisationens säkerhets krav.

Implementera rekommendationer från Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../governance/policy/tutorials/create-and-manage.md)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure Resource Manager mallar och Azure policy för att på ett säkert sätt konfigurera Azure-resurser som är associerade med privat länk och relaterade resurser.  

Azure Resource Manager mallar är JavaScript Object Notation (JSON) baserade filer som används för att distribuera Azure-resurser måste alla anpassade mallar lagras och bevaras säkert i en kod lagrings plats. 

Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.

- [Information om hur du skapar Azure Resource Manager mallar](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Azure Resource Manager mal sampel för privata slut punkter](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, till exempel anpassade Azure-principer, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. 

Bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps för deras åtkomst, eller Active Directory om det är integrerat med Team Foundation Server.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy. 

Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. 

Använd även de inbyggda princip definitionerna som är relaterade till alla vissa resurser som hanteras under prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../governance/policy/concepts/definition-structure.md#aliases)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy för att granska Azure Resource-konfigurationer. Azure Policy kan till exempel användas för att identifiera resurser som inte är konfigurerade med en privat slut punkt.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exempel på inbyggda Azure policy-moduler för privat länk](../governance/policy/samples/built-in-policies.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: Säkerställ regelbunden automatisk säkerhets kopiering

**Vägledning**: Använd Azure Resource Manager för att distribuera tjänster för privata länkar, slut punkter och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa privata länk slut punkter och relaterade resurser. 

Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager mal sampel för privata slut punkter](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](../automation/automation-intro.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: Använd Azure Resource Manager för att distribuera tjänster för privata länkar, slut punkter och relaterade resurser. Azure Resource Manager ger möjlighet att exportera mallar som kan användas som säkerhets kopior för att återställa privata länk slut punkter och relaterade resurser.  

Använd Azure Automation för att anropa export-API: t för Azure Resource Manager Template regelbundet.  

Säkerhetskopiera Kundhanterade nycklar inom Azure Key Vault.

- [Översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Azure Resource Manager mal sampel för privata slut punkter](/azure/templates/microsoft.network/2019-11-01/privateendpoints)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Resurs grupper – exportera mall](/rest/api/resources/resourcegroups/exporttemplate)

- [Introduktion till Azure Automation](../automation/automation-intro.md)

- [Så här säkerhetskopierar du nyckel valv nycklar i Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: validera möjligheten att regelbundet utföra distribution av Azure Resource Manager mallar regelbundet till en isolerad prenumeration enligt dina affärs behov. 

Verifiera också återställningar av säkerhetskopierade nycklar som hanteras av kunden.

- [Distribuera resurser med ARM-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

- [Återställa Key Vault-nycklar i Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. Azure Resource Manager mallar. 

Bevilja eller neka behörigheter till vissa användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure-DevOps för åtkomst till dessa resurser eller i Active Directory om det är integrerat med Team Foundation Server.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. 

Se till att du har skriftliga svar på incidenter som definierar alla roller för personal och faser av incident hantering eller hantering från identifiering till granskning efter incidenter.

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att kunna prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera prenumerationer (till exempel produktion, icke-produktion) med taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  

Kunden ansvarar för att prioritera reparationen av aviseringar baserat på allvarlighets graden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att dina data har använts av en olagligt eller obehörig part. 

Granska incidenter, post förekomster för att säkerställa att problem har lösts.

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker med Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. 

Du kan också använda Security Center Data Connector för att strömma aviseringarna till Azure Sentinel, enligt vad som krävs per affärs verksamhet.

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för arbets flödes automatisering i Security Center för att automatiskt utlösa svar med Logic Apps om säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. 

Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)