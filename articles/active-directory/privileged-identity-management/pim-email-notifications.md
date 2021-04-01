---
title: E-postmeddelanden i PIM – Azure Active Directory | Microsoft Docs
description: Beskriver e-postaviseringar i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394738"
---
# <a name="email-notifications-in-pim"></a>E-postmeddelanden i PIM

Privileged Identity Management (PIM) kan du se när viktiga händelser inträffar i din Azure Active Directory (Azure AD)-organisation, till exempel när en roll tilldelas eller aktive ras. Privileged Identity Management håller dig informerad genom att skicka e-postmeddelanden till dig och andra deltagare. Dessa e-postmeddelanden kan också innehålla länkar till relevanta uppgifter, t. ex. aktivering eller förnyelse av en roll. Den här artikeln beskriver vad dessa e-postmeddelanden ser ut när de skickas och vem som tar emot dem.

## <a name="sender-email-address-and-subject-line"></a>Avsändar-e-postadress och ämnesrad

E-postmeddelanden som skickas från Privileged Identity Management för både Azure AD och Azures resurs roller har följande avsändar-e-post adress:

- E-post adress:  **Azure-noreply \@ Microsoft.com**
- Visnings namn: Microsoft Azure

Dessa e-postmeddelanden innehåller ett **PIM** -prefix på ämnes raden. Här är ett exempel:

- PIM: Alain Charon har tilldelats permanent rollen som säkerhets kopierings läsare

## <a name="notifications-for-azure-ad-roles"></a>Aviseringar för Azure AD-roller

Privileged Identity Management skickar e-postmeddelanden när följande händelser inträffar för Azure AD-roller:

- När en aktivering av privilegierade roller väntar på godkännande
- När en aktiverings förfrågan för en privilegie rad roll har slutförts
- När Azure AD Privileged Identity Management är aktiverat

Vem som får dessa e-postmeddelanden för Azure AD-roller beror på din roll, händelsen och meddelande inställningen:

| User | Roll aktiveringen väntar på godkännande | Begäran om roll aktivering har slutförts | PIM är aktiverat |
| --- | --- | --- | --- |
| Privilegie rad roll administratör</br>(Aktive rad/berättigad) | Ja</br>(endast om inga uttryckliga god kännare har angetts) | Ja* | Ja |
| Säkerhetsadministratör</br>(Aktive rad/berättigad) | Inga | Ja* | Ja |
| Global administratör</br>(Aktive rad/berättigad) | Inga | Ja* | Ja |

\*Om inställningen [ **meddelanden**](pim-how-to-change-default-settings.md#notifications) är inställd på **Aktivera**.

Följande visar ett exempel på ett e-postmeddelande som skickas när en användare aktiverar en Azure AD-roll för den fiktiva Contoso-organisationen.

![Nytt Privileged Identity Management e-postmeddelande för Azure AD-roller](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Veckovis Privileged Identity Management sammandrag av e-post för Azure AD-roller

Ett vecko Privileged Identity Management Sammanfattning av e-post för Azure AD-roller skickas till privilegierade roll administratörer, säkerhets administratörer och globala administratörer som har aktiverat Privileged Identity Management. Detta vecko Visa e-postmeddelande ger en ögonblicks bild av Privileged Identity Management aktiviteter för veckan samt privilegierade roll tilldelningar. Den är endast tillgänglig för Azure AD-organisationer i det offentliga molnet. Här är ett exempel på ett e-postmeddelande:

![Veckovis Privileged Identity Management sammandrag av e-post för Azure AD-roller](./media/pim-email-notifications/email-directory-weekly.png)

E-postmeddelandet innehåller fyra paneler:

| Panel | Beskrivning |
| --- | --- |
| **Användare aktiverade** | Antal gånger som användare har aktiverat sin berättigade roll i organisationen. |
| **Användare som gjorts permanenta** | Antalet gånger som användare med en berättigad tilldelning görs permanenta. |
| **Roll tilldelningar i Privileged Identity Management** | Antal gånger som användare tilldelas en berättigad roll i Privileged Identity Management. |
| **Roll tilldelningar utanför PIM** | Antal gånger som användare tilldelas en permanent roll utanför Privileged Identity Management (i Azure AD). |

I avsnittet **Översikt över dina främsta roller** visas de fem främsta rollerna i din organisation baserat på det totala antalet permanenta och kvalificerade administratörer för varje roll. Länken **ta åtgärd** öppnar PIM- [guiden](pim-security-wizard.md) där du kan konvertera permanenta administratörer till berättigade administratörer i batchar.

## <a name="email-timing-for-activation-approvals"></a>E-posttiming för aktiverings godkännanden

När användarna aktiverar sin roll och roll inställningen kräver godkännande, så får god kännare två e-postmeddelanden för varje godkännande:

- Begäran om att godkänna eller neka användarens aktiverings förfrågan (som skickats av motorn för förfrågnings godkännande)
- Användarens begäran har godkänts (skickas av motorn för förfrågnings godkännande)

Globala administratörer och privilegierade roll administratörer får också ett e-postmeddelande för varje godkännande:

- Användarens roll aktive ras (skickas av Privileged Identity Management)

De första två e-postmeddelanden som skickas av motorn för godkännande av begär Ande kan vara fördröjda. För närvarande tar 90% av e-postmeddelanden tre till tio minuter, men för 1%-kunder kan det vara mycket längre, upp till femton minuter.

Om en begäran om godkännande godkänns i Azure Portal innan den första e-postmeddelandet skickas, utlöses inte det första e-postmeddelandet och andra god kännare kommer inte att meddelas via e-post för begäran om godkännande. Det kan visas som om de inte fick något e-postmeddelande, men det är det förväntade beteendet.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-e-post för Azures resurs roller

Privileged Identity Management skickar e-post till ägare och användar åtkomst administratörer när följande händelser inträffar för Azure-resurs roller:

- När en roll tilldelning väntar på godkännande
- När en roll tilldelas
- När en roll snart upphör att gälla
- När en roll är kvalificerad att utöka
- När en roll förnyas av en användare
- När en begäran om att aktivera en roll är slutförd

Privileged Identity Management skickar e-post till slutanvändare när följande händelser inträffar för Azure-resurs roller:

- När en roll tilldelas till användaren
- När en användares roll har upphört att gälla
- När en användares roll utökas
- När en användares roll aktiverings förfrågan har slutförts

Följande visar ett exempel på ett e-postmeddelande som skickas när en användare tilldelas en Azure-resurs roll för den fiktiva Contoso-organisationen.

![Nytt Privileged Identity Management e-post för Azures resurs roller](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Godkänn eller neka begär Anden för Azure AD-roller i Privileged Identity Management](azure-ad-pim-approval-workflow.md)
