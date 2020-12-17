---
title: 'Azure VPN Gateway: Konfigurera aviseringar för logg händelser för diagnostiska resurser'
description: Lär dig hur du konfigurerar aviseringar baserat på resurs logg händelser från Azure VPN Gateway med Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656622"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Konfigurera aviseringar för resurs logg händelser från VPN Gateway

Den här artikeln hjälper dig att ställa in aviseringar baserat på resurs logg händelser från Azure VPN Gateway att använda Azure Monitor Log Analytics. 

Följande resurs loggar är tillgängliga i Azure:

|***Namn** _ | _*_Beskrivning_*_ |
|--- | --- |
|GatewayDiagnosticLog | Innehåller resurs loggar för konfigurations händelser för gateway, primära ändringar och underhålls händelser |
|TunnelDiagnosticLog | Innehåller ändringar av tunnel tillstånds händelser. Tunnel anslutning/från kopplings händelser har en sammanfattande orsak för tillstånds ändringen om det är tillämpligt |
|RouteDiagnosticLog | Loggar ändringar av statiska vägar och BGP-händelser som inträffar på gatewayen |
|IKEDiagnosticLog | Loggar IKE-kontrollmeddelanden och-händelser på gatewayen |
|P2SDiagnosticLog | Loggar punkt-till-plats-kontrollmeddelanden och händelser på gatewayen. Information om anslutnings källan tillhandahålls enbart för IKEv2-och OpenVPN-anslutningar |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Konfigurera aviseringar i Azure Portal

Följande exempel steg skapar en avisering för en från kopplings händelse som omfattar en plats-till-plats-VPN-tunnel:


1. I Azure Portal söker du efter _ *Log Analytics** under **alla tjänster** och väljer **Log Analytics arbets ytor**.

   ![Val för att gå till Log Analytics arbets ytor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Skapa")

2. Välj **skapa** på sidan **Log Analytics** .

   ![Log Analytics sida med knappen Skapa](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Välj")

3. Välj **Skapa ny** och fyll i informationen.

   ![Information om hur du skapar en arbets yta för Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Välj")

4. Hitta din VPN-gateway på   >  bladet övervaka **diagnostikinställningar inställningar** .

   ![Val för att hitta VPN-gatewayen i diagnostikinställningar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Välj")

5. Om du vill aktivera diagnostik dubbelklickar du på gatewayen och väljer sedan **Aktivera diagnostik**.

   ![Val för att aktivera diagnostik](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Välj")

6. Fyll i informationen och kontrol lera att **Skicka till Log Analytics** och **TunnelDiagnosticLog** är markerat. Välj Log Analytics arbets ytan som du skapade i steg 3.

   ![Markerade kryss rutor](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Välj")

   > [!NOTE]
   > Det kan ta några timmar innan data visas initialt.

7. Gå till översikten för den virtuella nätverks-Gateway-resursen och välj **aviseringar** från fliken **övervakning** . Skapa sedan en ny varnings regel eller redigera en befintlig aviserings regel.

   ![Val för att skapa en ny varnings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")

   ![punkt-till-plats](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Välj")
8. Välj arbets ytan Log Analytics och resursen.

   ![Val för arbets yta och resurs](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Välj")

9. Välj **anpassad loggs ökning** som signal logik under **Lägg till villkor**.

   ![Val för en anpassad loggs ökning](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Välj")

10. Ange sedan följande fråga i textrutan **Sökfråga**. Ersätt värdena i <> och TimeGenerated efter behov.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Ange tröskelvärdet till 0 och välj **klart**.

    ![Ange en fråga och välja ett tröskelvärde](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Välj")

11. På sidan **Skapa regel** väljer du **Skapa ny** under avsnittet **Åtgärds grupper** . Fyll i informationen och välj **OK**.

    ![Information om en ny åtgärds grupp](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Välj")

12. På sidan **Skapa regel** fyller du i informationen för att **Anpassa åtgärder** och kontrollerar att rätt namn visas i avsnittet **Åtgärds grupp namn** . Välj **skapa aviserings regel** för att skapa regeln.

    ![Val för att skapa en regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Välj")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Konfigurera aviseringar med hjälp av PowerShell

Följande exempel steg skapar en avisering för en från kopplings händelse som omfattar en plats-till-plats-VPN-tunnel.

1. Skapa en Log Analytics arbets yta:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Aktivera diagnostik för VPN-gatewayen:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Skapa en åtgärds grupp.

   Den här koden skapar en åtgärds grupp som skickar ett e-postmeddelande när en avisering utlöses:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Skapa en varnings regel baserat på en anpassad loggs ökning:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar aviseringar för tunnel mått finns i [Konfigurera aviseringar på VPN Gateway mått](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
