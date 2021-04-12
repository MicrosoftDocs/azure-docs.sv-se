---
title: Översikt över Media Services v3 Widevine-licens mal len
description: Lär dig mer om Azure Media Services med licens mal len för Widevine och hur den används för att konfigurera Widevine-licenser.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ef45e804b593f36171907395c564c8c6058c286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453741"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Översikt över Media Services v3 med Widevine-licens mal len

Med Azure Media Services kan du kryptera ditt innehåll med **Google Widevine**. Media Services tillhandahåller också en tjänst för att leverera Widevine-licenser. Du kan konfigurera Widevine-licenser med hjälp av API: er för Azure Media Services. När en spelare försöker spela upp ditt Widevinet innehåll skickas en begäran till licens leverans tjänsten för att erhålla licensen. Om licens tjänsten godkänner begäran utfärdar tjänsten licensen. Den skickas till klienten och används för att dekryptera och spela upp det angivna innehållet.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

En begäran om Widevine-licens formateras som ett JSON-meddelande.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> Du kan skapa ett tomt meddelande utan värden, bara " {} ." Sedan skapas en licens mal len med standardinställningar. Standard fungerar i de flesta fall. Microsoft-baserade licens leverans scenarier bör alltid använda standardvärdena. Om du behöver ställa in värdena "Provider" och "content_id" måste en provider matcha Widevine-autentiseringsuppgifter.

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
Om det finns en befintlig princip behöver du inte ange något av värdena i innehålls nyckel specifikationen. Den befintliga principen som är associerad med det här innehållet används för att fastställa utmatnings skyddet, t. ex. digitala Content Protection med hög bandbredd (HDCP) och kopiering av General Management system (CGMS). Om en befintlig princip inte är registrerad med Widevine-licensserver, kan innehålls leverantören mata in värdena i licens förfrågan.   

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
| policy_overrides&#46;can_play |Booleskt värde, sant eller falskt |Anger att uppspelning av innehållet är tillåten. Standardvärdet är false. |
| policy_overrides&#46;can_persist |Booleskt värde, sant eller falskt |Anger att licensen kan vara bestående av Nonvolatile Storage för offline-användning. Standardvärdet är false. |
| policy_overrides&#46;can_renew |Booleskt värde, sant eller falskt |Anger att förnyelse av den här licensen är tillåten. Om värdet är true kan licensens giltighets tid utökas med pulsslag. Standardvärdet är false. |
| policy_overrides&#46;license_duration_seconds |Int64 |Anger tids perioden för den här speciella licensen. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;rental_duration_seconds |Int64 |Anger tidsfönstret då uppspelning tillåts. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;playback_duration_seconds |Int64 |Visnings fönstret av tiden efter uppspelningen startar inom licensens varaktighet. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Standardvärdet är 0. |
| policy_overrides&#46;renewal_server_url |sträng |Alla pulsslags begär Anden (förnyelse) för den här licensen dirigeras till den angivna URL: en. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renewal_delay_seconds |Int64 |Hur många sekunder efter license_start_time innan förnyelsen försöktes första gången. Det här fältet används endast om can_renew är sant. Standardvärdet är 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |Int64 |Anger fördröjningen i sekunder mellan efterföljande begär Anden om licens förnyelse, i händelse av ett haveri. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renewal_recovery_duration_seconds |Int64 |Tids period då uppspelningen kan fortsätta medan förnyelsen görs, men som inte lyckas på grund av backend-problem med licens servern. Värdet 0 anger att det inte finns någon gräns för varaktigheten. Det här fältet används endast om can_renew är sant. |
| policy_overrides&#46;renew_with_usage |Booleskt värde, sant eller falskt |Anger att licensen har skickats för förnyelse när användningen startar. Det här fältet används endast om can_renew är sant. |

## <a name="session-initialization"></a>Initiering av session
| Name | Värde | Beskrivning |
| --- | --- | --- |
| provider_session_token |Base64-kodad sträng |Denna sessionstoken skickas tillbaka i licensen och finns i efterföljande förnyelser. Sessionstoken kvarstår utanför sessioner. |
| provider_client_token |Base64-kodad sträng |Klient-token att skicka tillbaka i licens svaret. Om licens förfrågan innehåller en klient-token ignoreras värdet. Klientens token kvarstår utanför licens-sessioner. |
| override_provider_client_token |Booleskt värde, sant eller falskt |Om värdet är false och licens förfrågan innehåller en klient-token använder du token från begäran även om en klient-token har angetts i den här strukturen. Om värdet är true, ska du alltid använda den token som anges i den här strukturen. |

## <a name="configure-your-widevine-license-with-net"></a>Konfigurera din Widevine-licens med .NET 

Media Services tillhandahåller en klass som låter dig konfigurera en Widevine-licens. Om du vill konstruera licensen skickar du JSON till [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Om du vill konfigurera mallen kan du:

### <a name="directly-construct-a-json-string"></a>Konstruera en JSON-sträng direkt

Den här metoden kan vara fel känslig. Vi rekommenderar att du använder andra metoder, som beskrivs i [definiera nödvändiga klasser och seriellt till JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Definiera nödvändiga klasser och serialisera till JSON

#### <a name="define-classes"></a>Definiera klasser

I följande exempel visas ett exempel på definitioner av klasser som mappar till Widevine JSON-schemat. Du kan instansiera klasserna innan du serialiserar dem till JSON-sträng.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>Konfigurera licensen

Använd klasser som definierats i föregående avsnitt för att skapa JSON som används för att konfigurera [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate):

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

Ta en titt på hur du [skyddar med DRM](protect-with-drm.md)
