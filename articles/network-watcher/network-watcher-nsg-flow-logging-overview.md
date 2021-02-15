---
title: 'Introduktion till flödes loggning för NSG: er'
titleSuffix: Azure Network Watcher
description: Den här artikeln förklarar hur du använder funktionen NSG Flow-loggar i Azure Network Watcher.
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
ms.openlocfilehash: 2222c6b020f712282a78ac5f82a87015d4cd86a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368208"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introduktion till flödesloggning för nätverkssäkerhetsgrupper

## <a name="introduction"></a>Introduktion

Flödes loggar för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) är en funktion i Azure Network Watcher som gör att du kan logga information om IP-trafik som flödar genom en NSG. Flow-data skickas till Azure Storage konton från vilka du kan komma åt dem samt exportera dem till alla visualiserings verktyg, SIEM eller ID: n som du väljer.

![Översikt över flödes loggar](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Varför ska jag använda flödes loggar?

Det är viktigt att övervaka, hantera och känna till ditt eget nätverk för kompromisslös säkerhet, efterlevnad och prestanda. Att känna till din egen miljö är av största vikt för att skydda och optimera den. Du behöver ofta känna till det aktuella läget för nätverket, som ansluter, var de ansluter från, vilka portar som är öppna för Internet, förväntade nätverks beteende, oregelbunden nätverks beteende och plötsliga ökningar i trafiken.

Flödes loggar är källan till sanningen för all nätverks aktivitet i din moln miljö. Oavsett om du är en kommande start försöker du optimera resurser eller ett stort företag försöker identifiera intrång, flödes loggar är ditt bästa val. Du kan använda den för att optimera nätverks flöden, övervaka data flöde, kontrol lera efterlevnad, identifiera intrång med mera.

## <a name="common-use-cases"></a>Vanliga användarsituationer

**Nätverks övervakning**: identifiera okänd eller oönskad trafik. Övervaka trafik nivåer och bandbredds användning. Filtrera flödes loggar efter IP och port för att förstå program beteendet. Exportera flödes loggar till analys-och visualiserings verktyg som du väljer för att ställa in övervaknings instrument paneler.

**Användnings övervakning och optimering:** Identifiera de främsta pratarna i nätverket. Kombinera med GeoIP-data för att identifiera trafik över flera regioner. Förstå trafik tillväxten för kapacitets prognoser. Använd data för att ta bort Overtly restriktiva trafik regler.

**Kompatibilitet**: Använd Flow-data för att kontrol lera nätverks isolering och efterlevnad med regler för företags åtkomst

**Network data utredning & Security Analysis**: analysera nätverks flöden från komprometterade IP-adresser och nätverks gränssnitt. Exportera flödes loggar till valfritt SIEM-eller ID-verktyg som du själv väljer.

## <a name="how-logging-works"></a>Så här fungerar loggning

**Nyckel egenskaper**

- Flödes loggar körs på [nivå 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) och spelar in alla IP-flöden som går in och ut ur en NSG
- Loggar samlas in med **1-minuters intervall** via Azure-plattformen och påverkar inte kund resurser eller nätverks prestanda på något sätt.
- Loggarna skrivs i JSON-format och visar utgående och inkommande flöden per NSG-regel.
- Varje loggpost innehåller det nätverks gränssnitt (NIC) som flödet avser, 5-tuple-information, trafik beslut & (endast version 2) data flödes information. Se _logg formatet_ nedan för fullständig information.
- Flödes loggar har en kvarhållning som gör det möjligt att automatiskt ta bort loggar upp till ett år efter att de skapats. 

> [!NOTE]
> Kvarhållning är bara tillgängligt om du använder [generella lagrings konton för användning v2 (GPv2)](../storage/common/storage-account-overview.md#types-of-storage-accounts). 

**Huvudkoncept**

- Programdefinierade nätverk ordnas runt virtuella nätverk (virtuella nätverk) och undernät. Säkerheten för dessa virtuella nätverk och undernät kan hanteras med hjälp av en NSG.
- En nätverks säkerhets grupp (NSG) innehåller en lista över _säkerhets regler_ som tillåter eller nekar nätverks trafik i resurser som den är ansluten till. NSG: er kan kopplas till undernät, enskilda virtuella datorer eller enskilda nätverks gränssnitt (NIC) som är anslutna till virtuella datorer (Resource Manager). Mer information finns i [Översikt över nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Alla trafikflöden i nätverket utvärderas med hjälp av reglerna i tillämpliga NSG.
- Resultatet av dessa utvärderingar är NSG Flow-loggar. Flödes loggar samlas in via Azure-plattformen och kräver ingen ändring av kund resurserna.
- Obs! regler är av två typer – avslutande & icke-avslutande, var och en med olika loggnings beteenden.
- - NSG neka-regler avslutas. NSG som nekar trafiken kommer att loggas i flödes loggar och bearbetning i det här fallet stoppas efter att en NSG nekar trafik. 
- - NSG Tillåt-regler är icke-avslutande, vilket innebär att även om en NSG tillåter det, fortsätter bearbetningen till nästa NSG. Den senaste NSG som tillåter trafik kommer att logga trafiken till flödes loggar.
- NSG flödes loggar skrivs till lagrings konton där de kan nås.
- Du kan exportera, bearbeta, analysera och visualisera flödes loggar med verktyg som TA, Splunk, Grafana, Stealthwatch osv.

## <a name="log-format"></a>Logg format

Flödes loggar innehåller följande egenskaper:

* **tid** när händelsen loggades
* **systemId** -nätverks säkerhets grupp system-ID.
* **kategori** – händelsens kategori. Kategorin är alltid **NetworkSecurityGroupFlowEvent**
* **ResourceID** -resurs-ID för NSG
* **operationName** -Always NetworkSecurityGroupFlowEvents
* **Egenskaper** -en samling av flödets egenskaper
    * **Version** – versions nummer för flödes loggens händelse schema
    * **flöden** – en samling flöden. Den här egenskapen har flera poster för olika regler
        * **regel** – regel för vilken flödena visas
            * **flöden** – en samling flöden
                * **Mac** – Mac-adressen för nätverkskortet för den virtuella dator där flödet samlades in
                * **flowTuples** – en sträng som innehåller flera egenskaper för flödes tuppeln i kommaavgränsat format
                    * **Tidsstämpel** – det här värdet är tidsstämpeln för när flödet inträffade i formatet UNIX-epok
                    * **Käll-IP** – käll-IP
                    * **Mål-IP** -mål-IP
                    * **Källport** – käll porten
                    * **Målport** – mål porten
                    * **Protokoll** – protokollet för flödet. Giltiga värden är **T** för TCP och **U** för UDP
                    * **Trafikflöde** – riktningen för trafikflödet. Giltiga värden är **i** för inkommande och **O** för utgående trafik.
                    * **Trafik beslut** – huruvida trafik tillåts eller nekas. Giltiga värden är **A** för tillåtna och **D** för nekad.
                    * **Flödes tillstånd-version 2 endast** – fångar in flödets tillstånd. Möjliga tillstånd är **B**: Börja, när ett flöde skapas. Statistik tillhandahålls inte. **C**: Fortsätter (Continuing) för en pågående flöde. Statistik tillhandahålls med 5 minuters mellanrum. **E**: Slutet (End), när ett flöde avslutas. Statistik tillhandahålls.
                    * **Paket-källa till mål-version 2** Det totala antalet TCP-eller UDP-paket som har skickats från källan till målet sedan den senaste uppdateringen.
                    * **Skickade byte-källa till mål version 2** Det totala antalet TCP-eller UDP-paketfilter som har skickats från källan till målet sedan den senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.
                    * **Paket-mål till källa-endast version 2** Det totala antalet TCP-eller UDP-paket som har skickats från målet till källan sedan senaste uppdateringen.
                    * **Skickade byte – målet till källa-version 2** Det totala antalet byte för TCP-och UDP-paket som skickats från målet till källan sedan senaste uppdateringen. Paketbyte omfattar paketets huvud och nyttolast.


**NSG Flow-loggar version 2 (vs version 1)** 

Version 2 av loggarna introducerar konceptet flödes tillstånd. Du kan konfigurera vilken version av flödes loggar du får.

Flödets tillstånd _B_ registreras när ett flöde initieras. Flow State _C_ och Flow State _E_ är lägen som markerar fortsättningen för en flödes-och flödes avslutning. Både _C_ och _E_ -tillstånd innehåller bandbredds information.

### <a name="sample-log-records"></a>Exempel på logg poster

Texten som följer är ett exempel på en flödes logg. Som du kan se finns det flera poster som följer egenskaps listan som beskrivs i föregående avsnitt.

> [!NOTE]
> Värdena i egenskapen *flowTuples* är en kommaavgränsad lista.
 
**Version 1 NSG flödes logg format exempel**
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
**Version 2 NSG flöde logg format exempel**
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
**Förklarad logg tupel**

![tupel för flödes loggar](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Exempel på bandbredds beräkning**

Flow-tupler från en TCP-konversation mellan 185.170.185.105:35370 och 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4, 35370, 23, T, A, E, 52, 29952, 47, 27072"

För tilläggs- _C_ -och slut- _E_ -flöde-tillstånd är antalet byte och paket sammanställda från tiden för den föregående flödes tupel posten. Referens till föregående exempel konversation, det totala antalet överförda paket är 1021 + 52 + 8005 + 47 = 9125. Det totala antalet överförda byte är 588096 + 29952 + 4610880 + 27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Aktivera NSG-flödesloggar

Använd den relevanta länken nedan om du vill ha hjälp med att aktivera flödes loggar.

- [Azure-portalen](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [CLI](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Uppdaterar parametrar

**Azure-portalen**

På Azure Portal går du till avsnittet NSG Flow-loggar i Network Watcher. Klicka sedan på namnet på NSG. Då öppnas fönstret inställningar för flödes loggen. Ändra de parametrar som du vill ha och tryck på **Spara** för att distribuera ändringarna.

**PS/CLI/REST/ARM**

Om du vill uppdatera parametrar via kommando rads verktyg använder du samma kommando som används för att aktivera flödes loggar (från ovan), men med uppdaterade parametrar som du vill ändra.

## <a name="working-with-flow-logs"></a>Arbeta med flödes loggar

*Läs-och export flödes loggar*

- [Ladda ned &amp; Visa flödes loggar från portalen](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Läs flödes loggar med PowerShell Functions](./network-watcher-read-nsg-flow-logs.md)
- [Exportera NSG Flow-loggar till Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Vid flödes loggningens mål NSG: er, visas de inte på samma sätt som de andra loggarna. Flödes loggar lagras bara inom ett lagrings konto och följer loggnings Sök vägen som visas i följande exempel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualisera flödes loggar*

- [Azure Traffic Analytics](./traffic-analytics.md) är en Azure Native-tjänst för att bearbeta flödes loggar, extrahera insikter och visualisera flödes loggar. 
- [Gång Visualisera NSG flödes loggar med Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Gång Visualisera NSG flödes loggar med elastisk stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Gång Hantera och analysera NSG Flow-loggar med Grafana](./network-watcher-nsg-grafana.md)
- [Gång Hantera och analysera NSG Flow-loggar med Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>NSG flödes loggnings överväganden

**Överväganden för lagrings konto**: 

- Plats: det lagrings konto som används måste finnas i samma region som NSG.
- Prestanda nivå: för närvarande stöds endast lagrings konton på standard nivån.
- Själv hantering av nyckel rotation: om du ändrar/roterar åtkomst nycklarna till ditt lagrings konto slutar NSG Flow-loggar att fungera. För att åtgärda det här problemet måste du inaktivera och sedan återaktivera NSG Flow-loggar.

**Flödes loggnings kostnader**: NSG flödes loggning debiteras på volymen av loggar som skapats. Hög trafik volym kan resultera i stor flödes logg volym och tillhör ande kostnader. NSG Flow logg priser omfattar inte de underliggande lagrings kostnaderna. Om du använder funktionen bevarande princip med NSG flödes loggning innebär det att det uppstår separata lagrings kostnader för längre tids perioder. Om du inte behöver funktionen bevarande princip rekommenderar vi att du anger värdet till 0. Mer information finns i [Network Watcher priser](https://azure.microsoft.com/pricing/details/network-watcher/) och [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/) för ytterligare information.

**Problem med användardefinierade inkommande TCP-regler**: [nätverks säkerhets grupper (NSG: er)](../virtual-network/network-security-groups-overview.md) implementeras som en [tillstånds känslig brand vägg](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). På grund av aktuella plattforms begränsningar implementeras dock användardefinierade regler som påverkar inkommande TCP-flöden i ett tillstånds lös läge. På grund av detta blir flöden som påverkas av användardefinierade regler för inkommande trafik som inte avslutas. Dessutom registreras inte byte och antal paket för dessa flöden. Därför kan antalet byte och paket som rapporter ATS i NSG Flow-loggar (och Trafikanalys) skilja sig från de faktiska talen. En valbar flagga som åtgärdar de här problemen är schemalagda att vara tillgängliga senast den senaste mars 2021. Kunder som drabbas av allvarliga problem på grund av det här beteendet kan begära väljer via support, ge en supportbegäran under Network Watcher > flödes loggar.  

**Inkommande flöden som loggats från Internet-IP-adresser till virtuella datorer utan offentliga** IP-adresser: virtuella datorer som inte har en offentlig IP-adress som tilldelats via en offentlig IP-adress som är kopplad till nätverkskortet som en offentlig IP-adress på instans nivå eller som är en del av en grundläggande belastningsutjämnare, använder [standard SNAT](../load-balancer/load-balancer-outbound-connections.md) och har en IP-adress som tilldelats av Azure för att under lätta Därför kan du se flödes logg poster för flöden från Internet-IP-adresser, om flödet är avsett för en port i intervallet för de portar som tilldelats för SNAT. Även om Azure inte tillåter dessa flöden till den virtuella datorn, loggas försöket och visas i Network Watcher flödes loggen för NSG efter design. Vi rekommenderar att oönskad inkommande Internet trafik uttryckligen blockeras med NSG.

**Problem med Application Gateway v2-UNDERNÄT NSG**: flödes loggning på undernät för Application Gateway v2-NSG [stöds inte](../application-gateway/application-gateway-faq.yml#are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet) för närvarande. Det här problemet påverkar inte Application Gateway v1.

**Inkompatibla tjänster**: på grund av aktuella plattforms begränsningar stöds inte en liten uppsättning Azure-tjänster av NSG Flow-loggar. Den aktuella listan över inkompatibla tjänster är
- [Azure Kubernetes Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logic Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Bästa praxis

**Aktivera för kritiska virtuella nätverk/undernät**: flödes loggar ska aktive ras på alla kritiska virtuella nätverk/undernät i din prenumeration som bästa praxis för granskning och säkerhet. 

**Aktivera NSG flödes loggning på alla NSG: er som är kopplade till en resurs**: flödes loggning i Azure konfigureras på NSG-resursen. Ett flöde kommer bara att associeras med en NSG-regel. I scenarier där flera NSG: er används rekommenderar vi att du aktiverar NSG flödes loggar på alla NSG: er som tillämpas på resursens undernät eller nätverks gränssnitt för att säkerställa att all trafik registreras. Mer information finns i [hur trafiken utvärderas](../virtual-network/network-security-group-how-it-works.md) i nätverks säkerhets grupper. 

Några vanliga scenarier:
1. **Flera nätverkskort på en virtuell dator**: om flera nätverkskort är kopplade till en virtuell dator måste flödes loggning vara aktiverat på alla
1. **Ha NSG på både NIC-och under näts nivå**: om NSG har kon figurer ATS på nätverkskortet samt under näts nivån måste flödes loggning aktive ras på både NSG: er. 

**Lagrings etablering**: lagringen bör tillhandahållas i finjustera med den förväntade flödes logg volymen.

**Namn**: namnet på NSG måste vara upp till 80 tecken och NSG-regel namnen får innehålla mer än 65 tecken. Om namnen överskrider tecken gränsen kan de trunkeras vid loggning.

## <a name="troubleshooting-common-issues"></a>Felsöka vanliga problem

**Jag kunde inte aktivera NSG-flödesloggar**

- **Microsoft. Insights** -resurs leverantören har inte registrerats

Om du fick felet _AuthorizationFailed_ eller _GatewayAuthenticationFailed_ har du kanske inte aktiverat resursprovidern Microsoft Insights i din prenumeration. [Följ instruktionerna](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider) för att aktivera Microsoft Insights-providern.

**Jag har aktiverat NSG-flödesloggar men ser inte data i mitt lagringskonto**

- **Installationstid**

Det kan ta upp till 5 minuter innan du ser NSG-flödesloggar på ditt lagringskonto (om det är konfigurerat på rätt sätt). Du ser filen PT1H.json, och [här beskrivs hur du öppnar den](./network-watcher-nsg-flow-logging-portal.md#download-flow-log).

- **Ingen trafik på din NSG:er**

Ibland visas inte loggar om dina virtuella datorer inte är aktiva eller om det finns överordnade filter på en App Gateway eller andra enheter som blockerar trafiken till dina NSG:er.

**Jag vill automatisera NSG-flödesloggar**

Stöd för automatisering via ARM-mallar är för närvarande inte tillgängligt för NSG-flödesloggar. Mer information finns i [funktions meddelandet](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/) .

## <a name="faq"></a>Vanliga frågor

**Vad gör NSG Flow-loggarna?**

Azure nätverks resurser kan kombineras och hanteras via [nätverks säkerhets grupper (NSG: er)](../virtual-network/network-security-groups-overview.md). Med NSG Flow-loggar kan du logga 5 tuple-flödes information om all trafik via din NSG: er. Obehandlade flödes loggar skrivs till ett Azure Storage-konto där de kan bearbetas, analyseras, efter frågas eller exporteras efter behov.

**Påverkar användning av flödes loggar min nätverks svars tid eller prestanda?**

Flödes logg data samlas in utanför sökvägen till nätverks trafiken och påverkar därför inte nätverks flödet eller svars tiden. Du kan skapa eller ta bort flödes loggar utan risk för påverkan på nätverkets prestanda.

**Hur gör jag för att använda flödes loggar för NSG med ett lagrings konto bakom en brand vägg?**

Om du vill använda ett lagrings konto bakom en brand vägg måste du ange ett undantag för betrodda Microsoft-tjänster för åtkomst till ditt lagrings konto:

- Navigera till lagrings kontot genom att skriva lagrings kontots namn i den globala sökningen på portalen eller från [sidan lagrings konton](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)
- Under avsnittet  **Inställningar**  väljer du  **brand väggar och virtuella nätverk**
- I **Tillåt åtkomst från** väljer du  **valda nätverk**. Under  **undantag**, kryssa i rutan bredvid * * * * Tillåt betrodda Microsoft-tjänster att komma åt det här lagrings kontot * * * *
- Om det redan är valt behövs ingen ändring.
- Leta upp mål NSG på [översikts sidan för NSG Flow-loggar](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) och aktivera NSG flödes loggar med ovanstående lagrings konto valt.

Du kan kontrollera lagringsloggarna efter några minuter och bör se en uppdaterad tidsstämpel eller en ny JSON-fil som skapats.

**Hur gör jag för att använda flödes loggar för NSG med ett lagrings konto bakom en tjänst slut punkt?**

NSG flödes loggar är kompatibla med tjänst slut punkter utan att kräva någon extra konfiguration. Se [självstudien om hur du aktiverar tjänstens slut punkter](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) i det virtuella nätverket.

**Vad är skillnaden mellan flödes loggar version 1 & 2?**

Flödes loggar version 2 introducerar konceptet _flödes tillstånd_ & lagrar information om byte och paket som överförs. [Läs mer](#log-format)

## <a name="pricing"></a>Prissättning

NSG flödes loggar debiteras per GB insamlade loggar och levereras med en kostnads fri nivå på 5 GB/månad per prenumeration. För aktuell prissättning i din region, se [sidan Network Watcher prissättning](https://azure.microsoft.com/pricing/details/network-watcher/).

Lagring av loggar debiteras separat, se [Azure Storage Block Blob-pris sida](https://azure.microsoft.com/pricing/details/storage/blobs/) för relevanta priser.