---
title: Skala sessioner värdar Azure Automation Windows Virtual Desktop (klassisk) – Azure
description: Så här skalar du automatiskt Windows Virtual Desktop (klassiska) värdar med Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 907871a85680202a4a8b5f73b4454a9b2f2fe103
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444317"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>Skala Windows Virtual Desktop (klassiska)-sessionsbaserade med Azure Automation

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer (klassisk), vilket inte stöder Azure Resource Manager virtuella Skriv bords objekt i Windows.

Du kan minska din totala distributions kostnad för virtuella Windows-datorer genom att skala dina virtuella datorer (VM). Det innebär att du stänger av och avallokerar sessions värden för virtuella datorer vid låg belastnings tider, sedan aktiverar dem igen och omallokerar dem under hög belastnings tid.

I den här artikeln får du lära dig om skalnings verktyget som skapats med Azure Automation-kontot och Azure Logic-appen som automatiskt skalar virtuella datorer i Windows Virtual Desktop-miljön. Om du vill lära dig hur du använder skalnings verktyget kan du gå vidare till [krav](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Hur skalnings verktyget fungerar

Skalnings verktyget ger till gång till ett kostnads fritt automatiserings alternativ för kunder som vill optimera sina VM-kostnader för en värd.

Du kan använda skalnings verktyget för att:

- Schemalägg virtuella datorer för start och stopp baserat på högsta och Off-Peak kontors tid.
- Skala ut virtuella datorer baserat på antalet sessioner per CPU-kärna.
- Skala i virtuella datorer under Off-Peak timmar och lämna det lägsta antalet virtuella dator värdar som körs.

Skalnings verktyget använder en kombination av ett Azure Automation konto, en PowerShell-Runbook, en webhook och Azure Logic-appen att fungera. När verktyget körs anropar Azure Logic app en webhook för att starta Azure Automation Runbook. Runbooken skapar sedan ett jobb.

Under den högsta användnings tiden kontrollerar jobbet det aktuella antalet sessioner och VM-kapaciteten för den aktuella värddatorn som kör sessionen för varje adresspool. Den här informationen används för att beräkna om de virtuella datorerna i sessionen kan stödja befintliga sessioner baserat på den *SessionThresholdPerCPU* -parameter som definierats för **CreateOrUpdateAzLogicApp.ps1** -filen. Om de virtuella datorerna i sessionen inte har stöd för befintliga sessioner startar jobbet ytterligare virtuella dator värdar i den aktuella adresspoolen.

>[!NOTE]
>*SessionThresholdPerCPU* begränsar inte antalet sessioner på den virtuella datorn. Den här parametern bestämmer bara när nya virtuella datorer måste startas för att belastningsutjämna anslutningarna. Om du vill begränsa antalet sessioner måste du följa anvisningarna [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) för att konfigurera *MaxSessionLimit* -parametern enligt detta.

Under den högsta användnings tiden bestämmer jobbet hur många VM-sessioner som ska stängas av, baserat på parametern *MinimumNumberOfRDSH* . Om du ställer in parametern *LimitSecondsToForceLogOffUser* på ett positivt värde som inte är noll, kommer jobbet att ställa in de virtuella datorerna i den virtuella datorn i tömnings läge för att förhindra att nya sessioner ansluter till värdarna. Jobbet meddelar sedan alla användare som är inloggade att spara sitt arbete, väntar på den konfigurerade tiden och tvingar sedan användarna att logga ut. När alla användarsessioner på VM-sessionen har loggats ut stängs jobbet av den virtuella datorn. När den virtuella datorn har stängts av återställs sitt läge för att tömma sessionen.

>[!NOTE]
>Om du ställer in sessionens värddator för virtuella datorer manuellt i dränerings läge, hanterar inte jobbet den virtuella datorns VM för sessionen. Om den virtuella datorns värd för sessionen körs och är inställt på dränerings läge behandlas den som otillgänglig, vilket gör att jobbet startar ytterligare virtuella datorer för att hantera belastningen. Vi rekommenderar att du taggar alla virtuella Azure-datorer innan du anger dem till dränerings läge manuellt. Du kan namnge taggen med parametern *MaintenanceTagName* när du skapar Schemaläggaren för Azure Logic app senare. Taggar hjälper dig att skilja de virtuella datorerna från dem som skalnings verktyget hanterar. Att ställa in en underhålls tagg förhindrar även skalnings verktyget från att göra ändringar i den virtuella datorn tills du tar bort taggen.

Om du ställer in parametern *LimitSecondsToForceLogOffUser* på noll tillåter jobbet konfigurations inställningen för sessionen i angivna grup principer för att hantera signering av användarsessioner. Om du vill se dessa grup principer går du till **dator konfiguration**  >  **principer**  >  **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >    >  sessionsvärdservern för fjärrskrivbordssession. Om det finns aktiva sessioner på en virtuell dator för en virtuell dator kommer jobbet att lämna den virtuella dator som körs på sessionen. Om det inte finns några aktiva sessioner stängs jobbet av den virtuella datorns sessions värd.

När som helst tar jobbet också med *MaxSessionLimit* i kontot för att avgöra om det aktuella antalet sessioner är mer än 90% av den maximala kapaciteten. Om så är fallet kommer jobbet att starta ytterligare virtuella datorer i sessions värden.

Jobbet körs regelbundet baserat på ett angivet upprepnings intervall. Du kan ändra det här intervallet baserat på storleken på din Windows Virtual Desktop-miljö, men kom ihåg att starta och stänga av virtuella datorer kan ta en stund, så kom ihåg att ta hänsyn till fördröjningen. Vi rekommenderar att du ställer in upprepnings intervallet på var 15: e minut.

Verktyget har dock också följande begränsningar:

- Den här lösningen gäller endast för pooler för virtuella datorer i flera sessioner.
- Den här lösningen hanterar virtuella datorer i vilken region som helst, men kan bara användas i samma prenumeration som ditt Azure Automation-konto och Azure Logic-appen.
- Den maximala körningen av ett jobb i runbooken är 3 timmar. Om det tar längre tid att starta eller stoppa de virtuella datorerna i poolen, kommer jobbet att Miss Förslut. Mer information finns i [delade resurser](../../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>Skalnings verktyget styr belastnings Utjämnings läget för den aktuella värddatorn som skalas för närvarande. Verktyget använder det bredd-första belastnings Utjämnings läget för både högsta och låg belastnings tid.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar konfigurera skalnings verktyget ser du till att du har följande klart:

- En [Windows-klient för virtuella skriv bord och värd bassäng](create-host-pools-arm-template.md)
- VM-poolen för sessioner som kon figurer ATS och registrerats med Windows Virtual Desktop-tjänsten
- En användare med [deltagar åtkomst](../../role-based-access-control/role-assignments-portal.md) i Azure-prenumerationen

Datorn som du använder för att distribuera verktyget måste ha:

- Windows PowerShell 5,1 eller senare
- Microsoft AZ PowerShell-modulen

Om du har allt klart är det dags att sätta igång.

## <a name="create-or-update-an-azure-automation-account"></a>Skapa eller uppdatera ett Azure Automation konto

>[!NOTE]
>Om du redan har ett Azure Automation konto med en Runbook som kör en äldre version av skalnings skriptet behöver du bara följa anvisningarna nedan för att kontrol lera att det har uppdaterats.

Först behöver du ett Azure Automation-konto för att köra PowerShell-runbooken. Den process som beskrivs i det här avsnittet är giltig även om du har ett befintligt Azure Automation-konto som du vill använda för att konfigurera PowerShell-runbooken. Så här konfigurerar du det:

1. Öppna Windows PowerShell.

2. Kör följande cmdlet för att logga in på ditt Azure-konto.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Ditt konto måste ha deltagar behörighet för den Azure-prenumeration där du vill distribuera skalnings verktyget.

3. Kör följande cmdlet för att ladda ned skriptet för att skapa Azure Automation-kontot:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Kör följande cmdlet för att köra skriptet och skapa Azure Automation-kontot. Du kan antingen fylla i värden för parametrarna eller kommentera dem för att använda standardinställningarna.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Cmdletens utdata kommer att innehålla en webhook-URI. Se till att spara en post i URI: n eftersom du kommer att använda den som en parameter när du konfigurerar körnings schema för Azure Logic-appen.

6. Om du har angett parametern **WorkspaceName** för Log Analytics, kommer cmdletens utdata också att innehålla Log Analytics arbetsyte-ID och dess primära nyckel. Glöm inte att komma ihåg URI eftersom du behöver använda den igen senare som en parameter när du konfigurerar körnings schema för Azure Logic-appen.

7. När du har konfigurerat ditt Azure Automation-konto loggar du in på din Azure-prenumeration och kontrollerar att ditt Azure Automation-konto och att den relevanta runbooken har visats i den angivna resurs gruppen, som du ser i följande bild:

    >[!div class="mx-imgBorder"]
    >![En bild av sidan Azure-översikt som visar det nyskapade Azure Automation kontot och runbooken.](media/automation-account.png)

    Markera namnet på din Runbook för att kontrol lera om din webhook är där det ska vara. Gå sedan till avsnittet resurser för Runbook och välj **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Skapa ett Kör som-konto för Azure Automation

Nu när du har ett Azure Automation konto måste du också skapa ett Azure Automation kör som-konto om du inte redan har ett. Det här kontot kommer att ge verktyget åtkomst till dina Azure-resurser.

Ett [Azure Automation kör som-konto](../../automation/manage-runas-account.md) tillhandahåller autentisering för att hantera resurser i Azure med Azure-cmdletar. När du skapar ett Kör som-konto skapas en ny tjänst huvud användare i Azure Active Directory och deltagar rollen tilldelas till tjänstens huvud namns användare på prenumerations nivån. Ett Kör som-konto i Azure är ett bra sätt att autentisera säkert med certifikat och ett huvud namn för tjänsten utan att behöva lagra ett användar namn och lösen ord i ett Credential-objekt. Om du vill veta mer om autentisering med kör som-konto, se [begränsa behörigheter för kör som-konto](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Alla användare som är medlemmar i rollen prenumerations administratörer och medadministratör för prenumerationen kan skapa ett Kör som-konto.

Så här skapar du ett Kör som-konto i ditt Azure Automation-konto:

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser anger och väljer du **Automation-konton**.

2. På sidan **Automation-konton** väljer du namnet på ditt Azure Automation konto.

3. I fönstret till vänster i fönstret väljer du **Kör som-konton** under avsnittet **konto inställningar** .

4. Välj **Kör som-konto i Azure**. När fönstret **Lägg till Azure kör som-konto** visas granskar du översiktlig information och väljer sedan **skapa** för att starta processen för att skapa kontot.

5. Vänta några minuter för Azure för att skapa kör som-kontot. Du kan spåra hur du skapar en förloppet i menyn under meddelanden.

6. När processen har slutförts skapas en till gång med namnet **AzureRunAsConnection** i det angivna Azure Automation kontot. Välj **Kör som-konto i Azure**. Anslutnings till gången innehåller program-ID, klient-ID, prenumerations-ID och tumavtryck för certifikatet. Kom ihåg program-ID: t eftersom du kommer att använda det senare. Du kan också hitta samma information på sidan **anslutningar** . Gå till den här sidan genom att välja **anslutningar** under avsnittet **delade resurser** i fönstret till vänster i fönstret och klicka på anslutnings till gången med namnet **AzureRunAsConnection**.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Skapa en roll tilldelning i Windows Virtual Desktop

Därefter måste du skapa en roll tilldelning så att **AzureRunAsConnection** kan samverka med det virtuella Windows-skrivbordet. Se till att använda PowerShell för att logga in med ett konto som har behörighet att skapa roll tilldelningar.

Börja med att hämta och importera [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som ska användas i PowerShell-sessionen om du inte redan gjort det. Kör följande PowerShell-cmdlets för att ansluta till det virtuella Windows-skrivbordet och Visa dina klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

När du hittar klienten med de värdar som du vill skala, följer du instruktionerna i [skapa ett Azure Automation kör som-konto](#create-an-azure-automation-run-as-account) för att samla in program-ID: t för **AzureRunAsConnection** och använda namnet på den Windows-klient för virtuella skriv bord som du fick från föregående cmdlet i följande cmdlet för att skapa roll tilldelningen:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Skapa Azure Logic-appen och körnings schemat

Slutligen måste du skapa Azure Logic-appen och konfigurera ett körnings schema för det nya skalnings verktyget.

1. Öppna Windows PowerShell.

2. Kör följande cmdlet för att logga in på ditt Azure-konto.

    ```powershell
    Login-AzAccount
    ```

3. Kör följande cmdlet för att ladda ned skriptet för att skapa Azure Logic-appen.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Kör följande cmdlet för att logga in på Windows Virtual Desktop med ett konto som har behörighet som RDS-ägare eller RDS-deltagare.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Kör följande PowerShell-skript för att skapa Azure Logic-appen och körnings scheman för din värd bassäng

    >[!NOTE]
    >Du måste köra det här skriptet för varje adresspool som du vill skala, men du behöver bara ett Azure Automation konto.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    När du har kört skriptet bör Azure Logic app visas i en resurs grupp, som du ser i följande bild.

    >[!div class="mx-imgBorder"]
    >![En bild av översikts sidan för ett exempel på en Azure Logic-app.](../media/logic-app.png)

    Om du vill göra ändringar i körnings schemat, till exempel ändra upprepnings intervallet eller tids zonen, går du till Azure Logic Apps autoskalning Scheduler och väljer **Redigera** för att gå till Azure Logic App Designer.

    >[!div class="mx-imgBorder"]
    >![En bild av Azure Logic App Designer. De upprepnings-och webhook-menyer som låter användaren redigera upprepnings tider och webhook-filen är öppen.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Hantera skalnings verktyget

Nu när du har skapat skalnings verktyget kan du komma åt dess utdata. I det här avsnittet beskrivs några funktioner som du kan ha nytta av.

### <a name="view-job-status"></a>Visa jobbstatus

Du kan visa en sammanfattnings status för alla Runbook-jobb eller Visa en mer djupgående status för ett särskilt Runbook-jobb i Azure Portal.

Till höger om det valda Azure Automation kontot under "jobb statistik" kan du Visa en lista över sammanfattningar av alla Runbook-jobb. Om du öppnar sidan **jobb** till vänster i fönstret visas aktuella jobb status, start tider och slut för ande tider.

>[!div class="mx-imgBorder"]
>![En skärm bild av sidan jobb status.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Visa utdata för loggar och skalnings verktyg

Du kan visa loggarna för skalnings-och skalnings åtgärder genom att öppna din Runbook och välja jobbet.

Navigera till Runbook i resurs gruppen som är värd för Azure Automation konto och välj **Översikt**. På sidan Översikt väljer du ett jobb under **senaste jobb** för att Visa skalnings verktygets utdata, som du ser i följande bild.

>[!div class="mx-imgBorder"]
>![En bild av utdatafönstret för skalnings verktyget.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Kontrol lera Runbook-skriptets versions nummer

Du kan kontrol lera vilken version av Runbook-skriptet som du använder genom att öppna Runbook-filen i ditt Azure Automation konto och välja **Visa**. Ett skript för runbooken visas på höger sida av skärmen. I skriptet ser du versions numret i formatet `v#.#.#` under `SYNOPSIS` avsnittet. Du kan hitta det senaste versions numret [här](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1). Om du inte ser ett versions nummer i Runbook-skriptet, innebär det att du kör en tidigare version av skriptet och att du bör uppdatera den direkt. Om du behöver uppdatera ditt Runbook-skript följer du anvisningarna i [skapa eller uppdatera ett Azure Automation konto](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Rapporterings problem

När du rapporterar ett problem måste du ange följande information för att hjälpa oss att felsöka:

- En fullständig logg från fliken **alla loggar** i det jobb som orsakade problemet. Om du vill lära dig hur du hämtar loggen följer du instruktionerna i [utdata för Visa loggar och skalnings verktyg](#view-logs-and-scaling-tool-output). Om det finns känslig eller privat information i loggen kan du ta bort den innan du skickar in ärendet till oss.

- Den version av Runbook-skriptet som du använder. Läs mer om hur du hämtar versions numret i [kontrol lera Runbook-skriptets versions nummer](#check-the-runbook-script-version-number)

- Versions numret för var och en av följande PowerShell-moduler som är installerade på ditt Azure Automation-konto. Du hittar dessa moduler genom att öppna Azure Automation konto, välja **moduler** under avsnittet **delade resurser** i fönstret till vänster i fönstret och sedan söka efter modulens namn.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Microsoft. RDInfra. RDPowershell

- Förfallo datumet för ditt [Kör som-konto](#create-an-azure-automation-run-as-account). Du hittar detta genom att öppna ditt Azure Automation-konto och sedan välja **Kör som-konton** under **konto inställningar** i fönstret till vänster i fönstret. Förfallo datumet bör vara under **Azure kör som-kontot**.

### <a name="log-analytics"></a>Log Analytics

Om du valde att använda Log Analytics kan du Visa alla loggdata i en anpassad logg med namnet **WVDTenantScale_CL** under **anpassade loggar** i vyn **loggar** i din Log Analytics arbets yta. Vi har listat några exempel frågor som du kan ha nytta av.

- Ange följande fråga om du vill visa alla loggar för en värd pool

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Om du vill visa det totala antalet virtuella datorer som körs för närvarande och aktiva användarsessioner i din värd grupp anger du följande fråga

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Om du vill visa status för alla virtuella datorers VM-värdar i en adresspool anger du följande fråga

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Ange följande fråga om du vill visa eventuella fel och varningar

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Rapportera problem

Problem rapporter för skalnings verktyget hanteras för närvarande av Microsoft Support. När du skapar en ärende rapport, se till att följa anvisningarna i [rapporterings problem](#reporting-issues). Om du har feedback om verktyget eller vill begära nya funktioner, öppnar du ett GitHub-ärende med etiketten "4-WVD-Scaling-Tool" på [sidan RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool).
