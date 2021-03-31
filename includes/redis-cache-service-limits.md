---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "71839233"
---
| Resurs | Gräns |
| --- | --- |
| Cachestorlek |1,2 TB |
| Databaser |64 |
| Maximalt antal anslutna klienter |40 000 |
| Azure cache för Redis-repliker för hög tillgänglighet |1 |
| Shards i en Premium-cache med klustring |10 |

Azure cache för Redis-gränser och storlekar skiljer sig åt för varje pris nivå. Om du vill se pris nivåerna och deras associerade storlekar, se [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/).

Mer information om Azure cache för konfigurations gränser för Redis finns i [standard konfiguration av Redis-servern](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Eftersom konfiguration och hantering av Azure cache för Redis-instanser utförs av Microsoft, stöds inte alla Redis-kommandon i Azure cache för Redis. Mer information finns i [Redis-kommandon som inte stöds i Azure cache för Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

