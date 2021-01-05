---
title: Slut punkter för direkt uppspelning (ursprung)
titleSuffix: Azure Media Services
description: Lär dig mer om strömnings slut punkter (ursprung), en dynamisk paketerings-och strömnings tjänst som levererar innehåll direkt till en app i klient programmet eller till en Content Delivery Network (CDN).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: inhenkel
ms.openlocfilehash: f7307b95912a982bb36efbf2ce18668bd88e1195
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882882"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Slut punkter för direkt uppspelning (ursprung) i Azure Media Services

I Microsoft Azure Media Services representerar en [strömmande slut punkt](/rest/api/media/streamingendpoints) en dynamisk (just-in-Time)-paketering och ursprungs tjänst som kan leverera Live och innehåll på begäran direkt till en app i klient spelaren med ett av de vanliga protokollen för strömnings mediet (HLS eller tank streck). Dessutom tillhandahåller **direkt uppspelnings slut punkten** dynamisk kryptering (just-in-Time) för att branschledande DRM: er. 

När du skapar ett Media Services konto skapas en **standard** slut punkt för direkt uppspelning när du har stoppat tillstånd. Fler slut punkter för direkt uppspelning kan skapas under kontot (se [kvoter och gränser](limits-quotas-constraints.md)).

> [!NOTE]
> Om du vill starta strömmande videor måste du starta den **strömnings slut punkt** från vilken du vill strömma videon.
>
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

Se till att du också läser avsnittet [dynamisk förpackning](dynamic-packaging-overview.md) . 

## <a name="naming-convention"></a>Namngivnings konvention

Värd namn formatet för strömnings-URL: en är: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , där `servicename` = namn på direkt uppspelnings slut punkt eller Live-händelseloggen.

När du använder standard slut punkten för direkt uppspelning `servicename` utelämnas så att URL: en är: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Begränsningar

* Slut punktens namn för direkt uppspelning har ett max värde på 24 tecken.
* Namnet ska följa det här [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -mönstret: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Typer

Det finns två typer av **direkt uppspelnings slut punkter** : **standard** (för hands version) och **Premium**. Typen definieras av antalet skalnings enheter ( `scaleUnits` ) som du allokerar för slut punkten för direkt uppspelning.

Den maximala gränsen för strömnings enheter är vanligt vis 10. Kontakta oss [här](https://azure.microsoft.com/support/create-ticket/) för att öka gränsen för ditt konto.

I tabellen beskrivs typerna:

|Typ|Skalningsenheter|Description|
|--------|--------|--------|  
|**Standard**|0|Standard slut punkten för direkt uppspelning är en **standard** typ – den kan ändras till Premium-typen genom att justera `scaleUnits` .|
|**Premium**|> 0|**Premium** Slut punkter för direkt uppspelning passar för avancerade arbets belastningar och tillhandahåller dedikerad och skalbar bandbredds kapacitet. Du flyttar till en **Premium** typ genom att justera `scaleUnits` (enheter för strömning). `scaleUnits` ge dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. När du använder **Premium** -typen ger varje aktive rad enhet ytterligare bandbredds kapacitet till appen. |

> [!NOTE]
> För kunder som vill leverera innehåll till stora Internet-åhörare rekommenderar vi att du aktiverar CDN på slut punkten för direkt uppspelning.

Information om SLA finns i [prissättning och service avtal](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Jämföra strömnings typer

Funktion|Standard|Premium
---|---|---
Dataflöde |Upp till 600 Mbit/s och kan ge ett mycket mer effektivt data flöde när ett CDN används.|200 Mbit/s per strömnings enhet (SU). Kan ge ett mycket högre effektivt data flöde när ett CDN används.
CDN|Azure CDN, CDN för tredje part eller ingen CDN.|Azure CDN, CDN för tredje part eller ingen CDN.
Faktureringen beräknas proportionellt| Varje dag|Varje dag
Dynamisk kryptering|Ja|Ja
Dynamisk paketering|Ja|Ja
Skala|Skalar upp till det riktade data flödet automatiskt.|Ytterligare SUs
IP-filtrering/G20/anpassad värd <sup>1</sup>|Ja|Ja
Progressiv nedladdning|Ja|Ja
Rekommenderad användning |Rekommenderas för de flesta strömnings scenarier.|Professional-användning.

<sup>1</sup> används endast direkt på slut punkten för direkt uppspelning när CDN inte är aktiverat på slut punkten.<br/>

## <a name="streaming-endpoint-properties"></a>Egenskaper för strömnings slut punkt

Det här avsnittet innehåller information om några av egenskaperna för strömnings slut punkten. Exempel på hur du skapar en ny slut punkt för direkt uppspelning och beskrivningar av alla egenskaper finns i [direkt uppspelnings slut punkt](/rest/api/media/streamingendpoints/create).

- `accessControl`: Används för att konfigurera följande säkerhets inställningar för den här strömnings slut punkten: Akamai för signatur huvud och IP-adresser som tillåts att ansluta till den här slut punkten. Den här egenskapen kan bara anges när `cdnEnabled` har angetts till false.

- `cdnEnabled`: Anger om Azure CDN integrering för den här slut punkten för direkt uppspelning är aktive rad (inaktive rad som standard). Om du anger `cdnEnabled` till sant inaktive ras följande konfigurationer: `customHostNames` och `accessControl` .

    Alla data Center stöder inte Azure CDN-integrering. Gör så här för att kontrol lera om data centret har Azure CDN-integrering tillgänglig:

  - Försök att ange värdet `cdnEnabled` sant.
  - Kontrol lera det returnerade resultatet för en `HTTP Error Code 412` (PreconditionFailed) med ett meddelande om att det inte går att ange egenskapen CdnEnabled för strömmande slut punkt till True eftersom CDN-funktionen inte är tillgänglig i den aktuella regionen.

    Om du får det här felet stöder inte data centret det. Prova ett annat data Center.

- `cdnProfile`: Om `cdnEnabled` är inställt på Sant kan du också skicka `cdnProfile` värden. `cdnProfile` är namnet på CDN-profilen där CDN-slutpunkten kommer att skapas. Du kan ange en befintlig cdnProfile eller använda en ny. Om värdet är NULL och `cdnEnabled` Sant används standardvärdet "AzureMediaStreamingPlatformCdnProfile". Om den angivna `cdnProfile` redan finns skapas en slut punkt under den. Om profilen inte finns skapas en ny profil automatiskt.
- `cdnProvider`: När CDN har Aktiver ATS kan du också skicka `cdnProvider` värden. `cdnProvider` styr vilken provider som ska användas. För närvarande stöds tre värden: "StandardVerizon", "PremiumVerizon" och "StandardAkamai". Om inget värde anges och `cdnEnabled` är sant används "StandardVerizon" (det är standardvärdet).
- `crossSiteAccessPolicies`: Används för att ange åtkomst principer mellan platser för olika klienter. Mer information finns i [Specifikation över domän princip fil](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) och [göra en tjänst tillgänglig över domän gränser](/previous-versions/azure/azure-services/gg185950(v=azure.100)). Inställningarna gäller endast för Smooth Streaming.
- `customHostNames`: Används för att konfigurera en slut punkt för direkt uppspelning för att acceptera trafik som dirigerats till ett anpassat värdnamn. Den här egenskapen är giltig för slut punkter för standard-och Premium-direktuppspelning och kan ställas in när `cdnEnabled` : false.

    Ägande rätten till domän namnet måste bekräftas av Media Services. Media Services verifierar domän namnets ägarskap genom att kräva en `CName` post som innehåller Media Services-konto-ID som en komponent som ska läggas till i domänen som används. Som exempel, för att "sports.contoso.com" ska användas som ett anpassat värdnamn för strömnings slut punkten måste en post för vara `<accountId>.contoso.com` konfigurerad för att peka på ett av Media Services verifierings värd namn. Verifierings värd namnet består av verifydns. \<mediaservices-dns-zone> .

    Följande är de DNS-zoner som förväntas användas i verifiera-posten för olika Azure-regioner.
  
  - Nordamerika, Europa, Singapore, Hong Kong SAR, Japan:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Porslin

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Till exempel `CName` bevisar en post som mappar "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" till "verifydns.Media.Azure.net" att Media Services-ID: t 945a4c4e-28ea-45cd-8ccb-a519f6b700ad har ägandet av contoso.com-domänen, vilket innebär att alla namn under contoso.com ska användas som ett anpassat värdnamn för en strömnings slut punkt under det kontot. Om du vill hitta medie tjänstens ID-värde går du till [Azure Portal](https://portal.azure.com/) och väljer ditt medie tjänst konto. **Konto-ID** visas längst upp till höger på sidan.

    Om det finns ett försök att ange ett anpassat värdnamn utan en korrekt verifiering av `CName` posten, kommer DNS-svaret att Miss lyckas och cachelagras under en viss tid. När en riktig post är på plats kan det ta en stund tills det cachelagrade svaret har verifierats igen. Beroende på DNS-providern för den anpassade domänen tar det var som helst från några minuter till en timme för att omverifiera posten.

    Förutom det `CName` som mappar `<accountId>.<parent domain>` till `verifydns.<mediaservices-dns-zone>` måste du skapa en annan `CName` som mappar det anpassade värd namnet (till exempel `sports.contoso.com` ) till din Media Services direkt slut punkts värdnamn (till exempel `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Slut punkter för direkt uppspelning som finns i samma data Center kan inte dela samma anpassade värd namn.

    Media Services stöder för närvarande inte TLS med anpassade domäner.

- `maxCacheAge` – Åsidosätter standardvärdet för max-ålder i HTTP-cachen som anges av slut punkten för direkt uppspelning av medie fragment och manifest på begäran. Värdet anges i sekunder.
- `resourceState` -

    - Stoppades: initialt tillstånd för en strömmande slut punkt efter skapandet
    - Startar: övergår till körnings tillstånd
    - Körs: kan strömma innehåll till klienter
    - Skalning: skalnings enheterna ökas eller minskas
    - Stoppar: övergår till stoppat tillstånd
    - Tar bort: tas bort

- `scaleUnits`: Ger dig dedikerad utgående kapacitet som kan köpas i steg om 200 Mbit/s. Om du behöver flytta till en **Premium** -typ justerar du `scaleUnits` .

## <a name="why-use-multiple-streaming-endpoints"></a>Varför ska jag använda flera slut punkter för direkt uppspelning?

En enda slut punkt för direkt uppspelning kan strömma både direktsända videor och på begäran och de flesta kunder använder bara en slut punkt för direkt uppspelning. Det här avsnittet innehåller några exempel på varför du kan behöva använda flera slut punkter för direkt uppspelning.

* Varje reserverad enhet tillåter 200 Mbps bandbredd. Om du behöver mer än 2 000 Mbit/s (2 Gbit/s) bandbredd kan du använda den andra strömnings slut punkten och belastnings utjämning för att ge dig ytterligare bandbredd.

    CDN är dock det bästa sättet att skala ut för strömning av innehåll, men om du levererar så mycket innehåll som CDN hämtar mer än 2 Gbit/s kan du lägga till ytterligare slut punkter för direkt uppspelning (ursprung). I det här fallet skulle du behöva lämna ut innehålls-URL: er som är balanserade över de två slut punkterna för direkt uppspelning. Den här metoden ger bättre cachelagring än att försöka skicka begär anden till varje ursprung slumpmässigt (till exempel via en Traffic Manager). 
    
    > [!TIP]
    > Vanligt vis om CDN hämtar fler än 2 Gbit/s kan något vara felaktigt konfigurerat (till exempel ingen ursprungs avskärmning).
    
* Belastnings utjämning av olika CDN-providrar. Du kan till exempel ställa in standard slut punkten för direkt uppspelning för att använda Verizon CDN och skapa en andra för att använda Akamai. Lägg sedan till en belastnings utjämning mellan de två för att uppnå multi-CDN-balansering. 

    Men kunden använder ofta belastnings utjämning över flera CDN-providrar med ett enda ursprung.
* Strömma blandat innehåll: Live och video på begäran. 

    Åtkomst mönster för Live-och på-begäran-innehåll är väldigt olika. Live-innehållet tenderar att få mycket behov av samma innehåll på samma gång. Video innehåll på begäran (långt slut på Arkiv innehåll för instans) har låg användning av samma innehåll. Därför fungerar cachelagring mycket bra på Live-innehållet men inte även på det långa innehållet.

    Överväg ett scenario där dina kunder huvudsakligen tittar på Live-innehåll, men som bara kan titta på innehåll på begäran och som hanteras från samma slut punkt för direkt uppspelning. Den låga användningen av innehåll på begäran skulle uppta cacheminnet som skulle vara bättre sparat för Live-innehållet. I det här scenariot rekommenderar vi att du betjänar Live-innehållet från en slut punkt för direkt uppspelning och det långa innehållet från en annan slut punkt för strömning. Detta förbättrar prestandan för live event-innehållet.
    
## <a name="scaling-streaming-with-cdn"></a>Skala direktuppspelning med CDN

Se följande artiklar:

- [Översikt över CDN](../../cdn/cdn-overview.md)
- [Skala direktuppspelning med CDN](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Ställ frågor och få uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se även

[Dynamisk paketering](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Nästa steg

[Hantera slutpunkter för direktuppspelning](manage-streaming-endpoints-howto.md)
