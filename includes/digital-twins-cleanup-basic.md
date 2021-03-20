---
author: baanders
description: ta med fil för att rensa en digital Azure-instans
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244760"
---
* **Om du inte behöver någon av de resurser som du skapade i den här självstudien** kan du ta bort Azure Digitals-instansen och alla andra resurser från den här artikeln med kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) . Detta tar bort alla Azure-resurser i en resurs grupp och själva resurs gruppen.
    
    > [!IMPORTANT]
    > Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag.
    
    Öppna [Azure Cloud Shell](https://shell.azure.com)och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```