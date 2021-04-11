---
title: Säker webhook-leverans med Azure AD i Azure Event Grid
description: Beskriver hur du levererar händelser till HTTPS-slutpunkter som skyddas av Azure Active Directory att använda Azure Event Grid
ms.topic: how-to
ms.date: 03/20/2021
ms.openlocfilehash: 1298910db78ba468dd9744e84ee4629161e0a776
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076044"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicera händelser till Azure Active Directory-skyddade slutpunkter
Den här artikeln beskriver hur du använder Azure Active Directory (Azure AD) för att skydda anslutningen mellan **händelse prenumerationen** och **webhook-slutpunkten**. En översikt över Azure AD-program och tjänst huvud namn finns i [Översikt över Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

I den här artikeln används Azure Portal för demonstration, men funktionen kan också aktive ras med CLI, PowerShell eller SDK: er.


## <a name="create-an-azure-ad-application"></a>Skapa ett Azure AD-program
Registrera din webhook med Azure AD genom att skapa ett Azure AD-program för den skyddade slut punkten. Se [Scenario: skyddat webb-API](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview). Konfigurera ditt skyddade API så att det anropas av en daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Aktivera Event Grid att använda Azure AD-programmet
Det här avsnittet visar hur du aktiverar Event Grid att använda Azure AD-programmet. 

> [!NOTE]
> Du måste vara medlem i [rollen Azure AD-programadministratör](../active-directory/roles/permissions-reference.md#all-roles) för att köra det här skriptet.

### <a name="connect-to-your-azure-tenant"></a>Anslut till din Azure-klient
Anslut först till din Azure-klient med hjälp av `Connect-AzureAD` kommandot. 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Skapa Microsoft. EventGrid-tjänstens huvud namn
Kör följande skript för att skapa tjänstens huvud namn för **Microsoft. EventGrid** om det inte redan finns. 

```PowerShell
# This is the "Azure Event Grid" Azure Active Directory (AAD) AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"

# Create the "Azure Event Grid" AAD Application service principal if it doesn't exist
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" AAD Application and add it to the role
    Write-Host "Creating the Azure Event Grid service principal"
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
```

### <a name="create-a-role-for-your-application"></a>Skapa en roll för ditt program   
Kör följande skript för att skapa en roll för ditt Azure AD-program. I det här exemplet är roll namnet: **AzureEventGridSecureWebhookSubscriber**. Ändra PowerShell `$myTenantId` -skriptet för att använda ditt Azure AD-klient-ID och `$myAzureADApplicationObjectId` med objekt-ID: t för Azure AD-programmet

```PowerShell
# This is your Webhook's Azure AD Application's ObjectId. 
$myWebhookAadApplicationObjectId = "<Your webhook's aad application object id>"

# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhookSubscriber"
       
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.AllowedMemberTypes.Add("User");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
       
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myWebhookAadApplicationObjectId
$myAppRoles = $myApp.AppRoles

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
       
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{      
    # Add our new role to the Azure AD Application
    Write-Host "Creating the Azure Event Grid role in Azure Ad Application: " $myWebhookAadApplicationObjectId
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}

# print application's roles
Write-Host "My Azure AD Application's Roles: "
Write-Host $myAppRoles

```

### <a name="create-a-role-assignment"></a>Skapa en roll tilldelning
Roll tilldelningen ska skapas i webhook-Azure AD App för AAD-appen eller AAD-användaren som skapar händelse prenumerationen. Använd något av skripten nedan, beroende på om en AAD-app eller AAD-användare skapar händelse prenumerationen.

#### <a name="option-a-create-a-role-assignment-for-event-subscription-aad-app"></a>Alternativ A. skapa en roll tilldelning för AAD-appen för händelse prenumerationer 

```powershell
# This is the app id of the application which will create event subscription. Set to $null if you are not assigning the role to app.
$eventSubscriptionWriterAppId = "<the app id of the application which will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")

$eventSubscriptionWriterSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventSubscriptionWriterAppId + "'")
if ($eventSubscriptionWriterSP -eq $null)
{
        $eventSubscriptionWriterSP = New-AzureADServicePrincipal -AppId $eventSubscriptionWriterAppId
}

Write-Host "Creating the Azure Ad App Role assignment for application: " $eventSubscriptionWriterAppId
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="option-b-create-a-role-assignment-for-event-subscription-aad-user"></a>Alternativ B. skapa en roll tilldelning för AAD-användare för händelse prenumeration 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
New-AzureADUserAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="add-event-grid-service-principal-to-the-role"></a>Lägg till Event Grid tjänstens huvud namn i rollen
Kör kommandot New-AzureADServiceAppRoleAssignment för att tilldela Event Grid tjänstens huvud namn till den roll som du skapade i föregående steg.

```powershell
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
```

Kör följande kommandon för att visa information som du kommer att använda senare.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Konfigurera händelse prenumerationen
Följ dessa steg när du skapar en händelse prenumeration:

1. Välj typ av slut punkt som **Web Hook**. 
1. Ange slut punktens **URI**.

    ![Välj typ av slut punkts typ webhook](./media/secure-webhook-delivery/select-webhook.png)
1. Välj fliken **ytterligare funktioner** överst på sidan **Skapa händelse prenumerationer** .
1. Utför följande steg på fliken **ytterligare funktioner** :
    1. Välj **Använd AAD-autentisering** och konfigurera klient-ID och program-ID:
    1. Kopiera Azure AD-klient-ID: t från utdata från skriptet och ange det i fältet **AAD-klient-ID** .
    1. Kopiera Azure AD-programmets ID från utdata från skriptet och ange det i fältet **AAD-program-ID** .

        ![Säker webhook-åtgärd](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Mer information om nyckeln för autentisering finns i [Event Grid säkerhet och autentisering](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
