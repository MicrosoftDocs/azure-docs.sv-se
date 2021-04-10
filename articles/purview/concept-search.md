---
title: Förstå Sök funktioner i Azure avdelningens kontroll (för hands version)
description: Den här artikeln förklarar hur Azure avdelningens kontroll aktiverar data identifiering via Sök funktioner.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96553969"
---
# <a name="understand-search-features-in-azure-purview"></a>Förstå Sök funktioner i Azure avdelningens kontroll

Den här artikeln innehåller en översikt över Sök funktionen i Azure avdelningens kontroll. Sök är en kärn plattforms funktion i avdelningens kontroll, som ger data identifiering och data användning styrnings upplevelser i en organisation.

## <a name="search"></a>Sök

Avdelningens kontroll Sök upplevelsen drivs av ett hanterat sökindex. När en data källa har registrerats med avdelningens kontroll indexeras dess metadata av Sök tjänsten för att möjliggöra enkel identifiering. Indexet tillhandahåller Sök funktioner och slutför Sök begär Anden genom att fråga miljon tals metadata till gångar. Genom att söka kan du identifiera, förstå och använda data för att få ut det mesta av det.

Sök funktionen i avdelningens kontroll är en process i tre steg:

1. I sökrutan visas historiken som innehåller nyligen använda nyckelord och till gångar.
1. När du börjar skriva tecknen föreslår sökningen de matchande nyckelorden och till gångarna. 
1. Sidan Sök resultat visas med till gångar som matchar det angivna nyckelordet.

## <a name="reduce-the-time-to-discover-data"></a>Minska tiden för att identifiera data

Data identifiering är det första steget i arbets belastningen data analys eller data styrning för data konsumenter. Data identifiering kan ta lång tid, eftersom du kanske inte vet var du hittar de data som du vill ha. Trots att du har hittat data kan du vara osäker på om du kan lita på data och ta en beroende information. 

Målet med sökning i Azure avdelningens kontroll är att påskynda processen med data identifiering genom att tillhandahålla gester för att snabbt hitta de data som är viktiga för dig.

## <a name="recent-search-and-suggestions"></a>Senaste sökning och förslag

Många gånger kanske du arbetar på flera projekt samtidigt. För att göra det enklare att återuppta tidigare projekt ger avdelningens kontroll-sökningen möjlighet att se de senaste Sök nyckelorden och förslag. Du kan också hantera den senaste Sök historiken genom att välja **Visa alla** i list rutan Sök.

## <a name="filters"></a>Filter

Filter (kallas även *FACET*) är utformade för att komplettera sökningen. Filter visas på sidan Sök resultat. Filtren på Sök Resultat sidan inkluderar klassificering, känslighets etikett, data källa och ägare. Användare kan välja vissa värden i ett filter för att bara se matchande data till gångar och begränsa Sök resultatet till matchande till gångar.

## <a name="hit-highlighting"></a>Träffmarkering

Matchande nyckelord på sidan Sök Resultat markeras för att göra det enklare att identifiera varför en data till gång returnerades av sökningen. Nyckelords matchningen kan förekomma i flera fält, till exempel namn, beskrivning och ägare till data till gångar.

Det kan vara uppenbart varför en data till gång ingår i sökningen, även om träff markering har Aktiver ATS. Alla egenskaper genomsöks som standard. Därför kan en data till gång returneras på grund av en matchning på en egenskap på en kolumn nivå. Eftersom flera användare kan kommentera data till gångarna med sina egna klassificeringar och beskrivningar, visas inte alla metadata i listan med Sök resultat.

## <a name="sort"></a>Sortera

Användare har två alternativ för att sortera Sök resultaten. De kan sortera efter namnet på till gången eller som standard Sök relevans.

## <a name="search-relevance"></a>Sök efter relevans

Relevans är standard sorterings ordningen på sidan Sök resultat. Sök relevans hittar dokument som innehåller nyckelordet search (vissa eller alla). Till gångar som innehåller många instanser av nyckelordet search rangordnas högre. Dessutom är anpassade bedömnings metoder konstanter justerade för att förbättra Sök relevansen.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Azure avdelningens kontroll-konto i Azure Portal](create-catalog-portal.md)
* [Snabb start: skapa ett Azure avdelningens kontroll-konto med Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Snabb start: Använd avdelningens kontroll Studio](use-purview-studio.md)
