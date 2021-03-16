---
ms.service: hpc-cache
ms.topic: include
ms.date: 03/15/2021
author: ekpgh
ms.author: v-erkel
ms.openlocfilehash: 2804b3bb36a31c9cfbf4cce8db94b7469c8975c7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563251"
---
| Användnings modell | Cacheläge | Verifiering på Server Sidan | Maximal Skriv åtgärds fördröjning |
|--|--|--|--|
| Läs tung, sällan skrivna skrivningar | Läs | Aldrig | Inget |
| Större än 15% skrivningar | Läsning/skrivning | 8 timmar | 20 minuter |
| Klienterna kringgår cachen | Läs | 30 sekunder | Inget |
| Större än 15% skrivningar, frekvent kontroll av Server delen (30 sekunder) | Läsning/skrivning | 30 sekunder | 20 minuter |
| Större än 15% skrivningar, frekvent kontroll av Server delen (60 sekunder) | Läsning/skrivning | 60 sekunder | 20 minuter |
| Större än 15% skrivningar, frekvent skrivning | Läsning/skrivning | 30 sekunder | 30 sekunder |
| Läs tung, kontrol lera servern var 3: e timme | Läs | 3 timmar | Inget |
