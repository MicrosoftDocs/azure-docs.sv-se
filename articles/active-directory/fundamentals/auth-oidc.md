---
title: OpenID Connect-autentisering med Azure Active Directory
description: Arkitektur vägledning för att uppnå OpenID Connect-autentisering med Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168669"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>OpenID Connect-autentisering med Azure Active Directory

OpenID Connect (OIDC) är ett autentiseringsprotokoll baserat på OAuth2-protokollet (som används för auktorisering). OIDC använder standardiserade meddelande flöden från OAuth2 för att tillhandahålla identitets tjänster. 

Design målet för OIDC är "göra enkla saker enkla och komplicerade saker som möjligt". Med OIDC kan utvecklare autentisera sina användare på webbplatser och appar utan att behöva äga och hantera lösen ords filer. Detta ger app Builder ett säkert sätt att verifiera identiteten för personen som för närvarande använder webbläsaren eller den inbyggda appen som är ansluten till programmet.

Autentiseringen av användaren måste ske hos en identitets leverantör där användarens session eller autentiseringsuppgifter ska kontrol leras. Du behöver en betrodd agent för att göra det. Inbyggda appar startar vanligt vis system webbläsare för detta ändamål. Inbäddade vyer betraktas som ej betrodda eftersom det inte finns något att hindra appen från att snooping använda användar lösen ordet. 

Förutom autentiseringen kan användaren bli ombedd att ange medgivande. Medgivande är användarens uttryckliga behörighet att tillåta ett program att komma åt skyddade resurser. Medgivande skiljer sig från autentisering eftersom medgivande bara måste anges en gång för en resurs. Medgivande förblir giltigt tills användaren eller administratören manuellt återkallar tilldelningen. 

## <a name="use-when"></a>Använd när

Det finns ett behov av användar medgivande och för webb inloggning.

![arkitektur diagram](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>System komponenter

* **Användare**: begär en tjänst från programmet.

* **Betrodd agent**: komponenten som användaren interagerar med. Den här betrodda agenten är vanligt vis en webbläsare.

* **Program**: programmet eller resurs servern är den plats där resursen eller data finns. Den litar på identitets leverantören för att på ett säkert sätt autentisera och auktorisera den betrodda agenten. 

* **Azure AD**: OIDC-providern, som även kallas identitets leverantören, hanterar säkert vad som helst med användarens information, deras åtkomst och förtroende relationer mellan parter i ett flöde. Den autentiserar användarens identitet, beviljar och återkallar åtkomst till resurser och utfärdar token. 

## <a name="implement-oidc-with-azure-ad"></a>Implementera OIDC med Azure AD

* [Integrera program med Azure AD](../saas-apps/tutorial-list.md) 

* [OAuth 2,0-och OpenID Connect-protokoll på Microsoft Identity Platform](../develop/active-directory-v2-protocols.md) 

* [Microsoft Identity Platform och OpenID Connect-protokoll](../develop/v2-protocols-oidc.md) 

* [Webb inloggning med OpenID Connect i Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Skydda ditt program med OpenID Connect och Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

