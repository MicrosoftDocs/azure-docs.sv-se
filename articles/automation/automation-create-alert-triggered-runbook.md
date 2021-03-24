---
title: Använda en avisering för att utlösa en Azure Automation Runbook
description: Den här artikeln beskriver hur du utlöser en Runbook så att den körs när en Azure-avisering aktive ras.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: ea7979ad4a401d317ec126b7abfe354690475235
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953110"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använda en avisering för att utlösa en Azure Automation Runbook

Du kan använda [Azure Monitor](../azure-monitor/overview.md) för att övervaka mått och loggar på bas nivå för de flesta tjänster i Azure. Du kan anropa Azure Automation runbooks med hjälp av [Åtgärds grupper](../azure-monitor/alerts/action-groups.md) för att automatisera uppgifter baserat på aviseringar. Den här artikeln visar hur du konfigurerar och kör en Runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda Automation-runbooks med tre aviserings typer:

* Vanliga aviseringar
* Aktivitetsloggaviseringar
* Nära real tids aviseringar

> [!NOTE]
> Det vanliga aviserings schemat standardiserar användnings upplevelsen för aviserings meddelanden i Azure idag. Tidigare var de tre aviserings typerna i Azure idag (Metric, loggen och aktivitets loggen) haft egna e-postmallar, webhook-scheman osv. Läs mer i [vanliga aviserings scheman](../azure-monitor/alerts/alerts-common-schema.md)

När en avisering anropar en Runbook, är det faktiska anropet en HTTP POST-begäran till webhooken. Bröd texten i POST-begäran innehåller ett JSON-formaterat objekt som har användbara egenskaper som är relaterade till aviseringen. I följande tabell visas länkar till nytto Last schema för varje aviserings typ:

|Varning  |Beskrivning|Nytto Last schema  |
|---------|---------|---------|
|[Vanlig avisering](../azure-monitor/alerts/alerts-common-schema.md)|Det vanliga aviserings schema som standardiserar användnings upplevelsen för aviserings meddelanden i Azure idag.|Vanligt schema för aviserings nytto Last|
|[Aktivitets logg avisering](../azure-monitor/alerts/activity-log-alerts.md)    |Skickar ett meddelande när en ny händelse i Azure aktivitets loggen matchar vissa villkor. Till exempel när en `Delete VM` åtgärd utförs i **myProductionResourceGroup** eller när en ny Azure Service Health-händelse med en aktiv status visas.| [Schema för aktivitets logg aviserings nytto Last](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Varning för nära real tids mått](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Skickar ett meddelande snabbare än mått aviseringar när ett eller flera mått på plattforms nivå uppfyller angivna villkor. Till exempel när värdet för **CPU%** på en virtuell dator är större än 90 och värdet för **nätverk i** är större än 500 MB under de senaste 5 minuterna.| [Schema för nytto laster i real tids avisering](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika, hanteras varje aviserings typ annorlunda. I nästa avsnitt får du lära dig hur du skapar en Runbook för att hantera olika typer av aviseringar.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en Runbook för att hantera aviseringar

Om du vill använda automatisering med aviseringar behöver du en Runbook med logik som hanterar den aviserings-JSON-nyttolast som skickas till runbooken. Följande exempel-Runbook måste anropas från en Azure-avisering.

Enligt beskrivningen i föregående avsnitt har varje typ av avisering ett annat schema. Skriptet tar webhook-data från en avisering i `WebhookData` inmatnings parametern för Runbook. Sedan utvärderar skriptet JSON-nyttolasten för att avgöra vilken aviserings typ som används.

I det här exemplet används en avisering från en virtuell dator. Den hämtar VM-data från nytto lasten och använder sedan denna information för att stoppa den virtuella datorn. Anslutningen måste konfigureras i Automation-kontot där runbooken körs. När du använder aviseringar för att utlösa Runbooks, är det viktigt att kontrol lera aviserings statusen i den Runbook som utlöses. Runbooken utlöses varje tillfälle som aviseringen ändrar tillstånd. Aviseringar har flera tillstånd, med de två vanligaste som aktive ras och löses. Kontrol lera om det finns tillstånd i din Runbook-logik för att säkerställa att Runbook inte körs mer än en gång. Exemplet i den här artikeln visar hur du söker efter aviseringar med endast tillstånd aktiverat.

Runbooken använder `AzureRunAsConnection` [Kör som-kontot](./automation-security-overview.md) för anslutnings till gång för att autentisera med Azure för att utföra hanterings åtgärden mot den virtuella datorn.

Använd det här exemplet för att skapa en Runbook med namnet **Stop-AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda det med många olika resurser.

1. Gå till ditt Azure Automation-konto.
2. Under **process automatisering** väljer du **Runbooks**.
3. Längst upp i listan över Runbooks väljer du **+ skapa en Runbook**.
4. På sidan **Lägg till Runbook** anger du **Stop-AzureVmInResponsetoVMAlert** som Runbook-namn. För Runbook-typen väljer du **PowerShell**. Välj sedan **Skapa**.  
5. Kopiera följande PowerShell-exempel till sidan **Redigera** .

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Välj **publicera** för att spara och publicera runbooken.

## <a name="create-the-alert"></a>Skapa aviseringen

Aviseringar använder åtgärds grupper, som är samlingar av åtgärder som utlöses av aviseringen. Runbooks är bara en av de många åtgärder som du kan använda med åtgärds grupper.

1. I ditt Automation-konto väljer du **aviseringar** under **övervakning**.
1. Välj **+ Ny aviseringsregel**.
1. Klicka på **Välj** under **resurs**. På sidan **Välj en resurs** väljer du den virtuella dator som du vill Avisera om, och klickar på **Slutför**.
1. Klicka på **Lägg till villkor** under **villkor**. Välj den signal du vill använda, till exempel **procent processor** och klicka på **färdig**.
1. På sidan **Konfigurera signal logik** anger du **tröskelvärdet** under **aviserings logiken** och klickar på **Slutför**.
1. Under **Åtgärds grupper** väljer du **Skapa ny**.
1. På sidan **Lägg till åtgärds grupp** ger du åtgärds gruppen ett namn och ett kort namn.
1. Ge åtgärden ett namn. För åtgärds typ väljer du **Automation Runbook**.
1. Välj **Redigera information**. På sidan **Konfigurera Runbook** under Runbook- **källa** väljer du **användare**.  
1. Välj din **prenumeration** och ditt **Automation-konto** och välj sedan **Stop-AzureVmInResponsetoVMAlert** -runbooken.  
1. Välj **Ja** om **du vill aktivera det gemensamma aviserings schemat**.
1. Välj **OK** om du vill skapa en åtgärds grupp.

    ![Sidan Lägg till åtgärds grupp](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Du kan använda den här åtgärds gruppen i [aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts.md) och i [nära real tids aviseringar](../azure-monitor/alerts/alerts-overview.md) som du skapar.

1. Under **aviserings information** lägger du till en varnings regel namn och beskrivning och klickar på **skapa aviserings regel**.

## <a name="next-steps"></a>Nästa steg

* Information om hur du startar en Runbook med en webhook finns i [starta en Runbook från en webhook](automation-webhooks.md).
* Information om hur du startar en Runbook på olika sätt finns i [starta en Runbook](./start-runbooks.md).
* Information om hur du skapar en aktivitets logg avisering finns i [skapa aktivitets logg aviseringar](../azure-monitor/alerts/activity-log-alerts.md).
* Information om hur du skapar en nästan real tids avisering finns [i skapa en varnings regel i Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* En PowerShell-cmdlet-referens finns i [AZ. Automation](/powershell/module/az.automation).