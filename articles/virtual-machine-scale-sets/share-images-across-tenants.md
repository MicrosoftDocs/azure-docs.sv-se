---
title: Dela Galleri bilder över klienter
description: Lär dig hur du skapar skalnings uppsättningar med bilder som delas mellan Azure-klienter med hjälp av delade avbildnings gallerier.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b6b5e48997da03c7f180c62a9142c99b05c95b46
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444045"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Dela bilder över klienter med delade avbildnings Galleri

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Skapa en skalningsuppsättning med Azure CLI

Logga in tjänstens huvud namn för klient organisation 1 med hjälp av appID, appens nyckel och ID för klient 1. Du kan använda `az account show --query "tenantId"` för att hämta klient-ID: n vid behov.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Logga in tjänstens huvud namn för klient 2 med hjälp av appID, appens nyckel och ID: t för klient organisationen 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Skapa skalnings uppsättningen. Ersätt informationen i exemplet med din egen.

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Nästa steg

Om du stöter på problem kan du [Felsöka delade avbildnings gallerier](../virtual-machines/troubleshooting-shared-images.md).
