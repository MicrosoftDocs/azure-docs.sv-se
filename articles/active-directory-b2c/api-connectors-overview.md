---
title: Om API-kopplingar i Azure AD B2C
description: 'Använd Azure Active Directory (Azure AD) API-kopplingar för att anpassa och utöka dina registrerade användar flöden med webb-API: er.'
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611529"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>Använd API-kopplingar för att anpassa och utöka registrering av användar flöden

> [!IMPORTANT]
> API-kopplingar för registrering är en allmän förhands gransknings funktion i Azure AD B2C. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="overview"></a>Översikt 
Som utvecklare eller IT-administratör kan du använda API-kopplingar för att integrera dina registrerade användar flöden med webb-API: er för att anpassa registrerings upplevelsen och integrera med externa system. Med API-kopplingar kan du till exempel:

- **Verifiera indata från användaren**. Verifiera mot felaktiga eller ogiltiga användar data. Du kan till exempel validera data som tillhandahålls av användaren mot befintliga data i ett externt data lager eller en lista över tillåtna värden. Om det är ogiltigt kan du be en användare att tillhandahålla giltiga data eller hindra användaren från att fortsätta registrerings flödet.
- **Integrera med ett anpassat godkännande arbets flöde**. Anslut till ett anpassat godkännande system för att hantera och begränsa skapandet av konton.
- **Skriv över användarattribut**. Formatera om eller tilldela ett värde till ett attribut som samlas in från användaren. Om en användare till exempel anger det första namnet i gemener eller versaler, kan du formatera namnet med enbart den första bokstaven versaler. 
- **Utföra identitets verifiering**. Använd en identitets verifierings tjänst för att lägga till ytterligare en säkerhets nivå för besluts fattande.
- **Kör anpassad affärs logik**. Du kan utlösa underordnade händelser i moln systemen för att skicka push-meddelanden, uppdatera företags databaser, hantera behörigheter, granska databaser och utföra andra anpassade åtgärder.

En API-anslutning ger Azure Active Directory med den information som behövs för att anropa API-slutpunkten genom att definiera HTTP-slutpunktens URL och autentisering för API-anropet. När du har konfigurerat en API-anslutning kan du aktivera den för ett särskilt steg i ett användar flöde. När en användare når det steget i registrerings flödet anropas API-kopplingen och materialiseras som en HTTP POST-begäran till ditt API, vilket skickar användar information ("anspråk") som nyckel/värde-par i en JSON-text. API-svaret kan påverka körningen av användar flödet. API-svaret kan till exempel blockera en användare från att registrera sig, be användaren att ange information igen eller skriva över och lägga till användarattribut.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Där du kan aktivera en API-koppling i ett användar flöde

Det finns två platser i ett användar flöde där du kan aktivera en API-koppling:

- När du har loggat in med en identitets leverantör
- Innan du skapar användaren

> [!IMPORTANT]
> I båda fallen anropas API-kopplingarna vid användar **registrering**, inte för inloggning.

### <a name="after-signing-in-with-an-identity-provider"></a>När du har loggat in med en identitets leverantör

En API-anslutning i det här steget i registrerings processen anropas omedelbart när användaren autentiseras med en identitets leverantör (som Google, Facebook, & Azure AD). Det här steget föregår ***sidan för attribut samling***, som är det formulär som visas för användaren att samla in användarattribut. Det här steget anropas inte om en användare registreras med ett lokalt konto. Följande är exempel på scenarier med API-anslutningar som du kan aktivera i det här steget:

- Använd e-postadressen eller den federerade identiteten som användaren angav för att söka efter anspråk i ett befintligt system. Returnera dessa anspråk från det befintliga systemet, Fyll i sidan samling för attribut och gör dem tillgängliga för att returnera i token.
- Implementera en lista över tillåtna eller blockerade baserade på sociala identiteter.

### <a name="before-creating-the-user"></a>Innan du skapar användaren

En API-anslutning i det här steget i registrerings processen anropas efter sidan Attribute Collection, om en sådan ingår. Det här steget anropas alltid innan ett användar konto skapas. Följande är exempel på scenarier som du kan aktivera vid följande tidpunkt under registreringen:

- Verifiera indata från användaren och be användaren att skicka in data igen.
- Blockera en användar registrering baserat på data som anges av användaren.
- Utföra identitets verifiering.
- Fråga externa system efter befintliga data om användaren att returnera dem i Application-token eller lagra dem i Azure AD.


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [lägger till en API-anslutning till ett användar flöde](add-api-connector.md)
- Kom igång med våra [exempel](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->