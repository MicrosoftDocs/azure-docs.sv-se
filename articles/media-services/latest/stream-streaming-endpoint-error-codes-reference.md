---
title: Azure Media Services paketering och ursprungs fel
description: I det här avsnittet beskrivs fel som kan uppstå från Orgin-tjänsten (Azure Media Services streaming endpoint).
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
ms.date: 05/07/2019
ms.author: inhenkel
ms.openlocfilehash: 8ba1dd5657e1163648983fcb172aa7b14aaa06cd
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106282293"
---
# <a name="streaming-endpoint-origin-errors"></a>Fel med slutpunkter för direktuppspelning (ursprung)

I det här avsnittet beskrivs fel som kan uppstå från Azure Media Services [streaming Endpoint service](stream-streaming-endpoint-concept.md).

## <a name="400-bad-request"></a>400 – Felaktig begäran

Begäran innehåller ogiltig information och avvisas av dessa felkoder och av någon av följande orsaker:

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL-syntax eller format fel. Exempel på detta är en begäran om ogiltig typ, ett ogiltigt fragment eller ett ogiltigt spår. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Begäran saknar en krypterings kod i URL: en. CMAF-begäranden kräver en krypterings tag i URL: en. Andra protokoll som har kon figurer ATS med fler än en krypterings typ kräver också krypterings tag gen för untvetydighet. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Begäran om lagrings utrymme för att uppfylla begäran misslyckades med ett felaktigt fel i begäran. |

## <a name="403-forbidden"></a>403 – Förbjuden

Begäran tillåts inte på grund av en av följande orsaker:

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Begäran om lagring för att uppfylla begäran misslyckades med ett autentiseringsfel. Detta kan inträffa om lagrings nycklarna roterades och tjänsten inte kunde synkronisera lagrings nycklarna. <br/><br/>Kontakta Azure-supporten genom att gå till [Hjälp + Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure Portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Lagrings åtgärds fel, åtkomst misslyckades på grund av otillräcklig behörighet för kontot. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |Det gick inte att utföra begäran på lagrings platsen eftersom lagrings kontot är inaktiverat. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Lagrings åtgärds fel, åtkomst misslyckades på grund av allmänna fel. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |Utdataformatet blockeras på grund av konfigurationen i StreamingPolicy. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Kryptering krävs för innehållet, leverans principen krävs för utdataformatet. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Ingen kryptering har angetts i leverans princip inställningarna. |

## <a name="404-not-found"></a>404 – Hittades inte

Åtgärden försöker vidta en resurs som inte längre finns. Till exempel kanske resursen redan har tagits bort.

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Det gick inte att hitta det begärda spåret. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |Det gick inte att hitta den begärda resursen. |
|MPE_UNAUTHORIZED |0x80890244 |Åtkomsten är inte tillåten. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |Det gick inte att hitta den begärda tidsstämpeln. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Det gick inte att hitta det begärda dynamiska manifest filtret. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |Det begärda fragment indexet ligger utanför det giltiga intervallet. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Det går inte att hitta Live Media-poster för att hämta Moov-bufferten. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Det gick inte att hitta fragmentet vid den begärda tiden för ett visst spår.<br/><br/>Kan vara att det inte finns någon lagrings enhet. Prova ett annat lager i presentationen som kan ha ett fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Det gick inte att hitta medie posten för den begärda bit hastigheten i manifestet. <br/><br/>Kan vara att spelaren frågade efter ett video spår av en viss bit hastighet som inte fanns i manifestet.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Det gick inte att hitta vissa metadata i manifestet eller så gick det inte att hitta ombasen från Storage. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Lagrings åtgärds fel, resursen hittades inte. |

## <a name="409-conflict"></a>409 – Konflikt

Det ID som angetts för en resurs på en `PUT` eller en `POST` åtgärd har gjorts av en befintlig resurs. Använd ett annat ID för resursen för att lösa det här problemet.

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Lagrings åtgärds fel, konflikt fel.  |

## <a name="410"></a>410

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|För direkt uppspelning, när det filter som har forceEndTimestamp angetts till true, är start-eller slutdatumets tidstämpel utanför det aktuella DVR-fönstret.|

## <a name="412-precondition-failure"></a>412 för villkors haveri

Åtgärden angav en eTag som skiljer sig från den version som är tillgänglig på servern, det vill säga ett optimistiskt samtidighets fel. Försök att utföra åtgärden igen när du har läst in den senaste versionen av resursen och uppdaterat eTag i förfrågan.

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Det begärda fragmentet är inte klart.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Lagrings åtgärds fel, ett förhands fel.|

## <a name="415-unsupported-media-type"></a>415 medie typ stöds inte

Nytto Last formatet som skickas av klienten är i ett format som inte stöds.

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Bör inte använda kryptering på redan krypterat innehåll.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |Krypteringen är ogiltig för det angivna formatet.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Leverans princip typen är ogiltig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |De ursprungliga inställningarna kan delas av flera format.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|Medie formatet eller typen stöds inte. Media Services har till exempel inte stöd för antalet kvalitets nivåer som är över 64. I FLV-videotaggen har Media Services inte stöd för en video RAM med flera SPS och flera PPS-filer.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| Inspelnings formatet för den begärda till gången stöds inte. Media Services stöder smidiga (Live), MP4 (VoD) och progressivt nedladdnings format.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|Det begärda utdataformatet stöds inte. Media Services stöder formaten utjämna, streck (CSF, CMAF), HLS (v3, v4, CMAF) och progressiv nedladdning.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|En krypterings typ som inte stöds påträffades.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Den begärda medie typen stöds inte av utdataformatet. De typer som stöds är video, ljud eller "SUBT"-under text.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|Käll till gångs mediet kodades med ett medie format som inte är kompatibelt med utdataformatet.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Käll till gången kodades med ett video format som inte är kompatibelt med utdataformatet. H. 264, AVC, H. 265 (HEVC, hev1 eller hvc1) stöds.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Käll till gången kodades med ett ljud format som inte är kompatibelt med utdataformatet. Ljud formaten som stöds är AAC, E-AC3 (DD +), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Det går inte att konvertera källans skyddade till gång till utdataformatet.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|Skydds formatet stöds inte av utdataformatet.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|Skydds formatet stöds inte av indata-formatet.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ogiltig video-NAL-enhet, till exempel kan endast det första NAL i exemplet vara en AUD.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ogiltig storlek på NAL-enhet.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ogiltigt längd värde för NAL-enhet.|
|MPE_FILTER_INVALID|0x80890236|Ogiltiga dynamiska manifest filter.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Filter versioner som är ogiltiga eller inte stöds.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ogiltig filter typ.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Ogiltigt intervall har angetts av filtret.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Ogiltigt Track-attribut har angetts av filtret.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|Ogiltig längd på presentations fönster har angetts av filtret.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ogiltig Live-avstängning har angetts av filtret.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Endast ett absTimeInHNS-element stöds i äldre filter.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Det finns inga fler strömmar när filtren har tillämpats.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|Live-inaktive ring är utanför DVR-fönstret.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|Live-inaktive ring är större än presentations fönstret.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Överskridit tio (10) högsta tillåtna standard filter.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Det går inte att använda flera första video kvalitets operatorer i kombinerade filter för begäran.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Antalet attribut för första bit hastighet måste vara ett (1).|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS segmentets varaktighet måste vara mindre än en tredjedel av DVR-fönstret och HLS inaktive ras.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|Fragmentets varaktigheter måste vara mindre än eller lika med cirka 20 sekunder, eller så är ingångs kvalitets nivåerna inte tidsjusterade.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-Specific-fel, det går inte att hitta återbetjänings rutan när den ska finnas i DTSSpecficBox under parsning av DTS-rutor.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-Specific-fel, inga kanaler hittades i DTSSpecficBox under parsning av DTS-rutor.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-Specific-fel, felaktig matchning av exempel typ i DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-Specific-fel, flera till gångar har angetts men fel matchnings typ för DTSH.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-Specific-fel, kärn ström storleken är ogiltig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-Specific-fel, exempel matchning är ogiltig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-Specific-fel, under data tilläggs index är ogiltigt.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-Specific-fel, under Ströms block nummer är ogiltigt.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-Specific-fel, samplings frekvensen är ogiltig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-Specific-fel, referensens klock kod i under Ströms tillägget är ogiltig.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-Specific-fel, antalet önskade omslags inställningar för högtalare är ogiltigt.|

För krypterings artiklar och exempel, se:

- [Koncept: innehålls skydd](drm-content-protection-concept.md)
- [Koncept: innehålls nyckel principer](drm-content-key-policy-concept.md)
- [Koncept: strömmande principer](stream-streaming-policy-concept.md)
- [Exempel: skydda med AES-kryptering](drm-playready-license-template-concept.md)
- [Exempel: skydda med DRM](drm-protect-with-drm-tutorial.md)

Filter vägledning finns i:

- [Koncept: dynamiska manifest](filters-dynamic-manifest-concept.md)
- [Koncept: filter](filters-concept.md)
- [Exempel: skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Exempel: skapa filter med .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Exempel: skapa filter med CLI](filters-dynamic-manifest-cli-how-to.md)

För direktsända artiklar och exempel, se:

- [Koncept: översikt över direkt uppspelning](stream-live-streaming-concept.md)
- [Koncept: Live-händelser och Live-utdata](live-event-outputs-concept.md)
- [Exempel: självstudie för Live Streaming](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Ogiltigt intervall

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Lagrings åtgärds fel, returnerat http 416-fel, ogiltigt intervall.|

## <a name="500-internal-server-error"></a>500 internt serverfel

Under bearbetningen av begäran upptäcker Media Services fel som förhindrar bearbetningen från att fortsätta.  

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Mottaget och översatt från WinHTTP-felkoden ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Mottaget och översatt från WinHTTP-felkoden ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Mottaget och översatt från WinHTTP-felkoden ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Lagrings åtgärds fel, allmänt InternalError av ett HTTP 500-fel.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Lagrings åtgärds fel, allmänt OperationTimedOut av ett HTTP 500-fel.|
|MPE_STORAGE_FAILURE|0x808900F2|Lagrings åtgärds fel, andra HTTP 500-fel än InternalError eller OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig

Servern kan för närvarande inte ta emot begär Anden. Det här felet kan orsakas av för många förfrågningar till tjänsten. Begränsningsfunktionen i Media Services begränsar resursanvändningen för program som skickar för många förfrågningar till tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken till 503-felet. Det här felet medför inte alltid begränsning.
> 

|Felkod|HEXADECIMALT värde |Felbeskrivning|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Lagrings åtgärds fel, mottagen HTTP-server upptagen fel 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se även

- [Felkoder vid kodning](/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services begrepp](concepts-overview.md)
- [Kvoter och begränsningar](limits-quotas-constraints-reference.md)

## <a name="next-steps"></a>Nästa steg

[Exempel: åtkomst till ErrorCode och meddelande från ApiException med .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
