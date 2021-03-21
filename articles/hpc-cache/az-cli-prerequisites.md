---
title: Krav för Azure CLI för Azure HPC cache
description: Installations steg innan du kan använda Azure CLI för att skapa eller ändra en Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 13f45c96a830110bd0f4a2d4a2b422921d7a2e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94654464"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurera Azure CLI för Azure HPC Cache

Följ de här stegen för att förbereda din miljö innan du använder Azure CLI för att skapa eller hantera en Azure HPC-cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC-cache kräver version 2,7 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="set-default-resource-group-optional"></a>Ange standard resurs grupp (valfritt)

De flesta av HPC-cache-kommandona kräver att du överför cachens resurs grupp. Du kan ställa in standard resurs gruppen genom att använda [AZ konfigurera](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Nästa steg

När du har installerat Azure CLI-tillägget och loggat in kan du använda Azure CLI för att skapa och hantera Azure HPC cache-system.

* [Skapa en Azure HPC-cache](hpc-cache-create.md)
* [Azure CLI HPC-cache-dokumentation](/cli/azure/ext/hpc-cache/hpc-cache)
