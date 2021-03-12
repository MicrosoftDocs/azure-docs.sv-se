---
title: Lägg till, ta bort och lista grupper i en administrativ enhet-Azure Active Directory | Microsoft Docs
description: Hantera grupper och deras roll behörigheter i en administrativ enhet i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: f88a0818d93c33b6265cc8c695479d2a42678ada
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011042"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Lägg till och hantera grupper i en administrativ enhet i Azure Active Directory

I Azure Active Directory (Azure AD) kan du lägga till grupper i en administrativ enhet för en mer detaljerad administrativ omfattning av kontroll.

Information om hur du förbereder för att använda PowerShell och Microsoft Graph för hantering av administrativa enheter finns i [Kom igång](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Lägga till grupper i en administrativ enhet

Du kan lägga till grupper i en administrativ enhet med hjälp av Azure Portal, PowerShell eller Microsoft Graph.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan endast tilldela enskilda grupper till en administrativ enhet. Det finns inget alternativ för att tilldela grupper som en Mass åtgärd. I Azure Portal kan du tilldela en grupp till en administrativ enhet på något av två sätt:

* Från fönstret **grupper** :

  1. I Azure Portal går du till **Azure AD**.
  1. Välj **grupper** och välj sedan den grupp som du vill tilldela till den administrativa enheten. 
  1. I den vänstra rutan väljer du **administrativa enheter** för att visa en lista över de administrativa enheter som gruppen är tilldelad till. 

     ![Skärm bild av länken "tilldela till administrativ enhet" i fönstret "administrativa enheter".](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Välj **tilldela till administrativ enhet**.
  1. Välj den administrativa enheten i den högra rutan.

* Från fönstret **administrativa enheter**  >  **alla grupper** :

  1. I Azure Portal går du till **Azure AD**.
  
  1. Välj **administrativa enheter** i den vänstra rutan och välj sedan **alla grupper**. 
     Alla grupper som redan har tilldelats till den administrativa enheten visas i den högra rutan. 

  1. I fönstret **grupper** väljer du **Lägg till**.
    I den högra rutan visas alla tillgängliga grupper i din Azure AD-organisation. 

     ![Skärm bild av knappen Lägg till för att lägga till en grupp i en administrativ enhet.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Välj en eller flera grupper som ska tilldelas den administrativa enheten och välj sedan knappen **Välj** .

### <a name="use-powershell"></a>Använd PowerShell

I följande exempel använder du `Add-AzureADMSAdministrativeUnitMember` cmdleten för att lägga till gruppen i den administrativa enheten. Objekt-ID för den administrativa enheten och objekt-ID: t för den grupp som ska läggas till tas som argument. Ändra det markerade avsnittet efter behov för din speciella miljö.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Kör följande kommandon:

Förfrågan

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Brödtext

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Exempel

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Visa en lista över grupper i en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till **Azure AD**.

1. I den vänstra rutan väljer du **administrativa enheter** och väljer sedan den administrativa enhet vars grupper du vill visa. Som standard är **alla användare** markerade i det vänstra fönstret. 

1. I den vänstra rutan väljer du **grupper**. I den högra rutan visas en lista över grupper som är medlemmar i den valda administrativa enheten.

   ![Skärm bild av fönstret "grupper" som visar en lista över grupper i en administrativ enhet.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Använd PowerShell

Om du vill visa en lista över alla medlemmar i den administrativa enheten kör du följande kommando: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId
```

Om du vill visa alla grupper som är medlemmar i den administrativa enheten använder du följande kodfragment:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Kör följande kommando:

Förfrågan

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Brödtext

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Visa en lista över administrativa enheter för en grupp

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal går du till **Azure AD**.

1. I den vänstra rutan väljer du **grupper** för att visa en lista över grupper.

1. Välj en grupp för att öppna gruppens profil. 

1. I den vänstra rutan väljer du **administrativa enheter** för att visa en lista över de administrativa enheter där gruppen är medlem.

   ![Skärm bild av fönstret "administrativa enheter" som visar en lista med administrativa enheter som en grupp är tilldelad till.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Kör följande kommando:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Ta bort en grupp från en administrativ enhet

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan ta bort en grupp från en administrativ enhet i Azure Portal på något av två sätt:

- Ta bort den från en grupp översikt:

  1. I Azure Portal går du till **Azure AD**.
  1. I den vänstra rutan väljer du **grupper** och öppnar sedan profilen för gruppen som du vill ta bort från en administrativ enhet.
  1. I den vänstra rutan väljer du **administrativa enheter** för att visa en lista över de administrativa enheter som gruppen är tilldelad till. 
  1. Välj den administrativa enhet som du vill ta bort gruppen från och välj sedan **ta bort från administrativ enhet**.

     ![Skärm bild av fönstret "administrativa enheter" som visar en lista över grupper som har tilldelats den valda administrativa enheten.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Ta bort den från en administrativ enhet:

  1. I Azure Portal går du till **Azure AD**.
  1. I den vänstra rutan väljer du **administrativa enheter** och väljer sedan den administrativa enhet som gruppen är tilldelad till.
  1. I den vänstra rutan väljer du **grupper** för att visa en lista över alla grupper som är kopplade till den administrativa enheten.
  1. Välj den grupp som du vill ta bort och välj sedan **ta bort grupper**.

    ![Skärm bild av fönstret grupper som visar en lista över grupperna i en administrativ enhet.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Använd PowerShell

Kör följande kommando:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Använd Microsoft Graph

Kör följande kommando:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Nästa steg

- [Tilldela en roll till en administrativ enhet](admin-units-assign-roles.md)
- [Hantera användare i en administrativ enhet](admin-units-add-manage-users.md)
