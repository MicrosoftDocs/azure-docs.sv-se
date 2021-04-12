---
title: Svara snabbt på säkra identiteter med Azure Active Directory
description: Svara snabbt på hot med molnbaserade identiteter i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836690"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Svara säkra identiteter snabbt med Azure AD

Det kan verka som avskräckande försöker skydda dina arbetare i dagens värld, särskilt när du behöver svara snabbt och ge åtkomst till många tjänster snabbt. Den här artikeln är avsedd att ge en kortfattad lista över alla åtgärder som ska vidtas, vilket hjälper dig att identifiera och prioritera vilken ordning du behöver för att distribuera Azure AD-funktioner baserat på den licens typ som du äger. Azure AD erbjuder många funktioner och ger många olika säkerhets nivåer för dina identiteter, och det kan ibland vara svårt att navigera i vilken funktion som är relevant. Många organisationer finns redan i molnet eller går snabbt till molnet, men det här dokumentet är avsett att du snabbt ska kunna distribuera tjänster, med säkra dina identiteter som primärt övervägande. 

Varje tabell ger en konsekvent säkerhets rekommendation som skyddar både administratörs-och användar identiteter från huvud säkerhets angrepp (brott mot repetition, nätfiske och lösen ord) samtidigt som användaren kan påverka och förbättra användar upplevelsen. 

Vägledningen gör det också möjligt för administratörer att konfigurera åtkomst till SaaS och lokala program på ett säkert och skyddat sätt och gäller för antingen molnbaserade eller hybridbaserade (synkroniserade) identiteter och gäller för användare som arbetar på distans eller på kontoret.

Den här check listan hjälper dig att snabbt distribuera kritiska rekommenderade åtgärder för att skydda din organisation omedelbart genom att förklara hur du:

- Förstärk dina autentiseringsuppgifter.
- Minska attack ytan.
- Automatisera hot svar.
- Använd Cloud Intelligence.
- Aktivera självbetjäning för slutanvändare.

## <a name="prerequisites"></a>Förutsättningar

Den här guiden förutsätter att endast ditt moln eller Hybrid identiteter har skapats i Azure AD. Om du behöver hjälp med att välja din identitets typ läser du artikeln och [väljer rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>Sammanfattning

Det finns många aspekter av en säker identitets infrastruktur, men den här check listan fokuserar på en säker och säker identitets infrastruktur som gör det möjligt för användarna att arbeta på distans. Att skydda din identitet är bara en del av din säkerhets berättelse, att skydda data, program och enheter bör också beaktas.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Vägledning för Azure AD Free, Office 365 eller Microsoft 365 kunder.

Det finns ett antal rekommendationer som Azure AD Free, Office 365 eller Microsoft 365 App-kunder bör vidta för att skydda sina användar identiteter, följande tabell är avsedd att markera viktiga åtgärder för följande licens prenumerationer:

- Office 365 (Office 365 E1, E3, E5, F1, a1, A3, A5)
- Microsoft 365 (Business Basic, Apps for Business, Business Standard, Business Premium, a1)
- Azure AD Free (ingår i Azure, Dynamics 365, Intune och Power Platform)

| Rekommenderad åtgärd | Detalj |
| --- | --- |
| [Aktivera säkerhets inställningar](concept-fundamentals-security-defaults.md) | Skydda alla användar identiteter och program genom att aktivera MFA och blockera äldre autentisering |
| [Aktivera hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md) (om Hybrid identiteter används) | Ge redundans för autentisering och förbättra säkerheten (inklusive Smart utelåsning, IP-utelåsning och möjligheten att identifiera läckta autentiseringsuppgifter.) |
| [Aktivera ADFS Smart Lock Out](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (om tillämpligt) | Skyddar dina användare från att få extra näts konto utelåsning från skadlig aktivitet. |
| [Aktivera Azure Active Directory Smart utelåsning](../authentication/howto-password-smart-lockout.md) (om du använder hanterade identiteter) | Smart utelåsning hjälper dig att låsa upp dåliga aktörer som försöker gissa dina användares lösen ord eller använda råa metoder för att komma igång. |
| [Inaktivera slut användar medgivande till program](../manage-apps/configure-user-consent.md) | Arbets flödet för administratörs medgivande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörs godkännande så att slutanvändare inte exponerar företags data. Microsoft rekommenderar att du inaktiverar framtida användar medgivande åtgärder för att minska din yta och minimera risken. |
| [Integrera SaaS-program som stöds från galleriet till Azure AD och aktivera enkel inloggning](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals förintegrerade program. Några av de program som organisationen använder är förmodligen i galleriet tillgängligt direkt från Azure Portal. Ge åtkomst till företags SaaS-program via fjärr anslutning och på ett säkert sätt med förbättrad användar upplevelse (SSO) |
| [Automatisera användar etablering och avetablering från SaaS program](../app-provisioning/user-provisioning.md) (om tillämpligt) | Skapa automatiskt användar identiteter och roller i molnet (SaaS) som användare behöver åtkomst till. Förutom att skapa användar identiteter omfattar automatisk etablering underhåll och borttagning av användar identiteter som status eller roll förändringar, vilket ökar din organisations säkerhet. |
| [Aktivera säker hybrid åtkomst: säkra äldre appar med befintliga program leverans styrenheter och nätverk](../manage-apps/secure-hybrid-access.md) (om tillämpligt) | Publicera och skydda dina lokala och molnbaserade program för autentisering av äldre program genom att ansluta dem till Azure AD med din befintliga program leverans styrenhet eller ditt nätverk. |
| [Aktivera lösen ords återställning via självbetjäning](../authentication/tutorial-enable-sspr.md) (gäller endast moln konton) | Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program. |
| [Använd icke-globala administrativa roller där det är möjligt](../roles/permissions-reference.md) | Ge dina administratörer bara den åtkomst som de behöver för att komma åt de områden som de behöver åtkomst till. Alla administratörer behöver inte vara globala administratörer. |
| [Aktivera Microsofts lösen Ords vägledning](https://www.microsoft.com/research/publication/password-guidance/) | Sluta kräva att användarna ändrar sitt lösen ord enligt ett schema, inaktiverar komplexitets kraven och att användarna är mer Apta för att komma ihåg sina lösen ord och se till att de är säkra. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Vägledning för Azure AD Premium plan 1-kunder.

Följande tabell är avsedd att markera viktiga åtgärder för följande licens prenumerationer:

- Azure Active Directory Premium P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, A3, F1, F3)

| Rekommenderad åtgärd | Detalj |
| --- | --- |
| [Aktivera kombinerad registrerings upplevelse för Azure AD MFA och SSPR för att förenkla användar registrerings upplevelsen](../authentication/howto-registration-mfa-sspr-combined.md) | Tillåt att användarna registrerar sig från en vanlig upplevelse för både Azure AD-Multi-Factor Authentication och lösen ords återställning via självbetjäning. |
| [Konfigurera MFA-inställningar för din organisation](../authentication/howto-mfa-getstarted.md) | Se till att konton skyddas från att bli komprometterade med Multi-Factor Authentication |
| [Aktivera lösenordsåterställning via självbetjäning](../authentication/tutorial-enable-sspr.md) | Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program |
| [Implementera tillbakaskrivning av lösen ord](../authentication/tutorial-enable-sspr-writeback.md) (om Hybrid identiteter används) | Tillåt att lösen ords ändringar i molnet skrivs tillbaka till en lokal Windows Server Active Directory-miljö. |
| Skapa och aktivera principer för villkorlig åtkomst | [MFA för administratörer för att skydda konton som har tilldelats administrativa rättigheter.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blockera bakåtkompatibla autentiseringsprotokoll på grund av den ökade risken som är associerad med bakåtkompatibla autentiseringsprotokoll.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA för alla användare och program för att skapa en balanserad MFA-princip för din miljö, vilket skyddar dina användare och program.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [Kräv MFA för Azure-hantering för att skydda dina privilegierade resurser genom att kräva Multi-Factor Authentication för alla användare som kommer åt Azure-resurser.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Aktivera hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md) (om Hybrid identiteter används) | Ge redundans för autentisering och förbättra säkerheten (inklusive Smart utelåsning, IP-utelåsning och möjligheten att identifiera läckta autentiseringsuppgifter.) |
| [Aktivera ADFS Smart Lock Out](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (om tillämpligt) | Skyddar dina användare från att få extra näts konto utelåsning från skadlig aktivitet. |
| [Aktivera Azure Active Directory Smart utelåsning](../authentication/howto-password-smart-lockout.md) (om du använder hanterade identiteter) | Smart utelåsning hjälper dig att låsa upp dåliga aktörer som försöker gissa dina användares lösen ord eller använda råa metoder för att komma igång. |
| [Inaktivera slut användar medgivande till program](../manage-apps/configure-user-consent.md) | Arbets flödet för administratörs medgivande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörs godkännande så att slutanvändare inte exponerar företags data. Microsoft rekommenderar att du inaktiverar framtida användar medgivande åtgärder för att minska din yta och minimera risken. |
| [Aktivera fjärråtkomst till lokala äldre program med Application Proxy](../manage-apps/application-proxy-add-on-premises-application.md) | Aktivera Azure AD-programproxy och integrera med äldre appar för att få säker åtkomst till lokala program genom att logga in med deras Azure AD-konto. |
| [Aktivera säker hybrid åtkomst: skydda äldre appar med befintliga program leverans styrenheter och nätverk](../manage-apps/secure-hybrid-access.md) (om tillämpligt). | Publicera och skydda dina lokala och molnbaserade program för autentisering av äldre program genom att ansluta dem till Azure AD med din befintliga program leverans styrenhet eller ditt nätverk. |
| [Integrera SaaS-program som stöds från galleriet till Azure AD och aktivera enkel inloggning](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals förintegrerade program. Några av de program som organisationen använder är förmodligen i galleriet tillgängligt direkt från Azure Portal. Ge åtkomst till företags SaaS-program via fjärr anslutning och på ett säkert sätt med förbättrad användar upplevelse (SSO). |
| [Automatisera användar etablering och avetablering från SaaS program](../app-provisioning/user-provisioning.md) (om tillämpligt) | Skapa automatiskt användar identiteter och roller i molnet (SaaS) som användare behöver åtkomst till. Förutom att skapa användar identiteter omfattar automatisk etablering underhåll och borttagning av användar identiteter som status eller roll förändringar, vilket ökar din organisations säkerhet. |
| [Aktivera villkorlig åtkomst – enhet baserad](../conditional-access/require-managed-devices.md) | Förbättra säkerhets-och användar upplevelser med enhets-baserad villkorlig åtkomst. Det här steget ser till att användarna bara kan komma åt från enheter som uppfyller dina standarder för säkerhet och efterlevnad. Dessa enheter kallas även hanterade enheter. Hanterade enheter kan vara Intune-kompatibla eller hybrid Azure AD-anslutna enheter. |
| [Aktivera lösen ords skydd](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Skydda användare från att använda svaga och enkla att gissa lösen ord. |
| [Ange mer än en global administratör](../roles/security-emergency-access.md) | Tilldela minst två permanenta globala administratörs konton för enbart molnet som ska användas om det finns en nöd situation. Dessa konton används inte dagligen och bör ha långa och komplexa lösen ord. Bryt glas konton se till att du har åtkomst till tjänsten i nödfall. |
| [Använd icke-globala administrativa roller där det är möjligt](../roles/permissions-reference.md) | Ge dina administratörer bara den åtkomst som de behöver för att komma åt de områden som de behöver åtkomst till. Alla administratörer behöver inte vara globala administratörer. |
| [Aktivera Microsofts lösen Ords vägledning](https://www.microsoft.com/research/publication/password-guidance/) | Sluta kräva att användarna ändrar sitt lösen ord enligt ett schema, inaktiverar komplexitets kraven och att användarna är mer Apta för att komma ihåg sina lösen ord och se till att de är säkra. |
| [Skapa en plan för åtkomst till gäst användare](../external-identities/what-is-b2b.md) | Samar beta med gäst användare genom att låta dem logga in på dina appar och tjänster med sina egna arbets-, skol-eller sociala identiteter. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Vägledning för Azure AD Premium plan 2-kunder.

Följande tabell är avsedd att markera viktiga åtgärder för följande licens prenumerationer:

- Azure Active Directory Premium P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, A5)

| Rekommenderad åtgärd | Detalj |
| --- | --- |
| [Aktivera kombinerad registrerings upplevelse för Azure AD MFA och SSPR för att förenkla användar registrerings upplevelsen](../authentication/howto-registration-mfa-sspr-combined.md) | Tillåt att användarna registrerar sig från en vanlig upplevelse för både Azure AD-Multi-Factor Authentication och lösen ords återställning via självbetjäning. |
| [Konfigurera MFA-inställningar för din organisation](../authentication/howto-mfa-getstarted.md) | Se till att konton skyddas från att bli komprometterade med Multi-Factor Authentication |
| [Aktivera lösenordsåterställning via självbetjäning](../authentication/tutorial-enable-sspr.md) | Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program |
| [Implementera tillbakaskrivning av lösen ord](../authentication/tutorial-enable-sspr-writeback.md) (om Hybrid identiteter används) | Tillåt att lösen ords ändringar i molnet skrivs tillbaka till en lokal Windows Server Active Directory-miljö. |
| [Aktivera identitets skydds principer för att genomdriva MFA-registrering](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Hantera distributionen av Azure AD Multi-Factor Authentication (MFA). |
| [Aktivera identitets skydds användare och inloggnings risk principer](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Aktivera identitets skydds användare och inloggnings principer. Den rekommenderade inloggnings principen är riktad mot inloggnings program för medelhög risk och kräver MFA. För användar principer bör den vara riktad mot användare med hög risk som kräver åtgärden för lösen ords ändring. |
| Skapa och aktivera principer för villkorlig åtkomst | [MFA för administratörer för att skydda konton som har tilldelats administrativa rättigheter.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Blockera bakåtkompatibla autentiseringsprotokoll på grund av den ökade risken som är associerad med bakåtkompatibla autentiseringsprotokoll.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [Kräv MFA för Azure-hantering för att skydda dina privilegierade resurser genom att kräva Multi-Factor Authentication för alla användare som kommer åt Azure-resurser.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Aktivera hash-synkronisering av lösen ord](../hybrid/how-to-connect-password-hash-synchronization.md) (om Hybrid identiteter används) | Ge redundans för autentisering och förbättra säkerheten (inklusive Smart utelåsning, IP-utelåsning och möjligheten att identifiera läckta autentiseringsuppgifter.) |
| [Aktivera ADFS Smart Lock Out](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (om tillämpligt) | Skyddar dina användare från att få extra näts konto utelåsning från skadlig aktivitet. |
| [Aktivera Azure Active Directory Smart utelåsning](../authentication/howto-password-smart-lockout.md) (om du använder hanterade identiteter) | Smart utelåsning hjälper dig att låsa upp dåliga aktörer som försöker gissa dina användares lösen ord eller använda råa metoder för att komma igång. |
| [Inaktivera slut användar medgivande till program](../manage-apps/configure-user-consent.md) | Arbets flödet för administratörs medgivande ger administratörer ett säkert sätt att bevilja åtkomst till program som kräver administratörs godkännande så att slutanvändare inte exponerar företags data. Microsoft rekommenderar att du inaktiverar framtida användar medgivande åtgärder för att minska din yta och minimera risken. |
| [Aktivera fjärråtkomst till lokala äldre program med Application Proxy](../manage-apps/application-proxy-add-on-premises-application.md) | Aktivera Azure AD-programproxy och integrera med äldre appar för att få säker åtkomst till lokala program genom att logga in med deras Azure AD-konto. |
| [Aktivera säker hybrid åtkomst: skydda äldre appar med befintliga program leverans styrenheter och nätverk](../manage-apps/secure-hybrid-access.md) (om tillämpligt). | Publicera och skydda dina lokala och molnbaserade program för autentisering av äldre program genom att ansluta dem till Azure AD med din befintliga program leverans styrenhet eller ditt nätverk. |
| [Integrera SaaS-program som stöds från galleriet till Azure AD och aktivera enkel inloggning](../manage-apps/add-application-portal.md) | Azure AD har ett galleri som innehåller tusentals förintegrerade program. Några av de program som organisationen använder är förmodligen i galleriet tillgängligt direkt från Azure Portal. Ge åtkomst till företags SaaS-program via fjärr anslutning och på ett säkert sätt med förbättrad användar upplevelse (SSO). |
| [Automatisera användar etablering och avetablering från SaaS program](../app-provisioning/user-provisioning.md) (om tillämpligt) | Skapa automatiskt användar identiteter och roller i molnet (SaaS) som användare behöver åtkomst till. Förutom att skapa användar identiteter omfattar automatisk etablering underhåll och borttagning av användar identiteter som status eller roll förändringar, vilket ökar din organisations säkerhet. |
| [Aktivera villkorlig åtkomst – enhet baserad](../conditional-access/require-managed-devices.md) | Förbättra säkerhets-och användar upplevelser med enhets-baserad villkorlig åtkomst. Det här steget ser till att användarna bara kan komma åt från enheter som uppfyller dina standarder för säkerhet och efterlevnad. Dessa enheter kallas även hanterade enheter. Hanterade enheter kan vara Intune-kompatibla eller hybrid Azure AD-anslutna enheter. |
| [Aktivera lösen ords skydd](../authentication/howto-password-ban-bad-on-premises-deploy.md) | Skydda användare från att använda svaga och enkla att gissa lösen ord. |
| [Ange mer än en global administratör](../roles/security-emergency-access.md) | Tilldela minst två permanenta globala administratörs konton för enbart molnet som ska användas om det finns en nöd situation. Dessa konton används inte dagligen och bör ha långa och komplexa lösen ord. Bryt glas konton se till att du har åtkomst till tjänsten i nödfall. |
| [Använd icke-globala administrativa roller där det är möjligt](../roles/permissions-reference.md) | Ge dina administratörer bara den åtkomst som de behöver för att komma åt de områden som de behöver åtkomst till. Alla administratörer behöver inte vara globala administratörer. |
| [Aktivera Microsofts lösen Ords vägledning](https://www.microsoft.com/research/publication/password-guidance/) | Sluta kräva att användarna ändrar sitt lösen ord enligt ett schema, inaktiverar komplexitets kraven och att användarna är mer Apta för att komma ihåg sina lösen ord och se till att de är säkra. |
| [Skapa en plan för åtkomst till gäst användare](../external-identities/what-is-b2b.md) | Samar beta med gäst användare genom att låta dem logga in på dina appar och tjänster med sina egna arbets-, skol-eller sociala identiteter. |
| [Aktivera Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Med kan du hantera, kontrol lera och övervaka åtkomsten till viktiga resurser i din organisation, vilket garanterar att administratörer bara har åtkomst vid behov och med godkännande |

## <a name="next-steps"></a>Nästa steg

- Detaljerad distributions vägledning för enskilda funktioner i Azure AD finns i [distributions planerna för Azure AD-projekt](active-directory-deployment-plans.md).

- En distributions check lista för distribution från slut punkt till slut punkt finns i artikeln [Azure Active Directory funktions distributions guide](active-directory-deployment-checklist-p2.md)