---
title: Skapa och hantera åtgärdsgrupper i Azure-portalen
description: Lär dig hur du skapar och hanterar åtgärdsgrupper i Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 1486415c5d225163dd2b2c7e79cd008ad0a76588
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514877"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure-portalen
En åtgärdsgrupp är en samling aviseringsinställningar som definieras av ägaren av en Azure-prenumeration. Azure Monitor och Service Health-aviseringar använder åtgärdsgrupper för att meddela användare att en avisering har utlösts. Olika aviseringar kan använda samma åtgärdsgrupp eller olika åtgärdsgrupper beroende på användarens krav. 

Den här artikeln visar hur du skapar och hanterar åtgärdsgrupper i Azure Portal.

Varje åtgärd består av följande egenskaper:

* **Typ:** Meddelandet eller åtgärden som utfördes. Exempel är att skicka ett röstsamtal, SMS, e-post; eller utlöser olika typer av automatiserade åtgärder. Se typer senare i den här artikeln.
* **Namn:** En unik identifierare i åtgärdsgruppen.
* **Information:** Motsvarande information som varierar efter *typ*.

Information om hur du använder Azure Resource Manager för att konfigurera åtgärdsgrupper finns i [Resource Manager mallar](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en åtgärdsgrupp med hjälp av Azure Portal

1. I [Azure Portal](https://portal.azure.com)du efter och väljer **Övervaka**. Fönstret **Övervaka** konsoliderar alla dina övervakningsinställningar och data i en enda vy.

1. Välj **Aviseringar** och sedan **Hantera åtgärder.**

    ![Knappen Hantera åtgärder](./media/action-groups/manage-action-groups.png)
    
1. Välj **Lägg till åtgärdsgrupp** och fyll i relevanta fält i guiden.

    ![Kommandot "Lägg till åtgärdsgrupp"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurera grundläggande inställningar för åtgärdsgrupp

Under **Projektinformation:**

Välj den **prenumeration** **och resursgrupp** där åtgärdsgruppen sparas.

Under **Instansinformation:**

1. Ange ett **åtgärdsgruppnamn.**

1. Ange ett **Visningsnamn.** Visningsnamnet används i stället för ett fullständigt åtgärdsgruppnamn när meddelanden skickas med den här gruppen.

      ![Dialogrutan Lägg till åtgärdsgrupp](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurera meddelanden

1. Klicka på **knappen Nästa: >** för att  gå till fliken  Meddelanden eller välj fliken Meddelanden överst på skärmen.

1. Definiera en lista över meddelanden som ska skickas när en avisering utlöses. Ange följande för varje meddelande:

    a. **Meddelandetyp:** Välj den typ av avisering som du vill skicka. De tillgängliga alternativen är:
      * E Azure Resource Manager roll – Skicka ett e-postmeddelande till användare som har tilldelats vissa ARM-roller på prenumerationsnivå.
      * E-post/SMS/Push/röst – Skicka dessa meddelandetyper till specifika mottagare.
    
    b. **Namn:** Ange ett unikt namn för meddelandet.

    c. **Information:** Baserat på den valda meddelandetypen anger du en e-postadress, ett telefonnummer osv.
    
    d. **Vanligt aviseringsschema:** Du kan välja att aktivera det gemensamma aviseringsschemat [,](./alerts-common-schema.md)vilket ger fördelen med en enda utökningsbar och enhetlig aviseringsnyttolast för alla aviseringstjänster i Azure Monitor.

    ![Fliken Meddelanden](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurera åtgärder

1. Klicka på **knappen >:** Åtgärder för att gå **till**  fliken Åtgärder eller välj fliken Åtgärder högst upp på skärmen.

1. Definiera en lista över åtgärder som ska utlösas när en avisering utlöses. Ange följande för varje åtgärd:

    a. **Åtgärdstyp:** Välj Automation Runbook, Azure Function, ITSM, Logic App, Secure Webhook, Webhook.
    
    b. **Namn:** Ange ett unikt namn för åtgärden.

    c. **Information:** Baserat på åtgärdstyp anger du en webhook-URI, Azure-app, ITSM-anslutning eller Automation-runbook. För ITSM-åtgärd anger du dessutom **Arbetsobjekt och** andra fält som krävs av ITSM-verktyget.
    
    d. **Vanligt aviseringsschema:** Du kan välja att aktivera det gemensamma aviseringsschemat [,](./alerts-common-schema.md)vilket ger fördelen att du har en enda utökningsbar och enhetlig aviseringsnyttolast för alla aviseringstjänster i Azure Monitor.
    
    ![Fliken Åtgärder](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Skapa åtgärdsgruppen

1. Du kan utforska inställningarna **Taggar** om du vill. På så sätt kan du associera nyckel/värde-par till åtgärdsgruppen för din kategorisering och är en funktion som är tillgänglig för alla Azure-resurser.

    ![Fliken Taggar](./media/action-groups/action-group-4-tags.png)
    
1. Klicka på **Granska + skapa** för att granska dina inställningar. Detta gör en snabb validering av dina indata för att se till att alla obligatoriska fält är markerade. Om det finns problem, kommer de att rapporteras här. När du har granskat inställningarna klickar du på Skapa **för** att etablera åtgärdsgruppen.
    
    ![Fliken Granska + skapa](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> När du konfigurerar en åtgärd för att meddela en person via e-post eller SMS får personen en bekräftelse som anger att personen har lagts till i åtgärdsgruppen.

## <a name="manage-your-action-groups"></a>Hantera dina åtgärdsgrupper

När du har skapat en åtgärdsgrupp kan du visa **Åtgärdsgrupper genom** att **välja Hantera åtgärder** från **landningssidan Aviseringar** i **fönstret** Övervaka. Välj den åtgärdsgrupp som du vill hantera för att:

* Lägga till, redigera eller ta bort åtgärder.
* Ta bort åtgärdsgruppen.

## <a name="action-specific-information"></a>Åtgärdsspecifik information

> [!NOTE]
> Se [Prenumerationstjänstbegränsningar för](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) övervakning för numeriska gränser för vart och ett av objekten nedan.  

### <a name="automation-runbook"></a>Automation Runbook
Se [tjänstbegränsningar för Azure-prenumerationer](../../azure-resource-manager/management/azure-subscription-service-limits.md) för gränser för Runbook-nyttolaster.

Du kan ha ett begränsat antal Runbook-åtgärder i en åtgärdsgrupp. 

### <a name="azure-app-push-notifications"></a>Push-meddelanden för Azure-appar
Aktivera push-meddelanden till [Azure-mobilapp genom att](https://azure.microsoft.com/features/azure-portal/mobile-app/) ange den e-postadress som du använder som konto-ID när du konfigurerar Azure-mobilapp.

Du kan ha ett begränsat antal Azure-appåtgärder i en åtgärdsgrupp.

### <a name="email"></a>E-post
E-postmeddelanden skickas från följande e-postadresser. Kontrollera att din e-postfiltrering är korrekt konfigurerad
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e-poståtgärder i en åtgärdsgrupp. Se artikeln [om frekvensbegränsningsinformation.](./alerts-rate-limiting.md)

### <a name="email-azure-resource-manager-role"></a>E-posta Azure Resource Manager-rollen
Skicka e-post till medlemmar i prenumerationens roll. E-post skickas endast till **Azure AD-användarmedlemmar** i rollen. E-post kommer inte att skickas till Azure AD-grupper eller tjänstens huvudnamn.

Ett e-postmeddelande skickas endast till den primära *e-postadressen.*

Om du inte får meddelanden på din primära *e-post* kan du prova följande steg:

1. I Azure Portal du till *Active Directory*.
2. Klicka på Alla användare (i det vänstra fönstret) så visas en lista över användare (i den högra rutan).
3. Välj den användare som du vill granska den primära *e-postinformationen* för.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Exempel på hur du granskar användarprofilen." border="true":::

4. I Användarprofil under Kontaktinformation om fliken "E-post" är tom  klickar du  på redigeringsknappen längst upp och lägger till din primära e-postadress och trycker på knappen Spara längst upp. 

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Exempel på hur du lägger till primär e-post." border="true":::

Du kan ha ett begränsat antal e-poståtgärder i en åtgärdsgrupp. Se artikeln [om frekvensbegränsningsinformation.](./alerts-rate-limiting.md)

När du ställer *in ARM-e-postroll* måste du se till att följande tre villkor är uppfyllda:

1. Typen av entitet som tilldelas till rollen måste vara **"Användare".**
2. Tilldelningen måste göras på **prenumerationsnivå.**
3. Användaren måste ha ett e-postmeddelande konfigurerat i sin **AAD-profil**. 


### <a name="function"></a>Funktion
Anropar en befintlig HTTP-utlösarslutpunkt [i Azure Functions](../../azure-functions/functions-get-started.md). För att hantera en begäran måste slutpunkten hantera HTTP POST-verbet.

Du kan ha ett begränsat antal funktionsåtgärder i en åtgärdsgrupp.

### <a name="itsm"></a>ITSM
ITSM-åtgärden kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM-anslutning](./itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärdsgrupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal logikappåtgärder i en åtgärdsgrupp.

### <a name="secure-webhook"></a>Säker webhook
Med åtgärden Säker webhook för åtgärdsgrupper kan du dra nytta av Azure Active Directory för att skydda anslutningen mellan din åtgärdsgrupp och ditt skyddade webb-API (webhookslutpunkt). Det övergripande arbetsflödet för att dra nytta av den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänstens huvudnamn finns i [Översikt över Microsoft Identity Platform (v2.0).](../../active-directory/develop/v2-overview.md)

> [!NOTE]
> Om du använder webhook-åtgärden måste slutpunkten för målwebhooken antingen inte kräva information om aviseringen eller så kan den parsa den aviseringskontextinformation som tillhandahålls som en del av POST-åtgärden. Om webhook-slutpunkten inte kan hantera aviseringskontextinformationen på egen [](./action-groups-logic-app.md) hand kan du använda en lösning som en logikappåtgärd för anpassad manipulering av aviseringskontextinformationen så att den matchar webhookens förväntade dataformat.

1. Skapa ett Azure AD-program för ditt skyddade webb-API. Se [Skyddad webb-API: Appregistrering.](../../active-directory/develop/scenario-protected-web-api-app-registration.md)
    - Konfigurera ditt skyddade API så att det [anropas av en daemon-app](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Aktivera åtgärdsgrupper för att använda ditt Azure AD-program.

    > [!NOTE]
    > Du måste vara medlem i rollen [Azure AD-programadministratör för att](../../active-directory/roles/permissions-reference.md#all-roles) kunna köra det här skriptet.
    
    - Ändra PowerShell-skriptets anrop Connect-AzureAD att använda ditt Azure AD-klientorganisations-ID.
    - Ändra PowerShell-skriptets variabel för $myAzureADApplicationObjectId att använda objekt-ID:t för ditt Azure AD-program.
    - Kör det ändrade skriptet.
    
3. Konfigurera åtgärden Säker webhook för åtgärdsgrupp.
    - Kopiera värdet $myApp.ObjectId från skriptet och ange det i fältet Programobjekt-ID i webhook-åtgärdsdefinitionen.
    
    ![Säker webhook-åtgärd](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>PowerShell-skript för säker webhook

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object ID of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application (ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Se [frekvensbegränsningsinformation och](./alerts-rate-limiting.md) [SMS-aviseringsbeteende](./alerts-sms-behavior.md) för ytterligare viktig information. 

Du kan ha ett begränsat antal SMS-åtgärder i en åtgärdsgrupp.

> [!NOTE]
> Om Azure Portal för åtgärdsgruppen inte tillåter att du väljer lands-/regionskod stöds inte SMS för ditt land/din region.  Om ditt land/din regionkod inte är tillgänglig kan du rösta på att ditt land/din region ska läggas till på [användarens röst.](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice) Under tiden är en åtgärdsgrupp att anropa en webhook till en SMS-provider från tredje part med support i ditt land/din region.  

Priser för länder/regioner som stöds finns på [Azure Monitor sidan med priser.](https://azure.microsoft.com/pricing/details/monitor/)

**Lista över länder där SMS-meddelanden stöds**

| Landskod | Landsnamn |
|:---|:---|
| 61 | Australien |
| 43 | Österrike |
| 32 | Belgien |
| 55 | Brasilien |
| 1 |Kanada |
| 56 | Chile |
| 86 | Kina |
| 420 | Tjeckien |
| 45 | Danmark |
| 372 | Estland |
| 358 | Finland |
| 33 | Frankrike |
| 49 | Tyskland |
| 852 | Hongkong |
| 91 | Indien |
| 353 | Irland |
| 972 | Israel |
| 39 | Italien |
| 81 | Japan |
| 352 | Luxemburg |
| 60 | Malaysia |
| 52 | Mexico |
| 31 | Nederländerna |
| 64 | Nya Zeeland |
| 47 | Norge |
| 351 | Portugal |
| 1 | Puerto Rico |
| 40 | Rumänien |
| 65 | Singapore |
| 27 | Sydafrika |
| 82 | Sydkorea |
| 34 | Spanien |
| 41 | Schweiz |
| 886 | Taiwan |
| 44 | Storbritannien |
| 1 | USA |

### <a name="voice"></a>Röst
Ytterligare viktigt [beteende finns i informationsartikeln](./alerts-rate-limiting.md) om hastighetsbegränsning.

Du kan ha ett begränsat antal röståtgärder i en åtgärdsgrupp.

> [!NOTE]
> Om Azure Portal för åtgärdsgruppen inte låter dig välja ditt land/din regionkod stöds inte röstsamtal för ditt land/din region. Om ditt land/din regionkod inte är tillgänglig kan du rösta på att ditt land/din region läggs till på [user voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Under tiden är en åtgärdsgrupp att anropa en webhook till en röstsamtalsleverantör från tredje part med stöd i ditt land/din region.  
> Endast landskod som stöds i Azure Portal åtgärdsgrupp för röstmeddelanden är +1 (USA). 

Priser för länder/regioner som stöds finns på [Azure Monitor sidan med priser.](https://azure.microsoft.com/pricing/details/monitor/)

### <a name="webhook"></a>Webhook

> [!NOTE]
> Om du använder webhook-åtgärden måste slutpunkten för målwebhooken antingen inte kräva information om aviseringen eller så kan den parsa den aviseringskontextinformation som tillhandahålls som en del av POST-åtgärden. Om webhook-slutpunkten inte kan hantera aviseringskontextinformationen på egen [](./action-groups-logic-app.md) hand kan du använda en lösning som en logikappåtgärd för anpassad manipulering av aviseringskontextinformationen så att den matchar webhookens förväntade dataformat.

Webhooks bearbetas med hjälp av följande regler
- Ett webhook-anrop görs högst 3 gånger.
- Anropet kommer att göras om ett svar inte tas emot inom tidsgränsen eller om någon av följande HTTP-statuskoder returneras: 408, 429, 503 eller 504.
- Det första anropet väntar i 10 sekunder på ett svar.
- Det andra och tredje försöket väntar 30 sekunder på ett svar.
- När de tre försöken att anropa webhooken har misslyckats anropar ingen åtgärdsgrupp slutpunkten i 15 minuter.

Se [IP-adresser för åtgärdsgrupp för](../app/ip-addresses.md) källans IP-adressintervall.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [SMS-aviseringsbeteende.](./alerts-sms-behavior.md)  
* Få en [förståelse för webhookschemat för aktivitetsloggaviseringar.](./activity-log-alerts-webhook.md)  
* Läs mer om [att ITSM-anslutningsprogram](./itsmc-overview.md).
* Läs mer om [frekvensbegränsning för](./alerts-rate-limiting.md) aviseringar.
* Få en [översikt över aktivitetsloggaviseringar](./alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett meddelande om tjänstens hälsotillstånd publiceras.](../../service-health/alerts-activity-log-service-notifications-portal.md)
