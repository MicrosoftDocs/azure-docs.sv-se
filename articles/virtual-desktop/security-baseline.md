---
title: Azures säkerhets bas linje för Windows Virtual Desktop
description: Säkerhets bas linjen för Windows Virtual Desktop tillhandahåller procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42795e2dda6df24e656c9c06f6a9424bd9e4b5cb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092982"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azures säkerhets bas linje för Windows Virtual Desktop

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../security/benchmarks/overview.md) till Windows Virtual Desktop. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för virtuella Windows-datorer. **Kontroller** som inte gäller för virtuella Windows-datorer har uteslutits.

Tjänsten Windows Virtual Desktop innehåller själva tjänsten, Windows 10 Enterprise för virtuell SKU för flera sessioner samt FSLogix. FSLogix-relaterade säkerhets rekommendationer finns i [säkerhets bas linjen för lagring](../storage/common/security-baseline.md). Tjänsten har en agent som körs på virtuella datorer, men eftersom de virtuella datorerna har fullständig kontroll över kunden, följ [säkerhets rekommendationer för beräkning](../virtual-machines/windows/security-baseline.md)

Om du vill se hur Windows Virtual Desktop mappar fullständigt till Azures säkerhets benchmark kan du läsa mer i den [fullständiga Windows-filen för säkerhets bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

**Vägledning**: du måste skapa eller använda ett befintligt virtuellt nätverk när du distribuerar virtuella datorer som ska registreras på det virtuella Windows-skrivbordet. Se till att alla virtuella Azure-nätverk följer en företags segment princip som passar affärs riskerna. Alla system som kan ådra sig högre risk för organisationen bör isoleras i ett eget virtuellt nätverk och tillräckligt säkra med antingen en nätverks säkerhets grupp eller Azure-brandvägg.

Använd anpassade nätverks härdnings funktioner i Azure Security Center för att rekommendera konfigurationer för nätverks säkerhets grupper som begränsar portar och käll-IP-adresser med referenser till externa nätverks trafik regler.

Beroende på dina program och din strategi för företags segmentering begränsar eller tillåter du trafik mellan interna resurser baserat på regler för nätverks säkerhets grupper. För vissa väldefinierade program (t. ex. en app på en 3-nivå) kan detta vara en mycket säker "neka som standard, Tillåt enligt undantag". Detta kanske inte kan skalas bra om du har många program och slut punkter interagerar med varandra. Du kan också använda Azure-brandvägg i situationer där central hantering krävs över ett stort antal företags segment eller ekrar (i en nav/eker-topologi)

För de nätverks säkerhets grupper som är kopplade till den virtuella datorn (som är en del av Windows Virtual Desktop)-undernät måste du tillåta utgående trafik till vissa slut punkter. 

- [Ta reda på vilka URL: er som krävs för att få åtkomst till virtuella Windows-datorer](safe-url-list.md)

- [Anpassad nätverks härdning i Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall för virtuellt Windows-skrivbord ](../firewall/protect-windows-virtual-desktop.md)

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Distribuera och konfigurera Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

**Vägledning**: Använd Azure-ExpressRoute eller Azure Virtual Private Network för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en miljö med en valfri plats. ExpressRoute-anslutningar går inte via offentligt Internet, ger mer tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. 

För virtuella privata nätverk med punkt-till-plats och plats-till-plats kan du ansluta lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av alternativ för virtuella privata nätverk och Azure-ExpressRoute.

Använd peering för virtuella nätverk för att ansluta två eller flera virtuella nätverk tillsammans i Azure. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och finns kvar i Azures stamnät nätverket.

- [Vad är ExpressRoute-anslutnings modeller](../expressroute/expressroute-connectivity-models.md) 

- [Översikt över Azure VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Peering för virtuella nätverk](/azure/virtual-network/virtual-network-peering-overview)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. Skydda dina Windows-resurser för virtuella skriv bord mot attacker från externa nätverk, inklusive distribuerade denial of Service-attacker, programspecifika attacker, oönskad och potentiellt skadlig Internet trafik. Skydda dina till gångar mot distribuerade denial of Service-attacker genom att aktivera DDoS standard skydd på dina virtuella Azure-nätverk. Använd Azure Security Center för att identifiera problem med felaktig konfiguration som är relaterade till dina nätverks relaterade resurser.

Windows Virtual Desktop är inte avsett att köra webb program och kräver inte att du konfigurerar några ytterligare inställningar eller distribuerar några extra nätverks tjänster för att skydda det från externa nätverks attacker som riktar sig mot webb program.

- [Dokumentation om Azure Firewall](/azure/firewall)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Rekommendationer för Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

**Vägledning**: Använd Azure-brandvägg med hot information-baserad filtrering för att varna på och eventuellt blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. När nytto lasts granskning krävs kan du distribuera en tredjeparts intrångs identifiering eller en skydds lösning från Azure Marketplace. 

Om du har en regel eller annat krav för användning av intrångs avkänning eller skydd av förebyggande lösningar kontrollerar du att det alltid är anpassat för att tillhandahålla aviseringar med hög kvalitet till din SIEM-lösning (Security information and Event Management).

- [Så här distribuerar du Azure-brandvägg](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace innehåller funktioner från tredje part-ID](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP-EDR-kapacitet](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper eller en Azure-brandvägg som kon figurer ATS för dina Windows-resurser för virtuella datorer. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (till exempel: WindowsVirtualDesktop) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

- [Läs mer om vad som omfattas av tjänst tag gen för Windows Virtual Desktop här ](safe-url-list.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning**: Windows Virtual Desktop använder Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:

- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser.

Skyddet av Azure AD bör ha hög prioritet i organisationens säkerhetspraxis för molnet. Azure AD ger dig en identitetsskyddspoäng som hjälper dig att utvärdera identiteternas säkerhet i relation till Microsofts rekommendationer kring regelverk. Använd poängen till att mäta hur nära konfigurationen matchar rekommendationerna kring regelverk och förbättra säkerhetsläget.

Azure AD stöder externa identiteter som låter användare utan Microsoft-konto logga in till sina program och resurser med sin externa identitet.

- [Innehav i Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Använda en extern identitetsprovider för appar](/azure/active-directory/b2b/identity-providers)

- [Vad är identitetssäkerhetspoäng i Azure AD](../active-directory/fundamentals/identity-secure-score.md)

- [Vissa roller som du behöver för att använda Windows Virtual Desktop ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Hantera appidentiteter säkert och automatiskt

**Vägledning**: Windows Virtual Desktop stöder Azure Managed identiteter för icke-mänskliga konton som tjänster eller automatisering. Vi rekommenderar att du använder Azure-hanterad identitets funktion i stället för att skapa ett mer kraftfullt humant konto för att komma åt eller köra dina resurser. 

Windows Virtual Desktop rekommenderar att du använder Azure Active Directory (Azure AD) för att skapa ett huvud namn för tjänsten med begränsade behörigheter på resurs nivå för att konfigurera tjänstens huvud namn med autentiseringsuppgifter för certifikatet och återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (till exempel en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps) 

- [Skapa ett huvud namn för tjänsten med certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Använd Azure Key Vault för registrering av säkerhets objekt](../key-vault/general/authentication.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning**: Windows Virtual Desktop använder Azure Active Directory (Azure AD) för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. I det här ingår företagsidentiteter som anställda samt externa identiteter som partner, och leverantörer. På så sätt kan enkel inloggning (SSO) hantera och skydda åtkomsten till organisationens data och resurser både lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös säker åtkomst med bättre synlighet och kontroll.

- [Förstå SSO för appar i Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst 

**Vägledning**: Windows Virtual Desktop använder Azure Active Directory (Azure AD), som stöder starka verifierings kontroller via multifaktorautentisering och starka lösen ords avskrivna metoder.

- Multifaktorautentisering – aktivera Azure AD multifaktor-autentisering och följ rekommendationerna för identitets-och åtkomst hantering från Azure Security Center för vissa rekommenderade metoder i konfigurationen av multifaktorautentisering. Multifaktorautentisering kan tillämpas på alla, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer.

- Autentisering utan lösenord – det finns tre alternativ för autentisering utan lösenord: Windows Hello för företag, appen Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

Windows Virtual Desktop stöder äldre lösenordsbaserad autentisering, t. ex. endast molnbaserade konton (användar konton som skapats direkt i Azure) som har en bas linje för lösen ords principer eller hybrid konton (användar konton från lokala Azure AD som följer lokala lösen ords principer). När du använder lösenordsbaserad autentisering ger Azure AD en funktion för lösen ords skydd som förhindrar användare att ange lösen ord som är lätta att gissa. Microsoft tillhandahåller en global lista över blockerade lösen ord som uppdateras utifrån telemetri, och kunderna kan förstärka listan utifrån deras behov (till exempel varumärkes-, kultur referenser och så vidare). Detta lösen ords skydd kan endast användas för moln-och hybrid konton.

Autentisering utifrån enbart autentiseringsuppgifter för lösen ord är känsligt för populära angrepps metoder. För högre säkerhet använder du stark autentisering, till exempel multifaktorautentisering och en stark lösen ords princip. För program från tredje part och Marketplace-tjänster som kan ha standard lösen ord bör du ändra dem vid den första installationen av tjänsten.

För administratörer och privilegierade användare ser du till att högsta nivån av starka autentiseringsmetoder används, följt av lämplig princip för stark autentisering för andra användare.

- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Standardprincip för lösenord för Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminera Felaktiga lösen ord med Azure Active Directory lösen ords skydd](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning**: det virtuella Windows-skrivbordet är integrerat med Azure Active Directory (Azure AD) som tillhandahåller följande data Källor:

- Logga in – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.

- Riskfylld inloggning – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.

- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller ett SIEM-system (Security information and Event Management) från tredje part. Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen. Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Aviseringar i skyddsmodulen för hotinformation i Azure Security Center](../security-center/alerts-reference.md)

- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Windows Virtual Desktop stöder villkorlig åtkomst med Azure Active Directory (Azure AD) för detaljerad åtkomst kontroll baserat på användardefinierade villkor. Till exempel kan användar inloggningar från vissa IP-intervall krävas för att använda multifaktorautentisering för åtkomst. 

Dessutom kan detaljerad hanterings princip för autentiseringsprinciper också användas för olika användnings fall.

- [Översikt över villkorsstyrd åtkomst i Azure](../active-directory/conditional-access/overview.md) 

- [Vanliga policyer för villkorsstyrd åtkomst](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Konfigurera hantering av sessionsautentisering med villkorsstyrd åtkomst](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Konfigurations information för Windows virtuell Skriv bords installation hittar du här](set-up-mfa.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: det virtuella Windows-skrivbordet använder Azures rollbaserade åtkomst kontroll (Azure RBAC) för att isolera åtkomst till verksamhets kritiska system. Se till att du också begränsar åtkomsten till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till din affärs kritiska åtkomst, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg med agenter som är installerade på verksamhets kritiska system. Angripare som angriper dessa hanterings-och säkerhets system kan eventuellt omedelbart weaponize dem för att kompromettera affärs kritiska till gångar.

Alla typer av åtkomst kontroller bör justeras mot din strategi för företags segmentering för att säkerställa en konsekvent åtkomst kontroll.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Minsta administratörs behörighet som krävs för att hantera Windows Virtual Desktop](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning**: Windows Virtual Desktop använder Azure Active Directory-konton (Azure AD) för att hantera sina resurser, granska användar konton och få åtkomst till tilldelning regelbundet för att säkerställa att kontona och deras åtkomst är giltiga.

Använd åtkomst granskningar i Azure AD för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

Vissa Azure-tjänster har stöd för lokala användare och roller som inte hanteras via Azure AD. Du måste hantera dessa användare separat.

- [Inbyggda roller för virtuella Windows-datorer](rbac.md)

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nödåtkomst i Azure AD

**Vägledning**: Windows Virtual Desktop använder Azure Active Directory (Azure AD) för att hantera sina resurser. För att förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nödåtkomst har ofta hög behörighet och bör inte tilldelas till specifika individer. Konton för nödåtkomst är begränsade till nödsituationer där du inte kan använda normala administratörskonton.

Du bör se till att autentiseringsuppgifterna (som lösenord, certifikat eller smartkort) för nödåtkomstkonton är skyddade och bara kända av personer som har behörighet att använda dem i nödfall.

- [Hantera konton för nödåtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: det virtuella Windows-skrivbordet är integrerat med Azure Active Directory (Azure AD) för att hantera sina resurser. Använd funktioner för hantering av Azure AD-rättighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, granskningar och förfallo datum. I ytterligare stöds även dubbla och flera stegs godkännanden.

- [Vad är åtkomst granskningar för Azure AD](../active-directory/governance/access-reviews-overview.md) 

- [Vad är hantering av Azure AD-berättigande](../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: säkra och isolerade arbets stationer är mycket viktiga för att skydda känsliga roller, till exempel Administratörer, utvecklare och kritiska tjänst operatörer. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter. 

Använd Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP) eller Microsoft Intune för att distribuera en säker och hanterad användar arbets station för administrativa uppgifter. Den skyddade arbets stationen kan hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Distribuera en arbetsstation med privilegierad åtkomst](/azure/active-directory/devices/howto-azure-managed-workstation)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: det virtuella Windows-skrivbordet är integrerat med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera resurserna. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. 

De behörigheter som du tilldelar till resurser med Azure RBAC bör alltid begränsas till de som krävs av rollerna. Detta kompletterar JIT-metoden (just in Time) för Privileged Identity Management (PIM), med Azure Active Directory (Azure AD) och bör granskas regelbundet.

Använd dessutom inbyggda roller för att allokera behörigheter och endast skapa anpassade roller vid behov.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Inbyggda roller för virtuella Windows-datorer](rbac.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support  

**Vägledning**: i support scenarier där Microsoft behöver åtkomst till kund information, stöder Windows Virtual Desktop Customer lockbox för att tillhandahålla ett gränssnitt som du kan använda för att granska och godkänna eller avvisa förfrågningar om kund data åtkomst.

- [Förstå Customer Lockbox](../security/fundamentals/customer-lockbox-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Identifiera, klassificera och märk upp känsliga data

**Vägledning**: identifiera, klassificera och märk känsliga data så att du kan utforma lämpliga kontroller. Detta är att se till att känslig information lagras, bearbetas och överförs säkert av organisationens teknik system.

Använd Azure Information Protection (och dess associerade skannings verktyg) för känslig information i Office-dokument på Azure, lokalt, Office 365 och andra platser.

Du kan använda Azure SQL-Information Protection för att hjälpa till med klassificering och märkning av information som lagras i Azure SQL-databaser.

- [Tagga känslig information med Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Så här implementerar du identifiering av Azure SQL-data](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: skydda känsliga data genom att begränsa åtkomsten med hjälp av Azure Role-baserad Access Control (Azure RBAC), nätverksbaserade åtkomst kontroller och vissa kontroller i Azure-tjänster (till exempel kryptering i SQL och andra databaser).

För att säkerställa konsekvent åtkomstkontroll bör alla typer av åtkomstkontroll justeras mot din strategi för företagssegmentering. Strategin för företagssegmentering bör också informeras om platsen för känsliga eller affärskritiska data och system.

Microsoft behandlar allt kund innehåll som känsligt och skyddar mot kund data förlust och exponering. För att säkerställa att kunddata i Azure alltid är skyddade har Microsoft implementerat vissa standardkontroller och funktioner för dataskydd.

- [Rollbaserad åtkomstkontroll (RBAC) i Azure](../role-based-access-control/overview.md) 

- [Förstå skydd av kunddata i Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Övervaka för obehörig överföring av känsliga data

**Riktlinjer**: Övervaka för att upptäcka obehörig överföring av data till platser som företag inte kan se och kontrollera. Detta omfattar vanligtvis övervakning av avvikande aktiviteter (stora eller ovanliga överföringar) som kan tyda på obehörig dataexfiltrering.

Funktioner för avancerat skydd (ATP) med både Azure Storage och Azure SQL ATP kan varna vid avvikande överföring av information, vilket anger vad som kan vara obehörig överföring av känslig information.

Azure Information Protection (AIP) innehåller övervakningsfunktioner för information som har klassificerats och märkts.

Använd lösningar för data förlust skydd, till exempel värdbaserade, för att genomdriva detektions-och/eller förebyggande kontroller för att förhindra data exfiltrering.

- [Aktivera Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [Aktivera Azure Storage ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](/azure/security/benchmarks/security-controls-v2-asset-management).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker för tillgångar

**Riktlinjer**: Se till att säkerhetsteamen beviljas behörigheter för säkerhetsläsare i din Azure-klient och prenumerationer som gör att de kan övervaka säkerhetsrisker med hjälp av Azure Security Center. 

Beroende på hur säkerhets teamets ansvars områden är strukturerade kan övervakning av säkerhets risker vara ansvaret för en central säkerhets grupp eller ett lokalt team. Detta innebär att säkerhetsinsikter och -risker alltid måste samlas centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Ytterligare behörigheter kan krävas för insyn i arbets belastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning**: Använd taggar för dina Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

Använd inventering av virtuella Azure-datorer för att automatisera insamling av information om program vara på Virtual Machines. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Du kan få åtkomst till installations datum och annan information genom att aktivera diagnostik på gästnivå och ta Windows-händelseloggen till en Log Analytics-arbetsyta.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md) 

- [Beslutsguide för namngivning och taggning av resurser](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Använd Azure Resource Graph till att fråga efter och identifiera resurser i prenumerationerna. Du kan också använda Azure Monitor till att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning**: inte tillämpligt. Det går inte att använda det virtuella Windows-skrivbordet för att garantera säkerheten för till gångar i en process för livs cykel hantering. Det är kundens ansvar att underhålla attribut och nätverkskonfigurationer för till gångar som betraktas som en hög inverkan. 

Vi rekommenderar att kunden skapar en process för att avbilda attributet och ändringar i nätverks konfigurationen, mäter ändrings effekt och skapa reparations uppgifter, om det är tillämpligt.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd villkorlig åtkomst i Azure för att begränsa användarnas möjlighet att interagera med Azures resurs hanterare genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Använd endast godkända program i beräknings resurser

**Vägledning**: Använd inventering av virtuella Azure-datorer för att automatisera insamling av information om all program vara på virtuella datorer. Program varu namn, version, utgivare och uppdaterings tid är tillgängliga från Azure Portal. Du kan få åtkomst till installations datum och annan information genom att aktivera diagnostik på gästnivå och ta Windows-händelseloggen till en Log Analytics-arbetsyta.

- [Så här aktiverar du inventering av virtuella Azure-datorer](../automation/automation-tutorial-installed-software.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: Använd Azure Security Center inbyggd hot identifierings funktion och aktivera Azure Defender (formellt Azure Advanced Threat Protection) för dina Windows-resurser för virtuella skriv bord. Azure Defender för Windows Virtual Desktop tillhandahåller ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja dina Windows-resurser för virtuella skriv bord.

Vidarebefordra alla loggar från Windows Virtual Desktop till din SIEM-lösning (Security information Event Management) som kan användas för att konfigurera anpassade hot identifieringar. Se till att du övervakar olika typer av Azure-tillgångar för potentiella hot och avvikelser. Fokusera på att få aviseringar med hög kvalitet för att minska antalet falska positiva identifieringar för analytiker som ska sorteras. Aviseringar kan hämtas från loggdata, agenter eller andra data.

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection) 

- [Referens guide för Azure Security Center säkerhets varningar](../security-center/alerts-reference.md)

- [Skapa anpassade analys regler för att identifiera hot](../sentinel/tutorial-detect-threats-custom.md) 

- [Cyberhot Hot information med Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

**Vägledning**: Azure Active Directory (Azure AD) innehåller följande användar loggar som kan visas i Azure AD-rapportering eller integreras med Azure Monitor, Azure Sentinel eller annan säkerhets information och händelse hantering (Siem) eller övervaknings verktyg för ytterligare avancerade övervaknings-och analys användnings fall:

- Inloggning – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.

- Riskfylld inloggning – en riskfylld inloggning är en indikator för ett inloggnings försök som kan ha utförts av någon som inte är den legitima ägaren till ett användar konto.

- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Azure Security Center kan också Avisera om vissa misstänkta aktiviteter, till exempel för stora antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. Förutom den grundläggande säkerhetsövervakningen kan skydds modulen i Azure Security Center även samla in mer detaljerade säkerhets varningar från enskilda Azure Compute-resurser (virtuella datorer, behållare, App Service), data resurser (SQL DB och lagring) och Azure Service-lager. Med den här funktionen kan du få insyn i konto avvikelser i de enskilda resurserna.

- [Granska aktivitetsrapporter i Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Aktivera Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

**Vägledning**: det virtuella Windows-skrivbordet skapar eller bearbetar inte DNS-frågemeddelanden (Domain Name Service). Men resurser som är registrerade för tjänsten kan generera flödes loggar.

Aktivera och samla in resurs-och flödes loggar för nätverks säkerhets grupper, Azure Firewall-loggar och WAF-loggar (Web Application Firewall) för säkerhets analys för att stödja incident undersökningar, Hot jakt och generering av säkerhets aviseringar. Du kan skicka flödes loggarna till en Azure Monitor Log Analytics arbets yta och sedan använda Trafikanalys för att ge insikter.

- [Så här aktiverar du flödes loggar för nätverks säkerhets grupper](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure Firewall-loggar och mått](/azure/firewall/logs-and-metrics) 

- [Så här aktiverar och använder du Trafikanalys](../network-watcher/traffic-analytics.md) 

- [Lösningar för övervakning av Azure-nätverk i Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som aktive ras automatiskt, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Windows-resurser för virtuella skriv bord förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, de verktyg som används för att bearbeta och komma åt data samt krav för data lagring.

Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.

Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM (Security information Event Management) från tredje part. Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Publicera Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](/azure/security/benchmarks/security-controls-v2-incident-response).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: Se till att organisationen har processer för svar på säkerhetsincidenter, att processerna är uppdaterade för Azure och att de tränas regelbundet för att säkerställa beredskapen.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar av hög kvalitet

**Vägledning**: Se till att du har en process för att skapa aviseringar av hög kvalitet och mäta aviseringskvaliteten. På så sätt kan du lära dig av tidigare incidenter och prioritera aviseringar till analytiker så att de inte slösar tid på falska positiva identifieringar. 

Du kan basera aviseringar av hög kvalitet på erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar genom att sammanfoga och korrelera olika signalkällor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

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

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data. Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer för alla beräknings resurser, inklusive virtuella datorer, behållare och andra.

Du kan använda anpassade operativ system avbildningar eller Azure Automation tillstånds konfiguration för att upprätta säkerhets konfigurationen för det operativ system som krävs av din organisation.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md) 

- [Windows Virtual Desktop-miljö](environment-setup.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: upprätthålla säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att regelbundet utvärdera och åtgärda konfigurations risker på dina Azure Compute-resurser, inklusive virtuella datorer, behållare och andra. Dessutom kan du använda Azure Resource Manager mallar, anpassade operativ Systems avbildningar eller Azure Automation tillstånds konfiguration för att underhålla säkerhets konfigurationen för det operativ system som krävs av din organisation. Mallarna för Microsoft Virtual Machines tillsammans med Azure Automation tillstånds konfiguration kan hjälpa till att möta och upprätthålla säkerhets kraven.

Avbildningar av virtuella Azure Marketplace-datorer som publiceras av Microsoft hanteras och underhålls av Microsoft.

Azure Security Center kan också söka efter sårbarheter i behållar avbildningen och utföra kontinuerlig övervakning av Docker-konfigurationen i behållare mot Center Internet Securitys Docker benchmark. På sidan med Azure Security Center rekommendationer kan du se rekommendationer och åtgärda problem.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Så här skapar du en virtuell Azure-dator från en ARM-mall](../virtual-machines/windows/ps-template.md) 

- [Översikt över Azure Automation tillstånds konfiguration](../automation/automation-dsc-overview.md) 

- [Containersäkerhet i Security Center](../security-center/container-security.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: lagra anpassade operativ system och behållar avbildningar på ett säkert sätt

**Vägledning**: virtuella Windows-datorer gör det möjligt för kunder att hantera operativ system avbildningar. Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att säkerställa att endast behöriga användare kan komma åt dina anpassade avbildningar. Använd ett galleri för delade Azure-avbildningar du kan dela dina avbildningar till olika användare, tjänstens huvud namn eller Active Directory grupper i din organisation. Lagra behållar avbildningar i Azure Container Registry och Använd RBAC för att säkerställa att endast auktoriserade användare har åtkomst.

- [Förstå Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Så här konfigurerar du Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Översikt över delade avbildnings Galleri](/azure/virtual-machines/windows/shared-image-galleries)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: utför utvärdering av program sårbarhet

**Vägledning**: med Windows Virtual Desktop kan du distribuera dina egna virtuella datorer och registrera dem på tjänsten samt ha SQL-databas som körs i-miljön.

Windows Virtual Desktop kan använda en lösning från tredje part för att utföra sårbarhets bedömningar på nätverks enheter och webb program. När du utför fjärrgenomsökningar ska du inte använda ett enda, beständig, administrativt konto. Överväg att implementera JIT-etablering för skannings kontot. Autentiseringsuppgifterna för skannings kontot bör skyddas, övervakas och används endast för sårbarhets skanning.

Vid behov kan du exportera genomsöknings resultat med jämna mellanrum och jämföra resultaten med tidigare genomsökningar för att kontrol lera att sårbarheter har åtgärd ATS.

Följ rekommendationerna från Azure Security Center för att utföra sårbarhets bedömningar på dina virtuella Azure-datorer (och SQL-servrar). Azure Security Center har en inbyggd sårbarhets-skanner för virtuell dator, behållar avbildningar och SQL-databas.

Vid behov, exportera genomsöknings resultat med jämna mellanrum och jämför resultaten med tidigare genomsökningar för att verifiera att sårbarheter har åtgärd ATS. När du använder rekommendationer för sårbarhets hantering som föreslås av Azure Security Center kan du pivotera till den valda lösningens Portal för att visa historiska skannings data.

- [Så här implementerar du rekommendationer för Azure Security Center sårbarhets bedömning](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [Integrerad sårbarhets-skanner för virtuella datorer](/azure/security-center/built-in-vulnerability-assessment) 
- [SQL sårbarhets bedömning](../azure-sql/database/sql-vulnerability-assessment.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Åtgärda sårbarheter för programvara snabbt och automatiskt

**Vägledning**: det virtuella Windows-skrivbordet använder eller kräver ingen program vara från tredje part. Med Windows Virtual Desktop kan du dock distribuera dina egna virtuella datorer och registrera dem i tjänsten.

Använd Azure Automation Uppdateringshantering eller en lösning från tredje part för att se till att de senaste säkerhets uppdateringarna är installerade på dina virtuella Windows Server-datorer. För virtuella Windows-datorer kontrollerar du att Windows Update har Aktiver ATS och ställts in för automatisk uppdatering.

Använd en lösning för korrigerings hantering från tredje part för program vara från tredje part eller System Center Updates Publisher för Configuration Manager.

- [Så här konfigurerar du Uppdateringshantering för virtuella datorer i Azure](/azure/automation/update-management/overview) 

- [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](/azure/automation/update-management/manage-updates-for-vm)

- [Konfigurera Microsoft Endpoint Configuration Manager för virtuellt Windows-skrivbord](configure-automatic-updates.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: virtuella Windows-datorer tillåter inte att kunder utför sina egna inträngande tester på sina Windows-resurser för virtuella skriv bord.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="endpoint-security"></a>Slut punkts säkerhet

*Mer information finns i [säkerhets principen för Azure-säkerhet: slut punkts säkerhet](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Använd slut punkts identifiering och-svar (EDR)

**Vägledning**: det virtuella Windows-skrivbordet tillhandahåller inte några funktioner för processer för slut punkts avkänning och-svar (EDR). Resurser som är registrerade för tjänsten kan dock dra nytta av slut punkts identifiering och svars funktioner. 

Aktivera funktioner för slut punkts identifiering och-svar för servrar och klienter och integrera dem med SIEM-lösningar (Security information and Event Management) och säkerhets åtgärder.

Avancerat skydd från Microsoft Defender tillhandahåller funktioner för slut punkts identifiering och svar, som en del av en säkerhets plattform för företags slut punkt för att förhindra, identifiera, undersöka och svara på avancerade hot.

- [Översikt över Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Microsoft Defender ATP-tjänsten för Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Microsoft Defender ATP-tjänsten för icke-Windows-servrar](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Microsoft Defender ATP för icke-beständig infrastruktur för virtuella skriv bord](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Använd centralt hanterade moderna program mot skadlig kod

**Vägledning**: skydda dina Windows-virtuella Skriv bords resurser med en centralt hanterad och modern program vara mot skadlig kod som kan användas i real tid och regelbunden sökning.

Azure Security Center kan automatiskt identifiera användningen av ett antal populära lösningar mot skadlig kod för dina virtuella datorer och rapportera statusen för Endpoint Protection-körningen och göra rekommendationer.

Microsoft Antimalware för Azure Cloud Services är standard skyddet mot skadlig kod för virtuella Windows-datorer (VM: ar). Du kan också använda hot identifiering med Azure Security Center för data tjänster för att identifiera skadlig kod som laddats upp till Azure Storage-konton.

- [Så här konfigurerar du Microsoft Antimalware för Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md) 

- [Slut punkts skydds lösningar som stöds](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: se till att program och signaturer för skadlig program vara uppdateras

**Vägledning**: se till att signaturer mot skadlig kod uppdateras snabbt och konsekvent.

Följ rekommendationerna i Azure Security Center: "Compute &amp; Apps" för att se till att alla virtuella datorer och/eller behållare är uppdaterade med de senaste signaturerna.

Microsoft Antimalware kommer automatiskt att installera de senaste signaturerna och motorns uppdateringar som standard.

- [Så här distribuerar du Microsoft Antimalware för Azure Cloud Services och Virtual Machines](../security/fundamentals/antimalware.md) 

- [Endpoint Protection-utvärdering och rekommendationer i Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="backup-and-recovery"></a>Säkerhetskopiering och återställning

*Mer information finns i [Azure Security Benchmark: Säkerhetskopiering och återställning](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Säkerställ vanliga automatiserade säkerhets kopieringar

**Vägledning**: se till att du säkerhetskopierar system och data för att bibehålla affärs kontinuiteten efter en oväntad händelse. Detta bör vara vägledning för alla mål för återställnings punkt mål (återställnings punkt mål) och återställnings tids mål (RTO).

Aktivera Azure Backup och konfigurera säkerhets kopierings källan (t. ex. virtuella Azure-datorer, SQL Server, HANA-databaser eller fil resurser) samt önskad frekvens och kvarhållningsperiod.

För en högre nivå av redundans kan du aktivera Geo-redundant lagring för att replikera säkerhetskopierade data till en sekundär region och återställa med återställning mellan regioner.

- [Affärskontinuitet och haveriberedskap i företagsskala](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Så här aktiverar du Azure Backup](/azure/backup/) 

- [Aktivera återställning mellan regioner](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [Så här konfigurerar du en verksamhets kontinuitet och katastrof återställnings plan i Windows Virtual Desktop](disaster-recovery.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="br-2-encrypt-backup-data"></a>BR-2: kryptera säkerhets kopierings data

**Vägledning**: se till att dina säkerhets kopior skyddas mot attacker. Detta bör innefatta kryptering av säkerhets kopiorna för att skydda mot förlust av konfidentialitet.

För vanlig säkerhets kopiering av Azure-tjänsten krypteras säkerhetskopierade data automatiskt med hjälp av Azure Platform-hanterade nycklar. Du kan välja att kryptera säkerhets kopian med kundhanterad nyckel. I det här fallet ser du till att den här Kundhanterade nyckeln i nyckel valvet också finns i säkerhets kopierings omfånget.

Använd rollbaserad åtkomst kontroll i Azure Backup, Azure Key Vault eller andra resurser för att skydda säkerhets kopior och Kundhanterade nycklar. Dessutom kan du aktivera avancerade säkerhetsfunktioner för att kräva multifaktorautentisering innan säkerhets kopior kan ändras eller tas bort.

Översikt över säkerhetsfunktioner i Azure Backup/Azure/Backup/Security-Overview 

- [Kryptering av säkerhetskopieringsdata med kundhanterade nycklar](/azure/backup/encryption-at-rest-with-cmk) 

- [Säkerhetskopiera Key Vault nycklar i Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [Säkerhetsfunktioner som hjälper till att skydda hybrid säkerhets kopieringar från attacker](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validera all säkerhetskopiering med kundhanterade nycklar

**Vägledning**: Vi rekommenderar att du verifierar data integriteten på säkerhets kopierings medier regelbundet genom att utföra en data återställnings process för att säkerställa att säkerhets kopieringen fungerar korrekt.

- [Återställa filer från säkerhets kopiering av virtuella Azure-datorer](/azure/backup/backup-azure-restore-files-from-vm)

- [Säkerhets implementering](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av inbyggda säkerhetsfunktioner för Azure- och tredje partsdata

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Läs mer i följande referenser:
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark – hantering av tillgångar](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure Security Benchmark – dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera företagets segmenteringsstrategi 

**Vägledning**: Upprätta en strategi för hela företaget gällande att segmentera åtkomsten till tillgångar utifrån en kombination av identiteter, nätverk, program, prenumerationer, hanteringsgrupper och andra kontroller.

Du måste noga avväga behovet av separationsskyddet med behovet att underlätta den dagliga driften av de system som måste kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent för olika kontrolltyper som nätverkssäkerhet, modeller för identiteter och åtkomst, modeller för appbehörighet och appåtkomst samt kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmenteringen efter företagets segmenteringsstrategi](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera en strategi för hantering av säkerhetspositionen

**Vägledning**: Mät och minimera risker gällande enskilda tillgångar och miljöerna där de finns regelbundet. Prioritera värdefulla tillgångar och attackytor med stor exponering, som publicerade appar, in- och utgångar i nätverket och slutpunkter för användare och administratörer.

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera organisationens roller och ansvarsområden

**Vägledning**: Dokumentera och förmedla en tydlig strategi gällande säkerhetsorganisationens roller och ansvarsområden. Prioritera att delegera ett tydligt ansvar för olika säkerhetsbeslut och utbilda alla kring modellen med gemensamt ansvar, och ge de tekniska teamen den utbildning som behövs kring tekniken för att skydda molnet.

- [Regelverk för Azure-säkerhet 1 – personal: utbilda teamen om molnsäkerhetsresan](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Regelverk för Azure-säkerhet 2 – personal: utbilda teamen om molnsäkerhetstekniken](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Regelverk för Azure-säkerhet 3 – personal: tilldela ansvar för molnsäkerhetsbeslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

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

- [Azure Security Benchmark – nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security)

- [Översikt över nätverkssäkerhet i Azure](../security/fundamentals/network-overview.md)

- [En strategi för företagets nätverksarkitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera en strategi för identiteter och privilegierad åtkomst

**Vägledning**: Upprätta en strategi för identiteter och privilegierad åtkomst i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Ett centraliserat system för identiteter och autentisering och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användningsfall och scenarier

-   Skydda och användare med hög behörighet

-   Övervaka och hantera avvikande användaraktiviteter  

-   Process för att granska och stämma av identiteter och åtkomstbehörighet

Läs mer i följande referenser:

- [Azure Security Benchmark – hantering av identiteter](/azure/automation/update-management/overview)

- [Azure Security Benchmark – privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

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

- [Azure Security Benchmark – loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark – svar på incidenter](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Hantering och övervakning i företagsskala i Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Läs mer om [säkerhetsbaslinjer för Azure](/azure/security/benchmarks/security-baselines-overview)
