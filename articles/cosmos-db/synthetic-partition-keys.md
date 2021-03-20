---
title: Skapa en syntetisk partitionsnyckel i Azure Cosmos DB
description: Lär dig hur du använder syntetiska partitionsuppsättningar i dina Azure Cosmos-behållare för att distribuera data och arbets belastning jämnt över partitionernas nycklar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 6b8bc44f1ba5624c37620205aaa574e618ef395f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340661"
---
# <a name="create-a-synthetic-partition-key"></a>Skapa en syntetisk partitionsnyckel
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Vi rekommenderar att du har en partitionsnyckel med många distinkta värden, till exempel hundratals eller tusentals. Målet är att distribuera data och arbets belastning jämnt mellan objekten som är kopplade till dessa nyckel värden. Om sådan egenskap inte finns i dina data, kan du skapa en *syntetisk partitionsnyckel*. I det här dokumentet beskrivs flera grundläggande metoder för att skapa en syntetisk partitionsnyckel för Cosmos-behållaren.

## <a name="concatenate-multiple-properties-of-an-item"></a>Sammanfoga flera egenskaper för ett objekt

Du kan skapa en partitionsnyckel genom att sammanfoga flera egenskaps värden till en enda artificiell `partitionKey` egenskap. Dessa nycklar kallas för syntetiska nycklar. Anta till exempel följande exempel dokument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

I föregående dokument är ett alternativ att ange/deviceId eller/date som partitionsnyckel. Använd det här alternativet om du vill partitionera din behållare baserat på antingen enhets-ID eller datum. Ett annat alternativ är att sammanfoga dessa två värden till en syntetisk `partitionKey` egenskap som används som partitionsnyckel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

I real tids scenarier kan du ha tusentals objekt i en databas. I stället för att lägga till den syntetiska nyckeln manuellt definierar du logik på klient sidan för att sammanfoga värden och infoga den syntetiska nyckeln i objekten i dina Cosmos-behållare.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Använda en partitionsnyckel med ett slumpmässigt suffix

En annan möjlig strategi att distribuera arbets belastningen jämnt är att lägga till ett slumpmässigt nummer i slutet av värdet för partitionsnyckel. När du distribuerar objekt på det här sättet kan du utföra parallella Skriv åtgärder mellan partitioner.

Ett exempel är om en partitionsnyckel representerar ett datum. Du kan välja ett slumpmässigt nummer mellan 1 och 400 och sammanfoga det som ett suffix till datumet. Den här metoden resulterar i nyckel värden som  `2018-08-09.1` , `2018-08-09.2` och så vidare, via  `2018-08-09.400` . Eftersom du slumpar partitionsnyckel sprids Skriv åtgärderna på behållaren på varje dag jämnt över flera partitioner. Den här metoden resulterar i bättre parallellitet och övergripande data flöde.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Använd en partitionsnyckel med förberäknade suffix 

Strategin för slumpmässigt suffix kan avsevärt förbättra Skriv data flödet, men det är svårt att läsa ett speciellt objekt. Du vet inte vilket suffix-värde som användes när du skrev objektet. För att göra det lättare att läsa enskilda objekt använder du strategin för förberäknade suffix. I stället för att använda ett slumpmässigt nummer för att distribuera objekten mellan partitionerna, använder du ett tal som beräknas baserat på något som du vill fråga.

Överväg det tidigare exemplet där en behållare använder ett datum som partitionsnyckel. Anta nu att varje objekt har ett  `Vehicle-Identification-Number` ( `VIN` )-attribut som vi vill komma åt. Dessutom antar vi att du ofta kör frågor för att hitta objekt efter `VIN` , förutom datum. Innan ditt program skriver objektet till behållaren kan det beräkna ett hash-suffix baserat på VINet och lägga till det i partitionens nyckel datum. Beräkningen kan generera ett tal mellan 1 och 400 som är jämnt distribuerat. Det här resultatet liknar de resultat som genereras av strategi metoden för slumpmässigt suffix. Värdet för partitionsnyckel är sedan det datum som kombineras med det beräknade resultatet.

Med den här strategin sprids skrivningarna jämnt över nyckel värden och över partitionerna. Du kan enkelt läsa ett visst objekt och datum, eftersom du kan beräkna partitionens nyckel värde för en specifik `Vehicle-Identification-Number` . Fördelen med den här metoden är att du kan undvika att skapa en enskild snabb partitionsnyckel, d.v.s. en partitionsnyckel som tar all arbets belastning. 

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om partitionerings konceptet i följande artiklar:

* Läs mer om [logiska partitioner](partitioning-overview.md).
* Lär dig mer om hur du [etablerar data flöde i Azure Cosmos-behållare och databaser](set-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
