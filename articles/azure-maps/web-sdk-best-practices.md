---
title: Metod tips för Azure Maps Web SDK | Microsoft Azure Maps
description: Lär dig tips & trick för att optimera din användning av Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 3/22/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: ba351053c876c31d945ec7e4127a5caebd6a71ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878351"
---
# <a name="azure-maps-web-sdk-best-practices"></a>Metod tips för Azure Maps Web SDK

Det här dokumentet fokuserar på bästa praxis för Azure Maps Web SDK, men många av de bästa metoderna och Optimeringarna som beskrivs kan tillämpas på alla andra Azure Maps SDK: er.

Azure Maps Web SDK tillhandahåller en kraftfull arbets yta för att återge stora avstånds data uppsättningar på många olika sätt. I vissa fall finns det flera sätt att återge data på på samma sätt, men beroende på data uppsättningens storlek och önskade funktioner kan en metod fungera bättre än andra. Den här artikeln fokuserar på bästa praxis och tips och knep för att maximera prestanda och skapa en smidig användar upplevelse.

När du tittar på att förbättra kart systemets prestanda kan du i allmänhet se hur du kan minska antalet lager och källor och hur komplexa data uppsättningar och åter givnings format som används.

## <a name="security-basics"></a>Grundläggande säkerhets inställningar

Den enda viktigaste delen av ditt program är dess säkerhet. Om ditt program inte är säker kan en hackare förstöra alla program, oavsett hur väl användar upplevelsen är. Här följer några tips för att hålla ditt Azure Maps-program säkert. När du använder Azure måste du bekanta dig med de säkerhets verktyg som är tillgängliga för dig. I det här dokumentet finns en [Introduktion till Azure-säkerhet](https://docs.microsoft.com/azure/security/fundamentals/overview).

> [!IMPORTANT]
> Azure Maps tillhandahåller två metoder för autentisering.
> * Prenumerations nyckel baserad autentisering
> * Azure Active Directory autentisering använder Azure Active Directory i alla produktions program.
> En prenumerations nyckelbaserade autentisering är enkel och de flesta mappnings plattformar använder sig av en lätt metod för att mäta din användning av-plattformen för fakturering. Detta är dock inte en säker form av autentisering och bör endast användas lokalt när du utvecklar appar. Vissa plattformar ger möjlighet att begränsa vilka IP-adresser och/eller HTTP referent som är i begäran, men den här informationen kan lätt vara falsk. Om du använder prenumerations nycklar bör du se till att [rotera dem regelbundet](how-to-manage-authentication.md#manage-and-rotate-shared-keys).
> Azure Active Directory är en företags identitets tjänst som har ett stort urval av säkerhetsfunktioner och inställningar för alla typer av program scenarier. Microsoft rekommenderar att alla produktions program som använder Azure Maps använder Azure Active Directory för autentisering.
> Lär dig mer om att [hantera autentisering i Azure Maps](how-to-manage-authentication.md) i det här dokumentet.

### <a name="secure-your-private-data"></a>Skydda dina privata data

När data läggs till i Azure Maps interaktiva SDK: er, återges de lokalt på slutanvändarens enhet och skickas aldrig tillbaka till Internet av någon anledning.

Om ditt program läser in data som inte ska vara offentligt tillgängliga, se till att data lagras på en säker plats, att de används på ett säkert sätt och att själva programmet är låst och bara tillgängligt för dina önskade användare. Om något av dessa steg hoppas över kan en obehörig person få åtkomst till dessa data. Azure Active Directory kan hjälpa dig att låsa den här.

Läs den här självstudien om hur [du lägger till autentisering till din webbapp som körs på Azure App Service](https://docs.microsoft.com/azure/app-service/scenario-secure-app-authentication-app-service)

### <a name="use-the-latest-versions-of-azure-maps"></a>Använd de senaste versionerna av Azure Maps

Azure Maps SDK: er går igenom vanliga säkerhetstester tillsammans med eventuella externa beroende bibliotek som kan användas av SDK: erna. Eventuella kända säkerhets problem åtgärdas i rimlig tid och släpps till produktion. Om ditt program pekar på den senaste högre versionen av den värdbaserade versionen av Azure Maps Web SDK får den automatiskt alla lägre versions uppdateringar som innehåller säkerhets relaterade korrigeringar.

Om du själv är värd för Azure Maps webb-SDK via modulen NPM, måste du använda cirkumflex-symbolen (^) till i kombination med Azure Maps NPM-paketets versions nummer i `package.json` filen så att den alltid pekar mot den senaste del versionen.

```json
"dependencies": {
  "azure-maps-control": "^2.0.30"
}
```

## <a name="optimize-initial-map-load"></a>Optimera initial kart belastning

När en webb sida läses in börjar ett av de första saker som du vill göra att börja återge något så snart som möjligt, så att användaren inte får stjärn på en tom skärm.

### <a name="watch-the-maps-ready-event"></a>Titta på händelse för kartor klart

På samma sätt är det bra att läsa in data på den så fort som möjligt, så att användaren inte tittar på en tom karta när kartan läses in ofta. Eftersom kartan läser in resurser asynkront måste du vänta tills kartan är klar att interagera med innan du försöker återge egna data på den. Det finns två händelser som du kan vänta på, en `load` händelse och en `ready` händelse. Inläsnings händelsen utlöses efter att kartan har läst in den ursprungliga kart visningen och alla kart paneler har lästs in. Den färdiga händelsen utlöses när de minsta kart resurserna som behövs för att börja interagera med kartan. Den färdiga händelsen kan ofta aktive ras på halva tiden för inläsnings händelsen och gör det möjligt för dig att börja läsa in data till kartan tidigare.

### <a name="lazy-load-the-azure-maps-web-sdk"></a>Lazy load Azure Maps webb-SDK

Om kartan inte behövs direkt kan du läsa in Azure Maps Web SDK tills det behövs. Detta fördröjer inläsningen av JavaScript-och CSS-filerna som används av Azure Maps Web SDK tills det behövs. Ett vanligt scenario där detta inträffar är när kartan läses in på en flik eller en utfällbar panel som inte visas på sid inläsning.
Följande kod exempel visar hur du förskjuter inläsningen av Azure Maps Web SDK tills en knapp trycks ned.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lazy load kartan" src="https://codepen.io/azuremaps/embed/vYEeyOv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se den lazywrite-funktionen för <a href='https://codepen.io/azuremaps/pen/vYEeyOv'>att läsa in kartan</a> med Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-a-placeholder-for-the-map"></a>Lägg till en plats hållare för kartan

Om det tar en stund att läsa in kartan på grund av nätverks begränsningar eller andra prioriteter i ditt program, bör du överväga att lägga till en liten bakgrunds bild på kartan `div` som plats hållare för kartan. Då fylls annulleringen av kartan `div` medan den läses in.

### <a name="set-initial-map-style-and-camera-options-on-initialization"></a>Ange första kart stil och kamera alternativ vid initiering

Appar vill ofta läsa in kartan till en angiven plats eller ett särskilt format. Ibland väntar utvecklarna tills kartan har lästs in (eller väntar på `ready` händelsen) och använder sedan `setCemer` `setStyle` kartan eller. Det tar ofta längre tid att komma till önskad inledande Map-vy eftersom många resurser laddas som standard innan de resurser som krävs för den önskade kart visningen läses in. Ett bättre tillvägagångs sätt är att skicka in de önskade kart-och stil alternativen till kartan vid initiering.

## <a name="optimize-data-sources"></a>Optimera data källor

Webb-SDK: n har två data källor,

* **Mål-JSON-källa**: kallas för `DataSource` klassen, hanterar rå data i det lokala JSON-formatet lokalt. Lämpat för små till medel stora data mängder (fler hundra tusen tusen funktioner).
* **Vektor panels källa**: känd vid `VectorTileSource` klassen, läser in data som är formaterade som vektor paneler för den aktuella kartan, baserat på kartans placerings system. Perfekt för stora till enorma data uppsättningar (miljon tals eller miljarder funktioner).

### <a name="use-tile-based-solutions-for-large-datasets"></a>Använd panelbaserade lösningar för stora data uppsättningar

Om du arbetar med större data uppsättningar som innehåller miljon tals funktioner, är det rekommenderade sättet att uppnå optimal prestanda att exponera data med hjälp av en lösning på Server sidan, till exempel vektor-eller raster bild panels tjänst.  
Vektor paneler är optimerade för att bara läsa in de data som är i vyn med Geometries klippt till fokus ytan på panelen och generaliseras för att matcha kart bildens zoomnings nivå.

Med [Azure Maps Creator Platform](creator-indoor-maps.md) kan du hämta data i vektor panels format. Andra data format kan använda verktyg som [Tippecanoe](https://github.com/mapbox/tippecanoe) eller någon av [listan många resurser på den här sidan](https://github.com/mapbox/awesome-vector-tiles).

Du kan också skapa en anpassad tjänst som återger data uppsättningar som raster bild paneler på Server sidan och läsa in data med TileLayer-klassen i Map SDK. Detta ger enastående prestanda eftersom kartan bara behöver läsa in och hantera några dussin bilder högst. Det finns dock vissa begränsningar med att använda raster paneler eftersom rå data inte är tillgängliga lokalt. En sekundär tjänst krävs ofta för att få en typ av interaktions upplevelse, till exempel ta reda på vilken form en användare har klickat på. Dessutom är fil storleken för en raster panel ofta större än en komprimerad vektor panel som innehåller generaliserade Geometries-nivåer och zoom.

Lär dig mer om data källor i [skapa ett data käll](create-data-source-web-sdk.md) dokument.

### <a name="combine-multiple-datasets-into-a-single-vector-tile-source"></a>Kombinera flera data uppsättningar till en enda vektor panels källa

De mindre data källorna som kartan måste hantera, desto snabbare kan den bearbeta alla funktioner som ska visas. I synnerhet när det gäller att placera ut källor och kombinera två vektor panels källor, klipper ut antalet HTTP-förfrågningar för att hämta brickorna på hälften, och den totala mängden data skulle bli något mindre eftersom det bara finns ett fil huvud.

Att kombinera flera data uppsättningar i en enda vektor panels källa kan uppnås med hjälp av ett verktyg som [Tippecanoe](https://github.com/mapbox/tippecanoe). Data uppsättningar kan kombineras till en enda funktions samling eller delas upp i separata lager på den vektor panel som är känd som käll lager. När du ansluter en vektor panels källa till ett åter givnings lager anger du det käll skikt som innehåller de data som du vill återge med skiktet.

### <a name="reduce-the-number-of-canvas-refreshes-due-to-data-updates"></a>Minska antalet uppdateringar på arbets ytan på grund av data uppdateringar

Det finns flera sätt `DataSource` att lägga till eller uppdatera data i en klass. Nedan visas de olika metoderna och några överväganden för att säkerställa bästa prestanda.

* Funktionen data källor `add` kan användas för att lägga till en eller flera funktioner i en data källa. Varje gången den här funktionen anropas utlöses en uppdatering av en kart arbets yta. Om du lägger till många funktioner kan du kombinera dem i en matris eller funktions samling och skicka dem till den här funktionen en gång, i stället för att loopa över en data uppsättning och anropa funktionen för varje funktion.
* Funktionen data källor `setShapes` kan användas för att skriva över alla former i en data källa. Under huven kombinerar den data källorna `clear` och `add` fungerar tillsammans och gör en enkel kart arbets yta att uppdatera i stället för två, vilket är mycket snabbare. Se till att använda detta när du vill uppdatera alla data i en data källa.
* Funktionen data källor `importDataFromUrl` kan användas för att läsa in en INTERjson-fil via en URL till en data källa. När data har hämtats skickas de till funktionen data källor `add` . Om den lokala JSON-filen finns i en annan domän, se till att den andra domänen har stöd för kors domän förfrågningar (CORs). Om det inte går att kopiera data till en lokal fil på din domän eller skapa en proxy-tjänst där CORs är aktiverat. Om filen är stor kan du konvertera den till en vektor panels källa.
* Om funktioner är omslutna med `Shape` klassen, `addProperty` `setCoordinates` utlöser alla-och-funktionerna `setProperties` i en uppdatering i data källan och en mappnings arbets yta uppdateras. Alla funktioner som returneras av data källorna `getShapes` och `getShapeById` funktionerna radbryts automatiskt med- `Shape` klassen. Om du vill uppdatera flera former går det snabbare att konvertera dem till JSON med funktionen data källor `toJson` , redigera den och sedan skicka dessa data till funktionen data källor `setShapes` .

### <a name="avoid-calling-the-data-sources-clear-function-unnecessarily"></a>Undvik att anropa data källorna rensa funktionen i onödan

Om du anropar funktionen Clear i `DataSource` klassen så uppdateras en kart arbets yta. Om `clear` funktionen anropas flera gånger i rad, kan en fördröjning uppstå när kartan väntar på att varje uppdatering ska ske.

Ett vanligt scenario där det här ofta förekommer i program är när en app rensar data källan, hämtar nya data, raderar data källan igen och lägger till nya data i data källan. Beroende på den önskade användar upplevelsen är följande alternativ bättre.

* Ta bort data innan du hämtar nya data och skicka sedan de nya data till data källorna `add` eller `setShapes` funktionen. Om det här är den enda data uppsättningen på kartan kommer kartan att vara tom medan nya data hämtas.
* Ladda ned nya data och skicka dem sedan till funktionen data källor `setShapes` . Då ersätts alla data på kartan.

### <a name="remove-unused-features-and-properties"></a>Ta bort oanvända funktioner och egenskaper

Om din data uppsättning innehåller funktioner som inte ska användas i appen tar du bort dem. På samma sätt tar du bort alla egenskaper för funktioner som inte behövs. Detta har flera fördelar:

* Minskar mängden data som måste laddas ned.
* Minskar antalet funktioner som behöver upprepas genom när data återges.
* Kan ibland hjälpa till att förenkla eller ta bort data drivna uttryck och filter, vilket innebär mindre bearbetning som krävs vid åter givnings tiden.

När funktioner har många egenskaper eller innehåll är det mycket mer som gör att du kan begränsa vad som ska läggas till i data källan för att bara de som behövs för åter givning och för att få en separat metod eller tjänst för att hämta ytterligare egenskap eller innehåll när det behövs. Om du till exempel har en enkel karta som visar platser på en karta när du klickar på en bunt med detaljerat innehåll visas. Om du vill använda data driven format för att anpassa hur platserna återges på kartan läser du bara in de egenskaper som krävs för data källan. När du vill visa det detaljerade innehållet använder du ID: t för funktionen för att hämta ytterligare innehåll separat. Om innehållet lagras på Server sidan kan en tjänst användas för att hämta den asynkront, vilket minskar mängden data som behöver hämtas drastiskt när mappningen läses in initialt.

Dessutom kan minska antalet signifikanta siffror i koordinaterna för funktionerna också minska data storleken avsevärt. Det är inte ovanligt att koordinater innehåller 12 eller fler decimaler. sex decimaler har dock en noggrannhet på cirka 0,1 meter, vilket ofta är mer exakt än den plats som koordinaten representerar (sex decimaler rekommenderas när du arbetar med små plats data, t. ex. inomhus Bygg layouter). Om du har fler än sex decimaler kommer sannolikheten troligen inte att innebära någon skillnad i hur data återges och kräver bara att användaren hämtar mer data utan extra förmån.

Här är en lista över [användbara verktyg för att arbeta med data i interjson](https://github.com/tmcw/awesome-geojson).

### <a name="use-a-separate-data-source-for-rapidly-changing-data"></a>Använd en separat data källa för att snabbt ändra data

Ibland behöver det finnas behov av att snabbt uppdatera data på kartan, till exempel Visa live-uppdateringar av strömmande data eller animera funktioner. När en data källa uppdateras, kommer åter givnings motorn att loopa igenom och återge alla funktioner i data källan. Att separera statiska data från att snabbt ändra data till olika data källor kan avsevärt minska antalet funktioner som återges på nytt på varje uppdatering av data källan och förbättra den övergripande prestandan.

Om du använder vektor paneler med real tids data är ett enkelt sätt att stödja uppdateringar att använda `expires` svars huvudet. Som standard kommer alla vektor panels lager eller raster panels lager automatiskt att ladda upp paneler igen när `expires` datumet. Panelerna trafik flöde och incidenter i kartan använder den här funktionen för att säkerställa att data i real tid visas på kartan. Funktionen kan inaktive ras genom att ställa in `refreshExpiredTiles` alternativet Maps-tjänst till `false` .

### <a name="adjust-the-buffer-and-tolerance-options-in-geojson-data-sources"></a>Justera alternativen för buffert och tolerans i data källor för polyjson

`DataSource`Klassen konverterar obearbetade plats data till vektor paneler lokalt för åter givning. De här lokala vektor panelerna klipper ut rå data till gränserna för panel ytan med en buffert för att säkerställa smidig åter givning mellan paneler. Ju mindre `buffer` alternativet är, desto färre överlappande data lagras i de lokala vektor panelerna och ger bättre prestanda, men ju större ändring av åter givnings artefakter som inträffar. Försök att ändra det här alternativet för att få rätt blandning av prestanda med minimala åter givnings artefakter.

`DataSource`Klassen har också ett `tolerance` alternativ som används med algoritmen Douglas-Peucker förenkling när du minskar upplösningen för Geometries i åter givnings syfte. Om du ökar det här tolerans värdet minskas upplösningen på Geometries och prestanda förbättras. Ändra det här alternativet för att få rätt kombination av geometri upplösning och prestanda för din data uppsättning.

### <a name="set-the-max-zoom-option-of-geojson-data-sources"></a>Ställ in Max alternativet för zoomning av indata källor för indata

`DataSource`Klassen konverterar obearbetade plats data till vektor paneler lokalt för åter givning. Som standard gör den det tills zoomnings nivå 18, då den zoomas in närmare, så samplar data från de paneler som genereras för zoomnings nivå 18. Detta fungerar bra för de flesta data uppsättningar som måste ha hög upplösning vid zoomning på dessa nivåer. Men när du arbetar med data uppsättningar som är mer sannolika att visas när du zoomar ut mer, till exempel när du visar regions-polygoner, ställer du in `minZoom` alternativet för data källan till ett mindre värde, till exempel `12` minskar beräknings mängden för den lokala panelen och det minne som används av data källan och ökar prestandan.

### <a name="minimize-geojson-response"></a>Minimera interjson-svar

När du läser in data för en 2D-data från en server, antingen via en tjänst eller genom att läsa in en platt fil, måste du ha data minimerade för att ta bort onödiga blank steg som gör nedladdnings storleken större än vad som behövs.

### <a name="access-raw-geojson-using-a-url"></a>Få åtkomst till rå interjson med en URL

Det går att lagra inre JSON-objekt inuti Java Script, men det använder mycket minne som kopior av den kommer att lagras över variabeln som du skapade för det här objektet och data käll instansen, som hanterar den i en separat webb anställd. Exponera den inre JSON-appen till din app med hjälp av en URL i stället och data källan kommer att läsa in en enda kopia av data direkt i webb arbetaren för data källor.  

## <a name="optimize-rendering-layers"></a>Optimera åter givnings lager

Azure Maps innehåller flera olika lager för att återge data på en karta. Det finns många optimeringar som du kan dra nytta av för att skräddarsy dessa lager till ditt scenario och öka prestandan och den övergripande användar upplevelsen.

### <a name="create-layers-once-and-reuse-them"></a>Skapa lager en gång och återanvänd dem

Azure Maps Web SDK har valt att vara data driven. Data hamnar i data källor som sedan ansluts till åter givnings lager. Om du vill ändra data på kartan ska du uppdatera data i data källan eller ändra stil alternativen på ett lager. Detta är ofta mycket snabbare än att ta bort och återskapa lager när det sker en ändring.

### <a name="consider-bubble-layer-over-symbol-layer"></a>Beakta bubbeldiagram över symbol skikt

Bubble-lagret återger punkter som cirklar på kartan och kan enkelt ha sina RADIUS-och färg format med ett data drivet uttryck. Eftersom cirkeln är en enkel form för att WebGL ska kunna rita, kommer åter givnings motorn att kunna återge dessa mycket snabbare än ett symbol lager, som måste läsa in och återge en bild. Prestanda skillnaden för dessa två åter givnings lager märks vid åter givning av tusentals punkter.

### <a name="use-html-markers-and-popups-sparingly"></a>Använd HTML-märken och popup-fönster sparsamt

Till skillnad från de flesta lager i Azure Maps-webbkontroll som använder WebGL för rendering använder HTML-markörer och popup-fönster traditionella DOM-element för rendering. Därför är det mer HTML-märken och popup-fönster som har lagt till en sida, desto fler DOM-element. Prestanda kan försämras när du har lagt till några hundra HTML-markörer eller popup-fönster. För större data mängder bör du överväga att antingen klustra dina data eller använda ett symbol-eller bubbeldiagram. För popup-fönster är en vanlig strategi att skapa ett enda popup-fönster och återanvända den genom att uppdatera dess innehåll och position som visas i exemplet nedan:

<br/>

<iframe height='500' scrolling='no' title='Återanvända popup med flera PIN-bara' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se popup-fönstret för att <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>använda pennan med flera stift</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

Om du bara har några poäng som du kan återge på kartan, är det enkelt att använda HTML-märken. Dessutom kan HTML-märken enkelt göras vid behov.

### <a name="combine-layers"></a>Kombinera lager

Kartan kan återge hundratals lager, men det finns fler lager, desto mer tid tar det att rendera en scen. En strategi för att minska antalet lager är att kombinera lager som har liknande format eller kan formateras med hjälp av [data drivna format](data-driven-style-expressions-web-sdk.md).

Anta till exempel en data uppsättning där alla funktioner har en `isHealthy` egenskap som kan ha värdet `true` eller `false` . Om du skapar ett bubbeldiagram som återger olika färgade bubblor baserat på den här egenskapen, finns det flera sätt att göra detta på det sätt som anges nedan från minsta möjliga.

* Dela data i två data källor baserat på `isHealthy` värdet och fäst ett bubbel-lager med ett hårdkodat färg alternativ för varje data källa.
* Lägg till alla data i en enda data källa och skapa två bubbeldiagram med ett hårdkodat färg alternativ och ett filter baserat på `isHealthy` egenskapen. 
* Lägg till alla data i en enda data källa och skapa ett enda bubbeldiagram med ett `case` format uttryck för färg alternativet baserat på `isHealthy` egenskapen. Här är ett kod exempel som visar detta.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Get the 'isHealthy' property from the feature.
        ['get', 'isHealthy'],

        //If true, make the color 'green'. 
        'green',

        //If false, make the color red.
        'red'
    ]
});
```

### <a name="create-smooth-symbol-layer-animations"></a>Skapa mjuka symbol lager animeringar

Symbol lager har upptäckt att identifiering av kollision är aktiverat som standard. Denna identifiering av kollision syftar till att se till att inga symboler överlappar varandra. Ikonen och text alternativen för ett symbol lager har två alternativ,

* `allowOverlap` -Anger om symbolen ska visas om den kolliderar med andra symboler.
* `ignorePlacement` -Anger om de andra symbolerna får kollidera med symbolen.

Båda alternativen är inställda på `false` som standard. När du animerar en symbol körs identifieringen av kollision på varje bild ruta i animeringen, vilket kan sakta ned animeringen och göra den mer flytande. Om du vill utjämna animeringen anger du dessa alternativ till `true` .

Följande kod exempel är ett enkelt sätt att animera ett symbol lager.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Symbol skikts animering" src="https://codepen.io/azuremaps/embed/oNgGzRd?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Se rit stifts <a href='https://codepen.io/azuremaps/pen/oNgGzRd'>symbolen Layer animering</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="specify-zoom-level-range"></a>Ange intervall för zoomnings nivå

Om dina data uppfyller något av följande villkor, måste du ange den minsta och högsta zoomnings nivån för lagret så att åter givnings motorn kan hoppa över den när den ligger utanför zoomnings nivå intervallet.

* Om data kommer från en vektor panels källa är ofta källans lager för olika data typer tillgängliga via en rad zoomnings nivåer.
* Om du använder ett panel lager som inte har några paneler för alla zoomnings nivåer 0 till 24 och du vill att det endast ska renderas på de nivåer som har paneler, och inte fylla i paneler som saknas med paneler från andra zoomnings nivåer.
* Om du bara vill rendera ett lager vid vissa zoomnings nivåer.
Alla skikt har ett `minZoom` och `maxZoom` alternativ där lagret återges när du väljer mellan dessa zoomnings nivåer baserat på den här logiken ` maxZoom > zoom >= minZoom` .

**Exempel**

```javascript
//Only render this layer between zoom levels 1 and 9. 
var layer = new atlas.layer.BubbleLayer(dataSource, null, {
    minZoom: 1,
    maxZoom: 10
});
```

### <a name="specify-tile-layer-bounds-and-source-zoom-range"></a>Ange intervall för panel lager gränser och zoomning av källa

Som standard kommer panel lager att läsa in paneler över hela världen. Men om panel tjänsten bara har paneler för ett visst område försöker kartan läsa in paneler när den ligger utanför området. När detta inträffar kommer en begäran för varje panel att göras och väntar på ett svar som kan blockera andra begär Anden som görs av kartan och därmed sakta ned åter givningen av andra lager. Om du anger gränserna för ett panel lager kommer kartan bara begära paneler som finns inom den markerings rutan. Om panel lagret bara är tillgängligt mellan vissa zoomnings nivåer anger du den minsta och högsta zoominställningen av samma orsak.

**Exempel**

```javascript
var tileLayer = new atlas.layer.TileLayer({
    tileUrl: 'myTileServer/{z}/{y}/{x}.png',
    bounds: [-101.065, 14.01, -80.538, 35.176],
    minSourceZoom: 1,
    maxSourceZoom: 10
});
```

### <a name="use-a-blank-map-style-when-base-map-not-visible"></a>Använd en tom kart stil när bas kartan inte är synlig

Om ett lager översätts på kartan som helt behandlar bas kartan, bör du överväga att ställa in kart formatet på `blank` eller `blank_accessible` så att bas kartan inte återges. Ett vanligt scenario för att göra detta är när du överlappar en hel jordglob-panel i har inget ogenomskinligt eller transparent område ovanför bas kartan.

### <a name="smoothly-animate-image-or-tile-layers"></a>Animera bild-eller panel lager smidigt

Om du vill animera genom en serie med bild-eller panel lager på kartan. Det går ofta snabbare att skapa ett lager för varje bild eller panel och ändra opaciteten än att uppdatera källan för ett enskilt lager på varje animerad ram. Att dölja ett lager genom att ställa in ogenomskinligheten på noll och visa ett nytt lager genom att ställa in dess opacitet till ett värde som är större än noll är mycket snabbare än att uppdatera källan i lagret. Alternativt kan synligheten för lagren växlas, men se till att bildens tonings tid är noll, annars animeras lagret när det visas, vilket leder till en Flimm-effekt eftersom det föregående lagret skulle vara dolt innan det nya lagret visas.

### <a name="tweak-symbol-layer-collision-detection-logic"></a>Justera identifierings logiken för symbol lagrets kollision

Symbol lagret har två alternativ som finns för både ikon och text som kallas `allowOverlap` och `ignorePlacement` . De här två alternativen anger om ikonen eller texten för en symbol kan överlappa eller överlappa varandra. När dessa är inställt på `false` , kommer symbol lagret att utföra beräkningar när du återger varje punkt för att se om den kolliderar med andra redan återgivna symboler i lagret, och om det gör det återges inte den krockande symbolen. Detta är användbart när du minskar kartan och minskar antalet objekt som återges. Genom att ställa in dessa alternativ i `false` , hoppas den här logiken för upptäckt av kollision över och alla symboler återges på kartan. Ändra det här alternativet för att få den bästa kombinationen av prestanda och användar upplevelse.

### <a name="cluster-large-point-data-sets"></a>Data uppsättningar för stora kluster punkter

När du arbetar med stora uppsättningar data punkter kan det hända att många av punkterna överlappar och bara delvis är synliga, om de visas på vissa zoomnings nivåer. Klustring är en process för att gruppera punkter som är nära varandra och som representerar dem som en enda grupperad punkt. När användaren zoomar in kartan, delas kluster upp i sina enskilda punkter. Detta kan avsevärt minska mängden data som måste återges, se till att kartan är mindre rörig och förbättra prestandan. `DataSource`Klassen har alternativ för att klustra data lokalt. Dessutom har många verktyg som genererar vektor paneler också kluster alternativ.

Öka dessutom storleken på kluster-radien för att förbättra prestanda. Ju större kluster-RADIUS, desto mindre klustrade punkter kan du hålla koll på och återge.
Läs mer i [data dokumentet för kluster platsen](clustering-point-data-web-sdk.md)

### <a name="use-weighted-clustered-heat-maps"></a>Använd viktade, grupperade värme kartor

Värme kartans lager kan enkelt återge tusentals data punkter. För större data mängder bör du överväga att aktivera klustring på data källan och använda en liten kluster-radie och använda kluster `point_count` egenskapen som vikt för höjd kartan. Om kluster-radien bara är några få pixlar i storlek, finns det ingen liten visuell skillnad i den återgivna värme kartan. Om du använder en större kluster-RADIUS får du bättre prestanda, men det kan minska upplösningen för den återgivna värme kartan.

```javascript
var layer = new atlas.layer.HeatMapLayer(source, null, {
   weight: ['get', 'point_count']
});
```

Läs mer i [kluster-och värme kartor i det här dokumentet](clustering-point-data-web-sdk.md #clustering-and-the-heat-maps-layer)

### <a name="keep-image-resources-small"></a>Behåll bild resurser små

Bilder kan läggas till i Maps-bilden Sprite för att återge ikoner i ett symbol lager eller mönster i ett polygon-lager. Se till att de här bilderna är små för att minimera mängden data som måste laddas ned och mängden utrymme som de tar upp i Maps-bilden Sprite. När du använder ett symbol lager som skalar ikonen med `size` alternativet använder du en bild som är den maximala storlek som du planerar att visa på kartan och inte större. Detta säkerställer att ikonen återges med hög upplösning och minimerar de resurser som används. Dessutom kan SVG: t även användas som ett mindre fil format för enkla ikon bilder.

## <a name="optimize-expressions"></a>Optimera uttryck

[Data drivna format uttryck](data-driven-style-expressions-web-sdk.md) ger stor flexibilitet och möjlighet att filtrera och formatera data på kartan. Det finns många sätt i vilka uttryck kan optimeras. Här följer några tips.

### <a name="reduce-the-complexity-of-filters"></a>Minska komplexiteten för filter

Filtrerar en slinga över alla data i en data källa och kontrollerar om varje filter matchar logiken i filtret. Om filtren blir komplexa kan detta orsaka prestanda problem. Några möjliga strategier för att åtgärda detta är följande.

* Om du använder vektor paneler kan du dela upp data i olika käll lager.
* Om du använder `DataSource` klassen, kan du dela upp data i separata data källor. Försök att balansera antalet data källor med filtrets komplexitet. För många data källor kan orsaka prestanda problem, så du kan behöva göra vissa tester för att ta reda på vad som passar bäst för ditt scenario.
* När du använder ett komplext filter på ett lager bör du överväga att använda flera lager med stil uttryck för att minska filtrets komplexitet. Undvik att skapa en massa lager med hårdkodad-format när format uttryck kan användas som ett stort antal lager, kan även orsaka prestanda problem.

### <a name="make-sure-expressions-dont-produce-errors"></a>Kontrol lera att uttryck inte genererar fel

Uttryck används ofta för att generera kod för att utföra beräkningar eller logiska åtgärder vid åter givnings tid. Precis som koden i resten av ditt program måste du vara säker på att beräkningarna och det logiska och de är inte fel känsliga. Fel i uttryck kan orsaka problem i utvärderingen av uttrycket, vilket kan leda till minskad prestanda och åter givnings problem.

Ett vanligt fel som ska vara mindful av har ett uttryck som förlitar sig på en funktions egenskap som kanske inte finns i alla funktioner. Till exempel använder följande kod ett uttryck för att ange egenskapen Color för ett bubbeldiagram till `myColor` egenskapen för en funktion.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: ['get', 'myColor']
});
```

Ovanstående kod fungerar bra om alla funktioner i data källan har en `myColor` egenskap och värdet för egenskapen är en färg. Detta kanske inte är ett problem om du har fullständig kontroll över data i data källan och vet att vissa funktioner har en giltig färg i en `myColor` egenskap. För att göra den här koden säkert från fel `case` kan ett uttryck användas med `has` uttrycket för att kontrol lera att funktionen har `myColor` egenskapen. Om det gör det `to-color` kan typ uttrycket sedan användas för att försöka konvertera värdet för egenskapen till en färg. Om färgen är ogiltig kan en återställnings färg användas. Följande kod visar hur du gör detta och anger återställnings färgen till grön.

```javascript
var layer = new atlas.layer.BubbleLayer(source, null, {
    color: [
        'case',

        //Check to see if the feature has a 'myColor' property.
        ['has', 'myColor'],

        //If true, try validating that 'myColor' value is a color, or fallback to 'green'. 
        ['to-color', ['get', 'myColor'], 'green'],

        //If false, return a fallback value.
        'green'
    ]
});
```

### <a name="order-boolean-expressions-from-most-specific-to-least-specific"></a>Beställ booleska uttryck från mest aktuella till minst angivet

När du använder booleska uttryck som innehåller flera villkorliga tester, sorterar du de villkorliga testerna från mest aktuella till minst Special. Genom att göra detta bör det första villkoret minska mängden data som det andra villkoret måste testas mot, vilket minskar det totala antalet villkorliga tester som måste utföras.

### <a name="simplify-expressions"></a>Förenkla uttryck

Uttryck kan vara kraftfulla och ibland komplexa. Det enklare ett uttryck är, desto snabbare kommer det att utvärderas. Exempel: om en enkel jämförelse behövs är ett uttryck som är `['==', ['get', 'category'], 'restaurant']` bättre än att använda ett matchnings uttryck som `['match', ['get', 'category'], 'restaurant', true, false]` . I det här fallet, om den egenskap som är markerad är ett booleskt värde, är ett `get` uttryck ännu enklare `['get','isRestaurant']` .

## <a name="web-sdk-troubleshooting"></a>Fel sökning av webb-SDK

Här följer några tips för att felsöka några vanliga problem som uppstår när du utvecklar med Azure Maps Web SDK.

**Varför visas inte kartan när jag läser in webb kontrollen?**

Gör följande:

* Se till att du har lagt till dina ytterligare autentiseringsalternativ till kartan. Om detta inte läggs till, kommer kartan att läsas in med en tom arbets yta eftersom den inte kan komma åt bas kart data utan autentisering och 401 fel visas på fliken nätverk i webbläsarens utvecklarverktyg.
* Se till att du har en Internet anslutning.
* Se efter i konsolen om det finns fel i webbläsarens utvecklarverktyg. Vissa fel kan orsaka att kartan inte återges. Felsök ditt program.
* Se till att du använder en [webbläsare som stöds](supported-browsers.md).

**Alla mina data visas på den andra sidan av världen, vad händer?**
Koordinater, som även kallas positioner, i Azure Maps SDK: erna överensstämmer med det geospatiala bransch standardformatet `[longitude, latitude]` . Samma format är också hur koordinater definieras i det interjson-schemat. kärn data som formaterats i Azure Maps SDK: er. Om dina data visas på den motsatta sidan av världen, beror det förmodligen på att värdena för longitud och Latitude har återförts i din koordinat/position-information.

**Varför visas HTML-märken på fel plats i webb kontrollen?**

Saker att kontrol lera:

* Om du använder anpassat innehåll för markören ser du till `anchor` att `pixelOffset` alternativen och är korrekta. Som standard justeras innehållet i mitten av innehållet med placeringen på kartan.
* Se till att CSS-filen för Azure Maps har lästs in.
* Kontrol lera HTML-markörens DOM-element för att se om någon CSS från din app har lagts till i markören och påverkar dess placering.

**Varför visas ikoner eller text på symbol lagret på fel plats?**
Kontrol lera att `anchor` alternativen och har `offset` kon figurer ATS korrekt så att de överensstämmer med den del av bilden eller texten som du vill justera med koordinaten på kartan.
Om symbolen bara är på plats när kartan roteras, kontrollerar du `rotationAlignment` alternativet. Som standard kommer symboler vi roterar med kart visnings området så att de visas uppfyllt för användaren. Beroende på ditt scenario kan det dock vara önskvärt att låsa symbolen till kartans orientering. Ange `rotationAlignment` alternativet för `’map’` att göra detta.
Om symbolen bara är på plats när kartan är kanna/lutad, kontrollerar du `pitchAlignment` alternativet. Som standard behålls symboler som är upprättade med kart visnings området när kartan är kanna eller lutande. Beroende på ditt scenario kan det dock vara önskvärt att låsa symbolen till kartans bredd. Ange `pitchAlignment` alternativet för `’map’` att göra detta.

**Varför visas inte några data på kartan?**

Saker att kontrol lera:

* Mer fel finns i konsolen i webbläsarens utvecklarverktyg.
* Se till att en data källa har skapats och lagts till i kartan och att data källan har anslutits till ett åter givnings lager som också har lagts till i kartan.
* Lägg till Bryt punkter i koden och gå igenom den för att se till att data läggs till i data källan och att data källan och lagren läggs till i kartan utan att några fel inträffar.
* Försök att ta bort data drivna uttryck från åter givnings skiktet. Det är möjligt att en av dem kan ha ett fel som orsakar problemet.

**Kan jag använda Azure Maps Web SDK i en IFRAME i begränsat läge?**

Ja. Observera att [Safari har en bugg](https://bugs.webkit.org/show_bug.cgi?id=170075) som förhindrar att begränsade iframes från att köra webb arbetare, vilket är krav på Azure Maps Web SDK. Lösningen är att lägga till `"allow-same-origin"` taggen i egenskapen sandbox i iframe.

## <a name="get-support"></a>Få support

Följande är de olika sätten att få support för Azure Maps beroende på ditt problem.

**Hur gör jag för att rapportera ett data problem eller ett problem med en adress?**

Azure Maps har ett data feedback-verktyg där data problem kan rapporteras och spåras. [https://feedback.azuremaps.com/](https://feedback.azuremaps.com/) Varje problem som skickas genererar en unik URL som du kan använda för att spåra förloppet för data problemet. Hur lång tid det tar att lösa ett data ärende varierar beroende på typ av problem och hur enkelt det är att kontrol lera att ändringen är korrekt. När den här funktionen har åtgärd ATS ser åter givnings tjänsten uppdateringen i varje vecka, medan andra tjänster, t. ex. kod och routning, kommer att se uppdateringen i den månatliga uppdateringen. Detaljerade anvisningar om hur du rapporterar ett data problem finns i det här [dokumentet](how-to-use-feedback-tool.md).

**Hur gör jag för att rapportera ett fel i en tjänst eller API?**

https://azure.com/support

**Var får jag teknisk hjälp med Azure Maps?**

Om det är relaterat till Azure Maps visualisering i Power BI: https://powerbi.microsoft.com/support/ för alla andra Azure Maps tjänster: https://azure.com/support eller forum för utvecklare: https://docs.microsoft.com/answers/topics/azure-maps.html

**Hur gör jag för att gör du en funktions förfrågan?**

Gör en funktions förfrågan på vår användares röst webbplats: https://feedback.azure.com/forums/909172-azure-maps

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fler tips om hur du förbättrar användar upplevelsen i ditt program.

> [!div class="nextstepaction"]
> [Gör ditt program tillgängligt](map-accessibility.md)

Lär dig mer om den terminologi som används av Azure Maps och den geospatiala branschen.

> [!div class="nextstepaction"]
> [Azure Maps-ordlista](glossary.md)
