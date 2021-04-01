---
title: Smooth Streaming protokoll (MS-SSTR) ändring för HEVC – Azure
description: I den här specifikationen beskrivs protokoll och format för fragmenterad MP4-baserad direkt uppspelning med HEVC i Azure Media Services. Endast de ändringar som krävs för att leverera HEVC anges i den här artikeln, förutom "(ingen ändring)" anger att texten bara kopieras för klargörande.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: 6454bc863cb5fd628d581fff380c5ab61354f762
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87053043"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Smooth Streaming protokoll (MS-SSTR) ändring för HEVC 

## <a name="1-introduction"></a>1 Introduktion 

Den här artikeln innehåller detaljerade ändringar som ska tillämpas på Smooth Streaming protokoll specifikationen [MS-SSTR] för att aktivera Smooth Streaming av HEVC-kodad video. I den här specifikationen disponerar vi bara de ändringar som krävs för att leverera HEVC video-codec. Artikeln följer samma nummer schema som [MS-SSTR]-specifikationen. De tomma rubrikerna som presenteras i artikeln finns för att orientera läsaren till deras position i [MS-SSTR]-specifikationen.  "(Ingen ändring)" anger att texten bara kopieras för klargörande syfte.

Artikeln innehåller tekniska implementerings krav för signalering av HEVC video-codec (med antingen "hev1"-eller "hvc1"-format spår) i ett Smooth Streaming manifest och normativa referenser uppdateras för att referera till aktuella MPEG-standarder som inkluderar HEVC, Common Encryption av HEVC och Box-namn för ISO Base-filformat har uppdaterats för att överensstämma med de senaste specifikationerna. 

Den refererade Smooth Streaming protokoll specifikationen [MS-SSTR] beskriver det kabel format som används för att leverera direktsända digitala media på begäran, till exempel ljud och video på följande sätt: från en kodare till en webb server, från en server till en annan server, och från en server till en HTTP-klient.
Användning av en MPEG-4-baserad ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787)-baserad data struktur leverans via http möjliggör sömlös växling i nära real tid mellan olika kvalitets nivåer för komprimerat medie innehåll. Resultatet är en konstant uppspelnings upplevelse för HTTP-klientens slutanvändare, även om villkoren för nätverks-och video åter givning har ändrats för klient datorn eller enheten.

## <a name="11-glossary"></a>1,1 ord lista 

Följande villkor definieras i *[MS-Glos]*:

>   **globalt unik identifierare (GUID) globalt unik identifierare (UUID)**

Följande villkor gäller för det här dokumentet:

>  **sammansättnings tid:** Tiden som ett exempel visas på klienten, enligt definitionen i   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **Cenc**: common Encryption, enligt definitionen i [ISO/IEC 23001-7] Second Edition.
> 
>   **avkodnings tid:** Tiden som ett exempel måste avkodas på klienten, enligt definitionen i   [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** En separat nedladdnings bar enhet av **Media** som omfattar ett eller flera **exempel**.

>   **Hevc:** Video kodning med hög effektivitet enligt definitionen i [ISO/IEC 23008-2]
> 
>   **manifest:** Metadata om **presentationen** som gör det möjligt för en klient att göra begär Anden för **Media**. **Media:** Komprimerade ljud-, video-och text data som används av klienten för att spela upp en **presentation**. **medie format:** Ett väldefinierat format för att representera ljud eller video som ett komprimerat **exempel**.
> 
>   **presentation:** Uppsättningen med alla **strömmar** och relaterade metadata som krävs för att spela upp en enda film. **begäran:** Ett HTTP-meddelande som skickas från klienten till servern, enligt definitionen i [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **svar:** ett http-meddelande som skickas från servern till klienten, enligt definitionen i [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **exempel:** Den minsta grundläggande enheten (till exempel en ram) där   **mediet** lagras och bearbetas.
> 
>   **kan, om inte, får inte,** får inte: Dessa villkor (i alla versaler) används enligt beskrivningen i   [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) alla instruktioner för valfria beteenden, kan, eller inte.

## <a name="12-references"></a>1,2 referenser

>   Referenser till Microsoft Open Specifications-dokumentation innehåller inte ett publicering sår eftersom länkar till den senaste versionen av dokumenten uppdateras ofta. Referenser till andra dokument inkluderar ett publicering sår när ett är tillgängligt.

### <a name="121-normative-references"></a>1.2.1 normativa referenser 

>  [MS-SSTR] Smooth Streaming protokoll *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)
> 
>   [ISO/IEC 14496-12] Internationella standardiseringsorganisationen, "Information Technology--kodning av ljud-visuella objekt – del 12: ISO Base Media-filformat", ISO/IEC 14496-12:2014, utgåva 4, plus korrigering 1, ändringar 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] Internationella standardiseringsorganisationen, "Information Technology--kodning av ljud-visuella objekt – del 15: transport av NAL-enhet strukturerad video i formatet ISO-basadress", ISO 14496-15:2015, utgåva 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informations teknik – hög effektivitets kod och medie leverans i heterogena miljöer – del 2: hög effektivitets video kodning: 2013 eller nyaste Edition   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informations teknik – MPEG Systems Technologies – del 7: gemensam kryptering i ISO Base-filer för mediefiler, CENC Edition 2:2015 <https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, parametrarna ' Codecs ' och ' profiler ' för "Bucket" medie typer " <https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] MP4-registrerings utfärdaren, "MP4REG", [http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "viktiga ord som används i RFC: er för att indikera krav nivåer", BCP 14, RFC 2119, mars 1997,   [https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2-informativa referenser 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocols huvud ord lista*".
> 
>   [RFC3548] Josefsson, S., ED., "Base16, Base32 och base64 data encodings", RFC 3548, juli 2003, [https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., ED., och Overell, P., "förstärkta BNF för syntax: ABNF", STD 68, RFC 5234, januari 2008,   [https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1,3 översikt 

>   Endast ändringar av den Smooth Streaming specifikationen som krävs för leverans av HEVC anges nedan. Oförändrade avsnitts rubriker visas i listan för att behålla platsen i den refererade Smooth Streaming specifikationen [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1,4 relation till andra protokoll 

## <a name="15-prerequisitespreconditions"></a>1,5 krav/förutsättningar 

## <a name="16-applicability-statement"></a>1,6 tillämplighets instruktion 

## <a name="17-versioning-and-capability-negotiation"></a>1,7 versions hantering och kapacitets förhandling 

## <a name="18-vendor-extensible-fields"></a>1,8 leverantör – utöknings bar fält 

>   Följande metod används för att identifiera strömmar med HEVC-video formatet:
> 
>   * **Anpassade beskrivande koder för medie format:** Den här funktionen tillhandahålls av **FourCC** -fältet, enligt vad som anges i avsnitt *2.2.2.5*.
>   Implementerare kan se till att tillägg inte står i konflikt med att registrera tilläggs koder med MPEG4-RA, enligt vad som anges i [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1,9 standard tilldelningar 

## <a name="2-messages"></a>2 meddelanden 

## <a name="21-transport"></a>2,1 transport

## <a name="22-message-syntax"></a>2,2-meddelande-syntax 

### <a name="221-manifest-request"></a>2.2.1-manifest förfrågan 

### <a name="222-manifest-response"></a>2.2.2 manifest svar 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabel):** Den lägre versionen av manifest svars meddelandet. MÅSTE anges till 2. (Ingen ändring)
> 
>   **Tids skala (variabel):** Tids skalan för attributet duration, anges som antalet steg i en sekund. Standardvärdet är 
> 1. (Ingen ändring)
> 
>    Det rekommenderade värdet är 90000 för att representera den exakta varaktigheten för video bild rutor och fragment som innehåller fraktions ram video (till exempel 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

ProtectionElement måste finnas när Common Encryption (CENC) har tillämpats på video-eller ljud strömmar. HEVC-krypterade strömmar uppfyller Common Encryption andra utgåvan [ISO/IEC 23001-7]. Endast sektor data i VCL NAL-enheter måste vara krypterade.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabel):** Tids skalan för varaktighets-och tids värden i den här data strömmen, anges som antalet steg i en sekund. Ett värde på 90000 rekommenderas för HEVC-strömmar. Ett värde som matchar våg samplings frekvensen (till exempel 48000 eller 44100) rekommenderas för ljud strömmar.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabel):** En kod med fyra tecken som identifierar vilket Media format som används för varje exempel. Följande värde intervall är reserverat med följande semantiska betydelser:
> 
> * "hev1": video exempel för den här spåret använder HEVC video med hjälp av exempel beskrivningen "hev1" som anges i [ISO/IEC-14496-15].
>
> * "hvc1": video exempel för den här spåret använder HEVC video med hjälp av exempel beskrivningen "hvc1" som anges i [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variabel):** Data som anger parametrar som är unika för medie formatet och som är gemensamma för alla exempel i spåret, som visas som en sträng med hex-kodade byte. Format och semantisk innebörd i byte ordningen varierar med värdet för **FourCC** -fältet enligt följande:
> 
>   * När en TrackElement beskriver HEVC video, är **FourCC** -fältet lika med **"hev1"** eller **"hvc1"**
> 
>   Fältet **CodecPrivateData** måste innehålla en hex-kodad sträng representation av följande byte-sekvens, som anges i ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (ingen ändring från MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField innehåller sekvens parameter uppsättningen (SPS).
> 
>   * PPSField innehåller en sektor parameter uppsättning (PPS).
> 
>   Obs! video parameter uppsättningen (VPS) finns inte i CodecPrivateData, men ska finnas i fil huvudet för lagrade filer i rutan hvcC. System som använder Smooth Streaming protokoll måste signalera ytterligare avkodnings parametrar (till exempel HEVC-nivå) med hjälp av det anpassade attributet "codec".

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Fältet **Major version för SMOOTHSTREAMINGMEDIA** måste vara inställt på 2, och **MINORVERSION** -fältet måste anges till 2. (Ingen ändring)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3-fragment förfrågan 

>   **Obs!** standard medie formatet som krävs för **MinorVersion** 2 och ' hev1 ' eller ' hvc1 ' är ' Iso8 ' varumärke ISO Base Media-filformat som anges i [ISO/IEC 14496-12] ISO Base Media-filformat, fjärde utgåvan, och [ISO/IEC 23001-7] common Encryption andra utgåvan.

### <a name="224-fragment-response"></a>2.2.4 fragment-svar 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** är inaktuell och dess funktion ersätts av den avkodnings tid för spår fragment (' tfdt ') som anges i avsnittet [ISO/IEC 14496-12] 8.8.12.
> 
>   **Obs!** en klient kan beräkna varaktigheten för ett fragment genom att summera de exempel varaktigheter som anges i rutan spåra körning (' Trun ') eller multiplicera antalet prover gånger som standard samplings varaktighet. BaseMediaDecodeTime i tfdt plus Fragmentets varaktighet är lika med URL-tidsparametern för nästa fragment.
> 
>   En referens tid för en producent (' prft ') bör infogas före en film fragment ruta (' moof ') vid behov, för att ange den UTC-tid som motsvarar avkodnings tiden för spår fragment för det första exemplet som refereras till i rutan film fragment, enligt vad som anges i avsnittet [ISO/IEC 14496-12] 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** är inaktuell och dess funktion ersätts av den avkodnings tid för spår fragment (' tfdt ') som anges i avsnittet [ISO/IEC 14496-12] 8.8.12.
> 
>   **Obs!** en klient kan beräkna varaktigheten för ett fragment genom att summera de exempel varaktigheter som anges i rutan spåra körning (' Trun ') eller multiplicera antalet prover gånger som standard samplings varaktighet. BaseMediaDecodeTime i tfdt plus Fragmentets varaktighet är lika med URL-tidsparametern för nästa fragment. Se till att adresserna i förväg är föråldrade eftersom de fördröjer Direktsänd strömning.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** och relaterade fält kapslar in standardvärden för varje exempel-metadata i fragmentet. **TfhdBox** -fältets syntax är en strikt delmängd av syntaxen för rubrik rutan spåra fragment som definieras i avsnittet [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 8.8.7.
> 
>   **BaseDataOffset (8 byte):** Förskjutningen, i byte, från början av **MdatBox** -fältet till exempel fältet i fältet **MdatBox** . För att signalera den här begränsningen måste standardvärdet-basen-moof flagga (0x020000) anges.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** och relaterade fält inkapslas per exempel-metadata för det begärda fragmentet. Syntaxen för **TrunBox** är en strikt delmängd av körnings rutan version 1 spåra fragment som definieras i avsnittet [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 byte):** Tids förskjutningen för exempel kompositionen för varje exempel justerat så att presentations tiden för det första visade exemplet i fragmentet är lika med avkodnings tiden för det första avkodade exemplet. Exempel på kompositioner för negativa video prov skall användas.
> 
>   enligt definitionen i   [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   OBS! detta förhindrar att ett videosynkroniseringsfel som orsakas av video isolerings ljud är lika med den största avkodade bildbuffertens borttagnings fördröjning.
> 
>   Syntaxen för fälten som definieras i det här avsnittet, som anges i ABNF   [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) förblir samma, förutom följande:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>Vanliga fält för 2.2.4.9 fragment-svar 

### <a name="225-sparse-stream-pointer"></a>2.2.5 sparse Stream-pekare 

### <a name="226-fragment-not-yet-available"></a>2.2.6 fragment är inte tillgängligt än 

### <a name="227-live-ingest"></a>2.2.7 Live-intag 

#### <a name="2271-filetype"></a>2.2.7.1-filtyp 

>   **Filtyp (variabel):** anger under typen och avsedd användning av MPEG-4-filen ([[MPEG4-ra])](https://go.microsoft.com/fwlink/?LinkId=327787) och High-Level-attribut.
> 
>   **MajorBrand (variabel):** Medie filens huvudsakliga varumärke. MÅSTE vara inställd på "ISML".
> 
>   **MinorVersion (variabel):** Den lägre versionen av medie filen. MÅSTE vara inställt på 1.
> 
>   **CompatibleBrands (variabel):** Anger de varumärken som stöds av MPEG-4.
>   MÅSTE innehålla "CCFF" och "iso8".
> 
>   Syntaxen för fälten som definieras i det här avsnittet, som anges i ABNF   [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) är följande:

```properties
FileType = MajorBrand MinorVersion CompatibleBrands
MajorBrand = STRING_UINT32
MinorVersion = STRING_UINT32
CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)
```

**Obs!** kompatibiliteten "CCFF" och "iso8" visar att fragment följer "common container File Format" och common Encryption [ISO/IEC 23001-7] och ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5-fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 spåra utökat sidhuvud 

### <a name="228-server-to-server-ingest"></a>2.2.8 Server till Server 

## <a name="3-protocol-details"></a>3 protokoll information 


## <a name="31-client-details"></a>3,1-klient information 

### <a name="311-abstract-data-model"></a>3.1.1 abstrakt data modell 

#### <a name="3111-presentation-description"></a>Beskrivning av 3.1.1.1-presentation 

>   Data elementet presentations Beskrivning kapslar in alla metadata för presentationen.
> 
>   Metadata för presentation: en uppsättning metadata som är gemensamma för alla strömmar i presentationen. Metadata för presentationen består av följande fält som anges i avsnitt *2.2.2.1*:
> 
> * **Major version**
> * **MinorVersion**
> * **Tidsplan**
> * **Varaktighet**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   Presentationer som innehåller HEVC strömmar skall ange:

```properties
MajorVersion = 2
MinorVersion = 2
```

>   LookaheadCount = 0 (Obs! rutor föråldrade)
> 
>   Presentationer bör också anges:

```properties
TimeScale = 90000
```

>   Stream-samling: en samling data element för data Ströms Beskrivning enligt vad som anges i avsnittet *3.1.1.1.2*.
> 
>   Skydds Beskrivning: en samling skydds systemets metadata Beskrivning data element enligt vad som anges i avsnittet *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>Beskrivning av metadata för 3.1.1.1.1 Protection System 

>   Data elementet för data för metadata i skydds systemet kapslar in metadata som är unika för ett enda Content Protection System. (Ingen ändring)
> 
>   Beskrivning av skydds rubrik: metadata för innehålls skydd som hör till ett enda Content Protection System. Beskrivning av skydds rubrik omfattar följande fält, som anges i avsnitt *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>Beskrivning av 3.1.1.1.2-Stream 

###### <a name="311121-track-description"></a>Beskrivning av 3.1.1.1.2.1-spår 

###### <a name="3111211-custom-attribute-description"></a>Beskrivning av 3.1.1.1.2.1.1 anpassad attribut 

##### <a name="3113-fragment-reference-description"></a>Referens beskrivning för 3.1.1.3-fragment 

###### <a name="31131-track-specific-fragment-reference-description"></a>Beskrivning av referens för 3.1.1.3.1 Track-Specific fragment 

#### <a name="3112-fragment-description"></a>Beskrivning av 3.1.1.2-fragment 

##### <a name="31121-sample-description"></a>Beskrivning av 3.1.1.2.1-exempel 

### <a name="312-timers"></a>3.1.2 timers 

### <a name="313-initialization"></a>3.1.3-initiering 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Higher-Layer utlösta händelser 

#### <a name="3141-open-presentation"></a>3.1.4.1 öppen presentation 

#### <a name="3142-get-fragment"></a>3.1.4.2 Hämta fragment 

#### <a name="3143-close-presentation"></a>3.1.4.3 Stäng presentation 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 för bearbetning av händelser och ordningsföljd 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 manifest förfrågan och manifest svar 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 fragment-begäran och fragment svar

## <a name="32-server-details"></a>Information om 3,2-Server

## <a name="33-live-encoder-details"></a>3,3 Live Encoder-information 

## <a name="4-protocol-examples"></a>4 protokoll exempel 

## <a name="5-security"></a>5 säkerhet 

## <a name="51-security-considerations-for-implementers"></a>5,1 säkerhets aspekter för implementerare

>   Om innehållet som transporteras med det här protokollet har ett högt kommersiellt värde, bör ett Content Protection system användas för att förhindra obehörig användning av innehållet. **ProtectionElement** kan användas för att hantera metadata som är relaterade till användningen av ett Content Protection System. Skyddat ljud-och video innehåll måste krypteras enligt vad som anges av MPEG Common Encryption andra utgåvan: 2015 [ISO/IEC 23001-7].
> 
>   **Obs!** för hevc-video krypteras endast segment data i VCL NALs. Segment rubriker och andra NALs är tillgängliga för presentations program före dekryptering. i en säker video Sök väg är krypterad information inte tillgänglig för presentations program.

## <a name="52-index-of-security-parameters"></a>5,2 index för säkerhets parametrar 


| **Säkerhets parameter**  | **Avsnitt**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Common Encryption rutor | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5,3 Common Encryption rutor

Följande rutor kan finnas i fragment-svar när Common Encryption tillämpas och anges i [ISO/IEC 23001-7] eller [ISO/IEC 14496-12]:

1.  Skydds systemets Specific Head Box (' pssh ')

2.  Exempel på krypterings ruta (' senc ')

3.  Exempel på extra information om förskjutnings rutan (' saio ')

4.  Exempel på hjälp informationens storleks ruta (' Saiz ')

5.  Beskrivnings ruta för exempel grupp (' SGPD ')

6.  Exempel till grupp ruta (' SBGP ')

---

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
