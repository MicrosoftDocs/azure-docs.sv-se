---
title: Prestandaguide för Azure SignalR Service
description: En översikt över prestanda och benchmark för Azure SignalR-tjänsten. Viktiga mått att tänka på när du planerar kapaciteten.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "74157668"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestandaguide för Azure SignalR Service

En av de främsta fördelarna med att använda Azure SignalR service är det enkelt att skala signal program. I ett storskaligt scenario är prestanda en viktig faktor. 

I den här guiden ska vi införa faktorer som påverkar signal programmets prestanda. Vi beskriver typiska prestanda i olika användnings fall. I slutet presenterar vi den miljö och de verktyg som du kan använda för att generera en prestanda rapport.

## <a name="term-definitions"></a>Term definitioner

*Inkommande*: inkommande meddelande till Azure SignalR-tjänsten.

*Utgående*: utgående meddelande från Azure SignalR-tjänsten.

*Bandbredd*: den totala storleken på alla meddelanden i 1 sekund.

*Standard läge*: standard arbets läget när en Azure SignalR-tjänstinstans skapades. Azure SignalR-tjänsten förväntar sig att app servern upprättar en anslutning till den innan den accepterar eventuella klient anslutningar.

*Läge utan server*: ett läge där Azure SignalR service bara accepterar klient anslutningar. Ingen server anslutning tillåts.

## <a name="overview"></a>Översikt

Azure SignalR service definierar sju standard nivåer för olika prestanda kapaciteter. I den här hand boken besvaras följande frågor:

-   Vad är den typiska Azure SignalR service-prestandan för varje nivå?

-   Uppfyller Azure SignalR-tjänsten Mina krav för meddelande data flöde (till exempel skicka 100 000 meddelanden per sekund)?

-   Vilken nivå är lämplig för mig för mitt speciella scenario? Eller hur kan jag välja rätt nivå?

-   Vilken typ av app server (VM-storlek) är lämplig för mig? Hur många av dem ska jag distribuera?

För att besvara dessa frågor ger den här guiden först en övergripande förklaring av de faktorer som påverkar prestandan. Den visar sedan de maximalt inkommande och utgående meddelandena för varje nivå för vanliga användnings fall: **eko**, **sändning**, **Skicka till grupp** och **Skicka till anslutning** (peer-to-peer-samtal).

Den här guiden beskriver inte alla scenarier (och olika användnings fall, meddelande storlekar, meddelanden som skickar mönster och så vidare). Men det finns några metoder som hjälper dig att:

- Utvärdera ditt ungefärliga krav för inkommande eller utgående meddelanden.
- Hitta rätt nivåer genom att kontrol lera tabellen Performance.

## <a name="performance-insight"></a>Prestanda insikter

I det här avsnittet beskrivs metoder för prestanda utvärdering och en lista över alla faktorer som påverkar prestandan. I slutet innehåller den metoder som hjälper dig att utvärdera prestanda kraven.

### <a name="methodology"></a>Metodik

*Data flöde* och *svars tider* är två typiska aspekter av prestanda kontroll. För Azure SignalR-tjänsten har varje SKU-nivå sin egen data flödes begränsnings princip. Principen definierar *högsta tillåtna data flöde (inkommande och utgående bandbredd)* som maximalt uppnått data flöde när 99 procent av meddelanden har svars tid som är mindre än 1 sekund.

Latens är det tidsintervall från anslutningen som skickar meddelandet om att ta emot svarsmeddelandet från Azure SignalR-tjänsten. Vi tar **eko** som exempel. Varje klient anslutning lägger till en tidstämpel i meddelandet. App Server-hubben skickar det ursprungliga meddelandet tillbaka till klienten. Det innebär att spridnings fördröjning enkelt beräknas av varje klient anslutning. Tidstämpeln bifogas för varje meddelande i **sändning**, **Skicka till grupp** och **Skicka till anslutning**.

För att simulera tusentals samtidiga klient anslutningar skapas flera virtuella datorer i ett virtuellt privat nätverk i Azure. Alla dessa virtuella datorer ansluter till samma Azure SignalR-tjänstinstans.

I standard läget för Azure SignalR-tjänsten distribueras virtuella dator servrar i samma virtuella privata nätverk som klientens virtuella datorer. Alla virtuella klient datorer och virtuella App Server-datorer distribueras i samma nätverk av samma region för att undvika fördröjning mellan regioner.

### <a name="performance-factors"></a>Prestanda faktorer

Teoretiskt sett är Azure SignalR service-kapaciteten begränsad av beräknings resurser: processor, minne och nätverk. Till exempel orsakar fler anslutningar till Azure SignalR service att tjänsten använder mer minne. För större meddelande trafik (till exempel varje meddelande är större än 2 048 byte) måste Azure Signaling-tjänsten spendera fler CPU-cykler för att bearbeta trafiken. Samtidigt utgör Azure Network-bandbredden också en gräns för maximal trafik.

Transport typen är en annan faktor som påverkar prestandan. De tre typerna är [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-skickat – händelse](https://en.wikipedia.org/wiki/Server-sent_events)och [lång avsökning](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket är ett dubbelriktat kommunikations protokoll med dubbelriktad duplex över en enda TCP-anslutning. Server-skickat – händelse är ett enkelriktat protokoll för att skicka meddelanden från server till klient. Long-Polling kräver att klienterna regelbundet avsöker information från servern via en HTTP-begäran. För samma API har WebSocket den bästa prestandan, servern som skickas – händelsen långsammare och Long-Polling är långsammast. Azure SignalR service rekommenderar WebSocket som standard.

Kostnaden för vidarebefordring av meddelanden begränsar också prestanda. Azure SignalR service spelar en roll som en Message router, som dirigerar meddelandet från en uppsättning klienter eller servrar till andra klienter eller servrar. Ett annat scenario eller API kräver en annan princip för routning. 

För **ECHO** skickar klienten ett meddelande till sig själv, och routningstjänsten är också själva. Det här mönstret har den lägsta produktionskostnaden. Men för **sändning**, **Skicka till grupp** och **Skicka till anslutning** måste Azure Signaling-tjänsten leta upp mål anslutningarna via den interna distribuerade data strukturen. Den här extra bearbetningen använder mer processor, minne och nätverks bandbredd. Därför är prestanda långsammare.

I standard läget kan app-servern också bli en Flask hals för vissa scenarier. Azure SignalR SDK måste anropa hubben, medan den upprätthåller en Live-anslutning med varje klient genom pulsslags signaler.

I server utan läge skickar klienten ett meddelande via HTTP post, vilket inte är lika effektivt som WebSocket.

En annan faktor är protokoll: JSON och [MessagePack](https://msgpack.org/index.html). MessagePack har mindre storlek och levereras snabbare än JSON. MessagePack kanske inte ger bättre prestanda, men. Prestandan för Azure SignalR-tjänsten är inte känslig för protokoll eftersom den inte avkodar meddelande nytto lasten vid vidarebefordran av meddelanden från klienter till servrar eller vice versa.

I sammanfattning påverkar följande faktorer den inkommande och utgående kapaciteten:

-   SKU-nivå (CPU/minne)

-   Antal anslutningar

-   Meddelandestorlek

-   Antal meddelande utskick

-   Transport typ (WebSocket, Server-skickat-händelse eller lång avsökning)

-   Användnings Falls scenario (vidarebefordring av kostnader)

-   App Server-och tjänst anslutningar (i server läge)


### <a name="finding-a-proper-sku"></a>Hitta en korrekt SKU

Hur kan du utvärdera den inkommande/utgående kapaciteten eller hitta vilken nivå som passar för ett speciellt användnings fall?

Anta att app server är tillräckligt kraftfull och inte är prestanda Flask halsen. Kontrol lera sedan den högsta inkommande och utgående bandbredden för varje nivå.

#### <a name="quick-evaluation"></a>Snabb utvärdering

Vi fören klar utvärderingen först genom att använda vissa standardinställningar: 

- Transport typen är WebSocket.
- Meddelande storleken är 2 048 byte.
- Ett meddelande skickas varje 1 sekund.
- Azure SignalR service är i standard läge.

Varje nivå har sin egen maximala inkommande bandbredd och utgående bandbredd. En smidig användar upplevelse garanterar inte att den inkommande eller utgående anslutningen överskrider gränsen.

**ECHO** ger maximal inkommande bandbredd eftersom den har den lägsta produktionskostnaden. **Sändning** definierar den maximala bandbredden för utgående meddelanden.

Överskrider *inte* de markerade värdena i följande två tabeller.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Inkommande bandbredd** | **2 Mbit/s**    | **4 Mbit/s**    | **10 Mbit/s**   | **20 Mbit/s**    | **40 Mbit/s**    | **100 Mbit/s**   | **200 Mbit/s**    |
| Utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 Mbit/s   | 40 Mbit/s   | 100 Mbit/s  | 200 Mbit/s   |


|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande bandbredd  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Utgående bandbredd** | **4 Mbit/s**    | **8 Mbit/s**    | **20 Mbit/s**    | **40 Mbit/s**    | **80 Mbit/s**    | **200 Mbit/s**    | **400 Mbit/s**   |

*Inkommande bandbredd* och *utgående bandbredd* är total meddelande storlek per sekund.  Här följer formlerna för dem:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: antalet anslutningar som skickar meddelandet.

- *outboundConnections*: antalet anslutningar som tar emot meddelandet.

- *messageSize*: storleken på ett enskilt meddelande (genomsnittligt värde). Ett litet meddelande som är mindre än 1 024 byte har en prestanda påverkan som liknar ett meddelande med 1 024 byte.

- *sendInterval*: tiden för att skicka ett meddelande. Vanligt vis är det 1 sekund per meddelande, vilket innebär att skicka ett meddelande varje sekund. Ett mindre intervall innebär att skicka fler meddelanden under en tids period. Till exempel betyder 0,5 sekunder per meddelande att skicka två meddelanden varje sekund.

- *Anslutningar*: det maximala tröskelvärdet för Azure SignalR service för varje nivå. Om anslutnings numret ökar ytterligare kommer det att påverkas av anslutnings begränsningen.

#### <a name="evaluation-for-complex-use-cases"></a>Utvärdering för komplexa användnings fall

##### <a name="bigger-message-size-or-different-sending-rate"></a>Större meddelande storlek eller annan sändnings hastighet

Det verkliga användnings fallet är mer komplicerat. Det kan skicka ett meddelande som är större än 2 048 byte, eller så är sändnings hastigheten inte ett meddelande per sekund. Låt oss ta Unit100's broadcast som ett exempel för att hitta hur du ska utvärdera dess prestanda.

I följande tabell visas ett verkligt användnings fall av **sändningen**. Men storleken på meddelandet, antalet anslutningar och sändnings hastigheten för meddelanden skiljer sig från vad vi antog i föregående avsnitt. Frågan är hur vi kan härleda något av dessa objekt (meddelande storlek, antal anslutningar eller sändnings hastighet) om vi bara känner till två av dem.

| Sändning  | Meddelandestorlek | Inkommande meddelanden per sekund | Anslutningar | Skicka intervall |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 SEK                      |
| 2 | 256 kB               | 1                        | 8,000       | 5 SEK                      |

Följande formel är lätt att härleda baserat på föregående formel:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

För Unit100 är den maximala utgående bandbredden 400 MB från föregående tabell. För en meddelande storlek på 20 KB ska det högsta antalet utgående anslutningar vara 400 MB \* 5/20 KB = 100 000, som matchar det verkliga värdet.

##### <a name="mixed-use-cases"></a>Blandade användnings fall

Det verkliga användnings fallet blandar vanligt vis fyra grundläggande användnings fall: **eko**, **sändning**, **Skicka till grupp** och **Skicka till anslutning**. Den metod som du använder för att utvärdera kapaciteten är att:

1. Dela in blandade användnings fall i fyra grundläggande användnings fall.
1. Beräkna den maximala inkommande och utgående meddelande bandbredden med hjälp av föregående formler separat.
1. Summera bandbredds beräkningarna för att hämta den totala maximala inkommande/utgående bandbredden. 

Hämta sedan rätt nivå från tabellerna maximalt antal inkommande/utgående bandbredd.

> [!NOTE]
> För att skicka ett meddelande till hundratals eller tusentals små grupper, eller för tusentals klienter som skickar ett meddelande till varandra, blir produktionskostnaden dominerande. Ta hänsyn till den här effekten.

Kontrol lera att app-servern *inte* är Flask hals för användnings fall för att skicka ett meddelande till klienter. Följande "fallstudie" innehåller rikt linjer för hur många App-servrar du behöver och hur många Server anslutningar du ska konfigurera.

## <a name="case-study"></a>Fallstudie

Följande avsnitt går igenom fyra vanliga användnings fall för WebSocket-transport: **eko**, **sändning**, **Skicka till grupp** och **Skicka till anslutning**. I avsnittet visas den aktuella inkommande och utgående kapaciteten för Azure SignalR-tjänsten för varje scenario. Den förklarar också de huvudsakliga faktorer som påverkar prestanda.

I standard läget skapar app server fem Server anslutningar med Azure SignalR-tjänsten. App Server använder Azure SignalR service SDK som standard. I följande prestanda test resultat ökas Server anslutningarna till 15 (eller mer för sändning och sändning av ett meddelande till en stor grupp).

Olika användnings fall har olika krav för App-servrar. **Sändningen** behöver ett litet antal App-servrar. **Eko** eller **Skicka till anslutning** kräver många App-servrar.

I alla användnings fall är standard meddelande storleken 2 048 byte, och intervallet för meddelande sändning är 1 sekund.

### <a name="default-mode"></a>Standard läge

Klienter, webb program servrar och Azure SignalR-tjänsten ingår i standard läget. Varje klient står för en enda anslutning.

#### <a name="echo"></a>Echo

Först ansluter en webbapp till Azure SignalR-tjänsten. För det andra ansluter många klienter till webbappen, som omdirigerar klienterna till Azure SignalR-tjänsten med åtkomsttoken och slut punkten. Klienterna upprättar WebSocket-anslutningar med Azure SignalR-tjänsten.

När alla klienter upprättar anslutningar börjar de skicka ett meddelande som innehåller en tidsstämpel till den angivna hubben varje sekund. Hubben upprepar meddelandet tillbaka till den ursprungliga klienten. Varje klient beräknar svars tiden när den tar emot eko meddelandet igen.

I följande diagram finns 5 till 8 (röd markerad trafik) i en slinga. Slingan körs för en standard varaktighet (5 minuter) och hämtar statistiken för all meddelande fördröjning.

![Trafik för eko användnings fallet](./media/signalr-concept-performance/echo.png)

Beteendet för **ECHO** avgör att den högsta inkommande bandbredden är lika med den högsta utgående bandbredden. Mer information finns i följande tabell.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar                       | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Inkommande/utgående meddelanden per sekund | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Inkommande/utgående bandbredd | 2 Mbit/s   | 4 Mbit/s   | 10 Mbit/s  | 20 Mbit/s   | 40 Mbit/s   | 100 Mbit/s  | 200 Mbit/s   |

I det här användnings fallet anropar varje klient hubben som definierats i app-servern. Hubben anropar bara den metod som definierats på den ursprungliga klient sidan. Hubben är den mest lätta hubben för **eko**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Även för den här enkla hubben är trafikbelastningen på App-servern synlig när **inkommande meddelande** belastning ökar. För det här trafik trycket krävs många App-servrar för stora SKU-nivåer. I följande tabell visas antalet App-servrar för varje nivå.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient anslutningens nummer, meddelande storlek, hastighet för meddelande överföring, SKU-nivå och CPU/minne för program servern påverkar den övergripande prestandan för **eko**.

#### <a name="broadcast"></a>Sändning

När webbappen tar emot **meddelandet skickas broadcast**-meddelanden till alla klienter. Det finns fler klienter som kan sändas, desto mer meddelande trafik finns för alla klienter. Se följande diagram.

![Trafik för sändningens användnings fall](./media/signalr-concept-performance/broadcast.png)

Ett litet antal klienter sänder. Bandbredden för det inkommande meddelandet är liten, men den utgående bandbredden är enorma. Bandbredden för utgående meddelanden ökar när klient anslutningen eller broadcast-hastigheten ökar.

I följande tabell sammanfattas de maximala klient anslutningarna, antal inkommande/utgående meddelanden och bandbredd.

|     Sändning             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Utgående bandbredd | 4 Mbit/s   | 8 Mbit/s   | 20 Mbit/s   | 40 Mbit/s   | 80 Mbit/s   | 200 Mbit/s   | 400 Mbit/s   |

De sändnings klienter som skickar meddelanden är högst fyra. De behöver färre App-servrar jämfört med **ECHO** eftersom det inkommande meddelande beloppet är litet. Det finns två App-servrar för både SLA-och prestanda överväganden. Men du bör öka standard server anslutningarna för att undvika obalans, särskilt för Unit50 och Unit100.

|   Sändning      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka antalet Server anslutningar från 5 till 40 på varje app-server för att undvika eventuella obalanserade Server anslutningar till Azure SignalR-tjänsten.
>
> Klient anslutnings numret, meddelande storleken, antalet skickade meddelanden och SKU-nivån påverkar den övergripande prestandan för **sändning**.

#### <a name="send-to-group"></a>Skicka till grupp

Användnings fallet **Skicka till grupp** har ett liknande trafik mönster som **skickas**. Skillnaden är att när klienterna upprättar WebSocket-anslutningar med Azure SignalR-tjänsten måste de gå med i grupper innan de kan skicka ett meddelande till en speciell grupp. Följande diagram illustrerar trafikflödet.

![Trafik för användnings fall för sändning till grupp](./media/signalr-concept-performance/sendtogroup.png)

Grupp medlem och antal grupper är två faktorer som påverkar prestandan. För att förenkla analysen definierar vi två typer av grupper:

- **Liten grupp**: varje grupp har 10 anslutningar. Grupp numret är lika med (max antal anslutningar)/10. Om det till exempel finns 1 000 anslutnings antal för Unit1 har vi 1000/10 = 100 grupper.

- **Stor grupp**: grupp numret är alltid 10. Antalet grupp medlemmar är lika med (max antal anslutningar)/10. Om det till exempel finns 1 000 anslutnings antal för Unit1, har varje grupp 1000/10 = 100 medlemmar.

**Skicka till grupp** ger en produktionskostnad till Azure SignalR-tjänsten eftersom den måste hitta mål anslutningarna via en distribuerad data struktur. Kostnaden ökar när sändnings anslutningarna ökar.

##### <a name="small-group"></a>Liten grupp

Produktionskostnaden är viktig för att skicka meddelanden till många små grupper. Azure SignalR service-implementeringen träffar för närvarande en kostnads gräns för Unit50. Det går inte att lägga till mer processor och minne, så Unit100 kan inte förbättras ytterligare genom att utforma. Kontakta kund support om du behöver mer inkommande bandbredd.

|   Skicka till liten grupp     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000 | 100 000
| Antal grupp medlemmar        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Antal grupper               | 100   | 200   | 500    | 1 000  | 2 000  | 5 000  | 10 000 
| Inkommande meddelanden per sekund  | 200   | 400   | 1 000  | 2 500  | 4 000  | 7 000  | 7 000   |
| Inkommande bandbredd  | 400 KBps  | 800 KBps  | 2 Mbit/s     | 5 Mbit/s     | 8 Mbit/s     | 14 Mbit/s    | 14 Mbit/s     |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 25,000 | 40 000 | 70 000 | 70 000  |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 50 Mbit/s     | 80 Mbit/s    | 140 Mbit/s   | 140 Mbit/s    |

Många klient anslutningar anropar hubben, så app server-numret är också kritiskt för prestanda. I följande tabell visas de föreslagna antalet App-servrar.

|  Skicka till liten grupp   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient anslutnings numret, meddelande storleken, överförings hastigheten, tillverkningskostnaden, SKU-nivån och CPU/minne för program servern påverkar den övergripande prestandan för **Skicka till liten grupp**.

##### <a name="big-group"></a>Stor grupp

För **att skicka till Big-gruppen** blir den utgående bandbredden Flask hals innan du påträffar den kostnads gränsen för routning. I följande tabell visas den maximala utgående bandbredden som är nästan samma som för **sändning**.

|    Skicka till stor grupp      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000
| Antal grupp medlemmar        | 100   | 200   | 500    | 1 000  | 2 000  | 5 000   | 10 000 
| Antal grupper               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Inkommande meddelanden per sekund  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Inkommande bandbredd  | 80 KBps   | 40 KBps   | 40 KBps    | 20 kbit/s    | 40 KBps    | 40 KBps     | 40 KBps     |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Utgående bandbredd | 8 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 40 Mbit/s    | 80 Mbit/s    | 200 Mbit/s    | 400 Mbit/s    |

Antalet sändnings anslutningar är inte mer än 40. Belastningen på App-servern är liten, så det föreslagna antalet webb program är litet.

|  Skicka till stor grupp  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Öka antalet Server anslutningar från 5 till 40 på varje app-server för att undvika eventuella obalanserade Server anslutningar till Azure SignalR-tjänsten.
> 
> Klient anslutnings numret, meddelande storleken, överförings hastigheten, tillverkningskostnaden och SKU-nivån påverkar den övergripande prestandan för **Skicka till stor grupp**.

#### <a name="send-to-connection"></a>Skicka till anslutning

När klienterna upprättar anslutningar till Azure SignalR-tjänsten i ett fall med **Skicka till-anslutning** , anropar varje klient en särskild hubb för att hämta sitt eget anslutnings-ID. Prestanda mätningen samlar in alla anslutnings-ID: n, blandar dem och tilldelar om dem till alla klienter som ett mål för sändning. Klienterna håller på att skicka meddelandet till mål anslutningen tills prestanda testet har slutförts.

![Trafik för användnings fallet för sändning till klient](./media/signalr-concept-performance/sendtoclient.png)

Produktionskostnaden för **Skicka till-anslutning** liknar kostnaden för **att skicka till små grupper**.

När antalet anslutningar ökar, begränsar produktionskostnaden den allmänna prestandan. Unit50 har nått gränsen. Därför kan Unit100 inte förbättras ytterligare.

Följande tabell är en statistisk sammanfattning efter många avrundning för att köra benchmark- **anslutningen** .

|   Skicka till anslutning   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Anslutningar                        | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Inkommande/utgående meddelanden per sekund | 1 000 | 2 000 | 5 000 | 8,000  | 9,000  | 20 000 | 20 000 |
| Inkommande/utgående bandbredd | 2 Mbit/s    | 4 Mbit/s    | 10 Mbit/s   | 16 MBps    | 18 MBps    | 40 Mbit/s       | 40 Mbit/s       |

Det här användnings fallet kräver hög belastning på App Server-sidan. Se det föreslagna antalet App-servrar i följande tabell.

|  Skicka till anslutning  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient anslutnings numret, meddelande storleken, överförings hastigheten, tillverkningskostnaden, SKU-nivån och CPU/minne för app-servern påverkar den övergripande prestandan för **Skicka till-anslutning**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR eko, sändning och skicka till liten grupp

Azure SignalR service ger samma prestanda kapacitet för ASP.NET-Signalerare. 

Prestanda testet använder Azure Web Apps från [standard service plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) för ASP.net-signalerare.

I följande tabell får du förslag på antalet webb program för ASP.NET SignalR- **eko**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

I följande tabell får du förslag på antalet webb program för ASP.NET signaler- **sändning**.

|  Sändning       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

I följande tabell visas det föreslagna antalet webb program för ASP.NET SignalR- **sändning till liten grupp**.

|  Skicka till liten grupp     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Anslutningar      | 1 000 | 2 000 | 5 000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Antal App-servrar | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Läge utan Server

Klienter och Azure SignalR-tjänsten är involverade i Server lös läge. Varje klient står för en enda anslutning. Klienten skickar meddelanden via REST API till en annan klient eller broadcast-meddelanden till alla.

Att skicka meddelanden med hög densitet via REST API är inte lika effektivt som att använda WebSocket. Du måste bygga en ny HTTP-anslutning varje gång, och det är en extra kostnad i Server lös läge.

#### <a name="broadcast-through-rest-api"></a>Sändning via REST API
Alla klienter upprättar WebSocket-anslutningar med Azure SignalR-tjänsten. Därefter börjar vissa klienter att sända via REST API. Meddelandet sändning (inkommande) är allt via HTTP post, vilket inte är effektivt jämfört med WebSocket.

|   Sändning via REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Utgående meddelanden per sekund | 2 000 | 4 000 | 10 000 | 20 000 | 40 000 | 100 000 | 200 000 |
| Inkommande bandbredd  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Utgående bandbredd | 4 Mbit/s    | 8 Mbit/s    | 20 Mbit/s    | 40 Mbit/s    | 80 Mbit/s    | 200 Mbit/s    | 400 Mbit/s    |

#### <a name="send-to-user-through-rest-api"></a>Skicka till användare via REST API
Benchmark tilldelar användar namn till alla klienter innan de börjar ansluta till Azure SignalR-tjänsten. När klienterna upprättar WebSocket-anslutningar med Azure SignalR-tjänsten börjar de skicka meddelanden till andra via HTTP post.

|   Skicka till användare via REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Anslutningar               | 1 000 | 2 000 | 5 000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Inkommande meddelanden per sekund  | 300   | 600   | 900    | 1 300  | 2 000  | 10 000  | 18 000  |
| Utgående meddelanden per sekund | 300   | 600   | 900    | 1 300  | 2 000  | 10 000  | 18 000 |
| Inkommande bandbredd  | 600 KBps  | 1,2 Mbit/s  | 1,8 Mbit/s   | 2,6 Mbit/s   | 4 Mbit/s     | 10 Mbit/s     | 36 Mbit/s    |
| Utgående bandbredd | 600 KBps  | 1,2 Mbit/s  | 1,8 Mbit/s   | 2,6 Mbit/s   | 4 Mbit/s     | 10 Mbit/s     | 36 Mbit/s    |

## <a name="performance-test-environments"></a>Prestanda test miljöer

För alla användnings fall som listas tidigare genomförde vi prestandatester i en Azure-miljö. Oftast använde vi 50-klientens virtuella datorer och 20 virtuella datorer i App Server. Här följer lite information:

- VM-storlek för klient: StandardDS2V2 (2 vCPU, 7G-minne)

- VM-storlek för App Server: StandardF4sV2 (4 vCPU, 8G-minne)

- Azure SignalR SDK-Server anslutningar: 15

## <a name="performance-tools"></a>Prestanda verktyg

Du kan hitta prestanda verktyg för Azure SignalR-tjänsten på [GitHub](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du en översikt över Azure SignalR service-prestanda i vanliga scenarier med användnings fall.

Läs följande guider om du vill ha information om de interna tjänsterna och skalningen för den:

* [Azure SignalR Service – internt](signalr-concept-internals.md)
* [Skalning av Azure SignalR-tjänsten](signalr-howto-scale-multi-instances.md)
