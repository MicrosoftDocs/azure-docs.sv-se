---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755447"
---
1. Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

   ```azurecli
   az account list --all
   ```
3. Ange den prenumeration som du vill använda.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
