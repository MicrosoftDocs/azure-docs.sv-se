---
rubrik: direkt avskrift: Azure Media Services Beskrivning: Lär dig mer om Azure Media Services Live-avskrift.  
tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. service: Media-Services MS. arbets belastning: Media ms.tgt_pltfrm: na MS. devlang: Ne MS. topic: instruktionen MS. Date: 08/31/2020 MS. author: inhenkel

---

# <a name="live-transcription-preview"></a>Direkt avskrift (för hands version)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media service levererar video, ljud och text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, levererar vår tjänst den inskrivna texten i IMSC 1.1-kompatibla TTML. Leveransen är paketerad i MPEG-4 del 30 (ISO/IEC 14496-30) fragment. Om du använder leverans via HLS/TS levereras texten som segmenterad VTT.

Ytterligare avgifter gäller när direkt avskrift har Aktiver ATS. Läs pris informationen i live video-avsnittet på [sidan Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/).

I den här artikeln beskrivs hur du aktiverar direkt avskrift när du strömmar en Live-händelse med Azure Media Services. Innan du fortsätter bör du kontrol lera att du är van att använda Media Services v3 REST-API: er (se [den här självstudien](stream-files-tutorial-with-rest.md) för mer information). Du bör också vara bekant med [Live streaming](stream-live-streaming-concept.md) -konceptet. Vi rekommenderar att du slutför [Stream Live med Media Services](stream-live-tutorial-with-api.md) själv studie kurs.

## <a name="live-transcription-preview-regions-and-languages"></a>Förhands gransknings regioner och språk i Live-avskrift

Direkt avskrift är tillgängligt i följande regioner:

- Sydostasien
- Europa, västra
- Europa, norra
- East US
- Central US
- USA, södra centrala
- USA, västra 2
- Brasilien, södra

Det här är en lista över tillgängliga språk som kan skrivas över, Använd språk koden i API: et.

| Språk | Språkkod |
| -------- | ------------- |
| Katalanska  | ca-ES |
| Danska (Danmark) | da-DK |
| Tyska (Tyskland) | de-DE |
| Engelska (Australien) | en – AU |
| Engelska (Kanada) | en-CA |
| Engelska (Storbritannien) | en-GB |
| Engelska (Indien) | en-IN |
| Engelska (Nya Zeeland) | en-NZ |
| Engelska (USA) | sv-SE |
| Spanska (Spanien) | es-ES |
| Spanska (Mexiko) | ES – MX |
| Finska (Finland) | fi-FI |
| Franska (Kanada) | fr-CA |
| Franska (Frankrike) | fr-FR |
| Italienska (Italien) | it-IT |
| Nederländska (Nederländerna) | nl-NL |
| Portugisiska (Brasilien) | pt-BR |
| Portugisiska (Portugal) | pt-PT |
| Svenska (Sverige) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Skapa Live-händelsen med direkt avskrift

Om du vill skapa en Live-händelse med avskriften aktive rad skickar du åtgärden med API-versionen 2019-05-01-Preview, till exempel:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

Åtgärden har följande text (där en direkt sändnings händelse skapas med RTMP som inmatnings protokoll). Observera tillägget av en avskrifts egenskap.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Starta eller stoppa avskrift efter att Live-händelsen har startat

Du kan starta och stoppa direkt avskrift medan Live-händelsen är i körnings läge. Mer information om att starta och stoppa Live-händelser finns i avsnittet långvariga åtgärder i [utveckla med Media Services v3-API: er](media-services-apis-overview.md#long-running-operations).

Om du vill aktivera Live-avskrifter eller uppdatera avskrifts språket, korrigerar du Live-händelsen så att den innehåller egenskapen "avskrifter". Om du vill inaktivera direkt avskrifter tar du bort egenskapen "avskrifter" från Live Event-objektet.  

> [!NOTE]
> Att aktivera eller inaktivera avskriften **mer än en gång** under Live-händelsen är inte ett scenario som stöds.

Det här är exempel anropet för att aktivera Live-avskrifter.

9.0a ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "useStaticHostname": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Avskrifts leverans och uppspelning

Läs artikeln [Översikt över dynamisk paketering](encode-dynamic-packaging-concept.md#to-prepare-your-source-files-for-delivery) i hur tjänsten använder dynamisk paketering för att leverera video, ljud och text i olika protokoll. När du publicerar din Live Stream med MPEG-streck eller HLS/CMAF, tillsammans med video och ljud, levererar vår tjänst den inskrivna texten i IMSC 1.1-kompatibla TTML. Den här leveransen paketeras i MPEG-4 del 30-fragment (ISO/IEC 14496-30). Om du använder leverans via HLS/TS levereras texten som segmenterad VTT. Du kan använda en webb spelare som [Azure Media Player](player-use-azure-media-player-how-to.md) för att spela upp strömmen.  

> [!NOTE]
> Om du använder Azure Media Player använder du version 2.3.3 eller senare.

## <a name="known-issues"></a>Kända problem

För för hands versionen är följande kända problem med direkt avskriftering:

- Apparna måste använda API: erna för för hands versionen som beskrivs i [specifikationen Media Services v3 openapi](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- DRM-skyddet (Digital Rights Management) gäller inte för text spårningen. det går bara att kryptera AES-kuvert.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Media Services](media-services-overview.md)
