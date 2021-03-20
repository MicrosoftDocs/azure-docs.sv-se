---
title: Skapa en Azure-cache för Redis – Azure CLI
description: Detta exempel på Azure CLI-kod visar hur du skapar en Azure-cache för Redis-instans med kommandot AZ Redis Create.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77a03ac7ca47e3ba40242adfbd5d57d9dbd21f30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184152"
---
# <a name="create-an-azure-cache-for-redis"></a>Skapa en Azure Cache for Redis

I det här scenariot får du lära dig hur du skapar en Azure-cache för Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resurs grupp och en Azure-cache för Redis. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [AZ Redis Create](/cli/azure/redis) | Skapa Azure-cache för Redis-instans. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure-cache för Redis CLI-skript exempel finns i [Azure-cachen för Redis-dokumentationen](../cli-samples.md).