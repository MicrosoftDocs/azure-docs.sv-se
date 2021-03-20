---
title: Kärn-i/o-åtgärder | Microsoft Azure Maps
description: Lär dig hur du effektivt läser och skriver XML och avgränsade data med hjälp av kärn bibliotek från den spatiala IO-modulen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891384"
---
# <a name="core-io-operations"></a>Kärn-i/o-åtgärder

Förutom att tillhandahålla verktyg för att läsa spatialdata för spatialdata visar den spatiala IO-modulen kärn-underliggande bibliotek för att läsa och skriva XML och avgränsade data snabbt och effektivt.

`atlas.io.core`Namn området innehåller två lågnivå klasser som snabbt kan läsa och skriva CSV-och XML-data. De här bas klasserna driver data läsare och skrivare i den spatiala IO-modulen. Du kan använda dem för att lägga till ytterligare Läs-och skriv stöd för CSV-eller XML-filer.
 
## <a name="read-delimited-files"></a>Läsa avgränsade filer

`atlas.io.core.CsvReader`Klassen läser strängar som innehåller avgränsade data uppsättningar. Den här klassen ger två metoder för att läsa data:

- `read`Funktionen kommer att läsa den fullständiga data uppsättningen och returnera en tvådimensionell matris med strängar som representerar alla celler i den avgränsade data uppsättningen.
- `getNextRow`Funktionen läser varje textrad i en avgränsad data uppsättning och returnerar en sträng mat ris som representerar alla celler på den raden i data uppsättningen. Användaren kan bearbeta raden och ta bort alla onödiga minnen från den raden innan nästa rad bearbetas. Så är funktionen mer minnes effektiv.

Som standard använder läsaren kommatecknet som avgränsare. Avgränsaren kan dock ändras till ett enskilt tecken eller anges till `'auto'` . När den är inställd på `'auto'` analyserar läsaren den första text raden i strängen. Sedan väljer den det vanligaste specialtecknet från tabellen nedan för att användas som avgränsare.

| Avgränsare | Tecken |
| :-- | :-- |
| Komma | `,` |
| Flik | `\t` |
| Pipe | `|` |

Den här läsaren stöder också text kvalificerare som används för att hantera celler som innehåller avgränsnings tecken. Tecknet quote ( `'"'` ) är standard texten, men den kan ändras till ett enskilt tecken.

## <a name="write-delimited-files"></a>Skriv avgränsade filer

`atlas.io.core.CsvWriter`Skriver en matris med objekt som en avgränsad sträng. Ett enskilt tecken kan användas som avgränsare eller en text-kvalificerare. Standard avgränsaren är kommatecken ( `','` ) och standard text omkvalificeraren är citat `'"'` tecknet ().

Följ stegen nedan om du vill använda den här klassen:

- Skapa en instans av klassen och ange eventuellt en anpassad avgränsare eller text kvalificerare.
- Skriv data till klassen med hjälp av `write` funktionen eller `writeRow` funktionen. `write`Skicka en tvådimensionell matris med objekt som representerar flera rader och celler för funktionen. Om du vill använda `writeRow` funktionen skickar du en matris med objekt som representerar en rad med data med flera kolumner.
- Anropa `toString` funktionen för att hämta den avgränsade strängen. 
- Du kan också anropa `clear` metoden för att göra det återanvändbara och minska resursallokeringen, eller anropa `delete` metoden för att ta bort skrivar instansen.

> [!Note]
> Antalet kolumner som skrivs är begränsat till antalet celler i den första raden av de data som skickas till skrivaren.

## <a name="read-xml-files"></a>Läsa XML-filer

`atlas.io.core.SimpleXmlReader`Klassen är snabbare att parsa XML-filer än `DOMParser` . `atlas.io.core.SimpleXmlReader`Klassen kräver dock att XML-filer är väl formaterade. XML-filer som inte är väl formaterade, till exempel saknade avslutande taggar, kommer troligen att resultera i ett fel.

Följande kod visar hur du använder `SimpleXmlReader` -klassen för att parsa en XML-sträng i ett JSON-objekt och serialisera den till ett önskat format.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Skriv XML-filer

`atlas.io.core.SimpleXmlWriter`Klassen skriver välformaterad XML-kod på ett minnes effektivt sätt.

Följande kod visar hur du använder `SimpleXmlWriter` klassen för att generera en välformaterad XML-sträng.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Den genererade XML-koden från ovanstående kod skulle se ut ungefär så här.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

[Information om dataformat som stöds](spatial-io-supported-data-format-details.md)