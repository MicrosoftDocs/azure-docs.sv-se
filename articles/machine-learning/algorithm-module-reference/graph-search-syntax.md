---
title: Syntax för sökfråga i Graph
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Sök-frågesyntaxen i Azure Machine Learning designer för att söka efter noder i Pipeline-diagram.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420775"
---
# <a name="graph-search-query-syntax"></a>Syntax för sökfråga i Graph

I den här artikeln får du lära dig mer om syntaxen för att söka i grafen i Azure Machine Learning. Med hjälp av diagram Sök funktionen kan du söka efter en nod med hjälp av dess namn och egenskaper. 

 ![Animerad skärm bild som visar ett exempel på en diagram Sök upplevelse](media/search/graph-search.gif)

Graph search stöder nyckelords sökning med full text på nodnamn och kommentarer. Du kan också filtrera på Node-egenskapen som runStatus, duration, computeTarget. Nyckelords sökningen baseras på en Lucene-fråga. En fullständig Sök fråga ser ut så här:  

**[Lucene-fråga | [filter fråga]** 

Du kan använda antingen en Lucene-fråga eller en filter fråga. Använd avgränsaren för att använda båda **|** . Filtrerings frågans syntax är mer strikt än Lucene-frågan. Så om kund indata kan analyseras som båda, kommer filter frågan att tillämpas.

 

## <a name="lucene-query"></a>Lucene-fråga

I Graph search används en enkel texts ökning med hjälp av funktionen för full texts ökning på noden "name" och "comment". Följande Lucene-operatörer stöds:

 
- OCH/ELLER
- Matchar jokertecken med **?** och- **\*** operatörer.

### <a name="examples"></a>Exempel

- Enkel sökning: `JSON Data`

- OCH/ELLER: `JSON AND Validation`

- Flera och/eller: `(JSON AND Validation) OR (TSV AND Training)`

 
- Matchning med jokertecken: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Du kan inte starta en Lucene-fråga med ett "*"-Character.

##  <a name="filter-query"></a>Filter fråga

 
Filter frågor använder följande mönster:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Du kan använda följande Node-egenskaper som nycklar:

- runStatus
- compute
- varaktighet
- åter användning

Och Använd följande operatorer:

- Större än eller lika med: `>=`
- Mindre än eller lika med: `<=`
- Störst `>`
- Minskad `<`
- Skeppningskvantiteten `==`
- Innehåll `=`
- NotEqual: `!=`
- För `in`

 
 

### <a name="example"></a>Exempel

- varaktighet > 100;
- status i {failed, NotStarted}
- Compute i {GPU-Cluster}; runStatus i {completed}

## <a name="technical-notes"></a>Tekniska anteckningar

- Relationen mellan flera filter är "och"
- Om väljs `>=,  >,  <, or  <=` kommer värdena automatiskt att konverteras till Number-typ. Annars används sträng typer för jämförelse.
- För alla sträng typs värden är Skift läges okänsligt i jämförelse.
- Operatorn "i" förväntar sig en samling som värde, samlingens syntax är `{name1, name2, name3}`
- Utrymmet kommer att ignoreras mellan nyckelord