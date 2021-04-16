---
title: Skapa anpassade roller för att hantera företagsappar i Azure Active Directory
description: Skapa och tilldela anpassade Azure AD-roller för åtkomst till företagsappar i Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534719"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Skapa anpassade roller för att hantera företagsappar i Azure Active Directory

Den här artikeln förklarar hur du skapar en anpassad roll med behörigheter för att hantera tilldelningar av företagsapp för användare och grupper i Azure Active Directory (Azure AD). Information om elementen i rolltilldelningar och innebörden av termer som undertyp, behörighet och egenskapsuppsättning finns i [översikten över anpassade roller.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Rollbehörigheter för företagsapp

Det finns två behörigheter för företagsappen som beskrivs i den här artikeln. Alla exempel använder uppdateringsbehörigheten.

* Om du vill läsa användar- och grupptilldelningar i omfånget beviljar du `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` behörigheten
* Om du vill hantera användar- och grupptilldelningar i omfånget beviljar du `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` behörigheten

Om du beviljar uppdateringsbehörigheten kan den tilldelade personen hantera tilldelningar av användare och grupper till företagsappar. Omfånget för användar- och/eller grupptilldelningar kan beviljas för ett enda program eller beviljas för alla program. Om det beviljas på organisationsomfattande nivå kan den tilldelande personen hantera tilldelningar för alla program. Om det görs på programnivå kan den tilldelande personen endast hantera tilldelningar för det angivna programmet.

Du beviljar uppdateringsbehörigheten i två steg:

1. Skapa en anpassad roll med behörighet `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Ge användare eller grupper behörighet att hantera användar- och grupptilldelningar till företagsappar. Då kan du ange omfånget till organisationsomfattande nivå eller till ett enda program.

## <a name="use-the-azure-ad-admin-center"></a>Använda Azure AD-administrationscentret

### <a name="create-a-new-custom-role"></a>Skapa en ny anpassad roll

>[!NOTE]
> Anpassade roller skapas och hanteras på organisationsomfattande nivå och är endast tillgängliga från organisationens översiktssida.

1. Logga in på [Azure AD-administrationscentret med](https://aad.portal.azure.com) behörigheten Privilegierad rolladministratör eller Global administratör i din organisation.
1. Välj **Azure Active Directory**, välj **Roller och administratörer** och välj sedan Ny anpassad **roll**.

    ![Lägga till en ny anpassad roll från rolllistan i Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. På **fliken Grundläggande** anger du "Hantera användar- och grupptilldelningar" för namnet på rollen och "Bevilja behörigheter för att hantera användar- och grupptilldelningar" som rollbeskrivning och väljer **sedan Nästa.**

    ![Ange ett namn och en beskrivning för den anpassade rollen](./media/custom-enterprise-apps/role-name-and-description.png)

1. På fliken **Behörigheter** anger du "microsoft.directory/servicePrincipals/appRoleAssignedTo/update" i sökrutan. Markera sedan kryssrutorna bredvid önskade behörigheter och välj sedan **Nästa.**

    ![Lägga till behörigheter till den anpassade rollen](./media/custom-enterprise-apps/role-custom-permissions.png)

1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **Skapa.**

    ![Nu kan du skapa den anpassade rollen](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Tilldela rollen till en användare med hjälp av Azure AD-portalen

1. Logga in på [Azure AD-administrationscentret med behörigheter](https://aad.portal.azure.com) för privilegierad rolladministratör.
1. Välj **Azure Active Directory** och välj sedan **Roller och administratörer**.
1. Välj rollen **Bevilja behörigheter för att hantera användar- och grupptilldelningar.**

    ![Öppna Roller och administratörer och sök efter den anpassade rollen](./media/custom-enterprise-apps/select-custom-role.png)

1. Välj **Lägg till** tilldelning, välj önskad användare och klicka sedan på Välj **för** att lägga till rolltilldelning för användaren.

    ![Lägga till en tilldelning för den anpassade rollen för användaren](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Tilldelningstips

* Om du vill bevilja behörigheter för tilldelningar för att hantera användare och gruppåtkomst  för alla företagsappar i hela organisationen börjar du med listan Över roller och administratörer i hela organisationen på översiktssidan för Azure **AD** för din organisation.
* Om du vill bevilja behörigheter för att tilldela användare för att hantera användare och gruppåtkomst  för en specifik företagsapp går du till appen i Azure AD och öppnar den i listan Roller och administratörer för appen. Välj den nya anpassade rollen och slutför användar- eller grupptilldelningen. De som tilldelas kan endast hantera användare och gruppåtkomst för den specifika appen.
* Om du vill testa din anpassade rolltilldelning loggar du  in som mottagare och öppnar sidan Användare och grupper i ett program för att kontrollera att **alternativet Lägg** till användare är aktiverat.

    ![Verifiera användarbehörigheterna](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Använda Azure AD PowerShell

Mer information finns i Skapa [och tilldela en anpassad roll och Tilldela anpassade](custom-create.md) roller med [resursomfång med hjälp av PowerShell.](custom-assign-powershell.md)

Installera först Azure AD PowerShell-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importera sedan Azure AD PowerShell-förhandsgranskningsmodulen med följande kommando:

```powershell
Import-Module -Name AzureADPreview
```

Kontrollera att modulen är redo att användas genom att matcha den version som returneras av följande kommando med den som anges här:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Skapa en anpassad roll

Skapa en ny roll med följande PowerShell-skript:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>Tilldela den anpassade rollen

Tilldela rollen med hjälp av det här PowerShell-skriptet.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>Använda Microsoft Graph API

Skapa en anpassad roll med hjälp av det angivna exemplet i Microsoft Graph-API:et. Mer information finns i Skapa [och tilldela en anpassad roll och Tilldela anpassade](custom-create.md) [administratörsroller med hjälp av Microsoft Graph API.](custom-assign-graph.md)

HTTP-begäran för att skapa den anpassade rollen.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Tilldela den anpassade rollen med hjälp Microsoft Graph API

Rolltilldelningen kombinerar ett säkerhetsobjekt-ID (som kan vara en användare eller ett tjänsthuvudnamn), ett rolldefinitions-ID och ett Azure AD-resursomfång. Mer information om elementen i en rolltilldelning finns i översikten [över anpassade roller](custom-overview.md)

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

* [Utforska tillgängliga anpassade rollbehörigheter för företagsappar](custom-enterprise-app-permissions.md)
