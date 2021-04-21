---
title: Använda en avisering för att utlösa en Azure Automation runbook
description: Den här artikeln beskriver hur du utlöser en runbook som ska köras när en Azure-avisering utlöses.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36f1881ddd10498e7736de2d117a42021075abdc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834884"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Använda en avisering för att utlösa en Azure Automation runbook

Du kan använda [Azure Monitor](../azure-monitor/overview.md) för att övervaka mått och loggar på basnivå för de flesta tjänster i Azure. Du kan anropa Azure Automation runbooks med hjälp [av åtgärdsgrupper för](../azure-monitor/alerts/action-groups.md) att automatisera uppgifter baserat på aviseringar. Den här artikeln visar hur du konfigurerar och kör en Runbook med hjälp av aviseringar.

## <a name="alert-types"></a>Aviseringstyper

Du kan använda Automation-runbooks med tre aviseringstyper:

* Vanliga aviseringar
* Aktivitetsloggaviseringar
* Måttaviseringar nästan i realtid

> [!NOTE]
> Det vanliga aviseringsschemat standardiserar förbrukningsupplevelsen för aviseringsmeddelanden i Azure i dag. Tidigare har de tre aviseringstyperna i Azure idag (mått, logg och aktivitetslogg) haft egna e-postmallar, webhook-scheman osv. Mer information finns i Vanliga [aviseringsscheman](../azure-monitor/alerts/alerts-common-schema.md)

När en avisering anropar en runbook är det faktiska anropet en HTTP POST-begäran till webhooken. Brödtexten i POST-begäran innehåller ett JSON-formaterat objekt som har användbara egenskaper som är relaterade till aviseringen. I följande tabell visas länkar till nyttolastschemat för varje aviseringstyp:

|Varning  |Description|Nyttolastschema  |
|---------|---------|---------|
|[Vanlig avisering](../azure-monitor/alerts/alerts-common-schema.md)|Det vanliga aviseringsschemat som standardiserar förbrukningsupplevelsen för aviseringsmeddelanden i Azure idag.|Gemensamt schema för aviseringsnyttolast|
|[Aktivitetsloggavisering](../azure-monitor/alerts/activity-log-alerts.md)    |Skickar ett meddelande när en ny händelse i Azure-aktivitetsloggen matchar specifika villkor. Till exempel när en `Delete VM` åtgärd inträffar i **myProductionResourceGroup** eller när en Azure Service Health händelse med en aktiv status visas.| [Nyttolastschema för aktivitetsloggaviseringar](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Måttavisering i nära realtid](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Skickar ett meddelande snabbare än måttaviseringar när ett eller flera mått på plattformsnivå uppfyller angivna villkor. Till exempel när värdet för **CPU %** på en virtuell dator är större än 90 och värdet för **Nätverk i** är större än 500 MB under de senaste 5 minuterna.| [Nyttolastschema för måttaviseringar i nära realtid](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Eftersom de data som tillhandahålls av varje typ av avisering är olika hanteras varje aviseringstyp på olika sätt. I nästa avsnitt får du lära dig hur du skapar en runbook för att hantera olika typer av aviseringar.

## <a name="create-a-runbook-to-handle-alerts"></a>Skapa en runbook för att hantera aviseringar

Om du vill använda Automation med aviseringar behöver du en runbook som har logik som hanterar aviseringens JSON-nyttolast som skickas till runbooken. Följande exempel-runbook måste anropas från en Azure-avisering.

Enligt beskrivningen i föregående avsnitt har varje typ av avisering ett annat schema. Skriptet tar webhook-data från en avisering i `WebhookData` runbook-indataparametern. Sedan utvärderar skriptet JSON-nyttolasten för att avgöra vilken aviseringstyp som används.

I det här exemplet används en avisering från en virtuell dator. Den hämtar VM-data från nyttolasten och använder sedan informationen för att stoppa den virtuella datorn. Anslutningen måste konfigureras i Automation-kontot där runbooken körs. När du använder aviseringar för att utlösa runbooks är det viktigt att kontrollera aviseringsstatusen i den runbook som utlöses. Runbooken utlöses varje gång aviseringen ändras. Aviseringar har flera tillstånd, där de två vanligaste är Aktiverad och Löst. Kontrollera statusen i din Runbook-logik för att säkerställa att runbooken inte körs mer än en gång. Exemplet i den här artikeln visar hur du söker efter aviseringar med endast tillståndet Aktiverad.

Runbooken använder Kör som-kontot `AzureRunAsConnection` [för anslutningstillgången](./automation-security-overview.md) för att autentisera med Azure för att utföra hanteringsåtgärden mot den virtuella datorn.

Använd det här exemplet för att skapa en runbook med **namnet Stop-AzureVmInResponsetoVMAlert**. Du kan ändra PowerShell-skriptet och använda det med många olika resurser.

1. Gå till ditt Azure Automation konto.
2. Under **Processautomatisering** väljer **du Runbooks**.
3. Längst upp i listan över runbooks väljer du **+ Skapa en runbook.**
4. På sidan **Lägg till runbook** anger du **Stop-AzureVmInResponsetoVMAlert** som runbooknamn. Som Runbook-typ väljer du **PowerShell**. Välj sedan **Skapa**.  
5. Kopiera följande PowerShell-exempel till **sidan** Redigera.

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

6. Välj **Publicera** för att spara och publicera runbooken.

## <a name="create-the-alert"></a>Skapa aviseringen

Aviseringar använder åtgärdsgrupper, som är samlingar med åtgärder som utlöses av aviseringen. Runbooks är bara en av de många åtgärder som du kan använda med åtgärdsgrupper.

1. I ditt Automation-konto väljer du **Aviseringar** under **Övervakning.**
1. Välj **+ Ny aviseringsregel**.
1. Klicka **på Välj** under **Resurs.** På sidan **Välj en resurs väljer** du den virtuella dator som du vill varna för och klickar på **Klar.**
1. Klicka **på Lägg till villkor** under **Villkor**. Välj den signal som du vill använda, till exempel **Processorprocentandel och** klicka på **Klar.**
1. På sidan **Konfigurera signallogik** anger du tröskelvärdet under **Aviseringslogik** och klickar på **Klar.** 
1. Under **Åtgärdsgrupper** väljer du **Skapa ny.**
1. På sidan **Lägg till åtgärdsgrupp** ger du åtgärdsgruppen ett namn och ett kort namn.
1. Ge åtgärden ett namn. Som åtgärdstyp väljer du **Automation Runbook**.
1. Välj **Redigera information.** På sidan **Konfigurera runbook** går du till **Runbook-källan** och väljer **Användare.**  
1. Välj ditt **prenumerations- och Automation-konto** och välj sedan runbooken **Stop-AzureVmInResponsetoVMAlert.**   
1. Välj **Ja** för **Aktivera det vanliga aviseringsschemat**.
1. Välj OK för att skapa **åtgärdsgruppen.**

    ![Sidan Lägg till åtgärdsgrupp](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Du kan använda den här åtgärdsgruppen i [aktivitetsloggaviseringar](../azure-monitor/alerts/activity-log-alerts.md) [och nästan realtidsaviseringar](../azure-monitor/alerts/alerts-overview.md) som du skapar.

1. Under **Aviseringsinformation** lägger du till ett namn och en beskrivning för aviseringsregeln och klickar **på Skapa aviseringsregel.**

## <a name="next-steps"></a>Nästa steg

* Information om hur du startar en runbook med hjälp av en webhook finns [i Starta en runbook från en webhook.](automation-webhooks.md)
* Om du vill identifiera olika sätt att starta en runbook kan du [gå till Starta en runbook.](./start-runbooks.md)
* Information om hur du skapar en aktivitetsloggavisering finns i [Skapa aktivitetsloggaviseringar.](../azure-monitor/alerts/activity-log-alerts.md)
* Information om hur du skapar en avisering nästan i realtid finns i [Skapa en aviseringsregel i Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* En PowerShell-cmdlet-referens finns i [Az.Automation](/powershell/module/az.automation).
