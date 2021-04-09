---
title: Begränsningar i B2B-samarbete – Azure Active Directory | Microsoft Docs
description: Aktuella begränsningar för Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4f960819aa208dcc8d3e476fc45a766452b612c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168958"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Begränsningar i Azure AD B2B-samarbete
Azure Active Directory (Azure AD) B2B-samarbete omfattas för närvarande av begränsningarna som beskrivs i den här artikeln.

## <a name="possible-double-multi-factor-authentication"></a>Möjlig dubbel Multi-Factor Authentication
Med Azure AD B2B kan du tillämpa Multi-Factor Authentication i resurs organisationen (den bjudande organisationen). Orsakerna till den här metoden beskrivs i [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md). Om en partner redan har konfigurerat Multi-Factor Authentication och verkställs, kan deras användare behöva utföra autentiseringen en gång i sin hem organisation och sedan igen.

## <a name="instant-on"></a>Direkt
I B2B-samarbets flöden lägger vi till användare i katalogen och uppdaterar dem dynamiskt under inbjudan till inlösen, app-tilldelning och så vidare. Uppdateringar och skrivningar sker vanligt vis i en katalog instans och måste replikeras över alla instanser. Replikeringen slutförs när alla instanser har uppdaterats. Ibland när objektet skrivs eller uppdateras i en instans och anropet för att hämta det här objektet är till en annan instans, kan fördröjningar i replikeringen uppstå. Om det händer, uppdatera eller försök att hjälpa dig. Om du skriver en app med hjälp av vårt API, är försök med en viss säkerhets kopiering en bra, försvars metod för att minska det här problemet.

## <a name="azure-ad-directories"></a>Azure AD-kataloger
Azure AD B2B omfattas av Azure AD-tjänstens katalog gränser. Mer information om hur många kataloger en användare kan skapa och antalet kataloger som en användare eller gäst användare kan tillhöra finns i begränsningar [och begränsningar för Azure AD-tjänsten](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="national-clouds"></a>Nationella moln
[Nationella moln](../develop/authentication-national-cloud.md) är fysiskt isolerade instanser av Azure. B2B-samarbete stöds inte över nationella moln gränser. Om din Azure-klient till exempel finns i det offentliga, globala molnet kan du inte bjuda in en användare vars konto finns i ett nationellt moln. Om du vill samar beta med användaren ber du en annan e-postadress eller skapa ett medlems användar konto för dem i din katalog.

## <a name="azure-us-government-clouds"></a>Azure-myndigheter för amerikanska myndigheter
I Azure-molnet för amerikanska myndigheter stöds B2B-samarbete mellan klienter som är både i Azure-molnet för amerikanska myndigheter och som båda stöder B2B-samarbete. Azure amerikanska myndigheter som stöder B2B-samarbete kan även samar beta med sociala användare med hjälp av Microsoft eller Google-konton. Om du bjuder in en användare utanför dessa grupper (till exempel om användaren finns i en klient som inte är en del av Azures regerings moln eller ännu inte stöder B2B-samarbete) Miss lyckas inbjudan eller också kan användaren inte lösa in inbjudan. Mer information om andra begränsningar finns i [Azure Active Directory Premium P1-och P2-variationer](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Hur kan jag se om B2B-samarbete är tillgängligt i min Azure-klient för amerikanska myndigheter?
Gör så här om du vill ta reda på om din Azure-klient för amerikanska myndigheter stöder B2B-samarbete:

1. I en webbläsare går du till följande URL och ersätter klient namnet för *&lt; tenantname &gt;*:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Sök `"tenant_region_scope"` i JSON-svaret:

   - Om `"tenant_region_scope":"USGOV”` det visas stöds B2B.
   - Om `"tenant_region_scope":"USG"` det visas stöds inte B2B.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Delegera B2B-samarbets inbjudningar](delegate-invitations.md)