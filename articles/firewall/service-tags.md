---
title: Översikt över Azure Firewall tjänsttaggar
description: En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529555"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall tjänsttaggar

En tjänsttagg representerar en grupp IP-adressprefix och används i syfte att minska komplexiteten vid skapande av säkerhetsregler. Du kan inte skapa egna tjänsttaggar, och du kan inte heller ange vilka IP-adresser som ska finnas i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

Azure Firewall tjänsttaggar kan användas i målfältet för nätverksregler. Du kan använda dem i stället för specifika IP-adresser.

## <a name="supported-service-tags"></a>Tjänsttaggar som stöds

En [lista över tjänsttaggar](../virtual-network/service-tags-overview.md#available-service-tags) som är tillgängliga för användning i Azure Firewall-nätverksregler finns i Tjänsttaggar för virtuellt nätverk.

## <a name="configuration"></a>Konfiguration

Azure Firewall har stöd för konfiguration av tjänsttaggar via PowerShell, Azure CLI eller Azure Portal.

### <a name="configure-via-azure-powershell"></a>Konfigurera via Azure PowerShell

I det här exemplet måste vi först få kontext till den tidigare skapade Azure Firewall instansen.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Därefter måste vi skapa en ny regel.  För Källa eller Mål kan du ange textvärdet för den tjänsttagg som du vill använda, som nämnts ovan i den här artikeln.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Därefter måste vi uppdatera variabeln som innehåller vår Azure Firewall definition med de nya nätverksregler som vi skapade.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Till sist måste vi genomföra ändringar i nätverksregeln till den Azure Firewall instansen.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Firewall finns i Azure Firewall [om regelbearbetningslogik.](rule-processing.md)
