---
title: Hälsotillståndsavsökningar för att skala och tillhandahålla hög tillgänglighet för din tjänst
titleSuffix: Azure Load Balancer
description: I den här artikeln får du lära dig hur du använder hälso avsökningar för att övervaka instanser bakom Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a008d7b26738b9552a7a43ab026391bd9afe0aa8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96780951"
---
# <a name="load-balancer-health-probes"></a>Hälsoavsökningar för Load Balancer

När du använder regler för belastnings utjämning med Azure Load Balancer måste du ange hälso avsökningar för att tillåta Load Balancer att identifiera Server delens slut punkts status.  Konfigurationen av hälso avsökningen och avsöknings Svaren avgör vilka instanser av backend-poolen som kommer att ta emot nya flöden. Du kan använda hälso avsökningar för att identifiera ett programs fel på en backend-slutpunkt. Du kan också skapa ett anpassat svar på en hälso avsökning och använda hälso avsökningen för flödes kontroll för att hantera inläsning eller planerad stillestånds tid. När en hälso avsökning Miss lyckas, slutar Load Balancer att skicka nya flöden till respektive ohälsosam instans. Utgående anslutning påverkas inte, endast inkommande anslutningar påverkas.

Hälso avsökningar stöder flera protokoll. Tillgängligheten för ett speciellt hälso avsöknings protokoll varierar med Load Balancer SKU.  Dessutom varierar funktions sättet för tjänsten med Load Balancer SKU som visas i den här tabellen:

| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| **[Avsöknings typer](#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Beteende för avsökning](#probedown)** | Alla avsökningar, alla TCP-flöden fortsätter. | Alla avsökningar, alla TCP-flöden upphör att gälla. | 


>[!IMPORTANT]
>Granska det här dokumentet i sin helhet, inklusive viktig [design vägledning](#design) för att skapa en tillförlitlig tjänst.

>[!IMPORTANT]
>Load Balancer hälso avsökningar kommer från IP-168.63.129.16 och får inte blockeras för avsökningar för att markera instansen.  Granska [IP-adressen för avsöknings källan](#probesource) för mer information.

>[!IMPORTANT]
>Oavsett det konfigurerade tids gräns tröskelvärdet avsöker HTTP (S) Load Balancer hälso avsökningar automatiskt ned en instans om servern returnerar status kod som inte är HTTP 200 OK eller om anslutningen avbryts via TCP-återställning.

## <a name="probe-configuration"></a><a name="probes"></a>Avsöknings konfiguration

Konfigurationen av hälso avsökningen består av följande element:

- Varaktighet för intervallet mellan enskilda avsökningar
- Antal avsöknings svar som måste observeras innan avsökningen övergår till ett annat tillstånd
- Protokoll för avsökningen
- Port för avsökningen
- HTTP-sökväg som ska användas för HTTP GET vid användning av HTTP (S)-avsökningar

>[!NOTE]
>En avsöknings definition är inte obligatorisk eller kontrol leras när du använder Azure PowerShell, Azure CLI, mallar eller API. Verifierings test för avsökning görs bara när du använder Azure-portalen.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Förstå program signal, identifiering av signal och plattformens reaktion

Antalet avsöknings svar gäller både

- antalet lyckade avsökningar som tillåter att en instans markeras som upp och
- antalet timeout-avsökningar som gör att en instans markeras som nere.

De angivna värdena för timeout och intervall avgör om en instans ska markeras med upp eller ned.  Varaktigheten för intervallet multiplicerat med antalet avsöknings svar avgör den varaktighet under vilken avsöknings Svaren måste identifieras.  Tjänsten kommer att reagera efter att de begärda avsökningarna har uppnåtts.

Vi kan illustrera beteendet ytterligare med ett exempel. Om du har angett antalet avsöknings svar till 2 och intervallet till 5 sekunder, så innebär det att tids gräns felen för avsökningen måste observeras inom 10 sekunders intervall.  Eftersom den tid då en avsökning skickas inte synkroniseras när programmet kan ändra tillstånd, kan vi binda tiden till att identifiera genom två scenarier:

1. Om ditt program börjar skapa ett tids gräns avsöknings svar precis innan den första avsökningen kommer, tar identifieringen av dessa händelser 10 sekunder (2 x 5 sekunder) plus programmets varaktighet, vilket börjar signalera en timeout till när den första avsökningen kommer.  Du kan anta att identifieringen tar något över 10 sekunder.
2. Om ditt program börjar skapa ett tids gräns avsöknings svar strax efter det att den första avsökningen har infallit, börjar inte identifieringen av dessa händelser förrän nästa avsökning kommer (och tids gränsen) plus ytterligare 10 sekunder (2 x 5 sekunder).  Du kan anta att den här identifieringen tar strax under 15 sekunder.

I det här exemplet tar plattformen en liten stund att reagera på den här ändringen när identifieringen har skett.  Det innebär en beroende på 

1. När programmet börjar ändra tillstånd och
2. När den här ändringen upptäcks och uppfyller de nödvändiga kriterierna (antal avsökningar som skickats vid det angivna intervallet) och
3. När identifieringen har kommunicerats över plattformen 

Du kan anta att reaktionen på ett tids gräns avsöknings svar tar mellan minst 10 sekunder och högst en aning över 15 sekunder att reagera på en förändring i signalen från programmet.  Det här exemplet tillhandahålls för att illustrera vad som händer, men det är inte möjligt att prognostisera en exakt varaktighet utöver ovanstående rikt linjer som illustreras i det här exemplet.

>[!NOTE]
>Hälso avsökningen avsöker alla instanser som körs i backend-poolen. Om en instans stoppas kommer den inte att avsökas förrän den har startats igen.

## <a name="probe-types"></a><a name="types"></a>Avsöknings typer

Protokollet som används av hälso avsökningen kan konfigureras till något av följande:

- [TCP-lyssnare](#tcpprobe)
- [HTTP-slutpunkter](#httpprobe)
- [HTTPS-slutpunkter](#httpsprobe)

Vilka protokoll som är tillgängliga beror på vilken Load Balancer SKU som används:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| **Standard-SKU** |    &#9989; |   &#9989; |   &#9989; |
| **Grundläggande SKU** |   &#9989; |   &#9989; | &#10060; |

### <a name="tcp-probe"></a><a name="tcpprobe"></a> TCP-avsökning

TCP-avsökningar initierar en anslutning genom att utföra en tre-vägs öppen TCP-handskakning med den definierade porten.  TCP-avsökningar avslutar en anslutning med en fyra vägs stängnings-TCP-handskakning.

Det minsta avsöknings intervallet är 5 sekunder och det minsta antalet felaktiga svar är 2.  Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

En TCP-avsökning Miss lyckas när:
* TCP-lyssnaren på instansen svarar inte alls under tids gräns perioden.  En avsökning har marker ATS baserat på antalet avsöknings begär Anden som har kon figurer ATS för att svara innan avsökningen avbröts.
* Avsökningen tar emot en TCP-återställning från instansen.

Följande visar hur du kan uttrycka den här typen av avsöknings konfiguration i en Resource Manager-mall:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="http--https-probe"></a><a name="httpprobe"></a><a name="httpsprobe"></a>Http/https-avsökning

>[!NOTE]
>HTTPS-avsökning är endast tillgänglig för [standard Load Balancer](./load-balancer-overview.md).

HTTP-och HTTPS-avsökningar bygger på TCP-avsökningen och utfärdar en HTTP-hämtning med den angivna sökvägen. Båda dessa avsökningar stöder relativa sökvägar för HTTP GET. HTTPS-avsökningar är samma som HTTP-avsökningar med tillägg av en Transport Layer Security (TLS, tidigare kallat SSL). Hälso avsökningen markeras när instansen svarar med HTTP-status 200 inom tids gränsen.  Hälso avsökningen försöker kontrol lera den konfigurerade hälso avsöknings porten var 15: e sekund som standard. Det minsta avsöknings intervallet är 5 sekunder. Den totala varaktigheten för alla intervall får inte överstiga 120 sekunder.

HTTP/HTTPS-avsökningar kan också vara användbara för att implementera din egen logik för att ta bort instanser från belastnings Utjämnings rotation om avsöknings porten också är lyssnaren för själva tjänsten. Du kan till exempel välja att ta bort en instans om den är över 90% CPU och returnera en HTTP-status som inte är 200. 

> [!NOTE] 
> HTTPS-avsökningen kräver att certifikat används baserat på att det finns en minsta signatur-hash för SHA256 i hela kedjan.

Om du använder Cloud Services och har webb roller som använder w3wp.exe, kan du också få automatisk övervakning av din webbplats. Felen i din webbplats kod returnerar en status som inte är 200 till belastningsutjämnaren för belastnings utjämning.

En HTTP/HTTPS-avsökning Miss lyckas när:
* Avsöknings slut punkten returnerar en HTTP-svarskod som inte är 200 (till exempel 403, 404 eller 500). Hälso avsökningen markeras omedelbart. 
* Avsöknings slut punkten svarar inte alls under minimivärdet för avsöknings intervallet och tids gränsen på 30 sekunder. Flera avsöknings begär Anden kan bli obesvarade innan avsökningen markeras som ej igång och tills summan av alla timeout-intervall har uppnåtts.
* Avsöknings slut punkten stänger anslutningen via en TCP-återställning.

Följande visar hur du kan uttrycka den här typen av avsöknings konfiguration i en Resource Manager-mall:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guest-agent-probe-classic-only"></a><a name="guestagent"></a>Gästa Gent avsökning (endast klassiskt)

Moln tjänst roller (arbets roller och webb roller) använder en gästa Gent för avsöknings övervakning som standard.  En gästa Gent avsökning är en sista utväg-konfiguration.  Använd alltid en hälso avsökning uttryckligen med en TCP-eller HTTP-avsökning. En gästa Gent avsökning är inte lika effektiv som explicit definierade avsökningar för de flesta program scenarier.

En gästa Gent avsökning är en kontroll av gäst agenten i den virtuella datorn. Den lyssnar sedan och svarar bara med ett HTTP 200-svar på OK när instansen har statusen klar. (Andra tillstånd är upptagna, återvinning eller stoppas.)

Mer information finns i [Konfigurera tjänst definitions filen (csdef) för hälso avsökningar](/previous-versions/azure/reference/ee758710(v=azure.100)) eller [Kom igång genom att skapa en offentlig belastningsutjämnare för Cloud Services](/previous-versions/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Om gäst agenten inte svarar med HTTP 200 OK markerar belastnings Utjämnings instansen som svarar inte. Sedan slutar det att skicka flöden till den instansen. Belastnings utjämningen fortsätter att kontrol lera instansen. 

Om gäst agenten svarar med en HTTP 200 skickar belastningsutjämnaren nya flöden till den instansen igen.

När du använder en webb roll körs webbplats koden vanligt vis i w3wp.exe, som inte övervakas av Azure-infrastrukturen eller gäst agenten. Felen i w3wp.exe (till exempel HTTP 500-svar) rapporteras inte till gäst agenten. Därför tar belastningsutjämnaren inte den instansen från rotationen.

<a name="health"></a>
## <a name="probe-up-behavior"></a><a name="probehealth"></a>Avsöknings beteende

TCP-, http-och https-avsökningar betraktas som felfria och markerar backend-slutpunkten som felfri när:

* Hälso avsökningen lyckas när den virtuella datorn startas.
* Det angivna antalet avsökningar som krävs för att markera backend-slutpunkten som felfri har uppnåtts.

Alla backend-slutpunkter som har uppnått ett felfritt tillstånd är berättigade till att ta emot nya flöden.  

> [!NOTE]
> Om hälso avsökningen fluktuerar väntar belastningsutjämnaren längre innan den placerar backend-slutpunkten igen i felfritt tillstånd. Den extra vänte tiden skyddar användaren och infrastrukturen och är en avsiktlig princip.

## <a name="probe-down-behavior"></a><a name="probedown"></a>Beteende för avsökning

### <a name="tcp-connections"></a>TCP-anslutningar

Nya TCP-anslutningar kommer att lyckas på den återstående felfria Server delen.

Om en server dels hälso avsökningen Miss lyckas upprättas TCP-anslutningar till backend-slutpunkten.

Om alla avsökningar för alla instanser i en backend-pool Miss söker, skickas inga nya flöden till backend-poolen. Standard Load Balancer tillåter att etablerade TCP-flöden fortsätter.  Grundläggande Load Balancer avslutar alla befintliga TCP-flöden till backend-poolen.
 
Load Balancer är en direkt tjänst (avslutar inte TCP-anslutningar) och flödet är alltid mellan klienten och den virtuella datorns gäst operativ system och program. En pool med alla avsökningar gör att en klient del inte svarar på TCP-anslutningens öppna försök (SYN) eftersom det inte finns någon felfria backend-slutpunkt för att ta emot flödet och svara med en SYN-ACK.

### <a name="udp-datagrams"></a>UDP-datagram

UDP-datagram skickas till felfria backend-slutpunkter.

UDP är anslutnings löst och det finns inget flödes tillstånd spårat för UDP. Om det inte går att söka efter en backend-slutpunkt flyttas befintliga UDP-flöden till en annan felfria instans i backend-poolen.

Om alla avsökningar för alla instanser i en backend-pool Miss söker, avslutas befintliga UDP-flöden för Basic-och standard Load Balancer.

<a name="source"></a>
## <a name="probe-source-ip-address"></a><a name="probesource"></a>IP-adress för avsöknings källa

Load Balancer använder en distribuerad avsöknings tjänst för den interna hälso modellen. Tjänsten för avsökning finns på varje värd där virtuella datorer och kan köras på begäran för att generera hälso avsökningar enligt kundens konfiguration. Hälso avsöknings trafiken är direkt mellan avsöknings tjänsten som genererar hälso avsökningen och den virtuella kunden. Alla Load Balancer hälso avsökningar kommer från IP-168.63.129.16 som källa.  Du kan använda IP-adressutrymmet i ett VNet som inte är RFC1918 utrymme.  Med en globalt reserverad Microsoft-adress som ägs av Microsoft minskar risken för en IP-adresskonflikt med det IP-adressutrymme som du använder i VNet.  Den här IP-adressen är samma i alla regioner och ändras inte och är inte en säkerhets risk eftersom endast den interna Azure Platform-komponenten kan käll paket från den här IP-adressen. 

AzureLoadBalancer service tag identifierar den här käll-IP-adressen i dina [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) och tillåter hälso avsöknings trafik som standard.

Utöver Load Balancer hälso avsökningar [använder följande åtgärder följande IP-adress](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Gör det möjligt för VM-agenten att kommunicera med plattformen för att signalera att den är i ett klart läge
- Möjliggör kommunikation med den virtuella DNS-servern för att tillhandahålla filtrerad namn matchning till kunder som inte definierar anpassade DNS-servrar.  Den här filtreringen säkerställer att kunderna bara kan lösa värd namnen för deras distribution.
- Gör det möjligt för den virtuella datorn att hämta en dynamisk IP-adress från DHCP-tjänsten i Azure.

## <a name="design-guidance"></a><a name="design"></a> Design vägledning

Hälso avsökningar används för att göra tjänsten elastisk och kan skalas. En felaktig konfiguration eller ett dåligt design mönster kan påverka tjänstens tillgänglighet och skalbarhet. Granska hela dokumentet och fundera över vad som påverkar ditt scenario när avsöknings svaret är markerat eller markerat och hur det påverkar tillgängligheten för ditt program scenario.

När du utformar hälso modellen för ditt program bör du avsöka en port på en backend-slutpunkt som visar hälso tillståndet för den instansen __och__ den program tjänst som du tillhandahåller.  Program porten och avsöknings porten måste inte vara samma.  I vissa fall kan det vara önskvärt att avsöknings porten är annorlunda än porten som programmet tillhandahåller tjänsten på.  

Ibland kan det vara användbart för ditt program att generera ett hälso avsöknings svar för att inte bara identifiera ditt programs hälsa, utan också signaler direkt till Load Balancer om din instans ska ta emot eller inte ta emot nya flöden.  Du kan ändra avsöknings svaret så att ditt program kan skapa mottryck och begränsa leveransen av nya flöden till en instans genom att inte godkänna hälso avsökningen eller förbereda för underhåll av ditt program och börja tömma ditt scenario.  När du använder Standard Load Balancer kommer en [avsöknings](#probedown) signal att tillåta att TCP-flöden fortsätter tills tids gränsen för inaktivitet eller anslutning stängs. 

För UDP-belastnings utjämning bör du skapa en anpassad hälso avsöknings signal från backend-slutpunkten och använda antingen en TCP-, HTTP-eller HTTPS-hälsoavsökning som riktar sig mot motsvarande lyssnare för att avspegla hälso tillståndet för ditt UDP-program.

När du använder [belastnings Utjämnings regler för ha-portar](load-balancer-ha-ports-overview.md) med [standard Load Balancer](./load-balancer-overview.md), är alla portar belastningsutjämnade och ett enda svar på hälso avsökning måste visa status för hela instansen.

Översätt eller Använd inte en hälso avsökning via instansen som tar emot hälso avsökningen till en annan instans i ditt VNet eftersom den här konfigurationen kan leda till sammanhängande fel i ditt scenario.  Tänk dig följande scenario: en uppsättning tredjeparts-enheter distribueras i backend-poolen för en Load Balancer-resurs för att ge skalning och redundans för apparater och hälso avsökningen har kon figurer ATS för att avsöka en port som används av tredjeparts-proxyservrar eller översätts till andra virtuella datorer bakom enheten.  Om du avsöker samma port som du använder för att översätta eller proxyanslutningar till de andra virtuella datorerna bakom installationen, markerar alla avsöknings svar från en enskild virtuell dator bakom installations programmet själva enheten. Den här konfigurationen kan leda till ett överlappande avbrott i hela program scenariot, till följd av en enda server dels slut punkt bakom enheten.  Utlösaren kan vara ett tillfälligt avsöknings fel som gör att Load Balancer kan markera det ursprungliga målet (installations instansen) och i sin tur inaktivera hela program scenariot. Avsök själva hälso tillståndet för själva enheten i stället. Valet av avsökning för att fastställa hälso signalen är ett viktigt övervägande för NVA-scenarier (Network Virtual Restore) och du måste kontakta din program varu leverantör för att få en lämplig hälso signal för sådana scenarier.

Om du inte tillåter avsökningens [käll-IP-adress](#probesource) i brand Väggs principerna, Miss söker hälso avsökningen eftersom den inte kan komma åt din instans.  I sin tur kommer Load Balancer att markera din instans på grund av hälso avsöknings fel.  Den här Felaktiga konfigurationen kan orsaka att ditt belastningsutjämnade program scenario inte fungerar.

För att Load Balancer hälso avsökningen för att märka upp din instans **måste** du tillåta den här IP-adressen i alla Azure- [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) och lokala brand Väggs principer.  Som standard innehåller varje nätverks säkerhets grupp [service tag-](../virtual-network/network-security-groups-overview.md#service-tags) AzureLoadBalancer för att tillåta hälso avsöknings trafik.

Om du vill testa ett hälso avsöknings fel eller ange en enskild instans kan du använda en [nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md) för att uttryckligen blockera hälso avsökningen (mål port eller [käll-IP](#probesource)) och simulera fel i en avsökning.

Konfigurera inte ditt VNet med det Microsoft-adressintervall som innehåller 168.63.129.16.  Sådana konfigurationer hamnar i en kollision med hälso avsökningens IP-adress och kan orsaka att ditt scenario inte fungerar.

Om du har flera gränssnitt på den virtuella datorn måste du kontrol lera att du svarar på avsökningen på det gränssnitt som du fick det på.  Du kan behöva källans nätverks adress översätter den här adressen i den virtuella datorn per gränssnitt.

Aktivera inte [TCP-tidsstämplar](https://tools.ietf.org/html/rfc1323).  Om du aktiverar TCP-tidsstämplar kan det leda till att hälso avsökningar inte fungerar på grund av att TCP-paketen har släppts av den virtuella datorns gäst operativ system TCP-stack, vilket leder till att Load Balancer markerar respektive slut  TCP-tidsstämplar aktive ras rutinmässigt som standard på säkerhets härdning av virtuella dator avbildningar och måste inaktive ras.

## <a name="monitoring"></a>Övervakning

Både offentliga och interna [standard Load Balancer](./load-balancer-overview.md) exponeras per slut punkt och status för status avsökningar för backend-slutpunkt som flerdimensionella mått via Azure Monitor. Dessa mått kan utnyttjas av andra Azure-tjänster eller partner program. 

Grundläggande offentliga Load Balancer visar status för hälso avsökningar som sammanfattas per backend-pool via Azure Monitor loggar.  Azure Monitors loggar är inte tillgängliga för interna Basic Load Balancer.  Du kan använda [Azure Monitor loggar](load-balancer-monitor-log.md) för att kontrol lera hälso statusen för avsökningen av offentliga belastningsutjämnare och avsöknings antal. Loggning kan användas med Power BI eller Azure Operational Insights för att tillhandahålla statistik om belastnings utjämningens hälso status.

## <a name="limitations"></a>Begränsningar

- HTTPS-avsökningar stöder inte ömsesidig autentisering med ett klient certifikat.
- Du bör anta att hälso avsökningar Miss söker när TCP-tidsstämplar är aktiverade.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [standard Load Balancer](./load-balancer-overview.md)
- [Kom igång med att skapa en offentlig belastningsutjämnare i Resource Manager med hjälp av PowerShell](quickstart-load-balancer-standard-public-powershell.md)
- [REST API för hälso avsökningar](/rest/api/load-balancer/loadbalancerprobes/)
- Begär nya hälso avsöknings möjligheter med [Load Balancer UserVoice](https://aka.ms/lbuservoice)
