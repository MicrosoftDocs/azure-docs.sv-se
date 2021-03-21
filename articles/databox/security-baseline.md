---
title: Azures säkerhets bas linje för Azure Data Box
description: Azure Data Box säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0a05a4692bb4a09213532ed29b2501f6b8056857
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101721998"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Azures säkerhets bas linje för Azure Data Box

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 1,0](../security/benchmarks/overview-v1.md) till Azure Data box. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure.
Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Data box. **Kontroller** som inte är tillämpliga på Azure Data Box har uteslutits.

 
Om du vill se hur Azure Data Box helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Data Box mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets benchmark för Azure: loggning och övervakning](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering

**Vägledning**: Microsoft hanterar den tids källa som används för Azure-resurser, till exempel för tidsstämplar i loggarna. Azure Data Box tiden kan leda till att den inte är ansluten till nätverket som har åtkomst till NTP-servern på kund platsen.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar

**Vägledning**: motsvarar varje steg i din data Box-enhets ordning kan du vidta flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

- [Förstå spårning och händelse loggning för din Azure Data Box](data-box-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser

**Vägledning**: motsvarar varje steg i din data Box-enhets ordning kan du vidta flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

- [Förstå spårning och händelse loggning för din Azure Data Box](data-box-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar

**Vägledning**: motsvarar varje steg i din data Box-enhets ordning kan du vidta flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

- [Förstå spårning och händelse loggning för din Azure Data Box](data-box-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: aktivera aviseringar för avvikande aktiviteter

**Vägledning**: motsvarar varje steg i din data Box-enhets ordning kan du vidta flera åtgärder för att kontrol lera åtkomsten till ordern, granska händelserna, spåra beställningen och tolka de olika loggar som genereras.

- [Förstå spårning och händelse loggning för din Azure Data Box](data-box-logs.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-and-access-control"></a>Identitets- och åtkomstkontroll

*Mer information finns i [Azure Security benchmark: identitet och Access Control](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: underhåll en inventering av administrativa konton

**Vägledning**: underhålla en inventering av de användar konton som har administrativ åtkomst till din Azure Data box. Du kan använda fönstret identitets-och åtkomst kontroll (IAM) i Azure Portal för din prenumeration för att konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC). Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory.

Du kan styra vem som kan komma åt din beställning när ordern först skapas. Konfigurera Azure RBAC-roller i olika scope för att kontrol lera åtkomsten till Data Box-enhets ordningen. En Azure RBAC-roll avgör typ av åtkomst – Läs-och Skriv behörighet, skrivskyddad, Läs-och skriv åtgärder för en del av åtgärderna.

- [Förstå anpassade roller](../role-based-access-control/custom-roles.md)

- [Så här konfigurerar du Azure RBAC för arbets böcker](../sentinel/quickstart-get-visibility.md)

- [Förstå hur du konfigurerar åtkomst kontroll på ordern](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="32-change-default-passwords-where-applicable"></a>3,2: ändra standard lösen ord där tillämpligt

**Vägledning**: Azure Active Directory (Azure AD) har inte begreppet standard lösen ord. Andra Azure-resurser som kräver ett lösen ord tvingar ett lösen ord att skapas med komplexitets krav och minsta längd på lösen ord, vilket varierar beroende på tjänsten. Du ansvarar för program från tredje part och Marketplace-tjänster som kan använda standard lösen ord.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: Använd dedikerade administrativa konton

**Vägledning**: skapa standard procedurer för användning av dedikerade administrativa konton. Använd Azure Security Center identitets-och åtkomst hantering för att övervaka antalet administrativa konton.

För att hjälpa dig att hålla koll på dedikerade administrativa konton kan du dessutom använda rekommendationer från Azure Security Center eller inbyggda Azure-principer, t. ex.:

- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration

- [Använda Azure Security Center för att övervaka identitet och åtkomst (för hands version)](../security-center/security-center-identity-access.md)

- [Använda Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: Använd dedikerade datorer (arbets stationer med privilegie rad åtkomst) för alla administrativa uppgifter

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med Azure AD multifaktor-autentisering aktive rad för att logga in på och konfigurera Azure Data Box beställningar.

- [Arbetsstationer för privilegierad åtkomst (PAW)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Planera en molnbaserad distribution av Azure AD multifaktor-autentisering](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logga och Avisera om misstänkta aktiviteter från administrativa konton

**Vägledning**: inte tillämpligt, Azure Data Box inte har något eget administratörs konto. Du kommer åt den via Azure Portal. Du kan dock konfigurera din Azure-prenumeration så att den använder Azure Active Directory (Azure AD) Privileged Identity Management (PIM) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön.

Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: hantera endast Azure-resurser från godkända platser

**Vägledning**: Använd villkorlig åtkomst med namngivna platser för att tillåta åtkomst till Azure Portal från enbart vissa logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Så här konfigurerar du namngivna platser i Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="39-use-azure-active-directory"></a>3,9: Använd Azure Active Directory

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system i förekommande fall. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

- [Skapa och konfigurera en Azure AD-instans](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: granska och stäm regelbundet av användar åtkomst

**Vägledning**: Azure Active Directory (Azure AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

Detta stöds inte i real tid för Data Box-enhet-enheten. Du kan granska loggarna i slutet av jobbet.

- [Förstå Azure AD repor ting](/azure/active-directory/reports-monitoring/)

- [Så här använder du granskningar av Azure Identity Access](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: övervakaren försöker komma åt inaktiverade autentiseringsuppgifter

**Vägledning**: Använd Azure Active Directory (Azure AD) som central autentiserings-och auktoriserings system i förekommande fall. Azure AD skyddar data med stark kryptering för data i vila och under överföring. Azure AD innehåller även salter, hash-värden och lagrar användarautentiseringsuppgifter på ett säkert sätt.

Du har åtkomst till Azure AD-inloggning, gransknings-och risk händelse logg källor, som gör att du kan integrera med Azure Sentinel eller en SIEM från tredje part.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure AD-användarkonton och skicka gransknings loggar och inloggnings loggar till en Log Analytics-arbetsyta. Du kan konfigurera önskade logg aviseringar i Log Analytics.

Azure Data Box tjänst loggar skrivs inte till Log Analytics-arbetsytan.

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Aktivera Azure-kontroll på kort](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: varning vid inloggnings beteende för konto

**Vägledning**: för konto inloggning beteende avvikelse i kontroll planet (t. ex. Azure Portal) använder du Azure Active Directory (Azure AD) identitets skydd och funktioner för identifiering av risker för att konfigurera automatiserade svar på identifierade misstänkta åtgärder som rör användar identiteter. Du kan också mata in data i Azure Sentinel för ytterligare undersökning.

- [Visa Azure AD-riskfylld inloggning](../active-directory/identity-protection/overview-identity-protection.md)

- [Så här konfigurerar och aktiverar du risk principer för identitets skydd](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: ge Microsoft åtkomst till relevant kund information under support scenarier

**Vägledning**: snart tillgänglig. Customer Lockbox stöds ännu inte för Azure Data Box. 

- [Lista över Customer Lockbox tjänster som stöds](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-control-data-protection.md).*

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: övervaka och blockera obehörig överföring av känslig information

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure Data Box och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information. När Data Box-enhet är på kund platsen följer du bästa praxis för att se till att känsliga data överförs är skyddade.

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: kryptera all känslig information under överföring

**Vägledning**: Microsoft hanterar den underliggande infrastrukturen för Azure Data Box och har implementerat strikta kontroller för att förhindra förlust eller exponering av kund information. När Data Box-enhet är på kund platsen följer du bästa praxis för att se till att känsliga data överförs är skyddade.

- [Förstå migrering av data i Azure Data Box](data-box-faq.md)

- [Översikt över Data Box-enhet säkerhet](data-box-security.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Använd Azure RBAC för att kontrol lera åtkomsten till resurser 

**Vägledning**: kontrol lera att du har ägar-eller deltagar åtkomst till prenumerationen för att skapa en data Box-enhets order. Du kan också definiera Data Box-enhet läsare och Data Box-enhet deltagar roller på resurs nivå.

- [Lär dig hur du kommer igång med Azure Data Box](data-box-quickstart-portal.md)

- [Förstå hur du konfigurerar åtkomst kontroll](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: kryptera känslig information i vila

**Vägledning**: Azure Data Box implementerar AES 256-bitars kryptering för vilande data.

Azure Data Box implementerar AES 256-bitars kryptering för vilande data.

Azure Data Box skyddar dessutom enhetens upplåsnings nyckel (även kallat enhets lösen ord) som används för att låsa enheten via en krypterings nyckel. Som standard krypteras enhetens upplåsnings nyckel för en Data Box-enhet ordning med en hanterad Microsoft-nyckel. Du kan även ange en kundhanterad nyckel för ytterligare kontroll över enhetens upplåsnings nyckel. Kundhanterade nycklar måste skapas och lagras i en Azure Key Vault.
 

- [Förstå Data Box-enhet data skydd](data-box-security.md)

- [Använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box](data-box-customer-managed-encryption-key-portal.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: logg och varning vid ändringar av kritiska Azure-resurser

**Vägledning**: Använd Azure monitor med Azure aktivitets logg för att skapa aviseringar för när ändringar sker i Azure Data Box samt andra kritiska eller relaterade resurser.

- [Så här skapar du aviseringar för Azure aktivitets logg händelser](/azure/azure-monitor/platform/alerts-activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="inventory-and-asset-management"></a>Inventerings- och tillgångshantering

*Mer information finns i [Azure Security benchmark: inventering och till gångs hantering](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="secure-configuration"></a>Säker konfiguration

*Mer information finns i [Azure Security benchmark: säker konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="711-manage-azure-secrets-securely"></a>7,11: Hantera Azure-hemligheter på ett säkert sätt

**Vägledning**: Kundhanterade nycklar måste skapas och lagras i en Azure Key Vault.

- [Använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box](data-box-customer-managed-encryption-key-portal.md)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminera oavsiktlig exponering för autentiseringsuppgifter

**Vägledning**: Microsoft kör autentiseringstypen för autentiseringsuppgifter på data Box-enhets kod. Dessutom skyddar Microsoft också autentiseringsuppgifter på ett säkert sätt. Implementera autentiseringsuppgifterna för inloggning för att identifiera autentiseringsuppgifter inom koden. Credential Scanner uppmanar också till att flytta identifierade autentiseringsuppgifter till en säkrare plats som Azure Key Vault.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="malware-defense"></a>Skydd mot skadlig kod

*Mer information finns i [Azure Security benchmark: skydd mot skadlig kod](../security/benchmarks/security-control-malware-defense.md).*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: för skanning av filer som ska laddas upp till Azure-resurser som inte är Compute

**Vägledning**: Microsofts program mot skadlig kod har Aktiver ATS på den underliggande värden som har stöd för Azure-tjänster (till exempel Azure Customer lockbox), men det körs inte på kund innehåll. 

Det är ditt ansvar att i förväg genomsöka allt innehåll som laddas upp till Azure-resurser som inte är Compute. Microsoft kan inte komma åt kunddata och kan därför inte utföra genomsökningar av kund innehåll för program mot skadlig kod.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="data-recovery"></a>Dataåterställning

*Mer information finns i [Azure Security benchmark: Data återställning](../security/benchmarks/security-control-data-recovery.md).*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: Utför fullständig säkerhets kopiering av systemet och säkerhetskopiera alla Kundhanterade nycklar

**Vägledning**: se till att säkerhetskopiera alla data och kundens hanterade nyckel. Data Box-enhet gör inga säkerhets kopior.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: validera alla säkerhets kopior inklusive Kundhanterade nycklar

**Vägledning**: du bör kontrol lera att alla dina data är i Azure Storage konto innan du tar bort dem från dina lokaler.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: se till att skydda säkerhets kopior och Kundhanterade nycklar

**Vägledning**: se till att alla säkerhets kopieringar eller Kundhanterade nycklar som du har skyddas i enlighet med bästa praxis.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10,1: skapa en incident svars guide

**Vägledning**: Skapa en guide till incidentsvar för organisationen. Se till att det finns skriftliga planer för incidentsvar som definierar alla personalroller och faser i incidenthanteringen, från identifiering till granskning efter incidenten. 

- [Vägledning om hur du skapar en egen svars process för säkerhets incidenter](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Centers Beskrivning av en incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Utnyttja NISTs hanterings guide för dator säkerhet för att hjälpa dig att skapa en egen incident svars plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: skapa en incident bedömnings-och prioriterings procedur

**Vägledning**: Security Center tilldelar en allvarlighets grad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center befinner sig i att söka efter eller analysera den som används för att utfärda aviseringen samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen. 

Dessutom är det tydligt att markera prenumerationer (t. ex. produktion, icke-Prod.) med hjälp av taggar och skapa ett namngivnings system för att tydligt identifiera och kategorisera Azure-resurser, särskilt för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Skapa och använda Taggar](../azure-resource-manager/management/tag-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="103-test-security-response-procedures"></a>10,3: testa säkerhets svars procedurer

**Vägledning**: utföra övningar för att testa dina Systems funktioner för incident svar på en vanlig takt för att hjälpa till att skydda dina Azure-resurser. Identifiera svaga punkter och luckor, och ändra planen efter behov.

- [NIST-guide för att testa, träna och träna program för IT-planer och funktioner](https://csrc.nist.gov/publications/detail/sp/800-84/final)

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

**Vägledning**: Microsoft har inträngande testning och sårbarhets sökning på data Box-enhet enheter. Du kan göra din egen inträngande testning och sårbarhets ökning. Om du väljer att göra det följer du Microsofts regler för engagemang för att se till att dina inträngande tester inte strider mot Microsofts principer. Använd Microsofts strategi och körning av röda team indelning och inträngande av direktsända webbplatser mot Microsoft-hanterad moln infrastruktur, tjänster och program.

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
