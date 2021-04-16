---
title: Konvertera ontologier
titleSuffix: Azure Digital Twins
description: Förstå processen med att konvertera branschstandardmodeller till DTDL för Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484493"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Konvertera ontologier av branschstandard till DTDL för Azure Digital Twins

De [flesta ontologier](concepts-ontologies.md) baseras på semantiska webbstandarder, till [exempel RDF](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)och [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

Om du vill använda en modell Azure Digital Twins måste den vara i DTDL-format. Den här artikeln beskriver allmänna designvägledning i form av ett konverteringsmönster för konvertering av RDF-baserade modeller till DTDL så att de kan användas med Azure Digital Twins.  

Artikeln innehåller även exempel [**på konverterarkod**](#converter-samples) för RDF- och CONVERTER-konverterare, som kan utökas för andra scheman i byggbranschen.

## <a name="conversion-pattern"></a>Konverteringsmönster

Det finns flera bibliotek från tredje part som kan användas när du konverterar RDF-baserade modeller till DTDL. Med vissa av de här biblioteken kan du läsa in modellfilen i en graf. Du kan loopa genom grafen och leta efter specifika RDFS- och CONSTRUCT-konstruktioner och konvertera dem till DTDL.   

Följande tabell är ett exempel på hur RDFS- och CONSTRUCT-konstruktioner kan mappas till DTDL. 

| KONCEPT FÖR RDFS/CONCEPT | RDFS/CONSTRUCT-konstruktion | DTDL-begrepp | DTDL-konstruktion |
| --- | --- | --- | --- |
| Klasser | `owl:Class`<br>IRI-suffix<br>``rdfs:label``<br>``rdfs:comment`` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Underklasser | `owl:Class`<br>IRI-suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Egenskaper för datatyp | `owl:DatatypeProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:label`<br>`rdfs:range` | Gränssnittsegenskaper | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objektegenskaper | `owl:ObjectProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relation | `type:Relationship`<br>`name`<br>`target` (eller utelämnas om det inte finns `rdfs:range` någon )<br>`comment`<br>`displayName`<br>

Följande C#-kodfragment visar hur en RDF-modellfil läses in i en graf och konverteras till DTDL med hjälp av [**dotNetRDF-biblioteket.**](https://www.dotnetrdf.org/) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Exempel på konverterare

### <a name="rdf-converter-application"></a>RDF-konverterarprogram 

Det finns ett tillgängligt exempelprogram som konverterar en RDF-baserad modellfil till [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för användning Azure Digital Twins tjänsten. Det har verifierats för [schemat Brick](https://brickschema.org/ontology/) och kan utökas för andra scheman i byggbranschen (till exempel ROBOT [(Building Topology Ontology),](https://w3c-lbd-cg.github.io/bot/) [Semantic Sensor Network](https://www.w3.org/TR/vocab-ssn/)eller [BuildingSmart Industry Foundation Classes (IFC).](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)

Exemplet är ett .NET Core-kommandoradsprogram som heter **RdfToDtdlConverter.**

Du kan hämta exemplet här: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Om du vill ladda ned  koden till din dator väljer du knappen Bläddra kod under rubriken på exempelsidan, vilket tar dig till GitHub-lagringsplatsen för exemplet. Välj knappen **Kod** och Ladda **ned ZIP för** att ladda ned exemplet som en *. ZIP-fil* med *namnetRdfToDtdlConverter-main.zip*. Du kan sedan packa upp filen och utforska koden.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Skärmbild av lagringsplatsen RdfToDtdlConverter på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Du kan använda det här exemplet för att se konverteringsmönster i kontexten och ha som byggblock för dina egna program som utför modellkonverteringar enligt dina egna specifika behov.

### <a name="owl2dtdl-converter"></a>CONVERTER2DTDL-konverterare 

[**CONVERTER2DTDL Converter**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) är ett exempel som översätter en TRANSLATE-ontologi till en uppsättning DTDL-gränssnittsdeklarationer som kan användas med Azure Digital Twins-tjänsten. Det fungerar också för ontologinätverk som består av en rot ontologi som återanvänder andra ontologier via `owl:imports` deklarationer.

Den här konverteraren användes för att [översätta Real Estate Core-ontologin](https://doc.realestatecore.io/3.1/full.html) till DTDL och kan användas för alla AVS-baserade ontologier.

## <a name="next-steps"></a>Nästa steg 

* Läs mer om att utöka ontologier av branschstandard för att uppfylla dina specifikationer: [*Begrepp: Utöka bransch ontologier.*](concepts-ontologies-extend.md)

* Eller fortsätt med vägen för att utveckla modeller baserat på ontologier: [*Använda ontologistrategier i en modellutvecklingsväg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).