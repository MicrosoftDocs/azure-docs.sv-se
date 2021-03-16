---
title: Skapa anpassade roller för att hantera företags program i Azure Active Directory
description: Skapa och tilldela anpassade Azure AD-roller för åtkomst till företags program i Azure Active Directory
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
ms.openlocfilehash: 1a76e2d37e9dcdd285a8608fdbfd715bfb834eb8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467756"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Skapa anpassade roller för att hantera företags program i Azure Active Directory

Den här artikeln beskriver hur du skapar en anpassad roll med behörigheter för att hantera tilldelningar av företags program för användare och grupper i Azure Active Directory (Azure AD). För elementen i roll tilldelningarna och innebörden av termer som undertyp, behörighet och egenskaps uppsättning, se [översikten över anpassade roller](custom-overview.md).

## <a name="enterprise-app-role-permissions"></a>Roll behörigheter för företags program

Det finns två program behörigheter för företag som beskrivs i den här artikeln. Alla exempel använder Update-behörighet.

* Om du vill läsa användar-och grupp tilldelningarna i omfånget beviljar du `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` behörigheten
* Tilldela behörigheten för att hantera användar-och grupp tilldelningar i omfånget `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`

Om du beviljar behörigheten Uppdatera får du en person som kan hantera tilldelningar av användare och grupper till företags program. Omfattningen av användar-och/eller grupp tilldelningar kan beviljas för ett enskilt program eller beviljas för alla program. Om den tilldelas på en organisations nivå kan den tilldelade personen hantera tilldelningar för alla program. Om det görs på en program nivå kan den tilldelade som tilldelas bara hantera tilldelningar för det angivna programmet.

Beviljande av uppdaterings behörighet görs i två steg:

1. Skapa en anpassad roll med behörighet `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Bevilja användare eller grupper behörigheter att hantera användar-och grupp tilldelningar till företags program. Det är när du kan ställa in omfånget på nivån för hela organisationen eller i ett enda program.

## <a name="use-the-azure-ad-admin-center"></a>Använda Azure AD administrations Center

### <a name="create-a-new-custom-role"></a>Skapa en ny anpassad roll

>[!NOTE]
> Anpassade roller skapas och hanteras på en nivå med hela organisationen och är bara tillgängliga från organisationens översikts sida.

1. Logga in på [administrations Center för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i din organisation.
1. Välj **Azure Active Directory**, Välj **roller och administratörer** och välj sedan **ny anpassad roll**.

    ![Lägg till en ny anpassad roll från listan roller i Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. På fliken **grundläggande** anger du "hantera användar-och grupp tilldelningar" som namn på rollen och "bevilja behörigheter för att hantera användar-och grupp tilldelningar" för roll beskrivningen, och välj sedan **Nästa**.

    ![Ange ett namn och en beskrivning för den anpassade rollen](./media/custom-enterprise-apps/role-name-and-description.png)

1. På fliken **behörigheter** anger du "Microsoft. Directory/Service princip ALS/appRoleAssignedTo/Update" i sökrutan. Markera kryss rutorna bredvid önskade behörigheter och välj sedan **Nästa**.

    ![Lägg till behörigheterna till den anpassade rollen](./media/custom-enterprise-apps/role-custom-permissions.png)

1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **skapa**.

    ![Nu kan du skapa den anpassade rollen](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Tilldela rollen till en användare med hjälp av Azure AD-portalen

1. Logga in på [administrations Center för Azure AD](https://aad.portal.azure.com) med roll behörigheten privilegierad roll administratör.
1. Välj **Azure Active Directory** och välj sedan **roller och administratörer**.
1. Välj rollen **bevilja behörighet för att hantera användar-och grupp tilldelningar** .

    ![Öppna roller och administratörer och Sök efter den anpassade rollen](./media/custom-enterprise-apps/select-custom-role.png)

1. Välj **Lägg till tilldelning**, Välj önskad användare och klicka sedan på **Välj** för att lägga till roll tilldelning till användaren.

    ![Lägg till en tilldelning för den anpassade rollen till användaren](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Tilldelnings tips

* Om du vill ge behörighet till att hantera användare och grupp åtkomst för alla företags program i hela organisationen, kan du börja från listan **roller och administratörer** i organisationen på sidan Azure AD- **Översikt** för din organisation.
* Om du vill ge behörighet till att hantera användare och grupp åtkomst för en särskild företags app går du till appen i Azure AD och öppnar den i listan **roller och administratörer** för den appen. Välj den nya anpassade rollen och slutför användaren eller grupp tilldelningen. De som tilldelats kan endast hantera användare och grupp åtkomst för den aktuella appen.
* Om du vill testa den anpassade roll tilldelningen loggar du in som tilldelad och öppnar ett programs sida **användare och grupper** för att kontrol lera att alternativet **Lägg till användare** är aktiverat.

    ![Kontrol lera användar behörigheterna](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Använda Azure AD PowerShell

Mer information finns i [skapa och tilldela en anpassad roll](custom-create.md) och [tilldela anpassade roller med resurs omfång med hjälp av PowerShell](custom-assign-powershell.md).

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

### <a name="create-a-custom-role"></a>Skapa en anpassad roll

Skapa en ny roll med hjälp av följande PowerShell-skript:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Tilldela den anpassade rollen

Tilldela rollen med hjälp av det här PowerShell-skriptet.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>Använda Microsoft Graph API

Skapa en anpassad roll med hjälp av det medföljande exemplet i Microsoft Graph-API: et. Mer information finns i [skapa och tilldela en anpassad roll](custom-create.md) och [tilldela anpassade administratörs roller med hjälp av Microsoft Graph API](custom-assign-graph.md).

HTTP-begäran om att skapa den anpassade rollen.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Tilldela den anpassade rollen med hjälp av Microsoft Graph API

Roll tilldelningen kombinerar ett säkerhets objekt-ID (som kan vara en användare eller tjänstens huvud namn), ett roll Definitions-ID och ett Azure AD-resursprogram. Mer information om elementen i en roll tilldelning finns i [Översikt över anpassade roller](custom-overview.md)

HTTP-begäran om att tilldela en anpassad roll.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Nästa steg

* [Utforska tillgängliga anpassade roll behörigheter för företags program](custom-enterprise-app-permissions.md)
