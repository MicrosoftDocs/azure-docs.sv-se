---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "78942061"
---
I följande tabell beskrivs kapacitets-, skalbarhets-och prestanda mål för Table Storage.

| Resurs | Mål |
|----------|---------------|
| Antal tabeller i ett Azure Storage-konto | Begränsas bara av lagrings kontots kapacitet |
| Antal partitioner i en tabell | Begränsas bara av lagrings kontots kapacitet |
| Antal entiteter i en partition | Begränsas bara av lagrings kontots kapacitet |
| Maximal storlek för en enskild tabell | 500 TiB |
| Maximal storlek för en enskild entitet, inklusive alla egenskaps värden | 1 MiB |
| Maximalt antal egenskaper i en tabell enhet | 255 (inklusive de tre system egenskaperna, **PartitionKey**, **RowKey** och **timestamp**) |
| Maximal total storlek för en enskild egenskap i en entitet | Varierar beroende på egenskaps typ. Mer information finns i **egenskaps typer** i [förstå tabell tjänstens data modell](/rest/api/storageservices/understanding-the-table-service-data-model). |
| Storlek på **PartitionKey** | En sträng med upp till 1 KiB storlek |
| Storlek på **RowKey** | En sträng med upp till 1 KiB storlek |
| Storlek på en enhets grupps transaktion | En transaktion kan innehålla högst 100 entiteter och nytto lasten måste vara mindre än 4 MiB i storlek. En enhets grupp transaktion kan bara omfatta en uppdatering av en entitet en gång. |
| Maximalt antal lagrade åtkomst principer per tabell | 5 |
| Högsta begär ande frekvens per lagrings konto | 20 000 transaktioner per sekund, vilket förutsätter en 1-KiB enhets storlek |
| Mål data flöde för en enskild Table-partition (1 KiB-entiteter) | Upp till 2 000 enheter per sekund |