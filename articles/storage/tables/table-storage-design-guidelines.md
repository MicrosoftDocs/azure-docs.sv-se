---
title: Rikt linjer för tabell design för Azure Storage | Microsoft Docs
description: Förstå rikt linjerna för att utforma Azure Storage-tabellen för att stödja Läs-och skriv åtgärder effektivt.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88236326"
---
# <a name="guidelines-for-table-design"></a>Riktlinjer för tabelldesign

Att designa tabeller för användning med Azure Storage Table-tjänsten skiljer sig mycket från design överväganden för en Relations databas. I den här artikeln beskrivs rikt linjerna för att designa din Table service-lösning för att vara mer effektiv och skriv effektiv.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Utforma Table service-lösningen så att den blir Läs effektiv

* ***Design för frågor i Läs-tung-program.*** När du utformar dina tabeller bör du tänka på frågorna (särskilt svars tiden) som du ska köra innan du funderar på hur du kommer att uppdatera dina entiteter. Detta leder vanligt vis till en effektiv och genomförd lösning.  
* ***Ange både PartitionKey och RowKey i dina frågor.** _ _Point frågor * som dessa är de mest effektiva tabell tjänst frågorna.  
* ***Överväg att lagra kopior av entiteter.*** Table Storage är billigt så att du kan lagra samma entitet flera gånger (med olika nycklar) för att möjliggöra mer effektiva frågor.  
* ***Överväg att avnormalisera dina data.*** Table Storage är billigt så att du kan avnormalisera dina data. Lagra till exempel sammanfattnings enheter så att frågor för sammanställd data bara behöver ha åtkomst till en enda entitet.  
* ***Använd sammansatta nyckel värden.** _ De enda nycklar du har är _ *PartitionKey** och **RowKey**. Använd t. ex. värden för sammansatta nycklar för att aktivera alternativa, åtkomst vägar till entiteter.  
* ***Använd frågans projektion.*** Du kan minska mängden data som överförs via nätverket med hjälp av frågor som bara väljer de fält som du behöver.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Utforma Table service-lösningen så att den blir Skriv effektiv  

* ***Skapa inte varma partitioner.*** Välj nycklar som gör att du kan sprida dina begär anden över flera partitioner vid en viss tidpunkt.  
* ***Undvik toppar i trafik.*** Utjämna trafiken under en rimlig tids period och Undvik toppar i trafiken.
* ***Skapa inte nödvändigt vis en separat tabell för varje typ av entitet.*** När du kräver atomiska transaktioner över entitetstyper kan du lagra dessa typer av flera entiteter i samma partition i samma tabell.
* ***Ta hänsyn till det maximala data flöde som du måste uppnå.*** Du måste vara medveten om skalbarhets målen för Table service och se till att designen inte kommer att överskrida dem.  

När du läser den här guiden visas exempel som har alla dessa principer i praktiken. 

## <a name="next-steps"></a>Nästa steg

- [Mönster för tabelldesign](table-storage-design-patterns.md)
- [Utforma för frågor](table-storage-design-for-query.md)
- [Kryptera tabell data](table-storage-design-encrypt-data.md)
- [Utforma för dataändring](table-storage-design-for-modification.md)
