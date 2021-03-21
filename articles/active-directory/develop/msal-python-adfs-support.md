---
title: Stöd för Azure AD FS (MSAL python)
titleSuffix: Microsoft identity platform
description: Läs mer om stöd för Active Directory Federation Services (AD FS) (AD FS) i Microsoft Authentication Library för python
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d847e38a6114b0e5f8d56f6e994aa47dfbe929e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583713"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Active Directory Federation Services (AD FS) stöd i MSAL för python

Med Active Directory Federation Services (AD FS) (AD FS) i Windows Server kan du lägga till OpenID Connect och OAuth 2,0-baserad autentisering och auktorisering till dina appar med hjälp av Microsoft Authentication Library (MSAL) för python. Med hjälp av MSAL för python-biblioteket kan appen autentisera användare direkt mot AD FS. Mer information om scenarier finns i [AD FS scenarier för utvecklare](/windows-server/identity/ad-fs/ad-fs-development).

Det finns vanligt vis två sätt att autentisera mot AD FS:

- MSAL python pratar med Azure Active Directory, som i sin tur är federerad med andra identitets leverantörer. Federationen sker via AD FS. MSAL python ansluter till Azure AD, som loggar in användare som hanteras i Azure AD (hanterade användare) eller användare som hanteras av en annan identitets leverantör, till exempel AD FS (federerade användare). MSAL python vet inte att en användare är federerad. Det bara pratar med Azure AD. Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga utfärdaren (utfärdarens värdnamn + innehavare, vanliga eller organisationer).
- MSAL python pratar direkt med en AD FS utfärdare. Detta stöds endast av AD FS 2019 och senare.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>Ansluta till Active Directory federerad med AD FS

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>Hämta en token interaktivt för en federerad användare

Följande gäller om du ansluter direkt till Active Directory Federation Services (AD FS) (AD FS) eller via Active Directory.

När du anropar `acquire_token_by_authorization_code` eller `acquire_token_by_device_flow` , är användar upplevelsen vanligt vis följande:

1. Användaren anger sitt konto-ID.
2. Azure AD visar ett kort meddelande "som tar dig till din organisations sida" och användaren omdirigeras till inloggnings sidan för identitets leverantören. Inloggnings sidan är vanligt vis anpassad med organisationens logo typ.

De AD FS versioner som stöds i det här federerade scenariot är:
- Active Directory Federation Services (AD FS) FS v2
- Active Directory Federation Services (AD FS) v3 (Windows Server 2012 R2)
- Active Directory Federation Services (AD FS) v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>Hämta en token via användar namn och lösen ord

Följande gäller om du ansluter direkt till Active Directory Federation Services (AD FS) (AD FS) eller via Active Directory.

När du hämtar en token med hjälp av `acquire_token_by_username_password` MSAL python får identitets leverantören kontakt utifrån användar namnet. MSAL python hämtar en [SAML 1,1-token](reference-saml-tokens.md) från identitets leverantören, som sedan skickar den till Azure AD som returnerar JSON Web token (JWT).

## <a name="connecting-directly-to-ad-fs"></a>Ansluter direkt till AD FS

När du ansluter katalogen till AD FS kommer den behörighet som du vill använda för att bygga ditt program att vara något som liknar `https://somesite.contoso.com/adfs/`

MSAL python stöder ADFS 2019.

Den har inte stöd för en direkt anslutning till ADFS 2016 eller ADFS v2. Använd den senaste versionen av ADAL python för att stödja scenarier som kräver en direkt anslutning till ADFS 2016. När du har uppgraderat ditt lokala system till ADFS 2019 kan du använda MSAL python.

## <a name="next-steps"></a>Nästa steg

- För det federerade fallet, se [konfigurera Azure Active Directory inloggnings beteende för ett program med hjälp av en princip för identifiering av start sfär](../manage-apps/configure-authentication-for-federated-users-portal.md)
