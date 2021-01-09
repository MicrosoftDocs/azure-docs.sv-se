---
title: Microsoft Graph åtgärder som stöds
titleSuffix: Azure AD B2C
description: Ett index för Microsoft Graph åtgärder som stöds för hantering av Azure AD B2C resurser, inklusive användare, användar flöden, identitets leverantörer, anpassade principer, princip nycklar med mera.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: fed1e31380381b864530b3fa0b9e8c0886737d04
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033616"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Microsoft Graph åtgärder som är tillgängliga för Azure AD B2C

Följande Microsoft Graph API-åtgärder stöds för hantering av Azure AD B2C resurser, inklusive användare, identitets leverantörer, användar flöden, anpassade principer och princip nycklar.

Varje länk i följande avsnitt riktar sig mot motsvarande sida i Microsoft Graph API-referens för åtgärden.

## <a name="user-management"></a>Användarhantering

- [Visa användare](/graph/api/user-list)
- [Skapa en konsument användare](/graph/api/user-post-users)
- [Hämta en användare](/graph/api/user-get)
- [Uppdatera en användare](/graph/api/user-update)
- [Ta bort en användare](/graph/api/user-delete)

Mer information om hur du hanterar Azure AD B2C användar konton med Microsoft Graph-API finns i [hantera Azure AD B2C användar konton med Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Hantering av användar telefonnummer

- [Lägg till](/graph/api/authentication-post-phonemethods)
- [Ta](/graph/api/b2cauthenticationmethodspolicy-get)
- [Uppdatera](/graph/api/b2cauthenticationmethodspolicy-update)
- [Ta bort](/graph/api/phoneauthenticationmethod-delete)

Mer information om hur du hanterar användares inloggnings telefonnummer med Microsoft Graph-API finns i [B2C-autentiseringsmetoder](/graph/api/resources/b2cauthenticationmethodspolicy).

## <a name="identity-providers-user-flow"></a>Identitets leverantörer (användar flöde)

Hantera de identitets leverantörer som är tillgängliga för dina användar flöden i Azure AD B2C-klienten.

- [Visa lista med identitets leverantörer som registrerats i Azure AD B2C klient organisationen](/graph/api/identityprovider-list)
- [Skapa en identitets leverantör](/graph/api/identityprovider-post-identityproviders)
- [Hämta en identitets leverantör](/graph/api/identityprovider-get)
- [Uppdatera identitets leverantör](/graph/api/identityprovider-update)
- [Ta bort en identitets leverantör](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Användarflöde

Konfigurera fördefinierade principer för registrering, inloggning, kombinerad registrering och inloggning, återställning av lösen ord och profil uppdatering.

- [Visa lista över användar flöden](/graph/api/identitycontainer-list-b2cuserflows)
- [Skapa ett användarflöde](/graph/api/identitycontainer-post-b2cuserflows)
- [Hämta ett användar flöde](/graph/api/b2cidentityuserflow-get)
- [Ta bort ett användar flöde](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>Anpassade principer

Med följande åtgärder kan du hantera principer för Azure AD B2C förtroende Framework, så kallade [anpassade principer](custom-policy-overview.md).

- [Lista alla förtroende Ramverks principer som kon figurer ATS i en klient](/graph/api/trustframework-list-trustframeworkpolicies)
- [Skapa förtroende Ramverks princip](/graph/api/trustframework-post-trustframeworkpolicy)
- [Läsa egenskaper för en befintlig förtroende Ramverks princip](/graph/api/trustframeworkpolicy-get)
- [Uppdatera eller skapa förtroende Ramverks princip.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Ta bort en befintlig princip för förtroende ramverk](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Principnycklar

I ramverket med identitets upplevelsen lagras hemligheter som refereras i en anpassad princip för att upprätta förtroende mellan komponenter. Dessa hemligheter kan vara symmetriska eller asymmetriska nycklar/värden. I Azure Portal visas dessa entiteter som **princip nycklar**.

Resursen på den översta nivån för princip nycklar i Microsoft Graph API är den [betrodda Ramverks nyckel uppsättningen](/graph/api/resources/trustframeworkkeyset). Varje nyckel **uppsättning** innehåller minst en **nyckel**. Skapa en nyckel genom att först skapa en tom nyckel uppsättning och sedan generera en nyckel i nyckel uppsättningen. Du kan skapa en manuell hemlighet, överföra ett certifikat eller en PKCS12 nyckel. Nyckeln kan vara en genererad hemlighet, en sträng som du definierar (till exempel Facebook-programmets hemlighet) eller ett certifikat som du överför. Om en nyckel uppsättning har flera nycklar är bara en av nycklarna aktiv.

### <a name="trust-framework-policy-keyset"></a>Princip uppsättning för förtroende ramverk

- [Visa en lista med de förtroende Framework-standarduppsättningarna](/graph/api/trustframework-list-keysets)
- [Skapa ett förtroende Framework-standarduppsättningar](/graph/api/trustframework-post-keysets)
- [Hämta en nyckel uppsättning](/graph/api/trustframeworkkeyset-get)
- [Uppdatera en förtroende Framework-gruppuppsättning](/graph/api/trustframeworkkeyset-update)
- [Ta bort ett förtroende Framework-gruppuppsättningar](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Princip nyckel för förtroende ramverk

- [Hämta den aktuella aktiva nyckeln i nyckel uppsättningen](/graph/api/trustframeworkkeyset-getactivekey)
- [Generera en nyckel i nyckel uppsättningen](/graph/api/trustframeworkkeyset-generatekey)
- [Ladda upp en sträng baserad hemlighet](/graph/api/trustframeworkkeyset-uploadsecret)
- [Ladda upp ett X. 509-certifikat](/graph/api/trustframeworkkeyset-uploadcertificate)
- [Ladda upp ett PKCS12-format certifikat](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Program

- [Lista program](/graph/api/application-list)
- [Skapa ett program](/graph/api/resources/application)
- [Uppdatera program](/graph/api/application-update)
- [Skapa servicePrincipal](/graph/api/resources/serviceprincipal)
- [Skapa oauth2Permission-beviljande](/graph/api/resources/oauth2permissiongrant)
- [Ta bort program](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Egenskaper för program tillägg

- [Egenskaper för List tillägg](/graph/api/application-list-extensionproperty)

Azure AD B2C tillhandahåller en katalog som kan innehålla 100 anpassade attribut per användare. För användar flöden hanteras de här tilläggs egenskaperna [med hjälp av Azure Portal](user-flow-custom-attributes.md). För anpassade principer skapar Azure AD B2C egenskapen åt dig, första gången principen skriver ett värde för egenskapen Extension.

## <a name="audit-logs"></a>Granskningsloggar

- [Lista gransknings loggar](/graph/api/directoryaudit-list)

Mer information om hur du kommer åt Azure AD B2C gransknings loggar med Microsoft Graph-API finns i [åtkomst Azure AD B2C gransknings loggar](view-audit-logs.md).