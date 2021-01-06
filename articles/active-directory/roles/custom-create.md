---
title: Skapa anpassade roller i rollbaserad åtkomst kontroll i Azure AD | Microsoft Docs
description: Skapa och tilldela anpassade Azure AD-roller med resurs omfång på Azure Active Directory resurser.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 01/05/2021
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0adb88cd49dd3de8649703ed45821c68a31776e
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914475"
---
# <a name="create-and-assign-a-custom-role-in-azure-active-directory"></a>Skapa och tilldela en anpassad roll i Azure Active Directory

Den här artikeln beskriver hur du skapar nya anpassade roller i Azure Active Directory (Azure AD). Grunderna för anpassade roller finns i [Översikt över anpassade roller](custom-overview.md). Rollen kan tilldelas antingen på katalog nivå omfånget eller endast för en app Registration Resource-omfattning.

Du kan skapa anpassade roller på fliken [roller och administratörer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) på översikts sidan för Azure AD.

## <a name="create-a-role-in-the-azure-portal"></a>Skapa en roll i Azure Portal

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Skapa en ny anpassad roll för att ge åtkomst till hantering av app-registreringar

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med privilegierade roll administratörer eller globala administratörs behörigheter i Azure AD-organisationen.
1. Välj **Azure Active Directory**  >  **roller och administratörer**  >  **ny anpassad roll**.

   ![Skapa eller redigera roller från sidan roller och administratörer](./media/custom-create/new-custom-role.png)

1. På fliken **grundläggande** anger du ett namn och en beskrivning för rollen och klickar sedan på **Nästa**.

   ![Ange ett namn och en beskrivning för en anpassad roll på fliken grundläggande](./media/custom-create/basics-tab.png)

1. På fliken **behörigheter** väljer du de behörigheter som krävs för att hantera grundläggande egenskaper och egenskaper för autentiseringsuppgifter för app-registreringar. En detaljerad beskrivning av varje behörighet finns [under typer och behörigheter för program registrering i Azure Active Directory](custom-available-permissions.md).
   1. Ange först "autentiseringsuppgifter" i Sök fältet och välj `microsoft.directory/applications/credentials/update` behörigheten.

      ![Välj behörigheter för en anpassad roll på fliken behörigheter](./media/custom-create/permissions-tab.png)

   1. Ange sedan "grundläggande" i Sök fältet, Välj `microsoft.directory/applications/basic/update` behörigheten och klicka sedan på **Nästa**.
1. På fliken **Granska + skapa** granskar du behörigheterna och väljer **skapa**.

Den anpassade rollen visas i listan över tillgängliga roller att tilldela.

## <a name="create-a-role-using-powershell"></a>Skapa en roll med PowerShell

### <a name="prepare-powershell"></a>Förbered PowerShell

Först måste du [Ladda ned Azure AD Preview PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

Använd följande kommandon för att installera Azure AD PowerShell-modulen:

``` PowerShell
install-module azureadpreview 
import-module azureadpreview 
```

Kontrol lera att modulen är redo att användas genom att använda följande kommando:

``` PowerShell
get-module azureadpreview 

  ModuleType Version      Name                         ExportedCommands 
  ---------- ---------    ----                         ---------------- 
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...} 
```

### <a name="connect-to-azure"></a>Anslut till Azure

Använd följande kommando för att ansluta till Azure Active Directory:

``` PowerShell
Connect-AzureAD
```

### <a name="create-the-custom-role"></a>Skapa den anpassade rollen

Skapa en ny roll med hjälp av följande PowerShell-skript:

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Tilldela den anpassade rollen med hjälp av Azure AD PowerShell

Tilldela rollen med hjälp av PowerShell-skriptet nedan:

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="create-a-role-with-graph-api"></a>Skapa en roll med Graph API

1. Skapa roll definitionen.

    HTTP-begäran om att skapa en anpassad roll definition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    Brödtext

    ``` HTTP
    {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
    }
    ```

    > [!Note]
    > `"templateId": "GUID"`Är en valfri parameter som skickas i bröd texten, beroende på kravet. Om du har ett krav för att skapa flera olika anpassade roller med vanliga parametrar är det bäst att skapa en mall och definiera ett `templateId` värde. Du kan generera ett `templateId` värde i förväg genom att använda PowerShell-cmdleten `(New-Guid).Guid` . 

1. Skapa roll tilldelningen.

    HTTP-begäran om att skapa en anpassad roll definition.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    Brödtext

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="assign-a-custom-role-scoped-to-a-resource"></a>Tilldela en anpassad roll som är begränsad till en resurs

Precis som inbyggda roller tilldelas anpassade roller som standard till standard området för hela organisationen för att ge åtkomst behörigheter över alla app-registreringar i din organisation. Men till skillnad från inbyggda roller kan anpassade roller också tilldelas i omfånget för en enskild Azure AD-resurs. På så sätt kan du ge användaren behörighet att uppdatera autentiseringsuppgifter och grundläggande egenskaper för en enskild app utan att behöva skapa en andra anpassad roll.

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com) med programutvecklare behörigheter i Azure AD-organisationen.
1. Välj **Appregistreringar**.
1. Välj den app-registrering som du beviljar åtkomst för att hantera. Du kanske måste välja **alla program** om du vill se en fullständig lista över app-registreringar i din Azure AD-organisation.

    ![Välj app-registrering som ett resurs omfång för en roll tilldelning](./media/custom-create/appreg-all-apps.png)

1. I appens registrering väljer du **roller och administratörer**. Om du inte redan har skapat ett följer du anvisningarna i [föregående procedur](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Välj rollen för att öppna **tilldelnings** sidan.
1. Välj **Lägg till tilldelning** för att lägga till en användare. Användaren beviljas alla behörigheter enbart för den valda program registreringen.

## <a name="next-steps"></a>Nästa steg

- Du kan gärna dela med oss i [Azure AD-forumet för administrativa roller](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Mer information om roller och tilldelning av administratörs roller finns i [tilldela administratörs roller](permissions-reference.md).
- För standard användar behörigheter, se en [jämförelse av standard behörigheter för gäst-och medlems användare](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2froles%2fcontext%2fugr-context).