---
title: Azure AD Multi-Factor Authentication för din organisation – Azure Active Directory
description: Lär dig mer om de tillgängliga funktionerna i Azure AD Multi-Factor Authentication för din organisation baserat på din licens modell
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836750"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Översikt över Azure AD Multi-Factor Authentication för din organisation

Det finns flera sätt att aktivera Azure AD Multi-Factor Authentication för dina Azure Active Directory (AD)-användare baserat på de licenser som organisationen äger. 

![Undersök signaler och Använd MFA vid behov](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Utifrån våra studier är ditt konto mer än 99,9% mindre troligt att bli komprometterat om du använder Multi-Factor Authentication (MFA).

Så hur kan din organisation aktivera MFA även kostnads fritt innan du blir statistik?

## <a name="free-option"></a>Kostnads fritt alternativ

Kunder som utnyttjar de kostnads fria fördelarna med Azure AD kan använda [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md) för att aktivera Multi-Factor Authentication i sin miljö.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 eller E5

För kunder med Microsoft 365 finns det två alternativ:

* Azure AD Multi-Factor Authentication antingen aktive ras eller inaktive ras för alla användare, för alla inloggnings händelser. Det finns ingen möjlighet att endast aktivera Multi-Factor Authentication för en delmängd av användare eller endast i vissa scenarier. Hanteringen sker via Office 365-portalen. 
* Uppgradera till Azure AD Premium P1 eller P2 och Använd villkorlig åtkomst för att få en bättre användar upplevelse. Mer information finns i skydda Microsoft 365 resurser med Multi-Factor Authentication.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

För kunder med Azure AD Premium P1 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E3, Microsoft 365 F1 eller Microsoft 365 E3: 

Använd [villkorlig åtkomst i Azure AD](../authentication/tutorial-enable-azure-mfa.md) för att uppmana användarna att använda Multi-Factor Authentication under vissa scenarier eller händelser så att de passar dina affärs behov.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

För kunder med Azure AD Premium P2 eller liknande licenser som innehåller den här funktionen, till exempel Enterprise Mobility + Security E5 eller Microsoft 365 E5: 

Ger den starkaste säkerhets positionen och förbättrad användar upplevelse. Lägger till [riskfylld villkorlig åtkomst](../conditional-access/howto-conditional-access-policy-risk.md) till de Azure AD Premium P1-funktioner som anpassas efter användares mönster och minimerar Multi-Factor Authentication-prompter.

## <a name="authentication-methods"></a>Autentiseringsmetoder

| Metod | Standardinställningar för säkerhet | Alla andra metoder |
| --- | --- | --- |
| Meddelande via mobilapp | X | X |
| Verifierings kod från mobilapp eller maskinvaru-token |   | X |
| Textmeddelande till telefon |   | X |
| Ring till telefon |   | X |

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i självstudien för att [skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Mer information om licensiering finns i [funktioner och licenser för Azure AD Multi-Factor Authentication](../authentication/concept-mfa-licensing.md).
