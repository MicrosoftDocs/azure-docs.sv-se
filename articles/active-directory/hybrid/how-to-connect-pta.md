---
title: 'Azure AD Connect: direktautentisering | Microsoft Docs'
description: Den här artikeln beskriver Azure Active Directory (Azure AD) genom strömnings autentisering och hur det tillåter Azure AD-inloggningar genom att verifiera användarnas lösen ord mot lokala Active Directory.
services: active-directory
keywords: Vad är Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279473"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Användarinloggning med Azure Active Directory-direktautentisering

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>Vad är Azure Active Directory direktautentisering?

Med hjälp av Azure Active Directory (Azure AD) genom strömnings autentisering kan användarna logga in på både lokala och molnbaserade program med samma lösen ord. Den här funktionen ger användarna en bättre upplevelse – ett lösenord mindre att komma ihåg, och kostnaderna för IT-supportavdelningen minskas eftersom användarna blir mindre benägna att glömma hur de ska logga in. När användarna loggar in med Azure AD, verifierar den här funktionen användarnas lösen ord direkt mot din lokala Active Directory.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Den här funktionen är ett alternativ till [synkronisering av lösen ords-hash i Azure AD](how-to-connect-password-hash-synchronization.md), vilket ger samma fördelar med att Cloud-autentisering används för organisationer. Vissa organisationer vill tvinga sina lokala Active Directory säkerhets-och lösen ords principer, kan dock välja att använda direktautentisering i stället. Läs [den här guiden](./choose-ad-authn.md) för en jämförelse av de olika inloggnings metoderna för Azure AD och hur du väljer rätt inloggnings metod för din organisation.

![Direkt autentisering i Azure AD](./media/how-to-connect-pta/pta1.png)

Du kan kombinera direktautentisering med funktionen för [sömlös enkel inloggning](how-to-connect-sso.md) . På det här sättet behöver de inte ange sina lösen ord för att logga in när användarna får åtkomst till program på sina företags datorer i företagets nätverk.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Viktiga fördelar med att använda Azure AD-direktautentisering

- *En utmärkt användarupplevelse*
  - Användarna använder samma lösen ord för att logga in i både lokala och molnbaserade program.
  - Användarna tillbringar mindre tid på att prata med IT-supportavdelningen för att lösa problem som rör lösen ord.
  - Användare kan utföra uppgifter för [lösen ords hantering](../authentication/concept-sspr-howitworks.md) i molnet via självbetjäning.
- *Enkelt att distribuera & administrera*
  - Det behövs inga komplexa lokala distributioner eller nätverks konfiguration.
  - Behöver bara en Lightweight-agent för att installeras lokalt.
  - Inga hanterings kostnader. Agenten får automatiskt förbättringar och fel korrigeringar.
- *Skydda*
  - Lokala lösen ord lagras aldrig i molnet i något formulär.
  - Skyddar dina användar konton genom att arbeta sömlöst med [villkorliga åtkomst principer i Azure AD](../conditional-access/overview.md), inklusive Multi-Factor Authentication (MFA), [blockera äldre autentisering](../conditional-access/concept-conditional-access-conditions.md) och genom att [filtrera bort lösen ords attacker med brute force](../authentication/howto-password-smart-lockout.md).
  - Agenten gör bara utgående anslutningar inifrån nätverket. Det finns därför inget krav på att installera agenten i ett perimeternätverk, även kallat DMZ.
  - Kommunikationen mellan en agent och Azure AD skyddas med certifikatbaserad autentisering. Dessa certifikat förnyas automatiskt med några månader av Azure AD.
- *Högt tillgänglighet*
  - Ytterligare agenter kan installeras på flera lokala servrar för att tillhandahålla hög tillgänglighet för inloggnings begär Anden.

## <a name="feature-highlights"></a>Funktions markeringar

- Har stöd för användar inloggning i alla webbläsarbaserade program och i Microsoft Office-klient program som använder [modern autentisering](https://aka.ms/modernauthga).
- Inloggnings användar namn kan antingen vara det lokala standard användar namnet ( `userPrincipalName` ) eller ett annat attribut som kon figurer ATS i Azure AD Connect (kallas `Alternate ID` ).
- Funktionen fungerar sömlöst med [villkorliga åtkomst](../conditional-access/overview.md) funktioner som Multi-Factor Authentication (MFA) för att skydda dina användare.
- Integrerad med molnbaserad [lösen ords hantering via självbetjäning](../authentication/concept-sspr-howitworks.md), inklusive tillbakaskrivning av lösen ord till lokala Active Directory och lösen ords skydd genom förbjuda vanliga lösen ord.
- Miljöer med flera skogar stöds om det finns skogs förtroenden mellan dina AD-skogar och om routning av namnsuffix har kon figurer ATS korrekt.
- Det är en kostnads fri funktion, och du behöver inte några betalda versioner av Azure AD för att använda den.
- Den kan aktive ras via [Azure AD Connect](whatis-hybrid-identity.md).
- Den använder en lätt lokal agent som lyssnar efter och svarar på begär Anden om lösen ords verifiering.
- Om du installerar flera agenter får du hög tillgänglighet för inloggnings begär Anden.
- Den [skyddar](../authentication/howto-password-smart-lockout.md) dina lokala konton mot bruten lösen ords attacker i molnet.

## <a name="next-steps"></a>Nästa steg

- [Snabb start](how-to-connect-pta-quick-start.md) – kom igång med Azure AD-direktautentisering.
- [Migrera från AD FS till vidarekoppling](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md) – konfigurera Smart utelåsnings kapacitet på klienten för att skydda användar konton.
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md) – lär dig vilka scenarier som stöds och vilka som inte är det.
- [Teknisk djupgående](how-to-connect-pta-how-it-works.md) – förstå hur den här funktionen fungerar.
- [Vanliga frågor](how-to-connect-pta-faq.md) och svar på vanliga frågor.
- [Felsök](tshoot-connect-pass-through-authentication.md) – lär dig hur du löser vanliga problem med funktionen.
- [Djupgående säkerhet](how-to-connect-pta-security-deep-dive.md) – ytterligare djupgående teknisk information om funktionen.
- [Azure AD sömlös SSO](how-to-connect-sso.md) – Läs mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – för att arkivera nya funktions begär Anden.