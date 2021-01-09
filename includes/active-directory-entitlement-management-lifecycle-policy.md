---
title: ta med fil
description: ta med fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: db13884e3deed8e990d21d82e215a1e837371275
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052826"
---
## <a name="lifecycle"></a>Livscykel

På fliken **livs cykel** anger du när en användares tilldelning till åtkomst paketet upphör att gälla. Du kan också ange om användarna ska kunna utöka sina tilldelningar.

1. I avsnittet **förfallo** datum går det att ange **åtkomst paket tilldelningar förfaller** till **på datum**, **antal dagar** eller **aldrig**.

    För **på datum** väljer du ett förfallo datum i framtiden.

    Ange ett tal mellan 0 och 3660 dagar i **antal dagar**.

    Baserat på ditt val förfaller en användares tilldelning till åtkomst paketet ett visst datum, ett visst antal dagar efter att de har godkänts eller aldrig.

1. Klicka på **Visa avancerade inställningar för förfallo datum** för att visa ytterligare inställningar.

    ![Åtkomst paket – livs cykelns förfallo inställningar](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Om du vill tillåta att användare utökar sina tilldelningar ställer du in **Tillåt att användare utökar åtkomsten** till **Ja**.

    Om tillägg är tillåtna i principen får användaren ett e-postmeddelande 14 dagar och även en dag innan tilldelningen av åtkomst paket är inställd på att upphöra att gälla, så att de uppmanas att utöka tilldelningen. Användaren måste fortfarande vara i omfånget för principen vid den tidpunkt då de begär ett tillägg. Om principen har ett explicit slutdatum för tilldelningar, och en användare skickar en begäran om att utöka åtkomsten, måste tilläggs datumet i begäran vara på eller före när tilldelningarna upphör att gälla, enligt definitionen i principen som användes för att ge användaren åtkomst till åtkomst paketet. Om principen till exempel anger att tilldelningar är inställda på att upphöra att gälla den 30 juni, är det maximala tillägg som en användare kan begära den 30 juni.

    Om en användares åtkomst är utökad kommer de inte att kunna begära åtkomst paketet efter angivet tilläggs datum (datum angivet i tids zonen för den användare som skapade principen).

1. Kräv godkännande för att bevilja **tillägg** till **Ja** för att kräva att ett tillägg beviljas.

    Samma godkännande inställningar som angavs på fliken **förfrågningar** kommer att användas.

1. Klicka på **Nästa** eller **Uppdatera**.
