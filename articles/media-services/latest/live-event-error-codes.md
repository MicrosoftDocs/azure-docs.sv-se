---
title: Fel koder för Azure Media Services Live-händelse
description: Den här artikeln innehåller fel koder för Live-händelser.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: error-reference
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7c30649fe3486f812569cb51f609356a6cbfd58f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627548"
---
# <a name="media-services-live-event-error-codes"></a>Fel koder för Media Services Live-händelse

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I följande tabeller visas fel koderna för [Live-händelser](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

När du prenumererar på [Event Grid](../../event-grid/index.yml) händelser för en Live-händelse kan du se något av följande fel från händelsen [LiveEventConnectionRejected](monitoring/media-services-event-schemas.md\#liveeventconnectionrejected) .
> [!div class="mx-tdCol2BreakAll"]
>| Fel | Information |
>|--|--|
>|**MPE_RTMP_APPID_AUTH_FAILURE** ||
>|Beskrivning | Felaktig hämtnings-URL |
>|Föreslagen lösning| APPID är en GUID-token i RTMP inläsnings-URL. Se till att den matchar med inmatnings-URL från API. |
>|**MPE_INGEST_ENCODER_CONNECTION_DENIED** ||
>| Beskrivning |Kodarens IP finns inte i den konfigurerade listan över tillåtna IP-adresser |
>| Föreslagen lösning| Kontrol lera att kodarens IP-adress finns i listan över tillåtna IP-adresser. Använd ett online-verktyg, till exempel *whoismyip* eller *CIDR-kalkylatorn* , för att ange rätt värde.  Se till att kodaren kan komma åt servern innan den faktiska Live-händelsen. |
>|**MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED** ||
>| Beskrivning|RTMP-kodaren skickade inte `setDataFrame` kommandot. |
>| Föreslagen lösning|De flesta kommersiella kodare skickar data Ströms metadata. För en kodare som push-överför en enskild bit hastighet är det inte säkert att problemet uppstår. LiveEvent kan beräkna inkommande bit hastighet när Stream-metadata saknas.  För inmatning med flera bit hastigheter för en PassThru-kanal eller ett dubbelt push-scenario kan du försöka lägga till frågesträngen med "videodatarate" och "audiodatarate" i inmatnings-URL: en. Det ungefärliga värdet kan fungera. Enheten är i kbit. Till exempel  `rtmp://hostname:1935/live/GUID_APPID/streamname?videodatarate=5000&audiodatarate=192` |
>|**MPE_INGEST_CODEC_NOT_SUPPORTED** ||
>| Beskrivning|Den angivna codecen stöds inte.|
>| Föreslagen lösning| LiveEvent tog emot en codec som inte stöds. Till exempel en RTMP-inmatning, fick LiveEvent en video-codec som inte är AVC.  Kontrol lera förval för kodare. |
>|**MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED** ||
>| Beskrivning |Medie beskrivnings informationen togs inte emot innan de faktiska medie data levererades. |
>| Föreslagen lösning|LiveEvent får inte data Ströms beskrivningen (rubrik eller FLV-tagg) från kodaren. Detta är ett protokoll fel. Kontakta kodarens leverantör. |
>|**MPE_INGEST_MEDIA_QUALITIES_EXCEEDED** ||
>| Beskrivning|Antalet kvaliteter för ljud-eller video typ överskrider den maximalt tillåtna gränsen. |
>| Föreslagen lösning|När live event mode är Live Encoding bör kodaren sända en enda bit hastighet av video och ljud.  Observera att en redundant push från samma bit hastighet tillåts. Kontrol lera inställningarna för förval eller utdata för kodare för att se till att den matar ut en enda bit ström. |
>|**MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED** ||
>| Beskrivning|Den totala inkommande bit hastigheten i en Live-händelse eller kanal tjänst överskred den maximalt tillåtna gränsen. |
>| Föreslagen lösning|Kodaren överskred den högsta inkommande bit hastigheten. Den här gränsen aggregerar alla inkommande data från den bidragande kodaren. Minska bit hastigheten genom att kontrol lera inställningarna för kodare eller utdata. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** ||
>| Beskrivning|Tidsstämpeln för video-eller ljud-FLVTag är ogiltig från RTMP-kodaren. |
>| Föreslagen lösning|Inaktuellt. |
>|**MPE_INGEST_FRAMERATE_EXCEEDED** ||
>| Beskrivning|Inkommande kodare inmatade data strömmar med bild Rute hastigheter översteg den högsta tillåtna 30 fps för kodning av Live-händelser/-kanaler. |
>| Föreslagen lösning|Kontrol lera förval för kodare för att sänka bild frekvensen till under 36 fps. |
>|**MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED** ||
>| Beskrivning|Inkommande kodade inmatade data strömmar översteg följande tillåtna lösningar: 1920x1088 för att koda Live-händelser/kanaler och 4096 x 2160 för direkt sändnings händelser/kanaler. |
>| Föreslagen lösning|Kontrol lera att kodarens förval motsvarar lägre video upplösning så att den inte överskrider gränsen. |
>|**MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV** |
>| Beskrivning|Live-händelsen har fått en stor mängd ljuddata samtidigt, eller en stor mängd video data utan några viktiga bild rutor. Vi har frånkopplat kodaren för att ge den möjlighet att försöka igen med rätt data. |
>| Föreslagen lösning|Se till att kodaren skickar en nyckel ram för varje nyckel intervall (GOP).  Aktivera inställningar som "konstant bit hastighet (CBR)" eller "justera nyckel ramar". Ibland kan det vara hjälp att återställa den bidragnde koden. Kontakta Encoder-leverantören om det inte hjälper. |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Du kan se något av följande fel från händelsen [LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md\#liveeventencoderdisconnected) .

> [!div class="mx-tdCol2BreakAll"]
>| Fel | Information |
>|--|--|
>|**MPE_RTMP_SESSION_IDLE_TIMEOUT** |
>| Beskrivning|RTMP-sessionen nådde tids gränsen efter inaktivitet för tillåten tids gräns. |
>|Föreslagen lösning|Detta inträffar vanligt vis när en kodare slutar att ta emot inmatningen så att sessionen blir inaktiv eftersom det inte finns några data att skicka. Kontrol lera om kodarens eller inmatnings flödets status är i felfritt tillstånd. |
>|**MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID** |
>|Beskrivning| Tidsstämpeln för video-eller ljud-FLVTag är ogiltig från RTMP-kodaren. |
>| Föreslagen lösning| Inaktuellt. |
>|**MPE_CAPACITY_LIMIT_REACHED** |
>| Beskrivning|Kodaren skickar data för snabbt. |
>| Föreslagen lösning|Detta inträffar när kodaren överför en stor uppsättning fragment under en kort period.  Detta kan teoretiskt inträffa när kodaren inte kan skicka data för när det är på grund av ett nätverks problem och de överförda data burst-data när nätverket är tillgängligt. Hitta orsaken från Encoder-loggen eller system loggen. |
>|**Okända felkoder** |
>| Beskrivning| Dessa felkoder kan vara från minnes fel till dubbla poster i hash-mappningen. Detta kan inträffa när kodaren skickar ut en stor uppsättning fragment under en kort period.  Detta kan också inträffa när kodaren inte kan skicka data till när den på grund av ett nätverks problem och sedan skickar alla fördröjda fragment samtidigt när nätverket blir tillgängligt. |
>|Föreslagen lösning| Kontrol lera kodarens loggar.|

## <a name="other-error-codes"></a>Andra felkoder

> [!div class="mx-tdCol2BreakAll"]
>| Fel | Information |Avvisad/frånkopplad händelse|
>|--|--|--|
>|**ERROR_END_OF_MEDIA** ||Ja|
>| Beskrivning|Detta är ett allmänt fel. ||
>|Föreslagen lösning| Inga.||
>|**MPI_SYSTEM_MAINTENANCE** ||Ja|
>| Beskrivning|Kodaren kopplades från på grund av tjänst uppdatering eller system underhåll. ||
>|Föreslagen lösning|Se till att kodaren aktiverar "Auto Connect". Det gör det möjligt för kodaren att återansluta till den redundanta Live-händelsens slut punkt som inte är i underhålls läge. ||
>|**MPE_BAD_URL_SYNTAX** ||Ja|
>| Beskrivning|Inmatnings-URL: en är felaktigt formaterad. ||
>|Föreslagen lösning|Kontrol lera att inmatnings-URL: en är korrekt formaterad. För RTMP bör det vara `rtmp[s]://hostname:port/live/GUID_APPID/streamname` ||
>|**MPE_CLIENT_TERMINATED_SESSION** ||Ja|
>| Beskrivning|Kodaren kopplade från sessionen.  ||
>|Föreslagen lösning|Detta är inte ett fel. Kodaren initierade från koppling, inklusive korrekt från koppling. Om det är en oväntad från koppling kontrollerar du kodarens loggar. |
>|**MPE_INGEST_BITRATE_NOT_MATCH** ||Inga|
>| Beskrivning|Inkommande data hastighet matchar inte förväntad bit hastighet. ||
>|Föreslagen lösning|Detta är en varning som inträffar när inkommande data hastighet är för långsam eller snabbt. Kontrol lera Encoder-loggen eller system loggen.||
>|**MPE_INGEST_DISCONTINUITY** ||Inga|
>| Beskrivning| Det finns discontinuty i inkommande data.||
>|Föreslagen lösning| Detta är en varning om att kodaren släpper data på grund av ett nätverks problem eller ett system resurs problem. Kontrol lera Encoder-loggen eller system loggen. Övervaka system resursen (CPU, minne eller nätverk) också. Om systemets CPU är för hög, försök att sänka bit hastigheten eller Använd alternativet H/W Encoder från system grafik kortet.||

## <a name="see-also"></a>Se även

[Felkoder för strömnings slut punkt (ursprung)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Nästa steg

[Självstudie: strömma live med Media Services](stream-live-tutorial-with-api.md)
