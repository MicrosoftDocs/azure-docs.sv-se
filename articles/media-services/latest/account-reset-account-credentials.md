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
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783601"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-exempel: Återställa autentiseringsuppgifterna för kontot

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure CLI-skriptet i den här artikeln visar hur du kan återställa autentiseringsuppgifterna för ditt konto och app.config-inställningarna.

## <a name="prerequisites"></a>Förutsättningar

[Skapa ett Media Services konto](./create-account-howto.md).

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

* [az ams](/cli/azure/ams)
* [Återställ autentiseringsuppgifter](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
