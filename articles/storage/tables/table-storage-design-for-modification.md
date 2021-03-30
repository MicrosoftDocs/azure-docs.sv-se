---
title: Designa Azure Table Storage för data ändring | Microsoft Docs
description: Design tabeller för data ändring i Azure Table Storage. Optimera åtgärderna för att infoga, uppdatera och ta bort. Se till att dina lagrade entiteter är konsekventa.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236768"
---
# <a name="design-for-data-modification"></a>Utforma för dataändring
Den här artikeln fokuserar på design överväganden för att optimera infogningar, uppdateringar och borttagningar. I vissa fall måste du utvärdera de olika design modeller som optimeras för frågor mot designer som optimerar för data ändringar precis som du gör i design för Relations databaser (även om teknikerna för att hantera design kommersiella är olika i en Relations databas). Design mönstren för avsnitts tabeller beskriver några detaljerade design mönster för Table service och markerar vissa av dessa kompromisser. I praktiken kommer du att se att många design som är optimerade för att fråga entiteter också fungerar bra för att ändra entiteter.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Optimera prestanda för INSERT-, Update-och Delete-åtgärder
Om du vill uppdatera eller ta bort en entitet måste du kunna identifiera den med hjälp av värdena **PartitionKey** och **RowKey** . I detta hänseende bör ditt val av **PartitionKey** och **RowKey** för att ändra entiteter följa liknande kriterier som du väljer för att kunna använda punkt frågor eftersom du vill identifiera entiteter så effektivt som möjligt. Du vill inte använda en ineffektiv partition eller tabells ökning för att hitta en entitet för att identifiera de **PartitionKey** -och **RowKey** -värden som du behöver uppdatera eller ta bort.  

Följande mönster i avsnittets design mönster adress optimerar prestandan eller åtgärderna för att infoga, uppdatera och ta bort:  

* [Mönster för hög volym borttagning](table-storage-design-patterns.md#high-volume-delete-pattern) – aktivera borttagning av en stor mängd entiteter genom att lagra alla entiteter för samtidig borttagning i en egen separat tabell. du tar bort entiteterna genom att ta bort tabellen.  
* [Data serie mönster](table-storage-design-patterns.md#data-series-pattern) – lagra kompletta data serier i en enda entitet för att minimera antalet begär Anden som du gör.  
* [Mönster för breda entiteter](table-storage-design-patterns.md#wide-entities-pattern) – Använd flera fysiska entiteter för att lagra logiska entiteter med fler än 252 egenskaper.  
* [Mönster för stora entiteter](table-storage-design-patterns.md#large-entities-pattern) – Använd Blob Storage för att lagra stora egenskaps värden.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Säkerställa konsekvens i dina lagrade entiteter
Den andra viktiga faktorn som påverkar ditt val av nycklar för optimering av data ändringar är hur man säkerställer konsekvens genom att använda atomiska transaktioner. Du kan bara använda en avhjälpning för att hantera entiteter som lagras i samma partition.  

Följande mönster i artikel [tabell design mönster](table-storage-design-patterns.md) adress hanterar konsekvens:  

* [Sekundärt index mönster för sekundär partition](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika **RowKey** -värden (i samma partition) för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* [Sekundärt index mönster mellan partitioner](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) – lagra flera kopior av varje entitet med olika RowKey-värden i separata partitioner eller i separata tabeller för att möjliggöra snabba och effektiva sökningar och alternativa sorterings ordningar genom att använda olika **RowKey** -värden.  
* Till [sist konsekventa transaktions mönster](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) – aktivera konsekvent beteende för partitionerings gränser eller lagrings system gränser med hjälp av Azure-köer.
* [Mönster för index entiteter](table-storage-design-patterns.md#index-entities-pattern) – underhålla index enheter för att möjliggöra effektiva sökningar som returnerar listor med entiteter.  
* [Utnormaliserings mönster](table-storage-design-patterns.md#denormalization-pattern) – kombinera relaterade data tillsammans i en enda entitet så att du kan hämta alla data du behöver med en enda punkt fråga.  
* [Data serie mönster](table-storage-design-patterns.md#data-series-pattern) – lagra kompletta data serier i en enda entitet för att minimera antalet begär Anden som du gör.  

Information om enhets grupps transaktioner finns i avsnittet [enhets grupp transaktioner](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Se till att designen för effektiva ändringar underlättar effektiva frågor
I många fall kan en design för effektiv fråga resultera i effektiva ändringar, men du bör alltid utvärdera om detta är fallet för ditt speciella scenario. Några av mönstren i artikel [tabell design mönster](table-storage-design-patterns.md) utvärderar explicit kompromisser mellan frågor och ändring av entiteter, och du bör alltid ta hänsyn till antalet av varje typ av åtgärd.  

Följande mönster i artikel [tabell design mönster](table-storage-design-patterns.md) riktar sig mot att utforma för effektiva frågor och design för effektiv data ändring:  

* [Sammansatt nyckel mönster](table-storage-design-patterns.md#compound-key-pattern) – Använd sammansatta **RowKey** -värden för att göra det möjligt för en klient att söka efter relaterade data med en enda punkt fråga.  
* [Logg änden-mönster](table-storage-design-patterns.md#log-tail-pattern) – hämta de *n* entiteter som senast har lagts till i en partition med hjälp av ett **RowKey** -värde som sorterar efter omvänt datum-och tids ordning.  
