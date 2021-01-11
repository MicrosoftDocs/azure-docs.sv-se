---
title: AD FS stöd (MSAL för Java)
titleSuffix: Microsoft identity platform
description: Läs mer om stöd för Active Directory Federation Services (AD FS) (AD FS) i Microsoft Authentication Library för Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 82e3f3b32c6d96b83ec1d0402f344e7d65788c06
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063713"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Active Directory Federation Services (AD FS) stöd i MSAL för Java

Med Active Directory Federation Services (AD FS) (AD FS) i Windows Server kan du lägga till OpenID Connect och OAuth 2,0-baserad autentisering och auktorisering till din Microsoft Authentication Library för Java-app (MSAL for Java). När appen är integrerad kan din app autentisera användare i AD FS, federerade genom Azure AD. Mer information om scenarier finns i [AD FS scenarier för utvecklare](/windows-server/identity/ad-fs/ad-fs-development).

En app som använder MSAL för Java kommer att prata med Azure Active Directory (Azure AD) som sedan federerar till AD FS.

MSAL for Java ansluter till Azure AD, som loggar in användare som hanteras i Azure AD (hanterade användare) eller användare som hanteras av en annan identitets leverantör, till exempel AD FS (federerade användare). MSAL för Java vet inte att en användare är federerad. Det bara pratar med Azure AD.

Den [myndighet](msal-client-application-configuration.md#authority) som du använder i det här fallet är den vanliga utfärdaren (utfärdarens värdnamn + innehavare, vanliga eller organisationer).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Hämta en token interaktivt för en federerad användare

När du anropar `ConfidentialClientApplication.AcquireToken()` eller `PublicClientApplication.AcquireToken()` med `AuthorizationCodeParameters` eller `DeviceCodeParameters` , är användar upplevelsen vanligt vis:

1. Användaren anger sitt konto-ID.
2. Azure AD visar kortfattat "tar dig till din organisations sida" och användaren omdirigeras till inloggnings sidan för identitets leverantören. Inloggnings sidan är vanligt vis anpassad med organisationens logo typ.

De AD FS versioner som stöds i det här federerade scenariot är:
- Active Directory Federation Services (AD FS) FS v2
- Active Directory Federation Services (AD FS) v3 (Windows Server 2012 R2)
- Active Directory Federation Services (AD FS) v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Hämta en token via användar namn och lösen ord

När du hämtar en token med eller `ConfidentialClientApplication.AcquireToken()` `PublicClientApplication.AcquireToken()` `IntegratedWindowsAuthenticationParameters` eller med eller `UsernamePasswordParameters` , MSAL för Java får identitets leverantören kontakt utifrån användar namnet. MSAL for Java får en [SAML 1,1-token](reference-saml-tokens.md) -token från identitets leverantören, vilken den ger till gång till Azure AD som returnerar JSON Web token (JWT).

## <a name="next-steps"></a>Nästa steg

För det federerade fallet, se [konfigurera Azure Active Directory inloggnings beteende för ett program med hjälp av en princip för identifiering av start sfär](../manage-apps/configure-authentication-for-federated-users-portal.md)