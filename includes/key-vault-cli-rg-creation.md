---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 97fd969ddae2f2c222209259cccd6f6f55272524
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070273"
---
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Använd kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp med namnet *myResourceGroup* på den *östra* platsen.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```
