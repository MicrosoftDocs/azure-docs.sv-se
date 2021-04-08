---
title: Azure Maps ord lista | Microsoft Docs
description: En ord lista över ofta använda termer som är associerade med Azure Maps, Location-Based tjänster och GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897185"
---
# <a name="glossary"></a>Ordlista

I följande lista beskrivs vanliga ord som används med Azure Maps-tjänsterna.

## <a name="a"></a>A

<a name="address-validation"></a>**Adress verifiering**: processen att verifiera att det finns en adress.

<a name="advanced-routing"></a>**Avancerad routning**: en samling tjänster som utför förskotts åtgärder med hjälp av routnings data för vägar. till exempel beräkna nåbara intervall (ISO Kron), avstånds-matriser och batch Route-begäranden.

<a name="aerial-imagery"></a>**Flyg bilder**: se [satellit-bilder](#satellite-imagery). 

<a name="along-a-route-search"></a>**Utmed en vägs ökning**: en spatial fråga som söker efter data inom en angiven avvisnings tid eller avstånd från en väg Sök väg.

<a name="altitude"></a>**Höjd**: höjden eller den vertikala höjningen av en punkt ovanför en referens yta. Höjd mätningarna baseras på en specifik referens punkt, t. ex. Mean Sea-nivå. Se även höjning.

<a name="ambiguous"></a>**Tvetydig**: ett läge med osäkerhet i data klassificering som finns när ett objekt kan ha en lämplig tilldelning av två eller flera värden för ett angivet attribut. Till exempel returneras två tvetydiga resultat för "CA": t. ex. "Kanada" och "Kalifornien". "CA" är ett land/en region och en delstats kod för "Kanada" och "Kalifornien". 

<a name="annotation"></a>**Anteckning**: text eller grafik som visas på kartan för att ge information till användaren. Anteckningen kan identifiera eller beskriva en speciell kart organisation, ange allmän information om ett område på kartan eller tillhandahålla information om kartan.

<a name="antimeridian"></a>**Antimeridian**: kallas <sup>även 180-</sup> Meri Dian. Detta är punkten där-180 grader och 180 grader av longitud möts. Vilket är motsatsen till den viktigaste Meri Dian i hela världen.

<a name="application-programming-interface-api"></a>**API (Application Programming Interface)**: en specifikation som gör det möjligt för utvecklare att skapa program.

<a name="api-key"></a>**API-nyckel**: se [autentisering med delad nyckel](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Intresse område (AOI)**: den omfattning som används för att definiera ett fokus område för antingen en karta eller en databas produktion.

<a name="asset-tracking"></a>**Till gångs spårning**: processen att spåra platsen för en till gång, till exempel en person, ett fordon eller något annat objekt.

<a name="asynchronous-request"></a>**Asynkron begäran**: en http-begäran som öppnar en anslutning och skickar en begäran till servern som returnerar en identifierare för den asynkrona begäran och stänger sedan anslutningen. Servern fortsätter att bearbeta begäran och användaren kan kontrol lera statusen med hjälp av identifieraren. När begäran har bearbetats kan användaren hämta svaret. Den här typen av begäran används för tids krävande processer.

<a name="autocomplete"></a>**Autoavsluta**: en funktion i ett program som förutsäger resten av ett ord som användaren skriver. 

<a name="autosuggest"></a>**Automatiska förslag**: en funktion i ett program som förutsäger logiska möjligheter för vad användaren skriver.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (pund)**: det tidigare namnet på Azure Maps när det fanns i för hands versionen.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Azure AD är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Azure Maps Azure AD-integrering är för närvarande tillgänglig i för hands versionen för alla Azure Maps-API: er. Azure AD har stöd för rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge detaljerad åtkomst till Azure Maps resurser. Mer information om Azure Maps Azure AD-integrering finns i [Azure Maps och Azure AD](azure-maps-authentication.md) och [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Azure Maps nyckel**: Visa [autentisering med delad nyckel](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Bas karta**: den del av ett kart program som visar bakgrunds referensinformation som vägar, landmärken och politiska gränser.

<a name="batch-request"></a>**Batch-begäran**: processen att kombinera flera begär anden till en enskild begäran.

<a name="bearing"></a>**Bär**: den vågräta riktningen för en punkt i förhållande till en annan punkt. Detta uttrycks som en vinkel i förhållande till norr, från 0 – grader till 360 grader i medurs riktning. 

<a name="boundary"></a>**Gräns**: en linje eller polygon som separerar intilliggande politiska entiteter, t. ex. länder/regioner, distrikt och egenskaper. En kant linje är en linje som kanske inte följer fysiska funktioner, till exempel floder, berg eller väggar.

<a name="bounds"></a>**Gränser**: se [avgränsnings ruta](#bounding-box).

<a name="bounding-box"></a>**Avgränsnings ruta**: en uppsättning koordinater som används för att representera ett rektangulärt område på kartan. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: en post med registrerat land och egenskaper. Se även [paket](#parcel).

<a name="camera"></a>**Kamera**: i kontexten för en interaktiv kart kontroll definierar en kamera mappnings fältet i vyn. CAD-områdets visnings område bestäms baserat på flera kart parametrar: Center, zoomnings nivå, höjd vinkel. 

<a name="centroid"></a>**Centroid**: det geometriska mitten av en funktion. Centroid för en rad är mitt punkten medan centroid för en polygon skulle vara dess centrum av Area.

<a name="choropleth-map"></a>**Choropleth karta**: en tema karta där områden är skuggade i proportion till en mätning av en statistisk variabel. Den här statistiska variabeln visas på kartan. Du kan till exempel färga gränsen för varje AMERIKANSKt tillstånd baserat på dess relativa befolkning till alla andra tillstånd.

<a name="concave-hull"></a>**Concave skrov**: en form som representerar en möjlig Concave-geometri som innesluter alla former i den angivna data uppsättningen. Den genererade figuren påminner om att figursätta data med plast brytning och sedan Värm upp den, vilket leder till att stor sträcker sig mellan punkter att Cave i mot andra data punkter.

<a name="consumption-model"></a>**Förbruknings modell**: information som definierar den hastighet med vilken ett fordon förbrukar bränsle eller elektricitet. Se även [dokumentationen om förbruknings modellen](consumption-model.md).

<a name="control"></a>**Kontroll**: en självständig eller återanvändbar komponent som består av ett grafiskt användar gränssnitt som definierar en uppsättning beteenden för gränssnittet. En kart kontroll är till exempel vanligt vis delen av användar gränssnittet som läser in en interaktiv karta.

<a name="convex-hull"></a>**Konvex skrov**: en konvex skrovet är en form som representerar den minsta konvexa geometri som innesluter alla former i den angivna data uppsättningen. Den genererade figuren påminner om att figursätta ett elastiskt band runt data uppsättningen.

<a name="coordinate"></a>**Koordinat**: består av longitud-och Latitude-värden som används för att representera en plats på en karta.

<a name="coordinate-system"></a>**Koordinatsystem**: ett referens ramverk som används för att definiera positionen för punkter i utrymme i två eller tre dimensioner.

<a name="country-code"></a>**Landskod**: en unik identifierare för ett land/en region som baseras på ISO-standarden. ISO2 är en kod med två tecken för ett land/en region (till exempel US), som ISO3 representerar en kod på tre tecken (till exempel USA).

<a name="country-subdivision"></a>**Land-underavdelning**: en underavdelning på första nivån av ett land/en region, vanligt vis kallat delstat eller provins.

<a name="country-secondary-subdivision"></a>**Sekundär underavdelning i land**: en underavdelning på andra nivån av ett land/en region, som ofta kallas för en region.

<a name="country-tertiary-subdivision"></a>**Land, tertiär underavdelning**: en underindelning på tredje nivån av ett land/en region, vanligt vis ett namngivet område, till exempel ett inåt.

<a name="cross-street"></a>**Kors gatan**: en punkt där två eller flera gator överlappar varandra.

<a name="cylindrical-projection"></a>**Projektion av cylindriskt**: en projektion som transformerar punkter från ett spheroid eller en sfär till en tangens eller sekant-cylinder. Cylindern segmenteras sedan uppifrån och ned och plattas till ett plan.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: referens specifikationen för ett Mät system, ett system med koordinater på en yta (vågrätt datum) eller höjd över eller under en yta (lodrätt datum).

<a name="dbf-file"></a>**DBF-fil**: ett databas fil format som används i kombination med formfiler (SHP).

<a name="degree-minutes-seconds-dms"></a>Antal **minuter sekunder (DMS)**: mått enheten för att beskriva latitud och longitud. En grad<sup>är 1/360 a</sup> cirkel. En examen delas upp ytterligare i 60 minuter och en minut delas upp i 60 sekunder.

<a name="delaunay-triangulation"></a>**Delaunay-triangulering**: en teknik för att skapa ett rutnät av sammanhängande, överlappande trianglar från en data uppsättning med punkter. Varje triangels circumscribing-cirkel innehåller inga punkter från data uppsättningen i sin insida.

<a name="demographics"></a>**Demografiska**: statistiska egenskaper (till exempel ålder, födelse takt och inkomst) för en befolknings grupp.

<a name="destination"></a>**Mål**: en slut punkt eller plats där någon reser till.

<a name="digital-elevation-model-dem"></a>**Digital höjnings modell (dem)**: en data uppsättning med höjd punkts värden som är relaterade till en yta, som fångats upp över ett utrymme med jämna mellanrum med ett gemensamt datum. DEMs används vanligt vis för att representera terräng lättnader.

<a name="dijkstra's-algorithm"></a>**Dijkstra**: en algoritm som undersöker anslutningen till ett nätverk för att hitta den kortaste vägen mellan två punkter.

<a name="distance-matrix"></a>**Avstånds mat ris**: en matris som innehåller res tid och avstånds information mellan en uppsättning ursprung och mål. 

## <a name="e"></a>E

<a name="elevation"></a>**Höjning**: det lodräta avståndet för en punkt eller ett objekt ovanför eller under en referens yta eller ett datum. I allmänhet är referens ytan en havs nivå. Höjning avser vanligt vis den lodräta höjden på marken.

<a name="envelope"></a>**Envelope**: se [avgränsnings rutan](#bounding-box).

<a name="extended-postal-code"></a>**Utökat post** nummer: ett post nummer som kan innehålla ytterligare information. Zip-koder i USA har till exempel fem siffror. Men ett utökat post nummer, kallat zip + 4, innehåller fyra ytterligare siffror. Dessa ytterligare siffror används för att identifiera ett geografiskt segment inom ett leverans område med fem siffror, till exempel ett City, en grupp lägenheter eller en box. Att känna till det geografiska segmentet bidrar till effektiv e-postsortering och leverans.

<a name="extent"></a>**Omfattning**: se [avgränsnings rutan](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federerad autentisering**: en autentiseringsmetod som gör att en enskild mekanism för inloggning/autentisering kan användas över flera webb-och mobilappar. 

<a name="feature"></a>**Funktion**: ett objekt som kombinerar en geometri med ytterligare metadatainformation. 

<a name="feature-collection"></a>**Funktions samling**: en samling funktions objekt.

<a name="find-along-route"></a>**Sök utmed väg**: en rums fråga som söker efter data inom en angiven avvisnings tid eller avstånd från en väg Sök väg.

<a name="find-nearby"></a>**Sök i närheten**: en rums fråga som söker efter ett fast linje avstånd (som kråkfotsnotation) från en punkt.

<a name="fleet-management"></a>**Hantering av flottan**: hantering av nytto fordon, till exempel bilar, Last bilar, fartyg och plan. Flottans hantering kan omfatta en mängd funktioner, till exempel fordons finansiering, underhåll, telematik (spårning och diagnostik) samt driv rutin, hastighet, bränsle och hälso tillstånd och säkerhets hantering. Flottans hantering är en process som används av företag som använder transport i sin verksamhet. Företagen vill minimera riskerna och minska sina totala kostnader för transport och personal, samtidigt som du säkerställer efterlevnaden av myndighets lagstiftningen.

<a name="free-flow-speed"></a>**Fri flödes hastighet**: den kostnads fria flödes hastigheten förväntas under ideala förhållanden. Vanligt vis hastighets gränsen.

<a name="free-form-address"></a>**Fri Forms adress**: en fullständig adress som representeras som en enskild textrad.

<a name="fuzzy-search"></a>**Fuzzy-sökning**: en sökning som tar i en fritext text sträng som kan vara en adress eller ett intresse punkt. 

## <a name="g"></a>G

<a name="geocode"></a>**Landskod**: en adress eller plats som har konverterats till en koordinat som kan användas för att visa den platsen på en karta. 

<a name="geocoding"></a>En **hårdkodad** kod: kallas även för vanlig kodning, är processen att konvertera adress för plats data till koordinater. 

<a name="geodesic-path"></a>**Geodesic-sökväg**: den kortaste vägen mellan två punkter på en böjd yta. Vid åter givning av Azure Maps den här sökvägen visas som en böjd linje på grund av Mercator-projektionen.

<a name="geofence"></a>**Avgränsning**: en definierad geografisk region som kan användas för att utlösa händelser när en enhet går in eller finns i regionen.

<a name="geojson"></a>Geografiskt **JSON**: är ett vanligt JSON-baserat fil format som används för att lagra geografiska vektor data, till exempel punkter, linjer och polygoner. **Obs**: Azure Maps använder en utökad version av en interjson som [dokumenteras här](extend-geojson.md).

<a name="geometry"></a>**Geometry**: representerar ett spatial objekt, till exempel en punkt, linje eller polygon.

<a name="geometrycollection"></a>**GeometryCollection**: en samling geometri objekt.

<a name="geopol"></a>**GeoPol**: refererar till polypolitisk känslig information, till exempel omtvistade kant linjer och plats namn.

<a name="georeference"></a>**Referens**: processen för att justera geografiska data eller bilder till ett känt koordinatsystem. Den här processen kan bestå av växling, rotation, skalning eller skevning av data.

<a name="georss"></a>**GeoRSS**: ett XML-tillägg för att lägga till SPATIALDATA i RSS-flöden.

<a name="gis"></a>**GIS**: en förkortning för "geografisk informations system". En vanlig term som används för att beskriva mappnings branschen.

<a name="gml"></a>**GML**: kallas även för geografi märknings språk. Ett XML-filtillägg för lagring av spatialdata.

<a name="gps"></a>**GPS**: kallas även Global Positioning system, är ett system med satelliter som används för att fastställa enheternas placering på jorden. Krets satelliterna skickar signaler som tillåter en GPS-mottagare var som helst på jorden för att beräkna sin egen plats genom trilateration.

<a name="gpx"></a>**GPX**: även känt som GPS Exchange-format, är ett XML-filformat som vanligt vis skapas från GPS-enheter.  

<a name="great-circle-distance"></a>**Stort avstånd för cirkel**: det kortaste avståndet mellan två punkter på ytan på en sfär.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: tiden på den primära Meri Dian, som körs genom den här informandet i Greenwich, England.

<a name="guid"></a>**GUID**: en globalt unik identifierare. En sträng som används för att unikt identifiera ett gränssnitt, klass, typ bibliotek, en komponent kategori eller en post.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine formel**: en vanlig ekvation som används för att beräkna den fantastiska cirkel avståndet mellan två punkter på en sfär.

<a name="hd-maps"></a>**HD Maps**: även känt som hög definitions kartor, består av hög åter givning av trafik information på väg, till exempel band markeringar, signering och riktnings lampor som krävs för fristående körning.

<a name="heading"></a>**Rubrik**: riktningen något är riktad mot. Se även [försedda](#heading).

<a name="heatmap"></a>**Termisk karta**: en data visualisering där en uppsättning färger representerar densiteten för punkter i ett visst område. Se även tematiska kartor.

<a name="hybrid-imagery"></a>**Hybrid bilder**: satellit-eller flyg bilder som har väg data och etiketter som överordnas ovanpå den.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: en akronym för Internet Assigned Numbers Authority. En grupp som inte är vinst och som ser över den globala IP-adressallokering.

<a name="isochrone"></a>**Isochrone**: en Isochrone definierar det område där någon kan färdas inom en angiven tid för ett transport sätt i vilken riktning som helst från en viss plats. Se även [nåbart intervall](#reachable-range).

<a name="isodistance"></a>**Isodistance**: en isochrone definierar i vilken del någon kan färdas inom ett angivet avstånd för ett transport sätt i valfri riktning. Se även [nåbart intervall](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: det kallas även för-språk, är ett vanligt XML-filformat för att lagra geografiska vektor data som punkter, linjer och polygoner. 

## <a name="l"></a>L

<a name="landsat"></a>**LANDSAT**: Multispectral, jord-kretsing-satelliter som utvecklats av NASA som samlar in bilder av mark. Den här bilder används i många branscher, till exempel jord-, skogsbruks-och cartography.

<a name="latitude"></a>**Latitud**: vinkel Avståndet mäts i grader från likställaren i en norr eller syd-riktning.

<a name="level-of-detail"></a>**Detalj nivå**: se zoomnings nivå.

<a name="lidar"></a>**LIDAR**: akronym för Light och mellanliggande. En teknik för fjärranalys som använder laser för att mäta avstånd för att reflektera ytor.

<a name="linear-interpolation"></a>**Linjär interpolation**: en uppskattning av ett okänt värde med hjälp av det linjära avståndet mellan kända värden.

<a name="linestring"></a>**Lin Est ring**: en geometri som används för att representera en linje. Kallas även polyline. 

<a name="localization"></a>**Lokalisering**: stöd för olika språk och kulturer.

<a name="logistics"></a>**Logistik**: processen att flytta personer, fordon, varor eller till gångar på ett samordnat sätt.

<a name="longitude"></a>**Longitud**: det vinkel avstånd som mäts i grader från den primära Meri Dian i en öst-eller väst riktning.

## <a name="m"></a>M

<a name="map-tile"></a>**Kart panel**: en rektangulär bild som representerar en partition på en kart arbets yta. Mer information finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Markör**: kallas även för en PIN-kod eller kartnål, är en ikon som representerar en plats på en karta.

<a name="mercator-projection"></a>**Mercator projektion**: en cylindrisk kart projektion som blev standard kart projektion i nautiska syfte på grund av dess möjlighet att representera linjer med konstant kurs, även kallat Rhumb-linjer, som raka segment som bevarar vinklarna med meridianer. Alla fasta kart projektioner förvränger figurerna eller storlekarna på kartan när den jämförs med den verkliga layouten på jordens yta. Mercator projektion exaggerates områdena från den likställare, så att mindre områden visas större på kartan när du närmar staven. 

<a name="multilinestring"></a>**MultiLineString**: en geometri som representerar en samling av lin Est ring-objekt. 

<a name="multipoint"></a>**Multipoint**: en geometri som representerar en samling punkt objekt.

<a name="multipolygon"></a>**Multipolygon**: en geometri som representerar en samling polygon-objekt. Om du till exempel vill visa avgränsningen av Hawaii visas varje ön med en polygon. Därför skulle gränserna för Hawaii vara en multipolygon.

<a name="municipality"></a>**Kommun**: en stad eller stad. 

<a name="municipality-subdivision"></a>**Kommun del avdelning**: en underavdelning till en kommun, t. ex. ett område eller ett lokalt namn, till exempel "centralt".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigerings fält**: uppsättningen kontroller i en karta som används för att justera zoomnings nivå, färgdjup, rotation och växlar mellan bas kart skiktet.

<a name="nearby-search"></a>**Närliggande sökning**: en rums fråga som söker efter ett fast linje avstånd (som kråkfotsnotation) från en punkt.

<a name="neutral-ground-truth"></a>**Neutral mark sanningen**: en karta som återger etiketter på det officiella språket i den region som den representerar och i lokala skript om de är tillgängliga.

## <a name="o"></a>O

<a name="origin"></a>**Ursprung**: en start punkt eller plats där en användare är.

## <a name="p"></a>P

<a name="panning"></a>**Panorering**: processen för att flytta kartan i vilken riktning som helst och samtidigt bibehålla en konstant zoomnings nivå.

<a name="parcel"></a>**Skifte**: ett område med mark-eller egenskaps gränser.

<a name="pitch"></a>**Färgdjup**: den mängd lutning som kartan har i förhållande till den lodräta där 0 ser ut rakt nedåt på kartan.

<a name="point"></a>**Punkt**: en geometri som representerar en enskild position på kartan. 

<a name="points-of-interest-poi"></a>**Orienterings punkter (POI)**: en verksamhets-, landmärkes-eller gemensam plats.

<a name="polygon"></a>**Polygon**: en solid geometri som representerar ett område på en karta. 

<a name="polyline"></a>**Polyline**: en geometri som används för att representera en linje. Kallas även lin Est ring. 

<a name="position"></a>**Position**: longitud, latitud och höjd (x, y, z-koordinater) för en punkt.

<a name="post-code"></a>**Post** nummer: se [post nummer](#postal-code).

<a name="postal-code"></a>**Post nummer**: en serie bokstäver eller siffror, eller båda, i ett särskilt format. Post numret används av post tjänsten för ett land/en region för att dela upp geografiska områden i zoner för att förenkla leverans av e-post.

<a name="primary-key"></a>**Primär nyckel**: den första av två prenumerations nycklar som tillhandahölls för Azure Maps autentisering med delad nyckel. Se [autentisering med delad nyckel](#shared-key-authentication).

<a name="prime-meridian"></a>**Primtal Meri Dian**: en linje med longitud som representerar en longitud på 0 grader. I allmänhet minskar värdet för longitud när du reser i en Westerly riktning tills 180 graderas och ökar när du reser i Easterly-instruktioner till-180-grader. 

<a name="prj"></a>**PRJ**: en textfil som ofta medföljer en formfil-fil som innehåller information om det projicerade koordinatsystemet som data uppsättningen finns i.

<a name="projection"></a>**Projektion**: ett projicerat koordinatsystem baserat på en kart projektion, till exempel tvär Mercator, Albers Equal Area och Robinson. Dessa ger möjlighet till projekt kartor över jordens sfäriska yta på ett tvådimensionellt kartesiska koordinat plan. Projekterade koordinatsystem kallas ibland för kart projektioner.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: ett bas-4-adress index för en panel i ett quadtree-inpassande system. Mer information finns i [zoomnings nivåer och Brick rutnäts](zoom-levels-and-tile-grid.md) dokumentation för mer information.

<a name="quadtree"></a>**Quadtree**: en data struktur där varje nod har exakt fyra underordnade. I det ordnings system som används i Azure Maps används en quadtree-struktur, som en användare zoomar in på en nivå, varje kart panel bryts upp i fyra under paneler.  Mer information finns i [zoomnings nivåer och Brick rutnäts](zoom-levels-and-tile-grid.md) dokumentation för mer information.

<a name="queries-per-second-qps"></a>**Frågor per sekund (frågor per sekund)**: antalet frågor eller förfrågningar som kan göras till en tjänst eller plattform inom en sekund. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radiell sökning**: en rums fråga som söker efter ett fast linje avstånd (som kråkfotsnotation) från en punkt. 

<a name="raster-data"></a>**Raster data**: en matris med celler (eller bild punkter) som är ordnade i rader och kolumner (eller ett rutnät) där varje cell innehåller ett värde som representerar information, till exempel temperatur. Rastrering omfattar digitala flyg foton, bilder från satelliter, digitala bilder och scannade kartor.

<a name="raster-layer"></a>**Raster lager**: ett panel lager som består av raster bilder.

<a name="reachable-range"></a>**Nåbart intervall**: ett nåbart intervall definierar området där någon kan färdas inom en angiven tid eller ett visst avstånd, för ett läge med transport till färd, i vilken riktning som helst från en plats. Se även [Isochrone](#isochrone) och [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Fjärranalys**: processen att samla in och tolka sensor data från ett avstånd.

<a name="rest-service"></a>**REST-tjänst**: AKRONYM-rest står för ompresentations tillstånds överföring. En REST-tjänst är en URL-baserad webb tjänst som förlitar sig på grundläggande webb teknik för att kommunicera, de vanligaste metoderna är HTTP GET-och POST-förfrågningar. De här typerna av tjänster tenderar att ha mycket snabbare och mindre än traditionella SOAP-baserade tjänster.

<a name="reverse-geocode"></a>**Omvändly Code**: processen för att ta en koordinat och fastställa adressen som representerar på en karta.

<a name="reproject"></a>**Projekt**: se [omvandling](#transformation).

<a name="rest-service"></a>**REST-tjänst**: akronym för representations tillstånds överföring. En arkitektur för att utbyta information mellan peer-datorer i en decentraliserad, distribuerad miljö. REST gör att program på olika datorer kan kommunicera oberoende av ett operativ system eller en plattform. En tjänst kan skicka en Hypertext Transfer Protocol-begäran (HTTP) till en URL (Uniform Resource Locator) och hämta tillbaka data.

<a name="route"></a>**Route**: en sökväg mellan två eller flera platser, som även kan innehålla ytterligare information, till exempel instruktioner för waypoints längs vägen.

<a name="requests-per-second-rps"></a>**Begär Anden per sekund (RPS)**: se [frågor per sekund (frågor per sekund)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: akronym för Really Simple Syndication, RDF-Sammanfattning (Resource Description Framework) eller omfattande sammanfattning av webbplats, beroende på källa. Ett enkelt, strukturerat XML-format för att dela innehåll mellan olika webbplatser. RSS-dokument innehåller element för nyckel-metadata som författare, datum, rubrik, en kort beskrivning och en hypertext länk. Den här informationen hjälper en användare (eller en RSS-utgivar tjänst) bestämma vilka material som är värda ytterligare undersökningar.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satellit bilder**: bilder som har registrerats av plan och satelliter som pekar rakt nedåt.

<a name="secondary-key"></a>**Sekundär nyckel**: den andra av två prenumerations nycklar som tillhandahölls för Azure Maps autentisering med delad nyckel. Se [autentisering med delad nyckel](#shared-key-authentication).

<a name="shapefile-shp"></a>**Formfil (SHP)**: kallas även Esri-formfil, är ett vektor data lagrings format för lagring av plats, form och attribut för geografiska funktioner. En formfil lagras i en uppsättning relaterade filer.

<a name="shared-key-authentication"></a>**Autentisering med delad nyckel**: autentisering med delad nyckel förlitar sig på att skicka Azure Maps konto genererade nycklar med varje begäran om att Azure Maps. Dessa nycklar kallas ofta för prenumerations nycklar. Vi rekommenderar att nycklar regelbundet återskapas för säkerhet. Två nycklar är tillgängliga så att du kan underhålla anslutningar med en nyckel när du återskapar den andra. När du återskapar nycklarna måste du uppdatera alla program som använder det här kontot till att använda de nya nycklarna. Mer information om Azure Maps autentisering finns i [Azure Maps och Azure AD](azure-maps-authentication.md) och [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Software Development Kit (SDK)**: en samling dokumentation, exempel kod och exempel program som hjälper en utvecklare att använda ett API för att bygga appar.

<a name="spherical-mercator-projection"></a>**Sfärisk Mercator-projektion**: se [webb-Mercator](#web-mercator). 

<a name="spatial-query"></a>**Spatial fråga**: en begäran till en tjänst som utför en spatial åtgärd. Till exempel en radiell sökning eller en vägs ökning.

<a name="spatial-reference"></a>**Rums referens**: ett koordinatbaserade lokala, regionala eller globala system som används för att exakt hitta geografiska enheter. Den definierar koordinatsystemet som används för att relatera kart koordinater till platser i verkligheten. Rums referenser säkerställer att spatialdata från olika lager eller källor kan integreras för korrekt visning eller analys. Azure Maps använder referens systemet [EPSG: 3857](https://epsg.io/3857) koordinatsystemet och WGS 84 för indata för indata.

<a name="sql-spatial"></a>**SQL-spatialdata**: syftar på de spatiala funktioner som är inbyggda i SQL Azure och SQL Server 2008 och senare. Den här spatiala funktionen är även tillgänglig som ett .NET-bibliotek som kan användas oberoende av SQL Server. Mer information finns i dokumentationen om [spatial data (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) .

<a name="subscription-key"></a>**Prenumerations nyckel**: se [autentisering med delad nyckel](#shared-key-authentication).

<a name="synchronous-request"></a>**Synkron begäran**: en http-begäran öppnar en anslutning och väntar på ett svar. Webbläsare begränsar antalet samtidiga HTTP-begäranden som kan göras från en sida. Om flera tids krävande synkrona begär Anden görs samtidigt, kan den här gränsen nås. Begär Anden kommer att fördröjas tills en av de andra begär Anden har slutförts.

## <a name="t"></a>T

<a name="telematics"></a>**Telematik**: skickar, tar emot och lagrar information via telekommunikations enheter tillsammans med kontroll av fjärrobjekt. 

<a name="temporal-data"></a>**Temporala data**: data som specifikt refererar till tider eller datum. Temporala data kan referera till diskreta händelser, till exempel blixt träffar. flytta objekt, till exempel tåg; eller upprepade observationer, till exempel antal från trafik sensorer.

<a name="terrain"></a>**Terräng**: ett mark utrymme med en viss egenskap, till exempel sandbrun terräng eller terräng terräng.

<a name="thematic-maps"></a>**Tematiska kartor**: en tema karta är en enkel karta som görs för att återspegla ett tema om ett geografiskt område. Ett vanligt scenario för den här typen av karta är att färgsätta de administrativa regionerna, till exempel länder/regioner baserat på vissa data mått.

<a name="tile-layer"></a>**Panel lager**: ett lager som visas genom att montera kart paneler (rektangulära sektioner) i ett kontinuerligt lager. Panelerna är antingen raster bild paneler eller vektor paneler. Raster panels lager återges vanligt vis i förväg och de lagras som bilder på en server. Raster panels lager kan använda ett stort lagrings utrymme. Vektor panels lager återges nära real tid i klient programmet. Lagrings kraven på Server sidan är därför mindre för vektor panels lager.

<a name="time-zone"></a>**Tidszon**: en region i världen som iakttar en enhetlig normal tid för juridiska, kommersiella och sociala ändamål. Tids zoner tenderar att följa gränserna för länder/regioner och deras indelningar.

<a name="transaction"></a>**Transaktion**: Azure Maps använder en transaktionell licensierings modell där;

- En transaktion skapas för varje 15 mappnings-eller trafik paneler som begärs.
- En transaktion skapas för varje API-anrop till en av tjänsterna i Azure Maps. Sökning och routning är exempel på Azure Maps-tjänst.

<a name="transformation"></a>**Transformering**: processen att konvertera data mellan olika geografiska koordinatsystem. Du kan till exempel ha vissa data som fångats i Storbritannien och som baseras på OSGB 1936-geografiska koordinatsystemet. Azure Maps använder varianten [EPSG: 3857](https://epsg.io/3857) koordinat Reference system för WGS84. För att data ska kunna visas korrekt måste dess koordinater omvandlas från ett system till ett annat.

<a name="traveling-salesmen-problem-tsp"></a>**TSP (Travel Sales-problem)**: ett Hamiltonian-krets problem där en säljare måste hitta det mest effektiva sättet att besöka en serie stoppas och sedan återgå till Start platsen.  

<a name="trilateration"></a>**Trilateration**: processen för att fastställa positionen för en punkt på jordens yta, med avseende på två andra punkter, genom att mäta avstånden mellan alla tre punkterna.

<a name="turn-by-turn-navigation"></a>**Slå-vid-Stäng navigering**: ett program som innehåller väg instruktioner för varje steg i en väg när användarna närmar sig nästa manövrar.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektor data**: koordinera data som representeras som punkter, linjer eller polygoner.

<a name="vector-tile"></a>**Vektor panel**: en öppen data specifikation för att lagra geospatiala vektor data med samma panel system som kart kontrollen. Se även [panel lager](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problem med VRP (vehikel)**: en klass av problem där en uppsättning beställda vägar för en flotta av fordon beräknas och beaktas som en uppsättning begränsningar. Dessa begränsningar kan omfatta leverans tid i Windows, flera väg kapaciteter och rese varaktighets begränsningar.

<a name="voronoi-diagram"></a>**Voronoi-diagram**: en partition av utrymme i områden, eller celler, som omger en uppsättning geometriska objekt, vanligt vis punkt funktioner. Dessa celler, eller polygoner, måste uppfylla kriterierna för Delaunay-trianglar. Alla platser inom ett områden är närmare objektet det omger än med andra objekt i uppsättningen. Voronoi-diagram används ofta för att avgränsa områden som påverkar geografiska funktioner. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: en waypoint är en specificerad geografisk plats som definieras av longitud och latitud som används i navigerings syfte. Används ofta för att representera en punkt där någon navigerar en väg genom.

<a name="waypoint-optimization"></a>**Optimering av waypoint**: processen för att ordna om en uppsättning waypoints för att minimera res tiden eller avståndet som krävs för att släppa igenom alla angivna waypoints. Beroende på Optimeringens komplexitet kallas den här optimeringen ofta för [problem med rese försäljning](#traveling-salesmen-problem-tsp) eller [fordons routning](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Webb kart tjänst (WMS)**: WMS är en öppen geografisk konsortium (OGC) som definierar avbildnings kart tjänster. WMS-tjänster tillhandahåller kart avbildningar för olika områden i en karta på begäran. Bilderna innehåller förrenderade Symbology och kan återges i ett av flera namngivna format om det definieras av tjänsten.

<a name="web-mercator"></a>**Webb Mercator**: kallas även för sfäriska Mercator-projektioner. Det är en liten variant av Mercator-projektion, som används främst i webbaserade mappnings program. Den använder samma formler som standard-Mercator projektion som används för små skalnings kartor. Webb-Mercator använder dock de sfärisk formlerna i alla skalor, men storskaliga Mercator Maps använder vanligt vis Ellipsoidal form för projektionen. Avvikelsen är imperceptible i global skala, men det gör att Maps av lokala områden skiljer sig något från sanna Ellipsoidal Mercator-kartor, på samma skala.

<a name="wgs84"></a>**WGS84**: en uppsättning konstanter som används för att relatera spatiala koordinater till platser på kartans yta. WGS84-datum är den standard som används av de flesta online mappnings leverantörer och GPS-enheter. Azure Maps använder varianten [EPSG: 3857](https://epsg.io/3857) koordinat Reference system för WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-koordinat**: se [höjd](#altitude). 

<a name="zip-code"></a>**Post** nummer: se [post nummer](#postal-code).

<a name="Zoom level"></a>**Zoomnings nivå**: anger detalj nivån och hur mycket av kartan som är synlig. När du zoomar in hela vägen till nivå 0, är hela världs kartan ofta synlig. Men kartan visar begränsad information, till exempel namn, gränser och havs namn för land/region. När du zoomar in närmare på nivå 17, visar kartan ett område med några stads block med detaljerad väg information. I Azure Maps är den högsta zoomnings nivån 22. Mer information finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md) .