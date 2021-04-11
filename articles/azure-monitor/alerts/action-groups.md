---
title: Skapa och hantera åtgärdsgrupper i Azure-portalen
description: Lär dig hur du skapar och hanterar åtgärds grupper i Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: dukek
ms.openlocfilehash: 7010e20b65142cf0ab85c29d6b22c925c977f1f8
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104991"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Skapa och hantera åtgärdsgrupper i Azure-portalen
En åtgärds grupp är en samling aviserings inställningar som definieras av ägaren av en Azure-prenumeration. Azure Monitor-och Service Health-aviseringar använder åtgärds grupper för att meddela användare om att en avisering har utlösts. Olika aviseringar kan använda samma åtgärds grupp eller olika åtgärds grupper beroende på användarens krav. 

Den här artikeln visar hur du skapar och hanterar åtgärds grupper i Azure Portal.

Varje åtgärd består av följande egenskaper:

* **Typ**: meddelandet eller åtgärden som utförs. Exempel på detta är att skicka ett röst samtal, SMS, e-post; eller utlöser olika typer av automatiserade åtgärder. Se typer längre fram i den här artikeln.
* **Namn**: en unik identifierare i åtgärds gruppen.
* **Information**: motsvarande information som varierar efter *typ*.

Information om hur du använder Azure Resource Manager mallar för att konfigurera åtgärds grupper finns i [Åtgärds grupp Resource Manager-mallar](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Skapa en åtgärds grupp med hjälp av Azure Portal

1. Sök efter och välj **övervaka** i [Azure Portal](https://portal.azure.com). I **övervaknings** fönstret samlas alla övervaknings inställningar och data i en vy.

1. Välj **aviseringar** och välj sedan **Hantera åtgärder**.

    ![Knappen hantera åtgärder](./media/action-groups/manage-action-groups.png)
    
1. Välj **Lägg till åtgärds grupp** och fyll i de relevanta fälten i guiden.

    ![Kommandot Lägg till åtgärds grupp](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Konfigurera grundläggande inställningar för åtgärds grupp

Under **projekt information**:

Välj den **prenumeration** och **resurs grupp** där du vill spara åtgärds gruppen.

Under **instans information**:

1. Ange ett **namn på åtgärds gruppen**.

1. Ange ett **visnings namn**. Visnings namnet används i stället för ett fullständigt åtgärds grupp namn när meddelanden skickas med hjälp av den här gruppen.

      ![Dialog rutan Lägg till åtgärds grupp](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Konfigurera meddelanden

1. Klicka på **Nästa: meddelanden >** knappen för att gå till fliken **meddelanden** eller Välj fliken **meddelanden** överst på skärmen.

1. Definiera en lista med meddelanden som ska skickas när en avisering utlöses. Ange följande för varje meddelande:

    a. **Meddelande typ**: Välj den typ av meddelande som du vill skicka. De tillgängliga alternativen är:
      * E-Azure Resource Manager roll – skicka ett e-postmeddelande till användare som tilldelats vissa ARM-roller på prenumerations nivå.
      * E-post/SMS/push/röst – skicka dessa meddelande typer till vissa mottagare.
    
    b. **Namn**: Ange ett unikt namn för meddelandet.

    c. **Information**: baserat på vald meddelande typ anger du en e-postadress, telefonnummer osv.
    
    d. **Vanligt aviserings schema**: du kan välja att aktivera det [vanliga aviserings schemat](./alerts-common-schema.md), vilket ger fördelen att ha en enda utöknings bar och enhetlig aviserings nytto last för alla aviserings tjänster i Azure Monitor.

    ![Fliken meddelanden](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Konfigurera åtgärder

1. Klicka på **Nästa: åtgärder >** knappen för att gå till fliken **åtgärder** , eller Välj fliken **åtgärder** överst på skärmen.

1. Definiera en lista med åtgärder som ska utlösas när en avisering utlöses. Ange följande för varje åtgärd:

    a. **Åtgärds typ**: Välj Automation Runbook, Azure Function, ITSM, Logic app, säker webhook, webhook.
    
    b. **Namn**: Ange ett unikt namn för åtgärden.

    c. **Information**: baserat på åtgärds typ anger du en webhook-URI, Azure app, ITSM-anslutning eller Automation-Runbook. För ITSM-åtgärd anger du även **arbets objekt** och andra fält som ditt ITSM-verktyg kräver.
    
    d. **Vanligt aviserings schema**: du kan välja att aktivera det [vanliga aviserings schemat](./alerts-common-schema.md), vilket ger fördelen att ha en enda utöknings bar och enhetlig aviserings nytto last för alla aviserings tjänster i Azure Monitor.
    
    ![Fliken åtgärder](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Skapa åtgärds gruppen

1. Du kan utforska inställningarna **Taggar** om du vill. På så sätt kan du koppla nyckel/värde-par till åtgärds gruppen för din kategorisering och är en funktion som är tillgänglig för alla Azure-resurser.

    ![Fliken Taggar](./media/action-groups/action-group-4-tags.png)
    
1. Klicka på **Granska + skapa** för att granska dina inställningar. Detta gör att du snabbt kan verifiera dina indata och se till att alla obligatoriska fält är markerade. Om det finns problem, kommer de att rapporteras här. När du har granskat inställningarna klickar du på **skapa** för att etablera åtgärds gruppen.
    
    ![Fliken Granska och skapa](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> När du konfigurerar en åtgärd för att meddela en person via e-post eller SMS får användaren en bekräftelse som anger att de har lagts till i åtgärds gruppen.

## <a name="manage-your-action-groups"></a>Hantera dina åtgärds grupper

När du har skapat en åtgärds grupp kan du Visa **Åtgärds grupper** genom att välja **Hantera åtgärder** på sidan **aviserings** landning i **övervaknings** fönstret. Välj den åtgärds grupp som du vill hantera för att:

* Lägg till, redigera eller ta bort åtgärder.
* Ta bort åtgärds gruppen.

## <a name="action-specific-information"></a>Åtgärds information

> [!NOTE]
> Se [begränsningar för prenumerations tjänsten för övervakning](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) av numeriska gränser för varje objekt nedan.  

### <a name="automation-runbook"></a>Automation Runbook
Se begränsningar för [Azure-prenumerations tjänsten](../../azure-resource-manager/management/azure-subscription-service-limits.md) för begränsningar i Runbook-nyttolaster.

Du kan ha ett begränsat antal Runbook-åtgärder i en åtgärds grupp. 

### <a name="azure-app-push-notifications"></a>Push-meddelanden i Azure App
Aktivera push-meddelanden till [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/) genom att ange den e-postadress som du använder som ditt konto-ID när du konfigurerar Azure-mobilapp.

Du kan ha ett begränsat antal Azure App-åtgärder i en åtgärds grupp.

### <a name="email"></a>E-post
E-postmeddelanden kommer att skickas från följande e-postadresser. Kontrol lera att e-postfiltreringen är korrekt konfigurerad
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Du kan ha ett begränsat antal e-poståtgärder i en åtgärds grupp. Se artikeln [rate relimiting information](./alerts-rate-limiting.md) .

### <a name="email-azure-resource-manager-role"></a>E-posta Azure Resource Manager-rollen
Skicka e-post till medlemmarna i prenumerationens roll. E-post skickas endast till **användar medlemmar i Azure AD** i rollen. E-post kommer inte att skickas till Azure AD-grupper eller tjänstens huvudnamn.

Ett e-postmeddelande skickas endast till den *primära e-* postadressen.

Om du inte får meddelanden på ditt *primära e-postmeddelande* kan du prova följande steg:

1. I Azure Portal går du till *Active Directory*.
2. Klicka på alla användare (i det vänstra fönstret) så visas en lista över användare (i den högra rutan).
3. Välj den användare som du vill granska den *primära e-* postinformationen för.

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Exempel på hur du granskar användar profilen." border="true":::

4. I användar profil under kontakt information om fliken "e-post" är tom klickar du på knappen *Redigera* högst upp och lägger till din *primära e-postadress* och knappen tryck på *Spara* längst upp.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Exempel på hur du lägger till primär e-post." border="true":::

Du kan ha ett begränsat antal e-poståtgärder i en åtgärds grupp. Se artikeln [rate relimiting information](./alerts-rate-limiting.md) .

### <a name="function"></a>Funktion
Anropar en befintlig HTTP trigger-slutpunkt i [Azure Functions](../../azure-functions/functions-get-started.md). För att hantera en begäran måste slut punkten hantera HTTP POST-verbet.

Du kan ha ett begränsat antal funktions åtgärder i en åtgärds grupp.

### <a name="itsm"></a>ITSM
ITSM-åtgärden kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM-anslutning](./itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärds grupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal Logic app-åtgärder i en åtgärds grupp.

### <a name="secure-webhook"></a>Säker webhook
Med åtgärden åtgärds grupper säker webhook kan du dra nytta av Azure Active Directory för att skydda anslutningen mellan din åtgärds grupp och din skyddade webb-API (webhook-slutpunkt). Det övergripande arbets flödet för att dra nytta av den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänst huvud namn finns i [Översikt över Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

> [!NOTE]
> Att använda webhook-åtgärden kräver att mål-webhook-slutpunkten antingen inte behöver information om aviseringen för att fungera korrekt eller kan parsa den aviserings kontext information som tillhandahålls som en del av POST-åtgärden. Om webhook-slutpunkten inte kan hantera aviserings kontext informationen på egen hand kan du använda en lösning som en [logisk app-åtgärd](./action-groups-logic-app.md) för en anpassad manipulering av aviserings kontext informationen som matchar webhookens förväntade data format.

1. Skapa ett Azure AD-program för ditt skyddade webb-API. Se [Protected Web API: app Registration](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Konfigurera ditt skyddade API så att det [anropas av en daemon-app](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Aktivera åtgärds grupper för att använda Azure AD-programmet.

    > [!NOTE]
    > Du måste vara medlem i [rollen Azure AD-programadministratör](../../active-directory/roles/permissions-reference.md#all-roles) för att köra det här skriptet.
    
    - Ändra PowerShell-skriptets Connect-AzureAD anrop för att använda ditt Azure AD-klient-ID.
    - Ändra PowerShell-skriptets variabel $myAzureADApplicationObjectId att använda objekt-ID: t för ditt Azure AD-program.
    - Kör det ändrade skriptet.
    
3. Konfigurera åtgärds gruppens säkra webhook-åtgärd.
    - Kopiera värdet $myApp. ObjectId från skriptet och ange det i fältet program objekt-ID i definition av webhook-åtgärd.
    
    ![Säker webhook-åtgärd](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Secure webhook PowerShell-skript

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
Mer viktig information finns i [frekvens begränsa information](./alerts-rate-limiting.md) och [SMS-aviseringar](./alerts-sms-behavior.md) . 

Du kan ha ett begränsat antal SMS-åtgärder i en åtgärds grupp.

> [!NOTE]
> Om användar gränssnittet Azure Portal åtgärds grupp inte tillåter att du väljer din landskod, stöds SMS inte för ditt land/din region.  Om lands-/region koden inte är tillgänglig kan du rösta för att få ditt land/region tillagt i [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Under tiden är ett arbete runt att låta din åtgärds grupp anropa en webhook till en tredjeparts-SMS-provider med stöd i ditt land/din region.  

Priser för länder/regioner som stöds finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

**Lista över länder där SMS-avisering stöds**

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
Mer viktig information finns i artikeln om [pris begränsning](./alerts-rate-limiting.md) .

Du kan ha ett begränsat antal röst åtgärder i en åtgärds grupp.

> [!NOTE]
> Om användar gränssnittet Azure Portal åtgärds grupp inte tillåter att du väljer landskod, stöds inte röst samtal för ditt land/din region. Om lands-/region koden inte är tillgänglig kan du rösta för att få ditt land/region tillagt i [User Voice](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Under tiden är ett arbete runt att be din åtgärds grupp att anropa en webhook till en röst samtals leverantör från tredje part med support i ditt land/din region.  
> Endast lands kod som stöds i dag i Azure Portal åtgärds grupp för röst meddelande är + 1 (USA). 

Priser för länder/regioner som stöds finns på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>Webhook

> [!NOTE]
> Att använda webhook-åtgärden kräver att mål-webhook-slutpunkten antingen inte behöver information om aviseringen för att fungera korrekt eller kan parsa den aviserings kontext information som tillhandahålls som en del av POST-åtgärden. Om webhook-slutpunkten inte kan hantera aviserings kontext informationen på egen hand kan du använda en lösning som en [logisk app-åtgärd](./action-groups-logic-app.md) för en anpassad manipulering av aviserings kontext informationen som matchar webhookens förväntade data format.

Webhooks bearbetas med följande regler
- Ett webhook-anrop görs maximalt 3 gånger.
- Ett nytt anrop görs om ett svar inte tas emot inom tids perioden eller om en av följande HTTP-statuskod returneras: 408, 429, 503 eller 504.
- Det första anropet kommer att vänta 10 sekunder för ett svar.
- De andra och tredje försöken kommer att vänta i 30 sekunder för ett svar.
- När tre försök att anropa webhooken Miss lyckas, kommer ingen åtgärds grupp att anropa slut punkten i 15 minuter.

Se [Åtgärds gruppens IP-adresser](../app/ip-addresses.md) för käll-IP-adressintervall.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [SMS-aviserings beteende](./alerts-sms-behavior.md).  
* Få en [förståelse för aktivitets logg aviseringens webhook-schema](./activity-log-alerts-webhook.md).  
* Läs mer om [ITSM-anslutningsprogram](./itsmc-overview.md).
* Läs mer om [hastighets begränsning](./alerts-rate-limiting.md) av aviseringar.
* Få en [Översikt över aktivitets logg aviseringar](./alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett meddelande om tjänst hälsa har publicerats](../../service-health/alerts-activity-log-service-notifications-portal.md).
