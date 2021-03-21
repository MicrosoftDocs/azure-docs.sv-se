---
title: Krav för ritnings paket i Microsoft Azure Maps Creator (för hands version)
description: Lär dig mer om kraven för ritnings paket för att konvertera design filer för anläggningar för att mappa data
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/08/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2a37e716b7804b11ab396909f746af84294bb4e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895279"
---
# <a name="drawing-package-requirements"></a>Krav för ritningspaket


> [!IMPORTANT]
> Azure Maps Creator-tjänster finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan konvertera överförda ritnings paket till kart data med hjälp av [Azure Maps Conversion service](/rest/api/maps/conversion). I den här artikeln beskrivs kraven för ritnings paket för konverterings-API: et. Om du vill visa ett exempel paket kan du hämta exempel [ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Förutsättningar

Ritnings paketet innehåller ritningar som sparats i DWG-format, vilket är det interna fil formatet för Autodesks AutoCAD®-programvara.

Du kan välja valfri CAD-programvara för att skapa ritningarna i ritnings paketet.  

[Tjänsten Azure Maps Conversion](/rest/api/maps/conversion) konverterar ritnings paketet till kart data. Konverterings tjänsten fungerar med fil formatet AutoCAD DWG. `AC1032` är den interna format versionen för DWG-filerna, och det är en bra idé att välja `AC1032` för den interna DWG-versionen av fil formatet.  

## <a name="glossary-of-terms"></a>Ordlista

Här är några termer och definitioner som är viktiga när du läser den här artikeln för enkel referens.

| Period  | Definition |
|:-------|:------------|
| Skikt | Ett AutoCAD DWG-lager.|
| Nivå | Ett del i en byggnad med en angiven höjning. Till exempel en byggnads golv. |
| XREF  |En fil i AutoCAD DWG-filformat (. dwg) som är kopplad till den primära ritningen som en extern referens.  |
| Funktion | Ett objekt som kombinerar en geometri med mer information om metadata. |
| Funktions klasser | En gemensam skiss för funktioner. Till exempel är en *enhet* en funktions klass och ett *kontor* är en funktion. |

## <a name="drawing-package-structure"></a>Ritnings paketets struktur

Ett Drawing-paket är ett zip-arkiv som innehåller följande filer:

* DWG-filer i AutoCAD DWG-filformat.
* En _manifest.jspå_ en fil som beskriver DWG-filerna i ritnings paketet.

Ritnings paketet måste vara zippat till en enda arkivfil med fil namns tillägget. zip. DWG-filerna kan ordnas på valfritt sätt i paketet, men manifest filen måste vara Live i rot katalogen för det zippade paketet. Nästa avsnitt innehåller information om kraven för DWG-filerna, manifest filen och innehållet i dessa filer.

## <a name="dwg-files-requirements"></a>Krav för DWG-filer

En enskild DWG-fil krävs för varje nivå i anläggningen. Nivåns data måste finnas i en enskild DWG-fil. Alla externa referenser (_xrefs_) måste vara kopplade till den överordnade ritningen. Dessutom är varje DWG-fil:

* Måste definiera de _yttre_ och _enhets_ lagren. Du kan också definiera följande valfria lager: _vägg_, _dörr_, _UnitLabel_, _zon_ och _ZoneLabel_.
* Får inte innehålla funktioner från flera nivåer.
* Får inte innehålla funktioner från flera anläggningar.
* Måste referera till samma Mät system och mått enhet som andra DWG-filer i ritnings paketet.

[Tjänsten Azure Maps Conversion](/rest/api/maps/conversion) kan extrahera följande funktions klasser från en DWG-fil:

* Nivåer
* Enheter
* Zoner
* Öppningar
* Väggar
* Lodräta inträngare

Alla konverterings jobb resulterar i en minimal uppsättning standard kategorier: Room, Structure. vägg, Open. dörr, Zone och Facility. Ytterligare kategorier är för varje kategori namn som objekt refererar till.  

Ett DWG-lager måste innehålla funktioner i en enskild klass. Klasser får inte dela ett lager. Enheter och väggar kan till exempel inte dela ett lager.

DWG-lager måste också följa följande kriterier:

* Ritningarnas ursprung för alla DWG-filer måste anpassas till samma latitud och longitud.
* Varje nivå måste ha samma orientering som de andra nivåerna.
* Automatisk överlappande polygoner repare ras automatiskt och [tjänsten Azure Maps Conversion](/rest/api/maps/conversion) genererar en varning. Vi rekommenderar att du inspekterar de reparerade resultaten manuellt, eftersom de kanske inte överensstämmer med de förväntade resultaten.

Alla lager enheter måste vara av någon av följande typer: linje, polylinje, polygon, cirkelformad båge, cirkel, ellips (stängd) eller text (enskild linje). Andra typer av enheter ignoreras.

I tabellen nedan beskrivs de entitetstyper som stöds och konverterade kart funktioner för varje lager. Om ett lager innehåller enhets typer som inte stöds, ignorerar [Azure Maps Conversion service](/rest/api/maps/conversion) dessa entiteter.  

| Skikt | Entitetstyper | Konverterade funktioner |
| :----- | :-------------------| :-------
| [Yttre](#exterior-layer) | Polygon, PolyLine (stängd), cirkel, ellips (stängd) | Nivåer
| [Enhet](#unit-layer) |  Polygon, PolyLine (stängd), cirkel, ellips (stängd) | Lodräta inträngare, enhet
| [Byggnad](#wall-layer)  | Polygon, PolyLine (stängd), cirkel, ellips (stängd) | Inte tillämpligt. Mer information finns i [vägg lagret](#wall-layer).
| [Door](#door-layer) | Polygon, PolyLine, linje, CircularArc, cirkel | Öppningar
| [Zon](#zone-layer) | Polygon, PolyLine (stängd), cirkel, ellips (stängd) | Zon
| [UnitLabel](#unitlabel-layer) | Text (enskild linje) | Inte tillämpligt. Det här lagret kan bara lägga till egenskaper till enhets funktionerna från enhets skiktet. Mer information finns i UnitLabel- [skiktet](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Text (enskild linje) | Inte tillämpligt. Det här lagret kan bara lägga till egenskaper till zon funktioner från ZonesLayer. Mer information finns i ZoneLabel- [skiktet](#zonelabel-layer).

Nästa avsnitt innehåller information om kraven för varje lager.

### <a name="exterior-layer"></a>Yttre lager

DWG-filen för varje nivå måste innehålla ett lager för att definiera nivåns perimeter. Det här skiktet kallas för det *yttre* lagret. Om en anläggning till exempel innehåller två nivåer måste den ha två DWG-filer, med ett utvändigt lager för varje fil.

Oavsett hur många enhets ritningar finns i det yttre lagret innehåller den [resulterande data uppsättningen](tutorial-creator-indoor-maps.md#create-a-feature-stateset) bara en nivå funktion för varje DWG-fil. Dessutom:

* Yttre måste ritas som polygon, PolyLine (stängd), cirkel eller ellips (stängd).
* Yttre kan överlappa varandra, men löses upp i en geometri.
* Den resulterande nivå funktionen måste vara minst 4 kvadratmeter.
* Den resulterande nivå funktionen får inte vara större än 400 000 kvadratmeter.

Om lagret innehåller flera överlappande polystreck, löses polystrecken in i en enda nivå funktion. Alternativt, om lagret innehåller flera icke-överlappande polylinjes, har funktionen för den resulterande nivån en flera polygoner-representation.

Du kan se ett exempel på det yttre lagret som dispositions skiktet i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Enhets skikt

DWG-filen för varje nivå definierar ett lager som innehåller enheter. Enheter är portalerna utrymmen i byggnaden, till exempel kontor, korridoren, trappor och hissar. Om `VerticalPenetrationCategory` egenskapen definieras, konverteras portalerna-enheter som sträcker sig över flera nivåer, till exempel hissar och trappor, till lodräta inträngande funktioner. Lodräta inträngande funktioner som överlappar varandra tilldelas en `setid` .

Enhets lagret måste uppfylla följande krav:

* Enheter måste ritas som polygon, PolyLine (stängda), cirkel eller ellips (stängd).
* Enheter måste ligga innanför gränserna för funktionen yttre perimeter.
* Enheter får inte delvis överlappa varandra.
* Enheter får inte innehålla en egen överlappande geometri.

Namnge en enhet genom att skapa ett text objekt i UnitLabel-skiktet och sedan placera objektet innanför enhetens gränser. Mer information finns i UnitLabel- [skiktet](#unitlabel-layer).

Du kan se ett exempel på enhets lagret i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Vägg lager

DWG-filen för varje nivå kan innehålla ett lager som definierar de fysiska omfattningarna för väggar, kolumner och annan byggnads struktur.

* Väggarna måste ritas som polygon, PolyLine (stängd), cirkel eller ellips (stängd).
* Väggens lager eller lager får bara innehålla geometri som tolkas som en byggnads struktur.

Du kan se ett exempel på väggarna-lagret i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Dörr skikt

Du kan inkludera ett DWG-lager som innehåller dörrar. Varje dörr måste överlappa en enhets kant från enhets lagret.

Dörr öppningar i en Azure Maps data uppsättning representeras som ett enda linje segment som överlappar flera enhets gränser. Följande bilder visar hur du konverterar geometri i dörr skiktet för att öppna funktioner i en data uppsättning.

![Fyra bilder som visar stegen för att generera öppningar](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zon skikt

DWG-filen för varje nivå kan innehålla ett zon lager som definierar de fysiska omfattningarna för zoner. En zon är ett icke-portalerna utrymme som kan namnges och återges. Zoner kan sträcka sig över flera nivåer och grupperas tillsammans med egenskapen zoneSetId.

* Zoner måste ritas som polygoner, PolyLine (stängda) eller ellips (stängda).
* Zoner kan överlappa varandra.
* Zoner kan ligga innanför eller utanför anläggningens yttre perimeter.

Namnge en zon genom att skapa ett text objekt i ZoneLabel-skiktet och placera textobjektet innanför zonens gränser. Mer information finns i [ZoneLabel-skiktet](#zonelabel-layer).

Du kan se ett exempel på zon lagret i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>UnitLabel-skikt

DWG-filen för varje nivå kan innehålla ett UnitLabel-lager. UnitLabel-lagret lägger till en namn egenskap i enheter som extraheras från enhets lagret. Enheter med en namn egenskap kan ha mer information angiven i manifest filen.

* Enhets etiketter måste vara text enheter med en rad.
* Enhets etiketter måste ligga innanför gränserna för deras enhet.
* Enheter får inte innehålla flera text enheter i UnitLabel-skiktet.

Du kan se ett exempel på UnitLabel-lagret i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel-skikt

DWG-filen för varje nivå kan innehålla ett ZoneLabel-lager. Det här lagret lägger till en namn egenskap till zoner som har extraherats från zon skiktet. Zoner med en namn egenskap kan ha mer information angiven i manifest filen.

* Zon etiketter måste vara text enheter med en rad.
* Zon etiketter måste ligga innanför gränserna för zonen.
* Zoner får inte innehålla flera text enheter i ZoneLabel-skiktet.

Du kan se ett exempel på ZoneLabel-lagret i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Manifest fil krav

Zip-mappen måste innehålla en manifest fil på rot nivån i katalogen och filen måste ha namnet **manifest.jspå**. Den beskriver DWG-filerna för att tillåta att [Azure Maps konverterings tjänsten](/rest/api/maps/conversion) tolkar sitt innehåll. Endast de filer som identifieras av manifestet matas in. Filer som finns i zip-mappen, men som inte anges korrekt i manifestet, ignoreras.

Fil Sök vägarna i `buildingLevels` objekt i manifest filen måste vara relativa till roten i zip-mappen. DWG-filnamnet måste exakt matcha namnet på anläggnings nivån. Till exempel är en DWG-fil för nivån "Basement" "Basement. DWG". En DWG-fil för nivå 2 heter som "level_2. DWG." Använd ett under streck om ditt nivå namn har ett blank steg.

Även om det finns krav när du använder manifest objekt krävs inte alla objekt. I följande tabell visas de obligatoriska och valfria objekten för version 1,1 av [Azure Maps Conversion service](/rest/api/maps/conversion).

| Objekt | Krävs | Beskrivning |
| :----- | :------- | :------- |
| `version` | true |Schema version för manifest. För närvarande stöds endast version 1,1.|
| `directoryInfo` | true | Beskriver lokal geografiska och kontakt information. Det kan också användas för att disponera en kontakts geografiska och kontakt uppgifter. |
| `buildingLevels` | true | Anger nivåerna för byggnader och de filer som innehåller nivåernas design. |
| `georeference` | true | Innehåller numerisk geografisk information för ritningen. |
| `dwgLayers` | true | Visar en lista med namnen på lagren och varje lager visar namnen på dess egna funktioner. |
| `unitProperties` | falskt | Kan användas för att infoga fler metadata för enhets funktionerna. |
| `zoneProperties` | falskt | Kan användas för att infoga fler metadata för zon funktionerna. |

Nästa avsnitt innehåller information om kraven för varje objekt.

### `directoryInfo`

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
| `name`      | sträng | true   |  Namn på byggnaden. |
| `streetAddress`|    sträng |    falskt    | Byggnadens adress. |
|`unit`     | sträng    |  falskt    |  Enheten i byggnaden. |
| `locality` |    sträng |    falskt |    Namn på ett område, i eller region. Till exempel "översjöning" eller "Central distrikt". Lokal är inte en del av e-postadressen. |
| `adminDivisions` |    JSON-matris med strängar |    falskt     | En matris som innehåller adress utformningar (land, delstat, stad) eller (land, prefekturen, stad, stad). Använd ISO 3166-lands koder och ISO 3166-2-statliga/distrikts koder. |
| `postalCode` |    sträng    | falskt    | E-postsorterings koden. |
| `hoursOfOperation` |    sträng |     falskt | Följer formatet för [OSM Open hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| `phone`    | sträng |    falskt |    Telefonnummer som är associerat med byggnaden. Måste innehålla lands koden. |
| `website`    | sträng |    falskt    | Webbplats som är kopplad till byggnaden. Måste börja med http eller https. |
| `nonPublic` |    boolesk    | falskt | Flagga som anger om byggnaden är öppen för allmänheten. |
| `anchorLatitude` | numeric |    falskt | Latitud för en fäst punkt (kartnål). |
| `anchorLongitude` | numeric |    falskt | Longitud för en fäst punkt (kartnål). |
| `anchorHeightAboveSeaLevel`  | numeric | falskt | Höjden på anläggningens jord golv över havs nivån, i meter. |
| `defaultLevelVerticalExtent` | numeric | falskt | Standard höjd (tjocklek) på en nivå för den här funktionen som ska användas när en nivå `verticalExtent` är odefinierad. |

### `buildingLevels`

`buildingLevels`Objektet innehåller en JSON-matris med byggnader-nivåer.

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
|`levelName`    |sträng    |true |    Namn på beskrivande nivå. Till exempel: våning 1, lobbyn, blå parkering eller Basement.|
|`ordinal` | heltal |    true | Anger den lodräta ordningen för nivåer. Varje funktion måste ha en nivå med ordnings tal 0. |
|`heightAboveFacilityAnchor` | numeric | falskt |    Nivåns höjd över ankaret i meter. |
| `verticalExtent` | numeric | falskt | Nivå till tak höjd (tjocklek) för nivån i meter. |
|`filename` |    sträng |    true |    Fil Systems Sök väg för CAD-ritningen för en skapande nivå. Det måste vara i förhållande till roten i byggnadens zip-fil. |

### `georeference`

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    Decimal representation av grader latitud vid ritningens ursprung. Ursprungets koordinater måste vara i WGS84 Web Mercator ( `EPSG:3857` ).|
|`lon`    |numeric|    true|    Decimal representation av grader longitud vid ritningens ursprung. Ursprungets koordinater måste vara i WGS84 Web Mercator ( `EPSG:3857` ). |
|`angle`|    numeric|    true|   Vinkeln medsols, i grader, mellan sant norr och ritningens lodräta axel (Y).   |

### `dwgLayers`

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
|`exterior`    |matris med strängar|    true|    Namnen på de lager som definierar den yttre skapande profilen.|
|`unit`|    matris med strängar|    true|    Namnen på de lager som definierar enheter.|
|`wall`|    matris med strängar    |falskt|    Namnen på de lager som definierar väggar.|
|`door`    |matris med strängar|    falskt   | Namnen på de lager som definierar dörrar.|
|`unitLabel`    |matris med strängar|    falskt    |Namnen på de lager som definierar enheternas namn.|
|`zone` | matris med strängar    | falskt    | Namnen på de lager som definierar zoner.|
|`zoneLabel` | matris med strängar |     falskt |    Namnen på de lager som definierar namn på zoner.|

### `unitProperties`

`unitProperties`Objektet innehåller en JSON-matris med enhets egenskaper.

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
|`unitName`    |sträng    |true    |Namnet på den enhet som ska associeras med den här `unitProperty` posten. Posten är endast giltig när en etikett matchning finns `unitName` i `unitLabel` skikten. |
|`categoryName`|    sträng|    falskt    |Kategori namn. En fullständig lista över kategorier finns i [Kategorier](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| matris med strängar |    falskt    |Anger vilka typer av navigerings agenter som kan passera enheten. Den här egenskapen informerar wayfinding-funktionerna. De tillåtna värdena är:,,,,,,,,,, `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` och `disallowed` .|
|`routeThroughBehavior`|    sträng|    falskt    |Enhetens funktions sätt. De tillåtna värdena är `disallowed` , `allowed` och `preferred` . Standardvärdet är `allowed`.|
|`occupants`    |matris med directoryInfo-objekt |falskt    |Lista över personer för enheten. |
|`nameAlt`|    sträng|    falskt|    Alternativt namn på enheten. |
|`nameSubtitle`|    sträng    |falskt|    Under rubrik för enheten. |
|`addressRoomNumber`|    sträng|    falskt|    Enhetens rum, enhet, lägenhet eller serie nummer.|
|`verticalPenetrationCategory`|    sträng|    falskt| När den här egenskapen definieras är den resulterande funktionen en vertikal inträngning (VRT) i stället för en enhet. Du kan använda VRTs för att gå till andra VRT-funktioner på nivåerna ovan eller under. Vertikal inträngning är ett [kategori](https://aka.ms/pa-indoor-spacecategories) namn. Om den här egenskapen definieras `categoryName` åsidosätts egenskapen med `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    sträng|    falskt    |Om `verticalPenetrationCategory` har definierats kan du ange en giltig riktning för resan. De tillåtna värdena är: `lowToHigh` , `highToLow` , `both` , och `closed` . Standardvärdet är `both`.|
| `nonPublic` | boolesk | falskt | Anger om enheten är öppen för offentlig. |
| `isRoutable` | boolesk | falskt | När den här egenskapen har angetts till `false` kan du inte gå till eller genom enheten. Standardvärdet är `true`. |
| `isOpenArea` | boolesk | falskt | Tillåter att navigerings agenten anger enheten utan att behöva öppna den i enheten. Som standard är det här värdet inställt på `true` för enheter utan öppningar och `false` för enheter med öppningar. Om du manuellt ställer in `isOpenArea` `false` på en enhet utan öppningar resulterar det i en varning eftersom den resulterande enheten inte kan kontaktas av en navigerings agent.|

### `zoneProperties`

`zoneProperties`Objektet innehåller en JSON-matris med zon egenskaper.

| Egenskap  | Typ | Obligatorisk | Beskrivning |
|-----------|------|----------|-------------|
|Zonnamn        |sträng    |true    |Namnet på zonen som ska associeras med `zoneProperty` posten. Posten är endast giltig när en etikett matchning `zoneName` hittas i `zoneLabel` zonens skikt.  |
|categoryName|    sträng|    falskt    |Kategori namn. En fullständig lista över kategorier finns i [Kategorier](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    sträng|    falskt    |Alternativt namn på zonen.  |
|zoneNameSubtitle|    sträng |    falskt    |Under rubrik för zonen. |
|zoneSetId|    sträng |    falskt    | Ange ID för att upprätta en relation mellan flera zoner så att de kan frågas eller väljas som en grupp. Till exempel zoner som sträcker sig över flera nivåer. |

### <a name="sample-drawing-package-manifest"></a>Exempel på paket manifest för ritning

Nedan visas manifest filen för exempel ritnings paketet. Om du vill ladda ned hela paketet, se [exempel ritnings paket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Manifest fil

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

När ditt Drawing-paket uppfyller kraven kan du använda [Azure Maps Conversion service](/rest/api/maps/conversion) för att konvertera paketet till en karta-datauppsättning. Sedan kan du använda data uppsättningen för att generera en insamlad karta med hjälp av modulen inomhus Maps.

> [!div class="nextstepaction"]
>[Skapare (för hands version) för inomhus kartor](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Självstudie: skapa en skapare (för hands version) karta](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamisk formatering för inomhus-kartor](indoor-map-dynamic-styling.md)