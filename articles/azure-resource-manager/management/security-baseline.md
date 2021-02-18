---
title: Azures säkerhets bas linje för Azure Resource Manager
description: Azure Resource Manager säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5e44515c8ca71d115ced65c9123e2b1ae3bdf23a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578233"
---
# <a name="azure-security-baseline-for-azure-resource-manager"></a>Azures säkerhets bas linje för Azure Resource Manager

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../../security/benchmarks/overview-v1.md) för att Microsoft Azure Resource Manager. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Resource Manager. **Kontroller** som inte är tillämpliga på Azure Resource Manager har uteslutits.

 Om du vill se hur Azure Resource Manager helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Resource Manager mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: mata in Azure policy aktivitets loggar via Azure Monitor. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring eller arkiv. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../../azure-monitor/vm/quick-collect-azurevm.md) 

- [Komma igång med Azure Monitor och SIEM-integrering från tredje part](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: Azure Resource Manager använder aktivitets loggar som aktive ras automatiskt för att inkludera händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element.

- [Samla in plattforms loggar och mått med Azure Monitor](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../../azure-monitor/essentials/platform-logs-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar för avvikande beteende och granska resultaten regelbundet. Använd Azure Monitor och en Log Analytics arbets yta för att granska loggar och köra frågor på loggdata. 

Du kan också aktivera och inaktivera data till Azure Sentinel eller en SIEM från tredje part. 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Komma igång med Log Analytics frågor](../../azure-monitor/logs/log-analytics-tutorial.md) 

- [Så här utför du anpassade frågor i Azure Monitor](../../azure-monitor/logs/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: Använd Azure Security Center med Log Analytics för övervakning och aviseringar om avvikande aktivitet som finns i aktivitets loggar. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel. 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md) 

- [Hantera aviseringar i Azure Security Center](../../security-center/security-center-managing-and-responding-alerts.md) 

- [Avisering om Log Analytics loggdata](../../azure-monitor/alerts/tutorial-response.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azures säkerhets benchmark: identitets-och åtkomst kontroll](../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: Azure rollbaserad åtkomst kontroll (RBAC) gör att du kan hantera åtkomst till Azure-resurser via roll tilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure Portal.

- [Så här hämtar du en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [Så här hämtar du medlemmar i en katalog roll i Azure AD med PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, till exempel:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

Du kan också aktivera en just-in-Time-åtkomst genom att använda Azure AD Privileged Identity Management och Azure Resource Manager. 

- [Läs mer om Privileged Identity Management](../../active-directory/privileged-identity-management/index.yml)

- [Använda Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: Använd enkel inloggning (SSO) med Azure Active Directory

**Vägledning**: där det är möjligt använder Azure Active Directory SSO i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center identitets-och åtkomst rekommendationer. 

- [Förstå SSO med Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: aktivera Azure Active Directory Multi-Factor Authentication och följ rekommendationer för Azure Security Center identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en säker, Azure-hanterad arbets Station (kallas även en privilegie rad åtkomst arbets Station eller Paw) för administrativa uppgifter som kräver förhöjda privilegier.

- [Förstå säkra, Azure-hanterade arbets stationer](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Så här aktiverar du Azure AD MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: Använd Azure Active Directory säkerhets rapporter och övervakning för att identifiera när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets-och åtkomst aktiviteter.

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../../security-center/security-center-identity-access.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd namngivna platser i Azure AD för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du Azure AD-namngivna platser](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd namngivna platser i Azure AD för att endast tillåta åtkomst från särskilda logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du Azure AD-namngivna platser](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure AD innehåller loggar som hjälper till att identifiera inaktuella konton. Dessutom kan du använda Azure AD-identitets-och åtkomst granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

- [Förstå Azure AD repor ting](../../active-directory/reports-monitoring/index.yml)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: du har åtkomst till inloggnings aktiviteter, gransknings-och risk händelse logg källor i Azure AD, vilket gör att du kan integrera med alla Siem/övervaknings verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan.

- [Så här integrerar du Azure-aktivitets loggar med Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure AD Identity Protection funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Så visar du riskfyllda inloggningar för Azure AD](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security benchmark: Data Protection](../../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att spåra Azure-resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](./tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4,6: Använd Azure RBAC för att hantera åtkomst till resurser

**Vägledning**: Använd Azure AD RBAC för att kontrol lera åtkomsten till data och resurser, annars kan du använda tjänstesärskilda metoder för åtkomst kontroll.

- [Så här konfigurerar du RBAC i Azure](../../role-based-access-control/role-assignments-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: för kryptering på Server sidan i vila Azure Resource Manager stöder Microsoft-hanterade nycklar.

- [Förstå data skydd i Azure Resource Manager](azure-resource-manager-security-controls.md#data-protection)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar när ändringar sker i kritiska Azure-resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (till exempel data bearbetning, lagring, nätverk, portar och protokoll) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient organisation och räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

Även om klassiska Azure-resurser kan identifieras via Azure Resource Graph Explorer, rekommenderar vi starkt att du skapar och använder Azure Resource Manager-resurser.

- [Så här skapar du frågor med Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Förstå Azure RBAC](../../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: Använd princip namn, beskrivning och kategori för att logiskt organisera till gångar enligt en taxonomi.

- [Mer information om att tagga till gångar finns i avsnittet om namngivning av resurser och tagga besluts guide](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer om det behövs för att organisera och spåra Azure-resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

Använd dessutom Azure Policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Mer relaterad information finns nedan.

- [Så här skapar du ytterligare Azure-prenumerationer](../../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du hanterings grupper](../../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](./tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla en inventering av godkända Azure-resurser

**Vägledning**: skapa en inventering av godkända Azure-resurser och godkänd program vara för beräknings resurser enligt organisationens behov.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i dina prenumerationer.

Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna.  Se till att alla Azure-resurser som finns i miljön är godkända.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Resource Graph](../../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Mer relaterad information finns nedan.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../../governance/policy/samples/built-in-policies.md#general)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure AD för att begränsa användarnas möjlighet att interagera med Azures resurs hanteraren genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: begränsa användarnas möjlighet att köra skript i beräknings resurser

**Vägledning**: ej tillämpligt; den här rekommendationen är avsedd för beräknings resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: Använd Azure policy alias för att skapa anpassade principer för att granska eller tillämpa konfigurationen för dina Azure-resurser. Du kan också använda inbyggda Azure Policy definitioner.

Azure Resource Manager kan exportera mallen i JavaScript Object Notation (JSON), som bör granskas för att säkerställa att konfigurationerna uppfyller säkerhets kraven för din organisation.

Du kan också använda rekommendationerna från Azure Security Center som en säker konfigurations bas linje för dina Azure-resurser.

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Självstudie: skapa och hantera principer för att genomdriva efterlevnad](../../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../templates/export-template-portal.md)

- [Säkerhetsrekommendationer – en referensguide](../../security-center/recommendations-reference.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [neka] och [distribuera om det inte finns] för att framtvinga säkra inställningar i dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

Som administratör kan du också behöva låsa en prenumeration, resurs grupp eller resurs för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser. Du kan ange låsnivån till CanNotDelete eller ReadOnly.

- [Förstå Azure Policys effekter](../../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../templates/overview.md)

- [Så här låser du resurser för att förhindra oväntade ändringar](lock-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure policy definitioner, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för Azure-resurser med hjälp av Azure policy. Använd Azure Policy alias för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure-resurser. Du kan också använda inbyggda princip definitioner som är relaterade till dina speciella resurser.  Du kan också använda Azure Automation för att distribuera konfigurations ändringar.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Använda alias](../../governance/policy/concepts/definition-structure.md#aliases)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: använd inbyggda Azure policy definitioner och anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure Policy [audit], [neka] och [distribuera om det inte finns] för att automatiskt tillämpa konfigurationer för dina Azure-resurser.

- [Konfigurera och hantera Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Använd rekommenderade metoder när du skapar din arm-mall. dessa rekommendationer hjälper dig att undvika vanliga problem när du använder en arm-mall för att distribuera en lösning.

Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault. 

- [Implementera metod tips för mall säkerhet](../templates/template-best-practices.md)

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../../security/benchmarks/security-control-data-recovery.md).*

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: Säkerställ att du regelbundet kan utföra distribution av Azure Resource Manager mallar regelbundet till en isolerad prenumeration om det behövs.

- [Distribuera resurser med ARM-mallar och Azure Portal](../templates/deploy-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: Använd Azure-DevOps för att lagra och hantera din kod på ett säkert sätt, t. ex. anpassade Azure policy definitioner, Azure Resource Manager mallar och önskade tillstånds konfigurations skript. För att få åtkomst till de resurser som du hanterar i Azure DevOps, kan du bevilja eller neka behörigheter till särskilda användare, inbyggda säkerhets grupper eller grupper som definierats i Azure Active Directory (Azure AD) om det är integrerat med Azure DevOps eller Active Directory om det är integrerat med TFS.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Om behörigheter och grupper i Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security benchmark: incident svar](../../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: utveckla en incident svars guide för din organisation. Se till att det finns skriftliga svars planer för incidenter som definierar alla roller för personal samt de olika faserna av incident hantering och hantering från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Använd NIST hanterings guide för dator säkerhet för att hjälpa till med att skapa din egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor och ändra sedan svars planen efter behov.

- [NISTs publikation – guide för att testa, träna och träna program för IT-planer och-funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export för att identifiera risker för Azure-resurser. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel.

- [Så här konfigurerar du kontinuerlig export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöde Azure Security Center för att automatiskt utlösa svar på säkerhets aviseringar och rekommendationer för att skydda dina Azure-resurser.

- [Konfigurera automatisering av arbets flöden i Security Center](../../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [övningen för Azure Security benchmark: inträngande tester och röda team](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsoft Cloud inträngande test för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../../security/benchmarks/security-baselines-overview.md)