---
title: Azures säkerhets bas linje för Microsoft Azure peering-tjänsten
description: Säkerhets bas linjen för Microsoft Azure peering-tjänsten ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: peering-service
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 854c669861c6aff71861481843bff97fdb7157ab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315705"
---
# <a name="azure-security-baseline-for-microsoft-azure-peering-service"></a>Azures säkerhets bas linje för Microsoft Azure peering-tjänsten

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Microsoft Azure peering-tjänsten. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer för Microsoft Azure peering-tjänsten.

> [!NOTE]
> **Kontroller** som inte gäller för Microsoft Azure peering-tjänsten, eller för vilka ansvaret är Microsofts, har uteslutits. Om du vill se hur Microsoft Azure peering-tjänsten fullständigt mappar till Azures säkerhets benchmark, se den **[fullständiga Microsoft Azure peering-tjänstens säkerhets bas linje mappnings fil](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)**.

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: peering-tjänsten erbjuder inte interna intrångs identifierings-eller skydds tjänster. Kunderna bör följa rekommenderade säkerhets metoder, till exempel att använda hot information-baserad filtrering från Azure-brandväggen för att varna och blockera trafik från och till kända skadliga IP-adresser och domäner, beroende på vad som gäller för deras affärs behov. Dessa IP-adresser och domäner kommer från Microsoft Threat-Intelligence-feeden. 

Om det krävs en nytto Last kontroll kan du distribuera en tredjeparts intrångs identifiering eller skydds system (ID/IP-adresser), med nytto Last funktioner från Azure Marketplace.  
Du kan också använda en värdbaserad lösning för slut punkts identifiering och-svar (EDR) tillsammans med (eller i stället för) nätverksbaserade intrångs identifierings-eller skydds system.  

Om du har ett föreskrivande krav för att använda ett intrångs-eller intrångs skydds system bör du se till att det alltid är anpassat till att använda eller tillhandahålla aviseringar med hög kvalitet. 

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace innehåller funktioner från tredje part-ID](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP-EDR-kapacitet](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning**: upprätthålla en inventering av användar kontona med administrativ åtkomst till kontroll planet (till exempel Azure Portal) för dina Microsoft Azure peering service-resurser.

Använd fönstret identitets-och åtkomst kontroll (IAM) i Azure Portal för din prenumeration för att konfigurera rollbaserad åtkomst kontroll i Azure (Azure RBAC). Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Azure Active Directory (Azure AD).

- [Lägga till eller ta bort rolltilldelningar för Azure med hjälp av Azure-portalen](../role-based-access-control/role-assignments-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Hantera appidentiteter säkert och automatiskt

**Vägledning**: hanterade identiteter stöds inte för peering-tjänsten. För tjänster som inte har stöd för hanterade identiteter som peering-tjänsten använder du Azure Active Directory (Azure AD) för att skapa ett huvud namn för tjänsten med begränsade behörigheter på resurs nivå i stället. 

- [Hanterade Azure-identiteter](../active-directory/managed-identities-azure-resources/overview.md)

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps)

- [Skapa ett huvud namn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning**: Microsoft Azure-peering-tjänsten använder Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser. I det här ingår företagsidentiteter som anställda samt externa identiteter som partner, och leverantörer. 

Använd enkel inloggning för att hantera och skydda åtkomsten till din organisations data och resurser lokalt och i molnet. Anslut alla användare, appar och enheter till Azure AD för en smidig och säker åtkomst samt bättre insyn och kontroll.

- [Förstå SSO för appar i Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Aktivera multifaktorautentisering med Azure Active Directory (Azure AD) och följ rekommendationerna för identitets-och åtkomst hantering från Azure Security Center.

- [Så här aktiverar du multifaktorautentisering i Azure](../active-directory/authentication/howto-mfa-getstarted.md)

- [Övervaka identitet och åtkomst i Azure Security Center](../security-center/security-center-identity-access.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning**: Använd PRIVILEGED Identity Management (PIM) med Azure Active Directory (Azure AD) för att skapa loggar och varningar när misstänkt eller osäker aktivitet inträffar i miljön. Dessutom kan du använda Azure AD-farlighets identifiering för att visa aviseringar och rapporter om riskfyllda användar beteenden.

- [Distribuera Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Förstå identifieringar av Azure AD-risker](../active-directory/identity-protection/overview-identity-protection.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Använd villkorlig åtkomst med Azure Active Directory (Azure AD) för mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, till exempel att kräva användar inloggningar från vissa IP-intervall för att använda multifaktorautentisering. En detaljerad hantering av autentiseringsbegäranden kan också användas via en princip för villkorlig åtkomst i Azure AD för olika användnings fall. 

- [Översikt över villkorlig Azure-åtkomst](../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera autentiseringsessionshantering med villkorsstyrd åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminera oavsiktlig exponering av autentiseringsuppgifter

**Vägledning**: implementera autentisering av autentiseringsuppgifter i Azure-DevOps för att identifiera de autentiseringsuppgifter som används i koden. Med scannern för autentiseringsuppgifter kan du också flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

För GitHub kan du använda funktionen för identifiering av interna hemligheter till att identifiera autentiseringsuppgifter och andra hemligheter i koden.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Identifiering av hemligheter i GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa privilegierade användare

**Vägledning**: begränsa antalet starkt privilegierade användar konton och skydda dessa konton på en utökad nivå. 

De viktigaste inbyggda rollerna i Azure Active Directory (Azure AD) är global administratör och administratör för privilegierade roller, eftersom användare som tilldelats dessa två roller kan delegera administratörs roller. 

Med dessa privilegier kan användare direkt eller indirekt läsa och ändra alla resurser i din Azure-miljö:

- Global administratör/företags administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.

- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Det kan finnas andra viktiga roller som måste regleras om du använder anpassade roller med vissa privilegierade behörigheter. Använd liknande kontroller för administratörs kontot för kritiska företags till gångar.  

Aktivera just-in-Time (JIT) privilegie rad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Behörigheter för administratörsrollen i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: isolera åtkomst till verksamhets kritiska system genom att begränsa konton som har fått privilegie rad åtkomst till de prenumerationer och hanterings grupper som de tillhör. 

Begränsa åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till dina affärs kritiska till gångar, till exempel Active Directory-domän controllers (DC), säkerhets verktyg och system hanterings verktyg med agenter som är installerade på affärs kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan omedelbart weaponize dem för att kompromettera affärs kritiska till gångar. 

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll. 

Se till att tilldela separata privilegierade konton som skiljer sig från de standard användar konton som används för e-post, surfning och produktivitets uppgifter.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Åtkomst till hanterings grupp](https://docs.microsoft.com/azure/governance/management-groups/overview#management-group-access)

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning**: Azure Active Directory (AD) innehåller loggar för att hjälpa dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access-granskningar för att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användar åtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst.

- [Hitta Azure AD-aktivitets rapporter](../active-directory/reports-monitoring/howto-find-activity-reports.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nödåtkomst i Azure AD

**Vägledning**: Konfigurera ett nödfalls konto för åtkomst, när de vanliga administrativa kontona inte är tillgängliga, för att förhindra oavsiktligt utelåst av din Azure Active Directory (Azure AD)-organisation. Återställnings konton kan innehålla behörigheter på högre nivå och ska inte tilldelas till vissa individer. Begränsa åtkomsten till nöd situationer till nöd situationer eller Break glas-scenarier.

Se till att autentiseringsuppgifterna (till exempel lösen ord, certifikat eller smartkort) för konton för nöd åtkomst hålls skyddade och endast kända för personer som har behörighet att använda dem i emergent-situationer.

- [Hantera konton för nödåtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Använd Azure Active Directory (Azure AD) rättighets hanterings funktioner för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är Azure Active Directory (Azure AD) åtkomst granskningar](../active-directory/governance/access-reviews-overview.md)

- [Vad är Azure Active Directory (Azure AD) hantering av rättigheter](../active-directory/governance/entitlement-management-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: Använd en privilegie rad åtkomst arbets Station (Paw) med multifaktorautentisering från Azure Active Directory (Azure AD) som är aktive rad för att logga in på och konfigurera dina Azure Sentinel-relaterade resurser.

- [Arbetsstationer för privilegierad åtkomst (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planera en molnbaserad distribution av Azure AD multifaktor-autentisering](../active-directory/authentication/howto-mfa-getstarted.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: rollbaserad åtkomst kontroll i Azure (Azure RBAC) gör att du kan hantera Azure Resource Access via roll tilldelningar. Du kan tilldela dessa roller till användare, grupp tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell och Azure Portal.

De behörigheter som du tilldelar resurser via Azure RBAC bör alltid vara begränsade till vad som krävs av rollerna. Begränsade privilegier kompletterar just-in-Time-metoden (JIT) för Azure Active Directory (Azure AD) Privileged Identity Management (PIM) och dessa privilegier bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och skapa bara en anpassad roll vid behov.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Ytterligare behörigheter kan krävas för att få insyn i arbets belastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning**: se till att säkerhets teamen har åtkomst till en kontinuerligt uppdaterad inventering av till gångar på Azure. Säkerhetsteamet behöver den här inventeringen till att utvärdera organisationens potentiella exponering för nya risker samt som indata till löpande förbättringar av säkerheten. 

Azure Security Center Inventory-funktionen och Azure Resource Graph kan fråga efter och identifiera alla resurser i dina prenumerationer, inklusive Azure-tjänster, program och nätverks resurser.  

Organisera tillgångar logiskt enligt organisationens taxonomi med hjälp av taggar och andra metadata i Azure (namn, beskrivning och kategori).  

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md)

- [Mer information om att tagga till gångar finns i besluts guiden för resurs namn givning och taggning](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning**: Microsoft Azure-peering-tjänsten har stöd för Azure Resource Manager baserade distributioner och konfigurations tvång med hjälp av Azure policy i namn området Microsoft. peering/peering. Använd Azure Policy till att granska och begränsa vilka tjänster användarna kan etablera i miljön. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor till att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Så här nekar du en speciell resurs typ med Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Konfigurera villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azure Resource Manager genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

Använd Azure Security Center inbyggd hot identifierings funktion, som baseras på övervakning av Azure-tjänsten telemetri och analys av tjänst loggar. Data samlas in med hjälp av Log Analytics agent, som läser olika säkerhetsrelaterade konfigurationer och händelse loggar från systemet och kopierar data till din arbets yta för analys. 

Använd dessutom Azure Sentinel för att bygga analys regler, som rör hot som matchar specifika kriterier i din miljö. Reglerna genererar incidenter när villkoren matchas, så att du kan undersöka varje incident. Azure Sentinel kan också importera Hot information från tredje part för att förbättra sin funktion för hot identifiering. 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

- [Referens guide för Azure Security Center säkerhets varningar](../security-center/alerts-reference.md)

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md)

- [Cyberhot Hot information med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

**Vägledning**: Azure Active Directory (Azure AD) innehåller användar loggar som kan visas med Azure AD repor ting eller integrerat med Azure Monitor. Dessa loggar kan också integreras med Azure Sentinel eller andra lösningar för säkerhets information och händelse hantering (SIEM) eller övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall:

- Inloggning – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter

- Gransknings loggar – ger spårning genom loggar för alla ändringar som gjorts av olika funktioner i Azure AD. Exempel på gransknings loggar är ändringar som har gjorts i alla resurser i Azure AD, till exempel att lägga till eller ta bort användare, appar, grupper, roller och principer

- Riskfylld inloggning – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är en legitim ägare till ett användar konto

- Användare som har flaggats för risk – en riskfylld användare är en indikator för ett användar konto som kan ha komprometterats

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett alltför stort antal misslyckade autentiseringsförsök av föråldrade konton i en prenumeration. Förutom den grundläggande säkerhetsövervakningen kan skydds modulen i Security Center även samla in ytterligare djupgående säkerhets aviseringar från enskilda Azure Compute-resurser (till exempel virtuella datorer, behållare, App Service), data resurser (till exempel SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du Visa konto avvikelser i de enskilda resurserna.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

**Vägledning**: Konfigurera telemetri för anslutning som ger inblick i anslutningen mellan din anslutnings plats och

Microsoft-nätverk via Microsoft Azure-peering-tjänsten.

- [Konfigurera telemetri för anslutning](measure-connection-telemetry.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: Aktivera Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering 

Aktivitets loggar ger insikt i de åtgärder som utfördes på dina Azure ExpressRoute-resurser på kontroll planet nivå. Med hjälp av Azures aktivitets logg data kan du avgöra vad, vem och när som helst för Skriv åtgärder (skicka, skicka och ta bort) på kontroll Plans nivån för dina resurser.

- [Azure aktivitets logg](/azure/azure-monitor/platform/activity-log)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md)

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning**: i Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure-resurser enligt organisationens regler för efterlevnad.

- [Ange parametrar för logg bevarande](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: Se till att organisationen har processer för svar på säkerhetsincidenter, att processerna är uppdaterade för Azure och att de tränas regelbundet för att säkerställa beredskapen.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Konfigurera en säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet 

**Vägledning**: Se till att du har en process för att skapa aviseringar av hög kvalitet och mäta aviseringskvaliteten. På så sätt kan du lära dig av tidigare incidenter och prioritera aviseringar till analytiker så att de inte slösar tid på falska positiva identifieringar. 

Du kan basera aviseringar av hög kvalitet på erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar genom att sammanfoga och korrelera olika signalkällor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor, eftersom de undersöker potentiella incidenter för att bygga en fullständig vy över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: definiera säkerhets guardrails för infrastruktur-och DevOps-team genom att göra det enkelt att konfigurera de Azure-tjänster som de använder på ett säkert sätt. 

Starta din säkerhets konfiguration av Azure-tjänster med tjänstens bas linjer i Azures säkerhets benchmark och anpassa efter behov för din organisation. 

Använd Azure Security Center för att konfigurera Azure Policy för att granska och tillämpa konfigurationer av dina Azure-resurser. 

Du kan använda Azure-ritningar för att automatisera distribution och konfiguration av tjänster och program miljöer, inklusive Azure Resource Manager mallar, Azure RBAC-kontroller och-principer, i en enda skiss definition.

- [Bild av guardrails-implementering i netscale-landnings zon](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [Arbeta med säkerhets principer i Azure Security Center](../security-center/tutorial-security-policy.md)

- [Skapa och hantera principer för att använda kompatibilitet](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint](../governance/blueprints/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Underhåll säkra konfigurationer för Azure-tjänster

**Vägledning**: ej tillämpligt;  den här rekommendationen är avsedd för beräknings resurser.

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Nödvändig strategi för åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av dataskyddsfunktioner i Azure och från tredje part

-   Krav på datakryptering både under överföring och i vila

-   Lämpliga kryptografiska standarder

Läs mer i följande referenser:
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – hantering av tillgångar](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark – dataskydd](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera företagets segmenteringsstrategi 

**Vägledning**: Upprätta en strategi för hela företaget gällande att segmentera åtkomsten till tillgångar utifrån en kombination av identiteter, nätverk, program, prenumerationer, hanteringsgrupper och andra kontroller.

Du måste noga avväga behovet av separationsskyddet med behovet att underlätta den dagliga driften av de system som måste kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent för olika kontrolltyper som nätverkssäkerhet, modeller för identiteter och åtkomst, modeller för appbehörighet och appåtkomst samt kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmenteringen efter företagets segmenteringsstrategi](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera en strategi för hantering av säkerhetspositionen

**Vägledning**: Mät och minimera risker gällande enskilda tillgångar och miljöerna där de finns regelbundet. Prioritera värdefulla tillgångar och attackytor med stor exponering, som publicerade appar, in- och utgångar i nätverket och slutpunkter för användare och administratörer.

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera organisationens roller och ansvarsområden

**Vägledning**: Dokumentera och förmedla en tydlig strategi gällande säkerhetsorganisationens roller och ansvarsområden. Prioritera att delegera ett tydligt ansvar för olika säkerhetsbeslut och utbilda alla kring modellen med gemensamt ansvar, och ge de tekniska teamen den utbildning som behövs kring tekniken för att skydda molnet.

- [Regelverk för Azure-säkerhet 1 – personal: utbilda teamen om molnsäkerhetsresan](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Regelverk för Azure-säkerhet 2 – personal: utbilda teamen om molnsäkerhetstekniken](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Regelverk för Azure-säkerhet 3 – personal: tilldela ansvar för molnsäkerhetsbeslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Vägledning**: Upprätta en strategi för nätverkssäkerhet i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Centraliserade ansvarsområden kring nätverkshantering och säkerhet

-   Modell för segmentering av virtuella nätverk anpassad efter företagets segmenteringsstrategi

-   Åtgärdsstrategi för olika hot- och angreppsscenarier

-   Strategi för kantenheter på internet samt in- och utgångar

-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella nätverkssäkerhetsartefakter (som nätverksdiagram och referensnätverksarkitekturer)

Läs mer i följande referenser:
- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – nätverkssäkerhet](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Översikt över nätverkssäkerhet i Azure](../security/fundamentals/network-overview.md)

- [En strategi för företagets nätverksarkitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera en strategi för identiteter och privilegierad åtkomst

**Vägledning**: Upprätta en strategi för identiteter och privilegierad åtkomst i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Ett centraliserat system för identiteter och autentisering och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användningsfall och scenarier

-   Skydda och användare med hög behörighet

-   Övervaka och hantera avvikande användaraktiviteter  

-   Process för att granska och stämma av identiteter och åtkomstbehörighet

Läs mer i följande referenser:

- [Azure Security Benchmark – hantering av identiteter](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark – privilegierad åtkomst](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Vägledning**: Upprätta en strategi för loggning och hotåtgärder så att du snabbt kan identifiera och åtgärda hot och samtidigt uppfylla efterlevnadskraven. Prioritera att ge analytiker tillgång till aviseringar av hög kvalitet och en användarvänlig miljö så att de kan fokusera på hoten snarare än integrering och manuella steg. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Läs mer i följande referenser:

- [Azure Security Benchmark – loggning och hotidentifiering](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark – svar på incidenter](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Hantering och övervakning i företagsskala i Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
