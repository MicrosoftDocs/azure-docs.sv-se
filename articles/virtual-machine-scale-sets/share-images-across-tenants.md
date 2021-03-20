---
title: Dela Galleri bilder över klienter
description: Lär dig hur du skapar skalnings uppsättningar med bilder som delas mellan Azure-klienter med hjälp av delade avbildnings gallerier.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: ea61b3bd76fc4ada48a8a2fb734a841b8a969272
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323489"
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
