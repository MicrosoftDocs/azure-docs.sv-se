---
title: Lägga till, ta bort och lista användare i en administrativ enhet – Azure Active Directory | Microsoft Docs
description: Hantera användare och deras rollbehörigheter i en administrativ enhet i Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496834"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Lägga till och hantera användare i en administrativ enhet i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till användare i en administrativ enhet för en mer detaljerad administrativ kontroll.

Information om hur du förbereder dig för att använda PowerShell Microsoft Graph för hantering av administrativa enheter finns [i Kom igång.](admin-units-manage.md#get-started)

## <a name="add-users-to-an-administrative-unit"></a>Lägga till användare i en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan tilldela användare till administrativa enheter individuellt eller som en massåtgärd.

- Tilldela enskilda användare från en användarprofil:

   1. Logga in på [Azure AD-administrationscentret med](https://portal.azure.com) behörigheten Privilegierad rolladministratör.

   1. Välj **Användare** och välj sedan den användare som ska tilldelas en administrativ enhet för att öppna användarens profil.
   
   1. Välj **Administrativa enheter.** 
   
   1. Om du vill tilldela användaren till  en eller flera administrativa enheter väljer du Tilldela till administrativ enhet och väljer sedan de administrativa enheter som du vill tilldela användaren i den högra rutan.

       ![Skärmbild av fönstret "Administrativa enheter" för att tilldela en användare till en administrativ enhet.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Tilldela enskilda användare från en administrativ enhet:

   1. Logga in på [Azure AD-administrationscentret med](https://portal.azure.com) behörigheten Privilegierad rolladministratör.
   1. Välj **Administrativa enheter** och välj sedan den administrativa enhet där användaren ska tilldelas.
   1. Välj **Alla användare,** välj **Lägg** till  medlem och välj sedan en eller flera användare som du vill tilldela den administrativa enheten i fönstret Lägg till medlem.

        ![Skärmbild av fönstret "Användare" i den administrativa enheten för att tilldela en användare till en administrativ enhet.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Tilldela användare som en massåtgärd:

   1. Logga in på [Azure AD-administrationscentret med](https://portal.azure.com) behörigheten Privilegierad rolladministratör.

   1. Välj **Administrativa enheter.**

   1. Välj den administrativa enhet som du vill lägga till användare i.

   1. Välj **Användare**  >  **Massaktiviteter Mass**  >  **lägga till medlemmar**. Du kan sedan ladda ned mallen med kommaavgränsade värden (CSV) och redigera filen. Formatet är enkelt och behöver ett enda huvudnamn för användaren som ska läggas till på varje rad. När filen är klar sparar du den på en lämplig plats och laddar sedan upp den som en del av det här steget.

      ![Skärmbild av fönstret "Användare" för att tilldela användare till en administrativ enhet som en massåtgärd.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Använd PowerShell

I PowerShell använder du `Add-AzureADAdministrativeUnitMember` cmdleten i följande exempel för att lägga till användaren i den administrativa enheten. Objekt-ID:t för den administrativa enhet där du vill lägga till användaren och objekt-ID:t för den användare som du vill lägga till tas som argument. Ändra det markerade avsnittet efter behov för din specifika miljö.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Ersätt platshållaren med testinformation och kör följande kommando:

Förfrågan

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Brödtext

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Exempel

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Visa en lista över administrativa enheter för en användare

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

I Azure Portal kan du öppna en användares profil genom att göra följande:

1. Gå till **Azure AD** och välj sedan **Användare.**

1. Välj den användare vars profil du vill visa.

1. Välj **Administrativa enheter** för att visa listan över administrativa enheter som användaren har tilldelats.

   ![Skärmbild av administrativa enheter som en användare har tilldelats.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Som standard `Get-AzureADAdministrativeUnitMember` returnerar endast 100 medlemmar i en administrativ enhet. Om du vill hämta fler medlemmar kan du lägga till `"-All $true"` .

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Ersätt platshållaren med testinformation och kör följande kommando:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Ta bort en enskild användare från en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan ta bort en användare från en administrativ enhet på något av två sätt: 

* I Azure Portal du till **Azure AD** och väljer sedan **Användare.** 
  1. Välj användaren för att öppna användarens profil. 
  1. Välj den administrativa enhet som du vill ta bort användaren från och välj sedan **Ta bort från den administrativa enheten**.

     ![Skärmbild som visar hur du tar bort en användare från en administrativ enhet från användarens profilfönster.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* I den Azure Portal du till **Azure AD** och väljer sedan Administrativa **enheter**.
  1. Välj den administrativa enhet som du vill ta bort användaren från. 
  1. Välj användaren och välj sedan Ta **bort medlem.**
  
     ![Skärmbild som visar hur du tar bort en användare på administrativ enhetsnivå.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Ersätt platshållarna med testinformation och kör följande kommando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Ta bort flera användare som en massåtgärd

Om du vill ta bort flera användare från en administrativ enhet gör du följande:

1. I Azure Portal du till **Azure AD**.

1. Välj **Administrativa enheter** och välj sedan den administrativa enhet som du vill ta bort användare från. 

1. Välj **Massborttagning av** medlemmar och ladda sedan ned den CSV-mall som du ska använda för att visa en lista över de användare som du vill ta bort.

   ![Skärmbild som visar länken "Massborttagning av medlemmar" i fönstret "Användare".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Redigera den nedladdade CSV-mallen med relevanta användarposter. Ta inte bort de två första raderna i mallen. Lägg till ett UPN (User Principal Name) på varje rad.

   ![Skärmbild av en redigerad CSV-fil för att ta bort användare från en administrativ enhet i grupp.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Spara ändringarna, ladda upp filen och välj sedan **Skicka.**

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](admin-units-assign-roles.md)
- [Lägga till grupper i en administrativ enhet](admin-units-add-manage-groups.md)
