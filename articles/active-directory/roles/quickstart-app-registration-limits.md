---
title: Ta bort gränser för att skapa registrerings program – Azure AD | Microsoft Docs
description: Tilldela en anpassad roll för att bevilja obegränsade registrerade appar i Azure AD-Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: quickstart
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48740c52e6713735957e1ebf00e8996800eabec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015785"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>Snabb start: bevilja behörighet att skapa obegränsade app-registreringar

I den här snabb starts guiden ska du skapa en anpassad roll med behörighet att skapa ett obegränsat antal registrerade appar och sedan tilldela rollen till en användare. Den tilldelade användaren kan sedan använda Azure AD-portalen, Azure AD PowerShell eller Microsoft Graph API för att skapa program registreringar. Till skillnad från den inbyggda rollen program utvecklare ger den här anpassade rollen möjlighet att skapa ett obegränsat antal program registreringar. Rollen programutvecklare ger möjlighet, men det totala antalet skapade objekt är begränsat till 250 för att förhindra att [objekt kvoten för hela katalogen](../enterprise-users/directory-service-limits-restrictions.md)visas. Den minst privilegierade rollen som krävs för att skapa och tilldela anpassade Azure AD-roller är den privilegierade roll administratören.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-custom-role-using-the-azure-ad-portal"></a>Skapa en anpassad roll med hjälp av Azure AD-portalen

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**, Välj **roller och administratörer** och välj sedan **ny anpassad roll**.

    ![Skapa eller redigera roller från sidan roller och administratörer](./media/quickstart-app-registration-limits/new-custom-role.png)

1. På fliken **grundläggande** anger du "program registrerings skapare" som namn på rollen och "kan skapa ett obegränsat antal program registreringar" för roll beskrivningen, och välj sedan **Nästa**.

    ![Ange ett namn och en beskrivning för en anpassad roll på fliken grundläggande](./media/quickstart-app-registration-limits/basics-tab.png)

1. På fliken **behörigheter** anger du "Microsoft. Directory/Applications/Create" i sökrutan och markerar kryss rutorna bredvid önskade behörigheter och väljer sedan **Nästa**.

    ![Välj behörigheter för en anpassad roll på fliken behörigheter](./media/quickstart-app-registration-limits/permissions-tab.png)

1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **skapa**.

### <a name="assign-the-role-in-the-azure-ad-portal"></a>Tilldela rollen i Azure AD-portalen

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i din Azure AD-organisation.
1. Välj **Azure Active Directory** och välj sedan **roller och administratörer**.
1. Välj rollen program registrering skapare och välj **Lägg till tilldelning**.
1. Välj önskad användare och klicka på **Välj** för att lägga till användaren i rollen.

Klart! I den här snabb starten har du skapat en anpassad roll med behörighet att skapa ett obegränsat antal registrerade appar och sedan tilldela rollen till en användare.

> [!TIP]
> Om du vill tilldela rollen till ett program med hjälp av Azure AD-portalen anger du namnet på programmet i rutan Sök på sidan tilldelning. Program visas inte som standard i listan, men returneras i Sök resultaten.

### <a name="app-registration-permissions"></a>Behörigheter för appregistrering

Det finns två behörigheter som kan användas för att ge möjlighet att skapa program registreringar, var och en med olika beteenden.

- Microsoft. Directory/Applications/createAsOwner: om du tilldelar behörighets resultatet i skaparen läggs det till som första ägare av den skapade app-registreringen, och den skapade registrerings program registreringen räknas mot skapare objekt kvoten 250 skapade objekt.
- Microsoft. Directory/program/skapa: om du tilldelar det här behörighets resultatet i skaparen läggs det inte till som första ägare till den skapade app-registreringen, och den skapade app-registreringen räknas inte mot skapare objekt kvoten 250 skapade objekt. Använd den här behörigheten noggrant, eftersom det inte finns något hinder för att skapa registrerings program förrän kvoten på katalog nivå har nåtts. Om båda behörigheterna tilldelas, prioriteras den här behörigheten.

## <a name="create-a-custom-role-in-azure-ad-powershell"></a>Skapa en anpassad roll i Azure AD PowerShell

### <a name="prepare-powershell"></a>Förbered PowerShell

Installera först Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importera sedan för hands versionen av Azure AD PowerShell med följande kommando:

```powershell
Import-Module -Name AzureADPreview
```

Kontrol lera att modulen är redo att användas genom att matcha den version som returneras av följande kommando till den som visas här:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role-in-azure-ad-powershell"></a>Skapa den anpassade rollen i Azure AD PowerShell

Skapa en ny roll med hjälp av följande PowerShell-skript:

```powershell

# Basic role information
$displayName = "Application Registration Creator"
$description = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
    "microsoft.directory/applications/createAsOwner"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customRole = New-AzureAdMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-role-in-azure-ad-powershell"></a>Tilldela rollen i Azure AD PowerShell

Tilldela rollen med hjälp av följande PowerShell-skript:

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'Adam@contoso.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Registration Creator'"

# Get resource scope for assignment
$resourceScope = '/'

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-custom-role-in-the-microsoft-graph-api"></a>Skapa en anpassad roll i Microsoft Graph API

HTTP-begäran om att skapa den anpassade rollen.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

Brödtext

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                    "microsoft.directory/applications/createAsOwner"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-role-in-the-microsoft-graph-api"></a>Tilldela rollen i Microsoft Graph API

Roll tilldelningen kombinerar ett säkerhets objekt-ID (som kan vara en användare eller tjänstens huvud namn), ett roll Definitions-ID och ett Azure AD-resursprogram.

HTTP-begäran om att tilldela en anpassad roll.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Brödtext

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Nästa steg

- Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roll tilldelningar i Azure AD finns i [tilldela administratörs roller](permissions-reference.md).
- Mer information om standard användar behörigheter finns i [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md).
