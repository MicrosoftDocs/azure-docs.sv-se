---
title: Så här frågar du efter loggar från Azure Monitor for VMs
description: Azure Monitor for VMs lösning samlar in Mät värden och loggdata till och den här artikeln beskriver posterna och innehåller exempel frågor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: ae0bc6ea35d5c6e3ebe0cd7f232e5c8b1e637d9d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625121"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Så här frågar du efter loggar från Azure Monitor for VMs

Azure Monitor for VMs samlar in prestanda-och anslutnings mått, dator-och process inventerings data och hälso tillstånds information och vidarebefordrar den till arbets ytan Log Analytics i Azure Monitor.  Dessa data är tillgängliga för [fråga](../log-query/log-query-overview.md) i Azure Monitor. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

## <a name="map-records"></a>Mappa poster

En post skapas per timme för varje unik dator och process, förutom de poster som genereras när en process eller dator startar eller aktive ras till Azure Monitor for VMs kart funktion. Dessa poster har egenskaperna i följande tabeller. Fälten och värdena i ServiceMapComputer_CL händelser mappar till fält i dator resursen i ServiceMap Azure Resource Manager API. Fälten och värdena i ServiceMapProcess_CL händelser mappar till fälten i process resursen i ServiceMap Azure Resource Manager API. Fältet ResourceName_s matchar fältet namn i motsvarande Resource Manager-resurs. 

Det finns internt genererade egenskaper som du kan använda för att identifiera unika processer och datorer:

- Dator: Använd *ResourceID* eller *ResourceName_s* för att unikt identifiera en dator i en Log Analytics arbets yta.
- Process: Använd *ResourceID* för att unikt identifiera en process inom en Log Analytics-arbetsyta. *ResourceName_s* är unikt inom kontexten för den dator där processen körs (MachineResourceName_s) 

Eftersom det kan finnas flera poster för en angiven process och dator inom ett angivet tidsintervall, kan frågor returnera fler än en post för samma dator eller process. Om du bara vill ta med den senaste posten lägger `| summarize arg_max(TimeGenerated, *) by ResourceId` du till i frågan.

### <a name="connections-and-ports"></a>Anslutningar och portar

Funktionen anslutnings mått introducerar två nya tabeller i Azure Monitor loggar – VMConnection och VMBoundPort. Tabellerna innehåller information om anslutningarna för en dator (inkommande och utgående) samt de Server portar som är öppna/aktiva på dem. ConnectionMetrics exponeras också via API: er som gör det möjligt att hämta ett mått i ett tids fönster. TCP-anslutningar som orsakas av att de *godkänns* på en lyssnande socket är inkommande, medan de som skapas genom att *ansluta* till en specifik IP-adress och port är utgående. Riktningen för en anslutning representeras av egenskapen Direction, som kan anges till antingen **inkommande** eller **utgående**. 

Poster i dessa tabeller genereras från data som rapporteras av Dependency Agent. Varje post representerar en observation över ett tidsintervall på 1 minut. Egenskapen TimeGenerated anger start tiden för tidsintervallet. Varje post innehåller information för att identifiera respektive entitet, det vill säga anslutning eller port, samt mått som är associerade med den entiteten. För närvarande rapporteras endast nätverks aktivitet som sker med TCP över IPv4. 

#### <a name="common-fields-and-conventions"></a>Gemensamma fält och konventioner 

Följande fält och konventioner gäller både VMConnection och VMBoundPort: 

- Dator: fullständigt kvalificerat domän namn för rapporterings datorn 
- AgentId: den unika identifieraren för en dator med Log Analytics agent  
- Dator: namnet på Azure Resource Manager resursen för den dator som exponeras av ServiceMap. Det är i formatet *m-{GUID}*, där *GUID* är samma GUID som AgentId  
- Process: namnet på Azure Resource Manager resursen för den process som exponeras av ServiceMap. Det har formatet *p-{hex-sträng}*. Processen är unik inom ett dator omfång och genererar ett unikt process-ID mellan datorer, kombinera maskin-och process fält. 
- ProcessName: det körbara namnet på rapporterings processen.
- Alla IP-adresser är strängar i kanoniskt IPv4-format, till exempel *13.107.3.160* 

För att hantera kostnader och komplexitet representerar anslutnings posterna inte enskilda fysiska nätverks anslutningar. Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning, som sedan återspeglas i respektive tabell.  Det innebär att poster i *VMConnection* -tabellen representerar en logisk gruppering och inte de enskilda fysiska anslutningar som observeras. Den fysiska nätverks anslutningen som delar samma värde för följande attribut under ett angivet intervall på en minut, aggregeras i en enda logisk post i *VMConnection*. 

| Egenskap | Beskrivning |
|:--|:--|
|Riktning |Anslutningens riktning, värdet är *inkommande* eller *utgående* |
|Dator |Datorns FQDN |
|Process |Identitet för process eller grupper av processer, initierar/accepterar anslutningen |
|SourceIp |Källans IP-adress |
|DestinationIp |Målets IP-adress |
|DestinationPort |Port nummer för målet |
|Protokoll |Protokoll som används för anslutningen.  Värdena är *TCP*. |

För att kunna utnyttja gruppens påverkan finns information om antalet grupperade fysiska anslutningar i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|LinksEstablished |Antalet fysiska nätverks anslutningar som har upprättats under rapport tids perioden |
|LinksTerminated |Antalet fysiska nätverks anslutningar som har avbrutits under rapport tids perioden |
|LinksFailed |Antalet fysiska nätverks anslutningar som har misslyckats under rapporterings tids perioden. Den här informationen är för närvarande endast tillgänglig för utgående anslutningar. |
|LinksLive |Antalet fysiska nätverks anslutningar som öppnades i slutet av tids perioden för rapportering|

#### <a name="metrics"></a>Mått

Förutom antalet anslutningar räknas information om mängden data som skickas och tas emot på en specifik logisk anslutning eller nätverks port också med följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|Bytes sent |Totalt antal byte som har skickats under rapport tids perioden |
|BytesReceived |Totalt antal byte som har tagits emot under rapport tids perioden |
|Svar |Antalet svar som observerats under rapporterings tids perioden. 
|ResponseTimeMax |Den största svars tid (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|
|ResponseTimeMin |Den minsta svars tid (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|
|ResponseTimeSum |Summan av alla svars tider (millisekunder) som observeras under rapporterings tids perioden. Om inget värde anges är egenskapen tom.|

Den tredje typen av data som rapporteras är svars tid – hur lång tid en anropare ägnar åt att vänta på att en begäran skickas över en anslutning som ska bearbetas och besvaras av Fjärrslutpunkten. Svars tiden som rapporteras är en uppskattning av den sanna svars tiden för det underliggande applikations protokollet. Den beräknas med hjälp av heuristik baserat på observationen av data flödet mellan käll-och mål slutet av en fysisk nätverks anslutning. Konceptuellt är skillnaden mellan den tid då den sista byten av en begäran lämnar avsändaren och den tid då den sista byten av svaret kommer tillbaka till den. Dessa två tidsstämplar används för att avgränsa begär ande-och svars händelser på en specifik fysisk anslutning. Skillnaden mellan dem representerar svars tiden för en enskild begäran. 

I den här första versionen av den här funktionen är vår algoritm en uppskattning som kan fungera med varierande grad av lyckade beroende på det faktiska program protokollet som används för en viss nätverks anslutning. Till exempel fungerar den aktuella metoden bra för Request-Response-baserade protokoll som t. ex. HTTP (S), men fungerar inte med envägs-eller Message Queue-baserade protokoll.

Här är några viktiga saker att tänka på:

1. Om en process accepterar anslutningar på samma IP-adress men över flera nätverks gränssnitt rapporteras en separat post för varje gränssnitt. 
2. Poster med IP-adress med jokertecken kommer inte att innehålla någon aktivitet. De är inkluderade för att representera det faktum att en port på datorn är öppen för inkommande trafik.
3. För att minska utförligheten och data volymen utelämnas poster med IP-adress med jokertecken när det finns en matchande post (för samma process, port och protokoll) med en speciell IP-adress. När en IP-post med jokertecken utelämnas, anges IsWildcardBind Record-egenskapen med den angivna IP-adressen till "true" för att indikera att porten exponeras för varje gränssnitt på rapporterings datorn.
4. Portar som endast är kopplade till ett speciellt gränssnitt har IsWildcardBind inställt på *false*.

#### <a name="naming-and-classification"></a>Namn och klassificering

IP-adressen för fjärran sluten av en anslutning ingår i egenskapen RemoteIp för att under lätta. För inkommande anslutningar är RemoteIp detsamma som SourceIp, medan för utgående anslutningar är det samma som DestinationIp. Egenskapen RemoteDnsCanonicalNames representerar DNS-kanoniska namn som rapporteras av datorn för RemoteIp. Egenskapen RemoteDnsQuestions representerar DNS-frågor som rapporter ATS av datorn för RemoteIp. Egenskapen RemoveClassification är reserverad för framtida användning. 

#### <a name="geolocation"></a>Geolocation

*VMConnection* innehåller också information om geolokalisering för fjärrparten av varje anslutnings post i följande egenskaper för posten: 

| Egenskap | Beskrivning |
|:--|:--|
|RemoteCountry |Namnet på det land/den region som är värd för RemoteIp.  Till exempel *USA* |
|RemoteLatitude |Den geolokalisering på latitud. Till exempel *47,68* |
|RemoteLongitude |Longituden för den här platsen. Till exempel *– 122,12* |

#### <a name="malicious-ip"></a>Skadlig IP

Varje RemoteIp-egenskap i *VMConnection* -tabellen kontrol leras mot en uppsättning IP-adresser med känd skadlig aktivitet. Om RemoteIp identifieras som skadlig kommer följande egenskaper att fyllas i (de är tomma, om IP-adressen inte betraktas som skadlig) i följande egenskaper för posten:

| Egenskap | Beskrivning |
|:--|:--|
|MaliciousIp |RemoteIp-adressen |
|IndicatorThreadType |En hot indikator upptäcktes av följande värden: *botnät*, *C2*, *CryptoMining*, *Darknet*, *DDoS*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *oönskade program*, *visnings lista*.   |
|Description |Beskrivning av det observerade hotet. |
|TLPLevel |TLP-nivån (trafik ljus protokoll) är en av de definierade värdena, *vitt*, *grönt*, *gult*, *rött*. |
|Konfidensbedömning |Värdena är *0 – 100*. |
|Allvarlighetsgrad |Värdena är *0 – 5*, där *5* är det allvarligaste och *0* inte är allvarligt. Standardvärdet är *3*.  |
|FirstReportedDateTime |Första gången som providern rapporterade indikatorn. |
|LastReportedDateTime |Den senaste gången indikatorn visades vid ett flöde. |
|IsActive |Anger att indikatorer inaktive ras med värdet *True* eller *false* . |
|ReportReferenceLink |Länkar till rapporter som är relaterade till en bestämd som kan observeras. |
|AdditionalInformation |Innehåller ytterligare information om det observerade hotet. |

### <a name="ports"></a>Portar 

Portar på en dator som aktivt accepterar inkommande trafik eller som kan acceptera trafik, men som är inaktiva under rapporterings tids perioden, skrivs till tabellen VMBoundPort.  

Varje post i VMBoundPort identifieras med följande fält: 

| Egenskap | Beskrivning |
|:--|:--|
|Process | Identitet för processen (eller grupper av processer) som porten är associerad med.|
|Sökning | Portens IP-adress (kan vara IP *-adress* med jokertecken) |
|Port |Port numret |
|Protokoll | Protokollet.  Exempel: *TCP* eller *UDP* (endast *TCP* stöds för närvarande).|
 
Identiteten som en port härleds från ovanstående fem fält och lagras i egenskapen PortId. Den här egenskapen kan användas för att snabbt hitta poster för en speciell port över tid. 

#### <a name="metrics"></a>Mått 

Port poster innehåller mått som representerar de anslutningar som är kopplade till dem. För närvarande rapporteras följande mått (informationen för varje mått beskrivs i föregående avsnitt): 

- Bytes sent och BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Här är några viktiga saker att tänka på:

- Om en process accepterar anslutningar på samma IP-adress men över flera nätverks gränssnitt rapporteras en separat post för varje gränssnitt.  
- Poster med IP-adress med jokertecken kommer inte att innehålla någon aktivitet. De är inkluderade för att representera det faktum att en port på datorn är öppen för inkommande trafik. 
- För att minska utförligheten och data volymen utelämnas poster med IP-adress med jokertecken när det finns en matchande post (för samma process, port och protokoll) med en speciell IP-adress. När en IP-post med jokertecken utelämnas, anges egenskapen *IsWildcardBind* för posten med den angivna IP-adressen till *True*.  Detta anger att porten exponeras för varje gränssnitt på rapporterings datorn. 
- Portar som endast är kopplade till ett speciellt gränssnitt har IsWildcardBind inställt på *false*. 

### <a name="vmcomputer-records"></a>VMComputer-poster

Poster med en typ av *VMComputer* har inventerings data för servrar med beroende agenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Den unika identifieraren för arbets ytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tidsstämpel för posten (UTC) |
|Dator | Datorns FQDN | 
|AgentId | Unikt ID för Log Analytics agenten |
|Dator | Namnet på Azure Resource Manager resursen för den dator som exponeras av ServiceMap. Det har formatet *m-{GUID}*, där *GUID* är samma GUID som AgentId. | 
|DisplayName | Visningsnamn | 
|FullDisplayName | Fullständigt visnings namn | 
|HostName | Namnet på datorn utan domän namn |
|BootTime | Datorns start tid (UTC) |
|TimeZone | Normaliserad tidszon |
|VirtualizationState | *virtuell*, *hypervisor*, *fysisk* |
|Ipv4Addresses | Matris med IPv4-adresser | 
|Ipv4SubnetMasks | Matris med IPv4-nätmasker (i samma ordning som Ipv4Addresses). |
|Ipv4DefaultGateways | Matris med IPv4-gatewayer | 
|Ipv6Addresses | Matris med IPv6-adresser | 
|MacAddresses | Matris med MAC-adresser | 
|DnsNames | Matris med DNS-namn som är associerade med datorn. |
|DependencyAgentVersion | Den version av beroende agenten som körs på datorn. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Det fullständiga namnet på operativ systemet | 
|PhysicalMemoryMB | Fysiskt minne i megabyte | 
|CPU | Antal processorer | 
|CpuSpeed | CPU-hastigheten i MHz | 
|VirtualMachineType | *HyperV*, *VMware*, *xen* |
|VirtualMachineNativeId | Det VM-ID som tilldelats av dess hypervisor | 
|VirtualMachineNativeName | Namnet på den virtuella datorn |
|VirtualMachineHypervisorId | Den unika identifieraren för hypervisor-datorn som är värd för den virtuella datorn |
|HypervisorType | *HyperV* |
|HypervisorId | Det unika ID: t för hypervisorn | 
|HostingProvider | *Azure* |
|_ResourceId | Unikt ID för en Azure-resurs |
|AzureSubscriptionId | En globalt unik identifierare som identifierar din prenumeration | 
|AzureResourceGroup | Namnet på den Azure-resurs grupp som datorn är medlem i. |
|AzureResourceName | Namnet på Azure-resursen |
|AzureLocation | Platsen för Azure-resursen |
|AzureUpdateDomain | Namnet på Azure Update-domänen |
|AzureFaultDomain | Namnet på Azure-feldomänen |
|AzureVmId | Den unika identifieraren för den virtuella Azure-datorn |
|AzureSize | Storleken på den virtuella Azure-datorn |
|AzureImagePublisher | Namnet på den virtuella Azure-utgivaren |
|AzureImageOffering | Namnet på den virtuella Azure-datorns erbjudande typ | 
|AzureImageSku | SKU: n för den virtuella Azure-avbildningen | 
|AzureImageVersion | Versionen av Azure VM-avbildningen | 
|AzureCloudServiceName | Namnet på Azures moln tjänst |
|AzureCloudServiceDeployment | Distributions-ID för moln tjänsten |
|AzureCloudServiceRoleName | Namn på moln tjänst roll |
|AzureCloudServiceRoleType | Typ av moln tjänst roll: *Worker* eller *Web* |
|AzureCloudServiceInstanceId | Instans-ID för moln tjänst roll |
|AzureVmScaleSetName | Namnet på den virtuella datorns skal uppsättning |
|AzureVmScaleSetDeployment | Distributions-ID för skalnings uppsättning för virtuell dator |
|AzureVmScaleSetResourceId | Den unika identifieraren för den virtuella datorns skalnings uppsättnings resurs.|
|AzureVmScaleSetInstanceId | Den unika identifieraren för den virtuella datorns skal uppsättning |
|AzureServiceFabricClusterId | Det unika identifierare i Azure Service Fabric-klustret | 
|AzureServiceFabricClusterName | Namnet på Azure Service Fabric-klustret |

### <a name="vmprocess-records"></a>VMProcess-poster

Poster med en typ av *VMProcess* har inventerings data för TCP-anslutna processer på servrar med beroende agenten. Dessa poster har egenskaperna i följande tabell:

| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Den unika identifieraren för arbets ytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tidsstämpel för posten (UTC) |
|Dator | Datorns FQDN | 
|AgentId | Unikt ID för Log Analytics agenten |
|Dator | Namnet på Azure Resource Manager resursen för den dator som exponeras av ServiceMap. Det har formatet *m-{GUID}*, där *GUID* är samma GUID som AgentId. | 
|Process | Den unika identifieraren för den Tjänstkarta processen. Den är i formatet *p-{GUID}*. 
|ExecutableName | Namnet på den körbara processen | 
|DisplayName | Processens visnings namn |
|Roll | Process roll: *webserver*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Group | Namn på process grupp. Processer i samma grupp är logiskt relaterade, t. ex. en del av samma produkt-eller system komponent. |
|StartTime | Start tid för process bassäng |
|FirstPid | Det första PID i lagringspoolen |
|Description | Beskrivning av processen |
|CompanyName | Företagets namn |
|InternalName | Det interna namnet |
|ProductName | Produktens namn |
|ProductVersion | Versionen av produkten |
|FileVersion | Versionen av filen |
|ExecutablePath |Sökvägen till den körbara filen |
|Raden | Kommando raden |
|WorkingDirectory | Arbets katalogen |
|Tjänster | En matris med tjänster som processen körs under |
|Användarnamn | Kontot under vilket processen körs |
|UserDomain | Domänen som processen körs under |
|_ResourceId | Den unika identifieraren för en process inom arbets ytan |


## <a name="sample-map-queries"></a>Exempel mappnings frågor

### <a name="list-all-known-machines"></a>Visa en lista med alla kända datorer

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>När den virtuella datorn startades om senast

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Översikt över virtuella Azure-datorer efter avbildning, plats och SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Visar kapaciteten för fysiskt minne för alla hanterade datorer

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Visa dator namn, DNS, IP och OS

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Hitta alla processer med "SQL" på kommando raden

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Hitta en dator (senaste posten) efter resurs namn

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Hitta en dator (senaste posten) via IP-adress

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Visa en lista med alla kända processer på en angiven dator

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Visa en lista med alla datorer som kör SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Visa en lista med alla unika produkt versioner av sväng i mitt Data Center

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Skapa en dator grupp för alla datorer som kör CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Trender för skickade och mottagna byte

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Vilka virtuella Azure-datorer som överför de flesta byte

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Länka status trender

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Trend för anslutnings problem

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Kopplade portar

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Antal öppna portar på olika datorer

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Poäng processer på arbets ytan med antalet portar som de har öppna

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Mängd beteende för varje port

Den här frågan kan sedan användas för att räkna ut portar efter aktivitet, t. ex. portar med mest inkommande/utgående trafik, portar med de flesta anslutningar
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Sammanfatta utgående anslutningar från en grupp datorer

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Prestanda poster
Poster med en typ av *InsightsMetrics* har prestanda data från gäst operativ systemet på den virtuella datorn. Dessa poster har egenskaperna i följande tabell:


| Egenskap | Beskrivning |
|:--|:--|
|TenantId | Unikt ID för arbets ytan |
|SourceSystem | *Insikter* | 
|TimeGenerated | Tid då värdet samlades in (UTC) |
|Dator | Datorns FQDN | 
|Ursprung | *vm.azm.ms* |
|Namnområde | Prestanda räknarens kategori | 
|Name | Namn på prestanda räknaren |
|Val | Insamlat värde | 
|Taggar | Relaterad information om posten. Se tabellen nedan för taggar som används med olika post typer.  |
|AgentId | Unik identifierare för varje dators agent |
|Typ | *InsightsMetrics* |
|_ResourceId_ | Resurs-ID för den virtuella datorn |

De prestanda räknare som för närvarande samlas in i tabellen *InsightsMetrics* visas i följande tabell:

| Namnområde | Name | Beskrivning | Enhet | Taggar |
|:---|:---|:---|:---|:---|
| Dator    | Pulsslag             | Datorns pulsslag                        | | |
| Minne      | AvailableMB           | Tillgängliga byte för minne                    | Megabyte      | memorySizeMB – total minnes storlek|
| Nätverk     | WriteBytesPerSecond   | Skrivna byte för nätverks-byte per sekund            | BytesPerSecond | NetworkDeviceId-ID för enheten<br>Byte-totalt antal skickade byte |
| Nätverk     | ReadBytesPerSecond    | Nätverks läsnings byte per sekund             | BytesPerSecond | networkDeviceId-ID för enheten<br>Byte-totalt antal mottagna byte |
| Processor   | UtilizationPercentage | Procent andel processor användning          | Procent        | totalCpus – totalt antal processorer |
| Logisk disk | WritesPerSecond       | Skrivningar per sekund för logisk disk            | CountPerSecond | mountId-monterings-ID för enheten |
| Logisk disk | WriteLatencyMs        | Skriv fördröjning i millisekunder för logisk disk    | Millisekunder   | mountId-monterings-ID för enheten |
| Logisk disk | WriteBytesPerSecond   | Skrivna byte per sekund för logisk disk       | BytesPerSecond | mountId-monterings-ID för enheten |
| Logisk disk | TransfersPerSecond    | Överföringar av logiska diskar per sekund         | CountPerSecond | mountId-monterings-ID för enheten |
| Logisk disk | TransferLatencyMs     | Fördröjning för överföring av logisk disk i millisekunder | Millisekunder   | mountId-monterings-ID för enheten |
| Logisk disk | ReadsPerSecond        | Logiska disk läsningar per sekund             | CountPerSecond | mountId-monterings-ID för enheten |
| Logisk disk | ReadLatencyMs         | Läs fördröjning för logisk disk i millisekunder     | Millisekunder   | mountId-monterings-ID för enheten |
| Logisk disk | ReadBytesPerSecond    | Lästa byte per sekund för logisk disk        | BytesPerSecond | mountId-monterings-ID för enheten |
| Logisk disk | FreeSpacePercentage   | Procent andel ledigt utrymme på logisk disk        | Procent        | mountId-monterings-ID för enheten |
| Logisk disk | FreeSpaceMB           | Byte i ledigt utrymme på logisk disk             | Megabyte      | mountId-monterings-ID för enheten<br>diskSizeMB – total disk storlek |
| Logisk disk | BytesPerSecond        | Logiska Disk byte per sekund             | BytesPerSecond | mountId-monterings-ID för enheten |


## <a name="next-steps"></a>Nästa steg

* Om du inte har använt att skriva logg frågor i Azure Monitor kan du läsa om [hur du använder Log Analytics](../log-query/log-analytics-tutorial.md) i Azure Portal för att skriva logg frågor.

* Lär dig mer om att [skriva Sök frågor](../log-query/get-started-queries.md).