---
title: Hanterings lösning för Office 365 i Azure
description: Den här artikeln innehåller information om konfiguration och användning av Office 365-lösningen i Azure.  Den innehåller en detaljerad beskrivning av de Office 365-poster som skapats i Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: bde1c503d0aaaff1afcee67a26245d5021c43bb4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807758"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Office 365-hanterings lösning i Azure (för hands version)

![Office 365-logotyp](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Lösnings uppdatering
> Den här lösningen har ersatts av lösningen för allmänt tillgänglighet för [Office 365](../../sentinel/connect-office-365.md) i [Azure SENTINEL](../../sentinel/overview.md) och [Azure AD repor ting and Monitoring-lösningen](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Tillsammans tillhandahåller de en uppdaterad version av den tidigare Azure Monitor Office 365-lösningen med en förbättrad konfigurations upplevelse. Du kan fortsätta att använda den befintliga lösningen fram till den 31 oktober 2020.
> 
> Azure Sentinel är en säkerhets informations-och händelse hanterings lösning i molnet som matar in loggar och ger ytterligare SIEM-funktioner, inklusive identifieringar, undersökningar, jakt och Machine Learning-drivna insikter. Med Azure Sentinel får du nu en inmatning av Office 365 SharePoint-aktivitet och Exchange Management-loggar.
> 
> Azure AD repor ting ger en mer omfattande vy av loggar från Azure AD-aktivitet i din miljö, inklusive inloggnings händelser, gransknings händelser och ändringar i din katalog. Om du vill ansluta Azure AD-loggar kan du antingen använda [Azure Sentinel Azure AD Connector](../../sentinel/connect-azure-active-directory.md) eller konfigurera [Azure AD-loggar integration med Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> Insamlingen av Azure AD-loggen omfattas Azure Monitor prissättning.  Mer information finns i [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/) .
>
> Använda Azure Sentinel Office 365-lösningen:
> 1. Om du använder Office 365-anslutaren i Azure Sentinel påverkas prissättningen för din arbets yta. Mer information finns i [priser för Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Om du redan använder Azure Monitor Office 365-lösningen måste du först avinstallera den med hjälp av skriptet i [avsnittet Avinstallera nedan](#uninstall).
> 3. [Aktivera Azure Sentinel-lösningen](../../sentinel/quickstart-onboard.md) på din arbets yta.
> 4. Gå till sidan **data anslutningar** i Azure Sentinel och aktivera **Office 365** -anslutaren.
>
> ## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>F: är det möjligt för Office 365 Azure Monitor-lösningen mellan nu och 31 oktober?
> Nej, Azure Monitor Office 365-lösningen onboarding-skript är inte längre tillgängliga. Lösningen tas bort den 31 oktober.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>F: kommer tabeller och scheman att ändras?
> **OfficeActivity** -tabellens namn och schemat är fortfarande samma som i den aktuella lösningen. Du kan fortsätta att använda samma frågor i den nya lösningen, med undantag för frågor som refererar till Azure AD-data.
> 
> De nya [lösnings loggarna för Azure AD repor ting och övervakning](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) kommer att matas in i tabellerna [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) och [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) i stället för **OfficeActivity**. Mer information finns i [så här analyserar du Azure AD-loggar](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), som också är relevant för Azure Sentinel och Azure Monitor användare.
> 
> Följande är exempel på konvertering av frågor från **OfficeActivity** till **SigninLogs**:
> 
> **Fråga misslyckade inloggningar, av användare:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Visa Azure AD-åtgärder:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>F: Hur kan jag aktivera Azure-kontroll i ett kort?
> Azure Sentinel är en lösning som du kan aktivera på nya eller befintliga Log Analytics-arbetsytan. Mer information finns i [dokumentation om Azure Sentinel-dokumentation](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>F: behöver jag Azure Sentinel för att ansluta till Azure AD-loggarna?
> Du kan konfigurera [integreringen av Azure AD-loggar med Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), som inte är relaterad till Azure Sentinel-lösningen. Azure Sentinel tillhandahåller en inbyggd koppling och direkt Box-innehåll för Azure AD-loggar. Mer information finns i frågan nedan om fördefinierat säkerhetsorienteradt innehåll.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>F: Vad är skillnaderna när du ansluter Azure AD-loggar från Azure Sentinel och Azure Monitor?
> Azure Sentinel och Azure Monitor ansluta till Azure AD-loggar baserat på samma [Azure AD-rapportering och övervaknings lösning](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel tillhandahåller ett enda klick, en inbyggd anslutning som ansluter samma data och tillhandahåller övervaknings information.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>F: Vad behöver jag ändra när de flyttar till de nya Azure AD-rapporterings-och övervaknings tabellerna?
> Alla frågor som använder Azure AD-data, inklusive frågor i aviseringar, instrument paneler och innehåll som du har skapat med Office 365 Azure AD-data, måste återskapas med hjälp av de nya tabellerna.
>
> Azure Sentinel och Azure AD tillhandahåller inbyggt innehåll som du kan använda när du flyttar till Azure AD repor ting and Monitoring-lösningen. Mer information finns i nästa fråga för färdiga säkerhetsorienterade innehåll och [hur du använder Azure Monitor arbets böcker för Azure Active Directory rapporter](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>F: hur jag kan använda det säkerhetsorienterade innehållet i Azure Sentinel?
> Azure Sentinel tillhandahåller färdiga säkerhetsorienterade instrument paneler, anpassade aviserings frågor, jakt frågor, undersökningar och automatiserade svars funktioner baserat på Office 365-och Azure AD-loggar. Utforska Azure Sentinel-GitHub och självstudierna för att lära dig mer:
>
> - [Identifiera hot som är färdiga](../../sentinel/tutorial-detect-threats-built-in.md)
> - [Skapa anpassade analysregler för att identifiera misstänkta hot](../../sentinel/tutorial-detect-threats-custom.md)
> - [Övervaka dina data](../../sentinel/tutorial-monitor-your-data.md)
> - [Undersöka incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
> - [Konfigurera automatiska svar på hot i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Azure Sentinel GitHub-community](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>F: tillhandahåller Azure Sentinel ytterligare anslutningar som en del av lösningen?
> Ja, se [data källor i Azure Sentinel Connect](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>F: Vad kommer att ske den 31 oktober? Måste jag avpublicera i förväg?
> 
> - Du kommer inte att kunna ta emot data från **Office365** -lösningen. Lösningen tas bort från arbets ytan och kommer inte längre att vara tillgänglig i Marketplace.
> - För Azure Sentinel-kunder kommer den Log Analytics arbets ytans lösning **Office365** ingå i Azure Sentinel **SecurityInsights** -lösningen.
> - Om du inte avpublicera din lösning manuellt den 31 oktober kommer dina data att kopplas från automatiskt och **OfficeActivity** -tabellen tas bort. Dessutom kan du fortfarande återställa tabellen när du aktiverar Office 365-anslutaren i Azure Sentinel, enligt beskrivningen nedan.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>F: överförs data till den nya lösningen?
> Ja. När du tar bort **Office 365** -lösningen från arbets ytan blir dess data tillfälligt otillgängliga eftersom schemat tas bort. När du aktiverar den nya **Office 365** -anslutningen i Azure Sentinel, återställs schemat till arbets ytan och alla data som redan har samlats in blir tillgängliga. 
 

Med hanterings lösningen för Office 365 kan du övervaka din Office 365-miljö i Azure Monitor.

- Övervaka användar aktiviteter på dina Office 365-konton för att analysera användnings mönster och identifiera beteende trender. Du kan till exempel extrahera vissa användnings scenarier, till exempel filer som delas utanför organisationen eller de mest populära SharePoint-webbplatserna.
- Övervaka administratörs aktiviteter för att spåra konfigurations ändringar eller åtgärder med hög behörighet.
- Identifiera och undersök oönskade användar beteenden, som kan anpassas efter organisationens behov.
- Demonstrera granskning och efterlevnad. Du kan till exempel övervaka fil åtkomst åtgärder på konfidentiella filer, vilket kan hjälpa dig med gransknings-och efterlevnads processen.
- Utför drift fel sökning genom att använda [logg frågor](../log-query/log-query-overview.md) ovanpå Office 365-aktivitets data i din organisation.


## <a name="uninstall"></a>Avinstallera

Du kan ta bort hanterings lösningen för Office 365 med processen i [ta bort en hanterings lösning](solutions.md#remove-a-monitoring-solution). Detta kommer inte att stoppa data som samlas in från Office 365 till Azure Monitor. Följ proceduren nedan om du vill avbryta prenumerationen på Office 365 och sluta samla in data.

1. Spara följande skript som *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Kör skriptet med följande kommando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Exempel:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Du kommer att uppmanas att ange autentiseringsuppgifter. Ange autentiseringsuppgifterna för din Log Analytics-arbetsyta.

## <a name="data-collection"></a>Datainsamling

Det kan ta några timmar innan data samlas in från början. När den börjar samla in skickar Office 365 en [webhook-avisering](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) med detaljerade data till Azure Monitor varje gång en post skapas. Den här posten är tillgänglig i Azure Monitor inom några minuter efter att de mottagits.

## <a name="using-the-solution"></a>Använda lösningen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

När du lägger till Office 365-lösningen i din Log Analytics arbets yta läggs **office 365** -panelen till på instrument panelen. Den här panelen visar antal och en grafisk representation av antalet datorer i din miljö och deras uppdateringskompatibilitet.<br><br>
![Sammanfattnings panel för Office 365](media/solution-office-365/tile.png)  

Klicka på panelen **office 365** för att öppna **Office 365** -instrumentpanelen.

![Office 365-instrumentpanel](media/solution-office-365/dashboard.png)  

Instrumentpanelen innehåller kolumnerna i följande tabell. Varje kolumn listar de tio främsta aviseringarna genom att räkna matchningen av kolumnens kriterier för det angivna omfånget och tidsintervallet. Du kan köra en loggs ökning som ger hela listan genom att klicka på Visa alla längst ned i kolumnen eller genom att klicka på kolumn rubriken.

| Kolumn | Beskrivning |
|:--|:--|
| Operations | Innehåller information om aktiva användare från alla övervakade Office 365-prenumerationer. Du kommer också att kunna se antalet aktiviteter som sker över tid.
| Exchange | Visar en uppdelning av Exchange Server-aktiviteter som Add-Mailbox behörighet eller ange post låda. |
| SharePoint | Visar de viktigaste aktiviteterna som användare utför i SharePoint-dokument. När du ökar detalj nivån från den här panelen visar Sök sidan information om dessa aktiviteter, till exempel mål dokumentet och platsen för aktiviteten. För en fil som används, kan du till exempel se det dokument som har åtkomst till, dess associerade konto namn och IP-adress. |
| Azure Active Directory | Omfattar de vanligaste användar aktiviteterna, till exempel återställning av användar lösen ord och inloggnings försök. När du ökar detalj nivån kommer du att kunna se information om dessa aktiviteter, till exempel resultat status. Detta är mest användbart om du vill övervaka misstänkta aktiviteter på din Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Azure Monitor logg poster

Alla poster som skapats i arbets ytan Log Analytics i Azure Monitor av Office 365-lösningen har en **typ** av **OfficeActivity**.  Egenskapen **OfficeWorkload** bestämmer vilken Office 365-tjänst som posten refererar till-Exchange, AzureActiveDirectory, SharePoint eller OneDrive.  Egenskapen **RecordType** anger typ av åtgärd.  Egenskaperna varierar för varje åtgärds typ och visas i tabellerna nedan.

### <a name="common-properties"></a>Gemensamma egenskaper

Följande egenskaper är gemensamma för alla Office 365-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| Typ | *OfficeActivity* |
| ClientIP | IP-adressen för den enhet som användes när aktiviteten loggades. IP-adressen visas i IPv4- eller IPv6-adressformat. |
| OfficeWorkload | Office 365-tjänst som posten refererar till.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Åtgärd | Namnet på användarens eller administratörens aktivitet.  |
| Organisations-ID | GUID för organisationens Office 365-klient. Det här värdet är alltid detsamma för din organisation, oavsett Office 365-tjänsten där det sker. |
| RecordType | Typ av åtgärd som utförs. |
| ResultStatus | Anger om åtgärden (som anges i egenskapen Operation) lyckades eller inte. Möjliga värden är lyckades, PartiallySucceeded eller misslyckades. För Exchange admin-aktivitet är värdet antingen sant eller falskt. |
| UserId | UPN (User Principal Name) för den användare som utförde åtgärden som resulterade i att posten loggades. till exempel my_name@my_domain_name . Observera att poster för aktivitet som utförs av system konton (t. ex. SHAREPOINT\system eller NTAUTHORITY\SYSTEM) också ingår. | 
| UserKey | Ett alternativt ID för den användare som identifierats i UserId-egenskapen.  Till exempel fylls den här egenskapen i med Passport-unikt ID (PUID) för händelser som utförs av användare i SharePoint, OneDrive för företag och Exchange. Den här egenskapen kan också ange samma värde som UserID-egenskapen för händelser som inträffar i andra tjänster och händelser som utförs av system konton|
| UserType | Den typ av användaren som utförde åtgärden.<br><br>Administratör<br>Program<br>DcAdmin<br>Återkommande<br>Reserverat<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory bas

Följande egenskaper är gemensamma för alla Azure Active Directory poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Typ av Azure AD-händelse. |
| ExtendedProperties | Utökade egenskaper för Azure AD-händelsen. |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory konto inloggning

Dessa poster skapas när en Active Directory användare försöker logga in.

| Egenskap | Beskrivning |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Det program som utlöser konto inloggnings händelsen, till exempel Office 15. |
| `Client` | Information om klient enheten, enhetens operativ system och enhetens webbläsare som användes för konto inloggnings händelsen. |
| `LoginStatus` | Den här egenskapen är från OrgIdLogon. LoginStatus direkt. Mappningen av olika intressanta inloggnings problem kan göras genom att algoritmer för aviseringar. |
| `UserDomain` | Klientens identitets information (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Dessa poster skapas när ändringar eller tillägg görs till Azure Active Directory objekt.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Användaren som åtgärden (identifieras av egenskapen operation) utfördes på. |
| Skådespelare | Användaren eller tjänstens huvud namn som utförde åtgärden. |
| ActorContextId | GUID för den organisation som aktören tillhör. |
| ActorIpAddress | Aktörens IP-adress i IPV4-eller IPV6-adress format. |
| InterSystemsId | GUID som spårar åtgärder mellan komponenter i Office 365-tjänsten. |
| IntraSystemId |     Det GUID som genereras av Azure Active Directory för att spåra åtgärden. |
| SupportTicketId | Kund Supportens biljett-ID för åtgärden i "Act-on-of"-situationer. |
| TargetContextId | GUID för den organisation som mål användaren tillhör. |


### <a name="data-center-security"></a>Säkerhet för data Center

Dessa poster skapas från data Center säkerhets gransknings data.  

| Egenskap | Beskrivning |
|:--- |:--- |
| EffectiveOrganization | Namnet på den klient som höjningen/cmdleten riktades mot. |
| ElevationApprovedTime | Tidsstämpeln för när höjningen godkändes. |
| ElevationApprover | Namnet på en Microsoft-chef. |
| ElevationDuration | Varaktigheten för vilken höjningen var aktiv. |
| ElevationRequestId |     En unik identifierare för begäran om höjning. |
| ElevationRole | Rollen som höjningen begärdes för. |
| ElevationTime | Start tiden för utökningen. |
| Start_Time | Start tiden för cmdlet-körningen. |


### <a name="exchange-admin"></a>Exchange-administratör

Dessa poster skapas när ändringar görs i Exchange-konfigurationen.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Anger om cmdleten kördes av en användare i din organisation, av Microsoft Data Center-personal eller ett Data Center tjänst konto eller av en delegerad administratör. Värdet FALSE anger att cmdleten kördes av någon i din organisation. Värdet true anger att cmdleten kördes av data Center personal, ett Data Center tjänst konto eller en delegerad administratör. |
| ModifiedObjectResolvedName |     Detta är användarvänligt namn på objektet som ändrades av cmdleten. Detta loggas endast om cmdleten ändrar objektet. |
| OrganizationName | Namnet på klienten. |
| OriginatingServer | Namnet på den server från vilken cmdleten kördes. |
| Parametrar | Namnet och värdet för alla parametrar som användes med cmdleten som identifierades i egenskapen Operations. |


### <a name="exchange-mailbox"></a>Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-postlådor.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Information om e-postklienten som användes för att utföra åtgärden, till exempel en webb läsar version, Outlook-version och information om mobila enheter. |
| Client_IPAddress | IP-adressen för den enhet som användes när åtgärden loggades. IP-adressen visas i IPv4- eller IPv6-adressformat. |
| ClientMachineName | Namnet på datorn som är värd för Outlook-klienten. |
| ClientProcessName | E-postklienten som användes för att få åtkomst till post lådan. |
| ClientVersion | Versionen av e-postklienten. |
| InternalLogonType | Reserverat för internt bruk. |
| Logon_Type | Anger vilken typ av användare som har åtkomst till post lådan och utförde åtgärden som loggades. |
| LogonUserDisplayName |     Det användarvänliga namnet på den användare som utförde åtgärden. |
| LogonUserSid | SID för den användare som utförde åtgärden. |
| MailboxGuid | Exchange-GUID för den post låda som har öppnats. |
| MailboxOwnerMasterAccountSid | Post lådans ägar kontos huvud konto-SID. |
| MailboxOwnerSid | SID för post lådans ägare. |
| MailboxOwnerUPN | E-postadressen till den person som äger post lådan som har öppnats. |


### <a name="exchange-mailbox-audit"></a>Granskning av Exchange-postlåda

Dessa poster skapas när en post lådas gransknings post skapas.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Objekt | Representerar objektet som åtgärden utfördes på | 
| SendAsUserMailboxGuid | Exchange-GUID för den post låda som användes för att skicka e-post som. |
| SendAsUserSmtp | SMTP-adress för den användare som personifieras. |
| SendonBehalfOfUserMailboxGuid | Exchange-GUID för den post låda som användes för att skicka e-post åt. |
| SendOnBehalfOfUserSmtp | SMTP-adress till den användare på vars vägnar e-postmeddelandet skickas. |


### <a name="exchange-mailbox-audit-group"></a>Gransknings grupp för Exchange-postlåda

Dessa poster skapas när ändringar eller tillägg görs i Exchange-grupper.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Information om varje objekt i gruppen. |
| CrossMailboxOperations | Anger om åtgärden involverar fler än en post låda. |
| DestMailboxId | Ange endast om parametern CrossMailboxOperations är true. Anger mål post lådans GUID. |
| DestMailboxOwnerMasterAccountSid | Ange endast om parametern CrossMailboxOperations är true. Anger SID för huvud kontots SID för ägaren till mål brev lådan. |
| DestMailboxOwnerSid | Ange endast om parametern CrossMailboxOperations är true. Anger SID-mål post lådans SID. |
| DestMailboxOwnerUPN | Ange endast om parametern CrossMailboxOperations är true. Anger UPN för mål post lådans ägare. |
| DestFolder | Målmappen för åtgärder som flytta. |
| Mapp | Mappen där en grupp objekt finns. |
| Mappar |     Information om källmappen som ingår i en åtgärd. till exempel om mappar är markerade och tas bort. |


### <a name="sharepoint-base"></a>SharePoint-bas

De här egenskaperna är gemensamma för alla SharePoint-poster.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Anger att en händelse har inträffat i SharePoint. Möjliga värden är SharePoint eller ObjectModel. |
| ItemType | Typ av objekt som har öppnats eller ändrats. Se tabellen ItemType för information om objekt typer. |
| MachineDomainInfo | Information om synkronisering av enhets åtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| MachineId |     Information om synkronisering av enhets åtgärder. Den här informationen rapporteras endast om den finns i begäran. |
| Site_ | GUID för den plats där filen eller mappen som används av användaren finns. |
| Source_Name | Den entitet som utlöste den granskade åtgärden. Möjliga värden är SharePoint eller ObjectModel. |
| UserAgent | Information om användarens klient eller webbläsare. Den här informationen tillhandahålls av klienten eller webbläsaren. |


### <a name="sharepoint-schema"></a>SharePoint-schema

Dessa poster skapas när konfigurations ändringar görs i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Valfri sträng för anpassade händelser. |
| Event_Data |     Valfri nytto last för anpassade händelser. |
| ModifiedProperties | Egenskapen ingår för administratörs händelser, till exempel att lägga till en användare som medlem i en webbplats eller en administratörs grupp för webbplats samlingar. Egenskapen innehåller namnet på egenskapen som ändrades (t. ex. plats administratörs gruppen), det nya värdet för egenskapen modified (till exempel den användare som lades till som en plats administratör) och det tidigare värdet för det ändrade objektet. |


### <a name="sharepoint-file-operations"></a>SharePoint-filåtgärder

Dessa poster skapas som svar på fil åtgärder i SharePoint.

| Egenskap | Beskrivning |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Fil namns tillägget för en fil som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| DestinationFileName | Namnet på filen som kopieras eller flyttas. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| DestinationRelativeUrl | URL-adressen till målmappen dit en fil kopieras eller flyttas. Kombinationen av värdena för parametrarna SiteURL, DestinationRelativeURL och DestinationFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga Sök vägs namnet för den fil som kopierades. Den här egenskapen visas bara för FileCopied-och FileMoved-händelser. |
| SharingType | Den typ av delnings behörigheter som har tilldelats användaren som resursen delades med. Den här användaren identifieras av UserSharedWith-parametern. |
| Site_Url | URL-adressen till den plats där filen eller mappen som används av användaren finns. |
| SourceFileExtension | Fil namns tillägget för den fil som har öppnats av användaren. Den här egenskapen är tom om objektet som har öppnats är en mapp. |
| SourceFileName |     Namnet på filen eller mappen som används av användaren. |
| SourceRelativeUrl | URL till den mapp som innehåller den fil som används av användaren. Kombinationen av värdena för parametrarna SiteURL, SourceRelativeURL och SourceFileName är samma som värdet för egenskapen ObjectID, som är det fullständiga Sök vägs namnet för den fil som används av användaren. |
| UserSharedWith |     Användaren som en resurs delades med. |




## <a name="sample-log-queries"></a>Exempel på logg frågor

Följande tabell innehåller exempel på logg frågor för uppdaterings poster som samlas in av den här lösningen.

| Söka i data | Description |
| --- | --- |
|Antal åtgärder på din Office 365-prenumeration |OfficeActivity &#124; sammanfatta antal () efter åtgärd |
|Användning av SharePoint-webbplatser|OfficeActivity &#124; där OfficeWorkload = ~ "SharePoint" &#124; sammanfatta antal () efter SiteUrl \| Sortera efter antal ASC|
|Fil åtkomst åtgärder efter användar typ | OfficeActivity &#124; sammanfatta antal () efter UserType |
|Övervaka externa åtgärder på Exchange|OfficeActivity &#124; där OfficeWorkload = ~ "Exchange" och ExternalAccess = = True|



## <a name="next-steps"></a>Nästa steg

* Använd [logg frågor i Azure Monitor](../log-query/log-query-overview.md) om du vill visa detaljerade uppdaterings data.
* [Skapa dina egna instrument paneler](../learn/tutorial-logs-dashboards.md) för att visa dina favorit Sök frågor för Office 365.
* [Skapa aviseringar](../platform/alerts-overview.md) för att proaktivt meddela viktiga Office 365-aktiviteter.  
