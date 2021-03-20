---
title: Vad är Privileged Identity Management? – Azure AD | Microsoft Docs
description: Innehåller en översikt över Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370193"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Vad är Azure AD Privileged Identity Management?

 Privileged Identity Management (PIM) är en tjänst i Azure Active Directory (Azure AD) som gör att du kan hantera, kontrol lera och övervaka åtkomsten till viktiga resurser i din organisation. Resurserna omfattar resurser i Azure AD, Azure och andra Microsoft Online Services, till exempel Microsoft 365 eller Microsoft Intune. Följande video innehåller en introduktion till viktiga PIM-koncept och-funktioner.
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>Skäl att använda

Organisationer behöver minimera antalet personer som har åtkomst till skyddad information eller skyddade resurser för att därigenom minska risken för att användare som vill vålla skada får åtkomst till informationen eller att behöriga användare oavsiktligt påverkar känsliga resurser. Användare behöver dock fortfarande utföra privilegierade åtgärder i Azure AD, Azure, Microsoft 365 eller SaaS-appar. Organisationer kan ge användare just-in-Time-privilegierad åtkomst till Azure-resurser och Azure AD. Det finns ofta behov av att övervaka vad användarna gör med sina administratörsprivilegier.

## <a name="what-does-it-do"></a>Vad gör det?

Privileged Identity Management tillhandahåller tidsbaserad och godkännande-baserad roll aktivering för att minimera riskerna med alltför stora, onödiga eller felanvända åtkomst behörigheter för resurser som du bryr dig om. Här följer några av de viktigaste funktionerna i Privileged Identity Management:

- Ge privilegierad **just-in-time**-åtkomst till Azure AD och Azure-resurser
- Tilldela **tidsbunden** åtkomst till resurser med hjälp av start- och slutdatum
- Kräv **godkännande** för att aktivera privilegierade roller
- Framtvinga **multifaktorautentisering** för aktivering av roller
- Använd **motivering** för att förstå varför användare aktiverar
- Få **meddelanden** när privilegierade roller aktiveras
- Utför **åtkomstgranskningar** för att se till att användare fortfarande behöver ha vissa roller
- Ladda ned **granskningshistorik** för intern eller extern granskning

## <a name="what-can-i-do-with-it"></a>Vad kan jag göra med det?

När du har konfigurerat Privileged Identity Management visas alternativ för **aktiviteter**, **hantering** och **aktivitet** på den vänstra navigerings menyn. Som administratör väljer du mellan alternativ, till exempel hantering av **Azure AD-roller**, hantering av resurs roller i **Azure** eller privilegierade åtkomst grupper. När du väljer vad du vill hantera visas lämplig uppsättning alternativ för det alternativet.

![Skärm bild av Privileged Identity Management i Azure Portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Vem kan göra vad?

För Azure AD-roller i Privileged Identity Management kan endast en användare som är i rollen privilegierad roll administratör eller global administratör hantera tilldelningar för andra administratörer. Du kan [bevilja åtkomst till andra administratörer för att hantera Privileged Identity Management](pim-how-to-give-access-to-pim.md). Globala administratörer, säkerhets administratörer, globala läsare och säkerhets läsare kan också visa tilldelningar till Azure AD-roller i Privileged Identity Management.

För Azures resurs roller i Privileged Identity Management kan endast en prenumerations administratör, en resurs ägare eller en resurs användares åtkomst administratör hantera tilldelningar för andra administratörer. Användare som är privilegierade roll administratörer, säkerhets administratörer eller säkerhets läsare har som standard inte åtkomst att visa tilldelningar till Azures resurs roller i Privileged Identity Management.

## <a name="scenarios"></a>Scenarier

Privileged Identity Management stöder följande scenarier:

### <a name="privileged-role-administrator-permissions"></a>Administratörs behörighet för privilegie rad roll

- aktivera godkännande för specifika roller
- Ange användare eller grupper som ska godkännas för att godkänna begär Anden
- visa historik för begäranden och godkännanden för alla privilegierade roller.

### <a name="approver-permissions"></a>Behörighet för god kännare

- visa väntande godkännanden (begäranden)
- Godkänn eller avvisa begär Anden om roll höjning (enstaka och bulk)
- Ange motivering för mitt godkännande eller avvisande

### <a name="eligible-role-user-permissions"></a>Kvalificerade roll användar behörigheter

- begära aktivering av roller som kräver godkännande
- visa status för begäran att aktivera
- slutföra uppgiften i Azure AD om aktiveringen godkändes.

## <a name="terminology"></a>Terminologi

För att bättre förstå Privileged Identity Management och dess dokumentation bör du läsa följande villkor.

| Term eller begrepp | Kategori för rolltilldelning | Beskrivning |
| --- | --- | --- |
| berättigad | Typ | En rolltilldelning som kräver att en användare utför en eller flera åtgärder för att använda rollen. Om en användare har gjorts berättigad för en roll innebär det att användaren kan aktivera rollen när det finns behov av att utföra privilegierade åtgärder. Det finns ingen skillnad i den åtkomst som ges till någon med en permanent kontra berättigad rolltilldelning. Den enda skillnaden är att vissa användare inte behöver den åtkomsten hela tiden. |
| aktiv | Typ | En rolltilldelning som inte kräver att en användare utför några åtgärder för att använda rollen. Användare som är tilldelade som aktiva har de behörigheter som rollen har tilldelats. |
| aktivera |  | Processen med att utföra en eller flera åtgärder för att använda en roll som en användare är berättigad för. Det kan vara åtgärder som att utföra en kontroll av multifaktorautentisering (MFA), ange en affärsmotivering eller begära godkännande från utnämnda godkännare. |
| tilldelad | Tillstånd | En användare som har en aktiv rolltilldelning. |
| aktiverad | Tillstånd | En användare som har en berättigad rolltilldelning, utförde åtgärderna för att aktivera rollen och nu är aktiv.  När användaren är aktiverad kan han/hon använda rollen under en förinställd tidsperiod innan aktivering krävs igen. |
| permanent berättigad | Varaktighet | En rolltilldelning där en användare alltid är berättigad att aktivera rollen. |
| permanent aktiv | Varaktighet | En rolltilldelning där en användare alltid kan använda rollen utan att utföra några åtgärder. |
| berättigad med upphörande | Varaktighet | En rolltilldelning där en användare är berättigad att aktivera rollen inom angivna startdatum och slutdatum. |
| aktiv med upphörande | Varaktighet | En rolltilldelning där en användare kan använda rollen utan att utföra några åtgärder inom angivna startdatum och slutdatum. |
| just-in-time-åtkomst (JIT) |  | En modell i vilken användarna får tillfällig behörighet att utföra privilegierade uppgifter, vilket hindrar skadliga eller obehöriga användare från att få åtkomst när behörigheterna har gått ut. Åtkomst beviljas endast när användare behöver den. |
| princip om minsta behörighetsåtkomst |  | En rekommenderad säkerhetsrutin där varje användare endast har fått de minsta privilegier som krävs för att utföra uppgifterna som de har behörighet att utföra. Den här metoden minimerar antalet globala administratörer och använder i stället specifika administratörsroller för vissa scenarier. |

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Information om licenser för användare finns i [licens krav för att använda Privileged Identity Management](subscription-requirements.md).

## <a name="next-steps"></a>Nästa steg

- [Licens krav för att använda Privileged Identity Management](subscription-requirements.md)
- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Distribuera Privileged Identity Management](pim-deployment-plan.md)
