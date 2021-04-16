---
title: Autentiseringsmetoder och -funktioner – Azure Active Directory
description: Lär dig mer om de olika autentiseringsmetoder och funktioner som är Azure Active Directory för att förbättra och skydda inloggningshändelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: b4d69157f4544daad962cca15e53802e7b912399
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530430"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Vilka autentiserings- och verifieringsmetoder är tillgängliga i Azure Active Directory?

Som en del av inloggningen för konton i Azure Active Directory (Azure AD) finns det olika sätt som en användare kan autentisera sig på. Ett användarnamn och lösenord är det vanligaste sättet som en användare tidigare skulle ange autentiseringsuppgifter på. Med moderna autentiserings- och säkerhetsfunktioner i Azure AD bör det grundläggande lösenordet ersättas med säkrare autentiseringsmetoder.

![Tabell över styrkor och önskade autentiseringsmetoder i Azure AD](media/concept-authentication-methods/authentication-methods.png)

Lösenordslösa autentiseringsmetoder som Windows Hello, FIDO2-säkerhetsnycklar och Microsoft Authenticator-appen tillhandahåller de säkraste inloggningshändelserna.

Azure AD Multi-Factor Authentication (MFA) lägger till ytterligare säkerhet över att bara använda ett lösenord när en användare loggar in. Användaren kan uppmanas att ange ytterligare former av autentisering, till exempel att svara på ett push-meddelande, ange en kod från en programvaru- eller maskinvarutoken eller svara på ett SMS eller telefonsamtal.

För att förenkla användarupplevelsen och registrera dig för både MFA och självbetjäning av lösenordsåterställning (SSPR) rekommenderar vi att du aktiverar kombinerad registrering [av säkerhetsinformation.](howto-registration-mfa-sspr-combined.md) För återhämtning rekommenderar vi att du kräver att användarna registrerar flera autentiseringsmetoder. När en metod inte är tillgänglig för en användare under inloggningen eller SSPR kan de välja att autentisera med en annan metod. Mer information finns i Skapa [en strategi för hantering av elastisk åtkomstkontroll i Azure AD.](concept-resilient-controls.md)

Här är en [video som vi](https://www.youtube.com/watch?v=LB2yj4HSptc&feature=youtu.be) har skapat för att hjälpa dig att välja den bästa autentiseringsmetoden för att skydda din organisation.

## <a name="authentication-method-strength-and-security"></a>Styrka och säkerhet för autentiseringsmetod

När du distribuerar funktioner som Azure AD Multi-Factor Authentication i din organisation granskar du de tillgängliga autentiseringsmetoderna. Välj de metoder som uppfyller eller överskrider dina krav vad gäller säkerhet, användbarhet och tillgänglighet. Använd om möjligt autentiseringsmetoder med högsta säkerhetsnivå.

I följande tabell beskrivs säkerhetsöverväganden för tillgängliga autentiseringsmetoder. Tillgänglighet är en indikation på att användaren kan använda autentiseringsmetoden, inte tjänstens tillgänglighet i Azure AD:

| Autentiseringsmetod          | Säkerhet | Användbarhet | Tillgänglighet |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello för företag     | Högt     | Högt      | Högt         |
| Microsoft Authenticator-appen    | Högt     | Högt      | Högt         |
| FIDO2-säkerhetsnyckel             | Högt     | Högt      | Högt         |
| OATH-maskinvarutoken (förhandsversion) | Medel   | Medel    | Högt         |
| OATH-programvarutoken           | Medel   | Medel    | Högt         |
| SMS                            | Medel   | Högt      | Medel       |
| Röst                          | Medel   | Medel    | Medel       |
| Lösenord                       | Lågt      | Högt      | Högt         |

Den senaste informationen om säkerhet finns i våra blogginlägg:

- [Det är dags att lägga på telefontransport för autentisering](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
- [Sårbarheter i autentisering och attackvektorer](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)

> [!TIP]
> För flexibilitet och användbarhet rekommenderar vi att du använder Microsoft Authenticator appen. Den här autentiseringsmetoden ger den bästa användarupplevelsen och flera lägen, till exempel lösenordslösa, MFA-push-meddelanden och OATH-koder.

## <a name="how-each-authentication-method-works"></a>Så här fungerar varje autentiseringsmetod

Vissa autentiseringsmetoder kan användas som den primära faktorn när du loggar in på ett program eller en enhet, till exempel med hjälp av en FIDO2-säkerhetsnyckel eller ett lösenord. Andra autentiseringsmetoder är endast tillgängliga som en sekundär faktor när du använder Azure AD Multi-Factor Authentication eller SSPR.

I följande tabell beskrivs när en autentiseringsmetod kan användas under en inloggningshändelse:

| Metod                         | Primär autentisering | Sekundär autentisering  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello för företag     | Yes                    | Multifaktorautentisering                       |
| Microsoft Authenticator-appen    | Yes                    | MFA och SSPR              |
| FIDO2-säkerhetsnyckel             | Yes                    | Multifaktorautentisering                       |
| OATH-maskinvarutoken (förhandsversion) | No                     | Multifaktorautentisering                       |
| OATH-programvarutoken           | No                     | Multifaktorautentisering                       |
| SMS                            | Yes                    | MFA och SSPR              |
| Röstsamtal                     | No                     | MFA och SSPR              |
| Lösenord                       | Yes                    |                           |

Alla dessa autentiseringsmetoder kan konfigureras i Azure Portal och i allt högre grad med hjälp av [Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

Mer information om hur varje autentiseringsmetod fungerar finns i följande separata konceptuella artiklar:

* [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator-appen](concept-authentication-authenticator-app.md)
* [FIDO2-säkerhetsnyckel](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH-maskinvarutoken (förhandsversion)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH-programvarutoken](concept-authentication-oath-tokens.md#oath-software-tokens)
* [SMS-inloggning och](howto-authentication-sms-signin.md) [verifiering](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verifiering av röstsamtal](concept-authentication-phone-options.md)
* Lösenord

> [!NOTE]
> I Azure AD är ett lösenord ofta en av de primära autentiseringsmetoderna. Du kan inte inaktivera autentiseringsmetoden för lösenord. Om du använder ett lösenord som primär autentiseringsfaktor ökar du säkerheten för inloggningshändelser med hjälp av Azure AD Multi-Factor Authentication.

Följande ytterligare verifieringsmetoder kan användas i vissa scenarier:

* [Applösenord](howto-mfa-app-passwords.md) – används för gamla program som inte stöder modern autentisering och som kan konfigureras för Azure AD Multi-Factor Authentication per användare.
* [Säkerhetsfrågor –](concept-authentication-security-questions.md) används endast för SSPR
* [E-postadress](concept-sspr-howitworks.md#authentication-methods) – används endast för SSPR

## <a name="next-steps"></a>Nästa steg

Kom igång genom att gå [självstudien för självbetjäning av lösenordsåterställning (SSPR)][tutorial-sspr] och [Azure AD Multi-Factor Authentication.][tutorial-azure-mfa]

Mer information om SSPR-begrepp finns i [Så här fungerar självbetjäning av lösenordsåterställning i Azure AD.][concept-sspr]

Mer information om MFA-begrepp finns i [Så här fungerar Azure AD Multi-Factor Authentication.][concept-mfa]

Läs mer om hur du konfigurerar autentiseringsmetoder med [hjälp av Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).

Information om vilka autentiseringsmetoder som används finns i [Azure AD Multi-Factor Authentication-autentiseringsmetodanalys med PowerShell.](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
