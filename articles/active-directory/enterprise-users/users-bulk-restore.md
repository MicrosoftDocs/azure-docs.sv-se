---
title: Mass återställning av borttagna användare i Azure Active Directory Portal | Microsoft Docs
description: Återställa borttagna användare i grupp i Azure AD administrations Center i Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571512"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Mass återställning av borttagna användare i Azure Active Directory

Azure Active Directory (Azure AD) stöder Mass återställnings åtgärder och stöder nedladdning av listor över användare, grupper och grupp medlemmar.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Hämta och fyll i CSV-mallen för att hjälpa dig att återställa Azure AD-användare i bulk. Den CSV-mall som du hämtar kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Struktur för CSV-mall

Raderna i en Hämtad CSV-mall är följande:

- **Versions nummer**: den första raden som innehåller versions numret måste inkluderas i överförings-CSV-filen.
- **Kolumn rubriker**: kolumn rubrikernas format är &lt; *objekt namnet* &gt; [PropertyName] &lt; *obligatoriskt eller tomt* &gt; . Till exempel `Object ID [objectId] Required`. Vissa äldre versioner av mallen kan ha små variationer.
- **Exempel rad**: vi har inkluderat i mallen en rad exempel på acceptabla värden för varje kolumn. Du måste ta bort exempel raden och ersätta den med dina egna poster.

### <a name="additional-guidance"></a>Mer information

- De två första raderna i uppladdnings mal len får inte tas bort eller ändras, eller så går det inte att bearbeta överföringen.
- De obligatoriska kolumnerna visas först.
- Vi rekommenderar inte att du lägger till nya kolumner i mallen. Eventuella ytterligare kolumner som du lägger till ignoreras och bearbetas inte.
- Vi rekommenderar att du laddar ned den senaste versionen av CSV-mallen så ofta som möjligt.

## <a name="to-bulk-restore-users"></a>För Mass återställning av användare

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i Azure AD-organisationen.
1. I Azure AD väljer **du användare**  >  **borttagna**.
1. På sidan **borttagna användare** väljer du **Mass återställning** för att ladda upp en giltig CSV-fil med egenskaper för de användare som ska återställas.

    ![Välj kommandot Mass återställning på sidan borttagna användare](./media/users-bulk-restore/bulk-restore.png)

1. Öppna CSV-mallen och Lägg till en rad för varje användare som du vill återställa. Det enda obligatoriska värdet är **ObjectID**. Spara sedan filen.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Välj en lokal CSV-fil där du visar de användare som du vill lägga till":::

1. På sidan **Mass återställning** , under **överför din CSV-fil**, bläddrar du till filen. När du väljer filen och klickar på **Skicka**, startar verifieringen av CSV-filen.
1. När fil innehållet verifieras visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När du har verifierat filen väljer du **Skicka** för att starta den Azure Mass åtgärd som återställer användarna.
1. När återställningen är klar visas ett meddelande om att Mass åtgärden lyckades.

Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för **Mass åtgärder** . I filen kan du se orsaken till varje fel.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

[![Kontrol lera status på sidan med Mass åtgärds resultat.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

Sedan kan du kontrol lera att de användare som du har återställt finns i Azure AD-organisationen antingen i Azure Portal eller genom att använda PowerShell.

## <a name="view-restored-users-in-the-azure-portal"></a>Visa återställda användare i Azure Portal

1. [Logga in på administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**, väljer du **alla användare** och kontrollerar att de användare som du har återställt visas.

### <a name="view-users-with-powershell"></a>Visa användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har återställt visas.

## <a name="next-steps"></a>Nästa steg

- [Mass import av användare](users-bulk-add.md)
- [Massborttag användare](users-bulk-delete.md)
- [Hämta lista över användare](users-bulk-download.md)
