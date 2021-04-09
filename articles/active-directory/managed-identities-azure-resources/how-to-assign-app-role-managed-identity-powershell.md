---
title: Tilldela en hanterad identitet till en program roll med hjälp av PowerShell – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitets åtkomst till ett annat programs roll med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732108"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Tilldela en hanterad identitets åtkomst till en program roll med hjälp av PowerShell

Hanterade identiteter för Azure-resurser ger Azure-tjänster med en identitet i Azure Active Directory. De fungerar utan att behöva autentiseringsuppgifter i din kod. Azure-tjänster använder den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering. Program roller ger en form av rollbaserad åtkomst kontroll och låter en tjänst implementera auktoriseringsregler.

I den här artikeln får du lära dig hur du tilldelar en hanterad identitet till en program roll som exponeras av ett annat program med hjälp av Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempel skripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med knappen **prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Tilldela en hanterad identitets åtkomst till ett annat programs app-roll

1. Aktivera hanterad identitet på en Azure-resurs, [till exempel en virtuell Azure-dator](qs-configure-powershell-windows-vm.md).

1. Hitta objekt-ID: t för den hanterade identitetens tjänst objekt.

   **För en systemtilldelad hanterad identitet** hittar du objekt-ID: t på Azure Portal på resursens **identitets** sida. Du kan också använda följande PowerShell-skript för att hitta objekt-ID: t. Du behöver resurs-ID för resursen som du skapade i steg 1, som är tillgänglig i Azure Portal på resursens **egenskaps** sida.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **För en användardefinierad hanterad identitet** kan du hitta objekt-ID: t för den hanterade identiteten på den Azure Portal på resursens **översikts** sida. Du kan också använda följande PowerShell-skript för att hitta objekt-ID: t. Du behöver resurs-ID för den användare som tilldelats den hanterade identiteten.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Skapa en ny program registrering för att representera tjänsten som din hanterade identitet skickar en begäran till. Hoppa över det här steget om det API eller den tjänst som exponerar den hanterade identiteten redan har ett huvud namn för tjänsten i din Azure AD-klient. Om du till exempel vill ge åtkomst till hanterad identitet till Microsoft Graph API kan du hoppa över det här steget.

1. Hitta objekt-ID för tjänst programmets tjänst objekt. Du kan hitta detta med hjälp av Azure Portal. Gå till Azure Active Directory och öppna sidan **företags program** och leta upp programmet och leta efter **objekt-ID**. Du kan också hitta tjänstens huvud objekt-ID med dess visnings namn med hjälp av följande PowerShell-skript:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Visnings namnen för programmen är inte unika, så du bör kontrol lera att du får rätt program tjänstens huvud namn.

1. Lägg till en [app-roll](../develop/howto-add-app-roles-in-azure-ad-apps.md) till det program som du skapade i steg 3. Du kan skapa rollen med hjälp av Azure Portal eller genom att använda Microsoft Graph. Du kan till exempel lägga till en app-roll så här:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Tilldela appen rollen till den hanterade identiteten. Du behöver följande information för att tilldela app-rollen:
    * `managedIdentityObjectId`: objekt-ID för den hanterade identitetens tjänst objekt, som du hittade i steg 2.
    * `serverServicePrincipalObjectId`: objekt-ID för Server programmets tjänst objekt, som du hittade i steg 4.
    * `appRoleId`: ID: t för den app-roll som exponeras av serverprogrammet, som du skapade i steg 5, i exemplet är app-roll-ID: t `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Kör följande PowerShell-skript för att lägga till roll tilldelningen:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Fullständigt skript

Det här exempel skriptet visar hur du tilldelar den hanterade identiteten för en Azure-webbapp till en app-roll.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Om du vill aktivera hanterad identitet på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med PowerShell](qs-configure-powershell-windows-vm.md).