---
title: Azure Media Services schema för indata-metadata | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services schemat för indata-metadata.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: eebe1c77c9af6791be94c16f0f792a9e7deb9a6e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017417"
---
# <a name="input-metadata"></a>Metadata för indata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ett kodnings jobb är associerat med en ingångs till gång (eller till gångar) som du vill utföra vissa kodnings uppgifter på.  När en aktivitet har slutförts skapas en utmatnings till gång.  Utmatnings till gången innehåller video, ljud, miniatyrer, manifest osv. Till gången till utdata innehåller också en fil med metadata om inmatnings till gången. Namnet på XML-filen med metadata har följande format: &lt; asset_id &gt;_metadata.xml (till exempel 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), där &lt; asset_id &gt; är AssetID-värdet för indata till gången.  

Media Services förebyggande syfte inte för att generera metadata. Metadata för indata genereras bara som en artefakt när en indata-till gång bearbetas i ett jobb. Därför skrivs denna artefakt till utmatnings till gången. Olika verktyg används för att generera metadata för indata till gångar och utgående till gångar. Därför har metadata för indata ett något annorlunda schema än utdata-metadata.

Om du vill undersöka metadatafilen kan du skapa en **SAS** -lokaliserare och ladda ned filen till den lokala datorn. Du hittar ett exempel på hur du skapar en SAS-lokaliserare och laddar ned en fil  [med hjälp av Media Services .NET SDK-tillägg](media-services-dotnet-get-started.md).  

I den här artikeln beskrivs element och typer för XML-schemat som indata-metada ( &lt; asset_id &gt;_metadata.xml) baseras på.  Information om filen som innehåller metadata om utmatnings till gången finns i [utdata-metadata](media-services-output-metadata-schema.md).  

Du kan hitta [schema koden](media-services-input-metadata-schema.md#code) ett [XML-exempel](media-services-input-metadata-schema.md#xml) i slutet av den här artikeln.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a> AssetFiles-element (rot element)
Innehåller en samling av [AssetFile-element](media-services-input-metadata-schema.md#AssetFile)för kodnings jobbet.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

| Name | Beskrivning |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "obunden" |Ett enda underordnat element. Mer information finns i [AssetFile-element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile-element
 Innehåller attribut och element som beskriver en till gångs fil.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br /><br /> Obligatorisk |**XS: String** |Till gångs fil namn. |
| **Storlek**<br /><br /> Obligatorisk |**XS: Long** |Storlek på till gångs filen i byte. |
| **Varaktighet**<br /><br /> Obligatorisk |**XS: duration** |Innehållets uppspelnings varaktighet. Exempel: duration = "PT25M 37.757 S". |
| **NumberOfStreams**<br /><br /> Obligatorisk |**XS: int** |Antal strömmar i till gångs filen. |
| **FormatNames**<br /><br /> Obligatorisk |**XS: String** |Format namn. |
| **FormatVerboseNames**<br /><br /> Obligatorisk |**XS: String** |Formatera utförliga namn. |
| **/St** |**XS: duration** |Start tid för innehåll. Exempel: StartTime = "PT 2.669 S". |
| **OverallBitRate** |**XS: int** |Genomsnittlig bit hastighet för till gångs filen i kbit/s. |

> [!NOTE]
> Följande fyra underordnade element måste finnas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" | |Samling av alla [program element](media-services-input-metadata-schema.md#Programs) när till gångs filen är i MPEG-TS-format. |
| **VideoTracks**<br /><br /> minOccurs = "0" | |Varje fysisk till gångs fil kan innehålla noll eller flera video spår som överlämnas till ett lämpligt behållar format. Det här elementet innehåller en samling med alla [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) som ingår i till gångs filen. |
| **AudioTracks**<br /><br /> minOccurs = "0" | |Varje fysisk till gångs fil kan innehålla noll eller flera ljud spår som överlämnas till ett lämpligt behållar format. Det här elementet innehåller en samling med alla [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) som ingår i till gångs filen. |
| **Metadata**<br /><br /> minOccurs = "0" maxOccurs = "obunden" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Till gångs filens metadata representeras som key\value-strängar. Exempel:<br /><br /> **&lt;Metadata-nyckel = "språk" värde = "eng"/&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a> TrackType
Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Identitet**<br /><br /> Obligatorisk |**XS: int** |Noll-baserat index för det här ljud-eller video spåret.<br /><br /> Detta är inte nödvändigt vis att TrackID som används i en MP4-fil. |
| **ADPCM** |**XS: String** |Video spårets codec-sträng. |
| **CodecLongName** |**XS: String** |Ljud-eller video spårs-codec långt namn. |
| **Tids**<br /><br /> Obligatorisk |**XS: String** |Tids bas. Exempel: tidbas = "1/48000" |
| **NumberOfFrames** |**XS: int** |Antal bild rutor (som finns för video spår). |
| **/St** |**XS: duration** |Spåra start tid. Exempel: StartTime = "PT 2.669 S" |
| **Varaktighet** |**XS: duration** |Spår varaktighet. Exempel: duration = "PTSampleFormat M 37.757 S". |

> [!NOTE]
> Följande två underordnade element måste finnas i en sekvens.  
> 
> 

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Innehåller presentations information (till exempel om ett visst ljud spår är för synskadade visnings program). |
| **Metadata**<br /><br /> minOccurs = "0" maxOccurs = "obunden" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Generiska nyckel/värde-strängar som kan användas för att lagra en rad olika uppgifter. Till exempel Key = "Language" och value = "eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a> AudioTrackType (ärver från TrackType)
 **AudioTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

 Typen representerar ett särskilt ljud spår i till gångs filen.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **SampleFormat** |**XS: String** |Exempel format. |
| **ChannelLayout** |**XS: String** |Kanalens layout. |
| **Kanaler**<br /><br /> Obligatorisk |**XS: int** |Antal (0 eller fler) ljud kanaler. |
| **SamplingRate**<br /><br /> Obligatorisk |**XS: int** |Ljud samplings frekvens i sampel/SEK eller Hz. |
| **Hastigheten** |**XS: int** |Genomsnittlig ljud bit hastighet i bitar per sekund, beräknat från till gångs filen. Endast den grundläggande data Ströms nytto lasten räknas och förpacknings omkostnaderna ingår inte i det här antalet. |
| **BitsPerSample** |**XS: int** |Bitar per sampel för format typen wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a> VideoTrackType (ärver från TrackType)
**VideoTrackType** är en global komplex typ som ärver från [TrackType](media-services-input-metadata-schema.md#TrackType).  

Typen representerar ett särskilt video spår i till gångs filen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **FourCC**<br /><br /> Obligatorisk |**XS: String** |Video-codec FourCC-kod. |
| **Profil** |**XS: String** |Video spårets profil. |
| **Nivå** |**XS: String** |Video spårets nivå. |
| **PixelFormat** |**XS: String** |Video spårets bild punkts format. |
| **Bredd**<br /><br /> Obligatorisk |**XS: int** |Kodad video bredd i bild punkter. |
| **Höjd**<br /><br /> Obligatorisk |**XS: int** |Kodad video höjd i bild punkter. |
| **DisplayAspectRatioNumerator**<br /><br /> Obligatorisk |**XS: Double** |Täljare för bild förhållande i bild förhållande. |
| **DisplayAspectRatioDenominator**<br /><br /> Obligatorisk |**XS: Double** |Nämnare för bild förhållande i bild. |
| **DisplayAspectRatioDenominator**<br /><br /> Obligatorisk |**XS: Double** |Bild förhållande – täljare för video exempel. |
| **SampleAspectRatioNumerator** |**XS: Double** |Bild förhållande – täljare för video exempel. |
| **SampleAspectRatioNumerator** |**XS: Double** |Bild förhållande, nämnare. |
| **Ram**<br /><br /> Obligatorisk |**XS: decimal** |Uppmätt video bild Rute frekvens i. 3F-format. |
| **Hastigheten** |**XS: int** |Genomsnittlig video bit hastighet i kilobit per sekund, beräknat från till gångs filen. Endast den grundläggande data Ströms nytto lasten räknas och förpacknings omkostnaderna ingår inte. |
| **MaxGOPBitrate** |**XS: int** |Högsta GOP genomsnittlig bit hastighet för det här video spåret i kilobit per sekund. |
| **HasBFrames** |**XS: int** |Video spårs nummer för B-ramar. |

## <a name="metadatatype"></a><a name="MetadataType"></a> MetadataType
**MetadataType** är en global komplex typ som beskriver metadata för en till gångs fil som nyckel/värde-strängar. Till exempel Key = "Language" och value = "eng".  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **knapp**<br /><br /> Obligatorisk |**XS: String** |Nyckeln i nyckel/värde-paret. |
| **värde**<br /><br /> Obligatorisk |**XS: String** |Värdet i nyckel/värde-paret. |

## <a name="programtype"></a><a name="ProgramType"></a> ProgramType
**ProgramType** är en global komplex typ som beskriver ett program.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **ProgramId**<br /><br /> Obligatorisk |**XS: int** |Program-ID |
| **NumberOfPrograms**<br /><br /> Obligatorisk |**XS: int** |Antal program. |
| **PmtPid**<br /><br /> Obligatorisk |**XS: int** |Program kart tabeller (PMTs) innehåller information om program.  Mer information finns i [betalning](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Obligatorisk |**XS: int** |Används av avkodare. Mer information finns i [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**XS: Long** |Startar tids stämpling för presentationen. |
| **EndPTS** |**XS: Long** |Tids stämpling för presentationen avslutas. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** är en global komplex typ som beskriver data strömmen.  

Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Standardvärde**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att detta är standard presentationen. |
| **Dub**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att detta är dubbed-presentationen. |
| **Ursprunglig**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att detta är den ursprungliga presentationen. |
| **Kommentar**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 om du vill visa att det här spåret innehåller kommentarer. |
| **Låt**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 om du vill att det här spåret ska innehålla sång texter. |
| **Karaoke**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att detta representerar Karaoke-spåret (Bakgrunds musik, inga Vocals). |
| **Anses**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att detta är den framtvingade presentationen. |
| **HearingImpaired**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 för att indikera att det här spåret är för personer som är svåra att höra. |
| **VisualImpaired**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 om du vill att det här spåret ska vara visuellt nedkopplat. |
| **CleanEffects**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 om du vill visa att det här spåret har rena effekter. |
| **AttachedPic**<br /><br /> Obligatorisk |**XS: int** |Ange det här attributet till 1 om du vill visa att det här spåret innehåller bilder. |

## <a name="programs-element"></a><a name="Programs"></a> Program element
Omslutnings element som innehåller flera **program** element.  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "obunden" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |För till gångs filer som är i MPEG-TS-format, innehåller information om program i till gångs filen. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks-element
 Omslutnings element som innehåller flera **VideoTrack** -element.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Underordnade element
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "obunden" |[VideoTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Innehåller information om video spår i till gångs filen. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks-element
 Omslutnings element som innehåller flera **AudioTrack** -element.  

 Se ett XML-exempel i slutet av den här artikeln: [XML-exempel](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>ämnen
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "obunden" |[AudioTrackType (ärver från TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Innehåller information om ljud spår i till gångs filen. |

## <a name="schema-code"></a><a name="code"></a> Schema kod
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
            elementFormDefault="qualified">  

  <xs:complexType name="MetadataType">  
    <xs:attribute name="key"   type="xs:string" use="required"/>  
    <xs:attribute name="value" type="xs:string" use="required"/>  
  </xs:complexType>  

  <xs:complexType name="ProgramType">  
    <xs:attribute name="ProgramId" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Program Id</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>Number of programs</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PmtPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pmt pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="PcrPid" type="xs:int" use="required">  
      <xs:annotation>  
        <xs:documentation>pcr pid</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="StartPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>start pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="EndPTS" type="xs:long">  
      <xs:annotation>  
        <xs:documentation>end pts</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="StreamDispositionType">  
    <xs:attribute name="Default"          type="xs:int" use="required" />  
    <xs:attribute name="Dub"              type="xs:int" use="required" />  
    <xs:attribute name="Original"         type="xs:int" use="required" />  
    <xs:attribute name="Comment"          type="xs:int" use="required" />  
    <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
    <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
    <xs:attribute name="Forced"           type="xs:int" use="required" />  
    <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
    <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
    <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
    <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
  </xs:complexType>  

  <xs:complexType name="TrackType" abstract="true">  
    <xs:sequence>  
      <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
      <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
    </xs:sequence>  
    <xs:attribute name="Id" use="required">  
      <xs:annotation>  
        <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="Codec" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec string</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="CodecLongName" type="xs:string">  
      <xs:annotation>  
        <xs:documentation>video track codec long name</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="TimeBase"  type="xs:string" use="required">  
      <xs:annotation>  
        <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="NumberOfFrames">  
      <xs:annotation>  
        <xs:documentation>number of frames</xs:documentation>  
      </xs:annotation>  
      <xs:simpleType>  
        <xs:restriction base="xs:int">  
          <xs:minInclusive value="0"/>  
        </xs:restriction>  
      </xs:simpleType>  
    </xs:attribute>  
    <xs:attribute name="StartTime" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
    <xs:attribute name="Duration" type="xs:duration">  
      <xs:annotation>  
        <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
      </xs:annotation>  
    </xs:attribute>  
  </xs:complexType>  

  <xs:complexType name="VideoTrackType">  
    <xs:annotation>  
      <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="FourCC" type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>video codec FourCC code</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Profile" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>profile</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Level" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>level</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PixelFormat" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>Video track's pixel format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Width" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video width in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Height" use="required">  
          <xs:annotation>  
            <xs:documentation>encoded video height in pixels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
          <xs:annotation>  
            <xs:documentation>video display aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioNumerator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SampleAspectRatioDenominator">  
          <xs:annotation>  
            <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:double">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="FrameRate" use="required">  
          <xs:annotation>  
            <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:decimal">  
              <xs:minInclusive value="0"/>  
              <xs:fractionDigits value="3"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="MaxGOPBitrate">  
          <xs:annotation>  
            <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="HasBFrames" type="xs:int">  
          <xs:annotation>  
            <xs:documentation>video track number of B frames</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:complexType name="AudioTrackType">  
    <xs:annotation>  
      <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
    </xs:annotation>  
    <xs:complexContent>  
      <xs:extension base="TrackType">  
        <xs:attribute name="SampleFormat"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>sample format</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="ChannelLayout"  type="xs:string">  
          <xs:annotation>  
            <xs:documentation>channel layout</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Channels" use="required">  
          <xs:annotation>  
            <xs:documentation>number of audio channels</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="SamplingRate" use="required">  
          <xs:annotation>  
            <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Bitrate">  
          <xs:annotation>  
            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="BitsPerSample">  
          <xs:annotation>  
            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
      </xs:extension>  
    </xs:complexContent>  
  </xs:complexType>  

  <xs:element name="AssetFiles">  
    <xs:annotation>  
      <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
    </xs:annotation>  
    <xs:complexType>  
      <xs:sequence>  
        <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
          <xs:annotation>  
            <xs:documentation>asset file</xs:documentation>  
          </xs:annotation>  
          <xs:complexType>  
            <xs:sequence>  
              <xs:element name="Programs" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
            </xs:sequence>  
            <xs:attribute name="Name" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>the media asset file name</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Size" use="required">  
              <xs:annotation>  
                <xs:documentation>size of file in bytes</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:long">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Duration" type="xs:duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
              <xs:annotation>  
                <xs:documentation>number of streams in asset file</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatNames" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>format verbose names</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="StartTime" type="xs:duration">  
              <xs:annotation>  
                <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="OverallBitRate">  
              <xs:annotation>  
                <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:complexType>  
        </xs:element>  
      </xs:sequence>  
    </xs:complexType>  
  </xs:element>  
</xs:schema>  
```


## <a name="xml-example"></a><a name="xml"></a> XML-exempel
Följande är ett exempel på filen med indata-metadata.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
  <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
    <VideoTracks>  
      <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
      </VideoTrack>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
        <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
        <Metadata key="language" value="eng" />  
        <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
      </AudioTrack>  
    </AudioTracks>  
    <Metadata key="major_brand" value="mp42" />  
    <Metadata key="minor_version" value="0" />  
    <Metadata key="compatible_brands" value="mp42mp41" />  
    <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
    <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

