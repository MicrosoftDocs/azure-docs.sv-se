---
title: Diagnostisk resursloggning för en nätverkssäkerhetsgrupp
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar diagnostikresursloggar för händelse- och regelräknare för en Azure-nätverkssäkerhetsgrupp.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 0d171dee87a391c5e1d66db10363e6823ef387c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774169"
---
# <a name="resource-logging-for-a-network-security-group"></a>Resursloggning för en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp (NSG) innehåller regler som tillåter eller nekar trafik till ett virtuellt nätverksundernät, nätverksgränssnitt eller båda. 

När du aktiverar loggning för en NSG kan du samla in följande typer av resurslogginformation:

* **Händelse:** Poster loggas för vilka NSG-regler tillämpas på virtuella datorer, baserat på MAC-adress.
* **Regelräknare:** Innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik. Status för dessa regler samlas in var 300:e sekund.

Resursloggar är endast tillgängliga för NSG:er som distribueras via Azure Resource Manager distributionsmodellen. Du kan inte aktivera resursloggning för NSG:er som distribuerats via den klassiska distributionsmodellen. En bättre förståelse av de två modellerna finns i Förstå [Azure-distributionsmodeller.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Resursloggning aktiveras separat för varje *NSG* som du vill samla in diagnostikdata för. Om du i stället är intresserad av aktivitetsloggar (drift) kan du gå till [Azure-aktivitetsloggning.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du är intresserad av IP-trafik som flödar via NSG:er kan du se Azure Network Watcher [NSG-flödesloggar](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 

## <a name="enable-logging"></a>Aktivera loggning

Du kan använda [Azure Portal,](#azure-portal) [PowerShell eller](#powershell) [Azure CLI](#azure-cli) för att aktivera resursloggning.

### <a name="azure-portal"></a>Azure-portalen

1. Logga in på [portalen](https://portal.azure.com).
2. Välj **Alla tjänster** och skriv sedan *nätverkssäkerhetsgrupper*. När **Nätverkssäkerhetsgrupper** visas i sökresultatet väljer du dem.
3. Välj den NSG som du vill aktivera loggning för.
4. Under **ÖVERVAKNING** väljer **du Diagnostikloggar** och sedan **Aktivera diagnostik**, som du ser i följande bild:

   ![Slå på diagnostik](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Under **Diagnostikinställningar** anger eller väljer du följande information och väljer sedan **Spara:**

    | Inställning                                                                                     | Värde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Namn                                                                                        | Ett val av namn.  Till exempel: *myNsgDiagnostics*      |
    | **Arkivera till ett lagringskonto,** **strömma till en händelsehubb** och **skicka till Log Analytics** | Du kan välja så många mål som du vill. Mer information om var och en finns i [Loggmål.](#log-destinations)                                                                                                                                           |
    | LOG                                                                                         | Välj antingen eller båda loggkategorierna. Mer information om de data som loggas för varje kategori finns i [Loggkategorier.](#log-categories)                                                                                                                                             |
6. Visa och analysera loggar. Mer information finns i Visa [och analysera loggar.](#view-and-analyze-logs)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell), eller genom att köra PowerShell från datorn. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra för att logga in på `Connect-AzAccount` Azure med ett konto som har nödvändiga [behörigheter.](virtual-network-network-interface.md#permissions)

Om du vill aktivera resursloggning behöver du ID:t för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [New-AzNetworkSecurityGroup.](/powershell/module/az.network/new-aznetworksecuritygroup)

Hämta den nätverkssäkerhetsgrupp som du vill aktivera resursloggning för med [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup* anger du följande kommando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Du kan skriva resursloggar till tre måltyper. Mer information finns i [Loggmål.](#log-destinations) I den här artikeln skickas loggar till *Log Analytics-målet* som exempel. Hämta en befintlig Log Analytics-arbetsyta [med Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Om du till exempel vill hämta en befintlig *arbetsyta med namnet myWorkspace* i en resursgrupp med namnet *myWorkspaces* anger du följande kommando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Om du inte har en befintlig arbetsyta kan du skapa en med [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Det finns två typer av loggning som du kan aktivera loggar för. Mer information finns i [Loggkategorier.](#log-categories) Aktivera resursloggning för NSG med [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). I följande exempel loggas både händelse- och räknarkategoridata till arbetsytan för en NSG med hjälp av ID:n för den NSG och den arbetsyta som du hämtade tidigare:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Om du bara vill logga data för en kategori eller en annan, i stället för båda, lägger du till alternativet i föregående kommando följt av `-Categories` *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du vill logga till ett annat [mål](#log-destinations) än en Log Analytics-arbetsyta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) eller [en händelsehubb.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Visa och analysera loggar. Mer information finns i Visa [och analysera loggar.](#view-and-analyze-logs)

### <a name="azure-cli"></a>Azure CLI

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra Azure CLI från datorn. Det Azure Cloud Shell är ett kostnadsfritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör CLI från datorn behöver du version 2.0.38 eller senare. Kör `az --version` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli) Om du kör CLI lokalt måste du också köra för att logga in på `az login` Azure med ett konto som har de behörigheter som [krävs.](virtual-network-network-interface.md#permissions)

Om du vill aktivera resursloggning behöver du ID:t för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create).

Hämta den nätverkssäkerhetsgrupp som du vill aktivera resursloggning för med [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resursgrupp med namnet *myResourceGroup* anger du följande kommando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Du kan skriva resursloggar till tre måltyper. Mer information finns i [Loggmål.](#log-destinations) I den här artikeln skickas loggar till *Log Analytics-målet* som exempel. Mer information finns i [Loggkategorier.](#log-categories)

Aktivera resursloggning för NSG med [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create). I följande exempel loggas både händelse- och räknarkategoridata till en befintlig arbetsyta med namnet *myWorkspace*, som finns i en resursgrupp med namnet *myWorkspaces* och ID:t för den NSG som du hämtade tidigare:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Om du inte har en befintlig arbetsyta kan du skapa en med hjälp av [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) Det finns två typer av loggning som du kan aktivera loggar för.

Om du bara vill logga data för en kategori eller en annan tar du bort den kategori som du inte vill logga data för i föregående kommando. Om du vill logga till ett annat [mål](#log-destinations) än en Log Analytics-arbetsyta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) eller [en händelsehubb.](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)

Visa och analysera loggar. Mer information finns i Visa [och analysera loggar.](#view-and-analyze-logs)

## <a name="log-destinations"></a>Loggmål

Diagnostikdata kan vara:
- [Skrivs till ett Azure Storage konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)för granskning eller manuell granskning. Du kan ange kvarhållningstiden (i dagar) med hjälp av diagnostikinställningar för resurser.
- [Strömmas till en händelsehubb](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) för inmatning av en tjänst från tredje part eller en anpassad analyslösning, till exempel PowerBI.
- [Skrivs till Azure Monitor loggar](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Loggkategorier

JSON-formaterade data är skrivna för följande loggkategorier:

### <a name="event"></a>Händelse

Händelseloggen innehåller information om vilka NSG-regler som tillämpas på virtuella datorer, baserat på MAC-adress. Följande data loggas för varje händelse. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och MAC-adressen 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Regelräknare

Regelräknarloggen innehåller information om varje regel som tillämpas på resurser. Följande exempeldata loggas varje gång en regel tillämpas. I följande exempel loggas data för en virtuell dator med IP-adressen 192.168.1.4 och MAC-adressen 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Käll-IP-adressen för kommunikationen loggas inte. Du kan dock [aktivera NSG-flödesloggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md) för en NSG, som loggar all regelräknarinformation samt den IP-källadress som initierade kommunikationen. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Du kan analysera data med [trafikanalysfunktionerna](../network-watcher/traffic-analytics.md) i Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Information om hur du visar resursloggdata finns i [Översikt över Azure-plattformsloggar.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Om du skickar diagnostikdata till:
- **Azure Monitor loggar:** Du kan använda [nätverkssäkerhetsgruppens analyslösning](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) för att få bättre insikter. Lösningen tillhandahåller visualiseringar för NSG-regler som tillåter eller nekar trafik per MAC-adress för nätverksgränssnittet på en virtuell dator.
- **Azure Storage konto:** Data skrivs till en fil PT1H.jsen fil. Du hittar följande:
  - Händelseloggen på följande sökväg: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Logga in på följande sökväg för regelräknare: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitetsloggning.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Aktivitetsloggning är aktiverat som standard för NSG:er som skapats via azure-distributionsmodellen. Du kan ta reda på vilka åtgärder som slutfördes på NSG:er i aktivitetsloggen genom att leta efter poster som innehåller följande resurstyper:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Information om hur du loggar diagnostikinformation finns i NSG-flödesloggning för att inkludera [källans IP-adress för varje flöde.](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
