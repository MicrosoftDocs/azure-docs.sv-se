---
title: Timed metadata för Azure Media Services signalering i Live Streaming
description: Den här specifikationen beskriver metoder för att signalera Timed metadata vid inmatning och strömning till Azure Media Services. Detta omfattar stöd för allmänna timeed metadata-signaler (ID3), samt SCTE-35-signaler för annons infogning och splice-villkor signaler.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: f826ee9ef3c9fff0b721a9c79d3c12e0adbd5f7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336402"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Signalerar Timed metadata i Live Streaming 

Senast uppdaterad: 2019-08-22

### <a name="conformance-notation"></a>Överensstämmelse notation

Nyckelorden "måste", "måste", "krävs", ",", ",", ",", "ska inte", "ska inte", "rekommenderas", "maj" och "valfritt" i det här dokumentet tolkas enligt beskrivningen i RFC 2119

## <a name="1-introduction"></a>1. Introduktion 

För att signalera infogningen av annonseringar eller anpassade metadata-händelser på en klient spelare använder broadcast-meddelanden ofta inbäddade metadata inbäddade i videon. För att aktivera de här scenarierna ger Media Services stöd för transport av tidshanterade metadata från inmatnings punkten för Live streaming-kanalen till klient programmet.
Den här specifikationen beskriver flera lägen som stöds av Media Services för Timed metadata i direkt uppspelnings signaler.

1. [SCTE-35] signalerar som uppfyller de standarder som beskrivs av [SCTE-35], [SCTE-214-1], [SCTE-214-3] och [RFC8216]

2. [SCTE-35] signalerar som uppfyller den äldre [Adobe-Primetime]-specifikationen för RTMP AD-signalering.
   
3. Ett allmänt timeed metadata Signaling-läge för meddelanden som **inte** är [SCTE-35] och som kan ha [ID3v2] eller andra anpassade scheman som definieras av programutvecklaren.

## <a name="11-terms-used"></a>användnings villkor för 1,1

| Period                | Definition                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AD-avbrott            | En plats eller tidpunkt då en eller flera annonser kan schemaläggas för leverans; samma som DISP-och placerings möjlighet.                                                                                                                     |
| AD-besluts tjänst | extern tjänst som avgör vilka AD (er) och varaktigheter som ska visas för användaren. Tjänsterna tillhandahålls vanligt vis av en partner och är utanför omfånget för det här dokumentet.                                                                    |
| Utlösare                 | Indikering av tid och parametrar för den kommande AD-rasten. Observera att stacken kan indikera en väntande växel till en AD Break, väntar på att växla till nästa AD i en AD Break och väntande växel från en AD Break till huvud innehållet.           |
| Packager            | Azure Media Services slut punkt för direkt uppspelning tillhandahåller funktioner för dynamisk paketering för streck-och HLS och kallas för "Paketeraren" i medie branschen.                                                                              |
| Presentations tid   | Den tid som en händelse presenteras för ett visnings program. Tiden visar tidpunkten för den medie tids linje som ett visnings program skulle se händelsen. Till exempel är presentations tiden för kommando meddelandet SCTE-35 splice_info () splice_time (). |
| Införsel tid        | Den tidpunkt då ett händelse meddelande tas emot. Tiden är vanligt vis distinkt från händelsens presentations tid, eftersom händelse meddelanden skickas före händelsens presentations tid.                                                    |
| Sparse-spår        | medie spår som inte är kontinuerlig och som är tids synkroniserat med ett överordnat eller kontroll spår.                                                                                                                                                  |
| Ursprung              | Tjänsten Azure Media Streaming                                                                                                                                                                                                             |
| Kanal mottagare        | Tjänsten Azure Media Live Streaming                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming-protokoll                                                                                                                                                                                                            |
| DASH                | Dynamisk adaptiv strömning via HTTP                                                                                                                                                                                                          |
| Utseende              | Smooth Streaming protokoll                                                                                                                                                                                                                     |
| MPEG2 – TS            | MPEG 2-överförings strömmar                                                                                                                                                                                                                      |
| RTMP                | Real-Time multimedie protokoll                                                                                                                                                                                                                 |
| uimsbf              | Osignerat heltal, mest signifikanta bit först.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1,2 normativa referenser

Följande dokument innehåller bestämmelser, som till följd av referens i denna text utgör bestämmelser i det här dokumentet. Alla dokument är föremål för revidering av standard organen, och läsarna uppmuntras att undersöka möjligheten att tillämpa de senaste versionerna av dokumenten som anges nedan. Läsarna får också en påminnelse om att nyare versioner av de refererade dokumenten kanske inte är kompatibla med den här versionen av den tidsbegränsade metadata-specifikationen för Azure Media Services.


| Standard          | Definition                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime Digital program infogning, signal specifikation 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Språk referens för FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Åtgärds meddelande format AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [BINDESTRECK-IF-IOP]     | Utlinjes sällskap interop-vägledning v 4,2 [https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Tidsbaserade metadata för HTTP Live Streaming- [https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Timed metadata i det vanliga medie program formatet (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3-tagg version 2.4.0  [http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: del 12 ISO Base Media-filformat, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informations teknik – dynamisk anpassningsbar strömning via HTTP (bindestreck)--del 1: Beskrivning av medie presentation och segment format. Maj 2014. Delade. ADRESSER https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Information Technology – multimedie program format (MPEG-A)--del 19: common Media program format (CMAF) för segmenterade medier. 2018 januari. Delade. ADRESSER https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Information Technology – MPEG Systems Technologies – del 7: common Encryption in fil format i ISO Base-filer. Februari 2016. Delade. ADRESSER https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming-protokollet", 15 maj 2014](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-intag]  | [Azure Media Services-specifikation för fragmenterad MP4 Live](./media-services-fmp4-live-ingest-overview.md)                                                      |
| [RFC8216]         | R. Pantos, ED. W. maj. HTTP Live Streaming. 2017 augusti. Informations. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | Data kodningarna Base16, Base32 och base64 – [https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| RTMP            | ["Adobes Real-Time meddelande protokoll", 21 december 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35:2019-Digital program infogning cueing meddelande för kabel- https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG-streck för IP-Based kabel tjänster del 1: MPD-begränsningar och tillägg                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG-streck för IP-Based kabel tjänster del 3: streck-/FF-profil                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – händelse schema och meddelande gränssnitt                                                                                                                                                  |
| [SCTE-250]        | API för händelse-och signal hantering (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. inläsning av Timed metadata

Azure Media Services stöder real tids inlösnings-metadata för både [RTMP] och Smooth Streaming [MS-SSTR-insugning]-protokoll. Real tids metadata kan användas för att definiera anpassade händelser, med dina egna unika anpassade scheman (JSON, Binary, XML), samt bransch definierade format som ID3 eller SCTE-35 för AD-signalering i en broadcast-dataström. 

Den här artikeln innehåller information om hur du skickar anpassade metadata-signaler med de inmatnings protokoll som stöds av Azure Media Services. Artikeln beskriver också hur manifesten för HLS, bindestreck och Smooth Streaming dekoreras med de tidsvisade metadata-signalerna, samt hur den transporteras i band när innehållet levereras med CMAF (MP4-fragment) eller transport data ström (TS) segment för HLS. 

Vanliga användnings fall för Timed metadata inkluderar:

 - SCTE – 35 AD-signaler för att utlösa AD-avbrott i en Live-händelse eller linjär sändning
 - Anpassade ID3-metadata som kan utlösa händelser i ett klient program (webbläsare, iOS eller Android)
 - Anpassade JSON-, Binary-eller XML-metadata för att utlösa händelser i ett klient program
 - Telemetri från en Live Encoder, IP-kamera eller drönare
 - Händelser från en IP-kamera som rörelse, ansikts igenkänning osv.
 - Geografisk positions information från en åtgärd kamera, drönare eller rörlig enhet
 - Sång texter
 - Program gränser i en linjär live-feed
 - Bilder eller förstärkta metadata som ska visas i en live-feed
 - Sport poäng eller information om spel klock håll
 - Interaktiva annonserings paket som ska visas bredvid videon i webbläsaren
 - Frågor eller avsökningar
  
Azure Media Services Live-händelser och Paketeraren kan ta emot dessa tidsbaserade metadata signaler och omvandla dem till en data ström med metadata som kan komma åt klient program med hjälp av standardbaserade protokoll som HLS och bindestreck.


## <a name="21-rtmp-timed-metadata"></a>2,1 RTMP Timed metadata

Med [RTMP]-protokollet kan du skicka Timed metadata-signaler för olika scenarier, inklusive anpassade metadata och SCTE-35 AD-signaler. 

Annonserings signaler (Cue-meddelanden) skickas som [AMF0] Cue-meddelanden inbäddade i [RTMP]-strömmen. Stack-meddelanden kan skickas någon gång innan den faktiska händelsen eller [SCTE35] AD splice-signalen måste utföras. För att stödja det här scenariot skickas den faktiska presentationens tidstämpel för händelsen i stack-meddelandet. Mer information finns i [AMF0].

Följande [AMF0]-kommandon stöds av Azure Media Services för RTMP-inmatning:

- **onUserDataEvent** – används för anpassade metadata eller [ID3v2] Timed metadata
- **onAdCue** – används främst för att signalera en möjlighet att placera reklam i Live Stream. Två former av stack-ikonen stöds, ett enkelt läge och ett "SCTE-35"-läge. 
- **onCuePoint** – stöds av vissa lokala maskin varu kodare, till exempel grundämne Live Encoder, för att signalera [SCTE35]-meddelanden. 
  

I följande tabell beskrivs formatet på den AMF meddelande nytto last som Media Services kommer att mata in för både "enkla" och [SCTE35]-meddelande lägen.

Namnet på [AMF0]-meddelandet kan användas för att särskilja flera händelse strömmar av samma typ.  För både [SCTE-35]-meddelanden och läget "Simple" måste namnet på AMF-meddelandet vara "onAdCue", vilket krävs i [Adobe-Primetime]-specifikationen.  Fält som inte anges nedan ignoreras av Azure Media Services vid inmatning.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP med anpassade metadata med hjälp av "onUserDataEvent"

Om du vill tillhandahålla anpassade metadata från din överordnade kodare, IP-kamera, drönare eller enhet med hjälp av RTMP-protokollet använder du kommando typen "onUserDataEvent" [AMF0] data meddelande.

Data meddelande kommandot **"onUserDataEvent"** måste ha en meddelande nytto last med följande definition för att kunna fångas in av Media Services och paketeras i fil formatet in-band samt manifesten för HLS, bindestreck och Smooth Streaming.
Vi rekommenderar att skicka meddelanden med Time-metadata som inte ofta förekommer oftare än en gång var 0,5 sekund (500ms) eller stabilitets problem med den aktiva strömmen. Varje meddelande kan aggregera metadata från flera ramar om du behöver ange metadata för bildskärms nivå. Om du skickar strömmar med flera bit hastigheter rekommenderar vi att du även ger metadata på en enskild bit hastighet för att minska bandbredden och undvika störningar i video/ljud-bearbetning. 

Nytto lasten för **"onUserDataEvent"** ska vara ett [MPEGDASH] EventStream XML-formaterat meddelande. Detta gör det enkelt att skicka in anpassade scheman som kan utföras i "EMSG"-nytto laster på band för CMAF [MPEGCMAF]-innehåll som levereras över HLS-eller tank strecks protokoll. Varje streck händelse Stream-meddelande innehåller en schemeIdUri som fungerar som en identifierare för URN-meddelande scheman och definierar nytto lasten för meddelandet. Vissa scheman som " https://aomedia.org/emsg/ID3 " för [ID3v2] eller **urn: scte: scte35:2013: bin** för [scte-35] är standardiserade av bransch konsortier för interoperabilitet. Alla programproviders kan definiera egna anpassade scheman med en URL som de styr (ägs domän) och kan tillhandahålla en specifikation på denna URL om de väljer. Om en spelare har en hanterare för det definierade schemat, är det den enda komponenten som behöver förstå nytto lasten och protokollet.

Schemat för [MPEG-streck] EventStream XML-nyttolasten definieras som (utdrag från streck ISO-IEC-23009-1-tredje utgåva). Observera att endast en "EventType" per "EventStream" stöds för tillfället. Endast det första **händelse** elementet kommer att bearbetas om flera händelser finns i **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Exempel på XML-dataström med ID3-schema-ID och Base64-kodad data nytto Last.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Exempel händelse ström med anpassat schema-ID och base64-kodade binära data  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Exempel händelse ström med anpassat schema-ID och anpassad JSON  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Inbyggda schema-ID: n som stöds
| Schema-ID-URI                 | Beskrivning                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https: \/ /aomedia.org/EMSG/ID3 | Beskriver hur [ID3v2] metadata kan överföras som tids bara metadata i en CMAF-kompatibel [MPEGCMAF]-fragmenterad MP4. Mer information finns i de tidsspecifika [metadata i det vanliga medie program formatet (cmaf)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Händelse bearbetning och manifest signalering

Vid mottagande av en giltig **"onUserDataEvent"** -händelse kommer Azure Media Services att söka efter en giltig XML-nyttolast som matchar EventStreamType (definieras i [MPEGDASH]), PARSA XML-nyttolasten och konvertera den till ett [MPEGCMAF] MP4-fragment ' EMSG ' version 1-ruta för lagring i Live-arkivet och överföring till Media Services Paketeraren.   Paketeraren identifierar rutan "EMSG" i Live Stream och:

- (a) "paketera" det i TS-segment för leverans till HLS-klienter i enlighet med HLS-specifikationen för tidsbaserad metadata [HLS-TMD], eller
- (b) skicka det via för leverans i CMAF-fragment via HLS eller tank streck, eller 
- (c) konvertera den till en renullad spår signal för leverans via Smooth Streaming [MS-SSTR].

Förutom "EMSG"-formatet CMAF eller TS PARAMETERENTITETER-paket för HLS, innehåller manifesten för streck (MPD) och Smooth Streaming innehålla en referens till händelse strömmar för inband (även kallat re Stream-spår i Smooth Streaming).

Enskilda händelser eller deras data nytto laster matas inte ut direkt i HLS, bindestreck eller smidiga manifest. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Ytterligare information begränsningar och standardvärden för onUserDataEvent-händelser

- Om tids skalan inte har ställts in i EventStream-elementet används RTMP 1 kHz-tidsskalan som standard
- Leverans av ett onUserDataEvent-meddelande är begränsat till en gång varje 500ms max. Om du skickar händelser oftare kan det påverka bandbredden och stabiliteten hos Live-matningen

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP AD Cue-signalering med "onAdCue"

Azure Media Services kan lyssna efter och svara på flera [AMF0]-meddelande typer som kan användas för att signalera olika synkroniserade metadata i real tid i Live Stream.  [Adobe-Primetime]-specifikationen definierar två Cue-typer som kallas "enkelt" och "SCTE-35"-läge. För "enkelt" läge stöder Media Services ett enda AMF-Cue-meddelande med namnet "onAdCue" med en nytto last som matchar tabellen nedan definierad för signalen "enkel läge".  

I följande avsnitt visas en "enkel" läge "-nytto last som kan användas för att signalera en grundläggande" spliceOut "AD-signal som överförs till klient manifestet för HLS, tank streck och Microsoft Smooth Streaming. Detta är användbart för scenarier där kunden inte har ett komplext SCTE-35-baserat AD-signalerande distributions-eller infognings system och använder en grundläggande lokal kodare för att skicka i Cue-meddelandet via ett API. Den lokala kodaren har vanligt vis stöd för ett REST-baserat API för att utlösa den här signalen, som även "splice-Condition" video strömmen genom att infoga en IDR-ram i videon och starta en ny GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP AD Cue Signaling med "onAdCue" – enkelt läge

| Fältnamn | Fälttyp | Obligatoriskt? | Förklaringar                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| typ       | Sträng     | Obligatorisk  | Händelse meddelandet.  Måste vara "SpliceOut" för att ange en enkel läges splice.                                                                                                                                                                                                         |
| id         | Sträng     | Obligatorisk  | En unik identifierare som beskriver splice eller-segmentet. Identifierar den här instansen av meddelandet                                                                                                                                                                                       |
| varaktighet   | Antal     | Obligatorisk  | Varaktigheten för splice. Enheter är bråkiska sekunder.                                                                                                                                                                                                                           |
| elapsed    | Antal     | Valfritt  | När signalen upprepas för att ge stöd för att ställa in, är det här fältet den mängd av presentationen som har förflutit sedan splice började. Enheter är bråkiska sekunder. När du använder enkelt läge får det här värdet inte överskrida den ursprungliga varaktigheten för splice. |
| time       | Antal     | Obligatorisk  | Är tiden för splice i presentations tid. Enheter är bråkiska sekunder.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exempel på MPEG-datautdata-manifest när du använder enkel läge för Adobe RTMP

Se exempel [3.3.2.1 MPEG bindestreck. mpd EventStream med hjälp av Adobe Simple mode](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Se exempel [på 3.3.3.1-datastreck med enkel punkt och Adobe enkel läge ](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exempel på HLS manifest när du använder enkel läge för Adobe RTMP

Se exempel [3.2.2 HLS-manifestet med hjälp av Adobe Simple mode och EXT-X-Cue-taggen](#322-apple-hls-with-adobe-primetime-ext-x-cue)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP AD Cue-signalering med "onAdCue"-SCTE-35-läge

När du arbetar med ett mer avancerat arbets flöde för sändnings produktion som kräver ett fullständigt SCTE-35-nyttolast-meddelande som ska överföras till HLS-eller streck manifestet, är det bäst att använda "SCTE-35 mode" i [Adobe-Primetime]-specifikationen.  Det här läget stöder in-band-SCTE-35-signaler som skickas direkt till en lokal Live-kodare, som sedan kodar signaler ut till RTMP-dataströmmen med "SCTE-35-läge" som anges i [Adobe-Primetime]-specifikationen. 

Vanligt vis kan SCTE-35-meddelanden endast visas i TS-indata (MPEG-2 transport stream) på en lokal kodare. Kontrol lera med din Encoder-tillverkare om du vill ha mer information om hur du konfigurerar en transport stream-inmatning som innehåller SCTE-35 och gör det möjligt för genom strömning till RTMP i Adobe SCTE-35-läge.

I det här scenariot måste följande nytto Last skickas från den lokala kodaren med meddelande typen **"onAdCue"** [AMF0].

| Fältnamn | Fälttyp | Obligatoriskt? | Förklaringar                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| utlösare        | Sträng     | Obligatorisk  | Händelse meddelandet.  För [SCTE-35]-meddelanden måste detta vara den base64-kodade [RFC4648] binära splice_info_section () för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter.                                                                                                                                                                                                                               |
| typ       | Sträng     | Obligatorisk  | En URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden **ska** detta vara **"scte35"** för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter, i enlighet med [Adobe-Primetime]. Alternativt kan URN "urn: scte: scte35:2013: bin" också användas för att signalera ett [SCTE-35]-meddelande.                                                                                                        |
| id         | Sträng     | Obligatorisk  | En unik identifierare som beskriver splice eller-segmentet. Identifierar den här instansen av meddelandet.  Meddelanden med motsvarande semantik måste ha samma värde.                                                                                                                                                                                                                                                       |
| varaktighet   | Antal     | Obligatorisk  | Händelsens eller AD-splice-segmentets varaktighet, om det är känt. Om detta är okänt **ska** värdet vara 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Antal     | Valfritt  | När AD-signalen [SCTE-35] upprepas för att kunna ställa in, är det här fältet den mängd av presentationen som har förflutit sedan splice började. Enheter är bråkiska sekunder. I läget [SCTE-35] kan det här värdet överskrida den ursprungliga angivna varaktigheten för en splice eller ett segment.                                                                                                                   |
| time       | Antal     | Obligatorisk  | Presentations tiden för händelsen eller AD-splice.  Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS utgående, tid och varaktighet **bör** justeras mot segment gränser. Presentations tiden och varaktigheten för olika händelse meddelanden inom samma händelse ström får inte överlappa varandra. Enheter är bråkiska sekunder. |

---

<!---
#### Example MPEG DASH .mpd manifest with SCTE-35 mode
See [Section 3.3.3.2 example DASH manifest with SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)
--->

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exempel på HLS manifest. M3U8 med SCTE-35-läge
Se [avsnittet 3.2.1.1 exempel HLS manifest med SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35)



## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP AD signalerar med "onCuePoint" för grundämne Live

Den fördefinierade Live-kodarens lokala kodare stöder AD-markörer i RTMP-signalen. Azure Media Services stöder för närvarande bara AD-markör typen "onCuePoint" för RTMP.  Detta kan aktive ras i inställningarna för Adobe RTMP-gruppen i inställningarna för Media Live Encoder eller API genom att ange "**ad_markers**" till "onCuePoint".  Mer information finns i Live-dokumentationen för grundämne. Om du aktiverar den här funktionen i RTMP-gruppen skickas SCTE-35-signaler till de Adobe RTMP-utdata som ska bearbetas av Azure Media Services.

Meddelande typen "onCuePoint" definieras i [Adobe-Flash-AS] och har följande nytto Last struktur när den skickas från den fördefinierade Live RTMP-utdata.


| Egenskap   | Beskrivning                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | Namnet måste vara '**scte35**' av typen grundämne Live.                                                                                                                                                                              |
| time       | Tiden i sekunder då referens punkten inträffade i video filen under tids linjen                                                                                                                                           |
| typ       | Typen av referens punkt ska anges till "**Event**".                                                                                                                                                                             |
| parametrar | En associativ array med namn/värde-par strängar som innehåller information från SCTE-35-meddelandet, inklusive ID och varaktighet. Dessa värden analyseras av Azure Media Services och ingår i dekorations tag gen för manifestet. |


När det här läget för AD-markör används liknar HLS-Manifestets utdata samma som i Adobe "Simple"-läge.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exempel på MPEG-streck, MPD, enkel period, Adobe Simple mode-signaler

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
```

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exempel på HLS-spelnings lista, Adobe Simple mode-signaler med EXT-X-CUE-tagg (trunkerad "..." för det kortfattat)

I följande exempel visas utdata från Media Services dynamiska Paketeraren för en RTMP-inmatnings ström med hjälp av Adobe "Simple" mode-signaler och den äldre [Adobe-Primetime] EXT-X-CUE-taggen.  

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...
```

### <a name="216-cancellation-and-updates"></a>2.1.6 för annullering och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentations tid och ID. Presentations tiden och ID: t identifierar händelsen unikt och det senaste meddelandet togs emot för en speciell presentations tid som uppfyller för insamlade begränsningar är det meddelande som har åtgärd ATS. Den uppdaterade händelsen ersätter alla tidigare mottagna meddelanden. För registrerings begränsningen är fyra sekunder. Meddelanden som mottagits minst fyra sekunder innan presentations tiden kommer att behandlas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2-fragmenterad MP4-insugning (Smooth Streaming)

I [MS-SSTR-inmatning] finns krav på Live Stream-inmatning. I följande avsnitt finns information om inläsning av metadata för tids presentationer.  Timed presentations-metadata matas in som ett sparse-spår, som definieras i båda rutan Live Server-manifest (se MS-SSTR) och film rutan (' Moov ').  

Varje sparse-fragment består av en ruta för film fragment (' moof ') och media Data Box-enhet (' mdat '), där rutan ' mdat ' är det binära meddelandet.

För att det ska gå att lägga till en ram-korrekt infogning av annonser måste kodaren dela upp fragmentet vid den tidpunkt då stacken måste infogas.  Ett nytt fragment måste skapas som börjar med en nyligen skapad IDR-ram eller Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I.
<!--- This allows the Azure Media Packager to properly generate an HLS manifest and a DASH multi-period manifest where the new Period begins at the frame-accurate splice conditioned presentation time. --->

### <a name="221-live-server-manifest-box"></a>avsnitt med 2.2.1 Live Server-manifest

Det glesa spåret **måste** deklareras i rutan för Live Server-manifestet med en **\<textstream\>** post och **måste** ha följande attribut inställda:

| **Attributnamn** | **Fälttyp** | **Krävs?** | **Beskrivning**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Antal         | Obligatorisk      | **Måste** vara "0", vilket indikerar ett spår med okänd variabel bit hastighet.                                                                                                                                                          |
| parentTrackName    | Sträng         | Obligatorisk      | **Måste** vara namnet på det överordnade spåret, där tids koderna för den glesa spårnings tids skalan är justerade. Det överordnade spåret får inte vara ett sparse-spår.                                                                             |
| manifestOutput     | Boolesk        | Obligatorisk      | **Måste** vara "true" för att indikera att det glesa spåret ska bäddas in i det smidiga klient manifestet.                                                                                                                        |
| Subtype            | Sträng         | Obligatorisk      | **Måste** vara den fyra tecken koden "data".                                                                                                                                                                                  |
| Schema             | Sträng         | Obligatorisk      | **Måste** vara en URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden **måste** detta vara "urn: SCTE: scte35:2013: bin" för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter i enlighet med [SCTE-35]. |
| trackName          | Sträng         | Obligatorisk      | **Måste** vara namnet på det sparse-spåret. TrackName kan användas för att särskilja flera händelse strömmar med samma schema. Varje unik händelse ström **måste** ha ett unikt spårnings namn.                                |
| tidsplan          | Antal         | Valfritt      | **Måste** vara tids skalan för det överordnade spåret.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2-film Box

Film rutan (' Moov ') följer direkt Server manifest rutan som en del av data Ströms huvudet för ett sparse-spår.

Rutan ' Moov ' **ska** innehålla en **TrackHeaderBox (' tkhd ')** som definieras i [ISO-14496-12] med följande begränsningar:

| **Fält namn** | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| varaktighet       | 64-bitars osignerat heltal | Obligatorisk      | **Ska** vara 0, eftersom spår rutan innehåller noll exempel och den totala längden för exemplen i spår rutan är 0. |

---

Rutan Moov **ska** innehålla en **-hanterare (hdlr)** enligt definitionen i [ISO-14496-12] med följande begränsningar:

| **Fält namn** | **Fälttyp**          | **Krävs?** | **Beskrivning**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bitars osignerat heltal | Obligatorisk      | **Ska** vara meta. |

---

Rutan stsd **ska** innehålla en MetaDataSampleEntry-ruta med ett kodnings namn som definieras i [ISO-14496-12].  För SCTE-35-meddelanden **ska** kodnings namnet till exempel vara ' SCTE '.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3-film fragment Box och media Data Box-enhet

Uppdelade spår fragment består av en ruta för film fragment (' moof ') och en Media Data Box-enhet (' mdat ').

> [!NOTE]
> För att det ska gå att lägga till en ram-korrekt infogning av annonser måste kodaren dela upp fragmentet vid den tidpunkt då stacken måste infogas.  Ett nytt fragment måste skapas som börjar med en nyligen skapad IDR-ram eller Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I
> 

MovieFragmentBox (' moof ') **måste** innehålla en **TrackFragmentExtendedHeaderBox (' UUID ')** -ruta som definieras i [MS-SSTR] med följande fält:

| **Fält namn**         | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bitars osignerat heltal | Obligatorisk      | **Måste** vara händelsens ankomst tid. Det här värdet justerar meddelandet med det överordnade spåret.           |
| fragment_duration      | 64-bitars osignerat heltal | Obligatorisk      | **Måste** vara händelsens varaktighet. Varaktigheten kan vara noll för att indikera att varaktigheten är okänd. |

---


Rutan MediaDataBox (' mdat ') **måste** ha följande format:

| **Fält namn**          | **Fälttyp**                   | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Anger formatet för innehållet i rutan "mdat". Okända versioner kommer att ignoreras. För närvarande är den enda version som stöds 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik måste ha samma värde. Det räcker med att bearbeta en händelse meddelande ruta med samma ID.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bitars osignerat heltal (uimsbf) | Obligatorisk      | Summan av de fragment_absolute_time som anges i TrackFragmentExtendedHeaderBox och presentation_time_delta **måste** vara händelsens presentations tid. Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I. För HLS utgående, tid och varaktighet **bör** justeras mot segment gränser. Presentations tiden och varaktigheten för olika händelse meddelanden inom samma händelse ström **får** inte överlappa varandra. |
| meddelande                 | bytematris                       | Obligatorisk      | Händelse meddelandet. För [SCTE-35]-meddelanden är meddelandet binärfilen splice_info_section (). För [SCTE-35]-meddelanden **måste** det vara splice_info_section () för att meddelanden ska kunna skickas till HLS-, utjämna-och streck-klienter i enlighet med [SCTE-35]. För [SCTE-35]-meddelanden är binärfilen splice_info_section () nytto lasten för rutan "mdat" och den är **inte** Base64-kodad.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 för annullering och uppdateringar

Meddelanden kan avbrytas eller uppdateras genom att skicka flera meddelanden med samma presentations tid och ID.  Presentationens tid och ID identifierar händelsen unikt. Det sista meddelandet som togs emot för en speciell presentations tid, som uppfyller för hands versioner, är det meddelande som har åtgärd ATS. Det uppdaterade meddelandet ersätter alla tidigare mottagna meddelanden.  För registrerings begränsningen är fyra sekunder. Meddelanden som mottagits minst fyra sekunder innan presentations tiden kommer att behandlas. 


## <a name="3-timed-metadata-delivery"></a>3 Timed metadata-leverans

Händelse data strömmen är ogenomskinlig för Media Services. Media Services skickar bara tre delar av information mellan inmatnings slut punkten och klient slut punkten. Följande egenskaper levereras till klienten, i enlighet med [SCTE-35] och/eller klientens strömnings protokoll:

1.  Schema – en URN eller URL som identifierar meddelandets schema.
2.  Presentations tid – händelsens presentations tid på medie tids linjen.
3.  Duration – händelsens varaktighet.
4.  ID – en valfri unik identifierare för händelsen.
5.  Meddelande – händelse data.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 Microsoft Smooth Streaming-manifest  

Mer information om hur du formaterar ett reoptimerat meddelande spår finns i hantering av glesa spår [MS-SSTR]. För [SCTE35]-meddelanden kommer Smooth Streaming att mata ut base64-kodade splice_info_section () i ett sparse-fragment.
StreamIndex **måste** ha en undertyp till "data" och CustomAttributes **måste** innehålla ett attribut med Name = "schema" och värde = "urn: scte: scte35:2013: bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exempel på ett smidigt klient manifest som visar base64-kodade [SCTE35] splice_info_section ()
```xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
```

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS-manifest

Azure Media Services stöder följande HLS manifest-taggar för att signalera AD via-information under en Live-eller on-demand-händelse. 

<!--- EXT-X-DATERANGE as defined in Apple HLS [RFC8216] --->
- EXT-X-CUE enligt definitionen i [Adobe-Primetime]
<!--- this mode is considered "legacy".  Customers should adopt the EXT-X-DATERANGE tag when possible. --->

Datautdata till varje tagg varierar beroende på vilket inmatnings signal läge som används. Exempelvis innehåller RTMP-inmatningen med Adobe Simple mode inte den fullständiga SCTE-35 base64-kodade nytto lasten.

<!---
## 3.2.1 Apple HLS with EXT-X-DATERANGE (recommended)

The Apple HTTP Live Streaming [RFC8216] specification allows for signaling of [SCTE-35] messages. The messages are inserted into the segment playlist in an EXT-X-DATERANGE tag per [RFC8216] section titled "Mapping SCTE-35 into EXT-X-DATERANGE".  The client application layer can parse the M3U playlist and process M3U tags, or receive the events through the Apple player framework.  

The **RECOMMENDED** approach in Azure Media Services (version 3 API) is to follow [RFC8216] and use the EXT-X_DATERANGE tag for [SCTE35] ad avail decoration in the manifest.
--->


## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-cue-signaling-of-scte-35"></a>3.2.1.1 exempel på HLS manifest. M3U8 visar EXT-X-CUE-signalering av SCTE-35

I följande exempel HLS manifest utdata från Media Services dynamiska Paketeraren visas EXT-X-CUE-taggen för [Adobe-Primetime] i SCTE35-läge. 

```
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

```


<!---
THIS VERSION HAS THE HLSv8 DATERANGE Tags in it
~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~

--->

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue"></a>3.2.2 Apple-HLS med Adobe Primetime EXT-X-CUE

Media Services (version 2 och 3 API) stöder utdata från taggen EXT-X-stack som definieras i [Adobe-Primetime] "SCTE-35-läge". I det här läget kommer Azure Media Services bädda in Base64-kodat [SCTE-35] splice_info_section () i taggen EXT-X-CUE.  

Taggen "Legacy" EXT-X-CUE definieras enligt nedan och kan också vara normativ som refereras till i [Adobe-Primetime]-specifikationen. Detta bör endast användas för äldre SCTE35-signaler vid behov, annars definieras den rekommenderade taggen i [RFC8216] som EXT-X-DATERANGE. 

| **Attributnamn** | **Typ**                      | **Krävs?**                             | **Beskrivning**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| UTLÖSARE                | Citerad sträng                 | Obligatorisk                                  | Meddelandet kodas som en Base64-kodad sträng enligt beskrivningen i [RFC4648]. För [SCTE-35]-meddelanden är detta den base64-kodade splice_info_section ().                                                                                                                                      |
| TYP               | Citerad sträng                 | Obligatorisk                                  | En URN eller URL som identifierar meddelande schemat. För [SCTE-35]-meddelanden använder typen det särskilda värdet "scte35".                                                                                                                                                                          |
| ID                 | Citerad sträng                 | Obligatorisk                                  | En unik identifierare för händelsen. Om ID: t inte anges när meddelandet matas in kommer Azure Media Services att generera ett unikt ID.                                                                                                                                              |
| DURATION           | decimalt flytt ALS nummer | Obligatorisk                                  | Händelsens varaktighet. Om detta är okänt **ska** värdet vara 0. Enheter är factional sekunder.                                                                                                                                                                                           |
| Förfluten            | decimalt flytt ALS nummer | Valfritt, men krävs för glidande fönster | När signalen upprepas för att stödja ett glidande presentations fönster **måste** det här fältet vara den mängd av presentationen som har förflutit sedan händelsen började. Enheter är bråkiska sekunder. Det här värdet kan överskrida den ursprungliga angivna varaktigheten för en splice eller ett segment. |
| TIME               | decimalt flytt ALS nummer | Obligatorisk                                  | Händelsens presentations tid. Enheter är bråkiska sekunder.                                                                                                                                                                                                                        |

HLS Player-Programskiktet använder typen för att identifiera meddelandets format, avkoda meddelandet, tillämpa nödvändiga tids konverteringar och bearbeta händelsen.  Händelserna är tids synkroniserade i segment listan över överordnade spår enligt händelsens tidsstämpel.  De infogas före närmaste segment (#EXTINF tagg).


### <a name="323-hls-m3u8-manifest-example-using-adobe-primetime-ext-x-cue"></a>3.2.3 HLS. M3U8 manifest exempel med Adobe Primetime EXT-X-CUE

I följande exempel visas HLS-manifestet med hjälp av taggen Adobe Primetime EXT-X-CUE.  Parametern "CUE" innehåller bara egenskaperna TYPE och duration, vilket innebär att detta var en RTMP-källa med Adobe "Simple" mode-signalering.  
<!---If this was a SCTE-35 mode signal, the tag would include the base64 encoded binary SCTE-35 payload as seen in the [3.2.1.1 example](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).
--->


```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

```

### <a name="324-hls-message-handling-for-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-meddelande hantering för Adobe Primetime EXT-X-CUE

Händelser signaleras i listan över segment för varje video-och ljud spår. Positionen för EXT-X-CUE-taggen **måste** alltid vara omedelbart före det första HLS-segmentet (för splice out eller segment start) eller omedelbart efter det sista HLS-segmentet (för splice in-eller segment slut), vilket krävs av [Adobe-Primetime].

När ett glidande presentations fönster är aktiverat **måste** ext-X-Cue-taggen upprepas oftare så ofta att splice eller segmentet alltid är fullständigt beskrivet i segment listan och det förflutna attributet **måste** användas för att ange hur lång tid som splice eller segmentet har varit aktivt, enligt vad som krävs av [Adobe-Primetime].

När ett glidande presentations fönster är aktiverat tas EXT-X-CUE-taggarna bort från listan över segment när medie tiden som de hänvisar till har lyfts ut från fönstret med den glidande presentationen.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 STRECKs manifest dekoration (MPD)

[MPEGDASH] tillhandahåller tre sätt att signalera händelser:

1.  Händelser som signaleras i MPD-EventStream
2.  Händelser signalerade på-band med hjälp av händelse meddelande rutan ("EMSG")
3.  En kombination av både 1 och 2

Händelser som har signaler ATS i MPD-EventStream är användbara för VOD-direktuppspelning eftersom klienterna har åtkomst till alla händelser omedelbart när MPD laddas ned. Det är också användbart för SSAI-signaler, där den underordnade SSAI-leverantören behöver parsa signalerna från MPD-manifestet och infoga AD-innehåll dynamiskt.  Lösningen in-band (' EMSG ') är användbar för Live-direktuppspelning där klienterna inte behöver hämta MPD igen, eller så finns det ingen SSAI för manifest manipulering mellan klienten och ursprunget. 

Azure Media Services standard beteendet för bindestreck är att signalera både i MPD-EventStream och i-band med hjälp av händelse meddelande rutan ("EMSG").

Stack meddelanden som matats in över [RTMP] eller [MS-SSTR-inmatning] mappas till streck händelser, med hjälp av EMSG-rutorna och/eller i MPD-EventStreams. 

In-band-SCTE – 35 signalering för streck följer definitionen och kraven som definierats i [SCTE-214-3] och även i [streck-IF-IOP] avsnitt 13.12.2 ("SCTE35 Events"). 

För in-band-[SCTE-35]-transport använder händelse meddelande rutan ("EMSG") schemeId = "urn: SCTE: scte35:2013: bin". För dekoration av MPD-manifest EventStream-schemeId använder "urn: scte: scte35:2014: XML + bin".  Det här formatet är en XML-representation av händelsen som innehåller en binär Base64-kodad utdata från det fullständiga SCTE-35-meddelandet som kom in vid inmatningen. 

Normativ referens definitioner för transport av [SCTE-35] Cue-meddelanden i bindestreck är tillgängliga i [SCTE-214-1] SEK 6.7.4 (MPD) och [SCTE-214-3] SEC 7.3.2 (transport av SCTE 35 Cue-meddelanden).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG-streck (MPD) EventStream-signalering

Ett manifest (MPD) av händelser kommer att signaleras i det här MPD-objektet med hjälp av EventStream-elementet, som visas i period-elementet. SchemeId som används är "urn: scte: scte35:2014: XML + bin".


> [!NOTE]
> I det kortfattat-syfte [SCTE-35] kan använda det Base64-kodade avsnittet i signal. Binary-element (i stället för signal-SpliceInfoSection-elementet) som ett alternativ till att transportera ett fullständigt parsat Cue-meddelande.
> Azure Media Services använder den här metoden "XML + bin" för att signalera i MPD-manifestet.
> Detta är även den rekommenderade metoden som används i [bindestreck-IF-IOP] – Se avsnittet ["AD-infogning händelse strömmar" i bindestrecket om IOP-rikt linjen](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 


EventStream-elementet har följande attribut:

| **Attributnamn** | **Typ**                | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | sträng                  | Obligatorisk      | Identifierar meddelandets schema. Schemat anges till värdet för attributet Scheme i rutan Live Server manifest. Värdet **måste** vara en URN eller URL som identifierar meddelande schemat. De utdata-schemeId som stöds ska vara "urn: scte: scte35:2014: XML + bin" per [SCTE-214-1] SEK 6.7.4 (MPD), eftersom tjänsten endast stöder "XML + bin" för närvarande för det kortfattat i MPD. |
| värde              | sträng                  | Valfritt      | Ett ytterligare sträng värde som används av schemats ägare för att anpassa semantiken för meddelandet. För att kunna särskilja flera händelse strömmar med samma schema **måste** värdet anges till namnet på händelse strömmen (trackName for [MS-SSTR-inmatning] eller AMF-för [RTMP] intag).                                                                         |
| Tidsskala          | 32-bitars osignerat heltal | Obligatorisk      | Tids skalan i Tick per sekund.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 exempel händelse strömmar för MPEG-streck

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1-exempel på MPEG-streck. mpd-manifest signalering av RTMP streaming med hjälp av Adobe Simple mode

I följande exempel visas en utdrag-EventStream från Media Services dynamiska Paketeraren för en RTMP-dataström med hjälp av Adobe "enkel" läges signalering.

```xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
```


#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2-exempel på MPEG-streck. mpd-manifest signalering av en RTMP-dataström med Adobe SCTE-35-läge

I följande exempel visas en utdrag-EventStream från Media Services dynamiska Paketeraren för en RTMP-dataström med hjälp av Adobe SCTE-35-läge-signalering.

Exempel på EventStream-element med XML + lager plats format signalering per [SCTE-214-1]

```xml

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
```



> [!IMPORTANT]
> Observera att presentationTime är presentations tiden för händelsen [SCTE-35] översatt till att vara relativ till periodens start tid, inte för meddelandets införsel tid.
> [MPEGDASH] definierar Event@presentationTime som "anger presentations tiden för händelsen i förhållande till början av perioden.
> Värdet för presentations tiden i sekunder är indelningen av värdet för det här attributet och EventStream@timescale attributets värde.
> Om detta inte anges är värdet för presentations tiden 0.


#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1-exempel för MPEG-streck (MPD) med en EventStream med med Adobe Simple mode-signaler

I följande exempel visas utdata från den Media Services dynamiska Paketeraren för en käll-RTMP-dataström med hjälp av AD-signal metoden i Adobe "enkel" läge. Utdata är ett enda period manifest som visar en EventStream med schemeId-URI: n inställt på "urn: com: Adobe: dpi: simple: 2015" och Value Property inställd på "simplesignal".
Varje enkel signal tillhandahålls i ett händelse element med @presentationTime @duration egenskaperna,, och @id fylls i baserat på inkommande enkla signaler.

```xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

```

<!---
#### 3.3.3.2 Example MPEG DASH manifest (MPD) with multi-period, EventStream, using Adobe SCTE35 mode signaling

The following example shows the output from the Media Services dynamic packager for a source RTMP stream using the Adobe SCTE35 mode signaling.
In this case, the output manifest is a multi-period DASH .mpd with an EventStream element, and @schemeIdUri property set to "urn:scte:scte35:2014:xml+bin" and a @value property set to "scte35". Each Event element in the EventStream contains the full base64 encoded binary SCTE35 signal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

--->

### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>Varning för 3.3.4 MPEG-streck i band

En inbands händelse ström kräver att MPD har ett InbandEventStream-element på anpassnings uppsättnings nivån.  Det här elementet har ett obligatoriskt schemeIdUri-attribut och valfritt Tidsskale-attribut som också visas i händelse meddelande rutan (' EMSG ').  Händelse meddelande rutor med schema identifierare som inte har definierats i MPD **ska** inte finnas.

För in-band-[SCTE-35]-transport **måste** signaler använda schemeId = "urn: SCTE: scte35:2013: bin".
Normativa definitioner för transport av [SCTE-35] in-band-meddelanden definieras i [SCTE-214-3] s 7.3.2 (transport av SCTE 35 Cue-meddelanden).

Följande information beskriver de angivna värdena som klienten ska förvänta sig i EMSG i enlighet med [SCTE-214-3]:

| **Fält namn**          | **Fälttyp**          | **Krävs?** | **Beskrivning**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | sträng                  | Obligatorisk      | Identifierar meddelandets schema. Schemat anges till värdet för attributet Scheme i rutan Live Server manifest. Värdet **måste** vara en URN som identifierar meddelande schemat. För [SCTE-35]-meddelanden **måste** detta vara "urn: SCTE: scte35:2013: bin" i enlighet med [SCTE-214-3]          |
| Värde                   | sträng                  | Obligatorisk      | Ett ytterligare sträng värde som används av schemats ägare för att anpassa semantiken för meddelandet. För att skilja flera händelse strömmar med samma schema, anges värdet för händelse strömmen (trackName för utjämna inmatning eller AMF meddelande namn för RTMP-inmatning). |
| Tidsskala               | 32-bitars osignerat heltal | Obligatorisk      | Tids skalan, i Tick per sekund, för fälten tid och varaktighet i rutan "EMSG".                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bitars osignerat heltal | Obligatorisk      | Medie presentationens tids förändring av händelsens presentations tid och den tidigaste presentations tiden i det här segmentet. Presentationens tid och varaktighet **bör** justeras mot Stream-åtkomst punkter (SAP) av typ 1 eller 2, enligt definitionen i [ISO-14496-12] bilaga I.                                  |
| event_duration          | 32-bitars osignerat heltal | Obligatorisk      | Händelsens varaktighet eller 0xFFFFFFFF för att ange en okänd varaktighet.                                                                                                                                                                                                                              |
| Id                      | 32-bitars osignerat heltal | Obligatorisk      | Identifierar den här instansen av meddelandet. Meddelanden med motsvarande semantik måste ha samma värde. Om ID: t inte anges när meddelandet matas in kommer Azure Media Services att generera ett unikt ID.                                                                                        |
| Message_data            | bytematris              | Obligatorisk      | Händelse meddelandet. För [SCTE-35] meddelanden är meddelande data binärfilen splice_info_section () i enlighet med [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exempel på InBandEvenStream-entitet för Adobe Simple mode
```xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
```

### <a name="335-dash-message-handling"></a>Meddelande hantering för 3.3.5-datastreck

Händelser signaleras i band i rutan "EMSG" för både video-och ljud spår.  Signaleringen sker för alla segment begär Anden som presentation_time_delta är 15 sekunder eller mindre. 

När ett glidande presentations fönster är aktiverat tas händelse meddelanden bort från MPD när summan av tid och varaktighet för händelse meddelandet är mindre än tiden för medie data i manifestet.  Med andra ord tas händelse meddelandena bort från manifestet när medie tiden som de hänvisar till har lyfts ut från fönstret med den glidande presentationen.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE – 35 inmatnings vägledning för kodare-leverantörer

Följande rikt linjer är vanliga problem som kan påverka en kodares leverantörs implementering av den här specifikationen.  Rikt linjerna nedan har samlats in baserat på verklig feedback från vår partner för att göra det enklare att implementera den här specifikationen för andra. 

[SCTE-35] meddelanden matas in i binärformat med schemat **"urn: SCTE: scte35:2013: bin"** för [MS-SSTR-inmatning] och typen **"scte35"** för [RTMP] intag. För att under lätta konverteringen av [SCTE-35]-timing, som baseras på tidsstämpeler i MPEG-2-transportnivån (punkter), en mappning mellan punkter (pts_time + pts_adjustment av splice_time ()) och medie tids linjen tillhandahålls av händelse presentations tiden (fragment_absolute_time fältet för utmatnings-och tids fältet för RTMP-inmatning). Mappningen är nödvändig eftersom värdet 33-bit punkter rullar över ungefär var 26,5: e timme.

Smooth Streaming-inmatningen [MS-SSTR-inmatning] kräver att medie Data Box-enhet (' mdat ') **måste** innehålla **splice_info_section ()** som definierats i [SCTE-35]. 

För RTMP-inläsning anges attributet Cue för AMF-meddelandet Base64-kodat **splice_info_section ()** som definierats i [SCTE-35].  


När meddelandet har det format som beskrivs ovan skickas de till HLS-, utjämna-och streck-klienter enligt definitionen ovan.  

När du testar din implementering med Azure Media Services plattform börjar du testa med en "direkt" LiveEvent innan du går vidare till testning på en kodnings LiveEvent.

---

## <a name="change-history"></a>Ändra historik

| Datum     | Ändringar                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Ändrade RTMP-inmatnings stöd, har lagt till RTMP "onCuePoint" för grundämne Live                                            |
| 08/22/19 | Uppdaterat för att lägga till OnUserDataEvent i RTMP för anpassade metadata                                                          |
| 1/08/20  | Fast fel i RTMP enkel och RTMP SCTE35-läge. Har ändrats från "onCuePoint" till "onAdCue". Tabell med enkel läge har uppdaterats. |
| 08/4/20  | Tog bort stöd för DATERANGE-taggen för att matcha implementeringen i produktions tjänsten.    |

## <a name="next-steps"></a>Nästa steg
Visa Media Services utbildnings vägar.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
