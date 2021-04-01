---
title: 'Azure AD Connect: direkt autentisering – aktuella begränsningar | Microsoft Docs'
description: I den här artikeln beskrivs aktuella begränsningar i Azure Active Directory-direktautentisering (Azure AD)
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
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280204"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory direktautentisering: aktuella begränsningar

>[!IMPORTANT]
>Azure Active Directory (Azure AD) genom strömnings autentisering är en kostnads fri funktion och du behöver inte några betalda versioner av Azure AD för att använda den. Direkt autentisering är bara tillgängligt i den världs omfattande instansen av Azure AD och inte i [Microsoft Azure Tyskland molnet](https://www.microsoft.de/cloud-deutschland) eller [Microsoft Azure Government molnet](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scenarier som stöds

Följande scenarier stöds:

- Användar inloggningar till webb läsar program.
- Användar inloggningar till Outlook-klienter med äldre protokoll som Exchange ActiveSync, EAS, SMTP, POP och IMAP.
- Användar inloggningar till äldre Office-klientprogram och Office-program som stöder [modern autentisering](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Office 2013-och 2016-versioner.
- Användar inloggningar till äldre protokoll program, till exempel PowerShell version 1,0 och andra.
- Azure AD-anslutningar för Windows 10-enheter.
- Applösenord för Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds _inte_ :

- Identifiering av användare med [läckta autentiseringsuppgifter](../identity-protection/overview-identity-protection.md).
- Azure AD Domain Services kräver att hash-synkronisering av lösen ord är aktiverat på klienten. Klienter som använder _direkt autentisering fungerar_ därför inte för scenarier som behöver Azure AD Domain Services.
- Direkt autentisering är inte integrerat med [Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Som en lösning _för scenarier som_ inte stöds (förutom Azure AD Connect Health-integrering) aktiverar du synkronisering av lösen ords-hash på sidan [valfria funktioner](how-to-connect-install-custom.md#optional-features) i guiden Azure AD Connect.
> 
> [!NOTE]
> Om du aktiverar hash-synkronisering av lösen ord får du möjlighet att redundansväxla autentisering om den lokala infrastrukturen avbryts. Den här redundansväxlingen från direktautentisering till hash-synkronisering av lösen ord är inte automatisk. Du måste byta inloggnings metod manuellt med hjälp av Azure AD Connect. Om servern som kör Azure AD Connect kraschar behöver du hjälp med att Microsoft Support för att inaktivera direktautentisering.

## <a name="next-steps"></a>Nästa steg
- [Snabb start](how-to-connect-pta-quick-start.md): kom igång med Azure AD-direktautentisering.
- [Migrera från AD FS till vidarekoppling](https://aka.ms/ADFSTOPTADPDownload) – en detaljerad guide för att migrera från AD FS (eller andra Federations tekniker) till vidarekoppling.
- [Smart utelåsning](../authentication/howto-password-smart-lockout.md): Lär dig hur du konfigurerar funktionen för smart utelåsning på klienten för att skydda användar konton.
- [Teknisk djupgående](how-to-connect-pta-how-it-works.md): förstå hur funktionen för direkt autentisering fungerar.
- [Vanliga frågor](how-to-connect-pta-faq.md)och svar: få svar på vanliga frågor om funktionen för direkt autentisering.
- [Felsöka](tshoot-connect-pass-through-authentication.md): Lär dig hur du löser vanliga problem med funktionen för direkt autentisering.
- [Djupgående säkerhets](how-to-connect-pta-security-deep-dive.md)information: få djupgående teknisk information om funktionen för direkt autentisering.
- [Azure AD sömlös SSO](how-to-connect-sso.md): Lär dig mer om den här kompletterande funktionen.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Använd Azure Active Directory-forumet för att fil nya funktions begär Anden.
