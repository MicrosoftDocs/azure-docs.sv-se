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
ms.openlocfilehash: 6fe958c9300d6a6feeb6926f84dbecf565dfafc1
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954352"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-exempel: återställa kontoautentiseringsuppgifter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du kan återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services-konto](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

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
