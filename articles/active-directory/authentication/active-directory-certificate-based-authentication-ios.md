---
title: Certifikatbaserad autentisering på iOS – Azure Active Directory
description: Lär dig mer om de scenarier som stöds och kraven för att konfigurera certifikatbaserad autentisering för Azure Active Directory i lösningar med iOS-enheter
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861331"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Azure Active Directory certifikatbaserad autentisering på iOS

För att förbättra säkerheten kan iOS-enheter använda certifikatbaserad autentisering (CBA) för att autentisera till Azure Active Directory (Azure AD) med hjälp av ett klient certifikat på sin enhet vid anslutning till följande program eller tjänster:

* Office Mobile-program som Microsoft Outlook och Microsoft Word
* Exchange ActiveSync-klienter (EAS)

Med hjälp av certifikat slipper du ange en kombination av användar namn och lösen ord i vissa e-post-och Microsoft Office program på din mobila enhet.

Den här artikeln beskriver kraven och de scenarier som stöds för att konfigurera CBA på en iOS-enhet. CBA för iOS är tillgängligt i offentliga Azure-moln, i Microsofts offentliga moln, Microsoft Cloud Tyskland och Microsoft Azure Kina.

## <a name="microsoft-mobile-applications-support"></a>Stöd för Microsoft Mobile Applications

| Appar | Support |
| --- | --- |
| Azure Information Protection app |![Bock för att ange stöd för det här programmet][1] |
| Intune Företagsportal |![Bock för att ange stöd för det här programmet][1] |
| Microsoft Teams |![Bock för att ange stöd för det här programmet][1] |
| Office (mobilt) |![Bock för att ange stöd för det här programmet][1] |
| OneNote |![Bock för att ange stöd för det här programmet][1] |
| OneDrive |![Bock för att ange stöd för det här programmet][1] |
| Outlook |![Bock för att ange stöd för det här programmet][1] |
| Power BI |![Bock för att ange stöd för det här programmet][1] |
| Skype för företag |![Bock för att ange stöd för det här programmet][1] |
| Word/Excel/PowerPoint |![Bock för att ange stöd för det här programmet][1] |
| Yammer |![Bock för att ange stöd för det här programmet][1] |

## <a name="requirements"></a>Krav

Om du vill använda CBA med iOS gäller följande krav och överväganden:

* Enhetens OS-version måste vara iOS 9 eller senare.
* Microsoft Authenticator krävs för Office-program på iOS.
* En identitets inställning måste skapas i macOS-nyckel ringen som inkluderar autentiserings-URL: en för ADFS-servern. Mer information finns i [skapa en identitets inställning i nyckel rings åtkomst på Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Följande Active Directory Federation Services (AD FS) (ADFS) krav och överväganden gäller:

* ADFS-servern måste vara aktive rad för certifikatautentisering och använda federerad autentisering.
* Certifikatet måste använda förbättrad nyckel användning (EKU) och innehålla användarens UPN i det *alternativa ämnes namnet (NT-huvudnamn)*.

## <a name="configure-adfs"></a>Konfigurera ADFS

För att Azure AD ska kunna återkalla ett klient certifikat måste ADFS-token ha följande anspråk. Azure AD lägger till dessa anspråk till uppdateringstoken om de är tillgängliga i ADFS-token (eller någon annan SAML-token). När uppdateringstoken behöver verifieras används den här informationen för att kontrol lera åter kallelsen:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` – Lägg till serie numret för ditt klient certifikat
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` – Lägg till strängen för utfärdaren av klient certifikatet

Vi rekommenderar att du också uppdaterar organisationens ADFS-felsidor med följande information:

* Kravet på att installera Microsoft Authenticator på iOS.
* Instruktioner om hur du hämtar ett användar certifikat.

Mer information finns i [anpassa AD FS inloggnings sida](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Använd modern autentisering med Office-appar

Vissa Office-appar med modern autentisering har Aktiver ATS skicka `prompt=login` till Azure AD i sin begäran. Som standard översätter Azure AD `prompt=login` i begäran till ADFS som `wauth=usernamepassworduri` (uppmanar ADFS att göra U/P-autentisering) och `wfresh=0` (uppmanar ADFS att ignorera SSO-tillstånd och göra en ny autentisering). Om du vill aktivera certifikatbaserad autentisering för de här apparna ändrar du standard beteendet för Azure AD.

Om du vill uppdatera standard beteendet ställer du in "*PromptLoginBehavior*" i dina federerade domän inställningar på *inaktive rad*. Du kan använda [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) -cmdlet: en för att utföra den här uppgiften, som du ser i följande exempel:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Stöd för Exchange ActiveSync-klienter

På iOS 9 eller senare stöds den inbyggda iOS-e-postklienten. Kontakta programutvecklaren för att avgöra om den här funktionen stöds för alla andra Exchange ActiveSync-program.

## <a name="next-steps"></a>Nästa steg

Instruktioner för hur du konfigurerar certifikatbaserad autentisering i din miljö finns i [komma igång med certifikatbaserad autentisering](active-directory-certificate-based-authentication-get-started.md) .

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
