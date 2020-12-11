---
title: Autentiseringsmetoder och funktioner – Azure Active Directory
description: Lär dig mer om de olika autentiseringsmetoder och-funktioner som finns i Azure Active Directory för att förbättra och säkra inloggnings händelser
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: e239d9da4a083b50460ef746f9c538aa3081a577
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027330"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Vilka autentiserings- och verifieringsmetoder är tillgängliga i Azure Active Directory?

Som en del av inloggnings upplevelsen för konton i Azure Active Directory (Azure AD) finns det olika sätt som en användare kan autentisera sig på. Användar namn och lösen ord är det vanligaste sättet som en användare tidigare angav autentiseringsuppgifter. Med moderna autentiserings-och säkerhetsfunktioner i Azure AD bör det grundläggande lösen ordet kompletteras eller ersättas med säkrare autentiseringsmetoder.

![Tabell över metoder för starka och önskade autentiseringsmetoder i Azure AD](media/concept-authentication-methods/authentication-methods.png)

Metoder för lösen ords kryptering som Windows Hello, FIDO2 säkerhets nycklar och Microsoft Authenticator-appen tillhandahåller de säkraste inloggnings händelserna.

Azure AD Multi-Factor Authentication lägger till ytterligare säkerhet över med ett lösen ord när en användare loggar in. Användaren kan uppmanas att ange ytterligare typer av autentisering, t. ex. för att svara på ett push-meddelande, ange en kod från en program vara eller maskinvarubaserad token eller svara på ett SMS eller telefonsamtal.

För att förenkla användar upplevelsen och registrera dig för både MFA och SSPR rekommenderar vi att du [aktiverar kombinerad registrering av säkerhets information](howto-registration-mfa-sspr-combined.md). För återhämtning rekommenderar vi att du kräver att användarna registrerar flera autentiseringsmetoder. När en metod inte är tillgänglig för en användare under inloggnings-eller SSPR kan de välja att autentisera med en annan metod. Mer information finns i [skapa en elastisk åtkomst kontroll hanterings strategi i Azure AD](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Autentisering av autentiseringsmetod och säkerhet

När du distribuerar funktioner som Azure AD Multi-Factor Authentication i din organisation, granskar du tillgängliga autentiseringsmetoder. Välj de metoder som uppfyller eller överskrider dina krav när det gäller säkerhet, användbarhet och tillgänglighet. Använd om möjligt autentiseringsmetoder med den högsta säkerhets nivån.

Följande tabell beskriver säkerhets överväganden för tillgängliga autentiseringsmetoder. Tillgänglighet är en indikation på att användaren kan använda autentiseringsmetoden, inte av tjänstens tillgänglighet i Azure AD:

| Autentiseringsmetod          | Säkerhet | Användbarhet | Tillgänglighet |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello för företag     | Hög     | Hög      | Hög         |
| Microsoft Authenticator-appen    | Hög     | Hög      | Hög         |
| Säkerhets nyckel för FIDO2 (för hands version)   | Hög     | Hög      | Hög         |
| OATH-token för maskin vara (för hands version) | Medel   | Medel    | Hög         |
| OATH-programvaru-token           | Medel   | Medel    | Hög         |
| SMS                            | Medium   | Högt      | Medel       |
| Röst                          | Medel   | Medel    | Medel       |
| Lösenord                       | Lågt      | Högt      | Hög         |

Mer information om säkerhet finns i [sårbarheter och angrepps vektorer för autentisering](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> För flexibilitet och användbarhet rekommenderar vi att du använder Microsoft Authenticator-appen. Den här autentiseringsmetoden ger den bästa användar upplevelsen och flera lägen, t. ex. lösen ords lös, MFA push-meddelanden och OATH-koder.

## <a name="how-each-authentication-method-works"></a>Så här fungerar varje autentiseringsmetod

Vissa autentiseringsmetoder kan användas som primär faktor när du loggar in på ett program eller en enhet, till exempel att använda en FIDO2 säkerhets nyckel eller ett lösen ord. Andra autentiseringsmetoder är bara tillgängliga som en sekundär faktor när du använder Azure AD Multi-Factor Authentication eller SSPR.

Följande tabell beskriver när du kan använda en autentiseringsmetod under en inloggnings händelse:

| Metod                         | Primär autentisering | Sekundär autentisering  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello för företag     | Yes                    | Multifaktorautentisering                       |
| Microsoft Authenticator-appen    | Ja (för hands version)          | MFA och SSPR              |
| Säkerhets nyckel för FIDO2 (för hands version)   | Yes                    | Multifaktorautentisering                       |
| OATH-token för maskin vara (för hands version) | No                     | Multifaktorautentisering                       |
| OATH-programvaru-token           | No                     | Multifaktorautentisering                       |
| SMS                            | Ja (för hands version)          | MFA och SSPR              |
| Röstsamtal                     | No                     | MFA och SSPR              |
| Lösenord                       | Yes                    |                           |

Alla dessa autentiseringsmetoder kan konfigureras i Azure Portal och i allt större användning av [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Mer information om hur varje autentiseringsmetod fungerar finns i följande artiklar om olika koncept:

* [Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Microsoft Authenticator-appen](concept-authentication-authenticator-app.md)
* [Säkerhets nyckel för FIDO2 (för hands version)](concept-authentication-passwordless.md#fido2-security-keys)
* [OATH-token för maskin vara (för hands version)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [OATH-programvaru-token](concept-authentication-oath-tokens.md#oath-software-tokens)
* SMS- [inloggning (för hands version)](howto-authentication-sms-signin.md) och [verifiering](concept-authentication-phone-options.md#mobile-phone-verification)
* [Verifiering av röst samtal](concept-authentication-phone-options.md)
* Lösenord

> [!NOTE]
> I Azure AD är ett lösen ord ofta en av de primära autentiseringsmetoderna. Du kan inte inaktivera autentiseringsmetoden för lösen ord. Om du använder ett lösen ord som primär autentiserings faktor ökar du säkerheten för inloggnings händelser med hjälp av Azure AD Multi-Factor Authentication.

Följande ytterligare verifierings metoder kan användas i vissa scenarier:

* [Applösenord](howto-mfa-app-passwords.md) – används för gamla program som inte stöder modern autentisering och som kan konfigureras för Azure AD-Multi-Factor Authentication per användare.
* [Säkerhets frågor](concept-authentication-security-questions.md) – används endast för SSPR
* [E-postadress](concept-sspr-howitworks.md#authentication-methods) – används endast för SSPR

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [självstudien för självbetjäning för återställning av lösen ord (SSPR)][tutorial-sspr] och [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Mer information om SSPR-koncept finns i [hur Azure AD självbetjäning för återställning av lösen ord fungerar][concept-sspr].

Mer information om MFA-koncept finns i [hur Azure AD Multi-Factor Authentication fungerar][concept-mfa].

Läs mer om hur du konfigurerar autentiseringsmetoder med hjälp av [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Om du vill se vilka autentiseringsmetoder som används kan du läsa mer i [Azure AD Multi-Factor Authentication Authentication Method Analysis med PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
