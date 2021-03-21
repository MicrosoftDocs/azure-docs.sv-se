---
title: Mass borttagning av grupp medlemmar genom att ladda upp en CSV-fil – Azure Active Directory | Microsoft Docs
description: Ta bort grupp medlemmar i Mass åtgärder i administrations centret för Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b905faa892cc338b5bdf4f998ad95f7b8a484e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95488954"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Mass borttagning av grupp medlemmar i Azure Active Directory

Med hjälp av Azure Active Directory-portalen (Azure AD) kan du ta bort ett stort antal medlemmar från en grupp med hjälp av en fil med kommaavgränsade värden (CSV) för Mass borttagning av grupp medlemmar.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Ladda ned och fyll i mallen för Mass uppladdning av CSV för att lägga till Azure AD-gruppmedlemmar i grupp. Din CSV-mall kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>För Mass borttagning av grupp medlemmar

1. Logga in på [Azure Portal](https://portal.azure.com) med ett användar administratörs konto i organisationen. Grupp ägare kan också massredigera medlemmar av grupper som de äger.
1. I Azure AD väljer du **grupper**  >  **alla grupper**.
1. Öppna den grupp som du vill ta bort medlemmar från och välj sedan **medlemmar**.
1. På sidan **medlemmar** väljer du **ta bort medlemmar**.
1. På sidan **Mass borttagning av grupp medlemmar** väljer du **Hämta** för att hämta CSV-filmallen med obligatoriska grupp medlems egenskaper.

   ![Kommandot Ta bort medlemmar finns på profil sidan för gruppen](./media/groups-bulk-remove-members/remove-panel.png)

1. Öppna CSV-filen och Lägg till en rad för varje grupp medlem som du vill ta bort från gruppen (obligatoriska värden är medlems objekt-ID eller användarens huvud namn). Spara sedan filen.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV-filen innehåller namn och ID: n för de grupp medlemmar som ska tas bort":::

1. På sidan **Mass borttagning av grupp medlemmar** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen startar valideringen av CSV-filen.
1. När fil innehållet är verifierat visar sidan Mass import att **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som tar bort grupp medlemmarna från gruppen.
1. När borttagningen är klar visas ett meddelande om att Mass åtgärden lyckades.

## <a name="check-removal-status"></a>Kontrol lera borttagnings status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan Mass åtgärds resultat](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure** eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

## <a name="bulk-removal-service-limits"></a>Begränsningar för Mass borttagnings tjänst

Varje Mass aktivitet för att ta bort en lista över grupp medlemmar från kan köras i upp till en timme. Detta gör att en lista med minst 40 000 medlemmar kan tas bort.

## <a name="next-steps"></a>Nästa steg

- [Grupp medlemmar för Mass import](groups-bulk-import-members.md)
- [Ladda ned medlemmar i en grupp](groups-bulk-download-members.md)
- [Hämta en lista över alla grupper](groups-bulk-download.md)
