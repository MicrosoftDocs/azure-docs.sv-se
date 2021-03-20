---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/21/2018
ms.author: cephalin
ms.openlocfilehash: e7494db94c7d8f0fc610ab297798749bffd55e7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997974"
---
## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name myResourceGroup
```

Det kan några minuter att köra kommandot.