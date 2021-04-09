---
title: Autentisering baserad på Android-certifikat Azure Active Directory
description: Lär dig mer om de scenarier som stöds och kraven för att konfigurera certifikatbaserad autentisering i lösningar med Android-enheter
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861382"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Azure Active Directory certifikatbaserad autentisering på Android

Android-enheter kan använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory att använda ett klient certifikat på sin enhet vid anslutning till:

* Office Mobile-program som Microsoft Outlook och Microsoft Word
* Exchange ActiveSync-klienter (EAS)

Genom att konfigurera den här funktionen slipper du ange en kombination av användar namn och lösen ord i vissa e-post-och Microsoft Office program på din mobila enhet.

Det här avsnittet innehåller kraven och de scenarier som stöds för att konfigurera CBA på en Android-enhet för användare av klienter i Office 365 Enterprise, Business, Education, amerikanska myndigheter, Kina och tyska planer.

Den här funktionen är tillgänglig i för hands versionen i Office 365 amerikanska myndigheter och federala planer.

## <a name="microsoft-mobile-applications-support"></a>Stöd för Microsoft Mobile Applications

| Appar | Support |
| --- | --- |
| Azure Information Protection app |![Bock för att ange stöd för det här programmet][1] |
| Intune Företagsportal |![Bock för att ange stöd för det här programmet][1] |
| Microsoft Teams |![Bock för att ange stöd för det här programmet][1] |
| OneNote |![Bock för att ange stöd för det här programmet][1] |
| OneDrive |![Bock för att ange stöd för det här programmet][1] |
| Outlook |![Bock för att ange stöd för det här programmet][1] |
| Power BI |![Bock för att ange stöd för det här programmet][1] |
| Skype för företag |![Bock för att ange stöd för det här programmet][1] |
| Word/Excel/PowerPoint |![Bock för att ange stöd för det här programmet][1] |
| Yammer |![Bock för att ange stöd för det här programmet][1] |

### <a name="implementation-requirements"></a>Implementerings krav

Enhetens OS-version måste vara Android 5,0 (Lollipop) och högre.

En Federations Server måste konfigureras.

För att Azure Active Directory återkalla ett klient certifikat måste ADFS-token ha följande anspråk:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Klient certifikatets serie nummer)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (Strängen för utfärdaren av klient certifikatet)

Azure Active Directory lägger till dessa anspråk till uppdateringstoken om de är tillgängliga i ADFS-token (eller någon annan SAML-token). När uppdateringstoken behöver verifieras används den här informationen för att kontrol lera återkallning.

Vi rekommenderar att du uppdaterar organisationens ADFS-felsidor med följande information:

* Kravet på att installera Microsoft Authenticator på Android.
* Instruktioner om hur du hämtar ett användar certifikat.

Mer information finns i [anpassa AD FS inloggnings sidor](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Vissa Office-appar (med modern autentisering aktive rad) skicka '*prompt = inloggning*' till Azure AD i sin begäran. Som standard översätter Azure AD "*prompt = login*" i begäran till ADFS som "*WAUTH = usernamepassworduri*" (uppmanar ADFS att göra U/P auth) och "*wfresh = 0*" (uppmanar ADFS att ignorera SSO-tillstånd och gör en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för de här apparna måste du ändra standard beteendet för Azure AD. Ange "*PromptLoginBehavior*" i dina federerade domän inställningar till "*inaktive rad*".
Du kan använda [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) -cmdlet: en för att utföra den här uppgiften:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Stöd för Exchange ActiveSync-klienter

Vissa Exchange ActiveSync-program på Android 5,0 (Lollipop) eller senare stöds. Kontakta programutvecklaren för att ta reda på om ditt e-postprogram stöder den här funktionen.

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera certifikatbaserad autentisering i din miljö, se [Kom igång med certifikatbaserad autentisering på Android](active-directory-certificate-based-authentication-get-started.md) för instruktioner.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
