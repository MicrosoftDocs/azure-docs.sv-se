---
title: Introduktion till flödesloggning för NSG:er
titleSuffix: Azure Network Watcher
description: Den här artikeln förklarar hur du använder funktionen NSG-flödesloggar i Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 206bcfaeb5cb13d3ecf1e5f6335518c42df21eb8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535289"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödesloggning för nätverkssäkerhetsgrupper

## <a name="introduction"></a>Introduktion

[Flödesloggar](../virtual-network/network-security-groups-overview.md#security-rules) för nätverkssäkerhetsgrupp (NSG) är en funktion i Azure Network Watcher som gör att du kan logga information om IP-trafik som flödar genom en NSG. Flödesdata skickas till Azure Storage där du kan komma åt dem samt exportera dem till valbara visualiseringsverktyg, SIEM eller ID:n.

![översikt över flödesloggar](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Varför ska jag använda flödesloggar?

Det är viktigt att övervaka, hantera och känna till ditt eget nätverk för att få okomprometterad säkerhet, efterlevnad och prestanda. Att känna till din egen miljö är avgörande för att skydda och optimera den. Du behöver ofta känna till nätverkets aktuella tillstånd, vem som ansluter, var de ansluter från, vilka portar som är öppna till Internet, förväntat nätverksbeteende, oregelbunden nätverksbeteende och plötslig ökning av trafik.

Flödesloggar är sanningskällan för all nätverksaktivitet i din molnmiljö. Oavsett om du är en kommande startup som försöker optimera resurser eller stora företag som försöker identifiera intrång är Flödesloggar det bästa valet. Du kan använda den för att optimera nätverksflöden, övervaka dataflöde, verifiera efterlevnad, identifiera intrång och mycket mer.

## <a name="common-use-cases"></a>Vanliga användarsituationer

**Nätverksövervakning:** Identifiera okänd eller oönskad trafik. Övervaka trafiknivåer och bandbreddsförbrukning. Filtrera flödesloggar efter IP och port för att förstå programmets beteende. Exportera flödesloggar till valfria analys- och visualiseringsverktyg för att konfigurera instrumentpaneler för övervakning.

**Övervakning och optimering av användning:** Identifiera de främsta talarna i nätverket. Kombinera med GeoIP-data för att identifiera trafik mellan regioner. Förstå trafiktillväxt för kapacitetsprognoser. Använd data för att ta bort alltför restriktiva trafikregler.

**Efterlevnad:** Använd flödesdata för att verifiera nätverksisolering och efterlevnad med åtkomstregler för företag

**Nätverkssäkerhetsanalyser & säkerhetsanalys:** Analysera nätverksflöden från komprometterade IP-adresser och nätverksgränssnitt. Exportera flödesloggar till valbara SIEM- eller IDS-verktyg.

## <a name="how-logging-works"></a>Så här fungerar loggning

**Nyckelegenskaper**

- Flödesloggar fungerar [på Layer 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) och registrerar alla IP-flöden som går in och ut från en NSG
- Loggar samlas in med ett intervall på 1 minut via **Azure-plattformen** och påverkar inte kundresurser eller nätverksprestanda på något sätt.
- Loggar skrivs i JSON-format och visar utgående och inkommande flöden per NSG-regel.
- Varje loggpost innehåller det nätverksgränssnitt (NIC) som flödet gäller för, 5-tuppelinformation, dataflödesinformation för trafikbeslut & (endast version 2). Fullständig _information finns_ i Loggformat nedan.
- Flödesloggar har en kvarhållningsfunktion som gör att loggarna kan tas bort automatiskt upp till ett år efter att de har skapats. 

> [!NOTE]
> Kvarhållning är bara tillgängligt om du använder [GPv2-konton (Generell användning v2).](../storage/common/storage-account-overview.md#types-of-storage-accounts) 

**Huvudkoncept**

- Programvarudefinierade nätverk organiseras runt virtuella nätverk (VNET) och undernät. Säkerheten för dessa virtuella nätverk och undernät kan hanteras med hjälp av en NSG.
- En nätverkssäkerhetsgrupp (NSG) innehåller  en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik i resurser som den är ansluten till. NSG:er kan associeras med undernät, enskilda virtuella datorer eller enskilda nätverksgränssnitt (NIC) som är kopplade till virtuella datorer (Resource Manager). Mer information finns i Översikt [över nätverkssäkerhetsgrupp.](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- Alla trafikflöden i nätverket utvärderas med hjälp av reglerna i tillämplig NSG.
- Resultatet av dessa utvärderingar är NSG-flödesloggar. Flödesloggar samlas in via Azure-plattformen och kräver inte några ändringar i kundresurserna.
- Obs! Regler är av två typer – avslutande & icke-avslutande, var och en med olika loggningsbeteenden.
- - NSG-neka-regler avslutas. Den NSG som nekar trafiken loggar den i flödesloggar och bearbetningen i det här fallet stoppas när NSG nekar trafik. 
- - NSG Tillåt-regler är icke-avslutande, vilket innebär att även om en NSG tillåter det, fortsätter bearbetningen till nästa NSG. Den senaste NSG:n som tillåter trafik loggar trafiken till Flödesloggar.
- NSG-flödesloggar skrivs till lagringskonton som de kan nås från.
- Du kan exportera, bearbeta, analysera och visualisera flödesloggar med verktyg som TA, Splunk, Grafana, Stealthwatch osv.

## <a name="log-format"></a>Loggformat

Flödesloggar innehåller följande egenskaper:

* **tid** – Tid då händelsen loggades
* **systemId –** System-ID för nätverkssäkerhetsgrupp.
* **category** – Händelsens kategori. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **resourceid** – NSG:ns resurs-ID
* **operationName** – Always NetworkSecurityGroupFlowEvents
* **egenskaper** – en samling egenskaper för flödet
    * **Version** – Versionsnumret för händelseschemat för flödesloggen
    * **flows** – En samling flöden. Den här egenskapen har flera poster för olika regler
        * **regel** – Regel som flödena listas för
            * **flows** – en samling flöden
                * **mac** – MAC-adressen för nätverkskortet för den virtuella dator där flödet samlades in
                * **flowTuples** – En sträng som innehåller flera egenskaper för flödestuppeln i kommaavgränsat format
                    * **Tidsstämpel** – det här värdet är tidsstämpeln för när flödet inträffade i UNIX-epokformat
                    * **Käll-IP** – käll-IP
                    * **Mål-IP** – mål-IP
                    * **Källport** – källporten
                    * **Målport** – målporten
                    * **Protokoll** – protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Trafikflöde** – Trafikflödets riktning. Giltiga värden är **I** för inkommande och **O** för utgående.
                    * **Trafikbeslut** – Huruvida trafik tilläts eller nekades. Giltiga värden är **A för** tillåten och **D för** nekad.
                    * **Flödestillstånd – endast version 2** – Avbildar flödets tillstånd. Möjliga tillstånd är **B**: Börja, när ett flöde skapas. Statistik tillhandahålls inte. **C**: Fortsätter (Continuing) för en pågående flöde. Statistik tillhandahålls med 5 minuters mellanrum. **E**: Slutet (End), när ett flöde avslutas. Statistik tillhandahålls.
                    * **Paket – Källa till mål – endast version 2** Det totala antalet TCP- eller UDP-paket som skickats från källa till mål sedan den senaste uppdateringen.
                    * **Skickade byte – källa till mål – endast version 2** Det totala antalet TCP- eller UDP-paketbyte som skickats från källa till mål sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.
                    * **Paket – mål till källa – endast version 2** Det totala antalet TCP- eller UDP-paket som skickats från mål till källa sedan den senaste uppdateringen.
                    * **Skickade byte – mål till källa – endast version 2** Det totala antalet TCP- och UDP-paketbyte som skickats från mål till källa sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.


**NSG-flödesloggar, version 2 (jämfört med version 1)** 

Version 2 av loggarna introducerar begreppet flödestillstånd. Du kan konfigurera vilken version av flödesloggar som du får.

_Flödestillstånd B_ registreras när ett flöde initieras. _Flödestillstånd C_ och _flödestillstånd E_ är tillstånd som markerar fortsättningen av ett flöde respektive flödesavslut. Både _C-_ och _E-tillstånd_ innehåller information om trafikbandbredd.

### <a name="sample-log-records"></a>Exempelloggposter

Följande text är ett exempel på en flödeslogg. Som du ser finns det flera poster som följer egenskapslistan som beskrivs i föregående avsnitt.

> [!NOTE]
> Värden i egenskapen *flowTuples* är en kommaavgränsad lista.
 
**Exempel på NSG-flödesloggformat för version 1**
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        
        
```
**Exempel på NSG-flödesloggformat för version 2**
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        }
        
```
**Förklaring av loggtuppeln**

![flödesloggstuppeln](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Exempel på bandbreddsberäkning**

Flödestupplar från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

För _fortsättnings-C-_ _och E-slutflödes_ tillstånd är byte- och paketantal aggregerade från tiden för den tidigare flödestuppeln. När vi refererar till föregående exempelkonversation är det totala antalet överförda paket 1021+52+8005+47 = 9125. Det totala antalet överförda byte är 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Aktivera NSG-flödesloggar

Använd relevant länk nedan för guider om hur du aktiverar flödesloggar.

- [Azure-portalen](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Uppdatera parametrar

**Azure-portalen**

På sidan Azure Portal du till avsnittet NSG-flödesloggar i Network Watcher. Klicka sedan på namnet på NSG:n. Då visas inställningsfönstret för Flödesloggen. Ändra de parametrar som du vill använda och tryck **på Spara** för att distribuera ändringarna.

**PS/CLI/REST/ARM**

Om du vill uppdatera parametrar via kommandoradsverktyg använder du samma kommando som används för att aktivera flödesloggar (från ovan) men med uppdaterade parametrar som du vill ändra.

## <a name="working-with-flow-logs"></a>Arbeta med Flödesloggar

*Läsa och exportera flödesloggar*

- [Ladda &amp; ned visa flödesloggar från portalen](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Läsa flödesloggar med PowerShell-funktioner](./network-watcher-read-nsg-flow-logs.md)
- [Exportera NSG-flödesloggar till Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Flödesloggar är mål för NSG:er, men de visas inte på samma sätt som de andra loggarna. Flödesloggar lagras bara i ett lagringskonto och följer loggningssökvägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualisera flödesloggar*

- [Azure Traffic Analytics är](./traffic-analytics.md) en inbyggd Azure-tjänst för att bearbeta flödesloggar, extrahera insikter och visualisera flödesloggar. 
- [[Självstudie] Visualisera NSG-flödesloggar med Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Självstudie] Visualisera NSG-flödesloggar med Elastic Stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Självstudie] Hantera och analysera NSG-flödesloggar med Grafana](./network-watcher-nsg-grafana.md)
- [[Självstudie] Hantera och analysera NSG-flödesloggar med Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)

*Inaktivera flödesloggar*

När flödesloggen är inaktiverad stoppas flödesloggningen för associerad NSG. Men flödesloggen som en resurs finns kvar med alla dess inställningar och associationer. Den kan aktiveras när som helst för att påbörja flödesloggning på den konfigurerade NSG:n. Anvisningar för att inaktivera/aktivera flödesloggar finns i den [här guiden.](./network-watcher-nsg-flow-logging-powershell.md)  

*Ta bort flödesloggar*

När flödesloggen tas bort stoppas inte bara flödesloggningen för den associerade NSG:n, utan även flödesloggresursen tas bort med dess inställningar och associationer. Om du vill börja flödesloggningen igen måste en ny flödesloggresurs skapas för den NSG:n. En flödeslogg kan tas bort med [PowerShell,](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkwatcherflowlog) [CLI](https://docs.microsoft.com/cli/azure/network/watcher/flow-log#az_network_watcher_flow_log_delete) [eller REST API](https://docs.microsoft.com/rest/api/network-watcher/flowlogs/delete). Stödet för att ta bort flödesloggar från Azure Portal är i pipeline.    

När en NSG tas bort tas dessutom den associerade flödesloggresursen bort som standard.

> [!NOTE]
> Om du vill flytta en NSG till en annan resursgrupp eller prenumeration måste de associerade flödesloggarna tas bort, men det går inte att inaktivera flödesloggarna. Efter migreringen av NSG måste flödesloggarna återskapas för att aktivera flödesloggning på den.  

## <a name="nsg-flow-logging-considerations"></a>Överväganden för NSG-flödesloggning

**Överväganden för lagringskonto:** 

- Plats: Det lagringskonto som används måste finnas i samma region som NSG.
- Prestandanivå: För närvarande stöds endast lagringskonton på standardnivå.
- Självhantera nyckelrotation: Om du ändrar/roterar åtkomstnycklarna till ditt lagringskonto slutar NSG-flödesloggar att fungera. För att åtgärda det här problemet måste du inaktivera och sedan återaktivera NSG-flödesloggar.

**Kostnader för flödesloggning:** NSG-flödesloggning debiteras på den volym loggar som skapas. Hög trafikvolym kan resultera i stora flödesloggvolymer och tillhörande kostnader. Prissättningen för NSG-flödesloggar inkluderar inte de underliggande kostnaderna för lagring. Användning av funktionen för bevarandeprincip med NSG-flödesloggning innebär separata lagringskostnader under längre tidsperioder. Om du vill behålla data för alltid och inte vill tillämpa någon kvarhållningsprincip anger du kvarhållning (dagar) till 0. Mer information finns i [Network Watcher och](https://azure.microsoft.com/pricing/details/network-watcher/) [Azure Storage prisinformation.](https://azure.microsoft.com/pricing/details/storage/)

**Problem med användardefinierade inkommande TCP-regler:** [Nätverkssäkerhetsgrupper (NSG:er)](../virtual-network/network-security-groups-overview.md) implementeras som en [tillståndsful brandvägg.](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) På grund av aktuella plattformsbegränsningar implementeras dock användardefinierade regler som påverkar inkommande TCP-flöden på ett tillståndslöst sätt. På grund av detta blir flöden som påverkas av användardefinierade regler för inkommande trafik icke-avslutande. Dessutom registreras inte byte- och paketantal för dessa flöden. Därför kan antalet byte och paket som rapporteras i NSG-flödesloggar (och Trafikanalys) vara annorlunda än faktiska tal. En flagga för att anmäla sig som åtgärdar dessa problem är schemalagd att vara tillgänglig senast i mars 2021. Under tiden kan kunder som har allvarliga problem på grund av det här beteendet begära att få delta via support. Skapa en supportbegäran under Network Watcher > NSG-flödesloggar.  

Inkommande flöden som loggas från **Internet-IP-adresser** till virtuella datorer utan offentliga IP-adresser: Virtuella datorer som inte har en offentlig IP-adress tilldelad via en offentlig IP-adress som är associerad med nätverkskortet som en offentlig IP-adress på instansnivå, eller som ingår i en grundläggande lastbalanseringsdelspool, använder [standard-SNAT](../load-balancer/load-balancer-outbound-connections.md) och har en IP-adress tilldelad av Azure för att underlätta utgående anslutningar. Därför kan du se flödesloggposter för flöden från Internet-IP-adresser, om flödet är avsett för en port i intervallet med portar som tilldelats för SNAT. Azure tillåter inte dessa flöden till den virtuella datorn, men försöket loggas och visas i Network Watcher NSG-flödesloggen som design. Vi rekommenderar att oönskad inkommande Internettrafik uttryckligen blockeras med NSG.

**Problem med Application Gateway NSG** för V2-undernätet: Flödesloggning på programgatewayens V2-undernäts-NSG [stöds inte för](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) närvarande. Det här problemet påverkar inte Application Gateway V1.

**Inkompatibla** tjänster: På grund av aktuella plattformsbegränsningar stöds inte en liten uppsättning Azure-tjänster av NSG-flödesloggar. Den aktuella listan över inkompatibla tjänster är
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Bästa praxis

**Aktivera på kritiska virtuella nätverk/undernät: Flödesloggar** ska aktiveras på alla kritiska virtuella nätverk/undernät i din prenumeration som bästa praxis för granskning och säkerhet. 

**Aktivera NSG-flödesloggning** på alla NSG:er som är kopplade till en resurs: Flödesloggning i Azure har konfigurerats på NSG-resursen. Ett flöde associeras endast med en NSG-regel. I scenarier där kunden använder flera NSG:er rekommenderar vi att du aktiverar NSG-flödesloggar för alla NSG:er som används i resursens undernät eller nätverksgränssnitt för att säkerställa att all trafik registreras. Mer information finns i [hur trafik utvärderas i](../virtual-network/network-security-group-how-it-works.md) nätverkssäkerhetsgrupper. 

Några vanliga scenarier:
1. **Flera nätverkskort på en virtuell dator:** Om flera nätverkskort är anslutna till en virtuell dator måste flödesloggning vara aktiverat på alla
1. **Ha NSG** på både NIC- och undernätsnivå: Om NSG har konfigurerats på nätverkskortet och undernätsnivå måste flödesloggning vara aktiverat på båda NSG:erna. 

**Lagringsetablering:** Lagringen ska etableras i enlighet med förväntad flödesloggvolym.

**Namngivning:** NSG-namnet måste vara upp till 80 tecken och NSG-regelnamnen upp till 65 tecken. Om namnen överskrider teckengränsen kan det trunkeras under loggning.

## <a name="troubleshooting-common-issues"></a>Felsöka vanliga problem

**Jag kunde inte aktivera NSG-flödesloggar**

- **Resursprovidern Microsoft.Insights** har inte registrerats

Om du fick felet _AuthorizationFailed_ eller _GatewayAuthenticationFailed_ har du kanske inte aktiverat resursprovidern Microsoft Insights i din prenumeration. [Följ instruktionerna för](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) att aktivera Microsoft Insights-providern.

**Jag har aktiverat NSG-flödesloggar men ser inte data i mitt lagringskonto**

- **Installationstid**

Det kan ta upp till 5 minuter innan du ser NSG-flödesloggar på ditt lagringskonto (om det är konfigurerat på rätt sätt). Du ser filen PT1H.json, och [här beskrivs hur du öppnar den](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Ingen trafik på din NSG:er**

Ibland visas inte loggar om dina virtuella datorer inte är aktiva eller om det finns överordnade filter på en App Gateway eller andra enheter som blockerar trafiken till dina NSG:er.

**Jag vill automatisera NSG-flödesloggar**

Stöd för automatisering via ARM-mallar är för närvarande inte tillgängligt för NSG-flödesloggar. Mer information finns i [informationsmeddelandet för funktionen](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/).

## <a name="faq"></a>Vanliga frågor

**Vad gör NSG-flödesloggar?**

Azure-nätverksresurser kan kombineras och hanteras via [nätverkssäkerhetsgrupper (NSG:er).](../virtual-network/network-security-groups-overview.md) Med NSG-flödesloggar kan du logga 5-tuppelflödesinformation om all trafik via dina NSG:er. Råflödesloggarna skrivs till ett Azure Storage-konto där de kan bearbetas ytterligare, analyseras, efterfrågas eller exporteras efter behov.

**Påverkar användning av flödesloggar svarstiden eller prestandan i nätverket?**

Flödesloggdata samlas in utanför sökvägen till nätverkstrafiken och påverkar därför inte nätverkets dataflöde eller svarstid. Du kan skapa eller ta bort flödesloggar utan att påverka nätverkets prestanda.

**Hur gör jag för att NSG-flödesloggar med ett lagringskonto bakom en brandvägg?**

Om du vill använda ett lagringskonto bakom en brandvägg måste du ange ett undantag för betrodda Microsoft-tjänster för att komma åt ditt lagringskonto:

- Navigera till lagringskontot genom att skriva lagringskontots namn i global sökning på portalen eller från [sidan Lagringskonton](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Under avsnittet  **INSTÄLLNINGAR**  väljer du  **Brandväggar och virtuella nätverk**
- I **Tillåt åtkomst från** väljer du Valda  **nätverk**. Under Undantag  **markerar** du sedan rutan bredvid ***Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot***
- Om det redan är valt behövs ingen ändring.
- Leta upp din mål-NSG på översiktssidan [för NSG-flödesloggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) och aktivera NSG-flödesloggar med ovanstående lagringskonto valt.

Du kan kontrollera lagringsloggarna efter några minuter och bör se en uppdaterad tidsstämpel eller en ny JSON-fil som skapats.

**Hur gör jag för att NSG-flödesloggar med ett lagringskonto bakom en tjänstslutpunkt?**

NSG-flödesloggar är kompatibla med tjänstslutpunkter utan att det krävs någon extra konfiguration. Se [självstudien om hur du aktiverar tjänstslutpunkter](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) i ditt virtuella nätverk.

**Vad är skillnaden mellan flödesloggversion 1 & 2?**

Flödesloggar version 2 introducerar begreppet _flödestillstånd för_ & lagrar information om byte och paket som överförs. [Läs mer](#log-format)

## <a name="pricing"></a>Priser

NSG-flödesloggar debiteras per GB loggar som samlas in och har en kostnadsfri nivå på 5 GB/månad per prenumeration. Aktuella priser i din region finns på Network Watcher [sidan med priser.](https://azure.microsoft.com/pricing/details/network-watcher/)

Lagring av loggar debiteras separat. Information om [relevanta priser finns Azure Storage sidan med](https://azure.microsoft.com/pricing/details/storage/blobs/) priser för blockblobar.