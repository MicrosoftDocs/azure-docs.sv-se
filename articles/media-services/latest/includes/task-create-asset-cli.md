---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88605979"
---
<!--Create a media services asset CLI-->

Följande Azure CLI-kommando skapar en ny Media Services till gång. Ersätt värdena `assetName` `amsAccountName` och `resourceGroup` med värden som du håller på att arbeta med.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
