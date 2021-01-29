---
title: Begränsningar och begränsningar för Azure AD B2C tjänsten
titleSuffix: Azure AD B2C
description: Referens för tjänst begränsningar och begränsningar för Azure Active Directory B2C tjänst.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 866af8b992374492286f47357f108a01f35e560b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051049"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Begränsningar och begränsningar för Azure Active Directory B2C tjänsten

Den här artikeln innehåller användnings begränsningar och andra tjänst begränsningar för tjänsten Azure Active Directory B2C (Azure AD B2C).

## <a name="end-userconsumption-related-limits"></a>Begränsningar för slutanvändare/förbrukning

Följande tjänst gränser för slutanvändare gäller för alla autentiserings-och Authorization-protokoll som stöds av Azure AD B2C, inklusive SAML, Open ID Connect, OAuth2 och ROPC.

|Kategori |Gräns    |
|---------|---------|
|Antal begär Anden per IP-adress per Azure AD B2C klient       |6000/5min          |
|Totalt antal förfrågningar per Azure AD B2C klient     |12000/min          |

Antalet förfrågningar kan variera beroende på antalet katalog läsningar och skrivningar som inträffar under Azure AD B2C användar resan. En enkel inloggnings resa som läser från katalogen består till exempel av 1 begäran. Om inloggnings resan måste också uppdatera katalogen räknas den här åtgärden som en ytterligare begäran.

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C konfigurations gränser

I följande tabell visas gränserna för administrations konfiguration i Azure AD B2Cs tjänsten.

|Kategori  |Gräns  |
|---------|---------|
|Antal program per Azure AD B2C klient   |250           |
|Antal omfattningar per program        |1000          |
|Antal [anpassade attribut](user-profile-attributes.md#extension-attributes)   per användare <sup>1</sup>       |100         |
|Antal omdirigerings-URL: er per program       |100         |
|Antal utloggnings-URL: er per program        |1          |
|Sträng begränsning per attribut      |250 tecken          |
|Antal B2C-klienter per prenumeration      |20         |
|[Arvs](custom-policy-overview.md#inheritance-model) nivåer i anpassade principer     |10         |
|Antal principer per Azure AD B2C klient      |200          |
|Maximal princip fil storlek      |400 KB          |

<sup>1</sup> Se även [Azure AD-tjänstens gränser och begränsningar](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Microsoft Graph begränsnings vägledning](/graph/throttling.md) 
- Lär dig mer om [validerings skillnader för Azure AD B2C program](../active-directory/develop/supported-accounts-validation.md)













