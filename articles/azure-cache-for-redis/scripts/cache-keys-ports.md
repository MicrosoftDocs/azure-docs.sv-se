---
title: Hämta värdnamn, portar, nycklar – Azure cache för Redis – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du hämtar värdnamn, portar och nycklar för en Azure-cache för Redis-instansen.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184235"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Hämta värdnamn, portar och nycklar för Azure cache för Redis

I det här scenariot får du lära dig hur du hämtar värdnamn, portar och nycklar som används för att ansluta till en Azure-cache för Redis-instansen.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att hämta värdnamn, nycklar och portar för en Azure-cache för Redis-instans. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [AZ Redis show](/cli/azure/redis) | Hämta information om en Azure-cache för Redis-instansen. |
| [AZ Redis-lista – nycklar](/cli/azure/redis) | Hämta åtkomst nycklar för en Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).