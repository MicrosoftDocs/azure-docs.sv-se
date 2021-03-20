---
title: ta med fil
description: ta med fil
services: search
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/11/2020
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: fe5c8129434ddb4eec2dd25a3f123f28b4db221b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83682636"
---
En Sök tjänst begränsas av disk utrymme eller av en hård gräns på det maximala antalet index eller indexerare, beroende på vilket som kommer först. I följande tabell dokumenteras lagrings gränser. För maximala objekt gränser, se [gränser per resurs](../articles/search/search-limits-quotas-capacity.md#index-limits).

| Resurs | Kostnadsfri | Basic<sup>1</sup> | S1 | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| -------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Service nivå avtal (SLA)<sup>2</sup>  |Inga |Ja |Ja |Ja |Ja |Ja |Ja |Ja |
| Lagringsutrymme per partition |50 MB |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Partitioner per tjänst |Ej tillämpligt |1 |12 |12 |12 |3 |12 |12 |
| Partitionsstorlek |Ej tillämpligt |2 GB |25 GB |100 GB |200 GB |200 GB |1 TB |2 TB |
| Repliker |Ej tillämpligt |3 |12 |12 |12 |12 |12 |12 |

<sup>1</sup> Basic har en fast partition. Ytterligare Sök enheter kan användas för att lägga till repliker för större fråga-volymer.

<sup>2</sup> service nivå avtal gäller för fakturerbara tjänster på dedikerade resurser. Kostnads fria tjänster och för hands versions funktioner har inget service avtal. För fakturerbara tjänster börjar service avtal att gälla när du tillhandahåller tillräckligt med redundans för din tjänst. Två eller fler repliker krävs för frågan (Läs) service avtal. Tre eller flera repliker krävs för fråge-och indexerings-service avtal (Läs-och Skriv behörighet). Antalet partitioner är inte ett SLA-avtal. 