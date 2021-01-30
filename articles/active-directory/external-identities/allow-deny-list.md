---
title: Tillåt eller blockera inbjudningar till vissa organisationer – Azure AD
description: Visar hur en administratör kan använda Azure Portal eller PowerShell för att ange en åtkomst-eller neka-lista för att tillåta eller blockera B2B-användare från vissa domäner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cc336781e9a55bbcb6c51677b01bfc402126f4a
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071908"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Tillåt eller blockera inbjudningar till B2B-användare från specifika organisationer

Du kan använda en lista över tillåtna eller en neka för att tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer. Om du till exempel vill blockera privata e-postadresser kan du konfigurera en lista över nekade domäner som innehåller domäner som Gmail.com och Outlook.com. Eller, om ditt företag har ett partnerskap med andra företag som Contoso.com, Fabrikam.com och Litware.com, och du vill begränsa inbjudningar till dessa organisationer, kan du lägga till Contoso.com, Fabrikam.com och Litware.com i listan över tillåtna.
  
## <a name="important-considerations"></a>Att tänka på

- Du kan skapa antingen en lista över tillåtna eller nekade listor. Du kan inte konfigurera båda typerna av listor. Som standard är de domäner som inte finns i listan över tillåtna i listan över nekade, och vice versa. 
- Du kan bara skapa en princip per organisation. Du kan uppdatera principen för att inkludera fler domäner, eller så kan du ta bort principen för att skapa en ny. 
- Antalet domäner som du kan lägga till i en lista över tillåtna eller nekade listor begränsas bara av principens storlek. Den maximala storleken för hela principen är 25 KB (25 000 tecken) som innehåller listan över tillåtna eller nekade listor och andra parametrar som har kon figurer ATS för andra funktioner.
- Listan fungerar oberoende av listor över tillåtna/blockerade OneDrive för företag och SharePoint Online. Om du vill begränsa enskilda fildelningar i SharePoint Online måste du konfigurera en lista över tillåtna eller nekade för OneDrive för företag och SharePoint Online. Mer information finns i avsnittet [begränsade domäner som delas i SharePoint Online och OneDrive för företag](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Listan gäller inte för externa användare som redan har löst in inbjudan. Listan kommer att verkställas när listan har kon figurer ATS. Om en användar inbjudan är i ett väntande tillstånd och du anger en princip som blockerar sin domän, kommer användarens försök att lösa in inbjudan att Miss lyckas.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ange List principen Tillåt eller neka i portalen

Som standard är inställningen **Tillåt att inbjudningar skickas till alla domäner (mest inkluderade)** aktive rad. I det här fallet kan du bjuda in B2B-användare från vilken organisation som helst.

### <a name="add-a-deny-list"></a>Lägg till en neka-lista

Detta är det vanligaste scenariot, där din organisation vill arbeta med nästan vilken organisation som helst, men vill förhindra att användare från vissa domäner bjuds in som B2B-användare.

Så här lägger du till en neka-lista:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory**  >    >  **användar inställningar** för användare.
3. Under **externa användare** väljer du **Hantera inställningar för externt samarbete**.
4. Under **samarbets begränsningar** väljer **du neka inbjudningar till de angivna domänerna**.
5. Under **mål domäner** anger du namnet på en av de domäner som du vill blockera. För flera domäner anger du varje domän på en ny rad. Exempel:

   ![Visar alternativet neka med tillagda domäner](./media/allow-deny-list/DenyListSettings.png)
 
6. När du är klar klickar du på **Spara**.

Om du försöker bjuda in en användare från en blockerad domän efter att du har angett principen, får du ett meddelande om att användarens domän för närvarande är blockerad av din Inbjudnings princip.
 
### <a name="add-an-allow-list"></a>Lägg till en lista över tillåtna

Detta är en mer restriktiv konfiguration, där du kan ange vissa domäner i listan över tillåtna och begränsa inbjudningar till andra organisationer eller domäner som inte nämns. 

Om du vill använda en lista över tillåtna, se till att du tillbringar tid för att helt utvärdera vad ditt företag behöver. Om du gör den här principen för begränsad kan användarna välja att skicka dokument via e-post eller hitta andra icke-sanktionerade samarbets sätt.


Så här lägger du till en lista över tillåtna:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory**  >    >  **användar inställningar** för användare.
3. Under **externa användare** väljer du **Hantera inställningar för externt samarbete**.
4. Under **samarbets begränsningar** väljer du **Tillåt endast inbjudningar till de angivna domänerna (mest restriktiva)**.
5. Under **mål domäner** anger du namnet på en av de domäner som du vill tillåta. För flera domäner anger du varje domän på en ny rad. Exempel:

   ![Visar alternativet Tillåt med tillagda domäner](./media/allow-deny-list/AllowListSettings.png)
 
6. När du är klar klickar du på **Spara**.

Om du försöker bjuda in en användare från en domän som inte finns med i listan över tillåtna, visas ett meddelande om att användarens domän för närvarande är blockerad av din Inbjudnings princip när du har angett principen.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Växla från Tillåt till neka-lista och vice versa 

Om du växlar från en princip till en annan, ignorerar den befintliga princip konfigurationen. Se till att säkerhetskopiera information om konfigurationen innan du utför växeln. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ange List principen Tillåt eller neka med hjälp av PowerShell

### <a name="prerequisite"></a>Förutsättning

> [!Note]
> AzureADPreview-modulen är inte en fullt stödd modul som den är i för hands version. 

Om du vill ange listan över tillåtna eller nekade med hjälp av PowerShell måste du installera för hands versionen av Azure Active Directory-modulen för Windows PowerShell. Mer specifikt kan du installera AzureADPreview-modulen version 2.0.0.98 eller senare.

Kontrol lera versionen för modulen (och se om den är installerad):
 
1. Öppna Windows PowerShell som en utökad användare (kör som administratör). 
2. Kör följande kommando för att se om du har några versioner av Azure Active Directory-modulen för Windows PowerShell installerat på datorn:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Om modulen inte är installerad eller om du inte har en version som krävs, gör du något av följande:

- Om inga resultat returneras kör du följande kommando för att installera den senaste versionen av AzureADPreview-modulen:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Om endast AzureAD-modulen visas i resultatet kör du följande kommandon för att installera AzureADPreview-modulen: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Om endast AzureADPreview-modulen visas i resultatet, men versionen är mindre än 2.0.0.98, kör du följande kommandon för att uppdatera den: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Om både AzureAD-och AzureADPreview-modulerna visas i resultatet, men versionen av AzureADPreview-modulen är mindre än 2.0.0.98 kör du följande kommandon för att uppdatera den: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Använd AzureADPolicy-cmdletar för att konfigurera principen

Använd cmdleten [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) för att skapa en lista över tillåtna eller nekade. I följande exempel visas hur du anger en neka-lista som blockerar domänen "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Följande visar samma exempel, men med princip definitionen infogad.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Använd cmdleten [set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) för att ange List principen för att tillåta eller neka. Exempel:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Använd cmdleten [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) för att hämta principen. Exempel:

```powershell
$currentpolicy = Get-AzureADPolicy -All $true | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Använd cmdleten [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) om du vill ta bort principen. Exempel:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- Information om villkorlig åtkomst och B2B-samarbete finns i [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md).
