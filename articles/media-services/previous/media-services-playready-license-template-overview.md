---
title: Översikt för Media Services PlayReady-licensmall
description: Det här avsnittet ger en översikt över en PlayReady-licens mal len som används för att konfigurera PlayReady-licenser.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: fddce5d0-1278-478f-ae05-9b985c748731
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 6bcf6b439f8e49d194e1c0ea5dc1e996135589cf
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454319"
---
# <a name="media-services-playready-license-template-overview"></a>Översikt för Media Services PlayReady-licensmall

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services tillhandahåller nu en tjänst för att leverera PlayReady-licenser. När spelaren (till exempel Silverlight) försöker spela upp ditt PlayReady-skyddade innehåll, skickas en begäran till licens leverans tjänsten för att få en licens. Om licens tjänsten godkänner begäran utfärdar den den licens som skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

Media Services tillhandahåller också API: er som du kan använda för att konfigurera dina PlayReady-licenser. Licenser innehåller de rättigheter och begränsningar som du vill att en PlayReady-Digital Rights Management (DRM) kör för att genomdriva när en användare försöker spela upp skyddat innehåll.
Här följer några exempel på PlayReady License-begränsningar som du kan ange:

* Datum och tid då licensen är giltig.
* DateTime-värdet när licensen upphör att gälla. 
* För att licensen ska sparas i beständig lagring på klienten. Beständiga licenser används vanligt vis för att tillåta uppspelning av innehåll offline.
* Den minsta säkerhets nivå som en spelare måste ha för att kunna spela upp ditt innehåll. 
* Utmatnings skydds nivån för utdata-kontrollerna för audio\video-innehåll. 
* Mer information finns i avsnittet "utdata Controls" (3,5) i dokumentet med [reglerna för efterlevnadsprinciper](https://www.microsoft.com/playready/licensing/compliance/) .

> [!NOTE]
> För närvarande kan du bara konfigurera PlayRight för PlayReady-licensen. Den här behörigheten krävs. PlayRight ger klienten möjlighet att spela upp innehållet. Du kan också använda PlayRight för att konfigurera begränsningar som är begränsade till uppspelning. Mer information finns i [PlayReadyPlayRight](media-services-playready-license-template-overview.md#PlayReadyPlayRight).
> 
> 

Om du vill konfigurera PlayReady-licenser med hjälp av Media Services måste du konfigurera Media Services PlayReady-licens mal len. Mallen definieras i XML.

I följande exempel visas den enklaste (och vanligaste) mallen som konfigurerar en Basic streaming-licens. Med den här licensen kan dina klienter spela upp ditt PlayReady-skyddade innehåll.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PlayReadyLicenseResponseTemplate xmlns:i="https://www.w3.org/2001/XMLSchema-instance" 
                                  xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
  <LicenseTemplates>
    <PlayReadyLicenseTemplate>
      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
      <PlayRight />
    </PlayReadyLicenseTemplate>
  </LicenseTemplates>
</PlayReadyLicenseResponseTemplate>
```

XML-koden överensstämmer med det XML-schema för PlayReady-licensserver som definierats i avsnittet "PlayReady-licens mal len XML-schema".

Media Services definierar också en uppsättning .NET-klasser som kan användas för att serialisera och deserialisera till och från XML. En beskrivning av huvud klasserna finns i [Media Services .NET-klasser](media-services-playready-license-template-overview.md#classes) som används för att konfigurera licensfiler.

Ett slut punkt till slut punkt-exempel som använder .NET-klasser för att konfigurera PlayReady-licensservern finns i [använda PlayReady Dynamic Encryption och licens leverans tjänsten](media-services-protect-with-playready-widevine.md).

## <a name="media-services-net-classes-that-are-used-to-configure-license-templates"></a><a id="classes"></a>Media Services .NET-klasser som används för att konfigurera licensfiler
Följande klasser är de viktigaste .NET-klasserna som används för att konfigurera Media Services PlayReady-licensfiler. Dessa klasser mappar till de typer som definieras i [PlayReady-licensservern XML-schema](media-services-playready-license-template-overview.md#schema).

[MediaServicesLicenseTemplateSerializer](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.mediaserviceslicensetemplateserializer#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_mediaserviceslicensetemplateserializer) -klassen används för att serialisera och deserialisera till och från Media Services licens mal len XML.

### <a name="playreadylicenseresponsetemplate"></a>PlayReadyLicenseResponseTemplate
[PlayReadyLicenseResponseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicenseresponsetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicenseresponsetemplate): den här klassen representerar mallen för svaret som skickas tillbaka till användaren. Det innehåller ett fält för en anpassad data sträng mellan licens servern och programmet (som kan vara användbart för anpassad app-logik). Den innehåller också en lista med en eller flera licensfiler.

Som "översta nivån"-klassen i mapphierarkin innehåller svars mal len en lista över licensfiler. Licens mal len omfattar (direkt eller indirekt) alla andra klasser som utgör mallens data som ska serialiseras.

### <a name="playreadylicensetemplate"></a>PlayReadyLicenseTemplate
[PlayReadyLicenseTemplate](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadylicensetemplate#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadylicensetemplate): den här klassen representerar en licens mal len som används för att skapa PlayReady-licenser som ska returneras till användare. Den innehåller data i innehålls nyckeln i licensen. Den innehåller också alla rättigheter eller begränsningar som PlayReady-körningsmiljön-körningsmiljön måste framtvinga när innehålls nyckeln används.

### <a name="playreadyplayright"></a><a id="PlayReadyPlayRight"></a>PlayReadyPlayRight
[PlayReadyPlayRight](/dotnet/api/microsoft.windowsazure.mediaservices.client.contentkeyauthorization.playreadyplayright#microsoft_windowsazure_mediaservices_client_contentkeyauthorization_playreadyplayright): den här klassen representerar PlayRight för en PlayReady-licens. Den ger användaren möjlighet att spela upp innehåll som omfattas av eventuella begränsningar som kon figurer ATS i licensen och på själva PlayRight (för en uppspelnings princip). En stor del av principen på en PlayRight avser begränsningar för utdata som styr vilka typer av utdata som innehållet kan spelas upp över. Den innehåller också alla begränsningar som måste placeras på plats när en specifik utmatning används. Om DigitalVideoOnlyContentRestriction till exempel är aktive rad tillåter DRM-körningen bara att videon visas över digitala utdata. (Analoga video utmatningar är inte tillåtna för att skicka innehållet.)

> [!IMPORTANT]
> Dessa typer av begränsningar kan vara kraftfulla, men de kan också påverka konsument upplevelsen. Om utdatafilerna är för begränsade kan det hända att innehållet inte kan spelas upp på vissa klienter. Mer information finns i reglerna för [PlayReady-efterlevnad](https://www.microsoft.com/playready/licensing/compliance/).
> 
> 

Ett exempel på de skydds nivåer som Silverlight stöder finns i [Silverlight-stöd för skydd av utdata](/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc838192(v=vs.95)).

## <a name="playready-license-template-xml-schema"></a><a id="schema"></a>Versions schema för PlayReady-licensserver
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:ser="http://schemas.microsoft.com/2003/10/Serialization/" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:import namespace="http://schemas.microsoft.com/2003/10/Serialization/" />
  <xs:complexType name="AgcAndColorStripeRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction" />
  <xs:simpleType name="ContentType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Unspecified" />
      <xs:enumeration value="UltravioletDownload" />
      <xs:enumeration value="UltravioletStreaming" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="ContentType" nillable="true" type="tns:ContentType" />
  <xs:complexType name="ExplicitAnalogTelevisionRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="BestEffort" type="xs:boolean" />
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ExplicitAnalogTelevisionRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction" />
  <xs:complexType name="PlayReadyContentKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="PlayReadyContentKey" nillable="true" type="tns:PlayReadyContentKey" />
  <xs:complexType name="ContentEncryptionKeyFromHeader">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence />
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromHeader" nillable="true" type="tns:ContentEncryptionKeyFromHeader" />
  <xs:complexType name="ContentEncryptionKeyFromKeyIdentifier">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:PlayReadyContentKey">
        <xs:sequence>
          <xs:element minOccurs="0" name="KeyIdentifier" type="ser:guid" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="ContentEncryptionKeyFromKeyIdentifier" nillable="true" type="tns:ContentEncryptionKeyFromKeyIdentifier" />
  <xs:complexType name="PlayReadyLicenseResponseTemplate">
    <xs:sequence>
      <xs:element name="LicenseTemplates" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
      <xs:element minOccurs="0" name="ResponseCustomData" nillable="true" type="xs:string">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseResponseTemplate" nillable="true" type="tns:PlayReadyLicenseResponseTemplate" />
  <xs:complexType name="ArrayOfPlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfPlayReadyLicenseTemplate" nillable="true" type="tns:ArrayOfPlayReadyLicenseTemplate" />
  <xs:complexType name="PlayReadyLicenseTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AllowTestDevices" type="xs:boolean" />
      <xs:element minOccurs="0" name="BeginDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element name="ContentKey" nillable="true" type="tns:PlayReadyContentKey" />
      <xs:element minOccurs="0" name="ContentType" type="tns:ContentType">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExpirationDate" nillable="true" type="xs:dateTime">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="GracePeriod" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="LicenseType" type="tns:PlayReadyLicenseType" />
      <xs:element minOccurs="0" name="PlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyLicenseTemplate" nillable="true" type="tns:PlayReadyLicenseTemplate" />
  <xs:simpleType name="PlayReadyLicenseType">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Nonpersistent" />
      <xs:enumeration value="Persistent" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="PlayReadyLicenseType" nillable="true" type="tns:PlayReadyLicenseType" />
  <xs:complexType name="PlayReadyPlayRight">
    <xs:sequence>
      <xs:element minOccurs="0" name="AgcAndColorStripeRestriction" nillable="true" type="tns:AgcAndColorStripeRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AllowPassingVideoContentToUnknownOutput" type="tns:UnknownOutputPassingOption">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="AnalogVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="CompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="DigitalVideoOnlyContentRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ExplicitAnalogTelevisionOutputRestriction" nillable="true" type="tns:ExplicitAnalogTelevisionRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="FirstPlayExpiration" nillable="true" type="ser:duration">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComponentVideoRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ImageConstraintForAnalogComputerMonitorRestriction" type="xs:boolean">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalAudioOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
      <xs:element minOccurs="0" name="UncompressedDigitalVideoOpl" nillable="true" type="xs:int">
        <xs:annotation>
          <xs:appinfo>
            <DefaultValue EmitDefaultValue="false" xmlns="http://schemas.microsoft.com/2003/10/Serialization/" />
          </xs:appinfo>
        </xs:annotation>
      </xs:element>
    </xs:sequence>
  </xs:complexType>
  <xs:element name="PlayReadyPlayRight" nillable="true" type="tns:PlayReadyPlayRight" />
  <xs:simpleType name="UnknownOutputPassingOption">
    <xs:restriction base="xs:string">
      <xs:enumeration value="NotAllowed" />
      <xs:enumeration value="Allowed" />
      <xs:enumeration value="AllowedWithVideoConstriction" />
    </xs:restriction>
  </xs:simpleType>
  <xs:element name="UnknownOutputPassingOption" nillable="true" type="tns:UnknownOutputPassingOption" />
  <xs:complexType name="ScmsRestriction">
    <xs:sequence>
      <xs:element minOccurs="0" name="ConfigurationData" type="xs:unsignedByte" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ScmsRestriction" nillable="true" type="tns:ScmsRestriction" />
</xs:schema>
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]