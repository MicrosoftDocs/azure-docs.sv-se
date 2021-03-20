---
title: Säkerhets aviseringar för Azure AD-roller i PIM – Azure AD | Microsoft Docs
description: Konfigurera säkerhets aviseringar för Azure AD-roller Privileged Identity Management i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a632c0e31de1c2d7e5417656d537e5f9f82ecfbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180497"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Konfigurera säkerhets aviseringar för Azure AD-roller i Privileged Identity Management

Privileged Identity Management (PIM) genererar aviseringar när det finns misstänkt eller osäker aktivitet i din Azure Active Directory (Azure AD) organisation. När en avisering utlöses visas den på Privileged Identity Management instrument panelen. Välj aviseringen om du vill visa en rapport med en lista över de användare eller roller som utlöste aviseringen.

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med en användare som har rollen [privilegierad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

  [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Följ stegen i den här artikeln för att undersöka säkerhets aviseringar för Azure AD-roller.

# <a name="new-version"></a>[Ny version](#tab/new)

![Skärm bild som visar sidan "aviseringar" med en lista över aviseringar och deras allvarlighets grad.](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>Säkerhetsaviseringar

I det här avsnittet visas alla säkerhets aviseringar för Azure AD-roller, tillsammans med hur du åtgärdar och förhindrar. Allvarlighets graden har följande betydelse:

- **Hög**: kräver omedelbar åtgärd på grund av en princip överträdelse.
- **Medels Tor**: kräver ingen omedelbar åtgärd, men signalerar en eventuell princip överträdelse.
- **Låg**: kräver ingen omedelbar åtgärd, men föreslår en princip ändring som föredras.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratörer använder inte sina privilegierade roller

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Användare som har tilldelats privilegierade roller som de inte behöver ökar risken för angrepp. Det är också enklare för angripare att befinna sig i konton som inte används aktivt. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som de inte behöver. |
| **Prevention (Skydd)** | Tilldela endast privilegierade roller till användare som har en affärs motivering. </br>Schemalägg vanliga [åtkomst granskningar](pim-how-to-start-security-review.md) för att kontrol lera att användarna fortfarande behöver åtkomst. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om en användare går över ett angivet antal dagar utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger det maximala antalet dagar, från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller kräver inte Multi-Factor Authentication för aktivering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Utan Multi-Factor Authentication kan komprometterade användare aktivera privilegierade roller. |
| **Så här löser du?** | Granska listan över roller och [Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för varje roll. |
| **Prevention (Skydd)** | [KRÄV MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **Åtgärd som åtgärdar åtgärder i portalen** | Gör Multi-Factor Authentication nödvändigt för aktivering av den privilegierade rollen. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organisationen har inte Azure AD Premium P2

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Den aktuella Azure AD-organisationen har inte Azure AD Premium P2. |
| **Så här löser du?** | Läs mer om [Azure AD-versioner](../fundamentals/active-directory-whatis.md). Uppgradera till Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegie rad roll

| | |
| --- | --- |
| **Allvarlighetsgrad** | Medel |
| **Varför får jag den här aviseringen?** | Konton i en privilegie rad roll har inte ändrat sitt lösen ord under de senaste 90 dagarna. Dessa konton kan vara tjänst eller delade konton som inte underhålls och är utsatta för angripare. |
| **Så här löser du?** | Granska kontona i listan. Om de inte längre behöver åtkomst tar du bort dem från deras privilegierade roller. |
| **Prevention (Skydd)** | Se till att konton som delas roterar starka lösen ord när det sker en ändring i användare som känner till lösen ordet. </br>Granska regelbundet konton med privilegierade roller med [åtkomst granskningar](pim-how-to-start-security-review.md) och ta bort roll tilldelningar som inte längre behövs. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Bästa praxis** | Konton för delade, tjänster och nödfalls åtkomst som autentiserar med ett lösen ord och som har tilldelats privilegierade administrativa roller som global administratör eller säkerhets administratör bör ha sina lösen ord roterade för följande fall:<ul><li>Efter en säkerhets incident som inbegriper missbruk eller komprometterande av administrativa rättigheter</li><li>När en användares behörigheter har ändrats så att de inte längre är administratörer (till exempel efter att en medarbetare som var administratör lämnar den eller lämnar organisationen)</li><li>Med jämna mellanrum (till exempel kvartals vis eller årliga), även om det inte fanns något känt intrång eller ändra till IT-personal</li></ul>Eftersom flera personer har åtkomst till dessa konto uppgifter bör autentiseringsuppgifterna roteras så att personer som har lämnat sina roller inte längre kan komma åt kontona. [Läs mer om att skydda konton](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller tilldelas utanför Privileged Identity Management

| | |
| --- | --- |
| **Allvarlighetsgrad** | Högt |
| **Varför får jag den här aviseringen?** | Privilegierade roll tilldelningar som görs utanför Privileged Identity Management övervakas inte korrekt och kan tyda på en aktiv attack. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som tilldelats utanför Privileged Identity Management. |
| **Prevention (Skydd)** | Undersök var användarna tilldelas privilegierade roller utanför Privileged Identity Management och förhindra framtida tilldelningar därifrån. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort användaren från den privilegierade rollen. |

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Global administratör är den högsta privilegierade rollen. Om en global administratör komprometteras får angriparen åtkomst till alla deras behörigheter, vilket innebär att hela systemet är utsatt för risk. |
| **Så här löser du?** | Granska användarna i listan och ta bort alla som inte absolut behöver rollen som global administratör. </br>Tilldela de här användarna lägre privilegierade roller i stället. |
| **Prevention (Skydd)** | Tilldela användare den minst privilegierade rollen som de behöver. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om två olika villkor uppfylls och du kan konfigurera båda. Först måste du komma åt ett visst tröskelvärde för globala administratörs roll tilldelningar. För det andra måste en viss procent andel av dina totala roll tilldelningar vara globala administratörer. Om du bara uppfyller en av dessa mått visas inte aviseringen. |
| **Minsta antal globala administratörer** | Den här inställningen anger antalet globala administratörs roll tilldelningar, från 2 till 100, som du anser vara för få för din Azure AD-organisation. |
| **Procent andel globala administratörer** | Den här inställningen anger den lägsta procent andelen av administratörer som är globala administratörer, från 0 till 100% under vilka du inte vill att din Azure AD-organisation ska vara DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Roller aktive ras för ofta

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Flera aktiveringar till samma privilegierade roll av samma användare är ett tecken på ett angrepp. |
| **Så här löser du?** | Granska användarna i listan och kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för deras privilegierade roll är tillräckligt lång för att de ska kunna utföra sina uppgifter. |
| **Prevention (Skydd)** | Kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för privilegierade roller är tillräckligt lång för att användarna ska kunna utföra sina uppgifter.</br>[Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för privilegierade roller som har konton som delas av flera administratörer. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Ej tillämpligt |
| **Utlösare** | Utlöses om en användare aktiverar samma privilegierade roll flera gånger inom en angiven period. Du kan konfigurera både tids perioden och antalet aktiveringar. |
| **Tidsram för aktiverings förnyelse** | Den här inställningen anger i dagar, timmar, minuter och sekund den tids period som du vill använda för att spåra misstänkta förnyelser. |
| **Antal aktiverings förnyelser** | Den här inställningen anger antalet aktiveringar från 2 till 100, där du vill bli meddelad inom den tidsram som du har valt. Du kan ändra den här inställningen genom att flytta skjutreglaget eller skriva ett tal i text rutan. |

## <a name="customize-security-alert-settings"></a>Anpassa säkerhets aviserings inställningar

På sidan **aviseringar** väljer du **Inställningar**.

![Sidan aviseringar med markerade inställningar](media/pim-how-to-configure-security-alerts/alert-settings.png)

Anpassa inställningarna för de olika aviseringarna så att de fungerar med dina miljö-och säkerhets mål.

![Inställnings sida för en avisering för att aktivera och konfigurera inställningar](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

![Azure AD-roller – aviserings fönstret visar avisering och allvarlighets grad](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>Utförlig information om säkerhetsaviseringar

I det här avsnittet visas alla säkerhets aviseringar för Azure AD-roller, tillsammans med hur du åtgärdar och förhindrar. Allvarlighets graden har följande betydelse:

- **Hög**: kräver omedelbar åtgärd på grund av en princip överträdelse.
- **Medels Tor**: kräver ingen omedelbar åtgärd, men signalerar en eventuell princip överträdelse.
- **Låg**: kräver ingen omedelbar åtgärd, men föreslår en princip ändring som föredras.

### <a name="administrators-arent-using-their-privileged-roles"></a>Administratörer använder inte sina privilegierade roller

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Användare som har tilldelats privilegierade roller som de inte behöver ökar risken för angrepp. Det är också enklare för angripare att befinna sig i konton som inte används aktivt. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som de inte behöver. |
| **Prevention (Skydd)** | Tilldela endast privilegierade roller till användare som har en affärs motivering. </br>Schemalägg vanliga [åtkomst granskningar](pim-how-to-start-security-review.md) för att kontrol lera att användarna fortfarande behöver åtkomst. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om en användare går över ett angivet antal dagar utan att aktivera en roll. |
| **Antal dagar** | Den här inställningen anger det maximala antalet dagar, från 0 till 100, som en användare kan gå utan att aktivera en roll.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Roller kräver inte Multi-Factor Authentication för aktivering

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Utan Multi-Factor Authentication kan komprometterade användare aktivera privilegierade roller. |
| **Så här löser du?** | Granska listan över roller och [Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för varje roll. |
| **Prevention (Skydd)** | [KRÄV MFA](pim-how-to-change-default-settings.md) för varje roll.  |
| **Åtgärd som åtgärdar åtgärder i portalen** | Gör Multi-Factor Authentication nödvändigt för aktivering av den privilegierade rollen. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>Organisationen har inte Azure AD Premium P2

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Den aktuella Azure AD-organisationen har inte Azure AD Premium P2. |
| **Så här löser du?** | Läs mer om [Azure AD-versioner](../fundamentals/active-directory-whatis.md). Uppgradera till Azure AD Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Potentiella inaktuella konton i en privilegie rad roll

| | |
| --- | --- |
| **Allvarlighetsgrad** | Medel |
| **Varför får jag den här aviseringen?** | Konton i en privilegie rad roll har inte ändrat sitt lösen ord under de senaste 90 dagarna. Dessa konton kan vara tjänst eller delade konton som inte underhålls och är utsatta för angripare. |
| **Så här löser du?** | Granska kontona i listan. Om de inte längre behöver åtkomst tar du bort dem från deras privilegierade roller. |
| **Prevention (Skydd)** | Se till att konton som delas roterar starka lösen ord när det sker en ändring i användare som känner till lösen ordet. </br>Granska regelbundet konton med privilegierade roller med [åtkomst granskningar](pim-how-to-start-security-review.md) och ta bort roll tilldelningar som inte längre behövs. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Bästa praxis** | Konton för delade, tjänster och nödfalls åtkomst som autentiserar med ett lösen ord och som har tilldelats privilegierade administrativa roller som global administratör eller säkerhets administratör bör ha sina lösen ord roterade för följande fall:<ul><li>Efter en säkerhets incident som inbegriper missbruk eller komprometterande av administrativa rättigheter</li><li>När en användares behörigheter har ändrats så att de inte längre är administratörer (till exempel efter att en medarbetare som var administratör lämnar den eller lämnar organisationen)</li><li>Med jämna mellanrum (till exempel kvartals vis eller årliga), även om det inte fanns något känt intrång eller ändra till IT-personal</li></ul>Eftersom flera personer har åtkomst till dessa konto uppgifter bör autentiseringsuppgifterna roteras så att personer som har lämnat sina roller inte längre kan komma åt kontona. [Läs mer](../roles/security-planning.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>Roller tilldelas utanför Privileged Identity Management

| | |
| --- | --- |
| **Allvarlighetsgrad** | Högt |
| **Varför får jag den här aviseringen?** | Privilegierade roll tilldelningar som görs utanför Privileged Identity Management övervakas inte korrekt och kan tyda på en aktiv attack. |
| **Så här löser du?** | Granska användarna i listan och ta bort dem från privilegierade roller som tilldelats utanför Privileged Identity Management. |
| **Prevention (Skydd)** | Undersök var användarna tilldelas privilegierade roller utanför Privileged Identity Management och förhindra framtida tilldelningar därifrån. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort användaren från den privilegierade rollen. |

### <a name="there-are-too-many-global-administrators"></a>Det finns för många globala administratörer

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Global administratör är den högsta privilegierade rollen. Om en global administratör komprometteras får angriparen åtkomst till alla deras behörigheter, vilket innebär att hela systemet är utsatt för risk. |
| **Så här löser du?** | Granska användarna i listan och ta bort alla som inte absolut behöver rollen som global administratör. </br>Tilldela de här användarna lägre privilegierade roller i stället. |
| **Prevention (Skydd)** | Tilldela användare den minst privilegierade rollen som de behöver. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Tar bort kontot från deras privilegierade roller. |
| **Utlösare** | Utlöses om två olika villkor uppfylls och du kan konfigurera båda. Först måste du uppnå ett visst tröskelvärde för globala administratörer. För det andra måste en viss procent andel av dina totala roll tilldelningar vara globala administratörer. Om du bara uppfyller en av dessa mått visas inte aviseringen. |
| **Minsta antal globala administratörer** | Den här inställningen anger antalet globala administratörer, från 2 till 100, som du anser vara för få för din Azure AD-organisation. |
| **Procent andel globala administratörer** | Den här inställningen anger den lägsta procent andelen av administratörer som är globala administratörer, från 0 till 100% under vilka du inte vill att din Azure AD-organisation ska vara DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>Roller aktive ras för ofta

| | |
| --- | --- |
| **Allvarlighetsgrad** | Låg |
| **Varför får jag den här aviseringen?** | Flera aktiveringar till samma privilegierade roll av samma användare är ett tecken på ett angrepp. |
| **Så här löser du?** | Granska användarna i listan och kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för deras privilegierade roll är tillräckligt lång för att de ska kunna utföra sina uppgifter. |
| **Prevention (Skydd)** | Kontrol lera att [aktiverings tiden](pim-how-to-change-default-settings.md) för privilegierade roller är tillräckligt lång för att användarna ska kunna utföra sina uppgifter.</br>[Kräv Multi-Factor Authentication](pim-how-to-change-default-settings.md) för privilegierade roller som har konton som delas av flera administratörer. |
| **Åtgärd som åtgärdar åtgärder i portalen** | Ej tillämpligt |
| **Utlösare** | Utlöses om en användare aktiverar samma privilegierade roll flera gånger inom en angiven period. Du kan konfigurera både tids perioden och antalet aktiveringar. |
| **Tidsram för aktiverings förnyelse** | Den här inställningen anger i dagar, timmar, minuter och sekund den tids period som du vill använda för att spåra misstänkta förnyelser. |
| **Antal aktiverings förnyelser** | Den här inställningen anger antalet aktiveringar från 2 till 100, där du vill bli meddelad inom den tidsram som du har valt. Du kan ändra den här inställningen genom att flytta skjutreglaget eller skriva ett tal i text rutan. |

## <a name="configure-security-alert-settings"></a>Konfigurera säkerhets aviserings inställningar

Du kan anpassa några av säkerhets aviseringarna i Privileged Identity Management att arbeta med organisationens behov och säkerhets mål. Följ de här stegen för att öppna säkerhets aviserings inställningarna:

1. Öppna **Privileged Identity Management** i Azure AD.

1. Välj **Azure AD-roller**.

1. Välj **Inställningar** och sedan **aviseringar**.

    ![Azure AD-roller – inställningar med valda aviseringar](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Välj ett aviserings namn för att konfigurera inställningen för den aviseringen.

    ![För den valda aviseringen, fönstret säkerhets varnings inställningar](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-roller i Privileged Identity Management](pim-how-to-change-default-settings.md)