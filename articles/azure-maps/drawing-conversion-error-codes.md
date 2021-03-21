---
title: Azure Maps ritnings konverterings fel och varningar
description: Läs om de konverterings fel och varningar som du kan uppfylla när du använder Azure Maps Conversion service. Läs rekommendationerna för hur du löser felen och varningar, med några exempel.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906224"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Fel och varningar vid ritningskonvertering

Med [tjänsten Azure Maps Conversion](/rest/api/maps/conversion) kan du konvertera uppladdade ritnings paket till kart data. Ritnings paketen måste följa [kraven för ritnings paketet](drawing-requirements.md). Om ett eller flera krav inte uppfylls kommer konverterings tjänsten att returnera fel eller varningar. Den här artikeln innehåller konverterings fel och varnings koder, med rekommendationer om hur du löser dem. Den innehåller också några exempel på ritningar som kan orsaka att konverterings tjänsten returnerar dessa koder.

Konverterings tjänsten kommer att lyckas om det finns några konverterings varningar. Vi rekommenderar dock att du granskar och åtgärdar alla varningar. En varning innebär att en del av konverteringen ignorerades eller automatiskt åtgärdades. Om du inte löser varningarna kan det leda till fel i senare processer.

## <a name="general-warnings"></a>Allmänna varningar

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Beskrivning av geometryWarning*

En **geometryWarning** inträffar när ritningen innehåller en ogiltig entitet. En ogiltig entitet är en entitet som inte uppfyller geometriska begränsningar. Exempel på en ogiltig entitet är en själv överlappande polygon eller en icke-stängd polylinje i ett lager som endast stöder stängd geometri.

Konverterings tjänsten kan inte skapa en kart funktion från en ogiltig entitet och ignorerar i stället den.

#### <a name="examples-for-geometrywarning"></a>*Exempel för geometryWarning*

* De två bilderna nedan visar exempel på egna överlappande polygoner.

     ![Exempel på en själv överlappande polygon, till exempel en.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Exempel på en egen överlappande polygon, till exempel två.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Nedan visas en bild som visar en icke-stängd polylinje. Anta att lagret endast stöder stängd geometri.

    ![Exempel på en icke-stängd polylinje](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Så här åtgärdar du geometryWarning*

Kontrol lera **geometryWarning** för varje entitet för att kontrol lera att den följer geometriska begränsningar.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Beskrivning av unexpectedGeometryInLayer*

En **unexpectedGeometryInLayer** -varning inträffar när ritningen innehåller geometri som inte är kompatibel med den förväntade geometri typen för ett angivet lager. När konverterings tjänsten returnerar en **unexpectedGeometryInLayer** varning kommer den att ignoreras.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Exempel på unexpectedGeometryInLayer*

Bilden nedan visar en icke-stängd PolyLine. Anta att lagret endast stöder stängd geometri.

![Exempel på en icke-stängd polylinje](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Så här åtgärdar du unexpectedGeometryInLayer*

Kontrol lera varje **unexpectedGeometryInLayer** -varning och flytta den inkompatibla geometrin till ett kompatibelt lager. Om den inte är kompatibel med någon av de andra lagren bör det tas bort.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Beskrivning av unsupportedFeatureRepresentation*

**UnsupportedFeatureRepresentation** -varningen inträffar när ritningen innehåller en entitetstyp som inte stöds.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Exempel på unsupportedFeatureRepresentation*

Bilden nedan visar en entitetstyp som inte stöds som ett text objekt med flera rader på ett etikett lager.
  
![Exempel på ett text objekt med flera rader på etikett lagret](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Så här åtgärdar du unsupportedFeatureRepresentation*

Se till att dina DWG-filer endast innehåller de entitetstyper som stöds. De typer som stöds finns [i avsnittet krav för Drawing Files i artikeln krav för ritnings paket](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Beskrivning av automaticRepairPerformed*

**AutomaticRepairPerformed** -varningen inträffar när konverterings tjänsten automatiskt reparerar ogiltig geometri.

#### <a name="examples-for-automaticrepairperformed"></a>*Exempel för automaticRepairPerformed*

* Följande bild visar hur konverterings tjänsten reparerat en egen överlappande polygon i en giltig geometri.

    ![Exempel på en själv överlappande polygon som har reparerats](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Bilden nedan visar hur konverterings tjänsten har fäst det första och sista hörnet av en icke-stängd polylinje för att skapa en sluten PolyLine, där det första och sista hörnet var mindre än 1 mm från varandra.  

    ![Exempel på en fixerad PolyLine](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Bilden nedan visar hur, i ett lager som bara stöder stängda polylinjes, reparerar konverterings tjänsten flera icke-stängda polystreck. För att undvika att ta bort de icke-stängda polylinjerna kombinerar tjänsten dem till en enda stängd PolyLine.

    ![Exempel på icke-stängda polystreck i kombination med en enda stängd sammansatt linje](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Så här åtgärdar du automaticRepairPerformed*

Utför följande åtgärder för att åtgärda en **automaticRepairPerformed** -varning:

1. Kontrol lera varje varnings geometri och den speciella varnings texten.
2. Ta reda på om den automatiska reparationen är korrekt.
3. Om reparationen är korrekt fortsätter du. Annars går du till design filen och löser varningen manuellt.

>[!TIP]
>Om du vill ignorera en varning i framtiden gör du ändringar i den ursprungliga ritningen så att den ursprungliga ritningen matchar den reparerade ritningen.

## <a name="manifest-warnings"></a>Manifest varningar

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Beskrivning av redundantAttribution*

**RedundantAttribution** -varningen inträffar när manifestet innehåller redundanta eller motstridiga objekt egenskaper.

#### <a name="examples-for-redundantattribution"></a>*Exempel för redundantAttribution*

* JSON-kodfragmentet nedan innehåller två eller flera `unitProperties` objekt med samma `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* I JSON-kodfragmentet nedan har två eller fler `zoneProperties` objekt samma `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Så här åtgärdar du redundantAttribution*

Om du vill åtgärda en **redundantAttribution* varning tar du bort överflödiga eller motstridiga objekt egenskaper.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Beskrivning av manifestWarning*

En **manifestWarning** inträffar när manifestet innehåller unitProperties-eller zoneProperties-objekt som inte används under konverteringen.

#### <a name="examples-for-manifestwarning"></a>*Exempel för manifestWarning*

* Manifestet innehåller ett `unitProperties` objekt med ett `unitName` som inte har någon matchande etikett i ett `unitLabel` lager.

* Manifestet innehåller ett `zoneProperties` objekt med ett `zoneName` som inte har någon matchande etikett i ett `zoneLabel` lager.

#### <a name="how-to-fix-manifestwarning"></a>*Så här åtgärdar du manifestWarning*

Om du vill åtgärda en **manifestWarning** tar du bort det oanvända `unitProperties` `zoneProperties` objektet eller objektet från manifestet eller lägger till en enhet/zon-etikett till ritningen så att egenskaps-objektet används under konverteringen.

## <a name="wall-warnings"></a>Vägg varningar

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Beskrivning av wallOutsideLevel*

**WallOutsideLevel** -varningen inträffar när ritningen innehåller en vägg geometri utanför gränserna för en nivå kon tur.

#### <a name="example-for-walloutsidelevel"></a>*Exempel på wallOutsideLevel*

* Bilden nedan visar en inre vägg, i rött, utanför den gula nivåns gräns.

    ![Exempel på invändig vägg utanför gräns nivån](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Följande bild visar en yttre vägg, i rött, utanför den gula nivåns gräns.

    ![Exempel på yttre vägg utanför gräns nivån](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Så här åtgärdar du wallOutsideLevel*

Om du vill åtgärda en **wallOutsideLevel** varning expanderar du nivå geometrin så att alla väggar tas med. Eller ändra vägg gränserna så att de passar in i nivå gränsen.

## <a name="unit-warnings"></a>Enhets varningar

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Beskrivning av unitOutsideLevel*

En **unitOutsideLevel** -varning inträffar när ritningen innehåller enhets geometrin utanför gränserna för nivåns kontur.

#### <a name="example-for-unitoutsidelevel"></a>*Exempel på unitOutsideLevel*

 I följande bild överskrider enhets geometrin i rött gränsen för den gula nivå gränsen.

 ![Exempel på enhet som överskrider nivå gränsen](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Så här åtgärdar du unitOutsideLevel*

Om du vill åtgärda en **unitOutsideLevel** varning expanderar du nivåns gräns för att inkludera alla enheter. Du kan också ändra enhets geometrin så att den passar in i nivåns gräns.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Beskrivning av partiallyOverlappingUnit*

En **partiallyOverlappingUnit** -varning inträffar när ritningen innehåller en enhets geometri som delvis överlappar en annan enhets geometri. Konverterings tjänsten ignorerar överlappande enheter.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Exempel scenarier partiallyOverlappingUnit*

I följande bild markeras den överlappande enheten i rött. `UNIT110` och ignoreras `HALLWAY` .

![Exempel på överlappande enheter](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Så här åtgärdar du partiallyOverlappingUnit*

Om du vill åtgärda en **partiallyOverlappingUnit** -varning ritar du om varje delvis överlappande enhet så att den inte överlappar några andra enheter.

## <a name="door-warnings"></a>Dörr varningar

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Beskrivning av doorOutsideLevel*

En **doorOutsideLevel** -varning inträffar när ritningen innehåller en dörr geometri utanför gränserna för nivå geometrin.

#### <a name="example-for-dooroutsidelevel"></a>*Exempel på doorOutsideLevel*

I följande bild överlappar dörr geometrin, som är markerad i rött, den gula gräns nivån.

![Exempel på en dörr som överlappar en nivå gräns](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Så här åtgärdar du doorOutsideLevel*

Om du vill åtgärda en **doorOutsideLevel** varning ritar du om dörrens geometri så att den ligger innanför nivå gränserna.

## <a name="zone-warnings"></a>Zon varningar

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Beskrivning av zoneWarning*

**ZoneWarning** inträffar när en zon inte innehåller en etikett. Konverterings tjänsten ignorerar en zon som inte är etikett. l

#### <a name="example-for-zonewarning"></a>*Exempel på zoneWarning*

Följande bild visar en zon som inte innehåller en etikett.

![Exempel på en zon som inte innehåller en etikett](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Så här åtgärdar du zoneWarning*

Kontrol lera att varje zon har en enda etikett för att åtgärda ett **zoneWarning**.

## <a name="label-warnings"></a>Etikett varningar

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Beskrivning av labelWarning*

**LabelWarning** inträffar när ritningen innehåller tvetydiga eller motstridiga etiketter.

En **labelWarning** inträffar på grund av en eller flera av följande orsaker:

* En enhets etikett finns inte i några enheter.
* En zon etikett finns inte i några zoner.
* En zon etikett finns i två eller flera zoner.

#### <a name="example-for-labelwarning"></a>*Exempel på labelWarning*

Följande bild visar en etikett som finns i två zoner.

![Exempel på en etikett i två zoner ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Så här åtgärdar du labelWarning*

För att åtgärda en **labelWarning**, se till att:

* Alla enhets etiketter är inuti enheter.
* Alla zon etiketter är inuti zoner.
* Alla zon etiketter är i en och endast en zon.

## <a name="drawing-package-errors"></a>Fel i ritnings paket

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Beskrivning av invalidArchiveFormat*

Ett **invalidArchiveFormat** -fel uppstår när ritnings paketet har ett ogiltigt Arkiv format, till exempel gzip eller 7-zip. Endast ZIP-arkivets format stöds.

Ett **invalidArchiveFormat** -fel uppstår också om zip-arkivet är tomt.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Så här åtgärdar du invalidArchiveFormat*

Om du vill åtgärda ett **invalidArchiveFormat** -fel kontrollerar du att:

* Namnet på Arkiv filen slutar med _. zip_.
* Ditt ZIP-arkiv innehåller data.
* Du kan öppna ditt ZIP-arkiv.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Beskrivning av invalidUserData*

Ett **invalidUserData** -fel uppstår när konverterings tjänsten inte kan läsa ett användar data objekt från lagringen.

#### <a name="example-scenario-for-invaliduserdata"></a>*Exempel scenario för invalidUserData*

Du försökte ladda upp ett ritnings paket med en felaktig `udid` parameter.

#### <a name="how-to-fix-invaliduserdata"></a>*Så här åtgärdar du invalidUserData*

Om du vill åtgärda ett **invalidUserData** -fel kontrollerar du att:

* Du har angett rätt `udid` för det överförda paketet.
* Azure Maps Creator (för hands version) har Aktiver ATS för det Azure Maps-konto som du använde för att ladda upp ritnings paketet.
* API-begäran till konverterings tjänsten innehåller prenumerations nyckeln till det Azure Maps konto som du använde för att ladda upp ritnings paketet.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Beskrivning av dwgError*

En **dwgError** när ritnings paketet innehåller ett problem med en eller flera DWG-filer i det ÖVERFÖRda zip-arkivet.

**DwgError** inträffar när ritnings paketet innehåller en DWG-fil som inte kan öppnas eftersom det är ogiltigt eller skadat.

* En DWG-fil är inte en giltig ritning för AutoCAD DWG-filformat.
* En DWG-fil är skadad.
* En DWG-fil visas i _manifest.jspå_ filen, men saknas i zip-arkivet.

#### <a name="how-to-fix-dwgerror"></a>*Så här åtgärdar du dwgError*

Om du vill åtgärda en **dwgError** kan du kontrol _manifest.js_ lera att:

* Alla DWG-filer i ditt ZIP-arkiv är giltiga AutoCAD DWG-format ritningar, öppna var och en i AutoCAD. Ta bort eller åtgärda alla ogiltiga ritningar.
* Listan med DWG-filer i _manifest.jspå_  matchar DWG-filerna i zip-arkivet.

## <a name="manifest-errors"></a>Manifest fel

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Beskrivning av invalidJsonFormat

Ett **invalidJsonFormat** -fel uppstår när det inte går att läsa filen _manifest.js_ .

_manifest.json_file kan inte läsas på grund av JSON-formatering eller syntaxfel. Om du vill veta mer om hur JSON-format och syntax används, se [data överförings formatet JavaScript Object Notation (JSON)](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*Så här åtgärdar du invalidJsonFormat*

Om du vill åtgärda ett **invalidJsonFormat** -fel använder du en JSON-luddfri för att identifiera och lösa eventuella JSON-fel.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Beskrivning av missingRequiredField*

Ett **missingRequiredField** -fel uppstår när nödvändiga data saknas i _manifest.jspå_ filen.

#### <a name="how-to-fix-missingrequiredfield"></a>*Så här åtgärdar du missingRequiredField*

Kontrol lera att manifestet innehåller alla obligatoriska egenskaper för att åtgärda ett **missingRequiredField** -fel. En fullständig lista över nödvändiga manifest objekt finns i [avsnittet manifest i kraven för ritnings paket](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Beskrivning av missingManifest*

**MissingManifest** -felet uppstår när _manifest.jspå_ filen saknas i zip-arkivet.

**MissingManifest** -felet inträffar på grund av en eller flera av följande orsaker:

* _manifest.js_ filen är felstavad.
* _manifest.jspå_ saknas.
* _manifest.jspå_ finns inte i rot katalogen i zip-arkivet.

#### <a name="how-to-fix-missingmanifest"></a>*Så här åtgärdar du missingManifest*

Om du vill åtgärda ett **missingManifest** -fel bekräftar du att arkivet har en fil med namnet _manifest.jspå_ rot nivån i zip-arkivet.

### <a name="conflict"></a>**konflikt**

#### <a name="description-for-conflict"></a>*Beskrivning av konflikt*

**Konflikt** felet uppstår när _manifest.jsi_ filen innehåller motstridig information.

#### <a name="example-scenario-for-conflict"></a>*Exempel scenario för konflikt*

Konverterings tjänsten returnerar ett **konflikt** fel när fler än en nivå har definierats med samma nivå ordnings nummer. Följande JSON-kodfragment visar två nivåer som definierats med samma ordnings tal.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Åtgärda konflikter*

Om du vill åtgärda ett **konflikt** fel kan du kontrol lera _manifest.jspå_ och ta bort all motstridig information.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Beskrivning av invalidGeoreference*

**InvalidGeoreference** -felet uppstår när en _manifest.jsi_ filen innehåller en ogiltig referens.

**InvalidGeoreference** -felet inträffar på grund av en eller flera av följande orsaker:

* Användaren refererar till ett latitud-eller longitud-värde som ligger utanför intervallet.
* Användaren refererar till ett rotations värde som ligger utanför intervallet.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Exempel scenario för invalidGeoreference*

I JSON-kodfragmentet nedan är latituden ovanför den övre gränsen.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Så här åtgärdar du invalidGeoreference*

Om du vill åtgärda ett **invalidGeoreference** -fel kontrollerar du att de värden som är refererade är inom intervallet.

>[!IMPORTANT]
>Koordinaterna för koordinaterna är longitud och latitud i ett interjson. Om du inte använder rätt ordning kan du oavsiktligt referera till ett latitud-eller longitud-värde som ligger utanför intervallet.

## <a name="wall-errors"></a>Vägg fel

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Beskrivning av wallError*

**WallError** inträffar när ritningen innehåller ett fel vid försök att skapa en vägg funktion.

#### <a name="example-scenario-for-wallerror"></a>*Exempel scenario för wallError*

Följande bild visar en vägg funktion som inte överlappar några enheter.

![Exempel på en vägg funktion som inte överlappar några enheter](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Så här åtgärdar du wallError*

Om du vill åtgärda ett **wallError** -fel ritar du om väggen så att den överlappar minst en enhet. Eller skapa en ny enhet som överlappar väggen.

## <a name="vertical-penetration-errors"></a>Fel vid vertikal inträngning

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Beskrivning av verticalPenetrationError*

**VerticalPenetrationError** inträffar när ritningen innehåller en tvetydig lodrät inträngning-funktion.

**VerticalPenetrationError** inträffar på grund av en eller flera av följande orsaker:

* Ritningen innehåller ett vertikalt inträngande område utan överlappande lodräta inträngande områden på några nivåer ovanför eller under den.
* Ritnings paketet innehåller en nivå med två eller flera lodräta inträngande funktioner som båda överlappar en enskild lodrät inträngning på en annan nivå direkt ovanför eller under den.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Exempel scenario för verticalPenetrationError*

Bilden nedan visar ett vertikalt inträngande område utan överlappande lodräta inträngande områden på nivåer över eller under den.

![Exempel på en lodrät inträngning 1](./media/drawing-conversion-error-codes/vrt-2.png)

Följande bild visar ett lodrätt inträngande område som överlappar mer än ett lodrätt inträngande område på en intilliggande nivå.

![Exempel på en vertikal inträngning 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Så här åtgärdar du verticalPenetrationError

Om du vill åtgärda ett **verticalPenetrationError** -fel kan du läsa om hur du använder en lodrät inläsnings funktion i artikeln [krav för ritnings paket](drawing-requirements.md) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda Azure Maps ritnings fel visualiserare](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Skapare (för hands version) för från koppling till inomhus](creator-indoor-maps.md)