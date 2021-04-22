---
title: Azure CLI-krav för Azure HPC Cache
description: Installationssteg innan du kan använda Azure CLI för att skapa eller ändra en Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864297"
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
* [Dokumentation om Azure CLI hpc-cache](/cli/azure/hpc-cache)
