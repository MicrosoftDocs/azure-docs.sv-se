---
title: Prestanda justering av TCP/IP-prestanda för virtuella Azure-datorer | Microsoft Docs
description: Lär dig olika vanliga metoder för prestanda justering av TCP/IP-prestanda och deras relation till virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: 10fae563f80a4d77e3f4bb1d5ca809a4130b050a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558856"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Prestanda justering för TCP/IP för virtuella Azure-datorer

I den här artikeln beskrivs vanliga metoder för prestanda justering av TCP/IP-prestanda och några saker att tänka på när du använder dem för virtuella datorer som körs på Azure. Den ger en grundläggande översikt över teknikerna och utforska hur de kan finjusteras.

## <a name="common-tcpip-tuning-techniques"></a>Vanliga metoder för att justera TCP/IP-justering

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, fragmentering och överföring av stora sändningar

#### <a name="mtu"></a>STORLEK

Den största överförings enheten (MTU) är den största storleks ramen (paket), som anges i byte, som kan skickas över ett nätverks gränssnitt. MTU är en konfigurerbar inställning. Standard-MTU som används på virtuella Azure-datorer och standardinställningen på de flesta nätverks enheter globalt är 1 500 byte.

#### <a name="fragmentation"></a>Fragmentering

Fragmentering inträffar när ett paket skickas som överskrider MTU för ett nätverks gränssnitt. TCP/IP-stacken kommer att dela upp paketet i mindre delar (fragment) som överensstämmer med gränssnittets MTU. Fragmentering sker i IP-skiktet och är oberoende av det underliggande protokollet (t. ex. TCP). När ett paket med 2 000 byte skickas över ett nätverks gränssnitt med ett MTU-värde på 1 500, kommer paketet att delas upp i 1 1 500-byte-paket och 1 500 byte-paket.

Nätverks enheter i sökvägen mellan en källa och ett mål kan antingen släppa paket som överskrider MTU eller fragmentera paketet till mindre delar.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Biten Fragmentera inte i ett IP-paket

Biten Fragmentera inte (DF) är en flagga i IP-protokollets huvud. DF-biten anger att nätverks enheter på sökvägen mellan avsändaren och mottagaren inte får fragmentera paketet. Den här biten kan ställas in av många skäl. (Mer information finns i avsnittet "Path MTU Discovery" i den här artikeln för ett exempel.) När en nätverks enhet tar emot ett paket med biten Fragmentera inte och paketet överskrider enhetens gränssnitts-MTU är standard beteendet för enheten att släppa paketet. Enheten skickar ett meddelande om ICMP-fragmentering som krävs till den ursprungliga källan för paketet.

#### <a name="performance-implications-of-fragmentation"></a>Prestanda konsekvenser för fragmentering

Fragmentering kan ha negativa prestanda effekter. En av de främsta orsakerna till prestanda är att CPU/minnes påverkan från fragmentering och sammansättning av paket. När en nätverks enhet behöver fragmentera ett paket måste det allokeras processor-/minnes resurser för att utföra fragmentering.

Samma sak händer när paketet sätts samman igen. Nätverks enheten måste lagra alla fragment tills de tas emot så att de kan ommontera dem i det ursprungliga paketet. Den här processen för fragmentering och sammansättning kan också orsaka svars tider.

De andra möjliga negativa prestanda indirekt är att fragmenterade paket kan hamna i felaktig ordning. När paket tas emot i fel ordning kan vissa typer av nätverks enheter släppa dem. När detta sker måste hela paketet återsändas.

Fragment ignoreras vanligt vis av säkerhetsenheter som nätverks brand väggar eller när en nätverks enhet tar emot buffertar. När en nätverks enhets mottagningsbuffertar är slut försöker en nätverks enhet sätta samman ett fragmenterat paket igen, men har inte resurserna för att lagra och omanvända paketet.

Fragmentering kan ses som en negativ åtgärd, men stöd för fragmentering krävs när du ansluter olika nätverk via Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Fördelar och konsekvenser för att ändra MTU

I allmänhet kan du skapa ett effektivare nätverk genom att öka MTU. Varje paket som överförs har huvud information som läggs till i det ursprungliga paketet. När fragmenteringen skapar fler paket finns det fler huvud kostnader och det gör nätverket mindre effektivt.

Här är ett exempel. Ethernet-huvudstorleken är 14 byte plus en kontroll ordning i 4 byte-ramar för att säkerställa ram konsekvens. Om 1 2 000 byte-paket skickas läggs 18 byte med Ethernet-kostnader till i nätverket. Om paketet är fragmenterat i ett 1 500 byte-paket och ett paket med 500 byte, kommer varje paket att ha 18 byte Ethernet-huvud, totalt 36 byte.

Tänk på att det inte nödvändigt vis skapas något effektivare nätverk när du ökar MTU. Om ett program bara skickar 500 byte-paket, kommer samma rubrik för sidhuvudet att finnas om MTU är 1 500 byte eller 9 000 byte. Nätverket blir effektivare endast om det använder större paket storlekar som påverkas av MTU.

#### <a name="azure-and-vm-mtu"></a>Azure och VM MTU

Standard-MTU för virtuella Azure-datorer är 1 500 byte. Azure Virtual Network stacken kommer att försöka fragmentera ett paket med 1 400 byte.

Observera att Virtual Network stack inte är ineffektiv eftersom den fragmenterar paket på 1 400 byte trots att de virtuella datorerna har en MTU på 1 500. En stor del av nätverks paketen är mycket mindre än 1 400 eller 1 500 byte.

#### <a name="azure-and-fragmentation"></a>Azure och fragmentering

Virtual Network stack har kon figurer ATS för att ta bort "out of order-fragment", som är fragmenterade paket som inte kommer in i sin ursprungliga fragmenterade ordning. Paketen ignoreras huvudsakligen på grund av ett säkerhets problem i nätverket i november 2018 som kallas FragmentSmack.

FragmentSmack är en defekt i hur Linux-kerneln hanterade omsammansättning av fragmenterade IPv4-och IPv6-paket. En angripare kan använda det här felet för att utlösa dyra ommonterings åtgärder, vilket kan leda till ökad CPU och en denial of service i mål systemet.

#### <a name="tune-the-mtu"></a>Justera MTU

Du kan konfigurera en virtuell Azure-dators MTU, som du kan i andra operativ system. Men du bör överväga fragmenteringen i Azure, som beskrivs ovan, när du konfigurerar en MTU.

Vi uppmuntrar inte kunderna att öka VM-MTU. Denna diskussion är avsedd att förklara information om hur Azure implementerar MTU och utför fragmentering.

> [!IMPORTANT]
>Att utöka MTU är inte känt för att förbättra prestandan och kan ha en negativ effekt på programmets prestanda.
>
>

#### <a name="large-send-offload"></a>Avlastning för stor överföring

Avlastning av stor överföring (LSO) kan förbättra nätverks prestanda genom att avlasta paket segmentering till Ethernet-kortet. När LSO är aktive rad skapar TCP/IP-stacken ett stort TCP-paket och skickar det till Ethernet-kortet för segmentering innan det vidarebefordras. Fördelen med LSO är att det kan frigöra processorn från att segmentera paket i storlekar som stämmer överens med MTU och avlastning som bearbetar till Ethernet-gränssnittet där det utförs i maskin vara. Läs mer om fördelarna med LSO i stödjande överföring av [stora](/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)sändningar.

När LSO är aktive rad kan Azure-kunder se stora ram storlekar när de utför paket insamlingar. Dessa stora bild storlekar kan leda till att vissa kunder tror att fragmentering sker eller att en stor MTU används när det inte är det. Med LSO kan Ethernet-kortet annonsera en större maximal segment storlek (MSS) till TCP/IP-stacken för att skapa ett större TCP-paket. Hela den icke segmenterade ramen vidarebefordras sedan till Ethernet-kortet och visas i en paket avbildning som utförs på den virtuella datorn. Men paketet kommer att delas upp i många mindre ramar av Ethernet-kortet, enligt Ethernet-kortets MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>Skalnings-och PMTUD för TCP MSS-fönster

#### <a name="tcp-maximum-segment-size"></a>Maximal segment storlek för TCP

TCP maximal segment storlek (MSS) är en inställning som begränsar TCP-segmentens storlek, vilket förhindrar fragmentering av TCP-paket. Operativ system använder vanligt vis den här formeln för att ange MSS:

`MSS = MTU - (IP header size + TCP header size)`

IP-huvudet och TCP-huvudet är 20 byte vardera, eller 40 byte totalt. Det innebär att ett gränssnitt med en MTU på 1 500 har en MSS på 1 460. Men MSS kan konfigureras.

Den här inställningen är överenskommen i den tre tre-vägs hand skakningen när en TCP-session har kon figurer ATS mellan en källa och ett mål. Båda sidorna skickar ett MSS-värde och den nedre av de två används för TCP-anslutningen.

Tänk på att käll-och mål-MTU inte är de enda faktorer som avgör MSS-värdet. Mellanliggande nätverks enheter, t. ex. VPN-gatewayer, inklusive Azure VPN Gateway, kan justera MTU oberoende av källa och mål för att säkerställa optimal nätverks prestanda.

#### <a name="path-mtu-discovery"></a>Sökväg till MTU-identifiering

MSS förhandlas, men det är inte säkert att den faktiska MSS som kan användas anges. Detta beror på att andra nätverks enheter i sökvägen mellan källan och målet kan ha ett lägre MTU-värde än källan och målet. I det här fallet släpper enheten vars MTU är mindre än paketet. Enheten skickar tillbaka en ICMP-fragmentering som krävs (typ 3, kod 4) meddelande som innehåller dess MTU. Detta ICMP-meddelande gör att käll värden kan minska sökvägen till MTU. Processen kallas för sökvägs-MTU-identifiering (PMTUD).

PMTUD-processen är ineffektiv och påverkar nätverks prestanda. När paket skickas som överskrider en nätverks Sök vägs MTU måste paketen återsändas med en lägre MSS. Om avsändaren inte får meddelandet ICMP-fragmentering som krävs, kanske på grund av en nätverks brand vägg i sökvägen (kallas vanligt vis en *PMTUD Blackhole*), så vet inte avsändaren att den behöver minska MSS och kommer kontinuerligt att överföra paketet. Därför rekommenderar vi inte att du ökar den virtuella Azure-datorns MTU.

#### <a name="vpn-and-mtu"></a>VPN och MTU

Om du använder virtuella datorer som utför inkapsling (t. ex. IPsec-VPN) finns det några ytterligare överväganden angående paket storlek och MTU. VPN lägger till fler huvuden i paket, vilket ökar paket storleken och kräver en mindre MSS.

För Azure rekommenderar vi att du ställer in TCP MSS ihopfogning till 1 350 byte och tunnel Interface MTU till 1 400. Mer information finns på [sidan VPN-enheter och IPSec/IKE-parametrar](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Svars tid, Tur och retur-tid och TCP-Window skalning

#### <a name="latency-and-round-trip-time"></a>Svars tid och tids fördröjning

Nätverks fördröjningen styrs av hastigheten på ljuset över ett fiber optiskt nätverk. Nätverks data flödet i TCP styrs också effektivt av tiden för tur och retur mellan två nätverks enheter.

| Väg | Avstånd | Enkelriktad tid | RTT |
| ----- | -------- | ------------ | --- |
|New York till San Francisco|4 148 km|21 MS|42 MS|
|New York till London|5 585 km|28 MS|56 MS|
|New York till Sydney|15 993 km|80 MS|160 MS|

I den här tabellen visas det räta linje avståndet mellan två platser. I nätverk är avståndet vanligt vis längre än det räta linje avståndet. Här är en enkel formel för att beräkna lägsta efter klickning som styrs av ljus hastigheten:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

Du kan använda 200 för att sprida hastigheten. Detta är avståndet, i kilo meter, som lyser i 1 millisekunder.

Vi tar med New York till San Francisco som ett exempel. Det raka linje avståndet är 4 148 km. När du ansluter det värdet till ekvationen får vi följande:

`Minimum RTT = 2 * (4,148 / 200)`

Resultatet av formeln är i millisekunder.

Om du vill få bästa möjliga nätverks prestanda är det logiska alternativet att välja mål med det kortaste avståndet mellan dem. Du bör också utforma ditt virtuella nätverk för att optimera trafik vägen och minska svars tiden. Mer information finns i avsnittet "nätverks design överväganden" i den här artikeln.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Tids fördröjning för svars tider och tids fördröjningar i TCP

Svars tiden för fördröjningen har direkt påverkan på högsta TCP-dataflöde. I TCP-protokollet är *fönster storlek* den maximala mängd trafik som kan skickas via en TCP-anslutning innan avsändaren måste ta emot bekräftelse från mottagaren. Om TCP-MSS är inställt på 1 460 och TCP-fönstrets storlek är inställt på 65 535 kan avsändaren skicka 45-paket innan den måste ta emot bekräftelse från mottagaren. Om avsändaren inte får bekräftelse, kommer data att skickas igen. Här är formeln:

`TCP window size / TCP MSS = packets sent`

I det här exemplet är 65 535/1 460 avrundat uppåt till 45.

Detta "väntar på bekräftelse", en mekanism för att säkerställa tillförlitlig leverans av data, är det som gör att det påverkar TCP-dataflödet. Den längre avsändaren väntar på bekräftelse, desto längre tid måste vänta innan data skickas.

Här är formeln för att beräkna det maximala data flödet för en enskild TCP-anslutning:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Den här tabellen visar det högsta antalet megabyte/per sekund för en enda TCP-anslutning. (För läsbarhet används megabyte för mått enheten.)

| TCP-fönster storlek (byte) | Svars tid (MS) | Maximalt data flöde i MB/sekund | Maximalt megabits-/sekund data flöde |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|1|65,54|524,29|
|65 535|30|2,18|17,48|
|65 535|60|1.09|8,74|
|65 535|90|.73|5,83|
|65 535|120|.55|4,37|

Om paketen förloras kommer det maximala data flödet för en TCP-anslutning att minskas medan avsändaren skickar om data som redan har skickats.

#### <a name="tcp-window-scaling"></a>Skalning av TCP-fönster

TCP Window Scaling är en teknik som dynamiskt ökar TCP-fönstrets storlek för att tillåta att mer data skickas innan en bekräftelse krävs. I det föregående exemplet skulle 45 paket skickas innan en bekräftelse krävdes. Om du ökar antalet paket som kan skickas innan en bekräftelse krävs minskar du antalet gånger som en avsändare väntar på bekräftelse, vilket ökar det maximala TCP-dataflödet.

Den här tabellen illustrerar dessa relationer:

| TCP-fönster storlek (byte) | Svars tid (MS) | Maximalt data flöde i MB/sekund | Maximalt megabits-/sekund data flöde |
| ----------------------- | ---------------- | ---------------------------------- | --------------------------------- |
|65 535|30|2,18|17,48|
|131 070|30|4,37|34,95|
|262 140|30|8,74|69,91|
|524 280|30|17,48|139,81|

Men TCP-Huvudvärdet för TCP Window-storlek är bara 2 byte långt, vilket innebär att det maximala värdet för ett mottagnings fönster är 65 535. För att öka den maximala fönster storleken introducerades en skalnings faktor för TCP-fönster.

Skalnings faktorn är också en inställning som du kan konfigurera i ett operativ system. Här är formeln för att beräkna TCP-fönstrets storlek med hjälp av skalnings faktorer:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Här är beräkningen för en fönster skalnings faktor på 3 och en fönster storlek på 65 535:

`65,535 \* (2^3) = 262,140 bytes`

En skalnings faktor på 14 resulterar i en TCP-fönster storlek på 14 (den högsta tillåtna förskjutningen). TCP-fönstrets storlek är 1 073 725 440 byte (8,5 Gigabit).

#### <a name="support-for-tcp-window-scaling"></a>Stöd för skalning av TCP-fönster

Windows kan ange olika skalnings faktorer för olika anslutnings typer. (Klasser av anslutningar är data Center, Internet och så vidare.) Du använder `Get-NetTCPConnection` PowerShell-kommandot för att Visa anslutnings typen fönster skalning:

```powershell
Get-NetTCPConnection
```

Du kan använda `Get-NetTCPSetting` PowerShell-kommandot för att visa värdena för varje klass:

```powershell
Get-NetTCPSetting
```

Du kan ställa in den inledande TCP-fönster storleken och TCP-skalnings faktorn i Windows med hjälp av `Set-NetTCPSetting` PowerShell-kommandot. Mer information finns i  [set-NetTCPSetting](/powershell/module/nettcpip/set-nettcpsetting).

```powershell
Set-NetTCPSetting
```

Detta är de effektiva TCP-inställningarna för `AutoTuningLevel` :

| AutoTuningLevel | Skalnings faktor | Skalnings multiplikator | Formel för att<br/>beräkna maximal fönster storlek |
| --------------- | -------------- | ------------------ | -------------------------------------------- |
|Inaktiverad|Inga|Inga|Fönster storlek|
|Begränsade|4|2 ^ 4|Fönster storlek * (2 ^ 4)|
|Hög begränsad|2|2 ^ 2|Fönster storlek * (2 ^ 2)|
|Normal|8|2 ^ 8|Fönster storlek * (2 ^ 8)|
|Experimentell|14|2 ^ 14|Fönster storlek * (2 ^ 14)|

De här inställningarna är mest sannolika att påverka TCP-prestanda, men kom ihåg att många andra faktorer på Internet, utanför Azures kontroll, kan påverka TCP-prestanda.

#### <a name="increase-mtu-size"></a>Öka MTU-storlek

Eftersom en större MTU innebär en större MSS kanske du undrar om du ökar TCP-prestandan genom att öka MTU-storleken. Förmodligen inte. Det finns funktioner för-och nack delar med paket storlek än bara TCP-trafik. Som vi nämnt tidigare är de viktigaste faktorerna som påverkar TCP-dataflödets prestanda TCP-fönster storlek, paket förlust och för gång.

> [!IMPORTANT]
> Vi rekommenderar inte att Azure-kunder ändrar standard-MTU-värdet på virtuella datorer.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Accelererat nätverk och skalning på mottagar Sidan

#### <a name="accelerated-networking"></a>Snabbare nätverk

Nätverks funktionerna i den virtuella datorn har historiskt sin processor intensiv både på den virtuella gäst datorn och hypervisorn/värden. Varje paket som överförs via värden bearbetas i program varan av värd processorn, inklusive alla virtuella nätverks inkapslingar och avkapsling. Ju mer trafik som går via värden, desto högre processor belastning. Och om värd processorn är upptagen med andra åtgärder påverkar det även nätverks data flöde och svars tider. Azure löser problemet med accelererat nätverk.

Accelererat nätverk ger konsekvent ultralow nätverks fördröjning via den interna programmerbara maskin varan för Azure och tekniker som SR-IOV. Accelererat nätverk flyttar mycket av den Azure-programdefinierade nätverks stacken från processorerna och till FPGA-baserade SmartNICs. Den här ändringen gör det möjligt för slutanvändare att frigöra beräknings cykler, vilket innebär mindre belastning på den virtuella datorn, vilket minskar Darr och inkonsekvens i svars tid. Med andra ord kan prestanda vara mer deterministisk.

Accelererat nätverk förbättrar prestanda genom att låta den virtuella gäst datorn kringgå värden och upprätta en Datapath direkt med en värds SmartNIC. Här är några fördelar med accelererat nätverk:

- **Lägre latens/högre paket per sekund (PPS)**: om du tar bort den virtuella växeln från Datapath elimineras de tids paket som ägnas åt värden för princip bearbetning och ökar antalet paket som kan bearbetas på den virtuella datorn.

- **Reducerade Darr**: bearbetning av virtuell växel beror på den mängd princip som måste tillämpas och arbets belastningen för den processor som utför bearbetningen. Genom att avlasta den tvingande principen till maskin varan tar du bort den variabiliteten genom att leverera paket direkt till den virtuella datorn, vilket eliminerar kommunikationen mellan värd och virtuell dator och alla program avbrott och kontext växlar.

- **Minskad processor användning**: om du kringgår den virtuella växeln på värden går det snabbare att bearbeta nätverks trafiken.

Om du vill använda accelererat nätverk måste du uttryckligen aktivera det på varje aktuell virtuell dator. Instruktioner finns i [skapa en virtuell Linux-dator med accelererat nätverk](./create-vm-accelerated-networking-cli.md) .

#### <a name="receive-side-scaling"></a>Skalning på mottagar Sidan

För skalning på mottagar sidan (RSS) är en teknik för nätverks driv rutiner som distribuerar mottagandet av nätverks trafiken mer effektivt genom att distribuera mottagnings bearbetning över flera processorer i ett system med flera processorer. RSS gör det möjligt för ett system att bearbeta mer mottagen trafik eftersom den använder alla tillgängliga processorer i stället för bara en. En mer teknisk diskussion om RSS finns i [Introduktion till skalning på mottagar sidan](/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

För att få bästa möjliga prestanda när accelererat nätverk är aktiverat på en virtuell dator måste du aktivera RSS. RSS kan även ge förmåner på virtuella datorer som inte använder accelererat nätverk. En översikt över hur du avgör om RSS är aktiverat och hur du aktiverar det finns i [optimera nätverks data flöde för virtuella Azure-datorer](./virtual-network-optimize-network-bandwidth.md).

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP-TIME_WAIT och TIME_WAIT Assassination

TCP TIME_WAIT är en annan gemensam inställning som påverkar nätverkets och programmets prestanda. På upptagna virtuella datorer som öppnar och stänger många Sockets, antingen som klienter eller som servrar (käll-IP: käll-port + mål-IP: målport), under normal drift av TCP, kan en socket i ett TIME_WAIT tillstånd under en längre tid. TIME_WAITs tillstånd är avsett att tillåta att ytterligare data levereras på en socket innan de stängs. TCP/IP-stackar förhindrar vanligt vis åter användning av en socket genom att tyst släppa klientens TCP SYN-paket.

Hur lång tid en socket är i TIME_WAIT kan konfigureras. Det kan vara mellan 30 sekunder och 240 sekunder. Sockets är en begränsad resurs och antalet socketar som kan användas vid en bestämd tidpunkt kan konfigureras. (Antalet tillgängliga Sockets är normalt cirka 30 000.) Om de tillgängliga socketarna används, eller om klienter och servrar har fel matchnings TIME_WAIT inställningar, och en virtuell dator försöker återanvända en socket i ett TIME_WAIT tillstånd, kommer nya anslutningar inte att fungera eftersom TCP SYN-paketen ignoreras tyst.

Värdet för port intervall för utgående Sockets kan vanligt vis konfigureras i TCP/IP-stacken i ett operativ system. Samma sak gäller för TCP TIME_WAIT inställningar och åter användning av socket. Att ändra dessa siffror kan eventuellt förbättra skalbarheten. Men beroende på situationen kan dessa ändringar orsaka problem med samverkan. Du bör vara försiktig om du ändrar dessa värden.

Du kan använda TIME_WAIT Assassination för att hantera den här skalnings begränsningen. TIME_WAIT Assassination gör det möjligt att återanvända en socket i vissa situationer, t. ex. När sekvensnumret i IP-paketet för den nya anslutningen överskrider sekvensnumret för det sista paketet från den tidigare anslutningen. I det här fallet kommer operativ systemet att tillåta att den nya anslutningen upprättas (den kommer att acceptera den nya SYNen/ACK) och tvinga den tidigare anslutningen som var i ett TIME_WAITs tillstånd. Den här funktionen stöds på virtuella Windows-datorer i Azure. Om du vill veta mer om stöd i andra virtuella datorer kan du kontakta OS-leverantören.

Information om hur du konfigurerar TCP-TIME_WAIT inställningar och käll port intervall finns i [inställningar som kan ändras för att förbättra nätverks prestanda](/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuella nätverks faktorer som kan påverka prestanda

### <a name="vm-maximum-outbound-throughput"></a>Maximalt utgående data flöde för virtuell dator

Azure tillhandahåller en mängd olika storlekar och typer av virtuella datorer, var och en med en annan blandning av prestanda funktionerna. En av dessa funktioner är nätverks data flöde (eller bandbredd) som mäts i megabit per sekund (Mbit/s). Eftersom virtuella datorer finns på delad maskin vara måste nätverks kapaciteten delas relativt mellan de virtuella datorerna med samma maskin vara. Större virtuella datorer allokeras mer bandbredd än mindre virtuella datorer.

Nätverks bandbredden som allokeras till varje virtuell dator mäts på utgående trafik (utgående trafik) från den virtuella datorn. All nätverks trafik som lämnar den virtuella datorn räknas till den tilldelade gränsen, oavsett destination. Om till exempel en virtuell dator har en gräns på 1 000 Mbit/s, gäller den gränsen om den utgående trafiken är avsedd för en annan virtuell dator i samma virtuella nätverk eller en utanför Azure.

Ingress är inte avgiftsbelagda eller begränsade direkt. Men det finns andra faktorer, till exempel processor-och lagrings gränser, som kan påverka en virtuell dators möjlighet att bearbeta inkommande data.

Accelererat nätverk är utformat för att förbättra nätverks prestanda, inklusive svars tid, data flöde och processor belastning. Accelererat nätverk kan förbättra data flödet för en virtuell dator, men det kan bara göras till den virtuella datorns allokerade bandbredd.

Virtuella Azure-datorer har minst ett nätverks gränssnitt kopplat till sig. De kan ha flera. Bandbredden som tilldelas en virtuell dator är summan av all utgående trafik över alla nätverks gränssnitt som är anslutna till datorn. Med andra ord allokeras bandbredden för varje virtuell dator, oavsett hur många nätverks gränssnitt som är anslutna till datorn.

Förväntat utgående data flöde och antalet nätverks gränssnitt som stöds av varje VM-storlek beskrivs i [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Om du vill se maximalt data flöde väljer du en typ, som **generell användning**, och hittar sedan avsnittet om storleks serien på den resulterande sidan (till exempel "Dv2-serien"). För varje serie finns det en tabell som ger nätverks specifikationer i den sista kolumnen, som kallas "maximalt antal nätverkskort/förväntad nätverks bandbredd (Mbit/s)."

Data flödes gränsen gäller för den virtuella datorn. Data flödet påverkas inte av följande faktorer:

- **Antal nätverks gränssnitt**: bandbredds gränsen gäller summan av all utgående trafik från den virtuella datorn.

- **Accelererat nätverk**: även om den här funktionen kan vara till hjälp när du uppnår den publicerade gränsen ändras inte gränsen.

- **Trafik mål**: alla destinationer räknas mot utgående gräns.

- **Protokoll**: all utgående trafik över alla protokoll räknas mot gränsen.

Mer information finns i [bandbredd för virtuella dator nätverk](./virtual-machine-network-throughput.md).

### <a name="internet-performance-considerations"></a>Överväganden för Internet prestanda

Som vi diskuterat i den här artikeln kan faktorer på Internet och utanför kontrollen av Azure påverka nätverks prestanda. Här följer några av dessa faktorer:

- **Svars tid: fördröjningen** mellan två destinationer kan påverkas av problem i mellanliggande nätverk, av trafik som inte tar vägen "kortaste", och efter optimala peering-sökvägar.

- **Paket förlust**: paket förlust kan orsakas av överbelastning på nätverket, fysiska Sök vägs problem och underpresterande nätverks enheter.

- **MTU-storlek/fragmentering**: fragmentering längs sökvägen kan leda till fördröjningar i data som anländer eller i paket som anländer, vilket kan påverka leveransen av paket.

Traceroute är ett utmärkt verktyg för att mäta egenskaper för nätverks prestanda (till exempel paket förlust och svars tid) tillsammans med varje nätverks Sök väg mellan en käll enhet och en mål enhet.

### <a name="network-design-considerations"></a>Nätverks design överväganden

Tillsammans med de överväganden som beskrivs tidigare i den här artikeln kan topologin för ett virtuellt nätverk påverka nätverkets prestanda. Till exempel en nav-och-eker-design som används för att sänka trafik till ett virtuellt nätverk med en hubb kommer att introducera nätverks fördröjning, vilket påverkar övergripande nätverks prestanda.

Antalet nätverks enheter som nätverks trafiken passerar genom kan också påverka den totala svars tiden. I en nav-och-eker-design, om trafiken passerar genom en virtuell eker-nätverks installation och en nav-installation före överföring till Internet, kan virtuella nätverks enheter introducera svars tid.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-regioner, virtuella nätverk och svars tid

Azure-regioner består av flera data Center som finns inom ett allmänt geografiskt område. Dessa data Center kanske inte är fysiskt bredvid varandra. I vissa fall är de åtskilda med upp till 10 kilo meter. Det virtuella nätverket är ett logiskt överlägg ovanpå det fysiska Azure-datacenter-nätverket. Ett virtuellt nätverk kräver ingen speciell nätverkstopologi i data centret.

Till exempel kan två virtuella datorer som finns i samma virtuella nätverk och undernät finnas i olika rack, rader eller till och med data Center. De kan avgränsas med fötter av fiber optisk kabel eller med kilo meter av fiber optisk kabel. Den här variationen kan leda till variabel latens (några skillnader i millisekunder) mellan olika virtuella datorer.

Den geografiska placeringen av virtuella datorer och den potentiella resultat fördröjningen mellan två virtuella datorer kan påverkas av konfigurationen av tillgänglighets uppsättningar och Tillgänglighetszoner. Men avståndet mellan data Center i en region är regions-/regionsspecifika och främst påverkade av data Center sto pol Ogin i regionen.

### <a name="source-nat-port-exhaustion"></a>Källa för NAT-Port

En distribution i Azure kan kommunicera med slut punkter utanför Azure på det offentliga Internet och/eller i det offentliga IP-utrymmet. När en instans initierar en utgående anslutning, mappar Azure dynamiskt den privata IP-adressen till en offentlig IP-adress. När du har skapat den här mappningen kan returnera trafik för utgående sitta flöde också komma åt den privata IP-adressen som flödet kommer från.

För varje utgående anslutning måste Azure Load Balancer underhålla mappningen under en viss tids period. Med den här typen av Azure-klient kan du behålla den här mappningen för varje utgående flöde för varje virtuell dator som kan vara resurs intensiv. Därför finns det gränser som är inställda och baserade på konfigurationen av Azure-Virtual Network. Eller, för att säga att en virtuell Azure-dator bara kan göra ett visst antal utgående anslutningar vid en viss tidpunkt. När de här gränserna uppnås kan den virtuella datorn inte göra fler utgående anslutningar.

Men det här beteendet kan konfigureras. Mer information om SNAT och SNAT port överbelastning finns i [den här artikeln](../load-balancer/load-balancer-outbound-connections.md).

## <a name="measure-network-performance-on-azure"></a>Mät nätverks prestanda på Azure

Ett antal prestanda som är maximala i den här artikeln är relaterade till nätverks fördröjningen/tur och retur-tiden mellan två virtuella datorer. Det här avsnittet innehåller några förslag på hur du testar svars tid/sökmetod och hur du testar TCP-prestanda och prestanda för virtuella dator nätverk. Du kan justera och testa TCP/IP-och nätverks värden som beskrivs tidigare med hjälp av de metoder som beskrivs i det här avsnittet. Du kan koppla latens-, MTU-, MSS-och Window-värden till de beräkningar som tillhandahölls tidigare och jämföra teoretiska Max värdena med faktiska värden som du anser under testningen.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mäta tids fördröjning för svar och paket förlust

TCP-prestanda är kraftigt beroende av sökslag och paket förlust. PING-verktyget som är tillgängligt i Windows och Linux är det enklaste sättet att mäta efter-och paket förlust. Utdata från PING visar den minsta/högsta/genomsnittliga svars tiden mellan en källa och ett mål. Det visar även paket förlust. PING använder ICMP-protokollet som standard. Du kan använda PsPing för att testa TCP-inkörning. Mer information finns i [PsPing](/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mäta det faktiska data flödet för en TCP-anslutning

NTttcp är ett verktyg för att testa TCP-prestandan för en virtuell Linux-eller Windows-dator. Du kan ändra olika TCP-inställningar och sedan testa fördelarna med NTttcp. Mer information finns i följande resurser:

- [Test av bandbredd/data flöde (NTttcp)](./virtual-network-bandwidth-testing.md)

- [NTttcp-verktyg](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mäta faktisk bandbredd för en virtuell dator

Du kan testa prestanda för olika VM-typer, accelererade nätverk och så vidare med hjälp av ett verktyg som kallas iPerf. iPerf är också tillgängligt i Linux och Windows. iPerf kan använda TCP eller UDP för att testa det övergripande nätverks genomflödet. iPerf TCP-genomflöde påverkas av de faktorer som beskrivs i den här artikeln (t. ex. latens och önskad sökpunkt). Så UDP kan ge bättre resultat om du bara vill testa maximalt data flöde.

Mer information finns i de här artiklarna:

- [Felsöka ExpressRoute Network Performance](../expressroute/expressroute-troubleshooting-network-performance.md)

- [Verifiera VPN-dataflöde till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-validate-throughput-to-vnet.md)

### <a name="detect-inefficient-tcp-behaviors"></a>Identifiera ineffektiva TCP-beteenden

I paket fångster kan Azure-kunder se TCP-paket med TCP-flaggor (SÄCKAR, DUPLICERA ACK, resändning och snabb återöverföring) som kan tyda på problem med nätverks prestanda. Dessa paket indikerar särskilt nätverks ineffektivhet som orsakas av paket förlust. Men paket förlust orsakas inte nödvändigt vis av Azures prestanda problem. Prestanda problem kan bero på problem med program, problem med operativ system eller andra problem som inte är direkt relaterade till Azure-plattformen.

Tänk också på att vissa återöverföringar och identiska bekräftelser är normala i ett nätverk. TCP-protokoll är byggda för att vara pålitliga. Bevis på dessa TCP-paket i en paket fångst indikerar inte nödvändigt vis ett system problem, om de inte är för stora.

Dessa paket typer anger dock att TCP-dataflöde inte uppnår sin maximala prestanda, av skäl som beskrivs i andra avsnitt i den här artikeln.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om prestanda justering av TCP/IP-prestanda för virtuella Azure-datorer kan du läsa om andra saker att tänka på när du [planerar virtuella nätverk](./virtual-network-vnet-plan-design-arm.md) eller [Lär dig mer om att ansluta och konfigurera virtuella nätverk](./index.yml).