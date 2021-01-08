---
title: Användardefinierade funktioner i Azure Stream Analytics
description: Den här artikeln är en översikt över användardefinierade funktioner i Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: c671d3989fa46fa7546ba042b9132e19d80265a1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020510"
---
# <a name="user-defined-functions-in-azure-stream-analytics"></a>Användardefinierade funktioner i Azure Stream Analytics

SQL-liknande frågespråk i Azure Stream Analytics gör det enkelt att implementera analys logiken i real tid på strömmande data. Stream Analytics ger ytterligare flexibilitet genom anpassade funktioner som anropas i din fråga. Följande kod exempel är en UDF `sampleFunction` som accepterar en parameter, varje inmatnings post som jobbet tar emot och resultatet skrivs till utdata som `sampleResult` .

```sql
SELECT 
    UDF.sampleFunction(InputStream) AS sampleResult 
INTO 
    output 
FROM 
    InputStream 
```

## <a name="types-of-functions"></a>Typer av funktioner

Azure Stream Analytics stöder följande fyra funktions typer: 

* Användardefinierade JavaScript-funktioner 
* Användardefinierade JavaScript-mängder 
* Användardefinierade C#-funktioner (med Visual Studio) 
* Azure Machine Learning 

Du kan använda dessa funktioner för scenarier som real tids poängsättning med hjälp av maskin inlärnings modeller, sträng manipulationer, komplexa matematiska beräkningar, kodning och avkodning av data. 

## <a name="limitations"></a>Begränsningar

Användardefinierade funktioner är tillstånds lösa och returvärdet kan bara vara ett skalärt värde. Det går inte att anropa externa REST-slutpunkter från dessa användardefinierade funktioner, eftersom det förmodligen påverkar jobbets prestanda. 

Azure Stream Analytics har ingen post för alla funktioner-anrop och returnerade resultat. För att garantera repeterbarhet – till exempel att köra jobbet igen från äldre tidsstämpel ger samma resultat igen – Använd inte funktioner som `Date.GetData()` eller `Math.random()` , eftersom dessa funktioner inte returnerar samma resultat för varje anrop.  

## <a name="resource-logs"></a>Resursloggar

Eventuella körnings fel betraktas som allvarliga och exponeras genom aktivitets-och resurs loggar. Vi rekommenderar att din funktion hanterar alla undantag och fel och returnerar ett giltigt resultat till din fråga. Detta förhindrar att jobbet kommer till ett [felaktigt tillstånd](job-states.md).  

## <a name="exception-handling"></a>Undantagshantering

Eventuella undantag under data bearbetningen betraktas som ett oåterkalleligt fel när data förbrukas i Azure Stream Analytics. Användardefinierade funktioner har högre potential för att utlösa undantag och göra att bearbetningen stoppas. Undvik det här problemet genom att använda ett *try-catch-* block i Java Script eller C# för att fånga undantag vid kod körning. Undantag som fångas kan loggas och behandlas utan att orsaka systemfel. Du uppmanas att alltid figursätta din anpassade kod i ett *try-catch-* block för att undvika att oväntade undantag från bearbetnings motorn utlöses.

## <a name="next-steps"></a>Nästa steg

* [Användardefinierade JavaScript-funktioner i Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Azure Stream Analytics användardefinierade JavaScript-mängder](stream-analytics-javascript-user-defined-aggregates.md)
* [Utveckla .NET standard-användardefinierade funktioner för Azure Stream Analytics jobb](stream-analytics-edge-csharp-udf-methods.md)
* [Integrera Azure Stream Analytics med Azure Machine Learning](machine-learning-udf.md)
