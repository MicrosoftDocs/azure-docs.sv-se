---
title: Hämta hanterade resurs grupper & ändra storlek på virtuella datorer – Azure CLI
description: Innehåller exempel skript för Azure CLI som hämtar en hanterad resurs grupp i ett Azure-hanterat program. Skriptet ändrar storlek på virtuella datorer.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 711b516d1ba1154e574b0d8bbd8d86a02d7df018
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497825"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Hämta resurser från en hanterad resursgrupp och ändra storleken på virtuella datorer med Azure CLI

Detta skript hämtar resurser från en hanterad resursgrupp och ändrar storlek på de virtuella datorerna i resursgruppen.


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att distribuera det hanterade programmet. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az managedapp list](/cli/azure/managedapp#az-managedapp-list) | Visar en lista med hanterade program. Ange frågevärden om du vill förfina resultatvisningen. |
| [az resource list](/cli/azure/resource#az-resource-list) | Visar resurserna. Ange en resursgrupp och frågevärden om du vill förfina resultatvisningen. |
| [az vm resize](/cli/azure/vm#az-vm-resize) | Uppdatera storleken på en virtuell dator. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Azure Managed Application overview](../overview.md) (Översikt över Azure Managed Application).
* Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
