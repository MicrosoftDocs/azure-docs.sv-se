---
title: Få information om en konverterad modell
description: Beskrivning av alla modell konverterings parametrar
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: f5c38ac88503416b37b720a091c9e46d819a3146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509305"
---
# <a name="get-information-about-a-converted-model"></a>Få information om en konverterad modell

Den arrAsset-fil som skapas av konverterings tjänsten är enbart avsedd att användas av åter givnings tjänsten. Det kan dock finnas tillfällen när du vill komma åt information om en modell utan att starta en rendering-session. Därför placerar konverterings tjänsten en JSON-fil bredvid filen arrAsset i behållaren utdata. Om en fil exempelvis `buggy.gltf` konverteras innehåller behållaren utdata en fil med namnet `buggy.info.json` bredvid den konverterade till gången `buggy.arrAsset` . Den innehåller information om käll modellen, den konverterade modellen och om konverteringen.

## <a name="example-info-file"></a>Exempel på *informations* fil

Här är ett exempel på en *informations* fil som skapas genom att konvertera en fil med namnet `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Information i informations filen

### <a name="the-files-section"></a>Avsnittet *Files*

Det här avsnittet innehåller de angivna fil namnen.

* `input`: Namnet på käll filen.
* `output`: Namnet på utdatafilen, när användaren har angett ett namn som inte är standard.

### <a name="the-conversionsettings-section"></a>Avsnittet *conversionSettings*

Det här avsnittet innehåller en kopia av den [ConversionSettings](configure-model-conversion.md#settings-file) som angavs när modellen konverterades.

### <a name="the-inputinfo-section"></a>Avsnittet *inputInfo*

I det här avsnittet registreras information om käll fils formatet.

* `sourceAssetExtension`: Käll filens fil namns tillägg.
* `sourceAssetFormat`: En beskrivning av käll fils formatet.
* `sourceAssetFormatVersion`: Versionen av käll fils formatet.
* `sourceAssetGenerator`: Namnet på det verktyg som skapade käll filen, om det är tillgängligt.

### <a name="the-inputstatistics-section"></a>Avsnittet *inputStatistics*

Det här avsnittet innehåller information om käll scenen. Det kommer ofta att finnas skillnader mellan värdena i det här avsnittet och motsvarande värden i verktyget som skapade käll modellen. Sådana skillnader förväntas, eftersom modellen ändras under export-och konverterings stegen.

* `numMeshes`: Antalet nät delar, där varje del kan referera till ett enskilt material.
* `numFaces`: Det totala antalet _trianglar_ i hela modellen. Observera att nätet är triangulated under konverteringen. Det här talet bidrar till polygonens gräns på [standard storleken för åter givnings servern](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: Det totala antalet hörn i hela modellen.
* `numMaterial`: Det totala antalet material i hela modellen.
* `numFacesSmallestMesh`: Antalet trianglar i det minsta nätet i modellen.
* `numFacesBiggestMesh`: Antalet trianglar i modellens största maskor.
* `numNodes`: Antalet noder i modellens scen diagram.
* `numMeshUsagesInScene`: Antalet gånger som noder refererar till maskor. Mer än en nod kan referera till samma nät.
* `maxNodeDepth`: Det maximala djupet för noderna i scen diagrammet.

### <a name="the-outputinfo-section"></a>Avsnittet *outputInfo*

I det här avsnittet registreras allmän information om genererade utdata.

* `conversionToolVersion`: Modell konverterarens version.
* `conversionHash`: En hash av data i arrAsset som kan bidra till åter givning. Kan användas för att förstå om konverterings tjänsten har genererat ett annat resultat när den körs igen på samma fil.

### <a name="the-outputstatistics-section"></a>Avsnittet *outputStatistics*

I det här avsnittet registreras information som beräknas från den konverterade till gången.

* `numMeshPartsCreated`: Antalet maskor i arrAsset. Den kan skilja sig från `numMeshes` i `inputStatistics` avsnittet, eftersom indelningen påverkas av konverterings processen.
* `numMeshPartsInstanced`: Antalet maskor som återanvänds i arrAsset.
* `recenteringOffset`: När `recenterToOrigin` alternativet i [ConversionSettings](configure-model-conversion.md) har Aktiver ATS är det här värdet översättningen som flyttar den konverterade modellen tillbaka till dess ursprungliga plats.
* `boundingBox`: Modellens gränser.

## <a name="next-steps"></a>Nästa steg

* [Modell konvertering](model-conversion.md)
* [Konfigurera modellkonverteringen](configure-model-conversion.md)
