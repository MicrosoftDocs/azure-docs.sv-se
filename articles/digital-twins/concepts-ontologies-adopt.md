---
title: Införa bransch standard Ontologies
titleSuffix: Azure Digital Twins
description: Lär dig mer om befintliga bransch Ontologies som kan användas för Azures digitala dubbla
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561803"
---
# <a name="adopting-an-industry-ontology"></a>Anta en bransch Ontology

Eftersom det kan vara lättare att komma igång med en DTDL-Ontology med öppen källkod än att börja från en tom sida, samarbetar Microsoft med domän experter för att publicera Ontologies, som representerar allmänt vedertagna bransch konventioner och stöd för en mängd olika kund användnings fall. 

Resultatet är en uppsättning DTDL-baserade Ontologies med öppen källkod som lär sig från, bygga på, lära sig från eller direkt använda bransch standarder. Ontologies är utformade för att möta behoven hos underordnade utvecklare, och det är möjligt att både och/eller utökas av branschen.

För närvarande har Microsoft arbetat med fastighets partner att utveckla en Ontology för smarta byggnader, vilket ger ett gemensamt underlag för att utforma smarta byggnader baserade på bransch standarder för att undvika återskapande. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore Smart Building Ontology

Microsoft samarbetar med [RealEstateCore](https://www.realestatecore.io/), ett svenskt konsortium av fastighets ägare, program varu leverantörer och forsknings institutioner, för att leverera en DTDL-Ontology med öppen källkod för fastighets branschen: [**DTDL-baserade RealEstateCore Ontology för smarta byggnader**](https://github.com/Azure/opendigitaltwins-building).

Med den här Smart-Ontology får du ett gemensamt underlag för att utforma smarta byggnader med bransch standarder (som [Brick schema](https://brickschema.org/ontology/) eller [W3C-Ontology](https://w3c-lbd-cg.github.io/bot/index.html)) för att undvika återuppbyggnad. Ontology innehåller också metod tips för hur du kan använda och utöka dem på rätt sätt. 

Om du vill veta mer om ontologys struktur och modellerings konventioner, hur du använder den, hur du utökar den och hur du bidrar till det går du till Ontology- [lagringsplatsen på GitHub](https://github.com/Azure/opendigitaltwins-building). 

Du kan också läsa mer om partnerskapet med RealEstateCore och mål för det här initiativet i det här blogg inlägget och tillhör ande video: [*RealEstateCore, en smart Building-Ontology för digitala dubbla, är nu tillgänglig*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att utöka bransch standard Ontologies för att uppfylla dina specifikationer: [*begrepp: utöka bransch Ontologies*](concepts-ontologies-extend.md).

* Eller också kan du fortsätta med sökvägen för att utveckla modeller som baseras på Ontologies: [*använda Ontology-strategier i en modell utvecklings väg*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).