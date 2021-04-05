---
title: Identitets data lagring för Europeiska kunder – Azure AD
description: Lär dig mer om var Azure Active Directory lagrar identitets-relaterade data för sina europeiska kunder.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836945"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identitets data lagring för Europeiska kunder i Azure Active Directory
Identitets data lagras av Azure AD på en geografisk plats baserat på den adress som tillhandahålls av din organisation när du prenumererar på en Microsoft Online-tjänst, till exempel Microsoft 365 och Azure. Information om var dina identitets data lagras, kan du använda avsnittet var finns [dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) i Microsoft säkerhets Center.

För kunder som tillhandahöll en adress i Europa, behåller Azure AD de flesta identitets data inom de europeiska data centren. Det här dokumentet innehåller information om alla data som lagras utanför Europa av Azure AD-tjänster.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

För molnbaserad Azure AD-Multi-Factor Authentication slutförs autentiseringen i det närmaste data centret för användaren. Data Center för Azure AD Multi-Factor Authentication finns i Nordamerika, Europa och Asien och stillahavsområdet.

* Multi-Factor Authentication som använder telefonsamtal kommer från amerikanska data Center och dirigeras av globala leverantörer.
* Multi-Factor Authentication med SMS dirigeras av globala providers.
* Multi-Factor Authentication-begäranden som använder Microsoft Authenticator app-push-meddelanden som kommer från EU-datacentren bearbetas i EU-datacentren.
    * Enhets leverantörer – vissa tjänster, t. ex. Apple Push-meddelanden, kan vara utanför Europa.
* Multi-Factor Authentication-förfrågningar med OATH-koder som härstammar från EU-datacenter verifieras i EU.

Mer information om vilka användar uppgifter som samlas in av Azure Multi-Factor Authentication-server (MFA Server) och molnbaserad Azure AD MFA finns i [azure Multi-Factor Authentication User Data Collection](../authentication/howto-mfa-reporting-datacollection.md).

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>Lösenordsbaserade enkla Sign-On för företags program
 
Om en kund skapar ett nytt företags program (oavsett om det sker via Azure AD Gallery eller utanför galleriet) och aktiverar lösenordsbaserad SSO, lagras programmets inloggnings-URL och anpassade inloggnings fält för registrering lagras i USA. Mer information om den här funktionen finns i [Konfigurera lösenordsbaserad enkel inloggning](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C princip konfigurations data och nyckel behållare lagras i amerikanska data Center. Dessa innehåller inte några användares personliga data. Mer information om principkonfigurationer finns i artikeln om [inbyggda principer i Azure Active Directory B2C](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B lagrar inbjudningar med Lös in länk-och omdirigerings-URL-information i amerikanska data Center. Dessutom lagras e-postadresserna till användare som avbryter prenumerationen på att ta emot B2B-inbjudningar också i amerikanska data Center.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure AD DS lagrar användardata på samma plats som kundens valda Azure Virtual Network. Om nätverket finns utanför Europa replikeras och lagras alltså dessa data utanför Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federation i Microsoft Exchange Server 2013
    
- Program identifierare (AppID) – ett unikt nummer som genereras av Azure Active Directory Authentication system för att identifiera Exchange-organisationer.
- Lista över godkända federerade domäner för program
- Offentlig nyckel för programmets Token-signering 

Mer information om Federation i Microsoft Exchange Server finns i hjälp artikeln [Federation: Exchange 2013](/exchange/federation-exchange-2013-help) .


## <a name="other-considerations"></a>Ytterligare överväganden

Tjänster och program som integreras med Azure AD har åtkomst till identitets data. Utvärdera varje tjänst och program som du använder för att avgöra hur identitets data bearbetas av den specifika tjänsten och programmet och om de uppfyller företagets data lagrings krav.

Läs mer om Microsoft-tjänsternas datahemvist i avsnittet [Var finns dina data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) på Microsoft Trust Center.

## <a name="next-steps"></a>Nästa steg
Mer information om de funktioner och funktioner som beskrivs ovan finns i följande artiklar:
- [Vad är Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)

- [Lösen ords återställning via självbetjäning i Azure AD](../authentication/concept-sspr-howitworks.md)

- [Vad är Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [Vad är Azure AD B2B-samarbete?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)