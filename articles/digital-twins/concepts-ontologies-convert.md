---
title: Konverterar Ontologies
titleSuffix: Azure Digital Twins
description: Förstå processen med att konvertera bransch standard modeller till DTDL för digitala Azure-enheter
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: aa4dde51c077152dd5c8a938ad64ad0a051f89ad
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561834"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Omvandla bransch standard Ontologies till DTDL för digitala Azure-enheter

De flesta [Ontologies](concepts-ontologies.md) baseras på semantiska webb standarder som [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)och [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Om du vill använda en modell med digitala Azure-dubbla, måste den vara i DTDL-format. I den här artikeln beskrivs allmän design vägledning i form av ett **konverterings mönster** för att konvertera RDF-baserade modeller till DTDL så att de kan användas med digitala Azure-dubbla. 

Artikeln innehåller också [**exempel på konverterare**](#converter-samples) för RDF-och Owl-konverterare, som kan utökas för andra scheman i byggnads branschen.

## <a name="conversion-pattern"></a>Konverterings mönster

Det finns flera bibliotek från tredje part som kan användas när du konverterar RDF-baserade modeller till DTDL. Med vissa av dessa bibliotek kan du läsa in modell filen i ett diagram. Du kan gå igenom grafen som söker efter vissa RDFS-och OWL-konstruktioner och konvertera dessa till DTDL.   

Följande tabell är ett exempel på hur RDFS-och OWL-konstruktioner kan mappas till DTDL. 

| RDFS/OWL-koncept | RDFS/OWL-konstruktion | DTDL-koncept | DTDL-konstruktion |
| --- | --- | --- | --- |
| Klasser | `owl:Class`<br>IRI-suffix<br>``rdfs:label``<br>``rdfs:comment`` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Underklasser | `owl:Class`<br>IRI-suffix<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Gränssnitt | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Egenskaper för datatyp | `owl:DatatypeProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:label`<br>`rdfs:range` | Gränssnitts egenskaper | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Objekt egenskaper | `owl:ObjectProperty`<br>`rdfs:label` eller `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relation | `type:Relationship`<br>`name`<br>`target` (eller utelämnat om Nej `rdfs:range` )<br>`comment`<br>`displayName`<br>

Följande C#-kodfragment visar hur en RDF modell fil läses in i ett diagram och konverteras till DTDL med hjälp av [**dotNetRDF**](https://www.dotnetrdf.org/) -biblioteket. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Konverterare exempel

### <a name="rdf-converter-application"></a>RDF Converter-program 

Det finns ett tillgängligt exempel program som konverterar en RDF-baserad modell fil till [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för användning av tjänsten Azure Digitals dubbla. Den har verifierats för [Brick](https://brickschema.org/ontology/) schema och kan utökas för andra scheman i byggnads branschen (till exempel [Ontology för bygg topologi](https://w3c-lbd-cg.github.io/bot/), [semantisk sensor nätverk](https://www.w3.org/TR/vocab-ssn/)eller [buildingSmart (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Exemplet är ett .NET Core-kommando rads program som heter **RdfToDtdlConverter**.

Du kan hämta exemplet här: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Du kan ladda ned koden till datorn genom att trycka på *Hämta zip* -knappen under rubriken på exempel landnings sidan. En *zip* -fil kommer att hämtas under namnet *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, som du sedan kan packa upp och utforska.

Du kan använda det här exemplet för att se konverterings mönstren i sammanhanget och ha som bygg block för dina egna program som utför modell konverteringar enligt dina egna specifika behov.

### <a name="owl2dtdl-converter"></a>OWL2DTDL-konverterare 

[**OWL2DTDL-konverteraren**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) är ett exempel som översätter en Owl-Ontology till en uppsättning DTDL-gränssnitts deklarationer som kan användas med Azures digitala dubbla-tjänster. Det fungerar också för Ontology-nätverk, som görs av en rot Ontology som återanvänder andra Ontologies genom `owl:imports` deklarationer.

Denna konverterare användes för att översätta [fastighets kärn Ontology](https://doc.realestatecore.io/3.1/full.html) till DTDL och kan användas för alla Owl-baserade Ontology.

## <a name="next-steps"></a>Nästa steg 

* Lär dig mer om att utöka bransch standard Ontologies för att uppfylla dina specifikationer: [*begrepp: utöka bransch Ontologies*](concepts-ontologies-extend.md).

* Eller också kan du fortsätta med sökvägen för att utveckla modeller som baseras på Ontologies: [*använda Ontology-strategier i en modell utvecklings väg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).