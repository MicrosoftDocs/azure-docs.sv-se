---
title: Visa anpassade roll tilldelningar i Azure AD-portalen | Microsoft Docs
description: Nu kan du se och hantera medlemmar i en administratörs roll för Azure AD i administrations centret för Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f95ede778752a8367f77717247fff82ead7131
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012079"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Visa anpassade roll tilldelningar i Azure Active Directory

Den här artikeln beskriver hur du visar anpassade roller som du har tilldelat i Azure Active Directory (Azure AD). I Azure Active Directory (Azure AD) kan roller tilldelas till en omfattning i hela organisationen eller med en omfattning för ett enda program.

- Roll tilldelningar i området för hela organisationen läggs till i och visas i listan över tilldelningar för enskilda program roller.
- Roll tilldelningar i det enskilda programområdeet läggs inte till och visas inte i listan över områdes tilldelningar i organisationen.

## <a name="view-role-assignments-in-the-azure-portal"></a>Visa roll tilldelningar i Azure Portal

Den här proceduren beskriver hur du visar tilldelningar för en roll med omfattning i hela organisationen.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**, Välj **roller och administratörer** och välj sedan en roll för att öppna den och visa dess egenskaper.
1. Välj **tilldelningar** för att Visa rollernas tilldelningar.

    ![Visa roll tilldelningar och behörigheter när du öppnar en roll i listan](./media/custom-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Visa roll tilldelningar med hjälp av Azure AD PowerShell

I det här avsnittet beskrivs hur du visar tilldelningar för en roll med omfattning i hela organisationen. Den här artikeln använder modulen [Azure Active Directory PowerShell version 2](/powershell/module/azuread/#directory_roles) . Om du vill visa omfattnings tilldelningar för enskilda program med hjälp av PowerShell kan du använda cmdletarna i [tilldela anpassade roller med PowerShell](./custom-assign-powershell.md).

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [Ladda ned Azure AD Preview PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

Använd följande kommandon för att installera Azure AD PowerShell-modulen:

``` PowerShell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Kontrol lera att modulen är redo att användas genom att använda följande kommando:

``` PowerShell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Visa tilldelningarna för en roll

Exempel på hur du visar tilldelningarna för en roll.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Visa roll tilldelningar med Microsoft Graph API

I det här avsnittet beskrivs hur du visar tilldelningar för en roll med omfattning i hela organisationen.  Om du vill visa omfattnings tilldelningar för enskilda program med hjälp av Graph API kan du använda åtgärderna i [tilldela anpassade roller med Graph API](./custom-assign-graph.md).

HTTP-begäran om att få en roll tilldelning för en specifik roll definition.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Svarsåtgärder

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>Visa tilldelningar för en program omfattning

I det här avsnittet beskrivs hur du visar tilldelningar för en roll med en omfattning i ett program. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar** och välj sedan appens registrering för att visa dess egenskaper. Du kanske måste välja **alla program** om du vill se en fullständig lista över app-registreringar i din Azure AD-organisation.

    ![Skapa eller redigera registrerings program från sidan Appregistreringar](./media/custom-view-assignments/appreg-all-apps.png)

1. I appens registrering väljer du **roller och administratörer** och väljer sedan en roll för att visa dess egenskaper.

    ![Visa roll tilldelningar för program registrering från sidan Appregistreringar](./media/custom-view-assignments/appreg-assignments.png)

1. Välj **tilldelningar** för att Visa rollernas tilldelningar. Om du öppnar vyn tilldelningar från App-registreringen visas de tilldelningar som är begränsade till den här Azure AD-resursen.

    ![Visa roll tilldelningar för program registrering från egenskaperna för en app-registrering](./media/custom-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
