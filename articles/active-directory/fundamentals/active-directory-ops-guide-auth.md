---
title: Azure Active Directory referensguide för autentiseringshanteringsåtgärder
description: Den här åtgärdsreferensguiden beskriver de kontroller och åtgärder som du bör vidta för att skydda autentiseringshanteringen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531951"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory referensguide för autentiseringshanteringsåtgärder

Det här avsnittet av [referensguiden](active-directory-ops-guide-intro.md) för Azure AD-åtgärder beskriver de kontroller och åtgärder som du bör vidta för att skydda och hantera autentiseringsuppgifter, definiera autentiseringsupplevelse, delegera tilldelning, mäta användning och definiera åtkomstprinciper baserat på företagets säkerhetsstatus.

> [!NOTE]
> Dessa rekommendationer är aktuella från publiceringsdatumet men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitetsmetoder allt eftersom Microsofts produkter och tjänster utvecklas över tid.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte är en del av ett projekt för drift. Det är fortfarande viktigt att du ställer in de här uppgifterna för att optimera din miljö. Viktiga uppgifter och deras rekommenderade ägare är:

| Uppgift | Ägare |
| :- | :- |
| Hantera livscykeln för konfiguration av enkel inloggning (SSO) i Azure AD | IAM-driftteam |
| Utforma principer för villkorlig åtkomst för Azure AD-program | InfoSec-arkitekturteamet |
| Arkivera inloggningsaktivitet i ett SIEM-system | InfoSec-driftteamet |
| Arkivera riskhändelser i ett SIEM-system | InfoSec-driftteamet |
| Granska och undersöka säkerhetsrapporter | InfoSec-driftteamet |
| Granska och undersöka riskhändelser | InfoSec-driftteamet |
| Granska och undersöka användare som har flaggats för risk- och sårbarhetsrapporter från Azure AD Identity Protection | InfoSec-driftteamet |

> [!NOTE]
> Azure AD Identity Protection kräver en Azure AD Premium P2-licens. Information om hur du hittar rätt licens för dina krav finns i Jämföra allmänt tillgängliga [funktioner i Azure AD Free och Azure AD Premium utgåvor](https://azure.microsoft.com/pricing/details/active-directory/).

När du granskar listan kan du behöva tilldela en ägare för uppgifter som saknar en ägare eller justera ägarskapet för uppgifter med ägare som inte överensstämmer med rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Ägare rekommenderad läsning

- [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)
- [Styrning i Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Hantering av autentiseringsuppgifter

### <a name="password-policies"></a>Lösenordsprinciper

Att hantera lösenord på ett säkert sätt är en av de viktigaste delarna av identitets- och åtkomsthantering och ofta det största målet för attacker. Azure AD stöder flera funktioner som kan hjälpa till att förhindra att ett angrepp lyckas.

Använd tabellen nedan för att hitta den rekommenderade lösningen för att åtgärda problemet som behöver åtgärdas:

| Problem | Rekommendation |
| :- | :- |
| Ingen mekanism för att skydda mot svaga lösenord | Aktivera [självbetjäning av lösenordsåterställning i](../authentication/concept-sspr-howitworks.md) Azure AD (SSPR) och [lösenordsskydd](../authentication/concept-password-ban-bad-on-premises.md) |
| Ingen mekanism för att identifiera läckta lösenord | Aktivera [synkronisering av lösenordshashar](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) för att få insikter |
| Använda AD FS och kan inte gå över till hanterad autentisering | Aktivera [AD FS smart utelåsning i extranät](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) och/eller [Azure AD Smart-utelåsning](../authentication/howto-password-smart-lockout.md) |
| Lösenordsprincipen använder komplexitetsbaserade regler som längd, flera teckenuppsättningar eller förfallotid | Överväg att använda [Microsofts rekommenderade metoder och](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) byt metod för lösenordshantering och distribuera [Azure AD-lösenordsskydd.](../authentication/concept-password-ban-bad.md) |
| Användare är inte registrerade för att använda multifaktorautentisering (MFA) | [Registrera alla användares säkerhetsinformation](../identity-protection/howto-identity-protection-configure-mfa-policy.md) så att den kan användas som en mekanism för att verifiera användarens identitet tillsammans med deras lösenord |
| Det finns ingen återkallelse av lösenord baserat på användarrisk | Distribuera azure AD [Identity Protection-principer för användarrisk för](../identity-protection/howto-identity-protection-configure-risk-policies.md) att tvinga lösenordsändringar på läckta autentiseringsuppgifter med hjälp av SSPR |
| Det finns ingen smart utelåsningsmekanism för att skydda skadlig autentisering från obehöriga som kommer från identifierade IP-adresser | Distribuera moln hanterad autentisering med antingen synkronisering av lösenordshashar [eller direktautentisering](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lösenordsprinciper rekommenderad läsning

- [Metodtips för Azure AD AD FS: Skydd mot lösenordsattacker – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Aktivera lösenordsåterställning via självbetjäning och lösenordsskydd

Användare som behöver ändra eller återställa sina lösenord är en av de största källorna till volym- och kostnad för supportsamtal. Förutom kostnaden är det viktigt att ändra lösenordet som ett verktyg för att minska användarrisken för att förbättra organisationens säkerhetsposition.

Vi rekommenderar minst att du distribuerar Azure [AD-lösenordsåterställning](../authentication/concept-sspr-howitworks.md) via självbetjäning (SSPR) och lokalt [lösenordsskydd för](../authentication/howto-password-ban-bad-on-premises-deploy.md) att åstadkomma följande:

- Avleda samtal till supportavdelningen.
- Ersätt användningen av tillfälliga lösenord.
- Ersätt alla befintliga lösenordshanteringslösning med självbetjäning som förlitar sig på en lokal lösning.
- [Eliminera svaga lösenord](../authentication/concept-password-ban-bad.md) i din organisation.

> [!NOTE]
> För organisationer med en Azure AD Premium P2-prenumeration rekommenderar vi att du distribuerar SSPR och använder den som en del av [en identity protection-användarriskprincip.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="strong-credential-management"></a>Hantering av starka autentiseringsuppgifter

Lösenorden är inte tillräckligt säkra för att hindra obehöriga från att få åtkomst till din miljö. Alla användare med ett privilegierat konto måste minst vara aktiverade för multifaktorautentisering (MFA). Vi rekommenderar att du aktiverar kombinerad [registrering och](../authentication/concept-registration-mfa-sspr-combined.md) kräver att alla användare registrerar sig för MFA och SSPR med hjälp av den [kombinerade registreringsupplevelsen](../user-help/security-info-setup-signin.md). Så småningom rekommenderar vi att du inför en strategi [för](../authentication/concept-resilient-controls.md) att ge motståndskraft för att minska risken för utelåsning på grund av oförutsedda omständigheter.

![Kombinerat användarupplevelseflöde](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Autentiseringsåter återhämtning vid lokalt avbrott

Förutom fördelarna med enkelhet och aktivering av identifiering av läckta autentiseringsuppgifter kan Azure AD:s synkronisering av lösenordshashar (PHS) och Azure AD MFA ge användare åtkomst till SaaS-program och Microsoft 365 trots lokala avbrott på grund av cyberattacker som [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Det är också möjligt att aktivera PHS tillsammans med federation. Aktivering av PHS möjliggör återställning av autentisering när federationstjänster inte är tillgängliga.

Om din lokala organisation saknar en återhämtningsstrategi för avbrott eller har en som inte är integrerad med Azure AD bör du distribuera Azure AD PHS och definiera en haveriberedskapsplan som innehåller PHS. Om du aktiverar Azure AD PHS kan användarna autentisera mot Azure AD om lokal Active Directory inte är tillgängliga.

![synkroniseringsflöde för lösenordshashar](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Mer information om dina autentiseringsalternativ finns i Välja rätt autentiseringsmetod för din Azure Active Directory [hybrididentitetslösning.](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Programmatisk användning av autentiseringsuppgifter

Azure AD-skript som använder PowerShell eller program som använder Microsoft Graph-API:et kräver säker autentisering. Dålig hantering av autentiseringsuppgifter som kör dessa skript och verktyg ökar risken för stöld av autentiseringsuppgifter. Om du använder skript eller program som förlitar sig på hårdkodade lösenord eller lösenordsuppmaning bör du först granska lösenord i konfigurationsfiler eller källkod, [](../reports-monitoring/tutorial-access-api-with-certificates.md) sedan ersätta dessa beroenden och använda Hanterade Azure-identiteter, Integrated-Windows-autentisering eller certifikat när det är möjligt. För program där de tidigare lösningarna inte är möjliga kan du överväga att använda [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Om du fastställer att det finns tjänstens huvudnamn med lösenordsuppgifter och du är osäker på hur dessa lösenord skyddas av skript eller program, kontaktar du programmets ägare för att bättre förstå användningsmönster.

Microsoft rekommenderar också att du kontaktar programägare för att förstå användningsmönster om det finns tjänsthuvudnamn med autentiseringsuppgifter för lösenord.

## <a name="authentication-experience"></a>Autentiseringsupplevelse

### <a name="on-premises-authentication"></a>Lokal autentisering

Federerad autentisering med Integrerad Windows-autentisering (IWA) eller sömlös enkel Sign-On-hanterad autentisering (SSO) med synkronisering av lösenordshashar eller direktautentisering är den bästa användarupplevelsen i företagsnätverket med siktlinje till lokala domänkontrollanter. Det minimerar utmattning av autentiseringsuppgifter och minskar risken för att användare faller offer för nätfiskeattacker. Om du redan använder moln hanterad autentisering med PHS eller PTA, men användarna fortfarande behöver ange sina lösenord när de autentiserar lokalt, bör du omedelbart distribuera [sömlös enkel inloggning.](../hybrid/how-to-connect-sso.md) Å andra sidan, om du för närvarande är federerad med planer på att så småningom migrera till moln hanterad autentisering, bör du implementera sömlös enkel inloggning som en del av migreringsprojektet.

### <a name="device-trust-access-policies"></a>Principer för enhetsförtroendeåtkomst

Precis som en användare i din organisation är en enhet en grundläggande identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när som helst och från valfri plats. Genom att autentisera enheten och redovisa dess förtroendetyp förbättras din säkerhetsstatus och användbarhet genom att:

- Undvika friktion, till exempel med MFA, när enheten är betrodd
- Blockera åtkomst från ej betrodda enheter
- För Windows 10-enheter tillhandahåller [du enkel inloggning till lokala resurser sömlöst.](../devices/azuread-join-sso.md)

Du kan utföra det här målet genom att hämta enhetsidentiteter och hantera dem i Azure AD med någon av följande metoder:

- Organisationer kan använda [Microsoft Intune](/intune/what-is-intune) för att hantera enheten och genomdriva efterlevnadsprinciper, attesta enhetens hälsotillstånd och ange principer för villkorlig åtkomst baserat på om enheten är kompatibel eller inte. Microsoft Intune kan hantera iOS-enheter, Mac-datorer (via JAMF-integrering), Windows-datorer (inbyggt med Mobile Enhetshantering för Windows 10 och samhantering med Microsoft Endpoint Configuration Manager) och mobila Android-enheter.
- [Hybrid Azure AD Join](../devices/hybrid-azuread-join-managed-domains.md) ger hantering med grupprinciper eller Microsoft Endpoint Configuration Manager i en miljö med Active Directory-domän-anslutna datorer enheter. Organisationer kan distribuera en hanterad miljö antingen via PHS eller PTA med sömlös enkel inloggning. Genom att ta dina enheter till Azure AD maximeras användarproduktiviteten via enkel inloggning för dina molnresurser [](../conditional-access/overview.md) och lokala resurser, samtidigt som du kan skydda åtkomsten till dina molnresurser och lokala resurser med villkorlig åtkomst på samma gång.

Om du har domänanslutningar till Windows-enheter som inte är registrerade i molnet, eller domän anslutna Windows-enheter som är registrerade i molnet men utan principer för villkorlig åtkomst, bör du registrera de oregistrerade enheterna och i båda fallen använda [Hybrid Azure AD-anslutning](../conditional-access/require-managed-devices.md) som en kontroll i dina principer för villkorlig åtkomst.

![En skärmbild av beviljande i princip för villkorlig åtkomst som kräver hybridenhet](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Om du hanterar enheter med MDM eller Microsoft Intune, men inte använder enhetskontroller i [](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) dina principer för villkorlig åtkomst, rekommenderar vi att du använder Kräv att enheten är markerad som kompatibel som en kontroll i dessa principer.

![En skärmbild av beviljande i princip för villkorlig åtkomst som kräver enhetsefterlevnad](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Åtkomstprinciper för enhetsförtroende rekommenderad läsning

- [Gör så här för att: Planera implementeringen av Azure Active Directory hybridanslutningar](../devices/hybrid-azuread-join-plan.md)
- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 [ersätter Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification) lösenord med stark tvåfaktorautentisering på datorer. Windows Hello för företag ger en mer effektiv MFA-upplevelse för användare och minskar beroendet av lösenord. Om du inte har börjat distribuera Windows 10 eller bara delvis har distribuerat dem rekommenderar vi att du uppgraderar till Windows 10 och aktiverar [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) på alla enheter.

Om du vill veta mer om lösenordsfri autentisering kan du läsa Mer information i [A world without passwords with Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Programautentisering och -tilldelning

### <a name="single-sign-on-for-apps"></a>Enkel inloggning för appar

Att tillhandahålla en standardiserad mekanism för enkel inloggning för hela företaget är avgörande för bästa användarupplevelse, minskning av risker, möjlighet att rapportera och styrning. Om du använder program som stöder enkel inloggning med Azure AD men som för närvarande är konfigurerade att använda lokala konton bör du konfigurera om dessa program så att de använder enkel inloggning med Azure AD. Om du använder program som stöder enkel inloggning med Azure AD men använder en annan identitetsprovider bör du på samma sätt konfigurera om dessa program så att de även använder enkel inloggning med Azure AD. För program som inte stöder federationsprotokoll men som stöder formulärbaserad autentisering [](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) rekommenderar vi att du konfigurerar programmet så att det använder lösenordsvalv med Azure AD Programproxy.

![AppProxy-lösenordsbaserad inloggning](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Om du inte har någon mekanism för att identifiera ohanterade program i din organisation rekommenderar vi att du implementerar en identifieringsprocess med hjälp av en CASB-lösning (Cloud Access Security Broker), till [exempel Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Slutligen, om du har ett Azure AD-appgalleri och använder program som stöder enkel inloggning med Azure AD, rekommenderar vi att du listar [programmet i appgalleriet](../develop/v2-howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Rekommenderad läsning för enkel inloggning

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrering av AD FS till Azure AD

[Migrering av appar från AD FS](../manage-apps/migrate-adfs-apps-to-azure.md) till Azure AD ger ytterligare funktioner för säkerhet, mer konsekvent hanterbarhet och en bättre samarbetsupplevelse. Om du har program som konfigurerats i AD FS som stöder enkel inloggning med Azure AD bör du konfigurera om dessa program så att de använder enkel inloggning med Azure AD. Om du har program som konfigurerats i AD FS med ovanliga konfigurationer som inte stöds av Azure AD, bör du kontakta appägare för att förstå om den särskilda konfigurationen är ett absolut krav för programmet. Om det inte krävs bör du konfigurera om programmet så att det använder enkel inloggning med Azure AD.

![Azure AD som primär identitetsprovider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health för ADFS](../hybrid/how-to-connect-health-adfs.md) kan användas för att samla in konfigurationsinformation om varje program som potentiellt kan migreras till Azure AD.

### <a name="assign-users-to-applications"></a>Tilldela användare till program

[Att tilldela användare till program](../manage-apps/assign-user-or-group-access-portal.md) mappas bäst med hjälp av grupper eftersom de ger större flexibilitet och möjlighet att hantera i stor skala. Fördelarna med att använda grupper är [attributbaserat dynamiskt gruppmedlemskap och](../enterprise-users/groups-dynamic-membership.md) [delegering till appägare.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Om du redan använder och hanterar grupper rekommenderar vi därför att du gör följande för att förbättra hanteringen i stor skala:

- Delegera grupphantering och styrning till programägare.
- Tillåt självbetjäning för åtkomst till programmet.
- Definiera dynamiska grupper om användarattribut konsekvent kan fastställa åtkomsten till program.
- Implementera attestation till grupper som används för programåtkomst med hjälp av [Azure AD-åtkomstgranskningar.](../governance/access-reviews-overview.md)

Å andra sidan, om du hittar program som har tilldelning till enskilda användare, måste du implementera [styrning runt](../governance/index.yml) dessa program.

#### <a name="assign-users-to-applications-recommended-reading"></a>Tilldela användare till program som rekommenderas läsning

- [Tilldela användare och grupper till ett program i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegera behörigheter för appregistrering i Azure Active Directory](../roles/delegate-app-roles.md)
- [Regler för dynamiskt medlemskap för grupper i Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Åtkomstprinciper

### <a name="named-locations"></a>Namngivna platser

Med [namngivna platser i](../reports-monitoring/quickstart-configure-named-locations.md) Azure AD kan du märka betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:

- Förhindra falska positiva resultat i riskhändelser. Om du loggar in från en betrodd nätverksplats sänks en användares inloggningsrisk.
- Konfigurera [platsbaserad villkorlig åtkomst](../reports-monitoring/quickstart-configure-named-locations.md).

![Namngivna platser](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Baserat på prioritet använder du tabellen nedan för att hitta den rekommenderade lösning som bäst uppfyller organisationens behov:

| **Priority** | **Scenario** | **Rekommendation** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Om du använder PHS eller PTA och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 2 | Om du är federerad och inte använder "insideCorporateNetwork"-anspråk och namngivna platser har inte definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 3 | Om du inte använder namngivna platser i principer för villkorlig åtkomst och det inte finns någon risk eller enhetskontroller i principer för villkorlig åtkomst | Konfigurera principen för villkorlig åtkomst så att den inkluderar namngivna platser |
| 4 | Om du är federerad och använder "insideCorporateNetwork"-anspråket och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av riskhändelser |
| 5 | Om du använder betrodda IP-adresser med MFA i stället för namngivna platser och markerar dem som betrodda | Definiera namngivna platser och markera dem som betrodda för att förbättra identifieringen av riskhändelser |

### <a name="risk-based-access-policies"></a>Principer för riskbaserad åtkomst

Azure AD kan beräkna risken för varje inloggning och varje användare. Att använda risker som ett villkor i åtkomstprinciper kan ge en bättre användarupplevelse, till exempel färre autentiseringsuppmaningar och bättre säkerhet. Till exempel kan användarna bara frågas när de behövs och automatisera svar och reparation.

![Princip för inloggningsrisk](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Om du redan äger Azure AD Premium P2-licenser som stöder användning av risker i åtkomstprinciper, men de inte används, rekommenderar vi starkt att du lägger till risker i din säkerhetsstatus.

#### <a name="risk-based-access-policies-recommended-reading"></a>Riskbaserade åtkomstprinciper rekommenderad läsning

- [Gör så här för att: Konfigurera principen för inloggningsrisk](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Gör så här för att: Konfigurera riskprincipen för användare](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Åtkomstprinciper för klientprogram

Microsoft Intune application management (MAM) ger möjlighet att skicka dataskyddskontroller som lagringskryptering, PIN-kod, rensning av fjärrlagring osv. till kompatibla mobila klientprogram som Outlook Mobile. Dessutom kan principer för villkorlig åtkomst skapas för att [begränsa åtkomsten till](../conditional-access/app-based-conditional-access.md) molntjänster som Exchange Online från godkända eller kompatibla appar.

Om dina anställda installerar MAM-kompatibla program som Office-mobilappar för att få åtkomst till företagsresurser som Exchange Online eller SharePoint Online, och du även stöder BYOD (Bring Your Own Device), rekommenderar vi att du distribuerar MAM-principer för program för att hantera programkonfigurationen i personligt ägda enheter utan MDM-registrering och uppdaterar dina principer för villkorlig åtkomst så att endast åtkomst från MAM-kompatibla klienter tillåts.

![Kontroll för beviljande av villkorsstyrd åtkomst](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Om anställda installerar MAM-kompatibla program mot företagsresurser och åtkomsten är begränsad på Intune-hanterade enheter bör du överväga att distribuera MAM-programprinciper för att hantera programkonfigurationen för personliga enheter och uppdatera principer för villkorsstyrd åtkomst så att endast åtkomst från MAM-kompatibla klienter tillåts.

### <a name="conditional-access-implementation"></a>Implementering av villkorsstyrd åtkomst

Villkorsstyrd åtkomst är ett viktigt verktyg för att förbättra organisationens säkerhetsstatus. Därför är det viktigt att du följer dessa metodtips:

- Se till att alla SaaS-program har minst en tillämpad princip
- Undvik att kombinera **Alla appar med** blockkontrollen **för** att undvika utelåsningsrisker
- Undvik att använda **Alla användare som** ett filter och oavsiktligt lägga till **gäster**
- **Migrera alla "äldre" principer till Azure Portal**
- Fånga alla kriterier för användare, enheter och program
- Använd principer för villkorsstyrd [åtkomst för att implementera MFA](../conditional-access/plan-conditional-access.md)i stället för att använda **MFA per användare**
- Ha en liten uppsättning grundläggande principer som kan tillämpas på flera program
- Definiera tomma undantagsgrupper och lägg till dem i principerna för att ha en undantagsstrategi
- Planera för [brytkonton](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) utan MFA-kontroller
- Säkerställ en konsekvent upplevelse i Microsoft 365 klientprogram, till exempel Teams, OneDrive, Outlook osv.) genom att implementera samma uppsättning kontroller för tjänster som Exchange Online och Sharepoint Online
- Tilldelning till principer ska implementeras via grupper, inte individer
- Gör regelbundna granskningar av de undantagsgrupper som används i principer för att begränsa tiden som användare har utanför säkerhetspositionen. Om du äger Azure AD P2 kan du använda åtkomstgranskningar för att automatisera processen

#### <a name="conditional-access-recommended-reading"></a>Villkorlig åtkomst rekommenderad läsning

- [Metodtips för villkorlig åtkomst i Azure Active Directory](../conditional-access/overview.md)
- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory för inställningar för villkorlig åtkomst](../conditional-access/concept-conditional-access-conditions.md)
- [Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Åtkomstyta

### <a name="legacy-authentication"></a>Äldre autentisering

Starka autentiseringsuppgifter som MFA kan inte skydda appar med äldre autentiseringsprotokoll, vilket gör det till den prioriterade attackvektorn av illvilliga aktörer. Att låsa äldre autentisering är avgörande för att förbättra åtkomstsäkerhetsstatusen.

Äldre autentisering är en term som refererar till autentiseringsprotokoll som används av appar som:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klient)
- Klienter som använder e-postprotokoll som IMAP/SMTP/POP

Angripare föredrar starkt dessa protokoll – i själva verket använder nästan [100 % av](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) lösenordsattackerna äldre autentiseringsprotokoll! Hackare använder äldre autentiseringsprotokoll eftersom de inte stöder interaktiv inloggning, vilket krävs för ytterligare säkerhetsutmaningar som multifaktorautentisering och enhetsautentisering.

Om äldre autentisering används ofta i din miljö bör du planera att migrera äldre klienter till klienter som stöder [modern autentisering](/office365/enterprise/modern-auth-for-office-2013-and-2016) så snart som möjligt. Om du har vissa användare som redan använder modern autentisering men andra som fortfarande använder äldre autentisering bör du vidta följande steg för att låsa äldre autentiseringsklienter i samma token:

1. Använd [inloggningsaktivitetsrapporter för](../reports-monitoring/concept-sign-ins.md) att identifiera användare som fortfarande använder äldre autentisering och planera reparation:

   a. Uppgradera till moderna autentiserings kompatibla klienter till berörda användare.
   
   b. Planera en tidsram för att låsa enligt stegen nedan.
   
   c. Identifiera vilka äldre program som är beroende av äldre autentisering. Se steg 3 nedan.

2. Inaktivera äldre protokoll vid källan (till exempel Exchange Mailbox) för användare som inte använder äldre autentisering för att undvika mer exponering.
3. För återstående konton (helst icke-mänskliga identiteter som tjänstkonton) använder du villkorlig åtkomst för att [begränsa äldre protokoll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) efter autentisering.

#### <a name="legacy-authentication-recommended-reading"></a>Äldre autentisering rekommenderad läsning

- [Aktivera eller inaktivera POP3- eller IMAP4-åtkomst till postlådor i Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Medgivande till bidrag

Vid en attack med otillåtet medgivande skapar angriparen ett Azure AD-registrerat program som begär åtkomst till data, till exempel kontaktinformation, e-post eller dokument. Användare kan bevilja medgivande till skadliga program via nätfiskeattacker vid landning på skadliga webbplatser.

Nedan visas en lista över appar med behörigheter som du kanske vill granska för Microsofts molntjänster:

- Appar med app eller delegerad \* . Lässkrivningsbehörigheter
- Appar med delegerad behörighet kan läsa, skicka eller hantera e-post för användarens räkning
- Appar som beviljas med följande behörigheter:

| Resurs | Behörighet |
| :- | :- |
| Exchange Online | Eas. AccessAsUser.All |
| | Ews. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- Appar beviljas fullständig användarpersonifiering för den inloggade användaren. Exempel:

|Resurs | Behörighet |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

För att undvika det här scenariot bör du referera till att identifiera och åtgärda otillåtna medgivanden i [Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) för att identifiera och åtgärda program med otillåtna bidrag eller program som har fler bidrag än vad som är nödvändigt. Ta sedan bort [självbetjäning helt och](../manage-apps/configure-user-consent.md) upprätta [styrningsprocedurer](../manage-apps/configure-admin-consent-workflow.md). Schemalägg slutligen regelbundna granskningar av appbehörigheter och ta bort dem när de inte behövs.

#### <a name="consent-grants-recommended-reading"></a>Medgivande beviljar rekommenderad läsning

- [Microsoft Graph API-behörigheter](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Användar- och gruppinställningar

Nedan visas de användar- och gruppinställningar som kan låsas om det inte finns ett uttryckligt affärs behov:

#### <a name="user-settings"></a>Användarinställningar

- **Externa användare** – externt samarbete kan ske etiskt i företaget med tjänster som Teams, Power BI, Sharepoint Online och Azure Information Protection. Om du har explicita begränsningar för att styra användarinitierade externa samarbeten rekommenderar vi att du aktiverar externa användare med hjälp av [Azure AD-rättighetshantering](../governance/entitlement-management-overview.md) eller en kontrollerad åtgärd, till exempel via supportavdelningen. Om du inte vill tillåta organisk externt samarbete för tjänster kan du blockera medlemmar från [att bjuda in externa användare helt](../external-identities/delegate-invitations.md). Du kan också tillåta eller [blockera specifika domäner i](../external-identities/allow-deny-list.md) externa användarinbjudningar.
- **Appregistreringar** – när Appregistreringar är aktiverade kan slutanvändarna själva registrera program och bevilja åtkomst till sina data. Ett vanligt exempel på appregistrering är användare som aktiverar Outlook-plugin-program eller röstassistenter som Alexa och Siri för att läsa sin e-post och kalender eller skicka e-post åt dem. Om kunden bestämmer sig för att stänga av appregistrering måste InfoSec- och IAM-teamen vara inblandade i hanteringen av undantag (appregistreringar som behövs baserat på affärskrav), eftersom de skulle behöva registrera programmen med ett administratörskonto och troligen kräva att en process utformas för att operationalisera processen.
- **Administrationsportalen** – organisationer kan låsa Azure AD-bladet i Azure Portal så att icke-administratörer inte kan komma åt Azure AD-hantering i Azure Portal och bli förvirrade. Gå till användarinställningarna i Azure AD-hanteringsportalen för att begränsa åtkomsten:

![Begränsad åtkomst till administrationsportalen](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Icke-administratörer kan fortfarande komma åt Azure AD-hanteringsgränssnitten via kommandoraden och andra programmatiska gränssnitt.

#### <a name="group-settings"></a>Gruppinställningar

**Grupphantering med självbetjäning/användare kan skapa säkerhetsgrupper/Microsoft 365 grupper.** Om det inte finns något aktuellt självbetjäninginitiativ för grupper i molnet kan kunderna välja att inaktivera det tills de är redo att använda den här funktionen.

#### <a name="groups-recommended-reading"></a>Grupper som rekommenderas att läsa

- [Vad är Azure Active Directory B2B-samarbete?](../external-identities/what-is-b2b.md)
- [Integrera program med Azure Active Directory](../develop/quickstart-register-app.md)
- [Appar, behörigheter och medgivande i Azure Active Directory.](../develop/quickstart-register-app.md)
- [Använd grupper för att hantera åtkomst till resurser i Azure Active Directory](./active-directory-manage-groups.md)
- [Konfigurera åtkomsthantering för självbetjäningsprogram i Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Trafik från oväntade platser

Angripare kommer från olika delar av världen. Hantera den här risken genom att använda principer för villkorlig åtkomst med plats som villkor. [Platsvillkoret](../conditional-access/location-condition.md) för en princip för villkorlig åtkomst gör att du kan blockera åtkomst för platser där det inte finns någon affärsorsak att logga in från.

![Skapa en ny namngiven plats](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Om det är tillgängligt använder du en LÖSNING för säkerhetsinformation och händelsehantering (SIEM) för att analysera och hitta åtkomstmönster mellan regioner. Om du inte använder en SIEM-produkt, eller om den inte matar in autentiseringsinformation från Azure AD, rekommenderar vi att du använder Azure Monitor för att identifiera åtkomstmönster mellan regioner. [](../../azure-monitor/overview.md)

## <a name="access-usage"></a>Åtkomstanvändning

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-loggar arkiverade och integrerade med incidentsvarsplaner

Att ha åtkomst till inloggningsaktivitet, granskningar och riskhändelser för Azure AD är avgörande för felsökning, användningsanalys och kriminaltekniska undersökningar. Azure AD ger åtkomst till dessa källor via REST-API:er som har en begränsad kvarhållningsperiod. Ett SIEM-system (Säkerhetsinformation och händelsehantering) eller motsvarande arkiveringsteknik är nyckeln till långsiktig lagring av granskningar och support. Om du vill aktivera långsiktig lagring av Azure AD-loggar måste du antingen lägga till dem i din befintliga SIEM-lösning eller [använda Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Arkivera loggar som kan användas som en del av dina planer och utredningar för incidenter.

#### <a name="logs-recommended-reading"></a>Loggar rekommenderad läsning

- [Azure Active Directory api-referens för granskning](/graph/api/resources/directoryaudit)
- [AZURE ACTIVE DIRECTORY API-referens för inloggningsaktivitetsrapport](/graph/api/resources/signin)
- [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph för Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Api-referens för Office 365 Management-aktivitet](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Så här använder du innehållspaketet för Azure Active Directory Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Sammanfattning

Det finns 12 aspekter av en säker identitetsinfrastruktur. Den här listan hjälper dig att ytterligare skydda och hantera autentiseringsuppgifter, definiera autentiseringsupplevelse, delegera tilldelning, mäta användning och definiera åtkomstprinciper baserat på företagets säkerhetsstatus.

- Tilldela ägare till viktiga uppgifter.
- Implementera lösningar för att identifiera svaga eller läckta lösenord, förbättra lösenordshanteringen och skyddet och ytterligare skydda användaråtkomsten till resurser.
- Hantera identiteten för enheter för att skydda dina resurser när som helst och från valfri plats.
- Implementera lösenordsfri autentisering.
- Tillhandahålla en standardiserad mekanism för enkel inloggning i hela organisationen.
- Migrera appar från AD FS till Azure AD för att få bättre säkerhet och mer konsekvent hanterbarhet.
- Tilldela användare till program med hjälp av grupper för att ge större flexibilitet och möjlighet att hantera i stor skala.
- Konfigurera riskbaserade åtkomstprinciper.
- Lås äldre autentiseringsprotokoll.
- Identifiera och åtgärda otillåtna medgivandesmedgivanden.
- Lås användar- och gruppinställningar.
- Aktivera långsiktig lagring av Azure AD-loggar för felsökning, användningsanalys och kriminaltekniska undersökningar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [driftkontroller och åtgärder för identitetsstyrning.](active-directory-ops-guide-govern.md)
