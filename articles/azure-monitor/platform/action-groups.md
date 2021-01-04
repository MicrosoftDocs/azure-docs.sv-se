---
title: Skapa och hantera åtgärdsgrupper i Azure-portalen
description: Lär dig hur du skapar och hanterar åtgärds grupper i Azure Portal.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: b5b6a697e6a5cae064a6a48419246dc12e8d048c
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695813"
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

  :::image type="content" source="media/action-groups/active-directory-user-profile.png" alt-text="Exempel på hur du granskar användar profilen."border="true":::

4. I användar profil under kontakt information om fliken "e-post" är tom klickar du på knappen *Redigera* högst upp och lägger till din *primära e-postadress* och knappen tryck på *Spara* längst upp.

  :::image type="content" source="media/action-groups/active-directory-add-primary-email.png" alt-text="Exempel på hur du lägger till primär e-post."border="true":::

Du kan ha ett begränsat antal e-poståtgärder i en åtgärds grupp. Se artikeln [rate relimiting information](./alerts-rate-limiting.md) .

### <a name="function"></a>Funktion
Anropar en befintlig HTTP trigger-slutpunkt i [Azure Functions](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Du kan ha ett begränsat antal funktions åtgärder i en åtgärds grupp.

### <a name="itsm"></a>ITSM
ITSM-åtgärden kräver en ITSM-anslutning. Lär dig hur du skapar en [ITSM-anslutning](./itsmc-overview.md).

Du kan ha ett begränsat antal ITSM-åtgärder i en åtgärds grupp. 

### <a name="logic-app"></a>Logikapp
Du kan ha ett begränsat antal Logic app-åtgärder i en åtgärds grupp.

### <a name="secure-webhook"></a>Säker webhook

> [!NOTE]
> Att använda webhook-åtgärden kräver att mål-webhook-slutpunkten antingen inte behöver information om aviseringen för att fungera korrekt eller kan parsa den aviserings kontext information som tillhandahålls som en del av POST-åtgärden. Om webhook-slutpunkten inte kan hantera aviserings kontext informationen på egen hand kan du använda en lösning som en [logisk app-åtgärd](./action-groups-logic-app.md) för en anpassad manipulering av aviserings kontext informationen som matchar webhookens förväntade data format.

Med åtgärden åtgärds grupper webhook kan du dra nytta av Azure Active Directory för att skydda anslutningen mellan din åtgärds grupp och din skyddade webb-API (webhook-slutpunkt). Det övergripande arbets flödet för att dra nytta av den här funktionen beskrivs nedan. En översikt över Azure AD-program och tjänst huvud namn finns i [Översikt över Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

1. Skapa ett Azure AD-program för ditt skyddade webb-API. Se [Protected Web API: app Registration](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Konfigurera ditt skyddade API så att det [anropas av en daemon-app](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Aktivera åtgärds grupper för att använda Azure AD-programmet.

    > [!NOTE]
    > Du måste vara medlem i [rollen Azure AD-programadministratör](../../active-directory/roles/permissions-reference.md#available-roles) för att köra det här skriptet.
    
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
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
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
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
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

Käll-IP-adressintervall:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020: A04:402:: 178/125
 - 2603:1020: B04:402:: 178/125
 - 2603:1020: C04:402:: 178/125
 - 2603:1020: D04:402:: 178/125
 - 2603:1020: E04:402:: 178/125
 - 2603:1020: F04:402:: 178/125
 - 2603:1020:1004:800:: F8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030: f:400:: 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400:: f0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40b: 400:: 978/125
 - 2603:1030:40c: 402:: 178/125
 - 2603:1030:504:802:: F8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030: A07:402:: 8f8/125
 - 2603:1030: B04:402:: 178/125
 - 2603:1030: C06:400:: 978/125
 - 2603:1030: F05:402:: 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040: A06:402:: 178/125
 - 2603:1040: B04:402:: 178/125
 - 2603:1040: C06:402:: 178/125
 - 2603:1040: D04:800:: F8/125
 - 2603:1040: F05:402:: 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400:: 1f8/125

Om du vill ta emot uppdateringar om ändringar av dessa IP-adresser rekommenderar vi att du konfigurerar en Service Health avisering, som övervakar informations meddelanden om tjänsten åtgärds grupper.

Du kan ha ett begränsat antal webhook-åtgärder i en åtgärds grupp.

Frekventa uppdateringar av käll-IP-adresser kan ta lång tid i webhook. Genom att använda **service tag** för *ActionGroup* kan du minimera komplexiteten vid frekventa uppdateringar av IP-adresser manuellt. IP-adresser för käll-IP-adress intervall som delas ovan hanteras automatiskt av Microsoft som omfattas av **service tag**.

#### <a name="service-tag"></a>Service tag
En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler för en ActionGroup.

1. I Azure Portal under Azure-tjänster Sök efter *nätverks säkerhets grupp*.
2. Klicka på **Lägg till** och skapa en nätverks säkerhets grupp.

   1. Lägg till resurs gruppens namn och ange sedan *instans information*.
   1. Klicka på **Granska + skapa** och klicka sedan på *skapa*.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Exempel på hur du skapar en nätverks säkerhets grupp."border="true":::

3. Gå till resurs grupp och klicka sedan på *nätverks säkerhets grupp* som du har skapat.

    1. Välj *inkommande säkerhets regler*.
    1. Klicka på **Lägg till**.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Exempel på hur du lägger till en service tag-kod."border="true":::

4. Ett nytt fönster öppnas i det högra fönstret.
    1.  Välj källa: **service tag**
    1.  Käll tjänst tag gen: **ActionGroup**
    1.  Klicka på **Lägg till**.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Exempel på hur du lägger till service tag-koden."border="true":::

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [SMS-aviserings beteende](./alerts-sms-behavior.md).  
* Få en [förståelse för aktivitets logg aviseringens webhook-schema](./activity-log-alerts-webhook.md).  
* Läs mer om [ITSM-anslutningsprogram](./itsmc-overview.md).
* Läs mer om [hastighets begränsning](./alerts-rate-limiting.md) av aviseringar.
* Få en [Översikt över aktivitets logg aviseringar](./alerts-overview.md)och lär dig hur du tar emot aviseringar.  
* Lär dig hur du [konfigurerar aviseringar när ett meddelande om tjänst hälsa har publicerats](../../service-health/alerts-activity-log-service-notifications-portal.md).
