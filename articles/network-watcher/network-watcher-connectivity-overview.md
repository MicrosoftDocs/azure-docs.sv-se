---
title: Introduktion till fel sökning av Azure Network Watcher-anslutning | Microsoft Docs
description: Den här sidan innehåller en översikt över fel söknings funktionen Network Watcher anslutning
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654004"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Introduktion till anslutnings fel sökning i Azure Network Watcher

Funktionen för att felsöka anslutningar i Network Watcher ger möjlighet att kontrol lera en direkt TCP-anslutning från en virtuell dator till en virtuell dator (VM), fullständigt kvalificerat domän namn (FQDN), URI eller IPv4-adress. Nätverks scenarier är komplexa, de implementeras med hjälp av nätverks säkerhets grupper, brand väggar, användardefinierade vägar och resurser som tillhandahålls av Azure. Komplexa konfigurationer gör fel sökning av anslutnings problem. Network Watcher bidrar till att minska tiden för att hitta och identifiera anslutnings problem. Resultaten som returneras kan ge insikter om ett anslutnings problem på grund av ett plattforms-eller användar konfigurations problem. Anslutningen kan kontrol leras med [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md)och [REST API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Fel sökning av anslutning kräver att den virtuella datorn som du felsöker från har `AzureNetworkWatcherExtension` VM-tillägget installerat. För att installera tillägget på en virtuell Windows-dator går du till [azure Network Watcher agent-tillägget virtuell dator för Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och för virtuella Linux-datorer gå till [Azure Network Watcher virtuell dator tillägg för Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Tillägget krävs inte på mål slut punkten.

## <a name="response"></a>Svarsåtgärder

I följande tabell visas de egenskaper som returneras när fel sökning av anslutning har körts.

|Egenskap  |Beskrivning  |
|---------|---------|
|ConnectionStatus     | Status för anslutnings kontrollen. Det går att **komma åt** möjliga **resultat och det** kan inte användas.        |
|AvgLatencyInMs     | Genomsnittlig svars tid under anslutnings kontrollen i millisekunder. (Visas bara om kontroll status kan kontaktas)        |
|MinLatencyInMs     | Lägsta svars tid under anslutnings kontrollen i millisekunder. (Visas bara om kontroll status kan kontaktas)        |
|MaxLatencyInMs     | Högsta svars tid under anslutnings kontrollen i millisekunder. (Visas bara om kontroll status kan kontaktas)        |
|ProbesSent     | Antal avsökningar som skickats under kontrollen. Max värdet är 100.        |
|ProbesFailed     | Antal avsökningar som inte kunde kontrol leras. Max värdet är 100.        |
|Hopp     | Hopp per hopp väg från källa till mål.        |
|Hopp []. Bastyp     | Typ av resurs. Möjliga värden är **Source**, **VirtualAppliance**, **VnetLocal** och **Internet**.        |
|Hopp []. Identitet | Unikt ID för hoppet.|
|Hopp []. Adresspool | Hoppets IP-adress.|
|Hopp []. ResourceId | ResourceID för hoppet om hoppet är en Azure-resurs. Om det är en Internet resurs är ResourceID **Internet**. |
|Hopp []. NextHopIds | Den unika identifieraren för nästa hopp som tas.|
|Hopp []. Åtgärder | En samling problem som påträffades under kontrollen vid det hoppet. Om det inte fanns några problem är värdet tomt.|
|Hopp []. Problem []. Kommer | Vid det aktuella hoppet, där problemet uppstod. Möjliga värden:<br/> **Inkommande** – problem finns på länken från föregående hopp till det aktuella hoppet<br/>**Utgående** – problem är på länken från det aktuella hoppet till nästa hopp<br/>**Lokalt** problem är på det aktuella hoppet.|
|Hopp []. Problem []. Allvarlighets grad | Problemets allvarlighets grad har upptäckts. Möjliga värden är **fel** och **Varning**. |
|Hopp []. Problem []. Bastyp |Typ av problem som hittats. Möjliga värden: <br/>**Processor**<br/>**Minne**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hopp []. Problem []. Edit |Information om problemet som har hittats.|
|Hopp []. Problem []. Kontext []. nyckel |Nyckel för nyckel värdes paret som returneras.|
|Hopp []. Problem []. Kontext []. värde |Värdet för nyckel värdes paret som returnerades.|

Följande är ett exempel på ett problem som finns på ett hopp.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Fel typer

Fel sökning av anslutning returnerar fel typer om anslutningen. I följande tabell visas en lista över de aktuella fel typerna som returneras.

|Typ  |Beskrivning  |
|---------|---------|
|CPU     | Hög CPU-användning.       |
|Minne     | Hög minnes användning.       |
|GuestFirewall     | Trafiken blockeras på grund av en konfiguration av en brand vägg för virtuella datorer.        |
|DNSResolution     | DNS-matchningen misslyckades för mål adressen.        |
|NetworkSecurityRule    | Trafiken blockeras av en NSG-regel (regeln returneras)        |
|UserDefinedRoute|Trafiken bryts på grund av en användardefinierad eller system väg. |

### <a name="next-steps"></a>Nästa steg

Lär dig hur du felsöker anslutningar med hjälp av [Azure Portal](network-watcher-connectivity-portal.md), [POWERSHELL](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md)eller [REST API](network-watcher-connectivity-rest.md).