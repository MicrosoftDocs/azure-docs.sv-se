---
title: Vad är en Ontology?
titleSuffix: Azure Digital Twins
description: Lär dig mer om DTDL Industry Ontologies för modellering i en viss domän
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034701"
---
# <a name="what-is-an-ontology"></a>Vad är en Ontology? 

Vokabulären för en Azure digital-lösning med dubbla lösningar definieras med hjälp av [modeller](concepts-models.md)som beskriver vilka typer av entiteter som finns i din miljö.

Ibland kan det vara enklare och mer effektivt att börja med en uppsättning modeller för den aktuella branschen, i stället för att skapa en egen modell uppsättning från grunden, när din lösning är knuten till en viss bransch. Dessa befintliga modell uppsättningar kallas för **Ontologies**. 

I allmänhet är en Ontology en uppsättning modeller för en specifik domän, till exempel en byggnads struktur, IoT-system, Smart City, energi rutnät, webb innehåll osv. Ontologies används ofta som scheman för kunskaps grafer, så som de kan aktivera:
* Harmonisering av program varu komponenter, dokumentation, fråge bibliotek osv.
* Minskad investering i konceptuell modellering och system utveckling
* Enklare data samverkan på en semantisk nivå
* Bästa praxis för åter användning, i stället för att börja från början eller "att prova om hjulet"

I den här artikeln beskrivs varför och hur du använder Ontologies för dina Azures digitala dubbla modeller, samt vilka Ontologies och verktyg som finns tillgängliga idag.

## <a name="using-ontologies-for-azure-digital-twins"></a>Använda Ontologies för digitala Azure-dubbla

Ontologies ger en bra utgångs punkt för digitala dubbla lösningar. De omfattar en uppsättning företagsspecifika modeller och relationer mellan entiteter för att utforma, skapa och parsa ett digitalt, dubbel diagram. Ontologies gör det möjligt för lösningar utvecklare att påbörja en digital lösning från en beprövad start punkt och fokusera på att lösa affärs problem. Ontologies som tillhandahålls av Microsoft är också utformade för att enkelt utökas så att du kan anpassa dem efter din lösning. 

Dessutom kan du använda dessa Ontologies i dina lösningar för att ställa in dem för sömlös integrering mellan olika partner och leverantörer, eftersom Ontologies kan tillhandahålla en gemensam ord lista mellan olika lösningar.

Eftersom modeller i digitala Azure-grupper representeras i [Digitals definitions språk (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), skrivs Ontologies för användning med Azure Digital-dubblare också i DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Strategier för integrering av Ontologies

Det finns tre möjliga strategier för integrering av bransch standard Ontologies med DTDL. Du kan välja den som passar bäst för dig, beroende på dina behov:

| Strategi | Beskrivning | Resurser |
| --- | --- | --- |
| **Implementera** | Du kan starta din lösning med en DTDL-Ontology med öppen källkod som har byggts på de mest tillämpade bransch standarderna. Du kan antingen använda de här modell uppsättningarna i rutan eller utöka dem med dina egna tillägg för en anpassad lösning. | [*Koncept: &nbsp; anta &nbsp; bransch &nbsp; standard Ontologies*](concepts-ontologies-adopt.md)<br><br>[*Koncept: &nbsp; Utöka &nbsp; Ontologies*](concepts-ontologies-extend.md) |
| **Omvandla** | Om du redan har befintliga modeller som representeras i ett annat standardformat kan du konvertera dem till DTDL för att använda dem med Azure Digital-enheter. | [*Begrepp: &nbsp; konvertera &nbsp; Ontologies*](concepts-ontologies-convert.md)<br><br>[*Koncept: &nbsp; Utöka &nbsp; Ontologies*](concepts-ontologies-extend.md) |
| **Författare** | Du kan alltid utveckla egna anpassade DTDL-modeller från grunden med hjälp av tillämpliga bransch standarder som inspiration. | [*Koncept: DTDL-modeller*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Använda Ontology-strategier i en modell utvecklings väg

Oavsett vilken strategi du väljer för att integrera en Ontology i Azure Digitals, kan du följa den fullständiga sökvägen nedan för att få hjälp med att skapa och ladda upp dina Ontology som DTDL-modeller.

1. Börja med att granska och förstå [DTDL-modellering i Azure Digitals](concepts-models.md).
1. Fortsätt med din valda Ontology-integrerings strategi från ovan: [**anta**](concepts-ontologies-adopt.md), [**konvertera**](concepts-ontologies-convert.md)eller [**Redigera**](concepts-models.md) dina modeller baserat på din Ontology.
    1. Om det behövs kan du [utöka](concepts-ontologies-extend.md) din Ontology för att anpassa den efter dina behov.
1. [Verifiera](how-to-parse-models.md) dina modeller för att verifiera att de arbetar DTDL-dokument.
1. Ladda upp dina färdiga modeller till Azure Digitals-enheter med hjälp av [API: er](how-to-manage-model.md#upload-models) eller ett exempel som [Azures digitala dubbla modell](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader)för överföring av modeller.

Därefter bör du kunna använda dina modeller i din Azure Digital-instansen. 

Du kan visualisera dem med exempel som [Azure Digitals flätade Utforskare](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) eller [Azure Digitals modell visualiserare](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer), eller gå vidare till att använda dem för att skapa [digitala dubbla](concepts-twins-graph.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om strategierna för att införa, konvertera och redigera Ontologies:
* [*Koncept: implementera bransch standard Ontologies*](concepts-ontologies-adopt.md)
* [*Begrepp: konvertera Ontologies*](concepts-ontologies-convert.md)
* [*Så här gör du: hantera DTDL-modeller*](how-to-manage-model.md)

Eller Lär dig mer om hur modeller används för att skapa digitala dubbla: [*begrepp: digitala dubbla och dubbla grafer*](concepts-twins-graph.md).