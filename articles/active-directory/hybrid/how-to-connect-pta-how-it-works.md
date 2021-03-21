---
title: 'Azure AD Connect: direkt autentisering – hur det fungerar | Microsoft Docs'
description: Den här artikeln beskriver hur Azure Active Directory direktautentisering fungerar
services: active-directory
keywords: Azure AD Connect direktautentisering, installera Active Directory, nödvändiga komponenter för Azure AD, SSO, enkel inloggning
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996601"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Azure Active Directory direktautentisering: teknisk djupgående
Den här artikeln är en översikt över hur Azure Active Directory (Azure AD) passerar autentisering fungerar. Detaljerad teknisk information och säkerhets information finns i artikeln om [säkerhets djupet](how-to-connect-pta-security-deep-dive.md) .

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hur fungerar Azure Active Directory direktautentisering?

>[!NOTE]
>Som ett krav för direktautentisering för att fungera måste användarna vara etablerade i Azure AD från lokala Active Directory med hjälp av Azure AD Connect. Direktautentisering gäller inte enbart för molnbaserade användare.

När en användare försöker logga in i ett program som skyddas av Azure AD, och om direktautentisering har Aktiver ATS på klienten, inträffar följande steg:

1. Användaren försöker få åtkomst till ett program, till exempel [Outlook Web App](https://outlook.office365.com/owa/).
2. Om användaren inte redan är inloggad omdirigeras användaren till inloggnings sidan för Azure AD **-användare** .
3. Användaren anger sitt användar namn på inloggnings sidan för Azure AD och väljer sedan knappen **Nästa** .
4. Användaren anger sitt lösen ord på inloggnings sidan för Azure AD och väljer sedan knappen **Logga in** .
5. Azure AD, som tar emot en begäran om att logga in, placerar användar namn och lösen ord (krypteras med hjälp av den offentliga nyckeln för autentiserings agenter) i en kö.
6. En lokal autentiseringsnyckel hämtar användar namnet och det krypterade lösen ordet från kön. Observera att agenten inte ofta söker efter begär Anden från kön, men hämtar begär Anden via en företablerad permanent anslutning.
7. Agenten dekrypterar lösen ordet med hjälp av dess privata nyckel.
8. Agenten verifierar användar namnet och lösen ordet mot Active Directory med hjälp av vanliga Windows-API: er, vilket är en liknande mekanism som Active Directory Federation Services (AD FS) (AD FS) använder. Användar namnet kan antingen vara det lokala standard användar namnet, vanligt vis `userPrincipalName` eller något annat attribut som kon figurer ATS i Azure AD Connect (kallas `Alternate ID` ).
9. Den lokala Active Directory domänkontrollanten (DC) utvärderar begäran och returnerar lämpligt svar (lyckades, misslyckat, lösen ordet har upphört att gälla eller användaren utelåst) till agenten.
10. Authentication-agenten returnerar i sin tur det här svaret tillbaka till Azure AD.
11. Azure AD utvärderar svaret och svarar på användaren efter behov. Exempelvis signerar Azure AD användaren direkt eller begär Anden om Azure AD Multi-Factor Authentication.
12. Om användar inloggningen lyckas kan användaren komma åt programmet.

Följande diagram illustrerar alla komponenter och de steg som ingår:

![Direktautentisering](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Nästa steg
- [Aktuella begränsningar](how-to-connect-pta-current-limitations.md): Lär dig vilka scenarier som stöds och vilka som inte är det.
- [Snabbstart](how-to-connect-pta-quick-start.md): kom igång med Azure AD-direktautentisering.
- [Migrera från AD FS till vidarekoppling](https://aka.ms/adfstoPTADP) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md): Konfigurera funktionen för smart utelåsning på klienten för att skydda användar konton.
- [Vanliga frågor](how-to-connect-pta-faq.md)och svar: få svar på vanliga frågor.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen för direkt autentisering.
- [Djupgående säkerhets](how-to-connect-pta-security-deep-dive.md)information: få djupgående teknisk information om funktionen för direkt autentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Lär dig mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet för att fil nya funktions begär Anden.

