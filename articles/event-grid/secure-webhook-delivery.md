---
title: Säker WebHook-leverans med Azure AD i Azure Event Grid
description: Beskriver hur du levererar händelser till HTTPS-slutpunkter som skyddas av Azure Active Directory med Azure Event Grid
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 4238087d977fa1102d1dd31d0cc9080d6308c175
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389697"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Publicera händelser till Azure Active Directory-skyddade slutpunkter
Den här artikeln beskriver hur du använder Azure Active Directory (Azure AD)  för att skydda anslutningen mellan din händelseprenumeration och **din webhook-slutpunkt.** En översikt över Azure AD-program och tjänstens huvudnamn finns i [Översikt över Microsoft Identity Platform (v2.0).](../active-directory/develop/v2-overview.md)

Den här artikeln använder Azure Portal för demonstration, men funktionen kan också aktiveras med HJÄLP av CLI, PowerShell eller SDK:erna.

> [!IMPORTANT]
> Ytterligare åtkomstkontroll har introducerats som en del av att skapa eller uppdatera händelseprenumeration den 30 mars 2021 för att åtgärda en säkerhetsrisk. Prenumerantklientens huvudnamn för tjänsten måste antingen vara ägare eller ha en roll tilldelad på målprogrammets tjänsthuvudnamn. Konfigurera om ditt AAD-program genom att följa de nya anvisningarna nedan.


## <a name="create-an-azure-ad-application"></a>Skapa ett Azure AD-program
Registrera din webhook med Azure AD genom att skapa ett Azure AD-program för din skyddade slutpunkt. Se [Scenario: Skyddat webb-API.](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview) Konfigurera ditt skyddade API så att det anropas av en daemon-app.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Aktivera Event Grid att använda ditt Azure AD-program
Det här avsnittet visar hur du aktiverar Event Grid att använda ditt Azure AD-program. 

> [!NOTE]
> Du måste vara medlem i rollen [Azure AD-programadministratör för att](../active-directory/roles/permissions-reference.md#all-roles) kunna köra det här skriptet.

### <a name="connect-to-your-azure-tenant"></a>Ansluta till din Azure-klientorganisation
Anslut först till din Azure-klientorganisation med hjälp av `Connect-AzureAD` kommandot . 

```PowerShell
$myWebhookAadTenantId = "<Your Webhook's Azure AD tenant id>"

Connect-AzureAD -TenantId $myWebhookAadTenantId
```

### <a name="create-microsofteventgrid-service-principal"></a>Skapa tjänstens huvudnamn för Microsoft.EventGrid
Kör följande skript för att skapa tjänstens huvudnamn **för Microsoft.EventGrid** om det inte redan finns. 

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
Kör följande skript för att skapa en roll för ditt Azure AD-program. I det här exemplet är rollnamnet: **AzureEventGridSecureWebhookSubscriber**. Ändra PowerShell-skriptets så `$myTenantId` att det använder ditt Azure AD-klientorganisations-ID och med `$myAzureADApplicationObjectId` objekt-ID:t för ditt Azure AD-program

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

### <a name="create-role-assignment-for-the-client-creating-event-subscription"></a>Skapa rolltilldelning för klienten som skapar händelseprenumerationen
Rolltilldelningen ska skapas i webhook-Azure AD App för AAD-appen eller AAD-användaren som skapar händelseprenumerationen. Använd något av skripten nedan beroende på om en AAD-app eller AAD-användare skapar händelseprenumerationen.

> [!IMPORTANT]
> Ytterligare åtkomstkontroll har introducerats som en del av att skapa eller uppdatera händelseprenumeration den 30 mars 2021 för att åtgärda en säkerhetsrisk. Prenumerantklientens huvudnamn för tjänsten måste antingen vara ägare eller ha en roll som tilldelats tjänstens huvudnamn för målprogrammet. Konfigurera om ditt AAD-program genom att följa de nya anvisningarna nedan.

#### <a name="create-role-assignment-for-an-event-subscription-aad-app"></a>Skapa rolltilldelning för en AAD-app för händelseprenumeration 

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
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterSP.ObjectId -PrincipalId $eventSubscriptionWriterSP.ObjectId
```

#### <a name="create-role-assignment-for-an-event-subscription-aad-user"></a>Skapa rolltilldelning för en AAD-användare för händelseprenumeration 

```powershell
# This is the user principal name of the user who will create event subscription. Set to $null if you are not assigning the role to user.
$eventSubscriptionWriterUserPrincipalName = "<the user principal name of the user who will create event subscription>"

$myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
Write-Host "Creating the Azure Ad App Role assignment for user: " $eventSubscriptionWriterUserPrincipalName
$eventSubscriptionWriterUser = Get-AzureAdUser -ObjectId $eventSubscriptionWriterUserPrincipalName
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADUserAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventSubscriptionWriterUser.ObjectId -PrincipalId $eventSubscriptionWriterUser.ObjectId
```

### <a name="create-role-assignment-for-event-grid-service-principal"></a>Skapa rolltilldelning för Event Grid tjänstens huvudnamn
Kör kommandot New-AzureADServiceAppRoleAssignment för att Event Grid tjänstens huvudnamn till rollen som du skapade i föregående steg.

```powershell
$eventGridAppRole = $myApp.AppRoles | Where-Object -Property "DisplayName" -eq -Value $eventGridRoleName
New-AzureADServiceAppRoleAssignment -Id $eventGridAppRole.Id -ResourceId $myServicePrincipal.ObjectId -ObjectId -PrincipalId $eventGridSP.ObjectId
```

Kör följande kommandon för att mata ut information som du kommer att använda senare.

```powershell
Write-Host "My Webhook's Azure AD Tenant Id:  $myWebhookAadTenantId"
Write-Host "My Webhook's Azure AD Application Id: $($myApp.AppId)"
Write-Host "My Webhook's Azure AD Application ObjectId Id$($myApp.ObjectId)"
```

    
## <a name="configure-the-event-subscription"></a>Konfigurera händelseprenumerationen
Följ dessa steg när du skapar en händelseprenumeration:

1. Välj slutpunktstypen Web **Hook**. 
1. Ange **slutpunkts-URI:en**.

    ![Välj webhook av slutpunktstyp](./media/secure-webhook-delivery/select-webhook.png)
1. Välj fliken **Ytterligare funktioner** överst på sidan **Skapa händelseprenumerationer.**
1. Gör följande **på fliken** Ytterligare funktioner:
    1. Välj **Använd AAD-autentisering** och konfigurera klientorganisations-ID och program-ID:
    1. Kopiera Azure AD-klient-ID:t från skriptets utdata och ange det i fältet **AAD-klientorganisations-ID.**
    1. Kopiera Azure AD-program-ID:t från skriptets utdata och ange det i fältet **AAD-program-ID.** Du kan också använda AAD-program-ID:ts URI. Mer information om program-ID-URI finns i [den här artikeln.](../app-service/configure-authentication-provider-aad.md)

        ![Säker webhook-åtgärd](./media/secure-webhook-delivery/aad-configuration.png)



## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelseleveranser finns i [Övervaka Event Grid leverans av meddelanden.](monitor-event-delivery.md)
* Mer information om autentiseringsnyckeln finns i Event Grid [säkerhet och autentisering.](security-authentication.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns [i Event Grid prenumerationsschema](subscription-creation-schema.md).
