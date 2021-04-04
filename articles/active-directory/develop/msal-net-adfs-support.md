---
title: AD FS stöd i MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om stöd för Active Directory Federation Services (AD FS) (AD FS) i Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: a146b310e6056954ac2655ff2fd99e1e3d7c694f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063645"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory Federation Services (AD FS) stöd i MSAL.NET
Med Active Directory Federation Services (AD FS) (AD FS) i Windows Server kan du lägga till OpenID Connect och OAuth 2,0-baserad autentisering och auktorisering för program som du utvecklar. Dessa program kan sedan autentisera användare direkt mot AD FS. Mer information finns [i AD FS scenarier för utvecklare](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios).

Microsoft Authentication Library för .NET (MSAL.NET) stöder två scenarier för autentisering mot AD FS:

- MSAL.NET pratar med Azure Active Directory, som i sin tur är *federerad* med AD FS.
- MSAL.NET pratar **direkt** med en ADFS-auktoritet. Detta stöds endast från AD FS 2019 och senare. Ett av scenarierna som beskrivs är [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) support


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL ansluter till Azure AD, som är federerad med AD FS
MSAL.NET stöder anslutning till Azure AD, som loggar in i hanterade användare (användare som hanteras i Azure AD) eller federerade användare (användare som hanteras av en annan identitetsprovider, till exempel AD FS). MSAL.NET vet inte om det faktum att användare är federerade. Så långt det är fråga pratar det med Azure AD.

Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga utfärdaren (utfärdarens värdnamn + innehavare, vanliga eller organisationer).

### <a name="acquiring-a-token-interactively"></a>Att förvärva en token interaktivt
När du anropar `AcquireTokenInteractive` -metoden är användar upplevelsen normalt:

1. Användaren anger sitt konto-ID.
2. Azure AD visar ett kort meddelande "som tar dig till din organisations sida".
3. Användaren omdirigeras till inloggnings sidan för identitets leverantören. Inloggnings sidan är vanligt vis anpassad med organisationens logo typ.

AD FS versioner som stöds i det här federerade scenariot är AD FS v2, AD FS v3 (Windows Server 2012 R2) och AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>Hämta en token med AcquireTokenByIntegratedAuthentication eller AcquireTokenByUsernamePassword
När du hämtar en token med hjälp av `AcquireTokenByIntegratedAuthentication` `AcquireTokenByUsernamePassword` metoderna eller, hämtar MSAL.net identitets leverantören för att kontakta utifrån användar namnet.  MSAL.NET tar emot en [SAML 1,1-token](reference-saml-tokens.md) efter att ha kontaktat identitets leverantören.  MSAL.NET tillhandahåller sedan SAML-token till Azure AD som en användar kontroll (liknar [flödet på uppdrags](msal-authentication-flows.md#on-behalf-of)sidan) för att få tillbaka en JWT.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL ansluter direkt till AD FS
MSAL.NET stöder anslutning till AD FS 2019, som är öppen ID Connect-kompatibel och förstår PKCE och omfång. Detta stöd kräver att en service pack [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) tillämpas på Windows Server. När du ansluter direkt till AD FS, liknar den myndighet som du vill använda för att bygga ditt program på samma sätt `https://mysite.contoso.com/adfs/` .

För närvarande finns det inga planer på att stödja en direkt anslutning till:

- AD FS 16, eftersom den inte har stöd för PKCE och fortfarande använder resurser, inte omfång
- AD FS v2, som inte är OIDC-kompatibelt.

 Om du behöver stöd för scenarier som kräver en direkt anslutning till AD FS 2016 använder du den senaste versionen av [Azure Active Directory autentiseringspaket](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries). När du har uppgraderat ditt lokala system till AD FS 2019 kan du använda MSAL.NET.

## <a name="next-steps"></a>Nästa steg

För det federerade fallet, se [konfigurera Azure Active Directory inloggnings beteende för ett program med hjälp av en princip för identifiering av start sfär](../manage-apps/configure-authentication-for-federated-users-portal.md)