---
title: Azures säkerhets bas linje för Azure Active Directory
description: Azure Active Directory säkerhets bas linje ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: active-directory
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 8d4203b7b5d7742bf198778864fa4f42b7423596
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107286025"
---
# <a name="azure-security-baseline-for-azure-active-directory"></a>Azures säkerhets bas linje för Azure Active Directory

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark-version 2,0](../../security/benchmarks/overview.md) till Azure Active Directory. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter de **säkerhets kontroller** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azure Active Directory. 

> [!NOTE]
> **Kontroller** som inte är tillämpliga på Azure Active Directory, eller för vilka ansvaret är Microsofts, har uteslutits från denna bas linje. Om du vill se hur Azure Active Directory helt mappar till Azures säkerhets mätning, se den [fullständiga Azure Active Directory mappnings filen för säkerhets bas linjen](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](/azure/security/benchmarks/security-controls-v2-network-security).*

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: använd Azure Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg som kon figurer ATS för dina Azure Active Directory resurser. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet, t. ex. "AzureActiveDirectory" i lämpligt käll-eller mål fält för en regel, kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. 

- [Förstå och använda service märken](../../virtual-network/service-tags-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](/azure/security/benchmarks/security-controls-v2-identity-management).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Standardisera Azure Active Directory som centralt system för identiteter och autentisering

**Vägledning**: använda Azure Active Directory (Azure AD) som standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i: 
- Microsoft Cloud resurser, till exempel Azure Portal, Azure Storage, virtuell Azure-dator (Linux och Windows), Azure Key Vault, PaaS och SaaS program. 
- Organisationens resurser, till exempel program i Azure eller företagets nätverksresurser. 
 

Skyddet av Azure AD bör ha hög prioritet i organisationens säkerhetspraxis för molnet. Azure AD ger dig en identitetsskyddspoäng som hjälper dig att utvärdera identiteternas säkerhet i relation till Microsofts rekommendationer kring regelverk. Använd poängen till att mäta hur nära konfigurationen matchar rekommendationerna kring regelverk och förbättra säkerhetsläget. 

Azure AD stöder extern identitet som gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet. 

- [Klientorganisation i Azure Active Directory](../develop/single-and-multi-tenant-apps.md)

- [Skapa och konfigurera en Azure AD-instans](active-directory-access-create-new-tenant.md)

- [Använda en extern identitetsprovider för appar](/azure/active-directory/b2b/identity-providers)

- [Vad är identitetsskyddspoängen i Azure Active Directory](identity-secure-score.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Hantera appidentiteter säkert och automatiskt

**Vägledning**: Använd hanterad identitet för Azure-resurser för icke-mänskliga konton, t. ex. tjänster eller automatisering, vi rekommenderar att du använder Azure-hanterad identitets funktion i stället för att skapa ett mer kraftfullt humant konto för att komma åt eller köra dina resurser. Du kan internt autentisera till Azure-tjänster/-resurser som stöder Azure Active Directory (Azure AD)-autentisering genom fördefinierad åtkomst beviljande regel utan att använda autentiseringsuppgifter som är hårdkodade i källkod eller konfigurationsfiler. Du kan inte tilldela Azure-hanterade identiteter till Azure AD-resurser, men Azure AD är mekanismen för att autentisera med hanterade identiteter som har tilldelats till andra tjänst resurser.

- [Hanterad identitet för Azure-resurser](../managed-identities-azure-resources/overview.md)

- [Tjänster som stöder hanterad identitet för Azure-resurser](../managed-identities-azure-resources/services-support-managed-identities.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning i Azure AD (SSO) för åtkomst till appar

**Vägledning**: Använd Azure Active Directory för att tillhandahålla identitets-och åtkomst hantering för Azure-resurser, moln program och lokala program. I det här ingår företagsidentiteter som anställda samt externa identiteter som partner, och leverantörer. På så sätt kan enkel inloggning (SSO) hantera och skydda åtkomsten till organisationens data och resurser både lokalt och i molnet. Anslut alla användare, appar och enheter till Azure AD för en smidig och säker åtkomst samt bättre insyn och kontroll.

 
- [Förstå SSO för appar i Azure AD](../manage-apps/what-is-single-sign-on.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd kraftfulla verifieringskontroller för all Azure Active Directory-baserad åtkomst

**Vägledning**: Använd Azure Active Directory för att stödja starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.
- Multifaktorautentisering – aktivera MFA i Azure AD och följ rekommendationerna kring hantering av identiteter och åtkomst i Azure Security Center så att du använder de bästa regelverken i din MFA-konfiguration. MFA kan tillämpas för alla användare, vissa användare eller på användarnivå baserat på inloggningsförhållanden och riskfaktorer.
- Lösen ords kryptering – tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort.

För administratörer och privilegierade användare bör du se till att den högsta nivån för metoden stark autentisering används, följt av lämplig princip för stark autentisering för andra användare.

Azure AD har stöd för äldre lösenordsbaserad autentisering, till exempel endast molnbaserade konton (användar konton som skapats direkt i Azure AD) som har en bas linje för lösen ords principer eller hybrid konton (användar konton som kommer från lokala Active Directory) som följer de lokala lösen ords principerna. När du använder lösenordsbaserad autentisering ger Azure AD en funktion för lösen ords skydd som hindrar användare från att ange lösen ord som är lätta att gissa. Microsoft tillhandahåller en global lista över blockerade lösen ord som uppdateras utifrån telemetri, och kunderna kan förstärka listan utifrån deras behov (t. ex. varumärkes-, kultur referenser osv.). Detta lösen ords skydd kan endast användas för moln-och hybrid konton.

Autentisering utifrån enbart autentiseringsuppgifter för lösen ord är känsligt för populära angrepps metoder. För högre säkerhet använder du stark autentisering, till exempel MFA och en princip för starka lösen ord. För program från tredje part och Marketplace-tjänster som kan ha standard lösen ord bör du ändra dem vid den första installationen av tjänsten.

 
- [Så här distribuerar du Azure AD MFA](../authentication/howto-mfa-getstarted.md) 

 

 
- [Introduktion till alternativ för lösenordsfri autentisering för Azure Active Directory](../authentication/concept-authentication-passwordless.md) 

 

 
- [Standardprincip för lösenord för Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera felaktiga lösen ord med Azure AD-lösenordsskydd](../authentication/concept-password-ban-bad.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Övervaka och skicka aviseringar vid kontoavvikelser

**Vägledning**: Azure Active Directory tillhandahåller följande data Källor:

 
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.

 
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.

 
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

 
- Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

 

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

 

 
Azure Security Center kan också avisera om vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök eller föråldrade konton i prenumerationen.

 

 
Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda Active Directory-signaler för att identifiera, upptäcka och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

 

 
- [Granska aktivitetsrapporter i Azure Active Directory](../reports-monitoring/concept-audit-logs.md) 

 

 
- [Så visar du riskfyllda inloggningar för Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

 

 
- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

 

 
- [Så här övervakar du användarnas identitets- och åtkomstrelaterade aktiviteter i Azure Security Center](../../security-center/security-center-identity-access.md) 

 

 
- [Aviseringar i skyddsmodulen för hotinformation i Azure Security Center](../../security-center/alerts-reference.md) 

 

 
- [Så här integrerar du Azures aktivitetsloggar i Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: Använd Azure Active Directory (Azure AD) villkorlig åtkomst för en mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, t. ex. användar inloggningar från vissa IP-intervall måste använda MFA för inloggning. Du kan även använda detaljerade policyer för hantering av sessionsautentisering för olika användningsfall.

 
- [Översikt över villkorlig åtkomst för Azure AD](../conditional-access/overview.md) 

 

 
- [Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md) 

 

 
- [Konfigurera autentiseringsessionshantering med villkorsstyrd åtkomst](../conditional-access/howto-conditional-access-session-lifetime.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: säker användar åtkomst till äldre program

**Vägledning**: se till att du har moderna åtkomst kontroller och övervakning av sessioner för äldre program och de data som de lagrar och bearbetar. Även om VPN används ofta för att komma åt äldre program, har de ofta bara grundläggande åtkomst kontroll och begränsad övervakning av sessionen.

 
Med Azure AD-programproxy kan du publicera äldre lokala program till fjärran vändare med enkel inloggning samtidigt som man uttryckligen validerar trovärdighet för både fjärranslutna användare och enheter med villkorlig åtkomst i Azure AD.

 

 
Alternativt är Microsoft Cloud App Security en CASB-tjänst (Cloud Access Security Broker) som kan tillhandahålla kontroller för övervakning av användares programsessioner och blockera åtgärder (för både äldre lokala program och SaaS-program (Cloud program vara som en tjänst).

 

 
- [Azure Active Directory-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy#what-is-application-proxy) 

 

 
- [Metod tips för Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: Skydda och begränsa privilegierade användare

**Vägledning**: de mest kritiska inbyggda rollerna är Azure AD global administratör och administratör för privilegierade roller, eftersom användare som tilldelats dessa två roller kan delegera administratörs roller:

- Global administratör: användare med den här rollen har åtkomst till alla administrativa funktioner i Azure AD, samt tjänster som använder Azure AD-identiteter.

- Privilegie rad roll administratör: användare med den här rollen kan hantera roll tilldelningar i Azure AD, samt i Azure AD Privileged Identity Management (PIM). Dessutom tillåter den här rollen hantering av alla aspekter av PIM och administrativa enheter.

Du kan ha andra viktiga roller som behöver regleras om du använder anpassade roller med vissa privilegierade behörigheter tilldelade. Och du kanske också vill använda liknande kontroller för administratörs kontot för kritiska företags till gångar.

Azure AD har mycket privilegierade konton: användare och tjänstens huvud namn som är direkt eller indirekt tilldelade eller berättigade till, rollen global administratör eller privilegie rad roll administratör och andra privilegierade roller i Azure AD och Azure.

Begränsa antalet konton med hög behörighet och skydda dessa konton på en utökad nivå eftersom användare med den här behörigheten kan direkt eller indirekt läsa och ändra alla resurser i din Azure-miljö.

Du kan aktivera just-in-time (JIT)-privilegierad åtkomst till Azure-resurser och Azure AD med hjälp av Azure AD Privileged Identity Management (PIM). JIT beviljar temporära behörigheter för att utföra privilegierade uppgifter endast när användarna behöver det. PIM kan också generera säkerhetsaviseringar när det finns misstänkt eller osäker aktivitet i din Azure AD-organisation.

- [Behörigheter för administratörsrollen i Azure AD](/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- [Använda säkerhetsaviseringar från Privileged Identity Management i Azure](../privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: Använd Azure Active Directory Privileged Identity Management och Multi-Factor Authentication för att begränsa administrativ åtkomst till affärs kritiska system.

- [Privileged Identity Management godkännande av roll aktiverings begär Anden](../privileged-identity-management/azure-ad-pim-approval-workflow.md)

- [Multi-Factor Authentication och villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Granska och stäm av användarnas åtkomst regelbundet

**Vägledning**: granska användar kontots åtkomst tilldelningar regelbundet för att se till att kontona och deras åtkomst är giltiga, särskilt fokuserade på de mycket privilegierade rollerna, inklusive global administratör och administratör för privilegierade roller. Du kan använda åtkomst granskningar för Azure Active Directory (Azure AD) för att granska grupp medlemskap, åtkomst till företags program och roll tilldelningar, både för Azure AD-roller och Azure-roller. Azure AD repor ting kan tillhandahålla loggar för att identifiera inaktuella konton. Du kan också använda Azure AD Privileged Identity Management för att skapa åtkomst gransknings rapportens arbets flöde för att under lätta gransknings processen.

Dessutom kan Azure Privileged Identity Management också konfigureras för att varna när ett högt antal administratörs konton skapas och för att identifiera administratörs konton som är inaktuella eller felaktigt konfigurerade.

- [Skapa en åtkomst granskning av Azure AD-roller i Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md)

- [Skapa en åtkomst granskning av Azure Resource roles i Privileged Identity Management (PIM)](../privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../governance/access-reviews-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Konfigurera nödåtkomst i Azure AD

**Vägledning**: om du vill förhindra oavsiktligt låst av din Azure AD-organisation konfigurerar du ett konto för nöd åtkomst för åtkomst när normala administrativa konton inte kan användas. Konton för nödåtkomst har ofta hög behörighet och bör inte tilldelas till specifika individer.
Konton för nödåtkomst är begränsade till nödsituationer där du inte kan använda normala administratörskonton.

Du bör se till att autentiseringsuppgifterna (som lösenord, certifikat eller smartkort) för nödåtkomstkonton är skyddade och bara kända av personer som har behörighet att använda dem i nödfall.

- [Hantera konton för nödåtkomst i Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-5-automate-entitlement-management"></a>PA-5: automatisera rättighets hantering 

**Vägledning**: Använd funktioner för hantering av Azure AD-behörighet för att automatisera åtkomst till begär ande arbets flöden, inklusive åtkomst tilldelningar, recensioner och förfallo datum. Det finns också stöd för godkännande med dubbla eller flera steg.

- [Vad är hantering av Azure AD-berättigande](../governance/entitlement-management-overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: Det är viktigt med säkra och isolerade arbetsstationer för att skydda känsliga roller som administratörer, utvecklare och operatörer av kritiska tjänster. Använd hög säkra användar arbets stationer och/eller Azure-skydds för administrativa uppgifter. Använd Azure Active Directory, Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune när du ska distribuera en säker och hanterad arbetsstation för administrativa uppgifter. Skyddade arbetsstationer kan hanteras centralt för att upprätthålla en säker konfiguration med kraftfulla baslinjer för autentisering, programvara och maskinvara samt begränsad logisk och nätverksbaserad åtkomst.

- [Skydda enheter som en del av privilegie rad åtkomst](/security/compass/privileged-access-devices)

- [Implementering av privilegierad åtkomst](/security/compass/privileged-access-deployment)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: kunderna kan konfigurera sin Azure Active Directory (Azure AD)-distribution för minsta behörighet genom att tilldela användare till rollerna med de lägsta behörigheter som krävs för att användarna ska kunna utföra sina administrativa uppgifter.

- [Behörigheter för administratörsrollen i Azure AD](../roles/permissions-reference.md)

- [Tilldela administrativa roller i Azure AD](../roles/manage-roles-portal.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Välj godkännande process för Microsoft Support  

**Vägledning**: Azure Active Directory stöder inte kundsäkerhet. Microsoft kan arbeta med kunder via icke-säkra metoder för godkännande för åtkomst till kunddata.

**Ansvar**: Delad

**Azure Security Center övervakning**: ingen

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](/azure/security/benchmarks/security-controls-v2-data-protection).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Skydda känsliga data

**Vägledning**: Överväg följande rikt linjer för att implementera skydd av känsliga data:

- **Isolering:** En katalog är den data gränser som används av Azure Active Directory (Azure AD)-tjänster för att lagra och bearbeta data för en kund. Kunderna bör avgöra var de vill att de flesta Azure AD-kunddata ska finnas genom att ange egenskapen land i sin katalog.

- **Segmentering:** Den globala administratörs rollen har fullständig kontroll över alla katalog data och reglerna som styr åtkomst-och bearbetnings instruktioner. En katalog kan vara segmenterad i administrativa enheter och tillhandahållas med användare och grupper som ska hanteras av administratörer av dessa enheter. globala administratörer kan delegera ansvaret till andra principer i organisationen genom att tilldela dem till fördefinierade roller eller anpassade roller som de kan skapa.

 
- **Åtkomst:** Behörigheter kan tillämpas på användare, grupper, roller, program eller enheter. Tilldelningen kan vara permanent eller temporal per Privileged Identity Management konfiguration. 
  
- **Kryptering:** Azure AD krypterar alla data i vila eller under överföring. Erbjudandet tillåter inte att kunder krypterar katalog data med sin egen krypterings nyckel. 

För att avgöra hur deras valda land mappar till den fysiska platsen för deras katalog, se artikeln "var finns din data".

- [Var finns artikeln](https://www.microsoft.com/trust-center/privacy/data-location)

När kunden använder olika Azure AD-verktyg, funktioner och program som interagerar med sina kataloger, använder du artikeln Azure Active Directory – var finns dina data?

- [Var finns din data instrument panel](https://msit.powerbi.com/view?r=eyJrIjoiYzEyZTc5OTgtNTdlZS00ZTVkLWExN2ItOTM0OWU4NjljOGVjIiwidCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsImMiOjV9)

- [Dokumentation om Azure AD-roller](/azure/active-directory/roles/)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker (t. ex. trafik insamling) som använder kryptering för att säkerställa att angripare inte kan läsa eller ändra data.

Azure AD har stöd för data kryptering i överföring med TLS v 1.2 eller senare.

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center.

- [Förstå kryptering i överföring med Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit) 

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem) 

- [Double Encryption för Azure-data under överföring](https://docs.microsoft.com/azure/security/fundamentals/double-encryption#data-in-transit)

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

- [Översikt över Azure-hanteringsgrupper](../../governance/management-groups/overview.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: begränsa användarnas möjlighet att interagera med Azure Resource Manager

**Vägledning**: Använd Azure Active Directory (Azure AD) villkorlig åtkomst för att begränsa användarnas möjlighet att interagera med Azure AD via Azure Portal genom att konfigurera "blockera åtkomst" för appen "Microsoft Azure hantering".

- [Så här konfigurerar du villkorlig åtkomst för att blockera åtkomst till Azures resurs hanterare](../../role-based-access-control/conditional-access-azure-management.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivera hot identifiering för Azure-resurser

**Vägledning**: använd den Azure Active Directory (Azure AD) Identity Protection inbyggd hot identifierings funktion för dina Azure AD-resurser. 
 
 
 

 
Azure AD genererar aktivitets loggar som ofta används för hot identifiering och hot jakt. Inloggnings loggarna i Azure AD innehåller en post med autentiserings-och behörighets aktiviteter för användare, tjänster och appar. Azure AD audit-loggar spårar ändringar som gjorts i en Azure AD-klient, inklusive ändringar som förbättrar eller minskar säkerheten position.

- [Vad är Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)

- [Anslut Azure AD Identity Protection data till Azure Sentinel](../../sentinel/connect-azure-ad-identity-protection.md)

- [Anslut Azure Active Directory data till Azure Sentinel](../../sentinel/connect-azure-active-directory.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivera hotidentifiering i hanteringen av identiteter och åtkomst i Azure

**Vägledning**: Azure Active Directory (Azure AD) innehåller följande användar loggar som kan visas i Azure AD repor ting eller integreras med Azure Monitor, Azure Sentinel eller andra Siem/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall: 
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter. 
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer. 
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
- Riskfyllda användare – en riskfylld användare är en indikator för ett användar konto som kan ha komprometterats. 

Identifieringar av identitets skydd är aktiverade som standard och kräver ingen onboarding-process. Granularitet eller risk data avgörs av licens-SKU: n. 

- [Granska aktivitetsrapporter i Azure Active Directory](../reports-monitoring/concept-audit-logs.md)  

- [Aktivera Azure Identity Protection](../identity-protection/overview-identity-protection.md)  

- [Skydd mot hot i Azure Security Center](/azure/security-center/threat-protection)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: Azure Active Directory (Azure AD) skapar aktivitets loggar. Till skillnad från vissa Azure-tjänster är Azure AD ingen åtskillnad mellan aktivitets-och resurs loggar. Aktivitets loggar är automatiskt tillgängliga i avsnittet Azure AD i Azure Portal och kan exporteras till Azure Monitor, Azure Event Hubs, Azure Storage, Siem och andra platser.
 
- Inloggningsaktiviteter – Rapporten för inloggningsaktiviteter ger information om användningen av hanterade program och användares inloggningsaktiviteter.  
 
- Granskningsloggar – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. Exempel på granskningsloggar är de resursändringar som görs i Azure AD, som att lägga till eller ta bort användare, appar, grupper, roller och principer.  
 
 

Azure AD innehåller också säkerhetsrelaterade loggar som kan visas i Azure AD-rapportering eller integreras med Azure Monitor, Azure Sentinel eller andra SIEM/övervaknings verktyg för mer avancerade övervaknings-och analys användnings fall: 
- Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.
- Riskfyllda användare – en riskfylld användare är en indikator för ett användar konto som kan ha komprometterats. 

Identifieringar av identitets skydd är aktiverade som standard och kräver ingen onboarding-process. Granularitet eller risk data avgörs av licens-SKU: n. 

 
- [Aktivitets-och säkerhets rapporter i Azure Active Directory](../reports-monitoring/overview-reports.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Central hantering och analys av säkerhetsloggar

**Vägledning**: Vi rekommenderar följande rikt linjer när kunder vill centralisera sina säkerhets loggar för att förenkla hot och position analys:
 
- Centralisera loggning av lagring och analys för att aktivera korrelation. För varje logg källa i Azure AD ser du till att du har tilldelat en data ägare, åtkomst vägledning, lagrings plats, vilka verktyg som används för att bearbeta och komma åt data samt krav för data lagring.  
 
- Se till att du integrerar Azures aktivitets loggar i den centrala loggningen. Mata in loggar via Azure Monitor för att samla in säkerhets data som genererats av slut punkts enheter, nätverks resurser och andra säkerhets system. I Azure Monitor använder du Log Analytics arbets ytor för att fråga och utföra analyser och använda Azure Storage konton för långsiktig lagring.  
 
- Du kan också aktivera och publicera data till Azure Sentinel eller en SIEM från tredje part.  
 

Många organisationer väljer att använda Azure Sentinel för "heta" data som används ofta och Azure Storage för "kall" data som används mindre ofta.  
 

- [Samla in plattforms loggar och mått med Azure Monitor](/azure/azure-monitor/platform/diagnostic-settings)   
 

- [Publicera Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurera kvarhållning av loggar

**Vägledning**: kontrol lera att alla lagrings konton eller Log Analytics arbets ytor som används för att lagra Azure Active Directory inloggnings loggar, gransknings loggar och risk data loggar har den logg kvarhållningsperiod som anges enligt organisationens regler för efterlevnad.

I Azure Monitor kan du Log Analytics ange arbets ytans lagrings period enligt organisationens regler för efterlevnad. Använd Azure Storage-, Data Lake-eller Log Analytics arbets ytans konton för långsiktig lagring och arkivering.

- [Så här konfigurerar du Log Analytics bevarande period för arbets ytor](/azure/azure-monitor/platform/manage-cost-storage)  

- [Lagra resurs loggar i ett Azure Storage konto](/azure/azure-monitor/platform/resource-logs-collect-storage)

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

- [Konfigurera en säkerhetskontakt i Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet 

**Vägledning**: Se till att du har en process för att skapa aviseringar av hög kvalitet och mäta aviseringskvaliteten. På så sätt kan du lära dig av tidigare incidenter och prioritera aviseringar till analytiker så att de inte slösar tid på falska positiva identifieringar. 

Du kan basera aviseringar av hög kvalitet på erfarenheter från tidigare incidenter, validerade community-källor och verktyg som utformats för att skapa och rensa aviseringar genom att sammanfoga och korrelera olika signalkällor. 

Azure Security Center ger aviseringar med hög kvalitet över flera Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Riktlinjer**: Se till att analytiker kan fråga och använda olika datakällor när de undersöker potentiella incidenter, så att de kan skapa en fullständig överblick över vad som hände. Diverse loggar bör samlas in för att spåra en potentiell angripares aktiviteter över hela händelsekedjan för att undvika att blinda fläckar.  Du bör också se till att insikter och kunskaper fångas upp för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighets graden baseras på hur tillförlitlig Security Center är i sökningen eller analysen som används för att utfärda aviseringen, samt den konfidensnivå som det fanns skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](/azure/azure-resource-manager/resource-group-using-tags)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](https://docs.microsoft.com/azure/security-center/tutorial-security-incident#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Upprätta säkra konfigurationer för Azure-tjänster 

**Vägledning**: Microsofts lösningar för identitets-och åtkomst hantering hjälper till att skydda åtkomsten till program och resurser lokalt och i molnet. Det är viktigt att organisationer följer rekommenderade säkerhets metoder för att säkerställa att implementeringen av identitets-och åtkomst hantering är säker och mer elastisk för attacker. 

Baserat på din implementerings strategi för identitets-och åtkomst hantering bör din organisation följa Microsofts vägledning för bästa praxis för att skydda din identitets infrastruktur. 

Organisationer som samarbetar med externa partners bör dessutom utvärdera och implementera lämpliga konfigurationer för styrning, säkerhet och efterlevnad för att minska säkerhets risken och skydda känsliga resurser.

- [Azure Identity Management och regelverk kring åtkomstkontroll och säkerhet](../../security/fundamentals/identity-management-best-practices.md)

- [Fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md)

- [Skydda externt samarbete i Azure Active Directory och Microsoft 365](secure-external-access-resources.md)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Underhåll säkra konfigurationer för Azure-tjänster

**Vägledning**: Microsoft Secure Scores ger organisationer en mätning av sina säkerhets position och rekommendationer som kan hjälpa till att skydda organisationer mot hot. Vi rekommenderar att organisationer regelbundet granskar sina säkra Poäng för föreslagna förbättrings åtgärder för att förbättra sina position för identitets säkerhet. 

- [Vad är säkerhets poängen för identiteter i Azure Active Directory?](identity-secure-score.md)

- [Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score)

**Ansvar**: Kund

**Azure Security Center övervakning**: ingen

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../../security/fundamentals/pen-testing.md)

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
- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption)
 

 
- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../../security/fundamentals/encryption-overview.md) 

 
- [Cloud Adoption Framework – regelverk kring datasäkerhet och kryptering i Azure](../../security/fundamentals/data-encryption-best-practices.md) 

 
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

- [Översikt över nätverkssäkerhet i Azure](../../security/fundamentals/network-overview.md)

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

- [Översikt över säker identitetshantering i Azure](../../security/fundamentals/identity-management-overview.md)

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
