---
title: Hantera Azure cache för Redis med Azure CLI
description: 'Azure CLI-exempel för hantering av Azure cache för Redis: skapa en cache, ta bort en cache, Hämta cache-information, värdnamn, portar och nycklar, och Anslut en webbapp.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 32530982bc2a9d1b5deb31b3bc71460462352258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92536427"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Hantera Azure cache för Redis med Azure CLI

Följande tabell innehåller länkar till bash-skript som skapats med hjälp av Azure CLI.

| Skapa ett cacheminne | Beskrivning |
| ------------ | ----------- |
| [Skapa en cache](./scripts/create-cache.md) | Skapar en resurs grupp och en Basic-nivå i Azure cache för Redis. |
| [Skapa en Premium-cache med klustring](./scripts/create-premium-cache-cluster.md) | Skapar en resurs grupp och en cache på Premium-nivå med klustrad aktive rad.|
| [Hämta cache-information](./scripts/show-cache.md) | Hämtar information om en Azure-cache för Redis-instans, inklusive etablerings status. |
| [Hämta värdnamn, portar och nycklar](./scripts/cache-keys-ports.md) | Hämtar värdnamn, portar och nycklar för en Azure-cache för Redis-instansen. |
|**Webbapp plus cache**| **Beskrivning**|
| [Ansluta en webbapp till en Azure Cache for Redis](./../app-service/scripts/cli-connect-to-redis.md) | Skapar en Azure-webbapp och en Azure-cache för Redis och lägger sedan till Redis-anslutningsfel i appinställningar. |
|**Ta bort cache**| **Beskrivning** |
| [Ta bort en cache](./scripts/delete-cache.md) | Tar bort en Azure-cache för Redis-instans  |

Mer information om Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [komma igång med Azure CLI](/cli/azure/get-started-with-azure-cli).