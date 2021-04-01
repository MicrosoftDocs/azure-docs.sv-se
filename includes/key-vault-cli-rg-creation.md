---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070273"
---
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
