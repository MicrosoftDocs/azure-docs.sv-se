---
title: Återställa autentiseringsuppgifterna för ditt konto – CLI
description: Använd Azure CLI-skriptet för att återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101091841"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-exempel: återställa kontoautentiseringsuppgifter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du kan återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

## <a name="example-script"></a>Exempelskript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Nästa steg

* [AZ AMS](/cli/azure/ams)
* [Återställ autentiseringsuppgifter](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
