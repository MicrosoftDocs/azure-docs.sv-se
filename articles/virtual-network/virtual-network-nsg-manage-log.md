---
title: Diagnostisk resurs loggning för en nätverks säkerhets grupp
titlesuffix: Azure Virtual Network
description: Lär dig hur du aktiverar diagnostiska resurs loggar för händelse-och regel räknare för en Azure-nätverks säkerhets grupp.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: bb078b9738e995a1c507f7934a7dd64f075d5fe0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596529"
---
# <a name="resource-logging-for-a-network-security-group"></a>Resurs loggning för en nätverks säkerhets grupp

En nätverks säkerhets grupp (NSG) innehåller regler som tillåter eller nekar trafik till ett virtuellt nätverks under nät, ett nätverks gränssnitt eller både och. 

När du aktiverar loggning för en NSG kan du samla in följande typer av resurs logg information:

* **Händelse:** Poster loggas för vilka NSG-regler som tillämpas på virtuella datorer, baserat på MAC-adress.
* **Regel räknare:** Innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik. Status för dessa regler samlas in var 300: e sekund.

Resurs loggar är bara tillgängliga för NSG: er som distribueras via Azure Resource Manager distributions modell. Du kan inte aktivera resurs loggning för NSG: er som distribueras via den klassiska distributions modellen. En bättre förståelse för de två modellerna finns i [förstå Azures distributions modeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Resurs loggning aktive ras separat för *varje* NSG som du vill samla in diagnostikdata för. Om du är intresse rad av aktivitets loggar (operativa) i stället, se Azure- [aktivitets loggning](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Aktivera loggning

Du kan använda [Azure Portal](#azure-portal), [POWERSHELL](#powershell)eller [Azure CLI](#azure-cli) för att aktivera resurs loggning.

### <a name="azure-portal"></a>Azure-portalen

1. Logga in på [portalen](https://portal.azure.com).
2. Välj **alla tjänster** och skriv sedan *nätverks säkerhets grupper*. När **nätverks säkerhets grupper** visas i Sök resultaten väljer du den.
3. Välj den NSG som du vill aktivera loggning för.
4. Under **övervakning** **väljer du diagnostikloggar och** sedan **Aktivera diagnostik**, som du ser i följande bild:

   ![Slå på diagnostik](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Under **diagnostikinställningar** anger eller väljer du följande information och väljer sedan **Spara**:

    | Inställning                                                                                     | Värde                                                          |
    | ---------                                                                                   |---------                                                       |
    | Namn                                                                                        | Ett namn som du väljer.  Till exempel: *myNsgDiagnostics*      |
    | **Arkivera till ett lagrings konto**, **strömma till en Event Hub** och **Skicka till Log Analytics** | Du kan välja så många destinationer du vill. Mer information om var och en finns i [logg destinationer](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Välj antingen eller båda logg kategorierna. Mer information om de data som loggas för varje kategori finns i [logg kategorier](#log-categories).                                                                                                                                             |
6. Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/powershell)eller genom att köra PowerShell från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör PowerShell från datorn behöver du Azure PowerShell-modulen, version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att logga in på Azure med ett konto som har de behörigheter som [krävs](virtual-network-network-interface.md#permissions).

Om du vill aktivera resurs loggning behöver du ID: t för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Hämta den nätverks säkerhets grupp som du vill aktivera resurs loggning för med [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resurs grupp med namnet *myResourceGroup*, anger du följande kommando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Du kan skriva resurs loggar till tre mål typer. Mer information finns i [logg destinationer](#log-destinations). I den här artikeln skickas loggar till *Log Analytics* mål, som exempel. Hämta en befintlig Log Analytics-arbetsyta med [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Om du till exempel vill hämta en befintlig arbets yta med namnet min *arbets yta* i en resurs grupp med namnet mina *arbets ytor*, anger du följande kommando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Om du inte har en befintlig arbets yta kan du skapa en med [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Det finns två typer av loggning som du kan aktivera loggar för. Mer information finns i [logg kategorier](#log-categories). Aktivera resurs loggning för NSG med [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). I följande exempel loggas kategori data för händelse och räknare till arbets ytan för en NSG med ID: n för NSG och arbets ytan som du hämtade tidigare:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Om du bara vill logga data för en kategori eller den andra, i stället för båda, lägger du till `-Categories` alternativet i föregående kommando, följt av *NetworkSecurityGroupEvent* eller *NetworkSecurityGroupRuleCounter*. Om du vill logga till ett annat [mål](#log-destinations) än en Log Analytics arbets yta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) eller en [Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

### <a name="azure-cli"></a>Azure CLI

Du kan köra kommandona som följer i [Azure Cloud Shell](https://shell.azure.com/bash)eller genom att köra Azure CLI från datorn. Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Om du kör CLI från datorn behöver du version 2.0.38 eller senare. Kör `az --version` på datorn för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli). Om du kör CLI lokalt måste du också köra `az login` för att logga in på Azure med ett konto som har de [behörigheter som krävs](virtual-network-network-interface.md#permissions).

Om du vill aktivera resurs loggning behöver du ID: t för en befintlig NSG. Om du inte har en befintlig NSG kan du skapa en med [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create).

Hämta den nätverks säkerhets grupp som du vill aktivera resurs loggning för med [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show). Om du till exempel vill hämta en NSG med namnet *myNsg* som finns i en resurs grupp med namnet *myResourceGroup*, anger du följande kommando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Du kan skriva resurs loggar till tre mål typer. Mer information finns i [logg destinationer](#log-destinations). I den här artikeln skickas loggar till *Log Analytics* mål, som exempel. Mer information finns i [logg kategorier](#log-categories).

Aktivera resurs loggning för NSG med [AZ Monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). I följande exempel loggas kategori data för händelse och räknare till en befintlig arbets yta med namnet min *arbets yta*, som finns i en resurs grupp med namnet mina *arbets ytor* och ID: t för NSG som du hämtade tidigare:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Om du inte har en befintlig arbets yta kan du skapa en med hjälp av [Azure Portal](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Det finns två typer av loggning som du kan aktivera loggar för.

Om du bara vill logga data för en kategori eller en annan kategori tar du bort den kategori som du inte vill logga data för i föregående kommando. Om du vill logga till ett annat [mål](#log-destinations) än en Log Analytics arbets yta använder du lämpliga parametrar för ett Azure [Storage-konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) eller en [Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Visa och analysera loggar. Mer information finns i [Visa och analysera loggar](#view-and-analyze-logs).

## <a name="log-destinations"></a>Logg destinationer

Diagnostikdata kan vara:
- [Skrivs till ett Azure Storage konto](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)för granskning eller manuell kontroll. Du kan ange Retentions tiden (i dagar) med hjälp av inställningarna för resurs diagnostik.
- [Strömmas till en Event Hub](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) för inmatning av en tjänst från tredje part, eller en anpassad analys lösning, till exempel PowerBI.
- [Skrivs till Azure Monitor loggar](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).

## <a name="log-categories"></a>Logg kategorier

JSON-formaterade data skrivs för följande logg kategorier:

### <a name="event"></a>Händelse

Händelse loggen innehåller information om vilka NSG-regler som tillämpas på virtuella datorer, baserat på MAC-adress. Följande data loggas för varje händelse. I följande exempel loggas data för en virtuell dator med IP-192.168.1.4 och MAC-adressen 00-0D-3A-92-6A-7C:

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

### <a name="rule-counter"></a>Regel räknare

Regel räknar loggen innehåller information om varje regel som tillämpas på resurser. Följande exempel data loggas varje gången en regel tillämpas. I följande exempel loggas data för en virtuell dator med IP-192.168.1.4 och MAC-adressen 00-0D-3A-92-6A-7C:

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
> Källans IP-adress för kommunikationen loggas inte. Du kan aktivera [NSG flödes loggning](../network-watcher/network-watcher-nsg-flow-logging-portal.md) för en NSG men som loggar all regel räknar information, samt käll-IP-adressen som initierade kommunikationen. Loggdata från NSG-flödet skrivs till ett Azure Storage-konto. Du kan analysera data med [trafik analys](../network-watcher/traffic-analytics.md) funktionen i Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visa och analysera loggar

Information om hur du visar resurs logg data finns i [Översikt över Azure Platform-loggar](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du skickar diagnostikdata till:
- **Azure Monitor loggar**: du kan använda [nätverks säkerhets grupp analys](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) lösningen för förbättrade insikter. Lösningen innehåller visualiseringar för NSG-regler som tillåter eller nekar trafik, per MAC-adress, för nätverks gränssnittet på en virtuell dator.
- **Azure Storage konto**: data skrivs till en PT1H.jsi filen. Du kan hitta:
  - Händelse logg i följande sökväg: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Regel räknar loggen på följande sökväg: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Nästa steg

- Läs mer om [aktivitets loggning](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aktivitets loggning är aktiverat som standard för NSG: er som skapats via Azures distributions modell. För att avgöra vilka åtgärder som slutförts på NSG: er i aktivitets loggen, letar du efter poster som innehåller följande resurs typer:
  - Microsoft. ClassicNetwork/networkSecurityGroups
  - Microsoft. ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft. Network/networkSecurityGroups
  - Microsoft. Network/networkSecurityGroups/securityRules
- Information om hur du loggar diagnostikinformation för att inkludera käll-IP-adressen för varje flöde finns i [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
