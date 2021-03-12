---
title: Schema för Azure Media Services utdata-metadata | Microsoft Docs
description: Den här artikeln ger en översikt över Azure Media Services schemat för utdata.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 519aa158d60eae97eb4c1b792bcecfc8a6c066e0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008305"
---
# <a name="output-metadata"></a>Metadata för utdata

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt
Ett kodnings jobb är associerat med en ingångs till gång (eller till gångar) som du vill utföra vissa kodnings uppgifter på. Koda till exempel en MP4-fil till H. 264 MP4-anpassade bit hastighets uppsättningar; skapa en miniatyr bild. Skapa överlägg. När en aktivitet har slutförts skapas en utmatnings till gång.  Utmatnings till gången innehåller video, ljud, miniatyrer osv. Till gången till utdata innehåller också en fil med metadata om utmatnings till gången. Namnet på XML-filen med metadata har följande format: &lt; source_file_name &gt;_manifest.xml (till exempel BigBuckBunny_manifest.xml).  

Media Services förebyggande syfte inte för att generera metadata. Metadata för indata genereras bara som en artefakt när en indata-till gång bearbetas i ett jobb. Därför skrivs denna artefakt till utmatnings till gången. Olika verktyg används för att generera metadata för indata till gångar och utgående till gångar. Därför har metadata för indata ett något annorlunda schema än utdata-metadata.

Om du vill undersöka metadatafilen kan du skapa en **SAS** -lokaliserare och ladda ned filen till den lokala datorn.  

I den här artikeln beskrivs element och typer i XML-schemat där utdata-metada ( &lt; source_file_name &gt;_manifest.xml) är baserad. Information om filen som innehåller metadata om indata till gången finns i metadata för indata.  

Du hittar den fullständiga schema koden och XML-exemplet i slutet av den här artikeln.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a> AssetFiles rot element
Samling av AssetFile-poster för kodnings jobbet.  

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs = "0" maxOccurs = "1" |Ett AssetFile-element som ingår i AssetFiles-samlingen. |

## <a name="assetfile-element"></a><a name="AssetFile"></a> AssetFile-element
Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br/><br/> Obligatorisk |**XS: String** |Medie till gångens fil namn. |
| **Storlek**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: Long** |Storlek på till gångs filen i byte. |
| **Varaktighet**<br/><br/> Obligatorisk |**XS: duration** |Innehållets uppspelnings varaktighet. |

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **Källor** |Samling med indatafiler/källfiler, som bearbetades för att skapa den här AssetFile. Mer information finns i käll element. |
| **VideoTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Varje fysisk AssetFile kan innehålla i noll eller flera videor spårar överlagrad i ett lämpligt behållar format. Mer information finns i VideoTracks-element. |
| **AudioTracks**<br/><br/> minOccurs = "0" maxOccurs = "1" |Varje fysisk AssetFile kan innehålla i noll eller flera ljud spår som är överlagrade i ett lämpligt behållar format. Detta är samlingen av alla dessa ljud spår. Mer information finns i AudioTracks-element. |

## <a name="sources-element"></a><a name="Sources"></a> Element för källor
Samling med indatafiler/källfiler, som bearbetades för att skapa den här AssetFile.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **Källa**<br/><br/> minOccurs = "1" maxOccurs = "obunden" |En indata-/käll fil som används när du skapar den här till gången. Mer information finns i käll element. |

## <a name="source-element"></a><a name="Source"></a> Käll element
En indata-/käll fil som används när du skapar den här till gången.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Namn**<br/><br/> Obligatorisk |**XS: String** |Fil namn för indatakälla. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a> VideoTracks-element
Varje fysisk AssetFile kan innehålla i noll eller flera videor spårar överlagrad i ett lämpligt behållar format. Elementet **VideoTracks** representerar en samling med alla video spår.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs = "1" maxOccurs = "obunden" |Ett särskilt video spår i den överordnade AssetFile. Mer information finns i VideoTrack-element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a> VideoTrack-element
Ett särskilt video spår i den överordnade AssetFile.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Identitet**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Noll-baserat index för det här video spåret. **Obs:**  Detta **ID** är inte nödvändigt vis det TrackID som används i en MP4-fil. |
| **FourCC**<br/><br/> Obligatorisk |**XS: String** |Video-codec FourCC-kod. |
| **Profil** |**XS: String** |H264,-profil (gäller endast H264,-codec). |
| **Nivå** |**XS: String** |H264,-nivå (gäller endast H264,-codec). |
| **Bredd**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Kodad video bredd i bild punkter. |
| **Höjd**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Kodad video höjd i bild punkter. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: Double** |Täljare för bild förhållande i bild förhållande. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: Double** |Nämnare för bild förhållande i bild. |
| **Bildfrekvens**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: decimal** |Uppmätt video bild Rute frekvens i. 3F-format. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: decimal** |Förinställt mål video bild Rute frekvens i. 3F-format. |
| **Hastigheten**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Genomsnittlig video bit hastighet i kilobit per sekund, beräknat från AssetFile. Räknar endast den grundläggande nytto lasten i data strömmen och omfattar inte förpacknings omkostnaderna. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Genomsnittlig bit hastighet för mål för det här video spåret enligt begäran via kodnings inställningen i kilobit per sekund. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**XS: int** |Högsta GOP genomsnittlig bit hastighet för det här video spåret i kilobit per sekund. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a> AudioTracks-element
Varje fysisk AssetFile kan innehålla i noll eller flera ljud spår som är överlagrade i ett lämpligt behållar format. **AudioTracks** -elementet representerar en samling med alla dessa ljud spår.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs = "1" maxOccurs = "obunden" |Ett särskilt ljud spår i den överordnade AssetFile. Mer information finns i AudioTrack-element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a> AudioTrack-element
Ett särskilt ljud spår i den överordnade AssetFile.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **Identitet**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Noll-baserat index för det här ljud spåret. **Obs:**  Detta är inte nödvändigt vis TrackID som används i en MP4-fil. |
| **ADPCM** |**XS: String** |Ljud spårets codec-sträng. |
| **EncoderVersion** |**XS: String** |Valfri kodare versions sträng som krävs för EAC3. |
| **Kanaler**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Antal ljud kanaler. |
| **SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Ljud samplings frekvens i sampel/SEK eller Hz. |
| **Hastigheten**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Genomsnittlig ljud bit hastighet i bitar per sekund, beräknat från AssetFile. Räknar endast den grundläggande nytto lasten i data strömmen och omfattar inte förpacknings omkostnaderna. |
| **BitsPerSample**<br/><br/> minInclusive = "0"<br/><br/> Obligatorisk |**XS: int** |Bitar per sampel för format typen wFormatTag. |

### <a name="child-elements"></a>Underordnade element
| Name | Beskrivning |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs = "0" maxOccurs = "1" |Resultat parametrar för loudness-avläsning. Mer information finns i LoudnessMeteringResultParameters-element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a> LoudnessMeteringResultParameters-element
Resultat parametrar för loudness-avläsning.  

Du hittar ett XML-exempel med [XML-](#xml)exempel.  

### <a name="attributes"></a>Attribut
| Namn | Typ | Beskrivning |
| --- | --- | --- |
| **DPLMVersionInformation** |**XS: String** |**Dolby** Professional loudness-avläsning Development Kit-version. |
| **DialogNormalization**<br/><br/> minInclusive = "-31" maxInclusive = "-1"<br/><br/> Obligatorisk |**XS: int** |DialogNormalization som genererats via DPLM, krävs när LoudnessMetering har angetts |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive = "10"<br/><br/> Obligatorisk |**XS: Float** |Integrerad loudness |
| **IntegratedLoudnessUnit**<br/><br/> Obligatorisk |**XS: String** |Integrerad loudness-enhet. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Obligatorisk |**XS: String** |Hantera-ID |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive = "100" |**XS: Float** |Tal innehåll över programmet, i procent. |
| **SamplePeak**<br/><br/> Obligatorisk |**XS: Float** |Det absoluta exempel värdets topp, sedan reset eller sen det senast tömdes, per kanal.  Enheter är dBFS. |
| **SamplePeakUnit**<br/><br/> fast = "dBFS"<br/><br/> Obligatorisk |**XS: anySimpleType** |Exempel på hög belastnings enhet. |
| **TruePeak**<br/><br/> Obligatorisk |**XS: Float** |Högsta sanna högsta värde, som per ITU-R BS. 1770-2, sedan återställt eller sedan det senast rensades, per kanal. Enheter är dBTP. |
| **TruePeakUnit**<br/><br/> fast = "dBTP"<br/><br/> Obligatorisk |**XS: anySimpleType** |True Top-enhet. |

## <a name="schema-code"></a>Schema kod
```xml
<?xml version="1.0" encoding="utf-8"?>  
<xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
            elementFormDefault="qualified">  
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
              <xs:element name="Sources">  
                <xs:annotation>  
                  <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:attribute name="Name" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>input source file name</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="VideoTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                </xs:annotation>  
                <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="VideoTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
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
                        <xs:attribute name="FourCC" type="xs:string" use="required">  
                          <xs:annotation>  
                            <xs:documentation>video codec FourCC code</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Profile" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="Level" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                        <xs:attribute name="Framerate" use="required">  
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
                        <xs:attribute name="TargetFramerate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:decimal">  
                              <xs:minInclusive value="0"/>  
                              <xs:fractionDigits value="3"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="TargetBitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                      </xs:complexType>  
                    </xs:element>  
                  </xs:sequence>  
                </xs:complexType>  
              </xs:element>  
              <xs:element name="AudioTracks" minOccurs="0">  
                <xs:annotation>  
                  <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                </xs:annotation>  
                 <xs:complexType>  
                  <xs:sequence>  
                    <xs:element name="AudioTrack" maxOccurs="unbounded">  
                      <xs:annotation>  
                        <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                      </xs:annotation>  
                      <xs:complexType>  
                        <xs:sequence>  
                          <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                            <xs:annotation>  
                              <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                            </xs:annotation>  
                            <xs:complexType>  
                              <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="DialogNormalization" use="required">  
                                <xs:annotation>  
                                  <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:int">  
                                    <xs:minInclusive value="-31"/>  
                                    <xs:maxInclusive value="-1"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudness" use="required">  
                                <xs:annotation>  
                                  <xs:documentation>Integrated loudness</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="-70"/>  
                                    <xs:maxInclusive value="10"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                <xs:annotation>  
                                  <xs:documentation>Gating identifier</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                <xs:annotation>  
                                  <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                </xs:annotation>  
                                <xs:simpleType>  
                                  <xs:restriction base="xs:float">  
                                    <xs:minInclusive value="0"/>  
                                    <xs:maxInclusive value="100"/>  
                                  </xs:restriction>  
                                </xs:simpleType>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                              </xs:attribute>  
                              <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                <xs:annotation>  
                                  <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                </xs:annotation>  
                              </xs:attribute>  
                              <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                              </xs:attribute>  
                            </xs:complexType>  
                          </xs:element>  
                        </xs:sequence>  
                        <xs:attribute name="Id" use="required">  
                          <xs:annotation>  
                            <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="Codec" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>audio track codec string</xs:documentation>  
                          </xs:annotation>  
                        </xs:attribute>  
                        <xs:attribute name="EncoderVersion" type="xs:string">  
                          <xs:annotation>  
                            <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                        <xs:attribute name="Bitrate" use="required">  
                          <xs:annotation>  
                            <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                          </xs:annotation>  
                          <xs:simpleType>  
                            <xs:restriction base="xs:int">  
                              <xs:minInclusive value="0"/>  
                            </xs:restriction>  
                          </xs:simpleType>  
                        </xs:attribute>  
                        <xs:attribute name="BitsPerSample" use="required">  
                          <xs:annotation>  
                            <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
            <xs:attribute name="Duration" use="required">  
              <xs:annotation>  
                <xs:documentation>content play back duration</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:duration"/>  
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

Följande XML är ett exempel på filen utdata-metadata.  

```xml
<AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
            xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
  <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
      <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <VideoTracks>  
      <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
    </VideoTracks>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
  <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
    <Sources>  
      <Source Name="BigBuckBunny.mp4"/>  
    </Sources>  
    <AudioTracks>  
      <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
    </AudioTracks>  
  </AssetFile>  
</AssetFiles>  
```

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
