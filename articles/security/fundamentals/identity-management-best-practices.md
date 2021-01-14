---
title: Rekommenderade säkerhets metoder för Azure Identity & Access | Microsoft Docs
description: Den här artikeln innehåller en uppsättning metod tips för identitets hantering och åtkomst kontroll med hjälp av inbyggda Azure-funktioner.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: d2abc357a5a636aa15909a3645e284c978fb903f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197599"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Rekommenderade säkerhets metoder för Azure Identity Management och åtkomst kontroll

I den här artikeln diskuterar vi en samling med metod tips för Azure Identity Management och åtkomst kontroll. De här bästa metoderna är härledda från vår erfarenhet av [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) och kundernas upplevelser som du själv.

För varje bästa praxis förklarar vi:

* Det bästa tillvägagångs sättet är
* Varför du vill aktivera den bästa metoden
* Detta kan bero på att du inte kan aktivera bästa praxis
* Möjliga alternativ till bästa praxis
* Hur du kan lära dig att aktivera bästa praxis

Den här artikeln om metod tips för Azure Identity Management och Access Control är baserad på ett samförstånds besked och Azure-plattformens funktioner och funktions uppsättningar, eftersom de finns när den här artikeln skrevs.

Avsikten med att skriva den här artikeln är att tillhandahålla en allmän översikt över en stabilare säkerhets position efter distributionen genom våra "[5 steg för att skydda din identitets infrastruktur](steps-secure-identity.md)", som vägleder dig genom några av våra kärn funktioner och tjänster.

Åsikter och tekniker förändras med tiden och den här artikeln uppdateras regelbundet för att avspegla ändringarna.

Metod tips för Azure Identity Management och åtkomst kontroll som beskrivs i den här artikeln är:

* Behandla identitet som primär säkerhets perimeter
* Centralisera identitets hantering
* Hantera anslutna klienter
* Aktivera enkel inloggning
* Aktivera villkorlig åtkomst
* Planera för förbättringar av rutin säkerhet
* Aktivera lösenordshantering
* Framtvinga Multi-Factor Verification för användare
* Använd rollbaserad åtkomstkontroll
* Lägre exponering av privilegierade konton
* Kontrol lera platser där resurser finns
* Använda Azure AD för lagrings autentisering

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Behandla identitet som primär säkerhets perimeter

Många anser att identiteten är den primära omkretsen för säkerhet. Det här är en Skift från det traditionella fokus på nätverks säkerhet. Nätverks omkretserna kommer att få mer porösa och detta kan inte vara så effektivt som det var innan nedbrytningen av [BYOD](/mem/intune/fundamentals/byod-technology-decisions) -enheter och moln program.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) är Azure-lösningen för identitets-och åtkomst hantering. Azure AD är en molnbaserad katalog-och identitets hanterings tjänst för flera innehavare från Microsoft. Den kombinerar viktiga katalogtjänster, åtkomsthantering för program och identitetsskydd i en och samma lösning.

I följande avsnitt listas metod tips för identitets-och åtkomst säkerhet med hjälp av Azure AD.

**Bästa praxis**: Center säkerhets kontroller och identifieringar kring användar-och tjänst identiteter.
**Information**: Använd Azure AD för att samordna kontroller och identiteter.

## <a name="centralize-identity-management"></a>Centralisera identitets hantering

I ett [hybrid identitets](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) scenario rekommenderar vi att du integrerar dina lokala och molnbaserade kataloger. Integrationen gör det möjligt för IT-teamet att hantera konton från en plats, oavsett var ett konto skapas. Integreringen hjälper även dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst till både moln resurser och lokala resurser.

**Bästa praxis**: upprätta en enda Azure AD-instans. Konsekvens och en enda auktoritativa källor ökar skärpan och minskar säkerhets riskerna med mänsklig fel och konfigurations komplexitet.
**Information**: Ange en enda Azure AD-katalog som auktoritativ källa för företags-och organisations konton.

**Bästa praxis**: integrera dina lokala kataloger med Azure AD.  
**Information**: Använd [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) för att synkronisera din lokala katalog med din moln katalog.

> [!Note]
> Det finns [faktorer som påverkar prestandan för Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Se till att Azure AD Connect har tillräckligt med kapacitet för att upprätthålla system från att hindra säkerhet och produktivitet. Stora eller komplexa organisationer (organisationer som håller på att tillhandahålla fler än 100 000 objekt) bör följa [rekommendationerna](../../active-directory/hybrid/whatis-hybrid-identity.md) för att optimera sina Azure AD Connect-implementeringar.

**Bästa praxis**: Synkronisera inte konton till Azure AD som har hög behörighet i den befintliga Active Directory-instansen.
**Information**: Ändra inte standard [Azure AD Connects konfigurationen](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) som filtrerar bort dessa konton. Den här konfigurationen minskar risken för angripare pivotering från molnet till lokala till gångar (som kan skapa en större incident).

**Bästa praxis**: Aktivera synkronisering av lösen ords-hash.  
**Information**: hash-synkronisering av lösen ord är en funktion som används för att synkronisera hashar av användarens lösen ord från en lokal Active Directory-instans till en MOLNBASERAD Azure AD-instans. Den här synkroniseringen hjälper till att skydda mot läckta autentiseringsuppgifter som spelas upp från tidigare attacker.

Även om du bestämmer dig för att använda Federation med Active Directory Federation Services (AD FS) (AD FS) eller andra identitets leverantörer, kan du också ställa in lösen ords-hash-synkronisering som en säkerhets kopia om dina lokala servrar inte fungerar eller tillfälligt blir otillgängliga. Den här synkroniseringen gör det möjligt för användarna att logga in på tjänsten med samma lösen ord som de använder för att logga in på den lokala Active Directory-instansen. Det tillåter också identitets skydd att identifiera komprometterade autentiseringsuppgifter genom att jämföra synkroniserade lösen ord med lösen ord som är kända för att bli komprometterade, om en användare har använt samma e-postadress och lösen ord på andra tjänster som inte är anslutna till Azure AD.

Mer information finns i [implementera hash-synkronisering av lösen ord med Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Bästa praxis**: Använd Azure AD för att utveckla nya program.
**Information**: Använd rätt funktioner för att stödja autentisering:

  - Azure AD för anställda
  - [Azure AD B2B](../../active-directory/external-identities/index.yml) för gäst användare och externa partners
  - [Azure AD B2C](../../active-directory-b2c/index.yml) för att kontrol lera hur kunder registrerar sig, loggar in och hanterar sina profiler när de använder dina program

Organisationer som inte integrerar sin lokala identitet med sin moln identitet kan ha mer resurser i hanteringen av konton. Den här kostnaden ökar risken för misstag och säkerhets överträdelser.

> [!Note]
> Du måste välja vilka kataloger kritiska konton ska finnas i och om den använda admin-arbetsstationen hanteras av nya moln tjänster eller befintliga processer. Att använda befintliga hanterings-och identitets etablerings processer kan minska riskerna, men kan också skapa risken för att en angripare kan skada ett lokalt konto och pivotera till molnet. Du kanske vill använda en annan strategi för olika roller (till exempel IT-administratörer eller affär senhets administratörer). Du har två alternativ. Det första alternativet är att skapa Azure AD-konton som inte är synkroniserade med din lokala Active Directory-instans. Anslut din administratörs arbets station till Azure AD, som du kan hantera och korrigera med hjälp av Microsoft Intune. Det andra alternativet är att använda befintliga administratörs konton genom att synkronisera till den lokala Active Directory-instansen. Använd befintliga arbets stationer i din Active Directory-domän för hantering och säkerhet.

## <a name="manage-connected-tenants"></a>Hantera anslutna klienter
Din säkerhets organisation behöver synlig insyn för att bedöma risken och för att avgöra om principerna för din organisation och alla regler som krävs, följs. Du bör se till att din säkerhets organisation har insyn i alla prenumerationer som är anslutna till din produktions miljö och nätverket (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) eller [plats-till-plats-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). En [Global administratör/företags administratör](../../active-directory/roles/permissions-reference.md#company-administrator-permissions) i Azure AD kan öka sin åtkomst till [Administratörs rollen för användar åtkomst](../../role-based-access-control/built-in-roles.md#user-access-administrator) och se alla prenumerationer och hanterade grupper som är anslutna till din miljö.

Se [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md) för att se till att du och din säkerhets grupp kan visa alla prenumerationer eller hanterings grupper som är anslutna till din miljö. Du bör ta bort den här utökade åtkomsten när du har bedömt riskerna.

## <a name="enable-single-sign-on"></a>Aktivera enkel inloggning

I en mobil-och mellanliggande värld, vill du aktivera enkel inloggning (SSO) till enheter, appar och tjänster var som helst, så att användarna kan vara produktiva var som helst och när. När du har flera identitets lösningar för hantering, blir detta ett administrativt problem som inte bara gäller för den utan även för användare som behöver komma ihåg flera lösen ord.

Genom att använda samma identitets lösning för alla dina appar och resurser kan du få SSO. Och användarna kan använda samma uppsättning autentiseringsuppgifter för att logga in och få åtkomst till de resurser som de behöver, oavsett om resurserna finns lokalt eller i molnet.

**Bästa praxis**: Aktivera SSO.  
**Information**: Azure AD [utökar lokalt Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) till molnet. Användare kan använda sitt primära arbets-eller skol konto för sina domänanslutna enheter, företags resurser och alla webb-och SaaS-program som de behöver för att få jobbet gjort. Användarna behöver inte komma ihåg flera uppsättningar användar namn och lösen ord, och deras program åtkomst kan etableras automatiskt (eller avetableras) baserat på deras grupp medlemskap i organisationen och deras status som anställd. Och du kan kontrollera den åtkomsten för galleriappar eller för dina egna lokala appar som du har utvecklat och publicerat genom [Azure AD-programproxy](../../active-directory/manage-apps/application-proxy.md).

Använd SSO för att ge användarna åtkomst till sina [SaaS-program](../../active-directory/manage-apps/what-is-single-sign-on.md) baserat på deras arbets-eller skol konto i Azure AD. Detta gäller inte bara för Microsoft SaaS-appar, utan även andra appar, till exempel [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) och [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md). Du kan konfigurera ditt program så att det använder Azure AD som en [SAML-baserad identitets](../../active-directory/fundamentals/active-directory-whatis.md) leverantör. Som säkerhets kontroll utfärdar inte Azure AD en token som gör det möjligt för användarna att logga in i programmet, såvida de inte har beviljats åtkomst via Azure AD. Du kan bevilja åtkomst direkt eller via en grupp som användare är medlem i.

Organisationer som inte skapar en gemensam identitet för att upprätta SSO för sina användare och program är mer exponerade för scenarier där användare har flera lösen ord. De här scenarierna ökar sannolikheten för att användare återanvänder lösen ord eller använder svaga lösen ord.

## <a name="turn-on-conditional-access"></a>Aktivera villkorlig åtkomst

Användare kan komma åt din organisations resurser genom att använda en mängd olika enheter och appar var som helst. Som IT-administratör vill du se till att dessa enheter uppfyller dina standarder för säkerhet och efterlevnad. Det räcker bara att fokusera på vem som har åtkomst till en resurs.

För att balansera säkerhet och produktivitet måste du tänka på hur en resurs används innan du kan fatta ett beslut om åtkomst kontroll. Med villkorlig åtkomst för Azure AD kan du åtgärda detta krav. Med villkorlig åtkomst kan du fatta beslut om automatiserad åtkomst kontroll baserat på villkor för att komma åt dina molnappar.

**Bästa praxis**: hantera och kontrol lera åtkomsten till företags resurser.  
**Information**: Konfigurera vanliga Azure AD- [principer för villkorlig åtkomst](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) baserat på en grupp, plats och program känslighet för SaaS-appar och Azure AD – anslutna appar.

**Bästa praxis**: blockera bakåtkompatibla autentiseringsprotokoll.
**Information**: angripare utnyttjar svagheter i äldre protokoll varje dag, särskilt för attacker med lösen ords spridning. Konfigurera villkorlig åtkomst för att [blockera äldre protokoll](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Planera för förbättringar av rutin säkerhet

Säkerheten utvecklas alltid och det är viktigt att bygga in i moln-och identitets hanterings ramverket, ett sätt att regelbundet Visa tillväxten och identifiera nya sätt att skydda din miljö.

Identitets säkra poäng är en uppsättning rekommenderade säkerhets kontroller som Microsoft publicerar för att ge dig ett numeriskt resultat för att objektivt mäta din säkerhets position och hjälpa dig att planera framtida säkerhets förbättringar. Du kan också Visa poängen i jämförelse med dem i andra branscher samt dina egna trender över tid.

**Bästa praxis**: planera rutin säkerhets granskningar och förbättringar baserat på bästa praxis i din bransch.
**Information**: Använd funktionen identitet säkra Poäng för att rangordna dina förbättringar över tid.

## <a name="enable-password-management"></a>Aktivera lösenordshantering

Om du har flera klienter eller om du vill att användarna ska kunna [återställa sina egna lösen ord](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)är det viktigt att du använder lämpliga säkerhets principer för att förhindra missbruk.

**Bästa praxis**: Konfigurera lösen ords återställning via självbetjäning (SSPR) för dina användare.  
**Information**: Använd funktionen för [återställning av lösen ord](../../active-directory-b2c/user-flow-self-service-password-reset.md) i Azure AD.

**Bästa praxis**: övervaka hur eller om SSPR faktiskt används.  
**Information**: övervaka de användare som registrerar med hjälp av [registrerings aktivitets rapporten](../../active-directory/authentication/howto-sspr-reporting.md)för Azure AD-lösenord. Rapporterings funktionen som Azure AD tillhandahåller hjälper dig att besvara frågor genom att använda färdiga rapporter. Om du är korrekt licensierad kan du också skapa anpassade frågor.

**Bästa praxis**: utöka molnbaserade lösen ords principer till din lokala infrastruktur.
**Information**: förbättra lösen ords principerna i din organisation genom att utföra samma kontroller för lokala lösen ords ändringar som du gör för molnbaserade lösen ords ändringar. Installera [Azure AD Password Protection](../../active-directory/authentication/concept-password-ban-bad.md) för Windows Server Active Directory agents lokalt för att utöka förbjudna lösen ords listor till din befintliga infrastruktur. Användare och administratörer som ändrar, anger eller återställer lösen ord lokalt måste följa samma lösen ords princip som enbart moln användare.

## <a name="enforce-multi-factor-verification-for-users"></a>Framtvinga Multi-Factor Verification för användare

Vi rekommenderar att du kräver tvåstegsverifiering för alla dina användare. Detta inkluderar administratörer och andra i din organisation som kan ha en betydande inverkan om deras konto komprometteras (t. ex. ekonomi personal).

Det finns flera alternativ för att kräva tvåstegsverifiering. Det bästa alternativet för dig beror på dina mål, den Azure AD-version som du kör och ditt licensierings program. Se [hur du kräver tvåstegsverifiering för en användare](../../active-directory/authentication/howto-mfa-userstates.md) för att fastställa det bästa alternativet för dig. Mer information om licenser och priser finns på sidan med priser för [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

Följande är alternativ och fördelar med att aktivera tvåstegsverifiering:

**Alternativ 1**: Aktivera MFA för alla användare och inloggnings metoder med Azure AD Security defaults- **förmåner**: med det här alternativet kan du enkelt och snabbt framtvinga MFA för alla användare i din miljö med en strikt princip för att:

* Utmana administrativa konton och administrativa inloggnings metoder
* Kräv MFA-anrop via Microsoft Authenticator för alla användare
* Begränsa bakåtkompatibla autentiseringsprotokoll.

Den här metoden är tillgänglig för alla licensierings nivåer men kan inte blandas med befintliga principer för villkorlig åtkomst. Du hittar mer information i [säkerhets inställningarna för Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)

**Alternativ 2**: [Aktivera Multi-Factor Authentication genom att ändra användar tillstånd](../../active-directory/authentication/howto-mfa-userstates.md).   
**Förmån**: det här är den traditionella metoden för att kräva tvåstegsverifiering. Det fungerar med både [Azure AD-Multi-Factor Authentication i molnet och Azure Multi-Factor Authentication-Server](../../active-directory/authentication/concept-mfa-howitworks.md). Om du använder den här metoden måste användarna utföra tvåstegsverifiering varje gång de loggar in och åsidosätter principer för villkorlig åtkomst.

För att avgöra var Multi-Factor Authentication behöver aktive ras, se [vilken version av Azure AD MFA som passar min organisation?](../../active-directory/authentication/concept-mfa-howitworks.md).

**Alternativ 3**: [Aktivera Multi-Factor Authentication med princip för villkorlig åtkomst](../../active-directory/authentication/howto-mfa-getstarted.md).
**Förmån**: med det här alternativet kan du begära tvåstegsverifiering under vissa villkor genom [att använda villkorlig åtkomst](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). Vissa villkor kan vara användar inloggningar från olika platser, ej betrodda enheter eller program som du anser vara riskfyllda. Genom att definiera vissa villkor där du kräver tvåstegsverifiering kan du undvika en konstant uppskrivning för dina användare, vilket kan vara en krångligt användar upplevelse.

Detta är det mest flexibla sättet att aktivera tvåstegsverifiering för dina användare. Att aktivera en princip för villkorlig åtkomst fungerar bara för Azure AD Multi-Factor Authentication i molnet och är en Premium funktion i Azure AD. Du hittar mer information om den här metoden i [distribuera molnbaserad Azure AD-Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).

**Alternativ 4**: Aktivera Multi-Factor Authentication med principer för villkorlig åtkomst genom att utvärdera [riskfyllda principer för villkorlig åtkomst](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Förmån**: med det här alternativet kan du:

* Identifiera potentiella sårbarheter som påverkar organisationens identiteter.
* Konfigurera automatiserade svar på identifierade misstänkta åtgärder som är relaterade till din organisations identiteter.
* Undersök misstänkta incidenter och vidta lämpliga åtgärder för att lösa dem.

I den här metoden används Azure AD Identity Protection riskbedömning för att fastställa om tvåstegsverifiering krävs baserat på användar-och inloggnings risk för alla moln program. Den här metoden kräver Azure Active Directory P2-licensiering. Du hittar mer information om den här metoden i [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> Alternativ 2, aktivera Multi-Factor Authentication genom att ändra användar tillstånd åsidosätter villkorliga åtkomst principer. Eftersom alternativ 3 och 4 använder principer för villkorlig åtkomst kan du inte använda alternativ 2 med dem.

Organisationer som inte lägger till extra lager med identitets skydd, till exempel tvåstegsverifiering, är mer känsliga för stöld av autentiseringsuppgifter. En stöldskydds attack kan leda till data kompromisser.

## <a name="use-role-based-access-control"></a>Använd rollbaserad åtkomstkontroll

Åtkomst hantering för moln resurser är avgörande för alla organisationer som använder molnet. [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) hjälper dig att hantera vem som har åtkomst till Azure-resurser, vad de kan göra med dessa resurser och vilka områden de har åtkomst till.

Att utse grupper eller enskilda roller som är ansvariga för specifika funktioner i Azure bidrar till att undvika förvirring som kan leda till mänsklig och automation-fel som skapar säkerhets risker. Att begränsa åtkomsten baserat på [behovet av att känna till](https://en.wikipedia.org/wiki/Need_to_know) och [minsta behörighets](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhets principer är att det är absolut nödvändigt för organisationer som vill tillämpa säkerhets principer för data åtkomst.

Ditt säkerhets team behöver insyn i dina Azure-resurser för att utvärdera och åtgärda risker. Om säkerhets teamet har drift ansvar behöver de ytterligare behörigheter för att utföra sina jobb.

Du kan använda [Azure RBAC](../../role-based-access-control/overview.md) för att tilldela behörigheter till användare, grupper och program i ett visst omfång. Omfånget för en rolltilldelning kan vara en prenumeration, en resursgrupp eller en enskild resurs.

**Bästa praxis**: åtskiljer uppgifter i teamet och ge endast åtkomst till de användare som de behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter i din Azure-prenumeration eller resurser kan du bara tillåta vissa åtgärder i ett visst omfång.
**Information**: Använd [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md) i Azure för att tilldela behörigheter till användare.

> [!Note]
> Vissa behörigheter skapar onödiga komplexitetar och förvirring, ackumulerar en "äldre" konfiguration som är svår att åtgärda utan att du behöver bryta något. Undvik resursspecifika behörigheter. Använd i stället hanterings grupper för behörigheter och resurs grupper i hela företaget för behörigheter i prenumerationer. Undvik användarspecifika behörigheter. Tilldela istället åtkomst till grupper i Azure AD.

**Bästa praxis**: bevilja säkerhets team med Azures ansvar åtkomst för att se Azure-resurser så att de kan utvärdera och åtgärda risker.
**Information**: bevilja säkerhets team rollen Azure RBAC [Security Reader](../../role-based-access-control/built-in-roles.md#security-reader) . Du kan använda rot hanterings gruppen eller segment hanterings gruppen, beroende på ansvars områdets omfattning:

* **Rot hanterings grupp** för team som ansvarar för alla företags resurser
* **Segment hanterings grupp** för team med begränsad omfattning (vanligt vis på grund av regler eller andra organisatoriska gränser)

**Bästa praxis**: ge lämpliga behörigheter till säkerhets grupper som har direkt drifts ansvar.
**Information**: granska de inbyggda Azure-rollerna för lämplig roll tilldelning. Om de inbyggda rollerna inte uppfyller organisationens speciella behov kan du skapa [Azure-anpassade roller](../../role-based-access-control/custom-roles.md). Precis som med inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvud namn vid prenumeration, resurs grupp och resurs omfång.

**Bästa praxis**: bevilja Azure Security Center åtkomst till säkerhets roller som behöver den. Security Center gör det möjligt för säkerhets team att snabbt identifiera och åtgärda risker.
**Information**: Lägg till säkerhets team med dessa behov av rollen Azure RBAC- [säkerhetsadministratör](../../role-based-access-control/built-in-roles.md#security-admin) så att de kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer och stänga av aviseringar och rekommendationer. Du kan göra detta med hjälp av rot hanterings gruppen eller segment hanterings gruppen, beroende på ansvars området.

Organisationer som inte tillämpar data åtkomst kontroll med hjälp av funktioner som Azure RBAC kan ge fler privilegier än vad som krävs för sina användare. Detta kan leda till data kompromisser genom att tillåta användare att komma åt typer av data (till exempel hög påverkan på företaget) som de inte borde ha.

## <a name="lower-exposure-of-privileged-accounts"></a>Lägre exponering av privilegierade konton

Att skydda privilegie rad åtkomst är ett viktigt första steg för att skydda företagets till gångar. Att minimera antalet personer som har åtkomst till säker information eller resurser minskar risken för att obehöriga användare får åtkomst eller att en behörig användare oavsiktligt påverkar en känslig resurs.

Privilegierade konton är konton som administrerar och hanterar IT-system. Cyberhot-angripare riktar dessa konton för att få åtkomst till en organisations data och system. För att skydda privilegie rad åtkomst bör du isolera konton och system från risken att exponeras för en obehörig användare.

Vi rekommenderar att du utvecklar och följer en översikt för att skydda privilegie rad åtkomst mot cyberhot-attacker. Information om hur du skapar en detaljerad översikt för att skydda identiteter och åtkomst som hanteras eller rapporteras i Azure AD, Microsoft Azure, Microsoft 365 och andra moln tjänster finns [i skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](../../active-directory/roles/security-planning.md).

Följande sammanfattar de bästa metoderna för att [skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](../../active-directory/roles/security-planning.md):

**Bästa praxis**: hantera, kontrol lera och övervaka åtkomst till privilegierade konton.   
**Information**: aktivera [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md). När du har aktiverat Privileged Identity Management får du e-postaviseringar om roll ändringar för privilegie rad åtkomst. De här meddelandena ger tidig varning när ytterligare användare läggs till i privilegierade roller i din katalog.

**Bästa praxis**: se till att alla kritiska administratörs konton hanteras av Azure AD-konton.
**Information**: ta bort alla konsument konton från viktiga administratörs roller (till exempel Microsoft-konton som hotmail.com, live.com och Outlook.com).

**Bästa praxis**: se till att alla viktiga administratörs roller har ett separat konto för administrativa uppgifter för att undvika att nätfiske och andra attacker kan kompromettera administrativa privilegier.
**Information**: skapa ett separat administratörs konto som har tilldelats de behörigheter som krävs för att utföra administrativa uppgifter. Blockera användningen av dessa administratörs konton för dagliga produktivitets verktyg som Microsoft 365 e-post eller för valfri Webbs ökning.

**Bästa praxis**: identifiera och kategorisera konton som har hög privilegierade roller.   
**Information**: när du har aktiverat Azure AD Privileged Identity Management kan du Visa de användare som finns i den globala administratören, privilegierad roll administratör och andra privilegierade roller. Ta bort alla konton som inte längre behövs i dessa roller och kategorisera de återstående konton som har tilldelats administratörs roller:

* Individuellt tilldelad till administrativa användare och kan användas för icke-administrativa syfte (till exempel personligt e-post)
* Individuellt tilldelad till administrativa användare och endast avsedd för administrativa syfte
* Delas över flera användare
* För situationer med nöd åtkomst
* För automatiserade skript
* För externa användare

**Bästa praxis**: implementera JIT-åtkomst (just in Time) för att ytterligare minska exponerings tiden för privilegier och öka din insyn i användningen av privilegierade konton.   
**Information**: Azure AD Privileged Identity Management gör det möjligt att:

* Begränsa användare till att endast ta hänsyn till JIT-privilegier.
* Tilldela roller för kortare varaktighet med förtroende för att privilegierna återkallas automatiskt.

**Bästa praxis**: definiera minst två nödfalls åtkomst konton.   
**Information**: åtkomst konton för nöd situationer begränsar privilegie rad åtkomst i en befintlig Azure Active Directorys miljö. Dessa konton är mycket privilegierade och har inte tilldelats till vissa individer. Konton för nöd åtkomst är begränsade till scenarier där normala administrativa konton inte kan användas. Organisationerna måste begränsa nöd kontots användning enbart till den nödvändiga tiden.

Utvärdera de konton som är tilldelade eller berättigade till den globala administratörs rollen. Om du inte ser några moln konton med hjälp av `*.onmicrosoft.com` domänen (avsedd för nöd åtkomst) skapar du dem. Mer information finns i [Hantera administratörs konton för nöd åtkomst i Azure AD](../../active-directory/roles/security-emergency-access.md).

**Bästa praxis**: ha en "Break glas"-process på plats i händelse av en nöd situation.
**Information**: Följ stegen för att [skydda privilegie rad åtkomst för Hybrid-och moln distributioner i Azure AD](../../active-directory/roles/security-planning.md).

**Bästa praxis**: Kräv att alla kritiska administratörs konton är lösen ords lösa (önskade) eller kräver Multi-Factor Authentication.
**Information**: Använd [Microsoft Authenticator-appen](../../active-directory/authentication/howto-authentication-passwordless-phone.md) för att logga in på ett Azure AD-konto utan att använda ett lösen ord. Precis som [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification), använder Microsoft Authenticator nyckelbaserad autentisering för att aktivera autentiseringsuppgifter som är knutna till en enhet och använder bio metrisk autentisering eller PIN-kod.

Kräv Azure AD Multi-Factor Authentication vid inloggning för alla enskilda användare som är permanent tilldelade till en eller flera av administratörs rollerna för Azure AD: global administratör, privilegie rad roll administratör, Exchange Online-administratör och SharePoint Online-administratör. Aktivera [Multi-Factor Authentication för dina administratörs konton](../../active-directory/authentication/howto-mfa-userstates.md) och se till att administratörs kontots användare har registrerat sig.

**Bästa praxis**: för kritiska administratörs konton har du en administratörs arbets station där produktions aktiviteter inte är tillåtna (till exempel bläddring och e-post). Detta skyddar dina administratörs konton mot angrepps vektorer som använder bläddring och e-post och minskar risken för en större incident.
**Information**: Använd en administratörs arbets Station. Välj en säkerhets nivå för arbets stationen:

- Hög säkra produktivitets enheter ger avancerad säkerhet för bläddring och andra produktivitets uppgifter.
- [Arbets stationer med privilegie rad åtkomst (Paw)](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) tillhandahåller ett dedikerat operativ system som skyddas från Internet attacker och hot vektorer för känsliga uppgifter.

**Bästa praxis**: ta bort deetablering av administratörs konton när anställda lämnar din organisation.
**Information**: har en process på plats som inaktiverar eller tar bort administratörs konton när anställda lämnar din organisation.

**Bästa praxis**: testa administratörs konton regelbundet med hjälp av aktuella angrepps metoder.
**Information**: Använd Microsoft 365 angrepps simulator eller ett erbjudande från tredje part för att köra realistiska angrepps scenarier i din organisation. Detta kan hjälpa dig att hitta sårbara användare innan ett verkligt angrepp uppstår.

**Bästa praxis**: vidta åtgärder för att minimera de oftast använda angripna teknikerna.  
**Information**: [identifiera Microsoft-konton i administrativa roller som måste växlas till arbets-eller skol konton](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Se till att separata användar konton och vidarebefordran av e-post för globala administratörs konton](../../active-directory/roles/security-planning.md)  

[Kontrol lera att lösen orden för administrativa konton nyligen har ändrats](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Aktivera synkronisering av lösen ords-hash](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Kräv Multi-Factor Authentication för användare i alla privilegierade roller samt exponerade användare](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Få Microsoft 365 säkra poäng (om du använder Microsoft 365)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Läs Microsoft 365 säkerhets vägledning (om du använder Microsoft 365)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Konfigurera Microsoft 365 aktivitets övervakning (om du använder Microsoft 365)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Upprätta incidenter/svars plan för nöd situationer](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Skydda lokala privilegierade administratörs konton](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Om du inte skyddar privilegie rad åtkomst kanske du upptäcker att du har för många användare i privilegierade roller och är mer sårbara för attacker. Skadliga aktörer, inklusive cyberhot-angripare, använder ofta administratörs konton och andra delar av privilegie rad åtkomst för att få åtkomst till känsliga data och system med hjälp av stöld av autentiseringsuppgifter.

## <a name="control-locations-where-resources-are-created"></a>Kontrol lera platser där resurser skapas

Att göra det möjligt för moln operatörer att utföra uppgifter samtidigt som de inte behöver dela upp konventioner som behövs för att hantera organisationens resurser är mycket viktigt. Organisationer som vill styra de platser där resurser skapas bör hårdkoda dessa platser.

Du kan använda [Azure Resource Manager](../../azure-resource-manager/management/overview.md) för att skapa säkerhets principer vars definitioner beskriver de åtgärder eller resurser som uttryckligen nekas. Du tilldelar dessa princip definitioner i det önskade omfånget, till exempel prenumerationen, resurs gruppen eller en enskild resurs.

> [!NOTE]
> Säkerhets principer är inte samma som för Azure RBAC. De använder faktiskt Azure RBAC för att auktorisera användare att skapa dessa resurser.
>
>

Organisationer som inte kontrollerar hur resurser skapas är mer känsliga för användare som kan missbruka tjänsten genom att skapa fler resurser än de behöver. Att härdning av resurs skapandet är ett viktigt steg för att skydda ett scenario med flera innehavare.

## <a name="actively-monitor-for-suspicious-activities"></a>Övervaka aktivt för misstänkta aktiviteter

Ett aktivt identitets övervaknings system kan snabbt identifiera misstänkt beteende och utlösa en avisering för ytterligare undersökning. I följande tabell visas två funktioner i Azure AD som kan hjälpa organisationer att övervaka sina identiteter:

**Bästa praxis**: ha en metod för att identifiera:

- Försöker logga in [utan att spåras](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- [Brute Force](../../active-directory/reports-monitoring/howto-find-activity-reports.md) -attacker mot ett visst konto.
- Försöker logga in från flera platser.
- Inloggningar från [angripna enheter](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Misstänkta IP-adresser.

**Information**: Använd Azure AD Premium [avvikelse rapporter](../../active-directory/reports-monitoring/overview-reports.md). Ha processer och procedurer på plats för IT-administratörer för att köra rapporterna dagligen eller på begäran (vanligt vis i ett scenario med incident svar).

**Bästa praxis**: ha ett aktivt övervaknings system som meddelar dig om risker och kan justera risk nivån (hög, medel eller låg) enligt dina affärs behov.   
**Information**: Använd [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)som flaggar de aktuella riskerna på sin egen instrument panel och skickar dagliga sammanfattnings aviseringar via e-post. För att skydda din organisations identiteter kan du konfigurera riskfyllda principer som automatiskt svarar på identifierade problem när en angiven risk nivå uppnås.

Organisationer som inte aktivt övervakar sina identitets system riskerar att ha komprometterade användarautentiseringsuppgifter. Utan kunskap om att misstänkta aktiviteter sker genom dessa autentiseringsuppgifter kan organisationer inte minimera den här typen av hot.

## <a name="use-azure-ad-for-storage-authentication"></a>Använda Azure AD för lagrings autentisering
[Azure Storage](../../storage/common/storage-auth-aad.md) stöder autentisering och auktorisering med Azure AD för Blob Storage och Queue Storage. Med Azure AD-autentisering kan du använda Azures rollbaserade åtkomst kontroll för att ge specifika behörigheter till användare, grupper och program ned till omfånget för en enskild BLOB-behållare eller-kö.

Vi rekommenderar att du använder [Azure AD för att autentisera åtkomsten till lagringen](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Nästa steg

Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.