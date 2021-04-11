---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Obligatoriska fält. Läs mer på aka.ms/skyeye/meta.
title: strömma innehåll med CDN-integrering: Azure Media Services Beskrivning: Lär dig mer om strömnings innehåll med CDN-integrering samt för hämtning och Origin-Assist CDN-prefetch.
tjänster: Media-Services documentationcenter: ' ' author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. service: Media-Services MS. arbets belastning: MS. topic: konceptuell MS. Date: 08/31/2020 MS. author: inhenkel
---

# <a name="stream-content-with-cdn-integration"></a>Strömma innehåll med CDN-integrering

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med CDN får utvecklare en global lösning för snabb innehållsleverans med hög bandbredd till användarna eftersom innehållet cachelagras på fysiska noder på strategiska platser runt om i världen.  

CDN cachelagrar innehåll som strömmas från en Media Services [strömnings slut punkt (ursprung)](stream-streaming-endpoint-concept.md) per codec, per streaming-protokoll, per bit hastighet, per behållar format och per kryptering/DRM. För varje kombination av codec-strömmande protokoll – bit hastighet – kryptering, är det en separat CDN-cache.

Det populära innehållet kommer att betjänas direkt från CDN-cachen så länge video fragmentet cachelagras. Live-innehåll cachelagras förmodligen på grund av att många personer tittar på exakt samma sak. Innehåll på begäran kan vara en bit trickier eftersom du kan ha ett visst innehåll som är populärt och en del som inte är det. Om du har miljon tals video till gångar där ingen av dem är populär (endast en eller två användare per vecka), men du har tusentals människor som tittar på alla olika videor, blir CDN mycket mindre effektivt.

Du måste också fundera över hur anpassningsbar strömning fungerar. Varje enskilt video fragment cachelagras som sin egen entitet. Anta till exempel att en viss video är bevakad första gången. Om visnings programmet hoppar över att titta bara några sekunder här och där finns det bara videofragment som är associerade med vad den person som bevakar cachelagrar i CDN. Med anpassningsbar strömning har du normalt 5 till 7 olika bit hastigheter för video. Om en person tittar på en bit hastighet och en annan person tittar på en annan bit hastighet, är de båda cachelagrade separat i CDN. Även om två personer tittar på samma bit hastighet kan de strömmas över olika protokoll. Varje protokoll (HLS, MPEG-streck, Smooth Streaming) cachelagras separat. Varje bit hastighet och protokoll cachelagras separat och endast de video fragment som har begärts cachelagras.

Förutom test miljön rekommenderar vi att CDN aktive ras för både standard-och Premium-slutpunkter för direkt uppspelning. Varje typ av slut punkt för direkt uppspelning har en annan data flödes gräns som stöds.
Det är svårt att göra en exakt beräkning för maximalt antal samtidiga strömmar som stöds av en strömnings slut punkt eftersom det finns olika faktorer att ta med i beräkningen. Dessa omfattar:

- Högsta antal bit hastigheter som används för strömning
- Beteende för förbuffring och växling i spelaren. Spelarna försöker överföra segment från ett ursprung och använda belastnings hastigheten för att beräkna den anpassade bit hastighets växlingen. Om en strömmande slut punkt blir nära mättnad kan svars tiderna variera och spelarna börjar växla till lägre kvalitet. Eftersom detta minskar belastningen på de strömmande slut punkts spelarna kan du skala tillbaka till högre kvalitet och skapa oönskade växlings utlösare.
Generellt är det säkert att uppskatta maximalt antal samtidiga data strömmar genom att dra det maximala data flödet för strömnings slut punkt och dividera detta med den maximala bit hastigheten (förutsatt att alla spelare använder den högsta bit hastigheten.) Du kan till exempel ha en standard slut punkt för direkt uppspelning som är begränsad till 600 Mbit/s och den högsta bit hastigheten i 3Mbp. I det här fallet stöds cirka 200 samtidiga strömmar med den högsta bit hastigheten. Kom även ihåg att faktor i kraven för ljud bandbredd. Även om en ljud ström bara kan strömmas vid 128 KPS, ökar den totala direkt uppspelningen snabbt när du multiplicerar den med antalet samtidiga data strömmar.

I det här avsnittet beskrivs hur du aktiverar [CDN-integrering](#enable-azure-cdn-integration). Den förklarar också för hämtning (aktiv cachelagring) och [ursprunget-Assist CDN-prefetch](#origin-assist-cdn-prefetch) .

## <a name="considerations"></a>Överväganden

- [Slut punkten för direkt uppspelning](stream-streaming-endpoint-concept.md) `hostname` och STRÖMNINGS-URL: en är oförändrad oavsett om du aktiverar CDN eller inte.
- Om du behöver kunna testa ditt innehåll med eller utan CDN, skapar du en annan slut punkt för direkt uppspelning som inte är CDN-aktiverad.

## <a name="enable-azure-cdn-integration"></a>Aktivera Azure CDN-integrering

> [!IMPORTANT]
> Du kan inte aktivera CDN för utvärderings-eller student Azure-konton.
>
> CDN-integrering är aktiverat i alla Azure-datacenter utom federala myndigheter och regioner i Kina.

När en slut punkt för direkt uppspelning har tillhandahållits med CDN aktiverat har en definierad vänte tid på Media Services innan DNS-uppdateringen görs för att mappa slut punkten för direkt uppspelning till CDN-slutpunkten.

Om du senare vill inaktivera/aktivera CDN måste slut punkten för direkt uppspelning vara i **stoppat** tillstånd. När slut punkten för direkt uppspelning har startats kan det ta upp till fyra timmar innan Azure CDN-integrering aktive ras och för att ändringarna ska vara aktiva över alla CDN pop. Du kan dock starta slut punkten för direkt uppspelning och strömma utan avbrott från slut punkten för direkt uppspelning. När integrationen är klar levereras strömmen från CDN. Under etablerings perioden kommer slut punkten för direkt uppspelning att vara i **Start** läge och du kan observera försämrade prestanda.

När standard slut punkten för direkt uppspelning skapas, konfigureras den som standard med standard-Verizon. Du kan konfigurera Premium Verizon-eller standard Akamai-providers med hjälp av REST API: er.

Azure Media Services integration med Azure CDN implementeras på **Azure CDN från Verizon** för standard slut punkter för direkt uppspelning. Premium-slutpunkter för direkt uppspelning kan konfigureras med alla **Azure CDN pris nivåer och leverantörer**.

> [!NOTE]
> Mer information om Azure CDN finns i [CDN-översikten](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Avgöra om en DNS-ändring har gjorts

Du kan avgöra om DNS-ändringar har gjorts på en strömmande slut punkt (trafiken dirigeras till Azure CDN) med hjälp av <https://www.digwebinterface.com> . Om du ser azureedge.net domän namn i resultaten kommer trafiken nu att pekas mot CDN.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN-cachelagring är en reaktiv process. Om CDN kan förutsäga vad nästa objekt kommer att begäras, kan CDN proaktivt begära och cachelagra nästa objekt. Med den här processen kan du uppnå en cache-träff för alla (eller flest) objekt, vilket förbättrar prestandan.

Konceptet för för hämtning strävar efter att placera objekt på "kanten av Internet" i förväntat att de kommer att begäras av spelaren i fortsättningen, vilket minskar den tid det tar att leverera objektet till spelaren.

För att uppnå det här målet behöver en strömmande slut punkt (ursprung) och CDN arbeta på ett par olika sätt:

- Det Media Services ursprunget måste ha "Intelligence" (ursprungs assistenten) för att informera CDN-nästa objekt till för hämtning.
- CDN gör för hämtning och cachelagring (CDN-för hämtning). CDN måste också ha "Intelligence" för att informera ursprunget oavsett om det är en för hämtning eller en vanlig hämtning, hanterar 404-svar och ett sätt att undvika oändlig för hämtnings slinga.

### <a name="benefits"></a>Fördelar

Fördelarna med *Origine-funktionen CDN-prefetch* innehåller:

- För hämtning förbättrar video uppspelnings kvaliteten genom förväntad video segment vid kanten under uppspelningen, för att minska svars tiden till visnings programmet och förbättra nedladdnings tiderna för video segment. Detta resulterar i en snabbare start tid för videon och ger lägre ombuffring av förekomster.
- Det här konceptet gäller för allmänt CDN-ursprungs scenario och är inte begränsat till media.
- Akamai har lagt till den här funktionen i [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Den här funktionen gäller ännu inte för Akamai CDN som är integrerad med Media Services streaming-slutpunkten. Det är dock tillgängligt för Media Services kunder som har ett redan befintligt Akamai-kontrakt och kräver anpassad integrering mellan Akamai CDN och Media Services ursprung.

### <a name="how-it-works"></a>Så här fungerar det

CDN-stöd för `Origin-Assist CDN-Prefetch` sidhuvudena (för både Live och video på begäran) är tillgängligt för kunder som har direkt kontrakt med Akamai CDN. Funktionen omfattar följande HTTP-huvud utbyten mellan Akamai CDN och Media Services ursprung:

|HTTP-huvud|Värden|Avsändare|Mottagare|Syfte|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (standard) eller 0 |CDN|Ursprung|För att indikera att CDN är för hämtning aktiverat.|
|`CDN-Origin-Assist-Prefetch-Path`| Exempel: <br/>Fragment (video = 1400000000, format = mpd-Time-cmaf)|Ursprung|CDN|För att ange en för hämtnings Sök väg till CDN.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (för hämtnings förfrågan) eller 0 (vanlig begäran)|CDN|Ursprung|För att ange att begäran från CDN är en för hämtning.|

Du kan prova följande steg för att se en del av det att huvud utbytet i fungerar:

1. Använd Postman eller sväng för att skicka en begäran till Media Services ursprung för ett ljud-eller video segment eller fragment. Se till att lägga till rubriken `CDN-Origin-Assist-Prefetch-Enabled: 1` i begäran.
2. I svaret bör du se rubriken `CDN-Origin-Assist-Prefetch-Path` med en relativ sökväg som sitt värde.

### <a name="supported-streaming-protocols"></a>Strömmande protokoll som stöds

`Origin-Assist CDN-Prefetch`Funktionen stöder följande strömnings protokoll för direkt uppspelning på begäran:

* HLS v3
* HLS v4
* HLS CMAF
* STRECK (CSF)
* BINDESTRECK (CMAF)
* Smidig strömning

### <a name="faqs"></a>Vanliga frågor och svar

* Vad händer om en URL för för hämtnings Sök vägen är ogiltig så att CDN-förhämtningen får en 404?

    CDN kommer bara att cachelagra ett 404-svar i 10 sekunder (eller något annat konfigurerat värde).

* Anta att du har en video på begäran. Om CDN-prefetch är aktiverat, innebär den här funktionen att när en klient begär det första video segmentet startar för hämtning en loop för att förHämta alla efterföljande video segment till samma bit hastighet?

    Nej, CDN-prefetch görs bara efter en klient-initierad begäran/svar. CDN-prefetch utlöses aldrig av en för hämtning för att undvika en för hämtnings slinga.

* Är Origin-Assist CDN-Prefetch funktionen alltid aktive ras? Hur kan den aktive ras/inaktive ras?

    Den här funktionen är inaktive rad som standard. Kunderna behöver aktivera den via Akamai API.

* Vad skulle hända med Live streaming Origin-Assist om nästa segment eller fragment ännu inte är tillgängligt?

    I det här fallet kommer Media Services ursprung inte `CDN-Origin-Assist-Prefetch-Path` att tillhandahålla header och CDN-för hämtning sker inte.

* Hur `Origin-Assist CDN-Prefetch` fungerar det med dynamiska manifest filter?

    Den här funktionen fungerar oberoende av manifest filtret. När nästa fragment ligger utanför ett filter fönster kommer dess URL fortfarande att finnas genom att du tittar på det råa klient manifestet och sedan returneras som CDN-förhämtat svars huvud. CDN får URL: en för ett fragment som har filtrerats bort från streck/HLS/utjämna manifest. Dock kommer spelaren aldrig att göra en GET-begäran till CDN för att hämta det här fragmentet, eftersom denna fragment inte ingår i den streck-/HLS/utjämna-manifestet som innehas av spelaren (spelaren känner inte till att det finns fragment).

* Kan du hämta bindestreck/HLS-spelnings lista/utjämna manifestet?

    Nej, streck-MPD, HLS Master Playlist, HLS variant-spelnings lista eller smidig manifest-URL läggs inte till i för hämtnings huvudet.

* Är förhämtade URL: er relativa eller absoluta?

    Medan Akamai CDN tillåter båda, innehåller Media Services ursprung endast relativa URL: er för för hämtnings Sök vägen eftersom det inte finns någon uppenbar förmån att använda absoluta URL: er.

* Fungerar den här funktionen med DRM-skyddat innehåll?

    Ja, eftersom den här funktionen fungerar på HTTP-nivån avkodas eller tolkas inte eventuella segment/fragment. Det bryr sig inte om innehållet är krypterat eller inte.

* Fungerar den här funktionen med AD-infogning på Server sidan (SSAI)?
    
    Det fungerar för ursprungligt/huvud innehåll (det ursprungliga video innehållet före AD-infogning) eftersom SSAI inte ändrar tidsstämpeln för käll innehållet från Media Services ursprung. Om den här funktionen fungerar med AD-innehåll beror på om AD ursprung stöder Origin-Assist. Om t. ex. AD-innehåll också finns i Azure Media Services (samma eller separat ursprung) kommer AD-innehållet också att förhämtas.

* Fungerar den här funktionen med UHD/HEVC-innehåll?

    Ja.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* Se till att granska dokumentet för [strömnings slut punkt (Origin)](stream-streaming-endpoint-concept.md) .
* Exemplet [i den här databasen](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) visar hur du startar standard slut punkten för direkt uppspelning med .net.
