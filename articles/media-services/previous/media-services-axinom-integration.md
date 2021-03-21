---
title: Använda Axinom för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs
description: 'I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady-och Widevine-DRM: er. PlayReady-licensen kommer från Media Services PlayReady licens Server och Widevine-licensen levereras av Axinom-licensservern.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 532e982b02bb35074370db45fec8cc42a898c83e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015530"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd Axinom för att leverera Widevine-licenser till Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Översikt
Azure Media Services (AMS) har lagt till Google Widevine Dynamic Protection (se [Mingfeis blogg](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) för mer information). Dessutom har Azure Media Player (AMP) även lagt till stöd för Widevine (se [amp-dokument](https://amp.azure.net/libs/amp/latest/docs/) för mer information). Detta är en viktig funktion i strömmande streck innehåll som skyddas av CENC med multi-Native-DRM (PlayReady och Widevine) på moderna webbläsare som är utrustade med MSE och EME.

Från och med Media Services .NET SDK-versionen 3.5.2 kan du Media Services konfigurera licens mal len för Widevine och hämta Widevine-licenser. Du kan också använda följande AMS-partner för hjälp med att leverera Widevine-licenser: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) och [castLabs](https://castlabs.com/company/partners/azure/).

Den här artikeln beskriver hur du integrerar och testar Widevine licens server som hanteras av Axinom. Mer specifikt gäller följande:  

* Konfigurera dynamiska Common Encryption med multi-DRM (PlayReady och Widevine) med motsvarande licens hämtnings-URL: er;
* Skapa en JWT-token för att uppfylla licens Server kraven;
* Utveckla Azure Media Player app, som hanterar licens förvärv med JWT-token-autentisering;

Det fullständiga systemet och flödet av innehålls nyckel, nyckel-ID, nyckel-Seed, JTW-token och dess anspråk kan vara bäst beskrivet i följande diagram:

![BINDESTRECK och CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Information om hur du konfigurerar dynamisk skydd och viktiga leverans principer finns i Mingfeis blogg: [så här konfigurerar du Widevine-paketering med Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Du kan konfigurera dynamiskt CENC skydd med multi-DRM för streck strömning med följande båda:

1. PlayReady-skydd för Microsoft Edge och IE11, som kan ha en begränsning för token. Den begränsade token-principen måste åtföljas av en token som utfärdats av en Secure token service (STS), till exempel Azure Active Directory;
2. Widevine-skydd för Chrome, det kan kräva token-autentisering med token som utfärdats av en annan STS. 

Se avsnittet om [skapande av JWT-token](media-services-axinom-integration.md#jwt-token-generation) för varför Azure Active Directory inte kan användas som STS för Axinom-Widevine licens Server.

### <a name="considerations"></a>Överväganden
1. Du måste använda Axinom-8888000000000000000000000000000000000000 (Key frö) och det genererade eller valda nyckel-ID: t för att generera innehålls nyckeln för att konfigurera Key Delivery Service. Axinom licens Server utfärdar alla licenser som innehåller innehålls nycklar baserat på samma nyckel-utsäde, som är giltig för både testning och produktion.
2. URL: en för hämtning av Widevine-licens för testning: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . Både HTTP och HTTS är tillåtna.

## <a name="azure-media-player-preparation"></a>Azure Media Player förberedelser
AMP v-1.4.0 stöder uppspelning av AMS-innehåll som är dynamiskt paketerat med både PlayReady och Widevine DRM.
Om Widevine-licensservern inte kräver token-autentisering finns det ingenting ytterligare du behöver göra för att testa ett streck innehåll som skyddas av Widevine. Till exempel tillhandahåller AMP-teamet ett enkelt [exempel](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)där du kan se hur det fungerar i Microsoft Edge och IE11 med PlayReady och Chrome med Widevine.
Widevine-licensservern som tillhandahålls av Axinom kräver JWT-token-autentisering. JWT-token måste skickas med en licens förfrågan via ett HTTP-huvud "X-AxDRM-Message". För det här ändamålet måste du lägga till följande Java Script på webb sidan värd-AMP innan du anger källan:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

Resten av en AMP-kod är standard-AMP-API som i AMP-dokument [här](https://amp.azure.net/libs/amp/latest/docs/).

Java Script för att ställa in anpassat Authorization-huvud är fortfarande en kortsiktig metod innan den officiella långsiktiga metoden i AMP släpps.

## <a name="jwt-token-generation"></a>Generering av JWT-token
Axinom Widevine licens Server för testning kräver JWT-token-autentisering. Dessutom är en av anspråken i JWT-token av en komplex objekt typ i stället för primitiv data typ.

Azure AD kan tyvärr bara utfärda JWT-token med primitiva typer. På samma sätt kan .NET Framework-API (system. IdentityModel. tokens. SecurityTokenHandler och JwtPayload) bara ange komplexa objekt typer som anspråk. Anspråken är dock fortfarande serialiserade som sträng. Därför kan vi inte använda någon av de två för att generera JWT-token för Widevine-licens förfrågan.

John Sheehan ' s [JWT NuGet-paketet](https://www.nuget.org/packages/JWT) uppfyller behoven så vi ska använda det här NuGet-paketet.

Nedan visas koden för att generera en JWT-token med de krav som krävs av Axinom Widevine License Server för testning:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.IdentityModel.Tokens;
using System.IdentityModel.Protocols.WSTrust;
using System.Security.Claims;

namespace OpenIdConnectWeb.Utils
{
    public class JwtUtils
    {
        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
        {
            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

            var payload = new Dictionary<string, object>()
            {
                { "version", 1 },
                { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
            };

            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

            return token;
        }

        //convert hex string to byte[]
        public static byte[] ConvertHexStringToByteArray(string hexString)
        {
            if (hexString.Length % 2 != 0)
            {
                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
            }

            byte[] HexAsBytes = new byte[hexString.Length / 2];
            for (int index = 0; index < HexAsBytes.Length; index++)
            {
                string byteValue = hexString.Substring(index * 2, 2);
                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
            }

            return HexAsBytes;
        }

    }  

}  
```

Axinom Widevine licens Server

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Överväganden
1. Även om AMS PlayReady licens Delivery Service kräver "Bearer =" före en autentiseringstoken används den inte av Axinom Widevine licens Server.
2. Axinom-kommunikations nyckeln används som signerings nyckel. Nyckeln är en hex-sträng, men den måste behandlas som en serie byte som inte är en sträng vid kodning. Detta uppnås av metoden ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Hämtar nyckel-ID
Du kanske har märkt att i koden för att skapa en JWT-token krävs nyckel-ID. Eftersom JWT-token måste vara redo innan du läser in AMP Player måste du hämta nyckel-ID för att kunna generera JWT-token.

Självklart finns det flera sätt att komma till med nyckel-ID. En kan till exempel lagra nyckel-ID tillsammans med metadata för innehåll i en databas. Eller så kan du hämta nyckel-ID från STRECKat MPD-filen (medie presentations beskrivning). Koden nedan är för den senare.

```csharp
//get key_id from DASH MPD
public static string GetKeyID(string dashUrl)
{
    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
    {
        dashUrl += "(format=mpd-time-csf)";
    }

    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
    objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
    objXmlNamespaceManager.PushScope();

    XPathNodeIterator objXPathNodeIterator;
    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

    string key_id = string.Empty;
    if (objXPathNodeIterator.MoveNext())
    {
        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
    }

    return key_id;
}
```

## <a name="summary"></a>Sammanfattning

Med det senaste tillägget av Widevine-support i både Azure Media Services Content Protection och Azure Media Player kan vi implementera strömning av streck + multi-Native-DRM (PlayReady + Widevine) med både PlayReady Licensing Service i AMS och Widevine licens Server från Axinom för följande moderna webbläsare:

* Chrome
* Microsoft Edge på Windows 10
* IE 11 på Windows 8,1 och Windows 10
* Både Firefox (Desktop) och Safari på Mac (inte iOS) stöds också via Silverlight och samma URL med Azure Media Player

Följande parametrar krävs i den mini-lösning som utnyttjar Axinom Widevine License Server. Förutom nyckel-ID tillhandahålls resten av parametrarna av Axinom baserat på deras Widevine Server-installation.

| Parameter | Hur den används |
| --- | --- |
| ID för kommunikations nyckel |Måste inkluderas som värde för anspråket com_key_id i JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Kommunikations nyckel |Måste användas som signerings nyckel för JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Nyckel-Seed |Måste användas för att generera en innehålls nyckel med ett angivet innehålls nyckel-ID (se  [det här](media-services-axinom-integration.md#content-protection) avsnittet). |
| URL för hämtning av Widevine-licens |Måste användas för att konfigurera till gångs leverans princip för streck strömning (se  [det här](media-services-axinom-integration.md#content-protection) avsnittet). |
| Innehålls nyckel-ID |Måste ingå som en del av värdet för rättighets meddelande anspråk för JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Tack till
Vi vill bekräfta följande personer som bidragit till att skapa det här dokumentet: Kristjan Jõgi of Axinom, Mingfei Yan och Amit Rajput.

