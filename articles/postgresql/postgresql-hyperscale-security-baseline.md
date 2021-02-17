---
title: Azures säkerhets bas linje för Azure Database for PostgreSQL-storskalig (citus)
description: Säkerhets bas linjen Azure Database for PostgreSQL-storskalig (citus) ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 19935037e98bbc7ce9c4152b034b664d802a8f3a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588293"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>Azures säkerhets bas linje för Azure Database for PostgreSQL-storskalig (citus)

Azures säkerhets bas linje för Azure Database for PostgreSQL-storskalig (citus) innehåller rekommendationer som hjälper dig att förbättra distributionens säkerhets position.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](../security/benchmarks/overview.md), som ger rekommendationer om hur du kan skydda dina moln lösningar i Azure med våra bästa praxis rikt linjer.

Mer information finns i [Översikt över Azure Security-bas linjer](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [säkerhets kontroll: nätverks säkerhet](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: skydda Azure-resurser i virtuella nätverk

**Vägledning**: Azure Database for postgresql server brand vägg förhindrar all åtkomst till citus-koordinatorn tills du anger vilka datorer som har behörighet. Brand väggen beviljar åtkomst till servern baserat på den ursprungliga IP-adressen för varje begäran. Du konfigurerar brandväggen genom att skapa brandväggsregler som anger intervall med godkända IP-adresser. Du kan skapa brand Väggs regler på server nivå.

- [Konfigurera brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-firewall-rules.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

**Vägledning**: definiera och implementera standardinställda säkerhetskonfigurationer för nätverks inställningar och nätverks resurser som är kopplade till dina Azure Database for PostgreSQL-instanser med Azure policy. Använd Azure Policy alias i namn området "Microsoft. Network" om du vill skapa anpassade principer för granskning eller framtvinga nätverks konfigurationen för dina Azure Database for PostgreSQL-instanser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy exempel för nätverk](../governance/policy/samples/built-in-policies.md#network)

- [Så här skapar du en Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: för gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Dessutom skickas loggar via Azure Monitor för att samla in säkerhets data som genererats av storskalig (citus). Använd Log Analytics arbets ytor i Azure Monitor för att fråga och utföra analyser och Använd lagrings konton för långsiktig/lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM). 

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Mått i storskalig (citus)](./concepts-hyperscale-monitoring.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: storskalig (citus) tillhandahåller mått för varje nod i en Server grupp. Måtten ger inblick i beteendet för stöd resurser. Varje mått genereras med en minuters frekvens och har upp till 30 dagars historik.

För gransknings loggning i kontroll plan aktiverar du Azure aktivitets logg diagnostikinställningar och skickar loggarna till en Log Analytics arbets yta, Azure Event Hub eller Azure Storage-konto för arkivering. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina Azure-resurser.

Dessutom skickas loggar via Azure Monitor för att samla in säkerhets data som genererats av storskalig (citus). Använd Log Analytics arbets ytor i Azure Monitor för att fråga och utföra analyser och Använd lagrings konton för långsiktig/lagring. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en säkerhets incident och händelse hantering från tredje part (SIEM). 

- [Mått i storskalig (citus)](./concepts-hyperscale-monitoring.md)

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning

**Vägledning**: i Azure Monitor, för arbets ytan Log Analytics som används för att lagra dina citus-loggar, anger du kvarhållningsperioden enligt organisationens regler för efterlevnad. Använd Azure Storage konton för långsiktig/Arkiv lagring.

- [Ange logg lagrings parametrar för Log Analytics arbets ytor](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Lagra resurs loggar i ett Azure Storage konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: analysera och övervaka loggar från citus-instanserna för avvikande beteende. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Mer information om Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)

- [Så här utför du anpassade frågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: du kan aktivera diagnostikinställningar för storskalig (citus) och skicka loggar till en Log Analytics-arbetsyta. Du kan konfigurera och få en avisering utifrån övervaknings måtten för dina Azure-tjänster. Använd Azure Monitor Log Analytics för att granska loggar och köra frågor om loggdata. Alternativt kan du aktivera och fordonsbaserad data till Azure Sentinel eller en SIEM från tredje part.

Publicera din Log Analytics-arbetsyta till Azure-kontroll när den tillhandahåller en SOAR-lösning (Security Orchestration autoresponse). Detta gör det möjligt för spel böcker (automatiserade lösningar) att skapas och användas för att åtgärda säkerhets problem.

- [Mått i storskalig (citus)](./howto-hyperscale-alert-on-metric.md)

- [Konfigurera diagnostikinställningar för Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [säkerhets kontroll: identitets-och åtkomst kontroll](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: upprätthålla en inventering av de användar konton som har administrativ åtkomst till kontroll planet (t. ex. Azure Portal) för citus-instanserna. Dessutom bör du underhålla en inventering av de administrativa konton som har åtkomst till data planet (i själva databasen) i citus-instanserna.

Storskalig (citus) stöder inte inbyggd rollbaserad åtkomst kontroll, men du kan skapa anpassade roller baserat på vissa resurs leverantörs åtgärder.

Dessutom använder PostgreSQL-motorn roller för att kontrol lera åtkomsten till databas objekt och en nyskapad citus-servergrupp () innehåller flera roller som definierats i förväg. Om du vill ändra användar behörigheter använder du standard PostgreSQL-kommandon med ett verktyg som PgAdmin eller psql.

- [Förstå anpassade roller för Azure-prenumerationen](../role-based-access-control/custom-roles.md) 

- [Förstå Azure Database for PostgreSQL Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Förstå åtkomst hantering för Azure Database for PostgreSQL](./concepts-security.md#access-management)

- [Så här skapar du användare i Azure Database for PostgreSQL-storskalig (citus)](./howto-hyperscale-create-users.md)

- [Så här ansluter du till PostgreSQL (citus) med psql](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure AD har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord tvingar ett lösen ord att skapas med komplexitets krav och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton som används för att få åtkomst till dina citus-instanser. Administratörs kontona för att hantera Azure-resursen är knutna till Azure Active Directory, det finns även lokala server administratörs konton som finns i Server gruppen för storskaliga (citus) för att hantera åtkomst behörigheter för databasen. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton i Azure Active Directory.

- [Förstå Azure Security Center identitet och åtkomst](../security-center/security-center-identity-access.md) 

- [Så här skapar du användare i Azure Database for PostgreSQL-storskalig (citus)](./howto-hyperscale-create-users.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Använd Multi-Factor Authentication för all Azure Active Directory baserad åtkomst

**Vägledning**: för åtkomst till Azure Portal aktivera Azure Active Directory Multi-Factor Authentication (MFA) och Azure Security Center följa rekommendationerna för identitets-och åtkomst hantering.

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd Paw (Privileged Access Workstation) med Multi-Factor Authentication (MFA) konfigurerat för att logga in på och konfigurera Azure-resurser.

- [Lär dig mer om arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Aktivera MFA i Azure](../active-directory/authentication/howto-mfa-getstarted.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="37-alert-on-account-login-behavior-deviation"></a>3,7: avisering om beteende för beteende för konto inloggning

**Vägledning**: Använd Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Använd identifieringar av Azure AD-risker för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: använda villkorlig åtkomst med namngivna platser för att tillåta portal och Azure Resource Manager åtkomst från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (AD) som den centrala autentiserings-och auktoriserings systemet för att hantera postgresql-resurser. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Användare i en citus-Server grupp kan inte vara direkt kopplade till Azure Active Directory konton. Om du vill ändra användar behörighet för databas objekts åtkomst använder du standard PostgreSQL-kommandon med verktyg som PgAdmin eller psql.

- [Ändra privilegium för användar roller](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [Så här skapar och konfigurerar du en AAD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: granska och Stäm av åtkomst för båda användare som har åtkomst till den lokala databasen samt genom Azure Active Directory för att hantera postgresql-resurser.

För användare med åtkomst till hantera Azure-resurser i Azure granskar du Azure Active Directory (AD)-loggarna för att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program som kan användas för att få åtkomst till citus (storskalig) och roll tilldelningar. Användar åtkomsten bör granskas regelbundet, till exempel var 90: e dag och se till att endast rätt användare har fortsatt åtkomst.

- [Granska PostgreSQL-användare och tilldelade roller](https://www.postgresql.org/docs/current/database-roles.html)

- [Förstå Azure AD repor ting](../active-directory/reports-monitoring/index.yml)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: inom Azure Active Directory (AD) har du till gång till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med alla Siem/övervaknings verktyg. 

Du kan effektivisera processen genom att skapa diagnostikinställningar för Azure Active Directory användar konton och skicka gransknings loggar och inloggnings loggar till en Log Analytics arbets yta. Du kan konfigurera önskade aviseringar i Log Analytics arbets ytan. 

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: Använd Azure Active Directoryens identitets skydds-och risk identifierings funktioner för att konfigurera automatiserade svar på identifierade misstänkta åtgärder på Azure Active Directory (AD) nivå. Du kan aktivera automatiserade svar via Azure Sentinel för att implementera din organisations säkerhets svar.

Du kan också mata in loggar i Azure Sentinel för ytterligare undersökning.

- [Översikt över Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Så visar du riskfyllda inloggningar för Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: inte tillgänglig för tillfället. Customer Lockbox stöds ännu inte för storskalig (citus).

- [Lista över Customer Lockbox tjänster som stöds](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [säkerhets kontroll: data skydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: underhåll en inventering av känslig information

**Vägledning**: Använd taggar för att under lätta spårning av citus-instanser (interscales) eller relaterade resurser som lagrar eller bearbetar känslig information.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolera system som lagrar eller bearbetar känslig information

**Vägledning**: implementera separata prenumerationer och/eller hanterings grupper för utveckling, testning och produktion. Använd en kombination av administrativa roller och brand Väggs regler för att isolera och begränsa nätverks åtkomsten till dina Azure Database for PostgreSQL-instanser.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Förstå brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-firewall-rules.md)

- [Förstå roller i storskaliga (citus)](./howto-hyperscale-create-users.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: klient program anslutningar till den storskaliga (citus) koordinator noden kräver Transport Layer Security (TLS) 1,2. Att framtvinga TLS-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal är tvingande av TLS-anslutningar aktiverat som standard.

I vissa fall kräver program från tredje part en lokal certifikat fil som genererats från en certifikat utfärdare (. cer) från en betrodd certifikat utfärdare för att ansluta säkert.

- [Så här konfigurerar du TLS i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-ssl-connection-security.md)

- [Program som kräver certifikat verifiering för TLS-anslutning](./concepts-hyperscale-ssl-connection-security.md)



**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser

**Vägledning**: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra åtkomsten till citus-kontroll planet (t. ex. Azure Portal). Azure RBAC påverkar inte användar behörigheter i databasen.

Om du vill ändra användar behörighet på databas nivå använder du standard kommandon för PostgreSQL med ett verktyg som PgAdmin eller psql.

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Så här konfigurerar du användar åtkomst med SQL för Azure Database for PostgreSQL](./howto-hyperscale-create-users.md)


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**:  
Minst en gång per dag tar Azure Database for PostgreSQL storskalig (citus) ögonblicks bilder av datafiler och databas transaktions loggen. Med säkerhets kopiorna kan du återställa en server till alla tidpunkter inom kvarhållningsperioden. (Kvarhållningsperioden är för närvarande 35 dagar för alla kluster.) Alla säkerhets kopior krypteras med AES 256-bitars kryptering. PostgreSQL citus-erbjudandet använder Microsoft-hanterade nycklar för kryptering.

- [Förstå kryptering för säkerhets kopior av citus (Azure PostgreSQL-storskalig)](./concepts-hyperscale-backup.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i produktions instanser av storskaliga (citus) och andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

## <a name="vulnerability-management"></a>Sårbarhetshantering

*Mer information finns i [säkerhets kontroll: sårbarhets hantering](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: köra automatiserade sårbarhets skannings verktyg

**Vägledning**: inte tillgänglig för tillfället. Azure Security Center har ännu inte stöd för sårbarhets bedömning för Azure Database for PostgreSQL-storskalig skalning (citus).

- [Funktions täckning för Azure PaaS Services i Azure Security Center](../security-center/features-paas.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [säkerhets kontroll: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: Använd automatiserad identifierings lösning för till gång

**Vägledning**: Använd Azure Resource Graph för att fråga efter och identifiera alla resurser (inklusive storskaliga (citus) instanser) i dina prenumerationer. Se till att du har rätt (Läs) behörigheter i din klient och kan räkna upp alla Azure-prenumerationer samt resurser i dina prenumerationer.

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

- [Så här visar du dina Azure-prenumerationer](/powershell/module/az.accounts/get-azsubscription)

- [Förstå Azure RBAC](../role-based-access-control/overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="62-maintain-asset-metadata"></a>6,2: underhåll till gångens metadata

**Vägledning**: använda taggar till storskaliga (citus) instanser och andra relaterade resurser som ger metadata till att logiskt organisera dem i en taxonomi.

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: ta bort obehöriga Azure-resurser

**Vägledning**: Använd taggning, hanterings grupper och separata prenumerationer, vid behov, för att ordna och spåra citus-instanser och relaterade resurser. Stäm av inventering regelbundet och se till att obehöriga resurser tas bort från prenumerationen inom rimlig tid.

- [Så här skapar du ytterligare Azure-prenumerationer](../cost-management-billing/manage/create-subscription.md)

- [Så här skapar du Hanteringsgrupper](../governance/management-groups/create-management-group-portal.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definiera och underhålla inventering av godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper

- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)


**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: övervaka för ej godkända Azure-resurser

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

Använd dessutom Azure Resource Graph för att fråga/identifiera resurser i prenumerationerna.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här skapar du frågor med Azure Graph](../governance/resource-graph/first-query-portal.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="69-use-only-approved-azure-services"></a>6,9: Använd endast godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att ange begränsningar för den typ av resurser som kan skapas i kund prenumerationer med hjälp av följande inbyggda princip definitioner:

- Otillåtna resurstyper
- Tillåtna resurstyper

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/index.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd den villkorliga Azure-åtkomsten för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering". Detta kan förhindra att resurser skapas och ändras i en hög säkerhets miljö, till exempel instanser av citus () som innehåller känslig information.

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [säkerhets kontroll: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: upprätta säkra konfigurationer för alla Azure-resurser

**Vägledning**: definiera och implementera standardkonfigurationer för citus-instanser med Azure policy. Använd Azure Policy för att skapa anpassade principer för att granska eller tillämpa nätverks konfigurationen för dina Azure Database for PostgreSQL-instanser.

Azure Resource Manager har också möjlighet att exportera mallen i JavaScript Object Notation (JSON), vilken bör granskas för att säkerställa att konfigurationerna uppfyller/överskrider säkerhets kraven för din organisation. 

- [Visa tillgängliga Azure Policy alias](/powershell/module/az.resources/get-azpolicyalias)

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Exportera en och flera resurser till en mall i Azure Portal](../azure-resource-manager/templates/export-template-portal.md) 



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: underhåll säker Azure-resurs-konfigurationer

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: Spara konfigurationen av Azure-resurser på ett säkert sätt

**Vägledning**: om du använder anpassade Azure policy-definitioner för dina citus-instanser och relaterade resurser använder du Azure databaser för att lagra och hantera din kod på ett säkert sätt.

- [Så här lagrar du kod i Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

- [Dokumentation om Azure databaser](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: Distribuera konfigurations hanterings verktyg för Azure-resurser

**Vägledning**: Använd Azure policy [Deny] och [distribuera om det inte finns] för att genomdriva säkra inställningar i dina Azure-resurser.  Dessutom kan du använda Azure Resource Manager mallar för att underhålla säkerhets konfigurationen för dina Azure-resurser som krävs av din organisation. 

- [Förstå Azure Policys effekter](../governance/policy/concepts/effects.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Översikt över Azure Resource Manager mallar](../azure-resource-manager/templates/overview.md)



**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: implementera automatisk konfigurations övervakning för Azure-resurser

**Vägledning**: Använd Azure policy alias i namn området "Microsoft. DBforPostgreSQL" om du vill skapa anpassade principer för att varna, granska och genomdriva system konfigurationer. Använd Azure-princip [granskning], [neka] och [distribuera om det inte finns] för att automatiskt framtvinga konfigurationer för dina Azure Database for PostgreSQL-instanser och relaterade resurser.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: hantera identiteter säkert och automatiskt

**Vägledning**: Azure Database for PostgreSQL-storskalig (citus) stöder för närvarande inte hanterade identiteter direkt. När du skapar Azure Database for PostgreSQL-servern måste du ange autentiseringsuppgifter för en administratörs användare. Du kan skapa ytterligare användar roller i Azure Portal-gränssnittet.

- [Skapa en Azure Database for PostgreSQL-storskalig (citus)](./quickstart-create-hyperscale-portal.md#create-a-hyperscale-citus-server-group)

- [Skapa ytterligare användar roller](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvars område**: inte tillgängligt för tillfället

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [säkerhets kontroll: försvar mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster, till exempel Scale (citus), men den körs inte på kund innehåll.

Förskanna allt innehåll som laddas upp till Azure-resurser som inte är Compute, till exempel App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL osv. Microsoft kan inte komma åt dina data i dessa instanser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [säkerhets kontroll: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: se till att vanlig automatisk säkerhets kopiering UPS

**Vägledning**: Azure Database for PostgreSQL – storskalig (citus) skapar automatiskt säkerhets kopior av varje nod och lagrar dem i lokalt redundant lagring. Säkerhets kopieringar kan användas för att återställa ditt citus-kluster till en angiven tid.

- [Säkerhetskopiera och återställa i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: minst en gång per dag tar Azure Database for PostgreSQL ögonblicks bilder av datafiler och databas transaktions loggen. Med säkerhets kopiorna kan du återställa en server till alla tidpunkter inom kvarhållningsperioden. Kvarhållningsperioden är för närvarande 35 dagar för alla kluster. Alla säkerhetskopior krypteras med AES 256-bitars kryptering.

I Azure-regioner som har stöd för tillgänglighets zoner lagras ögonblicks bilder av säkerhets kopior i tre tillgänglighets zoner. Så länge minst en tillgänglighets zon är online är citus-klustret återställas.

- [Säkerhetskopiera och återställa i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-backup.md)


**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: i Azure Database for PostgreSQL återställer ett storskaligt kluster (citus) ett nytt kluster från de ursprungliga nodernas säkerhets kopior. Du kan återställa ett kluster till en viss tidpunkt inom de senaste 35 dagarna. Återställnings processen skapar ett nytt kluster i samma Azure-region, prenumeration och resurs grupp som originalet. Den nya kluster konfigurationen är samma som den ursprungliga kluster konfigurationen: samma antal noder, antalet virtuella kärnor, lagrings storlek och användar roller.

Brand Väggs inställningar och parametrar för PostgreSQL-servern bevaras inte från den ursprungliga Server gruppen. de återställs till standardvärdena. Brand väggen kommer att förhindra alla anslutningar. Du måste justera inställningarna manuellt efter återställningen.

- [Säkerhetskopiera och återställa i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: det går inte att återställa borttagna citus-kluster. Om du tar bort klustret tas alla noder som tillhör klustret bort och kan inte återställas. För att skydda kluster resurser efter en oavsiktlig borttagning eller oväntade ändringar kan administratörerna utnyttja hanterings lås.

- [Säkerhetskopiera och återställa i Azure Database for PostgreSQL-storskalig (citus)](./concepts-hyperscale-backup.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [säkerhets kontroll: incident svar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. 

- [Konfigurera automatisering av arbets flöden i Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Kunden kan också utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa till med att skapa egna incident svars planer](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller det analytiska som används för att utfärda aviseringen samt vilken konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod) och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems incident svars funktioner på en vanlig takt. Identifiera svaga punkter och luckor, och ändra planen efter behov. 

- [Se NIST: guide för test, utbildning och övnings program för IT-planer och funktioner](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: Ange kontakt information för säkerhets incidenter och konfigurera aviseringar för säkerhets incidenter

**Vägledning**: kontakt information om säkerhets incidenter kommer att användas av Microsoft för att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker att kundens data har öppnats av en olaglig eller obehörig part.  Granska incidenter när du är säker på att problemen är lösta. 

- [Så här ställer du in Azure Security Center säkerhets kontakt](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: införliva säkerhets aviseringar i ditt incident svars system

**Vägledning**: exportera Azure Security Center aviseringar och rekommendationer med hjälp av funktionen för kontinuerlig export. Med kontinuerlig export kan du exportera aviseringar och rekommendationer antingen manuellt eller i löpande miljö. Du kan använda Azure Security Center Data Connector för att strömma aviseringarna till Azure Sentinel. 

- [Så här konfigurerar du kontinuerlig export](../security-center/continuous-export.md) 

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: automatisera svaret på säkerhets aviseringar

**Vägledning**: Använd funktionen för automatisering av arbets flöden i Azure Security Center för att automatiskt utlösa svar via "Logic Apps" i säkerhets aviseringar och rekommendationer. 

- [Konfigurera automatisering av arbets flöden och Logic Apps](../security-center/workflow-automation.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstester och Red Team-tester

*Mer information finns i [säkerhets kontroll: inträngande tester och röda team övningar](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: utför regelbundna inträngande tester av dina Azure-resurser och se till att åtgärda alla viktiga säkerhets brister

**Vägledning**: Följ Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Du hittar mer information om Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="next-steps"></a>Nästa steg

- Se [Azures säkerhets benchmark](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)