---
title: Integrera branschstandardmodeller
titleSuffix: Azure Digital Twins
description: Förstå hur du integrerar bransch standard modeller i DTDL för Azure Digitals, antingen genom att använda särskilda DTDL-Ontologies eller konvertera befintliga Ontologies
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 3376f5d5e207a33ad39cd7506998e2ee90e084ad
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051555"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integrera bransch standard modeller med DTDL för Azure Digitals, dubbla

Med hjälp av modeller som baseras på bransch standarder eller använder standard Ontology-representation, t. ex. RDF eller OWL, får du en rik start punkt när du utformar dina Azures digitala dubbla modeller. Genom att använda bransch modeller kan du också hjälpa till med standardisering och informations delning.

För att kunna användas med digitala Azure-datorer måste en modell representeras i DTDL (JSON-LD-based [**digital)**](concepts-models.md). I den här artikeln beskrivs därför hur du representerar bransch standard modeller i DTDL, som integrerar de befintliga bransch koncepten med DTDL-semantik så att Azures digitala dubbla kan använda dem. DTDL-modellen fungerar sedan som källan till sanningen för modellen i Azure Digitals-modeller.

Det finns tre möjliga sökvägar för integrering av bransch standard modeller med DTDL:
* **Anta**: du kan starta din lösning med en DTDL-Ontology med öppen källkod som har byggts på de mest antagna bransch standarderna. 
* **Konvertera**: om du redan har befintliga modeller måste du konvertera dem till DTDL.
* **Författare**: du kan alltid utveckla dina egna anpassade DTDL-modeller från grunden, enligt beskrivningen i [*How-to: Hantera anpassade modeller*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Anta en DTDL-Ontology med öppen källkod

Det är ofta enklare att börja med en DTDL-Ontology med öppen källkod än att börja från en tom sida. 

Lösningar för smarta hus kan till exempel utnyttja [**DTDL-baserade RealEstateCore-Ontology**](https://github.com/Azure/opendigitaltwins-building)med öppen källkod, vilket ger ett gemensamt underlag för att utforma smarta byggnader och samtidigt utnyttja bransch standarder för att förhindra reuppfinning. 

Dessa DTDL-Ontologies med öppen källkod ger också bästa praxis för hur du använder och utökar modellerna på rätt sätt. 

## <a name="convert-existing-models-to-dtdl"></a>Konvertera befintliga modeller till DTDL

De flesta bransch modeller (kallas även **Ontologies**) baseras på semantiska webb standarder som [Owl](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)och [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Om du vill använda en modell med digitala Azure-dubbla, måste den vara i DTDL-format. I det här avsnittet beskrivs allmän design vägledning i form av ett **konverterings mönster** för att konvertera RDF-baserade modeller till DTDL så att de kan användas med digitala Azure-dubbla. 

Den innehåller också [ **exempel kod kod** för en RDF-konverterare](#sample-converter-application)som har verifierats för [Brick](https://brickschema.org/ontology/) schemat och som kan utökas för andra scheman i byggnads branschen.

### <a name="conversion-pattern"></a>Konverterings mönster

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

### <a name="sample-converter-application"></a>Exempel program för konverterare 

Det finns ett tillgängligt exempel program som konverterar en RDF-baserad modell fil till [DTDL (version 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för användning av tjänsten Azure Digitals dubbla. Den har verifierats för [Brick](https://brickschema.org/ontology/) schema och kan utökas för andra scheman i byggnads branschen (till exempel [Ontology för bygg topologi](https://w3c-lbd-cg.github.io/bot/), [semantisk sensor nätverk](https://www.w3.org/TR/vocab-ssn/)eller [buildingSmart (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Exemplet är ett .NET Core-kommando rads program som heter **RdfToDtdlConverter**.

Du kan hämta exemplet här: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Du kan ladda ned koden till datorn genom att trycka på *Hämta zip* -knappen under rubriken på exempel landnings sidan. En *zip* -fil kommer att hämtas under namnet *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, som du sedan kan packa upp och utforska.

Du kan använda det här exemplet för att se konverterings mönstren i sammanhanget och ha som bygg block för dina egna program som utför modell konverteringar enligt dina egna specifika behov.

## <a name="validate-and-upload-dtdl-models"></a>Verifiera och ladda upp DTDL-modeller

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

När en modell har konverterats och verifierats kan du **Ladda upp den till din Azure Digital-instansen**. Mer information om den här processen finns i avsnittet om att [*Ladda upp modeller*](how-to-manage-model.md#upload-models) i *anvisningar: Hantera anpassade modeller*.

## <a name="next-steps"></a>Nästa steg 

Läs mer om att utforma och hantera digitala dubbla modeller:
 
* [*Begrepp: anpassade modeller*](concepts-models.md)
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)
* [*Anvisningar: parsa och validera modeller*](how-to-parse-models.md)