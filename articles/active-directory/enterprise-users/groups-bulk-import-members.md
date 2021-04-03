---
title: Mass uppladdning för att lägga till eller skapa medlemmar i en grupp-Azure Active Directory | Microsoft Docs
description: Lägg till grupp medlemmar i grupp i Azure Active Directory administrations centret.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547754"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Mass tillägg av grupp medlemmar i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du lägga till ett stort antal medlemmar i en grupp genom att använda en fil med kommaavgränsade värden (CSV) för grupp medlemmar i Mass import.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Ladda ned och fyll i mallen för Mass uppladdning av CSV för att lägga till Azure AD-gruppmedlemmar i grupp. Din CSV-mall kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Struktur för CSV-mall

Raderna i en Hämtad CSV-mall är följande:

- **Versions nummer**: den första raden som innehåller versions numret måste inkluderas i överförings-CSV-filen.
- **Kolumn rubriker**: kolumn rubrikernas format är &lt; *objekt namnet* &gt; [PropertyName] &lt; *obligatoriskt eller tomt* &gt; . Till exempel `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Vissa äldre versioner av mallen kan ha små variationer. För ändringar i grupp medlemskap kan du välja vilken identifierare som ska användas: medlems objekt-ID eller User Principal Name.
- **Exempel rad**: vi har inkluderat i mallen en rad exempel på acceptabla värden för varje kolumn. Du måste ta bort exempel raden och ersätta den med dina egna poster.

### <a name="additional-guidance"></a>Mer information

- De två första raderna i uppladdnings mal len får inte tas bort eller ändras, eller så går det inte att bearbeta överföringen.
- De obligatoriska kolumnerna visas först.
- Vi rekommenderar inte att du lägger till nya kolumner i mallen. Eventuella ytterligare kolumner som du lägger till ignoreras och bearbetas inte.
- Vi rekommenderar att du laddar ned den senaste versionen av CSV-mallen så ofta som möjligt.
- Lägg till minst två användares UPN eller objekt-ID: n för att ladda upp filen.

## <a name="to-bulk-import-group-members"></a>Till Mass import av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också Mass import medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper**  >  **alla grupper**.
1. Öppna den grupp som du vill lägga till medlemmar i och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **Importera medlemmar**.
1. På sidan **medlemmar grupp medlemmar** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska grupp medlems egenskaper.

    ![Kommandot Importera medlemmar finns på profil sidan för gruppen](./media/groups-bulk-import-members/import-panel.png)

1. Öppna CSV-filen och Lägg till en rad för varje grupp medlem som du vill importera till gruppen (obligatoriska värden är antingen **medlems objekt-ID** eller **användarens huvud namn**). Spara sedan filen.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV-filen innehåller namn och ID: n för de medlemmar som ska importeras":::

1. På sidan **medlemmar i grupp medlemmar** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen startar valideringen av CSV-filen.
1. När fil innehållet är verifierat visar sidan Mass import att **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som importerar grupp medlemmarna till gruppen.
1. När importen är klar visas ett meddelande om att Mass åtgärden lyckades.

## <a name="check-import-status"></a>Kontrol lera import status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure** eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

## <a name="bulk-import-service-limits"></a>Begränsningar för Mass import av tjänster

Varje Mass aktivitet för att importera en lista över grupp medlemmar kan köras i upp till en timme. På så sätt kan du importera en lista med högst 40 000 medlemmar.

## <a name="next-steps"></a>Nästa steg

- [Mass borttagning av grupp medlemmar](groups-bulk-remove-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Hämta en lista över alla grupper](groups-bulk-download.md)
