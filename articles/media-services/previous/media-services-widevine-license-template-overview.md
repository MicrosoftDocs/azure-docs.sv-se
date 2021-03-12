---
title: Översikt över Widevine-licens mal len | Microsoft Docs
description: Det här avsnittet ger en översikt över en Widevine-licens mal len som används för att konfigurera Widevine-licenser.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4a3805e50ce48d1439696a3d9015d6ba8571470
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011195"
---
# <a name="widevine-license-template-overview"></a>Översikt över Widevine-licensmallen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Du kan använda Azure Media Services för att konfigurera och begära Google Widevine-licenser. När spelaren försöker spela upp det Widevine innehållet skickas en begäran till licens leverans tjänsten för att få en licens. Om licens tjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

En begäran om Widevine-licens formateras som ett JSON-meddelande.  

>[!NOTE]
> Du kan skapa ett tomt meddelande utan värden, bara " {} ." Sedan skapas en licens mal len med standardinställningar. Standard fungerar i de flesta fall. Microsoft-baserade licens leverans scenarier bör alltid använda standardvärdena. Om du behöver ställa in värdena "Provider" och "content_id" måste en provider matcha Widevine-autentiseringsuppgifter.

```json
{  
   "payload": "<license challenge>",
   "content_id": "<content id>" 
   "provider": "<provider>"
   "allowed_track_types": "<types>",
   "content_key_specs": [  
      {  
         "track_type": "<track type 1>"
      },
      {  
         "track_type": "<track type 2>"
      },
      …
   ],
   "policy_overrides": {  
      "can_play": <can play>,
      "can persist": <can persist>,
      "can_renew": <can renew>,
      "rental_duration_seconds": <rental duration>,
      "playback_duration_seconds": <playback duration>,
      "license_duration_seconds": <license duration>,
      "renewal_recovery_duration_seconds": <renewal recovery duration>,
      "renewal_server_url": "<renewal server url>",
      "renewal_delay_seconds": <renewal delay>,
      "renewal_retry_interval_seconds": <renewal retry interval>,
      "renew_with_usage": <renew with usage>
   }
}
```

## <a name="json-message"></a>JSON-meddelande
| Name | Värde | Beskrivning |
| --- | --- | --- |
| payload |Base64-kodad sträng |Den licens förfrågan som skickas av en klient. |
| content_id |Base64-kodad sträng |Identifierare som används för att härleda nyckel-ID och innehålls nyckel för varje content_key_specs. track_type. |
| CSP |sträng |Används för att söka efter innehålls nycklar och principer. Om Microsoft Key Delivery används för Widevine License Delivery ignoreras den här parametern. |
| policy_name |sträng |Namnet på en tidigare registrerad princip. Valfritt. |
| allowed_track_types |räkning |SD_ONLY eller SD_HD. Styr vilka innehålls nycklar som ingår i en licens. |
| content_key_specs |Matrisen med JSON-strukturer finns i avsnittet "innehålls nyckel-specifikationer".  |En detaljerad kontroll som visar vilka innehålls nycklar som ska returneras. Mer information finns i avsnittet "innehålls nyckel specifikationer". Det går bara att ange ett av värdena allowed_track_types och content_key_specs. |
| use_policy_overrides_exclusively |Booleskt värde, sant eller falskt |Använd de principmallar som anges av policy_overrides och utelämna alla tidigare lagrade principer. |
| policy_overrides |JSON-strukturen finns i avsnittet "princip åsidosättningar". |Princip inställningar för den här licensen.  I händelse av att den här till gången har en fördefinierad princip används de angivna värdena. |
| session_init |JSON-strukturen finns i avsnittet "session-initiering". |Valfria data överförs till licensen. |
| parse_only |Booleskt värde, sant eller falskt |Begäran om licens tolkas, men ingen licens utfärdas. Värden från licens förfrågan returneras dock i svaret. |

## <a name="content-key-specs"></a>Specifikationer för innehålls nyckel
Om det finns en befintlig princip behöver du inte ange något av värdena i innehålls nyckel specifikationen. Den befintliga principen som är associerad med det här innehållet används för att fastställa utmatnings skyddet, t. ex. digitala Content Protection med hög bandbredd (HDCP) och det kopierade hanterings systemet (CGMS). Om en befintlig princip inte är registrerad på Widevine-licensserver, kan innehålls leverantören mata in värdena i licens förfrågan.   

Varje content_key_specs värde måste anges för alla spår, oavsett alternativet use_policy_overrides_exclusively. 

| Name | Värde | Beskrivning |
| --- | --- | --- |
| content_key_specs. track_type |sträng |Ett namn på spår typ. Om content_key_specs anges i licens förfrågan, se till att ange alla spår typer explicit. Det gick inte att spela upp det senaste 10 sekunderna. |
| content_key_specs  <br/> security_level |UInt32 |Definierar stabilitets kraven för klienten för uppspelning. <br/> -Programvarubaserad kryptografisk kryptering krävs. <br/> – Program kryptering och en fördunklade-avkodare krävs. <br/> -Nyckel materialet och kryptografi åtgärderna måste utföras inom en maskinvarubaserad miljö med en maskin varu återställning. <br/> -Kryptering och avkodning av innehåll måste utföras inom en maskinvarubaserad miljö med en maskin varu återställning.  <br/> – Kryptering, avkodning och all hantering av mediet (komprimerade och okomprimerade) måste hanteras i en maskin varu hanterare som är en betrodd körnings miljö. |
| content_key_specs <br/> required_output_protection. HDC |sträng, en av HDCP_NONE HDCP_V1, HDCP_V2 |Anger om HDCP krävs. |
| content_key_specs <br/>key |Base64<br/>kodad sträng |Innehålls nyckel som ska användas för den här spårningen. Om det här alternativet anges måste track_type eller key_id. Innehålls leverantören kan använda det här alternativet för att mata in innehålls nyckeln för det här spåret i stället för att låta Widevine-licensservern generera eller slå upp en nyckel. |
| content_key_specs content_key_specs.key_id |Base64-kodad sträng binär, 16 byte |Unik identifierare för nyckeln. |

## <a name="policy-overrides"></a>Princip åsidosättningar
| Name | Värde | Beskrivning |
| --- | --- | --- |
| policy_overrides. can_play |Booleskt värde, sant eller falskt |Anger att uppspelning av innehållet är tillåten. Standardvärdet är false. |
| policy_overrides. can_persist |Booleskt värde, sant eller falskt |Anger att licensen kan vara bestående av Nonvolatile Storage för offline-användning. Standardvärdet är false. |
| policy_overrides. can_renew |Booleskt värde, sant eller falskt |Anger att förnyelse av den här licensen är tillåten. Om värdet är true kan licensens giltighets tid utökas med pulsslag. Standardvärdet är false. |
| policy_overrides. license_duration_seconds |Int64 |Anger tids perioden för den här speciella licensen. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. rental_duration_seconds |Int64 |Anger tidsfönstret då uppspelning tillåts. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. playback_duration_seconds |Int64 |Visnings fönstret av tiden efter uppspelningen startar inom licensens varaktighet. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides. renewal_server_url |sträng |Alla pulsslags begär Anden (förnyelse) för den här licensen dirigeras till den angivna URL: en. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renewal_delay_seconds |Int64 |Hur många sekunder efter license_start_time innan förnyelsen försöktes första gången. Det här fältet används endast om can_renew är sant. Standardvärdet är 0. |
| policy_overrides. renewal_retry_interval_seconds |Int64 |Anger fördröjningen i sekunder mellan efterföljande begär Anden om licens förnyelse, i händelse av ett haveri. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renewal_recovery_duration_seconds |Int64 |Tids period då uppspelningen kan fortsätta medan förnyelsen görs, men som inte lyckas på grund av backend-problem med licens servern. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Det här fältet används endast om can_renew är sant. |
| policy_overrides. renew_with_usage |Booleskt värde, sant eller falskt |Anger att licensen har skickats för förnyelse när användningen startar. Det här fältet används endast om can_renew är sant. |

## <a name="session-initialization"></a>Initiering av session
| Name | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Denna sessionstoken skickas tillbaka i licensen och finns i efterföljande förnyelser. Sessionstoken kvarstår utanför sessioner. |
| provider_client_token |Base64-kodad sträng |Klient-token att skicka tillbaka i licens svaret. Om licens förfrågan innehåller en klient-token ignoreras värdet. Klientens token kvarstår utanför licens-sessioner. |
| override_provider_client_token |Booleskt värde, sant eller falskt |Om värdet är false och licens förfrågan innehåller en klient-token använder du token från begäran även om en klient-token har angetts i den här strukturen. Om värdet är true, ska du alltid använda den token som anges i den här strukturen. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Konfigurera dina Widevine-licenser med hjälp av .NET-typer
Media Services tillhandahåller .NET-API: er som du kan använda för att konfigurera dina Widevine-licenser. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klasser som definieras i Media Services .NET SDK
Följande klasser är definitioner av dessa typer:

```dotnetcli
public class WidevineMessage
{
    public WidevineMessage();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public AllowedTrackTypes? allowed_track_types { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public ContentKeySpecs[] content_key_specs { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public object policy_overrides { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum AllowedTrackTypes
{
    SD_ONLY = 0,
    SD_HD = 1
}
public class ContentKeySpecs
{
    public ContentKeySpecs();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string key_id { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public RequiredOutputProtection required_output_protection { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public int? security_level { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string track_type { get; set; }
}

public class RequiredOutputProtection
{
    public RequiredOutputProtection();

    public Hdcp hdcp { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum Hdcp
{
    HDCP_NONE = 0,
    HDCP_V1 = 1,
    HDCP_V2 = 2
}
```

### <a name="example"></a>Exempel
I följande exempel visas hur du använder .NET-API: er för att konfigurera en enkel Widevine-licens:

```dotnetcli
private static string ConfigureWidevineLicenseTemplate()
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            can_renew = false
        }
    };

    string configuration = JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Använda PlayReady och/eller Widevine Dynamic Common Encryption](media-services-protect-with-playready-widevine.md)

