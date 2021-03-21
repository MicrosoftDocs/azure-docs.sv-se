---
title: Mass borttagning av användare i Azure Active Directory Portal | Microsoft Docs
description: Ta bort användare i grupp i Azure administrations centret i Azure Active Directory
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
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574471"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Mass borttagning av användare i Azure Active Directory

Med hjälp av Azure Active Directory (Azure AD)-portalen kan du ta bort ett stort antal medlemmar till en grupp med hjälp av en fil med kommaavgränsade värden (CSV) för Mass borttagning av användare.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Hämta och fyll i CSV-mallen så att du kan ta bort Azure AD-användare i bulk. Den CSV-mall som du hämtar kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Struktur för CSV-mall

Raderna i en Hämtad CSV-mall är följande:

- **Versions nummer**: den första raden som innehåller versions numret måste inkluderas i överförings-CSV-filen.
- **Kolumn rubriker**: kolumn rubrikernas format är &lt; *objekt namnet* &gt; [PropertyName] &lt; *obligatoriskt eller tomt* &gt; . Till exempel `User name [userPrincipalName] Required`. Vissa äldre versioner av mallen kan ha små variationer.
- **Exempel rad**: vi har inkluderat i mallen en rad exempel på acceptabla värden för varje kolumn. Du måste ta bort exempel raden och ersätta den med dina egna poster.

### <a name="additional-guidance"></a>Mer information

- De två första raderna i uppladdnings mal len får inte tas bort eller ändras, eller så går det inte att bearbeta överföringen.
- De obligatoriska kolumnerna visas först.
- Vi rekommenderar inte att du lägger till nya kolumner i mallen. Eventuella ytterligare kolumner som du lägger till ignoreras och bearbetas inte.
- Vi rekommenderar att du laddar ned den senaste versionen av CSV-mallen så ofta som möjligt.

## <a name="to-bulk-delete-users"></a>För Mass borttagning av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare**  >  **Mass borttagning**.
1. På sidan **Mass borttagning av användare** väljer du **Hämta** för att ta emot en giltig CSV-fil med användar egenskaper.

   ![Välj en lokal CSV-fil där du visar de användare som du vill ta bort](./media/users-bulk-delete/bulk-delete.png)

1. Öppna CSV-filen och Lägg till en rad för varje användare som du vill ta bort. Det enda obligatoriska värdet är **användarens huvud namn**. Spara sedan filen.

   ![CSV-filen innehåller namn och ID: n för de användare som ska tas bort](./media/users-bulk-delete/delete-csv-file.png)

1. På sidan **Mass borttagning av användare** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen och klickar på Skicka, startar verifieringen av CSV-filen.
1. När fil innehållet verifieras visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som tar bort användarna.
1. När borttagnings åtgärden har slutförts visas ett meddelande om att Mass åtgärden har slutförts.

Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för **Mass åtgärder** . I filen kan du se orsaken till varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

   [![Kontrol lera borttagnings status på sidan med Mass åtgärds resultat.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

Sedan kan du kontrol lera att de användare som du har tagit bort finns i Azure AD-organisationen antingen i Azure Portal eller med hjälp av PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Verifiera borttagna användare i Azure Portal

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa** väljer du **alla användare** och kontrollerar att de användare som du har tagit bort inte längre visas.

### <a name="verify-deleted-users-with-powershell"></a>Verifiera borttagna användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Kontrol lera att de användare som du har tagit bort inte längre visas.

## <a name="next-steps"></a>Nästa steg

- [Masstillägg av användare](users-bulk-add.md)
- [Hämta lista över användare](users-bulk-download.md)
- [Massåterställ användare](users-bulk-restore.md)
