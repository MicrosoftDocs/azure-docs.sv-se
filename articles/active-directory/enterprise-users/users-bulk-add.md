---
title: Massredigera användare i Azure Active Directory Portal | Microsoft Docs
description: Lägg till användare i grupp i Azure AD administrations Center i Azure Active Directory
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547822"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Massredigera användare i Azure Active Directory

Azure Active Directory (Azure AD) stöder Mass åtgärder för att skapa och ta bort och har stöd för nedladdning av listor över användare. Fyll bara i CSV-mallen (kommaavgränsade värden) som du kan ladda ned från Azure AD-portalen.

## <a name="required-permissions"></a>Behörigheter som krävs

Du måste vara inloggad som global administratör eller användar administratör för att kunna skapa användare i administrations portalen.

## <a name="understand-the-csv-template"></a>Förstå CSV-mallen

Ladda ned och fyll i mallen för Mass uppladdning av CSV så att du kan skapa Azure AD-användare i flera. Den CSV-mall som du hämtar kan se ut som i det här exemplet:

![Kalkyl blad för uppladdning och vidarekoppling förklarar syfte och värden för varje rad och kolumn](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Om du bara lägger till en post med hjälp av CSV-mallen måste du behålla rad 3 och lägga till din nya post på rad 4.

### <a name="csv-template-structure"></a>Struktur för CSV-mall

Raderna i en Hämtad CSV-mall är följande:

- **Versions nummer**: den första raden som innehåller versions numret måste inkluderas i överförings-CSV-filen.
- **Kolumn rubriker**: kolumn rubrikernas format är &lt; *objekt namnet* &gt; [PropertyName] &lt; *obligatoriskt eller tomt* &gt; . Till exempel `Name [displayName] Required`. Vissa äldre versioner av mallen kan ha små variationer.
- **Exempel rad**: vi har inkluderat i mallen en rad exempel på acceptabla värden för varje kolumn. Du måste ta bort exempel raden och ersätta den med dina egna poster.

### <a name="additional-guidance"></a>Mer information

- De två första raderna i uppladdnings mal len får inte tas bort eller ändras, eller så går det inte att bearbeta överföringen.
- De obligatoriska kolumnerna visas först.
- Vi rekommenderar inte att du lägger till nya kolumner i mallen. Eventuella ytterligare kolumner som du lägger till ignoreras och bearbetas inte.
- Vi rekommenderar att du laddar ned den senaste versionen av CSV-mallen så ofta som möjligt.
- Kontrol lera att det inte finns något oavsiktligt blank steg före/efter ett fält. För **användarens huvud namn** skulle ett sådant blank steg orsaka import fel.

## <a name="to-create-users-in-bulk"></a>Så här skapar du användare i grupp

1. [Logga in på din Azure AD-organisation](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I Azure AD väljer **du användare**  >  **Mass skapande**.
1. På sidan **skapa användare av grupp** väljer du **Hämta** för att ta emot en giltig CSV-fil (kommaavgränsade värden) för användar egenskaper och lägger sedan till Lägg till användare som du vill skapa.

   ![Välj en lokal CSV-fil där du visar de användare som du vill lägga till](./media/users-bulk-add/upload-button.png)

1. Öppna CSV-filen och Lägg till en rad för varje användare som du vill skapa. De enda värden som krävs är **namn**, **användarens huvud namn**, **inledande lösen ord** och **block inloggning (Ja/Nej)**. Spara sedan filen.

   [![CSV-filen innehåller namn och ID: n för de användare som ska skapas](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Bläddra till filen under överför din CSV-fil på sidan **Mass skapande av användare** . När du väljer filen och klickar på **Skicka**, startar verifieringen av CSV-filen.
1. När fil innehållet har verifierats visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
1. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som importerar de nya användarna.
1. När importen är klar visas ett meddelande om status för Mass åtgärds jobbet.

Om det finns fel kan du hämta och Visa resultat filen på resultat sidan för **Mass åtgärder** . I filen kan du se orsaken till varje fel. Fil överföringen måste matcha den angivna mallen och innehålla de exakta kolumn namnen.

## <a name="check-status"></a>Kontrollera status

Du kan se statusen för alla väntande Mass förfrågningar på resultat sidan för **Mass åtgärder** .

   [![Kontrol lera skapa status på sidan med Mass åtgärds resultat](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Sedan kan du kontrol lera att de användare du skapade finns i Azure AD-organisationen antingen i Azure Portal eller med hjälp av PowerShell.

## <a name="verify-users-in-the-azure-portal"></a>Verifiera användare i Azure Portal

1. [Logga in på administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en användar administratör i organisationen.
1. I navigerings fönstret väljer du **Azure Active Directory**.
1. Under **Hantera** väljer du **Användare**.
1. Under **Visa**, väljer du **alla användare** och kontrollerar att de användare som du har skapat visas.

### <a name="verify-users-with-powershell"></a>Verifiera användare med PowerShell

Kör följande kommando:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Du bör se att de användare som du har skapat visas i listan.

## <a name="bulk-import-service-limits"></a>Begränsningar för Mass import av tjänster

Varje Mass aktivitet för att skapa användare kan köras i upp till en timme. Detta möjliggör Mass skapande av minst 50 000 användare.

## <a name="next-steps"></a>Nästa steg

- [Massborttag användare](users-bulk-delete.md)
- [Hämta lista över användare](users-bulk-download.md)
- [Massåterställ användare](users-bulk-restore.md)
