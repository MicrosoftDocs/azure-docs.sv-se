---
title: Tilldela och lista roller med omfång för administrativ enhet – Azure Active Directory | Microsoft Docs
description: Använd administrativa enheter för att begränsa omfånget för rolltilldelningar i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505266"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Tilldela begränsade roller till en administrativ enhet

I Azure Active Directory (Azure AD) kan du för mer detaljerad administrativ kontroll tilldela användare till en Azure AD-roll med ett omfång som är begränsat till en eller flera administrativa enheter.

Information om hur du förbereder för att använda PowerShell Microsoft Graph för hantering av administrativa enheter finns [i Kom igång.](admin-units-manage.md#get-started)

## <a name="available-roles"></a>Tillgängliga roller

Roll  |  Beskrivning
----- |  -----------
Autentiseringsadministratör  |  Har åtkomst till att visa, ange och återställa autentiseringsmetodinformation för alla icke-administratörsanvändare endast i den tilldelade administrativa enheten.
Gruppadministratör  |  Kan hantera alla aspekter av inställningar för grupper och grupper, till exempel namngivnings- och förfalloprinciper, endast i den tilldelade administrativa enheten.
Supportadministratör  |  Kan endast återställa lösenord för icke-administratörer och supportavdelningsadministratörer i den tilldelade administrativa enheten.
Licensadministratör  |  Kan endast tilldela, ta bort och uppdatera licenstilldelningar inom den administrativa enheten.
Lösenordsadministratör  |  Kan endast återställa lösenord för icke-administratörer och lösenordsadministratörer inom den tilldelade administrativa enheten.
Användaradministratör  |  Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer inom den tilldelade administrativa enheten.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Säkerhetsobjekt som kan tilldelas till en begränsad roll

Följande säkerhetsobjekt kan tilldelas till en roll med en administrativ enhetsomfång:

* Användare
* Rolldeklara molngrupper (förhandsversion)
* Tjänstens huvudnamn (SPN)

## <a name="assign-a-scoped-role"></a>Tilldela en begränsad roll

Du kan tilldela en begränsad roll med hjälp av Azure Portal, PowerShell eller Microsoft Graph.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

1. I Azure Portal du till **Azure AD**.

1. Välj **Administrativa enheter** och välj sedan den administrativa enhet som du vill tilldela ett användarrollomfång till. 

1. I den vänstra rutan väljer du **Roller och administratörer för** att visa en lista över alla tillgängliga roller.

   ![Skärmbild av fönstret "Roll och administratörer" för att välja en administrativ enhet vars rollomfång du vill tilldela.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Välj den roll som ska tilldelas och välj sedan **Lägg till tilldelningar.** 

1. I fönstret **Lägg till tilldelningar** väljer du en eller flera användare som ska tilldelas rollen.

   ![Välj rollen som ska omfattas och välj sedan Lägg till tilldelningar](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Information om hur du tilldelar en roll på en administrativ enhet med hjälp Azure AD Privileged Identity Management (PIM) finns i [Tilldela Azure AD-roller i PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope).

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

Du kan ändra det markerade avsnittet efter behov för den specifika miljön.

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Förfrågan

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Brödtext

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Visa en lista över omfångsadministratörer i en administrativ enhet

Du kan visa en lista över omfångsadministratörer med hjälp Azure Portal, PowerShell eller Microsoft Graph.

### <a name="use-the-azure-portal"></a>Använda Azure-portalen

Du kan visa alla rolltilldelningar som skapats med ett administrativt enhetsomfång i [avsnittet Administrativa enheter i Azure AD.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) 

1. I Azure Portal du till **Azure AD**.

1. I den vänstra rutan väljer **du Administrativa enheter** och sedan den administrativa enheten för listan över rolltilldelningar som du vill visa. 

1. Välj **Roller och administratörer** och öppna sedan en roll för att visa tilldelningarna i den administrativa enheten.

### <a name="use-powershell"></a>Använd PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

Du kan ändra det markerade avsnittet efter behov för din specifika miljö.

### <a name="use-microsoft-graph"></a>Använda Microsoft Graph

Förfrågan

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Brödtext

```http
{}
```

## <a name="next-steps"></a>Nästa steg

- [Använda molngrupper för att hantera rolltilldelningar](groups-concept.md)
- [Felsöka roller som tilldelats till molngrupper](groups-faq-troubleshooting.md)
