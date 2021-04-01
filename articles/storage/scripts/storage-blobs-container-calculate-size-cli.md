---
title: Exempel på Azure CLI-skript – Beräkna blob-containerns storlek | Microsoft Docs
description: Beräkna storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: cli
ms.topic: sample
ms.date: 06/28/2017
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 45712632ebfb2da4b713038503965ce908c1dfc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87498896"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Beräkna storleken på en container för Blob-lagring

Det här skriptet beräknar storleken på en container i Azure Blob-lagring genom att addera storleken på blobarna i containern.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här CLI-skriptet ger en uppskattad storlek för containern och ska inte användas för faktureringsberäkningar.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli[main](../../../cli_scripts/storage/calculate-container-size/calculate-container-size.sh?highlight=2-3 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, containern och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att beräkna storleken på Blob-lagringscontainern. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage blob upload](/cli/azure/storage/account) | Överför lokala filer till en Azure Blob Storage-container. |
| [az storage blob list](/cli/azure/storage/account/keys) | Visar en lista över blobarna i en Azure Blob Storage-container. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för lagring finns i [Azure CLI-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-cli.md).
