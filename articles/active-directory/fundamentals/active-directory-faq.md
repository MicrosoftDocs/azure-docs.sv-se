---
title: Vanliga frågor och svar (FAQ) – Azure Active Directory | Microsoft Docs
description: Vanliga frågor och svar om Azure och Azure Active Directory, lösen ords hantering och program åtkomst.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b9f76eba9697d6ad47ecf8dc51689538923f88
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094483"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Vanliga frågor och svar om Azure Active Directory
Azure Active Directory (Azure AD) är en omfattande IDaaS-lösning (Identity as a Service) som omfattar alla aspekter relaterade till identiteter, åtkomsthantering och säkerhet.

Mer information finns i [Vad är Azure Active Directory?](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Kom åt Azure och Azure Active Directory
**F: Varför visas "inga prenumerationer hittades" när jag försöker komma åt Azure AD i Azure Portal?**

**S:** För att komma åt Azure-portalen behöver varje användare ha behörighet med en Azure-prenumeration. Om du inte har någon betald Microsoft 365 eller Azure AD-prenumeration måste du aktivera ett kostnads fritt [Azure-konto](https://azure.microsoft.com/free/
) eller en betald prenumeration.

Mer information finns i:

* [Hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**F: Vad är förhållandet mellan Azure AD, Microsoft 365 och Azure?**

**S:** Azure AD tillhandahåller gemensamma identitets- och åtkomstfunktioner för alla webbtjänster. Oavsett om du använder Microsoft 365, Microsoft Azure, Intune eller andra, använder du redan Azure AD för att aktivera inloggning och åtkomst hantering för alla dessa tjänster.

Alla användare som har ställts in för att använda webbtjänster definieras som användarkonton i en eller flera Azure AD-instanser. Du kan ställa in dessa konton för kostnadsfria Azure AD-funktioner, till exempel programåtkomst i molnet.

Azure AD-betalda tjänster som Enterprise Mobility + Security kompletterar andra webb tjänster som Microsoft 365 och Microsoft Azure med omfattande hanterings-och säkerhetslösningar i företags skala.

---

**F: Vad är skillnaderna mellan ägare och global administratör?**

**A:** Som standard tilldelas den person som registrerar sig för en Azure-prenumeration ägar rollen för Azure-resurser. En ägare kan antingen använda en Microsoft-konto eller ett arbets-eller skol konto från den katalog som Azure-prenumerationen är associerad med.  Den här rollen har behörighet att hantera tjänster på Azure-portalen.

Om andra behöver logga in och få åtkomst till tjänster med samma prenumeration kan du tilldela dem lämplig [inbyggd roll](../../role-based-access-control/built-in-roles.md). Mer information finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).

Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som global administratör för katalogen. Den globala administratören har åtkomst till alla funktioner i Azure AD-katalogen. Azure AD har en annan uppsättning administratörs roller för att hantera katalog-och Identity-relaterade funktioner. Dessa administratörer kommer att ha åtkomst till olika funktioner i Azure Portal. Administratörens roll bestämmer vad de kan göra, t. ex. skapa eller redigera användare, tilldela administrativa roller till andra, återställa användar lösen ord, hantera användar licenser eller hantera domäner.  Mer information om Azure AD Directory-administratörer och deras roller finns i [tilldela en användare till administratörs roller i Azure Active Directory](active-directory-users-assign-role-azure-portal.md) och [tilldela administratörs roller i Azure Active Directory](../roles/permissions-reference.md).

Dessutom har Azure AD-betal tjänster som Enterprise Mobility + Security kompletterar andra webb tjänster, till exempel Microsoft 365 och Microsoft Azure, med omfattande hanterings-och säkerhetslösningar i företags skala.

---
**F: Finns det någon rapport som visar när mina Azure AD-användarlicenser upphör att gälla?**

**A:** Nej.  Det här är inte tillgängligt för närvarande.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Kom igång med en Azure AD-hybridlösning


**F: Hur lämnar jag en klient när jag har lagts till som medarbetare?**

**A:** När du har lagts till som medarbetare till en annan organisations klient kan du använda "klientväxlaren" i det övre högra hörnet för att växla mellan klienter.  För närvarande går det inte att lämna organisationen som bjuder in och Microsoft arbetar med att tillhandahålla den här funktionen.  Tills den funktionen är tillgänglig kan du be organisationen som bjuder in att ta bort dig från deras klient.

---
**F: Hur kan jag ansluta min lokala katalog till Azure AD?**

**S:** Du kan ansluta din lokala katalog till Azure AD med hjälp av Azure AD Connect.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**F: Hur konfigurerar jag enkel inloggning (SSO) mellan min lokala katalog och mina molnprogram?**

**S:** Du behöver bara konfigurera enkel inloggning (SSO) mellan din lokala katalog och AD Azure. Så länge du kommer åt dina molnprogram via Azure AD ser tjänsten till att användarna autentiseras korrekt med deras lokala autentiseringsuppgifter.

Det går lätt att implementera SSO från lokala datorer med Federations lösningar som Active Directory Federation Services (AD FS) (AD FS) eller genom att konfigurera hash-synkronisering av lösen ord. Du kan enkelt distribuera båda alternativen med hjälp av konfigurations guiden för Azure AD Connect.

Mer information finns i [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

---
**F: Tillhandahåller Azure AD en självbetjäningsportal för användare i organisationen?**

**S:** Ja, Azure AD ger tillgång till [Azure AD-åtkomstpanelen](https://myapps.microsoft.com) för självbetjäning och programåtkomst. Om du är Microsoft 365 kund kan du hitta många av funktionerna i [Office 365-portalen](https://portal.office.com).

Mer information finns i [Introduktion till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

---
**F: Kan Azure AD hjälpa mig att hantera min lokala infrastruktur?**

**S:** Ja. Azure AD Premium-versionen tillhandahåller Azure AD Connect Health. Azure AD Connect Health hjälper dig att övervaka och få insyn i den lokala identitetsinfrastrukturen och synkroniseringstjänsterna.  

Mer information finns i [Övervaka den lokala identitetsinfrastrukturen och synkroniseringstjänster i molnet](../hybrid/whatis-azure-ad-connect.md).  

---
## <a name="password-management"></a>Lösenordshantering
**F: kan jag använda tillbakaskrivning av lösen ord I Azure AD utan Lösenordssynkronisering? (I det här scenariot är det möjligt att använda Azure AD självbetjäning för återställning av lösen ord (SSPR) med lösen ords skrivning och inte lagra lösen ord i molnet?)**

**S:** Du behöver inte synkronisera dina Active Directory-lösenord till Azure AD för att använda tillbakaskrivning. I en federerad miljö använder enkel inloggning (SSO) i Azure AD den lokala katalogen för att autentisera användaren. I det här scenariot måste inte det lokala lösenordet spåras i Azure AD.

---
**F: Hur lång tid tar det för ett lösenord att skrivas tillbaka till Active Directory lokalt?**

**S:** Tillbakaskrivningen av lösenord fungerar i realtid.

Mer information finns i [Komma igång med lösenordshantering](../authentication/tutorial-enable-sspr.md).

---
**F: Kan jag använda tillbakaskrivning av lösenord med lösenord som hanteras av en administratör?**

**S:** Ja, om du har aktiverat tillbakaskrivning av lösenord skrivs lösenordsåtgärder som utförs av en administratör tillbaka till din lokala miljö.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Fler svar på lösenordsrelaterade frågor finns i [Vanliga frågor och svar om lösenordshantering](../authentication/active-directory-passwords-faq.md).
---
**F: Vad kan jag göra om jag inte kan komma ihåg mitt befintliga Microsoft 365/Azure AD-lösenord vid försök att ändra mitt lösen ord?**

**S:** För den här typen av situation finns det ett par alternativ.  Använd självbetjäning för återställning av lösenord (SSPR) om det är tillgängligt.  Huruvida SSPR fungerar eller ej beror på hur det är konfigurerat.  Mer information finns i avsnittet som beskriver [Hur portalen för lösenordsåterställning fungerar](../authentication/howto-sspr-deployment.md).

För Microsoft 365 användare kan administratören återställa lösen ordet med hjälp av stegen som beskrivs i [återställa användar lösen ord](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

För Azure AD-konton kan administratörer återställa lösenord med någon av följande metoder:

- [Återställa konton på Azure-portalen](active-directory-users-reset-password-azure-portal.md)
- [Använda PowerShell](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Säkerhet
**Fråga: Låses konton efter ett visst antal misslyckade försök eller finns det en mer avancerad strategi?**

Vi använder en mer avancerad strategi för att låsa konton.  Den är baserad på IP-adressen för begäran och det lösenord som anges. Låsningens varaktighet ökar också beroende på sannolikheten för att det rör sig om ett angrepp.  

**F: vissa (vanliga) lösen ord avvisas med meddelandena "det här lösen ordet har använts för många gånger", avser detta lösen ord som används i den aktuella Active Directory?**

Detta avser lösen ord som är globalt gemensamma, till exempel varianter av "Password" och "123456".

**Fråga: Blockeras inloggningsbegäranden från misstänkta källor (botnät, Tor-slutpunkt) på en B2C-klient eller kräver detta att klienten har en Basic- eller Premium-utgåva?**

Vi har en gateway som filtrerar begäranden och som ger ett visst skydd mot botnät. Den används för alla B2C-klienter.

## <a name="application-access"></a>Programåtkomst

**F: Var kan jag hitta en lista över program som redan är integrerade i Azure AD och deras funktioner?**

**S:** Azure AD har mer än 2 600 redan integrerade program från Microsoft, programtjänstleverantörer och partner. Alla redan integrerade program stöder enkel inloggning (SSO). Med enkel inloggning kan du använda din organisations autentiseringsuppgifter för att komma åt dina appar. Vissa program stöder även automatisk etablering och avetablering.

En fullständig lista över redan integrerade program finns på [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

---
**F: Vad gör jag om jag inte hittar det program som jag behöver på Azure AD-Marketplace?**

**S:** Med Azure AD Premium kan du lägga till och konfigurera de program du vill. Beroende på programmets funktioner och dina inställningar kan du konfigurera SSO och automatisk etablering.  

Mer information finns i:

* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/configure-saml-single-sign-on.md)
* [Använda SCIM för att aktivera automatisk etablering av användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**F: Hur loggar användarna in i program med Azure AD?**

**S:** Med Azure AD kan användarna visa och komma åt sina program på flera olika sätt, t.ex.:

* Azure AD-åtkomstpanelen
* Microsoft 365 Application starta
* Direkt inloggning till federerade appar
* Djuplänkar till federerade, lösenordsbaserade eller befintliga appar

Mer information finns i [slut användar upplevelser för program](../manage-apps/end-user-experiences.md).

---
**F: På vilka sätt stöder Azure AD autentisering och enkel inloggning (SSO) i program?**

**S:** Azure AD har stöd för många standardiserade protokoll för autentisering och auktorisering, till exempel SAML 2.0, OpenID Connect, OAuth 2.0 och WS-Federation. Azure AD stöder också lösenordsvalv och automatisk inloggning för appar som endast har stöd för formulärbaserad autentisering.  

Mer information finns i:

* [Autentiseringsscenarier för Azure AD](../develop/authentication-vs-authorization.md)
* [Active Directory autentiseringsprotokoll](/previous-versions/azure/dn151124(v=azure.100))
* [Enkel inloggning för program i Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**F: kan jag lägga till program som jag kör lokalt?**

**S:** Azure AD Application Proxy ger enkel och säker åtkomst till lokala webbprogram som du väljer. Du kan komma åt dessa program på samma sätt som du kommer åt programvara som en tjänst (SaaS)-appar i Azure AD. Du behöver ingen VPN-anslutning och du behöver inte ändra din nätverksinfrastruktur.  

Mer information finns i [Så här tillhandahåller du säker fjärråtkomst till lokala program](../manage-apps/application-proxy.md).

---
**F: Hur kräver jag Multi-Factor Authentication för användare som kommer åt ett visst program?**

**A:** Med villkorlig åtkomst i Azure AD kan du tilldela en unik åtkomst princip för varje program. Du kan välja att alltid kräva Multi-Factor Authentication eller bara kräva det när användarna inte är anslutna till det lokala nätverket.  

Mer information finns i [Skydda åtkomsten till Microsoft 365 och andra appar som är anslutna till Azure Active Directory](../conditional-access/overview.md).

---
**F: Vad är automatisk användar etablering för SaaS-appar?**

**S:** Med Azure AD kan du automatisera genereringen, underhållet och borttagningen av användaridentiteter i många populära SaaS-appar i molnet.

Mer information finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

---
**F: Kan jag skapa en säker LDAP-anslutning med Azure AD?**

**A:**  Nej. Azure AD stöder inte LDAP-protokollet (Lightweight Directory Access Protocol) eller säkert LDAP direkt. Det är dock möjligt att aktivera Azure AD Domain Services (Azure AD DS)-instansen på din Azure AD-klient med rätt konfigurerade nätverks säkerhets grupper via Azure-nätverk för att uppnå LDAP-anslutning. Mer information finns i [Konfigurera säker LDAP för en Azure Active Directory Domain Services hanterad domän](../../active-directory-domain-services/tutorial-configure-ldaps.md)
