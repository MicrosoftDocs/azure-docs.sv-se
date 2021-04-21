---
title: Krav för Azure CLI för Azure HPC Cache
description: Konfigurera steg innan du kan använda Azure CLI för att skapa eller ändra en Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780503"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Konfigurera Azure CLI för Azure HPC Cache

Följ de här stegen för att förbereda din miljö innan du använder Azure CLI för att skapa eller hantera Azure HPC Cache.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Azure HPC Cache kräver version 2.7 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="set-default-resource-group-optional"></a>Ange standardresursgrupp (valfritt)

De flesta hpc-cache-kommandon kräver att du skickar cachens resursgrupp. Du kan ange standardresursgruppen med hjälp av [az configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Nästa steg

När du har installerat Azure CLI-tillägget och loggat in kan du använda Azure CLI för att skapa och hantera Azure HPC Cache system.

* [Skapa en Azure HPC Cache](hpc-cache-create.md)
* [Dokumentation om Azure CLI hpc-cache](/cli/azure/ext/hpc-cache/hpc-cache)
