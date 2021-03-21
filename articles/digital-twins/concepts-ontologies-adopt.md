---
title: Införa bransch standard Ontologies
titleSuffix: Azure Digital Twins
description: Lär dig mer om befintliga bransch Ontologies som kan användas för Azures digitala dubbla
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124235"
---
# <a name="adopting-an-industry-ontology"></a>Anta en bransch Ontology

Eftersom det kan vara lättare att komma igång med en DTDL-Ontology med öppen källkod än att börja från en tom sida, samarbetar Microsoft med domän experter för att publicera Ontologies, som representerar allmänt vedertagna bransch konventioner och stöd för en mängd olika kund användnings fall. 

Resultatet är en uppsättning DTDL-baserade Ontologies med öppen källkod som lär sig från, bygga på, lära sig från eller direkt använda bransch standarder. Ontologies är utformade för att möta behoven hos underordnade utvecklare, och det är möjligt att både och/eller utökas av branschen.

För närvarande har Microsoft arbetat med partner för att utveckla en Ontology för [smarta byggnader](#realestatecore-smart-building-ontology) och en Ontology för [smarta städer](#smart-cities-ontology)som ger en gemensam grund för modellering baserat på standarder i dessa branscher för att undvika återskapande. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore Smart Building Ontology

*Hitta Ontology här: [**Digitals Definition Language-baserade RealEstateCore-Ontology för smarta byggnader**](https://github.com/Azure/opendigitaltwins-building)*.

Microsoft samarbetar med [RealEstateCore](https://www.realestatecore.io/), ett svenskt konsortium av fastighets ägare, program varu leverantörer och forsknings institutioner, för att leverera denna DTDL-Ontology med öppen källkod för fastighets branschen.

Med den här Smart-Ontology får du ett gemensamt underlag för att utforma smarta byggnader med bransch standarder (som [Brick schema](https://brickschema.org/ontology/) eller [W3C-Ontology](https://w3c-lbd-cg.github.io/bot/index.html)) för att undvika återuppbyggnad. Ontology innehåller också metod tips för hur du kan använda och utöka dem på rätt sätt. 

Om du vill veta mer om ontologys struktur och modellerings konventioner, hur du använder den, hur du utökar den och hur du bidrar till det, kan du gå till Ontology-lagringsplatsen på GitHub: [Azure/opendigitaltwins-Building](https://github.com/Azure/opendigitaltwins-building). 

Du kan också läsa mer om partnerskapet med RealEstateCore och mål för det här initiativet i det här blogg inlägget och tillhör ande video: [RealEstateCore, en smart Building-Ontology för digitala dubbla, är nu tillgänglig](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Smarta städer Ontology

*Hitta Ontology här: [**DTDL-Ontology (Digital Definition Language) för smarta städer**](https://github.com/Azure/opendigitaltwins-smartcities)*.

Microsoft har samarbetat med [Öppna Agile Smart städer (OASC)](https://oascities.org/) och [Sirus](https://sirus.be/) för att tillhandahålla en DTDL-baserad Ontology för smarta städer som börjar med [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim). Förutom ETSI NGSI-LD har vi också utvärderat Saref4City, CityGML, ISO och andra.

Den aktuella versionen av Ontology fokuserar på en inledande uppsättning modeller. Ontology-författarna är välkommen att bidra till att utöka den första uppsättningen användnings fall, samt att förbättra befintliga modeller. 

Om du vill veta mer om Ontology, hur du använder det och hur du bidrar till det går du till Ontology-lagringsplatsen på GitHub: [Azure/opendigitaltwins-SMARTCITIES](https://github.com/Azure/opendigitaltwins-smartcities). 

Du kan också läsa mer om partnerskap och tillvägagångs sätt för smarta städer i det här blogg inlägget och medföljande video: [smarta städer Ontology för digitala dubbla](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att utöka bransch standard Ontologies för att uppfylla dina specifikationer: [*begrepp: utöka bransch Ontologies*](concepts-ontologies-extend.md).

* Eller också kan du fortsätta med sökvägen för att utveckla modeller som baseras på Ontologies: [*använda Ontology-strategier i en modell utvecklings väg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).