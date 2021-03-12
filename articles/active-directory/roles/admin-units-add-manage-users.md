---
title: Lägga till, ta bort och lista användare i en administrativ enhet – Azure Active Directory | Microsoft Docs
description: Hantera användare och deras roll behörigheter i en administrativ enhet i Azure Active Directory
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
ms.openlocfilehash: 7a9d80344a31023d174935e7f785e36102e99eba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011557"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Lägga till och hantera användare i en administrativ enhet i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till användare i en administrativ enhet för en mer detaljerad administrativ omfattning av kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Lägga till användare till en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan tilldela användare till administrativa enheter individuellt eller som en Mass åtgärd.

- Tilldela enskilda användare från en användar profil:

   1. Logga in på [administrations Center för Azure AD](https://portal.azure.com) med administratörs behörighet för privilegierade roller.

   1. Välj **användare** och klicka sedan på den användare som ska tilldelas en administrativ enhet för att öppna användarens profil.
   
   1. Välj **administrativa enheter**. 
   
   1. Om du vill tilldela användaren till en eller flera administrativa enheter väljer du **tilldela till administrativ enhet** och väljer sedan de administrativa enheter som du vill tilldela användaren i den högra rutan.

       ![Skärm bild av fönstret "administrativa enheter" för att tilldela en användare till en administrativ enhet.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Tilldela enskilda användare från en administrativ enhet:

   1. Logga in på [administrations Center för Azure AD](https://portal.azure.com) med administratörs behörighet för privilegierade roller.
   1. Välj **administrativa enheter** och välj sedan den administrativa enhet där användaren ska tilldelas.
   1. Välj **alla användare**, Välj **Lägg till medlem** och välj sedan en eller flera användare som du vill tilldela den administrativa enheten i fönstret **Lägg till medlem** .

        ![Skärm bild av den administrativa enhetens "användare"-ruta för att tilldela en användare till en administrativ enhet.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Tilldela användare som en Mass åtgärd:

   1. Logga in på [administrations Center för Azure AD](https://portal.azure.com) med administratörs behörighet för privilegierade roller.

   1. Välj **administrativa enheter**.

   1. Välj den administrativa enhet som du vill lägga till användare i.

   1. Välj **användare**  >  **Mass aktiviteter**  >  **Mass Lägg till medlemmar**. Sedan kan du hämta den kommaavgränsade värden (CSV)-mallen och redigera filen. Formatet är enkelt och behöver ett enda User Principal Name som ska läggas till på varje rad. När filen är klar sparar du den på en lämplig plats och laddar sedan upp den som en del av det här steget.

      ![Skärm bild av fönstret "användare" för att tilldela användare till en administrativ enhet som en Mass åtgärd.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Använd PowerShell

I PowerShell använder du `Add-AzureADAdministrativeUnitMember` cmdleten i följande exempel för att lägga till användaren till den administrativa enheten. Objekt-ID för den administrativa enhet som du vill lägga till användaren i och objekt-ID: t för den användare som du vill lägga till tas som argument. Ändra det markerade avsnittet efter behov för din speciella miljö.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.ObjectId -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Ersätt plats hållaren med test information och kör följande kommando:

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

I Azure Portal kan du öppna en användar profil genom att göra följande:

1. Gå till **Azure AD** och välj **användare**.

1. Välj den användare vars profil du vill visa.

1. Välj **administrativa enheter** om du vill visa listan över administrativa enheter som användaren har tilldelats till.

   ![Skärm bild av de administrativa enheter som en användare har tilldelats till.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Som standard `Get-AzureADAdministrativeUnitMember` returnerar endast 100 medlemmar av en administrativ enhet. Om du vill hämta fler medlemmar kan du lägga till `"-All $true"` .

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Ersätt plats hållaren med test information och kör följande kommando:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Ta bort en enskild användare från en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan ta bort en användare från en administrativ enhet på något av två sätt: 

* I Azure Portal går du till **Azure AD** och väljer sedan **användare**. 
  1. Välj användaren för att öppna användarens profil. 
  1. Välj den administrativa enhet som du vill ta bort användaren från och välj sedan **ta bort från administrativ enhet**.

     ![Skärm bild som visar hur du tar bort en användare från en administrativ enhet från användarens profil fönster.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* I Azure Portal går du till **Azure AD** och väljer sedan **administrativa enheter**.
  1. Välj den administrativa enhet som du vill ta bort användaren från. 
  1. Välj användaren och välj sedan **ta bort medlem**.
  
     ![Skärm bild som visar hur du tar bort en användare på den administrativa enhets nivån.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Ersätt plats hållarna med test information och kör följande kommando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Ta bort flera användare som en Mass åtgärd

Om du vill ta bort flera användare från en administrativ enhet gör du följande:

1. I Azure Portal går du till **Azure AD**.

1. Välj **administrativa enheter** och välj sedan den administrativa enhet som du vill ta bort användare från. 

1. Välj **Mass borttagnings medlemmar** och hämta sedan den CSV-mall som du vill använda för att visa en lista över de användare som du vill ta bort.

   ![Skärm bild som visar länken "Mass borttagnings medlemmar" i fönstret "användare".](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Redigera den hämtade CSV-mallen med relevanta användar poster. Ta inte bort de två första raderna i mallen. Lägg till en User Principal Name (UPN) på varje rad.

   ![Skärm bild av en redige rad CSV-fil för att ta bort användare från en administrativ enhet i bulk.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Spara ändringarna, ladda upp filen och välj sedan **Skicka**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](admin-units-assign-roles.md)
- [Lägga till grupper i en administrativ enhet](admin-units-add-manage-groups.md)
