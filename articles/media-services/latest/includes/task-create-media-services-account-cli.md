---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88602448"
---
<!--Create a media services account -->

Följande Azure CLI-kommando skapar ett nytt Media Services-konto. Du kan ersätta följande värden: `amsaccount` `storageaccountforams` (måste matcha det värde du angav för ditt lagrings konto) och `amsResourceGroup` (måste matcha det värde som du har gett för resurs gruppen).  

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```