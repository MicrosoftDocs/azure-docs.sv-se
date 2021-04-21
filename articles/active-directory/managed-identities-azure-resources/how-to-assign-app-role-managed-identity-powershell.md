---
title: Tilldela en hanterad identitet till en programroll med hjälp av PowerShell – Azure AD
description: Stegvisa instruktioner för att tilldela en hanterad identitet åtkomst till ett annat programs roll med hjälp av PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784919"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Tilldela en hanterad identitet åtkomst till en programroll med hjälp av PowerShell

Hanterade identiteter för Azure-resurser ger Azure-tjänster en identitet i Azure Active Directory. De fungerar utan att du behöver autentiseringsuppgifter i koden. Azure-tjänster använder den här identiteten för att autentisera mot tjänster som stöder Azure AD-autentisering. Programroller tillhandahåller en form av rollbaserad åtkomstkontroll och gör det möjligt för en tjänst att implementera auktoriseringsregler.

I den här artikeln får du lära dig hur du tilldelar en hanterad identitet till en programroll som exponeras av ett annat program med hjälp av Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du inte är bekant med hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Se till att granska skillnaden [mellan en system-tilldelad och användar tilldelad hanterad identitet](overview.md#managed-identity-types)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill köra exempelskripten har du två alternativ:
    - Använd [Azure Cloud Shell](../../cloud-shell/overview.md), som du kan öppna med hjälp av knappen **Prova** i det övre högra hörnet av kodblock.
    - Kör skript lokalt genom att installera den senaste versionen av [Azure AD PowerShell.](/powershell/azure/active-directory/install-adv2)

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Tilldela en hanterad identitet åtkomst till ett annat programs approll

1. Aktivera hanterad identitet på en Azure-resurs, [till exempel en virtuell Azure-dator.](qs-configure-powershell-windows-vm.md)

1. Hitta objekt-ID:t för den hanterade identitetens tjänsthuvudnamn.

   **För en systemtilldelning av en hanterad** identitet hittar du objekt-ID:t Azure Portal på resursens **identitetssida.** Du kan också använda följande PowerShell-skript för att hitta objekt-ID:t. Du behöver resurs-ID:t för den resurs som du skapade i steg 1, som finns i Azure Portal på resursens **egenskapssida.**

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **För en användar tilldelad hanterad** identitet hittar du objekt-ID:t för den hanterade identiteten på Azure Portal på resursens **översiktssida.** Du kan också använda följande PowerShell-skript för att hitta objekt-ID:t. Du behöver resurs-ID:t för den användar tilldelade hanterade identiteten.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Skapa en ny programregistrering som representerar den tjänst som din hanterade identitet skickar en begäran till. Om API:et eller tjänsten som exponerar approlls grant för den hanterade identiteten redan har ett tjänsthuvudnamn i Azure AD-klienten kan du hoppa över det här steget. Om du till exempel vill ge den hanterade identiteten åtkomst till Microsoft Graph API kan du hoppa över det här steget.

1. Hitta objekt-ID:t för tjänstprogrammets huvudnamn för tjänsten. Du hittar detta med hjälp av Azure Portal. Gå till Azure Active Directory och öppna **sidan Företagsprogram.** Leta sedan reda på programmet och leta upp **objekt-ID:t**. Du kan också hitta objekt-ID:t för tjänstens huvudnamn efter dess visningsnamn med hjälp av följande PowerShell-skript:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Visningsnamn för program är inte unika, så du bör kontrollera att du har rätt programtjänsthuvudnamn.

1. Lägg till [en approll](../develop/howto-add-app-roles-in-azure-ad-apps.md) i programmet som du skapade i steg 3. Du kan skapa rollen med hjälp av Azure Portal eller Microsoft Graph. Du kan till exempel lägga till en approll så här:

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

1. Tilldela approllen till den hanterade identiteten. Du behöver följande information för att tilldela approllen:
    * `managedIdentityObjectId`: objekt-ID:t för den hanterade identitetens tjänsthuvudnamn, som du hittade i steg 2.
    * `serverServicePrincipalObjectId`: objekt-ID:t för serverprogrammets tjänsthuvudnamn, som du hittade i steg 4.
    * `appRoleId`: ID:t för den approll som exponeras av serverappen, som du genererade i steg 5 – i exemplet är approlls-ID:t `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Kör följande PowerShell-skript för att lägga till rolltilldelningen:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Fullständigt skript

Det här exempelskriptet visar hur du tilldelar en hanterad identitet för en Azure-webbapp till en approll.

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

- [Översikt över hanterad identitet för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterade identiteter på en virtuell Azure-dator finns i Konfigurera hanterade [identiteter för Azure-resurser på en virtuell Azure-dator med hjälp av PowerShell.](qs-configure-powershell-windows-vm.md)
