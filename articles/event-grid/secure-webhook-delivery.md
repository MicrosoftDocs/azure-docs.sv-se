---
title: Säker webhook-leverans med Azure AD i Azure Event Grid
description: Beskriver hur du levererar händelser till HTTPS-slutpunkter som skyddas av Azure Active Directory att använda Azure Event Grid
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: be38edfb4edfee5b3a3b42d7b1c5a44c9d72ceef
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035453"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicera händelser till Azure Active Directory-skyddade slutpunkter

Den här artikeln beskriver hur du kan dra nytta av Azure Active Directory för att skydda anslutningen mellan händelse prenumerationen och webhook-slutpunkten. En översikt över Azure AD-program och tjänst huvud namn finns i [Översikt över Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

I den här artikeln används Azure Portal för demonstration, men funktionen kan också aktive ras med CLI, PowerShell eller SDK: er.


## <a name="create-an-azure-ad-application"></a>Skapa ett Azure AD-program

Börja med att skapa ett Azure AD-program för den skyddade slut punkten. Se https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Konfigurera ditt skyddade API så att det anropas av en daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Aktivera Event Grid att använda Azure AD-programmet
Det här avsnittet visar hur du aktiverar Event Grid att använda Azure AD-programmet. 

> [!NOTE]
> Du måste vara medlem i [rollen Azure AD-programadministratör](../active-directory/roles/permissions-reference.md#all-roles) för att köra det här skriptet.

### <a name="connect-to-your-azure-tenant"></a>Anslut till din Azure-klient
Anslut först till din Azure-klient med hjälp av `Connect-AzureAD` kommandot. 

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"
Connect-AzureAD -TenantId $myTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Skapa Microsoft. EventGrid-tjänstens huvud namn
Kör följande skript för att skapa tjänstens huvud namn för **Microsoft. EventGrid** om det inte redan finns. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
} else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Skapa en roll för ditt program   
Kör följande skript för att skapa en roll för ditt Azure AD-program. I det här exemplet är roll namnet: **AzureEventGridSecureWebhook**. Ändra PowerShell `$myTenantId` -skriptet för att använda ditt Azure AD-klient-ID och `$myAzureADApplicationObjectId` med objekt-ID: t för Azure AD-programmet

```PowerShell
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}

# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
} else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Lägg till Event Grid tjänstens huvud namn i rollen    
Kör nu `New-AzureADServiceAppRoleAssignment` kommandot för att tilldela Event Grid tjänstens huvud namn till den roll som du skapade i föregående steg. 

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Kör följande kommandon för att visa information som du kommer att använda nästa steg. 

```powershell    
Write-Host "My Azure AD Tenant Id: $myTenantId"
Write-Host "My Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Azure AD Application ObjectId: $($myApp.ObjectId)"
```
    
## <a name="configure-the-event-subscription"></a>Konfigurera händelse prenumerationen

I det skapande flödet för din händelse prenumeration väljer du slut punkts typ ' Web Hook '. När du har fått slut punkts-URI: n, klickar du på fliken Ytterligare funktioner överst på bladet skapa händelse prenumerationer.

![Välj typ av slut punkts typ webhook](./media/secure-webhook-delivery/select-webhook.png)

På fliken Ytterligare funktioner markerar du kryss rutan för "Använd AAD-autentisering" och konfigurerar klient-ID och program-ID:

* Kopiera Azure AD-klient-ID: t från utdata från skriptet och ange det i fältet AAD-klient-ID.
* Kopiera Azure AD-programmets ID från utdata från skriptet och ange det i fältet AAD-program-ID.

    ![Säker webhook-åtgärd](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Mer information om nyckeln för autentisering finns i [Event Grid säkerhet och autentisering](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
