---
title: Över gång till styrt samarbete med Azure Active Directory B2B-samarbete
description: Gå vidare till styrt samarbete med Azure AD B2B-samarbete.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4206ba7617032e34310682d1468e6b1b661b8c8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648595"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Över gång till styrt samarbete med Azure Active Directory B2B-samarbete 

Att få ditt samarbete under kontrollen är nyckel för att skydda extern åtkomst till dina resurser. Innan du går vidare med den här artikeln måste du vara säker på att du har:

* [Fastställde din säkerhets position](1-secure-access-posture.md)

* [Identifierat ditt nuvarande tillstånd](2-secure-access-current-state.md)

* [En säkerhets plan har skapats](3-secure-access-plan.md)

* [Förstå hur grupper och säkerhet fungerar tillsammans](4-secure-access-groups.md)

När du har gjort dessa saker är du redo att gå vidare till det kontrollerade samarbetet. Den här artikeln hjälper dig att flytta allt ditt externa samarbete till [Azure Active Directory B2B-samarbete](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure AD B2B är en funktion i [externa Azure AD-identiteter](../external-identities/compare-with-b2c.md).

## <a name="control-who-your-organization-collaborates-with"></a>Kontrol lera vem din organisation samarbetar med

Du måste bestämma om du vill begränsa vilka organisationer som dina användare kan samar beta med och vilka inom din organisation kan initiera samarbetet. De flesta organisationer tar med sig metoden att tillåta affär senheter att bestämma vem de samarbetar med, samt att delegera godkännande och insyn vid behov. Vissa myndigheter, utbildningar och finansiella tjänster organisationer tillåter till exempel inte öppna samarbete. Du kanske vill använda Azure AD-funktionerna för att begränsa samarbetet, enligt beskrivningen i resten av det här avsnittet.

### <a name="determine-collaboration-partners"></a>Bestäm samarbets partners

Se först till att du har dokumenterat de organisationer som du för närvarande samarbetar med, och domänerna för dessa organisationers användare. En samarbets partner kan ha flera domäner. En partner kan till exempel ha flera affär senheter med separata domäner.

Sedan bestämmer du om du vill aktivera framtida samarbete med 

* valfri domän (mest inkluderad)

* alla domäner utom de som uttryckligen nekas 

* endast vissa domäner (mest restriktiva)

> [!NOTE]
> De mer restriktiva samarbets inställningarna, desto troligare är att användarna hamnar utanför ditt godkända samarbets ramverk. Vi rekommenderar att du aktiverar det bredaste samarbetet som dina säkerhets behov kommer att tillåta, och att se till att det samarbetar i stället för att begränsas. 

Observera också att begränsa till en enda domän kan oavsiktligt förhindra auktoriserat samarbete med organisationer, som har andra orelaterade domäner för sina användare. Om du till exempel gör affärer med en organisation contoso, kan den första kontakt punkten med contoso vara en av sina USA-baserade anställda som har ett e-postmeddelande med en ". com"-domän. Men om du bara tillåter domänen ". com" kan du oavsiktligt utelämna de kanadensiska anställda som har ". ca"-domän. 

Det finns omständigheter där du bara vill tillåta vissa samarbets partner. Till exempel kanske ett universitet system bara vill tillåta sin egen fakultets åtkomst till en resurs innehavare. Eller ett konglomerat kanske bara vill tillåta vissa dotter bolag att samar beta med varandra för att uppnå efterlevnad av ett nödvändigt ramverk.

#### <a name="using-allow-and-deny-lists"></a>Använda listorna Tillåt och neka

Du kan använda en lista över tillåtna eller neka om du vill [begränsa inbjudningar till B2B-användare](../external-identities/allow-deny-list.md) från vissa organisationer. Du kan bara använda en lista över tillåtna eller nekade.

* En [lista över tillåtna](../external-identities/allow-deny-list.md) begränsningar begränsar samarbetet till de domäner som anges. alla andra domäner är effektiva i listan över nekade.

* Med en [neka-lista](../external-identities/allow-deny-list.md) kan du samar beta med alla domäner som inte finns på listan över nekade.

> [!IMPORTANT]
> Listorna gäller inte för användare som redan finns i katalogen. De gäller inte heller för OneDrive för företag och SharePoint Tillåt nekade listor som är separata.

Vissa organisationer använder en lista över kända "dåliga skådespelare"-domäner som tillhandahålls av deras hanterade säkerhetsprovider för sin neka-lista. Om organisationen t. ex. gör affärer med Contoso och använder en. com-domän, kan det finnas en icke-relaterad organisation som har använt contoso. org-domänen och försöker ett phishing-angrepp för att personifiera contoso-anställda. 

## <a name="control-how-external-users-gain-access"></a>Styra hur externa användare får åtkomst

Det finns många sätt att samar beta med externa partners med hjälp av Azure AD B2B. För att börja samar beta kan du bjuda in eller på annat sätt ge din partner åtkomst till dina resurser. Användare kan få åtkomst genom att svara på:

* Att lösa [en inbjudan som skickats via ett e-postmeddelande](../external-identities/redemption-experience.md)eller [en direkt länk till delning av](../external-identities/redemption-experience.md) en resurs.

* Begära åtkomst [via ett program](../external-identities/self-service-sign-up-overview.md) som du skapar

* Begär åtkomst via portalen för [åtkomst](../governance/entitlement-management-request-access.md)

När du aktiverar Azure AD B2B kan du ge gäst användare möjlighet att bjuda in gäst användare via direkt länkar och e-postinbjudningar som standard. Inbjudningar via e-posteng ång slö sen ord och en självbetjänings Portal finns för närvarande i för hands version och måste aktive ras inom externa identiteter | Inställningar för externt samarbete i Azure AD-portalen.

### <a name="control-who-can-invite-guest-users"></a>Kontrol lera vem som kan bjuda in gäst användare

Ta reda på vem som kan bjuda in gäst användare att komma åt resurser.

* Den mest restriktiva inställningen är att bara tillåta administratörer och de användare som har tilldelats [rollen som gäst inbjudare](../external-identities/delegate-invitations.md) att bjuda in gäster.

* Om dina säkerhets krav tillåter det rekommenderar vi att alla användare med en userType medlem kan bjuda in gäster.

* Avgör om du vill att användare med en userType gäst, vilket är standard konto typen för Azure AD B2B-användare, för att kunna bjuda in andra gäster. 

![Skärm bild av inställningar för gäst inbjudning.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Samla in ytterligare information om externa användare

Om du använder hantering av Azure AD-rättigheter kan du konfigurera frågor för externa användare att besvara. Frågorna visas sedan för god kännare för att hjälpa dem att fatta ett beslut. Du kan konfigurera olika uppsättningar av frågor för varje [åtkomst paket princip](../governance/entitlement-management-access-package-approval-policy.md) så att god kännare kan ha relevant information för den åtkomst som de godkänner. Om till exempel ett åtkomst paket är avsett för åtkomst till leverantören, kan begär Anden efter frågas om leverantörs kontrakts nummer. Ett annat åtkomst paket som är avsett för leverantörer, kan begära sitt ursprungsland.

Om du använder en självbetjänings Portal kan du använda [API-kopplingar](../external-identities/api-connectors-overview.md) för att samla in ytterligare attribut om användarna när de registrerar sig. Du kan sedan använda dessa attribut för att tilldela åtkomst. Om du till exempel registrerar dig för att registrera leverantörs-ID kan du använda attributet för att dynamiskt tilldela dem till en grupp eller ett åtkomst paket för den leverantören. Du kan skapa anpassade attribut i Azure Portal och använda dem i dina användar flöden för självbetjänings registrering. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](../../active-directory-b2c/microsoft-graph-operations.md). 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Felsöka inbjudan till Azure AD-användare

Det finns tre instanser när inbjudna gäst användare från en samarbets partner som använder Azure AD har problem med att lösa in en inbjudan.

* Om du använder en lista över tillåtna och användarens domän inte finns med i listan över tillåtna.

* Om samarbets partnerns hem klient har klient begränsningar som förhindrar samarbete med externa användare..

* Om användaren inte är en del av partnerns Azure AD-klient. Det finns till exempel användare på contoso.com som bara finns i Active Directory (eller någon annan lokal IdP). de kommer bara att kunna lösa in inbjudningar via e-postprocessen för eng ång slö sen ord. Mer information finns i inbjudan till [inlösnings flödet](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Styr vad externa användare kan komma åt

De flesta organisationer är inte monolitisk. Det innebär att det finns vissa resurser som är bra att dela med externa användare, och att du inte vill att externa användare ska ha åtkomst. Därför måste du kontrol lera vilka externa användare som har åtkomst. Överväg att använda [rättighets hantering och åtkomst paket för att kontrol lera åtkomsten](6-secure-access-entitlement-managment.md) till vissa resurser.

Som standard kan gäst användare se information och attribut om klient medlemmar och andra partner, inklusive grupp medlemskap. Överväg om dina säkerhets krav kräver begränsad åtkomst till den här informationen i externa användare.

![Skärm bild av konfiguration av inställningar för externt samarbete.](media/secure-external-access/5-external-collaboration-settings.png)

Vi rekommenderar följande begränsningar för gäst användare.

* **Begränsa gäst åtkomsten till bläddring i grupper och andra egenskaper i katalogen**

   * Använd de externa samarbets inställningarna för att begränsa gäst möjligheten att läsa grupper som de inte är medlemmar i.

* **Blockera åtkomst till appar som endast är anställda**.

   * Skapa en princip för villkorlig åtkomst för att blockera åtkomst till Azure AD-integrerade program som endast lämpar sig för användare som inte är gäst användare. 

* **Blockera åtkomst till Azure Portal. Du kan göra sällsynta nödvändiga undantag**. 

   * Skapa en princip för villkorlig åtkomst som inkluderar antingen alla gäst-och externa användare och [implementera sedan en princip för att blockera åtkomst](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Ta bort användare som inte längre behöver åtkomst

Utvärdera aktuell åtkomst så att du kan [Granska och ta bort användare som inte längre behöver åtkomst](../governance/access-reviews-external-users.md). Ta med externa användare i din klient organisation som gäster och med medlems konton. 

Vissa organisationer har lagt till externa användare, till exempel leverantörer, partners och leverantörer som medlemmar. Dessa medlemmar kan ha ett speciellt attribut eller användar namn som börjar med, till exempel

* v – för leverantörer

* p – för partner

* c – för leverantörer

Utvärdera externa användare med medlems konton för att avgöra om de fortfarande behöver åtkomst. I så fall, kan du överföra dessa användare till Azure AD B2B enligt beskrivningen i nästa avsnitt.

Du kan också ha gäst användare som inte bjudits in via hantering av rättigheter eller Azure AD B2B

För att hitta dessa användare kan du:

* [Hitta gäst användare som inte bjudits in via rättighets hantering](../governance/access-reviews-external-users.md).

   * Vi tillhandahåller ett [exempel på PowerShell-skript.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Gå över dessa användare till Azure AD B2B-användare enligt beskrivningen i följande avsnitt.

## <a name="transition-your-current-external-users-to-b2b"></a>Gå över till dina aktuella externa användare till B2B

Om du inte har använt Azure AD B2B har du troligen inte anställda som användare i din klient organisation. Vi rekommenderar att du överför dessa konton till Azure AD B2B external User-konton och sedan ändrar deras UserType till gäst. På så sätt kan du dra nytta av de många sätt som Azure AD och Microsoft 365 ger dig möjlighet att behandla externa användare på olika sätt. Några av dessa sätt är:

* Enkelt inklusive eller exklusive gäst användare i principer för villkorlig åtkomst

* Enkelt inklusive eller exklusive gäst användare i åtkomst paket och åtkomst granskningar

* Du kan enkelt ta med eller utesluta extern åtkomst till team, SharePoint och andra resurser.

Om du vill överföra dessa interna användare och behålla deras aktuella åtkomst, UPN och grupp medlemskap, se [Bjud in externa användare till B2B-samarbete](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Inaktivera oönskade samarbets metoder

Om du vill slutföra över gången till styrt samarbete bör du inaktivera oönskade samarbets metoder. Vilken du inaktiverar är baserad på den kontroll som du vill att den ska utöva över samarbetet och din säkerhets position. Information om IT-och slutanvändarens kontroll finns i [avgöra säkerhets position för extern åtkomst](1-secure-access-posture.md).

Följande är samarbets bilar som du kanske vill utvärdera.

### <a name="direct-invitation-through-microsoft-teams"></a>Dirigera inbjudan genom Microsoft Teams

Som standard tillåter extern åtkomst, vilket innebär att organisationen kan kommunicera med alla externa domäner. Om du vill begränsa eller tillåta vissa domäner bara för team kan du göra det i [team administrations portalen](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Direkt delning via SharePoint och OneDrive

Direkt delning via SharePoint och OneDrive kan lägga till användare utanför rättighets hanterings processen. En djupgående titt på dessa konfigurationer finns i [Hantera åtkomst med Microsoft Teams, SharePoint och OneDrive för företag](9-secure-access-teams-sharepoint.md) . du kan också [blockera användningen av användarens personliga OneDrive](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) om du vill.

### <a name="sending-documents-through-email"></a>Skicka dokument via e-post

Användarna kommer att skicka dokument via e-post till externa användare. Överväg hur du vill begränsa och kryptera åtkomsten till dessa dokument med hjälp av känslighets etiketter. Mer information finns i Hantera åtkomst med känslighets etiketter.

### <a name="unsanctioned-collaboration-tools"></a>Ej sanktionerade samarbets verktyg

Samarbetet med samarbets verktyg är stort. Användarna använder ofta många utanför sina officiella uppgifter, inklusive plattformar som Google-dokument, DropBox, slack eller zoom. Det är möjligt att blockera användningen av sådana verktyg från ett företags nätverk på brand Väggs nivå och med hantering av mobil program för organisations hanterade enheter. Detta kommer dock även att blockera alla sanktionerade instanser av dessa plattformar och inte blockera åtkomst från ohanterade enheter. Blockera plattformar som du inte vill använda om det behövs, och skapa affärs principer för att ingen annan användning av sanktioner ska användas för de plattformar du behöver använda. 

Mer information om hur du hanterar program som inte är sanktionerade finns i:

* [Styra anslutna appar](/cloud-app-security/governance-actions)

* [Sanktionering och avsanktionerning av ett program.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Nästa steg

Se följande artiklar om hur du skyddar extern åtkomst till resurser. Vi rekommenderar att du vidtar åtgärder i den angivna ordningen.

1. [Bestäm din säkerhets position för extern åtkomst](1-secure-access-posture.md)

2. [Identifiera ditt nuvarande tillstånd](2-secure-access-current-state.md)

3. [Skapa en styrnings plan](3-secure-access-plan.md)

4. [Använda grupper för säkerhet](4-secure-access-groups.md)

5. [Över gång till Azure AD B2B](5-secure-access-b2b.md) (du är här.)

6. [Säker åtkomst med hantering av rättigheter](6-secure-access-entitlement-managment.md)

7. [Säker åtkomst med principer för villkorlig åtkomst](7-secure-access-conditional-access.md)

8. [Säker åtkomst med känslighets etiketter](8-secure-access-sensitivity-labels.md)

9. [Säker åtkomst till Microsoft Teams, OneDrive och SharePoint](9-secure-access-teams-sharepoint.md)