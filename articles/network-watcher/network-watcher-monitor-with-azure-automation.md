---
title: Felsöka och övervaka VPN-gatewayer – Azure Automation
titleSuffix: Azure Network Watcher
description: Den här artikeln beskriver hur du diagnostiserar lokal anslutning med Azure Automation och Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011820"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Övervaka VPN-gatewayer med Network Watcher fel sökning

Det är viktigt att få till gång till djupgående insikter om nätverkets prestanda för att tillhandahålla pålitliga tjänster till kunder. Det är därför viktigt att identifiera problem med nätverks avbrott snabbt och vidta korrigerings åtgärder för att minimera drift stopps tillståndet. Med Azure Automation kan du implementera och köra en aktivitet i programmerings miljö genom Runbooks. Med Azure Automation skapas ett perfekt recept för att utföra kontinuerlig och proaktiv nätverks övervakning och avisering.

## <a name="scenario"></a>Scenario

Scenariot i följande bild är ett program med flera nivåer, med lokal anslutning som etableras med hjälp av en VPN Gateway och tunnel. Att se till att VPN Gateway är igång är viktigt för programmens prestanda.

En Runbook skapas med ett skript för att kontrol lera anslutnings status för VPN-tunneln med hjälp av resurs fel söknings-API: et för att kontrol lera anslutningens tunnel status. Om statusen inte är felfri skickas en e-utlösare till administratörer.

![Exempel på ett scenario][scenario]

Det här scenariot kommer att:

- Skapa en Runbook som anropar `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdleten för att felsöka anslutnings status
- Länka ett schema till Runbook

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar det här scenariot måste du ha följande krav:

- Ett Azure Automation-konto i Azure. Kontrol lera att Automation-kontot har de senaste modulerna och att den också har modulen AzureRM. Network. Modulen AzureRM. Network finns i modulen modul om du behöver lägga till den i ditt Automation-konto.
- Du måste ha en uppsättning autentiseringsuppgifter konfigurera i Azure Automation. Läs mer på [Azure Automation säkerhet](../automation/automation-security-overview.md)
- En giltig SMTP-server (Microsoft 365, ditt lokala e-postmeddelande eller en annan) och autentiseringsuppgifterna som definierats i Azure Automation
- En konfigurerad Virtual Network gateway i Azure.
- Ett befintligt lagrings konto med en befintlig behållare för att lagra loggarna i.

> [!NOTE]
> Den infrastruktur som visas i föregående bild är i illustrations syfte och skapas inte med de steg som beskrivs i den här artikeln.

### <a name="create-the-runbook"></a>Skapa Runbook

Det första steget i att konfigurera exemplet är att skapa runbooken. I det här exemplet används ett Kör som-konto. Mer information om kör som-konton finns i [autentisera Runbooks med kör som-konto i Azure](../automation/manage-runas-account.md)

### <a name="step-1"></a>Steg 1

Navigera till Azure Automation i [Azure Portal](https://portal.azure.com) och klicka på **Runbooks**

![Översikt över Automation-konto][1]

### <a name="step-2"></a>Steg 2

Klicka på **Lägg till en Runbook** för att starta Runbook-processen.

![Runbooks-blad][2]

### <a name="step-3"></a>Steg 3

Under **snabb registrering** klickar du på **skapa en ny Runbook** för att skapa runbooken.

![Lägg till ett Runbook-blad][3]

### <a name="step-4"></a>Steg 4

I det här steget ger vi runbooken ett namn, i exemplet kallas **Get-VPNGatewayStatus**. Det är viktigt att ge Runbook ett beskrivande namn och rekommendera att ge det ett namn som följer standard standarder för PowerShell-namngivning. Runbook-typen för det här exemplet är **PowerShell**, de andra alternativen är grafiska, PowerShell-arbetsflöde och grafiskt PowerShell-arbetsflöde.

![Runbook-blad][4]

### <a name="step-5"></a>Steg 5

I det här steget skapas Runbook, följande kod exempel visar all kod som behövs för exemplet. De objekt i koden som innehåller \<value\> måste ersättas med värdena från din prenumeration.

Använd följande kod som Klicka på **Spara**

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Steg 6

När runbooken har sparats måste ett schema länkas till det för att automatisera början av runbooken. Starta processen genom att klicka på **Schemalägg**.

![Steg 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Länka ett schema till Runbook

Ett nytt schema måste skapas. Klicka på **Länka ett schema till din Runbook**.

![Steg 7][7]

### <a name="step-1"></a>Steg 1

Klicka på **skapa ett nytt schema** på bladet **schema**

![Steg 8][8]

### <a name="step-2"></a>Steg 2

På bladet **nytt schema** fyller du i schema informationen. De värden som kan anges finns i följande lista:

- **Namn** – det egna namnet på schemat.
- **Beskrivning** – en beskrivning av schemat.
- **Startar** – det här värdet är en kombination av datum, tid och tidszon som utgör den tidpunkt då schemat utlöses.
- **Upprepning** – det här värdet avgör vilka scheman som upprepas.  Giltiga värden är **en gång** eller **återkommande**.
- **Upprepa var** och en av schemats upprepnings intervall i timmar, dagar, veckor eller månader.
- **Ange förfallo datum** – värdet bestämmer om schemat ska förfalla eller inte. Kan anges till **Ja** eller **Nej**. Du måste ange ett giltigt datum och en giltig tid om du väljer Ja.

> [!NOTE]
> Om du behöver köra en Runbook oftare än en gång i timmen måste flera scheman skapas med olika intervall (det vill säga 15, 30 45 minuter efter timmen)

![Steg 9][9]

### <a name="step-3"></a>Steg 3

Klicka på Spara för att spara schemat i runbooken.

![Steg 10][10]

## <a name="next-steps"></a>Nästa steg

Nu när du har en förståelse för hur du integrerar Network Watcher fel sökning med Azure Automation kan du lära dig hur du aktiverar paket fångster på VM-aviseringar genom att gå [till skapa en avisering utlöst paket avbildning med Azure Network Watcher](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png