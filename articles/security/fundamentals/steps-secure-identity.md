---
title: Skydda din Azure AD Identity-infrastruktur
titleSuffix: Azure Active Directory
description: Det här dokumentet beskriver en lista över viktiga åtgärder som administratörer ska implementera för att skydda sin organisation med Azure AD-funktioner
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: ffc5bafca305086b0c524c76eb91b8aec2e2655d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602449"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fem steg för att skydda din identitets infrastruktur

Om du läser det här dokumentet är du medveten om betydelsen av säkerhet. Du bär förmodligen redan ansvaret för att skydda din organisation. Om du behöver övertyga andra om vikten av säkerhet kan du skicka dem för att läsa den senaste [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/security/business/security-intelligence-report).

Med det här dokumentet får du en säkrare position med funktionerna i Azure Active Directory genom att använda en check lista för fem steg för att Inokulera din organisation mot cyberhot-attacker.

Den här check listan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

* Förstärk dina autentiseringsuppgifter.
* Minska attack ytan.
* Automatisera hot svar.
* Använd Cloud Intelligence.
* Aktivera självbetjäning för slutanvändare.

Se till att du håller reda på vilka funktioner och steg som är slutförda när du läser den här check listan.

> [!NOTE]
> Många av rekommendationerna i det här dokumentet gäller endast för program som har kon figurer ATS för att använda Azure Active Directory som identitets leverantör. När du konfigurerar appar för en enda Sign-On säkerställer du fördelarna med principer för autentiseringsuppgifter, Hot identifiering, granskning, loggning och andra funktioner som läggs till i dessa program. [Azure AD Application Management](../../active-directory/manage-apps/what-is-application-management.md) är grunden som alla dessa rekommendationer baseras på.

Rekommendationerna i det här dokumentet är justerade med [identitetens säkra Poäng](../../active-directory/fundamentals/identity-secure-score.md), en automatisk utvärdering av din Azure AD-klients identitets säkerhets konfiguration. Organisationer kan använda sidan för säker användning av identiteter på Azure AD-portalen för att hitta luckor i den aktuella säkerhets konfigurationen för att se till att de följer aktuella Microsofts [bästa praxis](identity-management-best-practices.md) för säkerhet. Genom att implementera varje rekommendation på sidan säker Poäng ökar du dina poäng och du kan spåra dina framsteg, samt hjälpa dig att jämföra din implementering mot andra liknande storleks organisationer eller din bransch.

![Identifiera säkra Poäng för identitet](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Många av de funktioner som beskrivs här kräver en Azure AD Premium prenumeration, medan vissa är kostnads fria. Läs igenom vår [Azure Active Directory priser](https://azure.microsoft.com/pricing/details/active-directory/) och [Check lista för Azure AD-distribution](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) om du vill ha mer information.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Innan du börjar: skydda privilegierade konton med MFA

Innan du börjar check listan, se till att du inte blir komprometterad när du läser den här check listan. Du måste först skydda dina privilegierade konton.

Angripare som får kontroll över privilegierade konton kan orsaka mycket allvarliga skador, så det är viktigt att skydda kontona först. Aktivera och Kräv [Azure ad Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) för alla administratörer i din organisation med hjälp av [standardinställningar för Azure AD-säkerhet](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) eller [villkorlig åtkomst](../../active-directory/conditional-access/plan-conditional-access.md). Om du inte har implementerat MFA kan du göra det nu! Det är viktigt.

Har du angett? Vi börjar med check listan.

## <a name="step-1---strengthen-your-credentials"></a>Steg 1 – Förstärk dina autentiseringsuppgifter

De flesta säkerhets överträdelser i företaget kommer att ha ett konto som har komprometterats med en fåtal av metoder, till exempel lösen ords spray, intrång, omuppspelning eller nätfiske. Läs mer om dessa angrepp i den här videon (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Kontrol lera att din organisation använder stark autentisering

Med hänsyn till frekvensen av lösen ord som gissas, phished, blir stulen med skadlig kod eller återanvänds, är det viktigt att du säkerhetskopierar lösen ordet med någon form av stark autentiseringsuppgift – Läs mer om [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Om du vill aktivera den grundläggande säkerhets nivån för identiteter kan du använda det här alternativet för att aktivera med [Azure AD-säkerhetsinställningarna](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Säkerhets standarder upprätthåller Azure AD MFA för alla användare i en klient organisation och blockerar inloggningar från äldre protokoll för hela klienten.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Starta förbjuda ofta angrepp av lösen ord och inaktivera traditionell komplexitet och utgångs regler.

Många organisationer använder den traditionella komplexiteten (krav på specialtecken, siffror, versaler och gemener) och regler för lösen ordets giltighets tid. [Microsofts forskning](https://aka.ms/passwordguidance) har visat dessa principer som gör det lättare för användarna att välja lösen ord som är lättare att gissa.

Funktionen för [dynamiskt blockerade lösen ord](../../active-directory/authentication/concept-password-ban-bad.md) i Azure AD använder nuvarande angrepps beteende för att hindra användare från att ange lösen ord som lätt kan gissas. Den här funktionen är alltid på när användare skapas i molnet, men är nu även tillgängligt för Hybrid organisationer när de distribuerar [Azure AD Password Protection för Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Lösen ords skydd i Azure AD hindrar användare från att välja dessa vanliga lösen ord och kan utökas för att blockera lösen ord som innehåller anpassade nyckelord som du anger. Du kan till exempel hindra användarna från att välja lösen ord som innehåller företagets produkt namn eller ett lokalt sport team.

Microsoft rekommenderar att du antar följande modern lösen ords princip baserat på [NIST-vägledning](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Kräv lösen ord måste innehålla minst 8 tecken. Längre är inte nödvändigt vis bättre, eftersom de gör det möjligt för användarna att välja förutsägbara lösen ord, spara lösen ord i filer eller skriva ned dem.
2. Inaktivera utgångs regler, som gör att användarna lätt kan gissa lösen ord, till exempel **Spring2019!**
3. Inaktivera krav på tecken komposition och hindra användarna från att välja ofta angripna lösen ord, eftersom de gör det möjligt för användarna att välja förutsägbara tecken ersättningar i lösen ord.

Du kan använda [PowerShell för att förhindra att lösen ord upphör att gälla](../../active-directory/authentication/concept-sspr-policy.md) för användare om du skapar identiteter i Azure AD direkt. Hybrid organisationer bör implementera dessa principer med hjälp av [domän grup princip inställningar](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) eller [Windows PowerShell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Skydda mot läckta autentiseringsuppgifter och Lägg till återhämtning mot avbrott

Om din organisation använder en hybrid identitets lösning med direktautentisering eller Federation, bör du aktivera lösen ords-hash-synkronisering av följande två orsaker:

* Rapporten [användare med läckta autentiseringsuppgifter](../../active-directory/identity-protection/overview-identity-protection.md) i Azure AD-hanteringen varnar dig om användar namn och lösen ord, som har exponerats på den "mörka webben". En otrolig mängd lösen ord läcker genom nätfiske, skadlig kod och återkoppling av lösen ord på tredje parts webbplatser som senare strider mot varandra. Microsoft hittar många av dessa autentiseringsuppgifter och meddelar dig, i den här rapporten, om de matchar autentiseringsuppgifter i din organisation, men bara om du [aktiverar lösen ords-hash-synkronisering](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) eller bara har moln identiteter!
* I händelse av ett lokalt avbrott (till exempel i en utpressnings attack) kan du växla över till att använda [molnbaserad autentisering med hjälp av hash-synkronisering av lösen ord](../../active-directory/hybrid/choose-ad-authn.md). Med den här metoden för säkerhets kopiering kan du fortsätta att komma åt appar som kon figurer ATS för autentisering med Azure Active Directory, inklusive Microsoft 365. I det här fallet behöver IT-personalen inte använda personliga e-postkonton för att dela data förrän det lokala avbrottet har åtgärd ATS.

Lär dig mer om hur [synkronisering av lösen ord för hash](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) fungerar.

> [!NOTE]
> Om du aktiverar hash-synkronisering av lösen ord och använder Azure AD Domain Services, kommer Kerberos (AES 256) hash-värden och alternativt NTLM (RC4, ingen salt) hashs att krypteras och synkroniseras till Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementera Smart utelåsning för AD FS extra nät

Organisationer, som konfigurerar program att autentisera direkt till Azure AD-förmånen från [Azure AD Smart utelåsning](../../active-directory/authentication/concept-sspr-howitworks.md). Om du använder AD FS i Windows Server 2012R2 implementerar du AD FS [extra näts utelåsning-skydd](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Om du använder AD FS på Windows Server 2016 implementerar du [extra näts Smart utelåsning](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS Smart extra nät utelåsning skyddar mot brute force-attacker, vilket riktar AD FS samtidigt som användarna förhindras att låsas i Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Dra nytta av de säkraste och enklare att använda autentiseringsuppgifter

Med hjälp av [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)kan du ersätta lösen ord med stark tvåfaktorautentisering på datorer och mobila enheter. Den här autentiseringen består av en ny typ av användarautentiseringsuppgifter som är säkert bundna till en enhet och använder bio metrisk eller PIN-kod.

## <a name="step-2---reduce-your-attack-surface"></a>Steg 2 – minska din attack yta

Med tanke på den genomgripande risken för lösen ords angrepp är det viktigt att minimera angrepps ytan i din organisation. Att eliminera användningen av äldre, mindre säkra protokoll, begränsa åtkomst punkter och utnyttja mer betydande kontroll över administrativ åtkomst till resurser kan minska risken för attack ytan.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering

Appar som använder sina egna äldre metoder för att autentisera med Azure AD och få åtkomst till företags data, utgör en annan risk för organisationer. Exempel på appar som använder äldre autentisering är POP3-, IMAP4-eller SMTP-klienter. Äldre autentiserings-appar autentiseras för användarens räkning och förhindrar att Azure AD utför avancerade säkerhets utvärderingar. Den alternativa, modern autentiseringen, minskar säkerhets risken, eftersom den stöder Multi-Factor Authentication och villkorlig åtkomst. Vi rekommenderar följande tre åtgärder:

1. Blockera [äldre autentisering om du använder AD FS](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Konfigurera [SharePoint Online och Exchange Online för att använda modern autentisering](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Om du har Azure AD Premium använder du villkorliga åtkomst principer för att [blockera äldre autentisering](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md), annars använder du [standardinställningar för Azure AD-säkerhet](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blockera ogiltiga start punkter för autentisering

Genom att använda den ungefärliga kränkningen av intrång bör du minska effekten av komprometterade användarautentiseringsuppgifter när de inträffar. För varje app i din miljö kan du överväga giltiga användnings fall: vilka grupper, vilka nätverk, vilka enheter och andra element som har auktoriserats – och sedan blockera resten. Med [villkorlig åtkomst i Azure AD](../../active-directory/conditional-access/overview.md)kan du styra hur behöriga användare får åtkomst till sina appar och resurser baserat på särskilda villkor som du definierar.

### <a name="restrict-user-consent-operations"></a>Begränsa åtgärder för användar medgivande

Det är viktigt att förstå de olika [Azure AD-programmens medgivande upplevelser](../../active-directory/develop/application-consent-experience.md), vilka [typer av behörigheter och medgivande](../../active-directory/develop/v2-permissions-and-consent.md)och deras konsekvenser i din organisations säkerhets position. Som standard kan alla användare i Azure AD bevilja program som utnyttjar Microsofts identitets plattform för att komma åt din organisations data. Samtidigt som användare kan godkänna själva kan användarna enkelt förvärva användbara program som integrerar med Microsoft 365, Azure och andra tjänster, men det kan representera en risk om den inte används och övervakas noggrant.

Microsoft rekommenderar att du begränsar användar medgivande för att minska ditt arbets yta och minimera den här risken. Du kan också använda [principer för program medgivande (för hands version)](../../active-directory/manage-apps/configure-user-consent.md) för att begränsa Slutanvändarens medgivande till endast verifierade utgivare och endast för de behörigheter som du väljer. Om slut användar medgivande är begränsat kommer tidigare medgivande bidrag fortfarande att bevaras, men alla framtida medgivande åtgärder måste utföras av en administratör. För begränsade fall kan administratörs medgivande begäras av användare via ett integrerat [arbets flöde för administratörs medgivande](../../active-directory/manage-apps/configure-admin-consent-workflow.md) , eller genom egna support processer. Innan du begränsar Slutanvändarens medgivande bör du använda våra [rekommendationer](../../active-directory/manage-apps/manage-consent-requests.md) för att planera den här ändringen i din organisation. För program som du vill att alla användare ska ha åtkomst till, kan du överväga att [bevilja medgivande åt alla användare](../../active-directory/develop/v2-admin-consent.md), och se till att användare som ännu inte har samtyckt individuellt kommer att kunna komma åt appen. Om du inte vill att dessa program ska vara tillgängliga för alla användare i alla scenarier, använder du [program tilldelning](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) och villkorlig åtkomst för att begränsa användar åtkomsten till [specifika appar](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md).

Se till att användarna kan begära administratörs godkännande för nya program för att minska användar friktionen, minimera support volymen och hindra användare från att registrera sig för program som använder autentiseringsuppgifter som inte är Azure AD. När du reglerar dina medgivande åtgärder bör administratörerna granska appen och godkända behörigheter regelbundet.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementera Azure AD Privileged Identity Management

En annan effekt av "antagen intrång" är behovet av att minimera sannolikheten att ett komprometterat konto kan användas med en privilegie rad roll.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) hjälper dig att minimera konto privilegier genom att hjälpa dig att:

* Identifiera och hantera användare som tilldelats administrativa roller.
* Lär dig mer om roller som inte används eller för hög behörighet.
* Upprätta regler för att se till att privilegierade roller skyddas av Multi-Factor Authentication.
* Upprätta regler för att se till att privilegierade roller bara beviljas tillräckligt länge för att kunna utföra den privilegierade uppgiften.

Aktivera Azure AD PIM och Visa sedan de användare som har tilldelats administrativa roller och ta bort onödiga konton i dessa roller. För återstående privilegierade användare flyttar du dem från permanent till berättigade. Upprätta sedan lämpliga principer för att se till att när de behöver få åtkomst till dessa privilegierade roller, kan de göra detta på ett säkert sätt med nödvändig ändrings kontroll.

Som en del av processen för att distribuera ditt privilegierat konto bör du följa [bästa praxis för att skapa minst två nöd konton](../../active-directory/roles/security-planning.md) för att se till att du fortfarande har åtkomst till Azure AD om du låser dig.

## <a name="step-3---automate-threat-response"></a>Steg 3 – automatisera hot svar

Azure Active Directory har många funktioner som automatiskt fångar upp attacker, för att ta bort fördröjningen mellan identifiering och svar. Du kan minska kostnaderna och riskerna när du minskar tiden som cyberbrottslingarna används för att bädda in dem i din miljö. Här är de konkreta steg som du kan vidta.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementera säkerhets princip för användar risk med Azure AD Identity Protection

Användar risken indikerar sannolikheten för att en användares identitet har komprometterats och beräknas utifrån de [användar risk identifieringar](../../active-directory/identity-protection/overview-identity-protection.md) som är associerade med en användares identitet. En användar risk princip är en princip för villkorlig åtkomst som utvärderar risk nivån för en speciell användare eller grupp. Utifrån låg, medel, hög risk nivå kan en princip konfigureras för att blockera åtkomst eller kräva en säker ändring av lösen ord med hjälp av Multi-Factor Authentication. Microsofts rekommendation är att kräva en säker lösen ords ändring för användare med hög risk.

![Skärm bild som visar användare som har flaggats för risk, med en vald användare.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementera inloggnings risk princip med Azure AD Identity Protection

Inloggnings risk är sannolikheten någon annan än konto ägaren försöker logga in med identiteten. En [princip för inloggnings risker](../../active-directory/identity-protection/overview-identity-protection.md) är en princip för villkorlig åtkomst som utvärderar risk nivån för en speciell användare eller grupp. Utifrån risk nivån (hög/medel/låg) kan en princip konfigureras för att blockera åtkomst eller tvinga Multi-Factor Authentication. Se till att du tvingar Multi-Factor Authentication på medium eller över risk inloggningar.

![Logga in från anonyma IP-adresser](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Steg 4 – använda Cloud Intelligence

Granskning och loggning av säkerhetsrelaterade händelser och relaterade aviseringar är viktiga komponenter i en effektiv skydds strategi. Säkerhets loggar och-rapporter ger dig en elektronisk post med misstänkta aktiviteter och hjälper dig att identifiera mönster som kan indikera försök till eller lyckad extern inträngande av nätverket och interna attacker. Du kan använda granskning för att övervaka användar aktivitet, dokumentera regelefterlevnad, utföra kriminal tekniska analys med mera. Aviseringar ger meddelanden om säkerhets händelser.

### <a name="monitor-azure-ad"></a>Övervaka Azure AD

Microsoft Azure tjänster och funktioner ger dig möjlighet att konfigurera säkerhets gransknings-och loggnings alternativ som hjälper dig att identifiera luckor i dina säkerhets principer och mekanismer och åtgärda dessa luckor för att förhindra överträdelser. Du kan använda [Azure-loggning och granskning](log-audit.md) och använda [gransknings aktivitets rapporter i Azure Active Directory-portalen](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Övervaka Azure AD Connect Health i hybrid miljöer

[Genom att övervaka AD FS med Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) får du bättre inblick i potentiella problem och synligheten för angrepp i din AD FS-infrastruktur. Azure AD Connect Health skickar aviseringar med information, lösnings steg och länkar till relaterad dokumentation; användnings analys för flera mått som rör autentiseringstrafik. prestanda övervakning och rapporter.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Övervaka Azure AD Identity Protection händelser

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) är ett meddelande, övervaknings-och rapporterings verktyg som du kan använda för att identifiera potentiella sårbarheter som påverkar organisationens identiteter. Den identifierar risk identifieringar, t. ex. läckta autentiseringsuppgifter, omöjliga resor och inloggningar från angripna enheter, anonyma IP-adresser, IP-adresser som är kopplade till den misstänkta aktiviteten och okända platser. Aktivera aviseringar om du vill få e-postaviseringar för användare med risk och/eller ett vecko sammandrag e-postmeddelande.

Azure AD Identity Protection ger två viktiga rapporter som du bör övervaka dagligen:
1. Riskfyllda inloggnings rapporter kan användas för användarnas inloggnings aktiviteter som du bör undersöka, den legitima ägaren kanske inte har utfört inloggningen.
2. Riskfyllda användar rapporter kommer att påverka användar konton som kan ha komprometterats, till exempel läckta autentiseringsuppgifter som identifierats eller användaren som är inloggad från olika platser och som orsakar en omöjlig resa-händelse.

![Skärm bild som visar fönstret Azure ett D Identity Protection med användare och deras risk nivåer.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Granska appar och samskickade behörigheter

Användare kan luras att navigera till en komprometterad webbplats eller appar som får åtkomst till profil information och användar data, t. ex. e-post. En skadlig aktör kan använda de behörigheter som den tagit emot för att kryptera sitt post låda innehåll och begära en utpressnings tro för att återställa data från din post låda. [Administratörer bör granska och granska](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) de behörigheter som anges av användarna eller inaktivera möjligheten för användare att ge sitt medgivande som standard.

Förutom att granska de behörigheter som användarna har givit kan du [hitta riskfyllda eller oönskade OAuth-program](/cloud-app-security/investigate-risky-oauth) i Premium miljöer.

## <a name="step-5---enable-end-user-self-service"></a>Steg 5 – aktivera självbetjäning för slutanvändare

Så mycket som möjligt bör du utjämna säkerheten med produktiviteten. På samma sätt som du närmar dig resan med tänkesätt som du ställer in en grund för säkerhet i den långa körningen kan du ta bort friktion från din organisation genom att ge användarna möjlighet att göra återstående vigilant.

### <a name="implement-self-service-password-reset"></a>Implementera återställning av lösen ord för självbetjäning

Azure ADs självbetjäning för [återställning av lösen ord (SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) erbjuder ett enkelt sätt för IT-administratörer att tillåta användare att återställa eller låsa upp sina lösen ord eller konton utan supportavdelningen eller administratörs åtgärder. Systemet innehåller detaljerad rapportering som spårar när användare har återställt sina lösen ord, tillsammans med aviseringar för att varna dig om missbruk eller missbruk.

### <a name="implement-self-service-group-and-application-access"></a>Implementera självbetjänings grupp-och program åtkomst

Azure AD ger möjlighet till icke-administratörer att hantera åtkomst till resurser, använda säkerhets grupper, Microsoft 365 grupper, program roller och åtkomst till paket kataloger.  [Grupp hantering via självbetjäning](../../active-directory/enterprise-users/groups-self-service-management.md) gör det möjligt för grupp ägare att hantera sina egna grupper, utan att behöva tilldelas en administrativ roll. Användare kan också skapa och hantera Microsoft 365 grupper utan att behöva förlita sig på administratörer för att hantera sina förfrågningar, och oanvända grupper upphör automatiskt att gälla.  [Hantering av Azure AD](../../active-directory/governance/entitlement-management-overview.md) -behörighet ger ytterligare delegering och synlighet, med omfattande åtkomst begär ande arbets flöden och automatiskt upphör Ande.  Du kan delegera till icke-administratörer möjligheten att konfigurera sina egna åtkomst paket för grupper, grupper, program och SharePoint Online-webbplatser som de äger, med anpassade principer för vem som krävs för att godkänna åtkomst, inklusive konfiguration av medarbetares chefer och affärs partner sponsorer som god kännare.

### <a name="implement-azure-ad-access-reviews"></a>Implementera åtkomst granskningar för Azure AD

Med [åtkomst granskningar i Azure AD](../../active-directory/governance/access-reviews-overview.md)kan du hantera åtkomst paket och grupp medlemskap, åtkomst till företags program och privilegierade roll tilldelningar för att se till att du upprätthåller en säkerhets standard.  Regelbundna tillsyn av själva användarna, resurs ägare och andra granskare ser till att användarna inte behåller åtkomsten under längre tid när de inte längre behöver det.

## <a name="summary"></a>Sammanfattning

Det finns många aspekter av en säker identitets infrastruktur, men den här check listan i fem steg hjälper dig att snabbt uppnå en säkrare och säker identitets infrastruktur:

* Förstärk dina autentiseringsuppgifter.
* Minska attack ytan.
* Automatisera hot svar.
* Använd Cloud Intelligence.
* Aktivera mer förutsägbar och fullständig slut användar säkerhet med hjälp av självhjälp.

Vi uppskattar hur allvarligt du tar identitets säkerheten och hoppas att det här dokumentet är en bra översikt över en säkrare position för din organisation.

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med att planera och distribuera rekommendationerna läser du [distributions planerna för Azure AD-projekt](../../active-directory/fundamentals/active-directory-deployment-plans.md) för att få hjälp.

Om du är säker på att alla dessa steg är fullständiga, använder du Microsofts [identitet för säker](../../active-directory/fundamentals/identity-secure-score.md)användning, som håller dig uppdaterad med de [senaste bästa metoderna](identity-management-best-practices.md) och säkerhetshot.
