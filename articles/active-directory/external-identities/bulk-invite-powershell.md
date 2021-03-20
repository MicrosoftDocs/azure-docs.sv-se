---
title: Självstudie för Mass inbjudan av B2B-samarbets användare – Azure Active Directory | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder PowerShell och en CSV-fil för att skicka mass inbjudningar till externa Azure AD B2B-samarbets gäst användare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e047f11cc243ab1a36a8c61dd1b229d9e115115
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92892493"
---
# <a name="tutorial-use-powershell-to-bulk-invite-azure-ad-b2b-collaboration-users"></a>Självstudie: Använd PowerShell för att skicka inbjudan till Azure AD B2B Collaboration-användare

Om du använder Azure Active Directory (Azure AD) B2B-samarbete för att arbeta med externa partners, kan du bjuda in flera gästanvändare till din organisation samtidigt. I den här självstudien lär du dig hur du använder PowerShell för att skicka massinbjudningar till externa användare. Närmare bestämt kan du göra följande:

> [!div class="checklist"]
> * Förbereda en fil med kommaavgränsade värden (CSV) med användarinformation
> * Skicka inbjudningar genom att köra ett PowerShell-skript
> * Verifiera att användarna har lagts till i katalogen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

### <a name="install-the-latest-azureadpreview-module"></a>Installera den senaste AzureADPreview-modulen

Se till att du installerar den senaste versionen av Azure AD PowerShell för Graph-modulen (AzureADPreview). 

Kontrollera först vilka moduler du har installerat. Öppna Windows PowerShell som upphöjd användare (Kör som administratör) och kör följande kommando:

```powershell
Get-Module -ListAvailable AzureAD*
```

Gör något av följande, baserat på utdata:

- Om inga resultat returneras installerar du modulen AzureADPreview genom att köra följande kommando:
  
   ```powershell
   Install-Module AzureADPreview
   ```

- Om enbart AzureAD-modulen visas i resultaten, så installera modulen AzureADPreview genom att köra följande kommandon: 

   ```powershell
   Uninstall-Module AzureAD
   Install-Module AzureADPreview
   ```

- Om endast AzureADPreview-modulen visas i resultatet, men du får ett meddelande som anger att det finns en senare version, så uppdatera modulen genom att köra följande kommandon:

   ```powershell
   Uninstall-Module AzureADPreview
   Install-Module AzureADPreview
   ```

Du får eventuellt en uppmaning om att installera modulen från en ej betrodd lagringsplats. Det här inträffar om du inte tidigare angett PSGallery-lagringsplatsen som en betrodd lagringsplats. Installera modulen genom att trycka på **Y**.

### <a name="get-test-email-accounts"></a>Hämta test-e-postkontona

Du behöver två eller flera test-e-postkonton att skicka inbjudningar till. Kontona måste finnas utanför din organisation. Du kan använda vilken typ av konto som helst, t.ex. konton i sociala medier som gmail.com- eller outlook.com-adresser.

## <a name="prepare-the-csv-file"></a>Förbered CSV-filen

Skapa en CSV-fil i Microsoft Excel med listan med de inbjudnas användarnamn och e-postadresser. Se till att inkludera kolumnrubrikerna **Namn** och **InvitedUserEmailAddress**.

Du kan t.ex. skapa ett kalkylblad i följande format:

![PowerShell-utdata som visar väntande godkännande av användare](media/tutorial-bulk-invite/AddUsersExcel.png)

Spara filen som **C:\BulkInvite\Invitations.csv**. 

Om du inte har Excel kan du skapa en CSV-fil i en textredigerare, t.ex. Anteckningar. Avgränsa varje värde med ett kommatecken och varje rad med ny rad. 

## <a name="sign-in-to-your-tenant"></a>Logga in på din klientorganisation

Kör följande kommando för att ansluta till klientorganisationens domän:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```

Till exempel `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Ange dina autentiseringsuppgifter när du uppmanas att göra det.

## <a name="send-bulk-invitations"></a>Skicka massinbjudningar

Skicka inbjudningarna genom att köra följande PowerShell-skript (där **c:\bulkinvite\invitations.csv** är CSV-filens sökväg):

```powershell
$invitations = import-csv c:\bulkinvite\invitations.csv

$messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo

$messageInfo.customizedMessageBody = "Hello. You are invited to the Contoso organization."

foreach ($email in $invitations)
   {New-AzureADMSInvitation `
      -InvitedUserEmailAddress $email.InvitedUserEmailAddress `
      -InvitedUserDisplayName $email.Name `
      -InviteRedirectUrl https://myapps.microsoft.com `
      -InvitedUserMessageInfo $messageInfo `
      -SendInvitationMessage $true
   }
```

Skriptet skickar en inbjudan till e-postadresserna i filen Invitations.csv. Du bör se utdata som liknar följande för respektive användare:

![Skärm bild som visar PowerShell-utdata som innehåller väntande användar godkännande.](media/tutorial-bulk-invite/B2BBulkImport.png)

## <a name="verify-users-exist-in-the-directory"></a>Kontrollera att det finns användare i katalogen

Kontrollera att de inbjudna användarna har lagts till i Azure AD genom att köra följande kommando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Du bör se de användare som du har bjudit in i listan, med en User Principal Name (UPN) i formatet *emailaddress*#EXT # \@ *Domain*. Till exempel *lstokes_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, där contoso.onmicrosoft.com är organisationen som du skickade inbjudningarna från.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändarkontona i katalogen. Kör följande kommando för att ta bort ett användarkonto:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Exempelvis: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skickat massinbjudningar till gästanvändare utanför organisationen. Härnäst ska du få lära dig hur inlösningsprocessen för inbjudningar ser ut.

> [!div class="nextstepaction"]
> [Läs mer om inlösningsprocessen för Azure AD B2B-samarbetsinbjudningar](redemption-experience.md)
