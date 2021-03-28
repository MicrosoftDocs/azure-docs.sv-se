---
title: Referens guide för Azure Active Directory hanterings åtgärder för autentisering
description: Den här åtgärds hand boken beskriver de kontroller och åtgärder som du bör vidta för att skydda autentiseringen
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
ms.openlocfilehash: 0d425111e151d60ab38a60e38cd1805cef39f606
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641766"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Referens guide för Azure Active Directory hanterings åtgärder för autentisering

Det här avsnittet i [hand boken för Azure AD-åtgärder](active-directory-ops-guide-intro.md) beskriver de kontroller och åtgärder som du bör vidta för att skydda och hantera autentiseringsuppgifter, definiera autentisering, delegera tilldelning, mäta användning och definiera åtkomst principer baserat på position för företags säkerhet.

> [!NOTE]
> Dessa rekommendationer är aktuella vid publicerings datumet, men kan ändras med tiden. Organisationer bör kontinuerligt utvärdera sina identitets metoder när Microsofts produkter och tjänster utvecklas med tiden.

## <a name="key-operational-processes"></a>Viktiga operativa processer

### <a name="assign-owners-to-key-tasks"></a>Tilldela ägare till viktiga uppgifter

Hantering av Azure Active Directory kräver kontinuerlig körning av viktiga operativa uppgifter och processer, som kanske inte ingår i ett lanserings projekt. Det är fortfarande viktigt att du konfigurerar dessa uppgifter för att optimera din miljö. De viktigaste uppgifterna och deras rekommenderade ägare är:

| Uppgift | Ägare |
| :- | :- |
| Hantera konfiguration av enkel inloggning (SSO) i Azure AD | IAM-åtgärds team |
| Design principer för villkorlig åtkomst för Azure AD-program | Arkitektur team för informations säkerhet |
| Arkivera inloggnings aktivitet i ett SIEM-system | Åtgärds team för informations säkerhet |
| Arkivera risk händelser i ett SIEM-system | Åtgärds team för informations säkerhet |
| Prioritering och undersök säkerhets rapporter | Åtgärds team för informations säkerhet |
| Prioritering och undersök risk händelser | Åtgärds team för informations säkerhet |
| Prioritering och undersök användare som har flaggats för risk-och sårbarhets rapporter från Azure AD Identity Protection | Åtgärds team för informations säkerhet |

> [!NOTE]
> Azure AD Identity Protection kräver en Azure AD Premium P2-licens. Information om rätt licens för dina krav finns i [jämföra allmänt tillgängliga funktioner i Azure AD Free-och Azure AD Premium-versionerna](https://azure.microsoft.com/pricing/details/active-directory/).

När du granskar listan kanske du måste tilldela en ägare för aktiviteter som saknar ägare eller justera ägarskapet för aktiviteter med ägare som inte är justerade enligt rekommendationerna ovan.

#### <a name="owner-recommended-reading"></a>Rekommenderad läsning av ägare

- [Tilldela administratörsroller i Azure Active Directory](../roles/permissions-reference.md)
- [Styrning i Azure](../../governance/index.yml)

## <a name="credentials-management"></a>Hantering av autentiseringsuppgifter

### <a name="password-policies"></a>Lösenordsprinciper

Att hantera lösen ord säkert är en av de viktigaste delarna av identitets-och åtkomst hantering och ofta det största målet för angrepp. Azure AD stöder flera funktioner som kan hjälpa till att förhindra att en attack lyckas.

Använd tabellen nedan för att hitta den rekommenderade lösningen för att åtgärda problemet som måste åtgärdas:

| Problem | Rekommendation |
| :- | :- |
| Ingen mekanism för att skydda mot svaga lösen ord | Aktivera återställning av [lösen ord för självbetjäning i Azure AD (SSPR)](../authentication/concept-sspr-howitworks.md) och [lösen ords skydd](../authentication/concept-password-ban-bad-on-premises.md) |
| Ingen mekanism för att identifiera läckta lösen ord | Aktivera PHS ( [Password hash Sync](../hybrid/how-to-connect-password-hash-synchronization.md) ) för att få insikter |
| Använda AD FS och inte flytta till hanterad autentisering | Aktivera [AD FS extra näts Smart utelåsning](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) och/eller [Azure AD Smart utelåsning](../authentication/howto-password-smart-lockout.md) |
| Lösen ords principen använder komplexitets regler som längd, flera teckenuppsättningar eller förfallo datum | Fundera över fördelarna med [Microsofts rekommenderade metoder](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) och växla din metod till lösen ords hantering och distribuera [Azure AD Password Protection](../authentication/concept-password-ban-bad.md). |
| Användare har inte registrerats för användning av Multi-Factor Authentication (MFA) | [Registrera all användares säkerhets information](../identity-protection/howto-identity-protection-configure-mfa-policy.md) så att den kan användas som en mekanism för att verifiera användarens identitet tillsammans med lösen ordet |
| Det finns inget återkallande av lösen ord baserat på användar risk | Distribuera [användar risk principer](../identity-protection/howto-identity-protection-configure-risk-policies.md) i Azure AD Identity Protection för att framtvinga lösen ords ändringar för läckta AUTENTISERINGSUPPGIFTER med SSPR |
| Det finns ingen Smart utelåsnings funktion för att skydda skadlig autentisering från felaktiga aktörer som kommer från identifierade IP-adresser | Distribuera molnbaserad autentisering med antingen Password-hash-synkronisering eller [direktautentisering](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Lösen ords principer rekommenderas att läsa

- [Azure AD och AD FS bästa praxis: försvara mot angrepp vid lösen ords attacker – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Aktivera självbetjäning för återställning av lösen ord och lösen ords skydd

Användare som behöver ändra eller återställa sina lösen ord är en av de största volym källorna och kostnader för support samtal. Förutom kostnaden är det grundläggande att ändra lösen ordet som ett verktyg för att minimera en användar risk är ett grundläggande steg i att förbättra din organisations säkerhets position.

Vi rekommenderar minst att du distribuerar Azure AD självbetjäning för [återställning av lösen ord](../authentication/concept-sspr-howitworks.md) (SSPR) och lokalt [lösen ords skydd](../authentication/howto-password-ban-bad-on-premises-deploy.md) för att utföra:

- Avhjälpa support samtal.
- Ersätt användningen av tillfälliga lösen ord.
- Ersätt eventuella befintliga självbetjänings lösningar för lösen ords hantering som förlitar sig på en lokal lösning.
- [Eliminera svaga lösen ord](../authentication/concept-password-ban-bad.md) i din organisation.

> [!NOTE]
> För organisationer med en Azure AD Premium P2-prenumeration rekommenderar vi att du distribuerar SSPR och använder den som en del av en [identitets skydds princip för användar risker](../identity-protection/howto-identity-protection-configure-risk-policies.md).

### <a name="strong-credential-management"></a>Hantering av starka autentiseringsuppgifter

Lösen orden själva är inte tillräckligt säkra för att förhindra att dåliga aktörer får åtkomst till din miljö. Som minst måste alla användare med ett privilegierat konto aktive ras för Multi-Factor Authentication (MFA). Vi rekommenderar att du aktiverar [kombinerad registrering](../authentication/concept-registration-mfa-sspr-combined.md) och kräver att alla användare registrerar sig för MFA och SSPR med hjälp av den [kombinerade registrerings upplevelsen](../user-help/security-info-setup-signin.md). Slutligen rekommenderar vi att du använder en strategi för att [ge återhämtning](../authentication/concept-resilient-controls.md) för att minska risken för utelåsning på grund av oförutsedda omständigheter.

![Kombinerat användar upplevelse flöde](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Återhämtnings förmåga för autentisering på lokalt avbrott

Förutom fördelarna med enkelhet och aktivering av läckta autentiseringsuppgifter för identifiering av autentiseringsuppgifter kan Azure AD-PHS (Password hash Sync) och Azure AD MFA tillåta användare att komma åt SaaS-program och Microsoft 365 trots lokala avbrott på grund av cyberattacker, till exempel [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Det är också möjligt att aktivera PHS i samband med Federation. Genom att aktivera PHS får du en återställning av autentisering när Federations tjänsterna inte är tillgängliga.

Om din lokala organisation saknar återhämtnings strategi för avbrott eller har en som inte är integrerad med Azure AD, bör du Distribuera Azure AD PHS och definiera en katastrof återställnings plan som innehåller PHS. Genom att aktivera Azure AD PHS kan användare autentisera sig mot Azure AD om din lokala Active Directory inte är tillgänglig.

![flöde för synkronisering av lösen ords hash](./media/active-directory-ops-guide/active-directory-ops-img5.png)

För att bättre förstå dina autentiseringsalternativ, se [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../hybrid/choose-ad-authn.md).

### <a name="programmatic-usage-of-credentials"></a>Användning av autentiseringsuppgifter för program mässig användning

Azure AD-skript med hjälp av PowerShell eller program som använder Microsoft Graph API kräver säker autentisering. Dålig hantering av autentiseringsuppgifter som kör dessa skript och verktyg ökar risken för stöld av autentiseringsuppgifter. Om du använder skript eller program som förlitar sig på hårdkodade lösen ord eller lösen ord, bör du först granska lösen ord i konfigurationsfiler eller käll koden, sedan ersätta dessa beroenden och använda Azure Managed Identities, Integrated-Windows autentisering eller [certifikat](../reports-monitoring/tutorial-access-api-with-certificates.md) närhelst det är möjligt. Överväg att använda [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)för program där tidigare lösningar inte är möjliga.

Om du fastställer att det finns tjänstens huvud namn med autentiseringsuppgifter för lösen ord och du är osäker på hur lösen ords uppgifterna skyddas av skript eller program, kan du kontakta programmets ägare för att bättre förstå användnings mönster.

Microsoft rekommenderar också att du kontaktar program ägare för att förstå användnings mönster om det finns tjänstens huvud namn med autentiseringsuppgifter för lösen ord.

## <a name="authentication-experience"></a>Autentisering

### <a name="on-premises-authentication"></a>Lokal autentisering

Federerad autentisering med integrerad Windows-autentisering (IWA) eller sömlös enkel Sign-On-hanterad autentisering med lösen ords-hash-synkronisering eller direktautentisering är den bästa användar upplevelsen i företags nätverket med detaljerad information till lokala domänkontrollanter. Den minimerar risken för autentiseringsuppgifter för att minska risken för att användare som faller Prey till nätfiske-attacker minskar. Om du redan använder molnbaserad autentisering med PHS eller PTA, men användarna fortfarande behöver ange sina lösen ord vid autentisering lokalt, bör du omedelbart [distribuera sömlös SSO](../hybrid/how-to-connect-sso.md). Å andra sidan bör du implementera sömlös SSO som en del av migreringsjobbet om du för närvarande är federerad med planer för att till sist migrera till molnbaserad autentisering.

### <a name="device-trust-access-policies"></a>Åtkomst principer för enhets förtroende

Som en användare i din organisation är en enhet en kärn identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när du vill och var som helst. Att autentisera enheten och redovisningen för dess förtroende typ ökar din säkerhets position och användbarhet genom att:

- Undvika friktion, till exempel med MFA, när enheten är betrodd
- Blockera åtkomst från ej betrodda enheter
- För Windows 10-enheter ger [enkel inloggning till lokala resurser sömlöst](../devices/azuread-join-sso.md).

Du kan utföra det här målet genom att sätta enhets identiteter och hantera dem i Azure AD genom att använda någon av följande metoder:

- Organisationer kan använda [Microsoft Intune](/intune/what-is-intune) för att hantera enheten och genomdriva efterlevnadsprinciper, intyga enhetens hälsa och ange principer för villkorlig åtkomst baserat på om enheten är kompatibel. Microsoft Intune kan hantera iOS-enheter, Mac-datorer (via JAMF-integrering), Windows-skrivbord (internt med hantering av mobila enheter för Windows 10 och samhantering med Microsoft Endpoint Configuration Manager) och mobila Android-enheter.
- [Hybrid Azure AD-anslutning](../devices/hybrid-azuread-join-managed-domains.md) ger hantering med grup principer eller Microsoft Endpoint Configuration Manager i en miljö med Active Directory domänanslutna dator enheter. Organisationer kan distribuera en hanterad miljö antingen via PHS eller PTA med sömlös SSO. Genom att ta med dina enheter till Azure AD kan du maximera användar produktiviteten via SSO i molnet och lokala resurser samtidigt som du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../conditional-access/overview.md) på samma gång.

Om du har domänanslutna Windows-enheter som inte är registrerade i molnet, eller domänanslutna Windows-enheter som är registrerade i molnet men utan principer för villkorlig åtkomst, bör du registrera de oregistrerade enheterna och i båda fallen [använda hybrid Azure AD Join som en kontroll](../conditional-access/require-managed-devices.md) i dina principer för villkorlig åtkomst.

![En skärm bild av bidraget i principen för villkorlig åtkomst som kräver hybrid enhet](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Om du hanterar enheter med MDM eller Microsoft Intune, men inte använder enhets kontroller i dina principer för villkorlig åtkomst, rekommenderar vi att du använder [Kräv att enheten är markerad som kompatibel](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) som en kontroll i dessa principer.

![En skärm bild av bidraget i principen för villkorlig åtkomst som kräver enhetens efterlevnad](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Åtkomst principer för enhets förtroende rekommenderas läsning

- [Gör så här: planera din hybrid Azure Active Directory delta-implementering](../devices/hybrid-azuread-join-plan.md)
- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello för företag

I Windows 10 ersätter [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-identity-verification) lösen ord med stark tvåfaktorautentisering på datorer. Windows Hello för företag ger en mer strömlinjeformad MFA-upplevelse för användare och minskar beroendet av lösen ord. Om du inte har börjat installera Windows 10-enheter eller bara har delvis distribuerat dem, rekommenderar vi att du uppgraderar till Windows 10 och [aktiverar Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) på alla enheter.

Om du vill veta mer om lösen ords lös autentisering kan du läsa mer om lösen ords lös autentisering i [en värld utan lösen ord med Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Programautentisering och tilldelning

### <a name="single-sign-on-for-apps"></a>Enkel inloggning för appar

Att tillhandahålla en standardiserad mekanism för enkel inloggning till hela företaget är avgörande för bästa användar upplevelse, minskning av risk, rapportering och styrning. Om du använder program som har stöd för enkel inloggning med Azure AD men som för närvarande är konfigurerade för att använda lokala konton, bör du konfigurera om dessa program så att de använder SSO med Azure AD. Om du använder program som har stöd för enkel inloggning med Azure AD men använder en annan identitetsprovider, bör du även konfigurera om dessa program så att de använder enkel inloggning med Azure AD. För program som inte stöder Federations protokoll, men som stöder formulärbaserad autentisering, rekommenderar vi att du konfigurerar programmet att använda [lösen ords valv](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) med Azure AD-programproxy.

![AppProxy-baserad inloggning](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Om du inte har en mekanism för att identifiera ohanterade program i din organisation rekommenderar vi att du implementerar en identifierings process med hjälp av en lösning för CASB (Cloud Access Security Broker) som [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Slutligen, om du har ett Azure AD App-galleri och använder program som har stöd för enkel inloggning med Azure AD, rekommenderar vi [att du visar programmet i app-galleriet](../develop/v2-howto-app-gallery-listing.md).

#### <a name="single-sign-on-recommended-reading"></a>Enkel inloggning, Rekommenderad läsning

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migrering av AD FS program till Azure AD

[Migrering av appar från AD FS till Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) ger ytterligare funktioner för säkerhet, mer konsekvent hanterbarhet och en bättre samarbets upplevelse. Om du har program som kon figurer ATS i AD FS som stöder SSO med Azure AD bör du konfigurera om dessa program så att de använder SSO med Azure AD. Om du har program som kon figurer ATS i AD FS med ovanliga konfigurationer som inte stöds av Azure AD, bör du kontakta appens ägare för att ta reda på om den särskilda konfigurationen är ett absolut krav för programmet. Om det inte behövs bör du konfigurera om programmet så att det använder SSO med Azure AD.

![Azure AD som primär identitets leverantör](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health för ADFS](../hybrid/how-to-connect-health-adfs.md) kan användas för att samla in konfigurations information om varje program som eventuellt kan migreras till Azure AD.

### <a name="assign-users-to-applications"></a>Tilldela användare till program

[Att tilldela användare till program](../manage-apps/assign-user-or-group-access-portal.md) mappas bäst med hjälp av grupper eftersom de ger större flexibilitet och möjlighet att hantera i hög skala. Fördelarna med att använda grupper är [attribut-baserat dynamiskt grupp medlemskap](../enterprise-users/groups-dynamic-membership.md) och [delegering till app-ägare](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Om du redan använder och hanterar grupper rekommenderar vi därför att du vidtar följande åtgärder för att förbättra hanteringen i skala:

- Delegera grupp hantering och styrning till program ägare.
- Tillåt självbetjänings åtkomst till programmet.
- Definiera dynamiska grupper om användarattribut kan konsekvent bestämma åtkomst till program.
- Implementera attestering till grupper som används för program åtkomst med hjälp av [åtkomst granskningar i Azure AD](../governance/access-reviews-overview.md).

Om du däremot hittar program som har tilldelning till enskilda användare ska du se till att implementera [styrningar](../governance/index.yml) runt dessa program.

#### <a name="assign-users-to-applications-recommended-reading"></a>Tilldela användare till program som rekommenderas läsning

- [Tilldela användare och grupper till ett program i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Delegera registrerings behörigheter för app i Azure Active Directory](../roles/delegate-app-roles.md)
- [Regler för dynamiskt medlemskap för grupper i Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Åtkomstprinciper

### <a name="named-locations"></a>Namngivna platser

Med [namngivna platser](../reports-monitoring/quickstart-configure-named-locations.md) i Azure AD kan du märka betrodda IP-adressintervall i din organisation. Azure AD använder namngivna platser för att:

- Förhindra falska positiva identifieringar i risk händelser. Om du loggar in från en betrodd nätverks plats sänker du en användares inloggnings risk.
- Konfigurera [plats baserad villkorlig åtkomst](../reports-monitoring/quickstart-configure-named-locations.md).

![Namngivna platser](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Baserat på prioritet använder du tabellen nedan för att hitta den rekommenderade lösning som bäst uppfyller organisationens behov:

| **Priority** | **Scenario** | **Rekommendation** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Om du använder PHS eller PTA och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 2 | Om du är federerad och inte använder "insideCorporateNetwork"-anspråk och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 3 | Om du inte använder namngivna platser i principer för villkorlig åtkomst och det inte finns några risk-eller enhets kontroller i principer för villkorlig åtkomst | Konfigurera principen för villkorlig åtkomst så att den innehåller namngivna platser |
| 4 | Om du är federerad och använder "insideCorporateNetwork"-anspråk och namngivna platser inte har definierats | Definiera namngivna platser för att förbättra identifieringen av risk händelser |
| 5 | Om du använder betrodda IP-adresser med MFA i stället för namngivna platser och markerar dem som betrodda | Definiera namngivna platser och markera dem som betrodda för att förbättra identifieringen av risk händelser |

### <a name="risk-based-access-policies"></a>Riskfyllda åtkomst principer

Azure AD kan beräkna risken för varje inloggning och varje användare. Att använda risk som ett villkor i åtkomst principer kan ge en bättre användar upplevelse, till exempel färre autentiserings-prompter och bättre säkerhet, till exempel endast fråga användare när de behövs och automatisera svar och reparationer.

![Princip för inloggningsrisk](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Om du redan äger Azure AD Premium P2-licenser som stöder användning av risk i åtkomst principer, men inte används, rekommenderar vi starkt att du lägger till risker för din säkerhets position.

#### <a name="risk-based-access-policies-recommended-reading"></a>Vid riskfyllda åtkomst principer rekommenderas läsning

- [Gör så här: Konfigurera inloggnings risk principen](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Gör så här: konfigurera användar risk principen](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Åtkomst principer för klient program

Microsoft Intune Application Management (MAM) ger möjlighet att skicka data skydds kontroller, till exempel lagrings kryptering, PIN-kod, rensning av Fjärrlagring osv. till kompatibla klient mobil program som Outlook Mobile. Dessutom kan principer för villkorlig åtkomst skapas för att [begränsa åtkomsten](../conditional-access/app-based-conditional-access.md) till moln tjänster, till exempel Exchange Online från godkända eller kompatibla appar.

Om dina anställda installerar MAM program som Office-mobilappar för att komma åt företagets resurser, till exempel Exchange Online eller SharePoint Online, och du också stöder BYOD (ta med din egen enhet) rekommenderar vi att du distribuerar program MAM-principer för att hantera program konfigurationen i personligt ägda enheter utan MDM-registrering och sedan uppdatera dina principer för villkorlig åtkomst för att endast tillåta åtkomst från MAM-kompatibla klienter.

![Kontroll av villkorlig åtkomst beviljande](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Om de anställda ska kunna installera MAM program mot företags resurser och åtkomsten är begränsad på Intune-hanterade enheter, bör du överväga att distribuera MAM-principer för att hantera program konfigurationen för personliga enheter och uppdatera principer för villkorlig åtkomst för att endast tillåta åtkomst från MAM-kompatibla klienter.

### <a name="conditional-access-implementation"></a>Implementering av villkorlig åtkomst

Villkorlig åtkomst är ett viktigt verktyg för att förbättra din organisations säkerhets position. Därför är det viktigt att du följer dessa metod tips:

- Se till att alla SaaS-program har minst en princip tillämpad
- Undvik att kombinera filtret **alla appar** med **block** kontrollen för att undvika utelåsnings risk
- Undvik att använda **alla användare** som ett filter och oavsiktligt lägga till **gäster**
- **Migrera alla äldre principer till Azure Portal**
- Fånga alla kriterier för användare, enheter och program
- Använd principer för villkorlig åtkomst för att [implementera MFA](../conditional-access/plan-conditional-access.md)i stället för att använda **MFA per användare**
- Ha en liten uppsättning kärn principer som kan tillämpas på flera program
- Definiera tomma undantags grupper och Lägg till dem i principerna för att få en undantags strategi
- Planera för [Bryt glas](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) konton utan MFA-kontroller
- Få en konsekvent upplevelse för Microsoft 365 klient program, till exempel team, OneDrive, Outlook osv.) genom att implementera samma uppsättning kontroller för tjänster som Exchange Online och SharePoint Online
- Tilldelning till principer bör implementeras genom grupper, inte individer
- Gör regelbundna granskningar av undantags grupperna som används i principer för att begränsa den tid som användarna ligger utanför säkerhets position. Om du äger Azure AD P2 kan du använda åtkomst granskningar för att automatisera processen

#### <a name="conditional-access-recommended-reading"></a>Rekommenderad läsning för villkorlig åtkomst

- [Metod tips för villkorlig åtkomst i Azure Active Directory](../conditional-access/overview.md)
- [Konfigurationer för identitets- och enhetsåtkomst](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Referens för Azure Active Directory villkorlig åtkomst inställningar](../conditional-access/concept-conditional-access-conditions.md)
- [Vanliga principer för villkorlig åtkomst](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Åtkomst till Surface-området

### <a name="legacy-authentication"></a>Äldre autentisering

Starka autentiseringsuppgifter, till exempel MFA, kan inte skydda appar med äldre autentiseringsprotokoll, vilket gör det till den önskade angrepps vektorn från skadliga aktörer. Att låsa äldre autentisering är avgörande för att förbättra åtkomst säkerhets position.

Äldre autentisering är en term som refererar till autentiseringsprotokoll som används av appar som:

- Äldre Office-klienter som inte använder modern autentisering (till exempel Office 2010-klient)
- Klienter som använder e-postprotokoll som IMAP/SMTP/POP

Angripare föredrar dessa protokoll – i själva verket är det nästan [100% av](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) angrepps behållarna för att använda bakåtkompatibla autentiseringsprotokoll! Hackare använder äldre autentiseringsprotokoll, eftersom de inte stöder interaktiv inloggning, vilket krävs för ytterligare säkerhets utmaningar som multifaktorautentisering och enhetsautentisering.

Om äldre autentisering ofta används i din miljö bör du planera att migrera äldre klienter till klienter som stöder [modern autentisering](/office365/enterprise/modern-auth-for-office-2013-and-2016) så snart som möjligt. Om du har vissa användare som redan använder modern autentisering i samma token, men andra som fortfarande använder äldre autentisering, bör du vidta följande steg för att låsa äldre autentiserings klienter:

1. Använd [rapporter om inloggnings aktiviteter](../reports-monitoring/concept-sign-ins.md) för att identifiera användare som fortfarande använder tidigare autentisering och prenumerations reparationer:

   a. Uppgradera till moderna klienter som kan påverka användare.
   
   b. Planera en start punkt-tidsram för att låsa upp enligt stegen nedan.
   
   c. Identifiera vilka äldre program som har ett hårt beroende av tidigare autentisering. Se steg 3 nedan.

2. Inaktivera äldre protokoll på källan (till exempel Exchange-postlåda) för användare som inte använder äldre autentisering för att undvika mer exponering.
3. För de återstående kontona (helst icke-mänskliga identiteter som tjänst konton) använder [du villkorlig åtkomst för att begränsa äldre protokoll](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) efter autentisering.

#### <a name="legacy-authentication-recommended-reading"></a>Tidigare autentisering, Rekommenderad läsning

- [Aktivera eller inaktivera POP3-eller IMAP4-åtkomst till post lådor i Exchange Server](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Medgivande bidrag

Vid en olaglig godkännande attack skapar angriparen ett Azure AD-registrerat program som begär åtkomst till data, till exempel kontakt information, e-post eller dokument. Användare kan beviljas medgivande till skadliga program via nätfiske-attacker vid landning på skadliga webbplatser.

Nedan visas en lista över appar med behörigheter som du kanske vill granska för Microsofts moln tjänster:

- Appar med app eller delegerad \* . ReadWrite-behörigheter
- Appar med delegerade behörigheter kan läsa, skicka eller hantera e-post för användarens räkning
- Appar som beviljas med följande behörigheter:

| Resurs | Behörighet |
| :- | :- |
| Exchange Online | EA. AccessAsUser. all |
| | EWS. AccessAsUser. all |
| | E-post. Read |
| Microsoft Graph API | E-post. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Appar har beviljat fullständig användar personifiering av den inloggade användaren. Exempel:

|Resurs | Behörighet |
| :- | :- |
| Microsoft Graph API| Directory. AccessAsUser. all |
| Azure REST API | user_impersonation |

För att undvika det här scenariot bör du läsa om att [identifiera och åtgärda illegala medgivande i Office 365](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) för att identifiera och åtgärda alla program med illegala bidrag eller program som har fler bidrag än vad som behövs. Ta sedan bort självbetjäningen [helt och hållet](../manage-apps/configure-user-consent.md) och [upprätta styrnings procedurer](../manage-apps/configure-admin-consent-workflow.md). Slutligen kan du schemalägga regelbundna granskningar av app-behörigheter och ta bort dem när de inte behövs.

#### <a name="consent-grants-recommended-reading"></a>Medgivande ger Rekommenderad läsning

- [Microsoft Graph API-behörigheter](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Användar-och grupp inställningar

Nedan visas de användar-och grupp inställningar som kan låsas ned om det inte finns något behov av affärs behov:

#### <a name="user-settings"></a>Användarinställningar

- **Externa användare** – externt samarbete kan ske ekologiskt i företaget med tjänster som team, Power BI, SharePoint Online och Azure information Protection. Om du har uttryckliga begränsningar för att styra det externa samarbetet som initieras av användaren, rekommenderar vi att du aktiverar externa användare med hjälp av [hantering av Azure AD-rättighet](../governance/entitlement-management-overview.md) eller en kontrollerad åtgärd, till exempel genom supportavdelningen. Om du inte vill tillåta ekologiskt externt samarbete för tjänster kan du [blockera medlemmar från att bjuda in externa användare fullständigt](../external-identities/delegate-invitations.md). Du kan också [tillåta eller blockera vissa domäner](../external-identities/allow-deny-list.md) i externa användar inbjudningar.
- **App-registreringar** – när Appregistreringar är aktiverade kan slutanvändare själva publicera program och ge åtkomst till sina data. Ett typiskt exempel på registrering av appar är användare som aktiverar Outlook-plugin-program eller röst assistenter som Alexa och Siri för att läsa e-post och kalender eller skicka e-post åt dig. Om kunden bestämmer sig för att inaktivera app-registrering, måste informations-och IAM-teamen ingå i hanteringen av undantag (app-registreringar som behövs baserat på affärs krav), eftersom de skulle behöva registrera programmen med ett administratörs konto, och de flesta sannolikt behöver utforma en process för att operationalisera processen.
- **Administrations portalen** – organisationer kan låsa Azure AD-bladet i Azure Portal så att icke-administratörer inte kan komma åt Azure AD-hanteringen i Azure Portal och få förvirrande. Begränsa åtkomsten genom att gå till användar inställningarna i hanterings portalen för Azure AD:

![Begränsad åtkomst till administrations Portal](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Icke-administratörer kan fortfarande komma åt Azure AD-hanterings gränssnitt via kommando rads program och andra programmerings gränssnitt.

#### <a name="group-settings"></a>Gruppinställningar

**Grupp hantering via självbetjäning/användare kan skapa säkerhets grupper/Microsoft 365 grupper.** Om det inte finns något aktuellt självbetjänings initiativ för grupper i molnet kan kunderna välja att inaktivera det tills de är redo att använda den här funktionen.

#### <a name="groups-recommended-reading"></a>Grupper som rekommenderar läsning

- [Vad är Azure Active Directory B2B-samarbete?](../external-identities/what-is-b2b.md)
- [Integrera program med Azure Active Directory](../develop/quickstart-register-app.md)
- [Appar, behörigheter och medgivande i Azure Active Directory.](../develop/quickstart-register-app.md)
- [Använd grupper för att hantera åtkomst till resurser i Azure Active Directory](./active-directory-manage-groups.md)
- [Konfigurera åtkomst hantering för självbetjänings program i Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Trafik från oväntade platser

Angripare kommer från olika delar av världen. Hantera den här risken genom att använda principer för villkorlig åtkomst med plats som villkor. [Plats villkoret](../conditional-access/location-condition.md) för en princip för villkorlig åtkomst gör att du kan blockera åtkomst för platser där det inte finns något affärs skäl att logga in från.

![Skapa en ny namngiven plats](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Om det är tillgängligt använder du en SIEM-lösning (Security information and Event Management) för att analysera och hitta mönster för åtkomst över flera regioner. Om du inte använder en SIEM-produkt, eller om du inte matar in autentiseringsinformation från Azure AD, rekommenderar vi att du använder [Azure Monitor](../../azure-monitor/overview.md) för att identifiera åtkomst mönster över flera regioner.

## <a name="access-usage"></a>Åtkomst användning

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-loggar har arkiverats och integrerats med incident svars planer

För att få till gång till inloggnings aktivitet är granskningar och risk händelser för Azure AD avgörande för fel sökning, användnings analys och data utredning undersökningar. Azure AD ger åtkomst till dessa källor via REST-API: er som har en begränsad kvarhållningsperiod. Ett system för säkerhets informations-och händelse hantering (SIEM), eller motsvarande lagrings teknik, är nyckeln för långsiktig lagring av revisioner och support. Om du vill aktivera långsiktig lagring av Azure AD-loggar måste du antingen lägga till dem i din befintliga SIEM-lösning eller använda [Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md). Arkivera loggar som kan användas som en del av dina incident svars planer och undersökningar.

#### <a name="logs-recommended-reading"></a>Loggar Rekommenderad läsning

- [Azure Active Directory gransknings-API-referens](/graph/api/resources/directoryaudit?view=graph-rest-beta)
- [Rapport API-referens för Azure Active Directory inloggnings aktivitet](/graph/api/resources/signin?view=graph-rest-beta)
- [Hämta data med hjälp av Azure AD Reporting-API:et med certifikat](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph för Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [API-referens för Office 365 Management Activity](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Så här använder du innehållspaketet för Azure Active Directory Power BI](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Sammanfattning

Det finns 12 aspekter till en säker identitets infrastruktur. Med den här listan kan du skydda och hantera autentiseringsuppgifter ytterligare, definiera autentisering, delegera tilldelning, mäta användning och definiera åtkomst principer baserat på position för företags säkerhet.

- Tilldela ägare till viktiga uppgifter.
- Implementera lösningar för att identifiera svaga eller läcker lösen ord, förbättra lösen ords hantering och skydd och ytterligare säkra användar åtkomst till resurser.
- Hantera identiteten för enheter för att skydda dina resurser när som helst och var som helst.
- Implementera lösenordsbaserad autentisering.
- Tillhandahålla en standardiserad mekanism för enkel inloggning i hela organisationen.
- Migrera appar från AD FS till Azure AD för att ge bättre säkerhet och mer konsekvent hanterbarhet.
- Tilldela användare till program med hjälp av grupper för att ge större flexibilitet och möjlighet att hantera i stor skala.
- Konfigurera riskfyllda åtkomst principer.
- Lås bakåtkompatibla autentiseringsprotokoll.
- Identifiera och åtgärda illegala medgivande bidrag.
- Lås användar-och grupp inställningar.
- Aktivera långsiktig lagring av Azure AD-loggar för fel sökning, användnings analys och data utredning undersökningar.

## <a name="next-steps"></a>Nästa steg

Kom igång med [identitets styrningens operativa kontroller och åtgärder](active-directory-ops-guide-govern.md).
