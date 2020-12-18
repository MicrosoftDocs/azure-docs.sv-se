---
title: Lägg till en identitetsprovider – Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du lägger till en identitetsprovider till din Active Directory B2C-klient.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 12/07/2020
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a5e8914b170f972304098bf9b4d0022164d30777
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669099"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Lägg till en identitetsprovider till din Azure Active Directory B2C-klient

Du kan konfigurera Azure AD B2C så att användarna kan logga in till ditt program med autentiseringsuppgifter från externa sociala eller företags identitets leverantörer (IdP). Azure AD B2C stöder externa identitets leverantörer som Facebook, Microsoft-konto, Google, Twitter och alla identitets leverantörer som stöder OAuth 1,0-, OAuth 2,0-, OpenID Connect-och SAML-protokoll.

Med extern Identity Provider Federation kan du erbjuda konsumenterna möjlighet att logga in med sina befintliga sociala konton eller företags konton, utan att behöva skapa ett nytt konto för ditt program.

På sidan för registrering eller inloggning visar Azure AD B2C en lista över externa identitets leverantörer som användaren kan välja för inloggning. När de har valt en av de externa identitets leverantörerna tas de (omdirigeras) till den valda providerns webbplats för att slutföra inloggnings processen. När användaren har loggat in returneras de till Azure AD B2C för autentisering av kontot i ditt program.

![Mobilt inloggnings exempel med ett socialt konto (Facebook)](media/add-identity-provider/external-idp.png)

Du kan lägga till identitets leverantörer som stöds av Azure Active Directory B2C (Azure AD B2C) till dina [användar flöden](user-flow-overview.md) med hjälp av Azure Portal. Du kan också lägga till identitets leverantörer i dina [anpassade principer](custom-policy-get-started.md).

## <a name="select-an-identity-provider"></a>Välj en identitets leverantör

Du använder vanligt vis bara en identitets leverantör i dina program, men du har möjlighet att lägga till fler. I instruktions artiklarna nedan visas hur du skapar ett identitets leverantörs program, lägger till identitets leverantören i din klient organisation och lägger till identitets leverantören i ditt användar flöde eller en anpassad princip.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (en enda klient)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (flera klienter)](identity-provider-azure-ad-multi-tenant.md)
* [Facebook](identity-provider-facebook.md)
* [Allmän identitetsprovider](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft-konto](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
