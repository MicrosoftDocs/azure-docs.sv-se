---
title: Hämta information om en Azure-cache för Redis – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du hämtar information om en Azure-cache för Redis-instans, inklusive dess etablerings status.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8901847a1c214b257243c4da0a74897061409bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184254"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Hämta information om en Azure-cache för Redis

I det här scenariot får du lära dig hur du hämtar information om en Azure-cache för Redis-instansen, inklusive dess etablerings status.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att hämta information om en Azure-cache för Redis-instansen. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Hämta information om en Azure-cache för Redis-instansen. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).