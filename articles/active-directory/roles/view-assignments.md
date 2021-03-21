---
title: Visa en lista över Azure AD-roll tilldelningar
description: Nu kan du se och hantera medlemmar i en Azure Active Directory administratörs roll i Azure Active Directory administrations centret.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: de546ef091b1a8e996f286b0c9af45e93488b5b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467665"
---
# <a name="list-azure-ad-role-assignments"></a>Visa en lista över Azure AD-roll tilldelningar

Den här artikeln beskriver hur du visar en lista över roller som du har tilldelat i Azure Active Directory (Azure AD). I Azure Active Directory (Azure AD) kan roller tilldelas till en omfattning i hela organisationen eller med en omfattning för ett enda program.

- Roll tilldelningar i området för hela organisationen läggs till i och visas i listan över tilldelningar för enskilda program roller.
- Roll tilldelningar i det enskilda programområdeet läggs inte till och visas inte i listan över områdes tilldelningar i organisationen.

## <a name="list-role-assignments-in-the-azure-portal"></a>Lista roll tilldelningar i Azure Portal

Den här proceduren beskriver hur du visar roll tilldelningar med omfattning i hela organisationen.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**, Välj **roller och administratörer** och välj sedan en roll för att öppna den och visa dess egenskaper.
1. Välj **tilldelningar** för att lista roll tilldelningarna.

    ![Lista roll tilldelningar och behörigheter när du öppnar en roll i listan](./media/view-assignments/role-assignments.png)

## <a name="list-my-role-assignments"></a>Lista mina roll tilldelningar

Det är enkelt att lista dina egna behörigheter. Välj **din roll** på sidan **roller och administratörer** för att se de roller som för närvarande är tilldelade till dig.

## <a name="download-role-assignments"></a>Hämta roll tilldelningar

Om du vill hämta alla tilldelningar för en speciell roll väljer du en roll på sidan **roller och administratörer** och väljer sedan **Hämta roll tilldelningar**. En CSV-fil som visar tilldelningar för alla omfattningar för rollen hämtas.

![Hämta alla tilldelningar för en roll](./media/view-assignments/download-role-assignments.png)

## <a name="list-role-assignments-using-azure-ad-powershell"></a>Lista roll tilldelningar med hjälp av Azure AD PowerShell

I det här avsnittet beskrivs hur du visar tilldelningar för en roll med omfattning i hela organisationen. Den här artikeln använder modulen [Azure Active Directory PowerShell version 2](/powershell/module/azuread/#directory_roles) . Om du vill visa omfattnings tilldelningar för enskilda program med hjälp av PowerShell kan du använda cmdletarna i [tilldela anpassade roller med PowerShell](custom-assign-powershell.md).

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

### <a name="list-role-assignments"></a>Visa lista över rolltilldelningar

Exempel på hur du visar roll tilldelningarna.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="list-role-assignments-using-microsoft-graph-api"></a>Lista roll tilldelningar med Microsoft Graph API

I det här avsnittet beskrivs hur du visar roll tilldelningar med omfattning i hela organisationen.  Om du vill visa roll tilldelningar för en program omfattning med Graph API kan du använda åtgärderna i [tilldela anpassade roller med Graph API](custom-assign-graph.md).

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

## <a name="list-role-assignments-with-single-application-scope"></a>Lista roll tilldelningar med program med en enda applikation

I det här avsnittet beskrivs hur du visar roll tilldelningar med ett definitions område för ett enda program. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar** och välj sedan appens registrering för att visa dess egenskaper. Du kanske måste välja **alla program** om du vill se en fullständig lista över app-registreringar i din Azure AD-organisation.

    ![Skapa eller redigera registrerings program från sidan Appregistreringar](./media/view-assignments/app-reg-all-apps.png)

1. I appens registrering väljer du **roller och administratörer** och väljer sedan en roll för att visa dess egenskaper.

    ![Visa lista över program registrerings roll tilldelningar från sidan Appregistreringar](./media/view-assignments/app-reg-assignments.png)

1. Välj **tilldelningar** för att lista roll tilldelningarna. Om du öppnar sidan tilldelningar inifrån appens registrering visas roll tilldelningarna som är begränsade till den här Azure AD-resursen.

    ![Visa lista över program registrerings roll tilldelningar från egenskaperna för en app-registrering](./media/view-assignments/app-reg-assignments-2.png)

## <a name="next-steps"></a>Nästa steg

* Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md).
* För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
