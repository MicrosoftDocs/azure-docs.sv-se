---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 26ae372b6e431247d2038445daafcb3c997a232d
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512742"
---
<!--Create a media services account -->

Följande Azure CLI-kommando skapar ett nytt Media Services-konto. Du kan ersätta följande värden: (måste matcha det värde som du gav för ditt lagringskonto) och (måste matcha det värde `amsaccount` `storageaccountforams` som du gav för `amsResourceGroup` resursgruppen).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```
