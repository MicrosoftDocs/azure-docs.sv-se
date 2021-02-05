---
author: baanders
description: ta med fil för att rensa en digital Azure-instans
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575701"
---
* **Om du inte behöver någon av de resurser som du skapade i den här självstudien** kan du ta bort Azure Digitals-instansen och alla andra resurser från den här artikeln med kommandot [AZ Group Delete](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) . Detta tar bort alla Azure-resurser i en resurs grupp och själva resurs gruppen.
    
    > [!IMPORTANT]
    > Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag.
    
    Öppna [Azure Cloud Shell](https://shell.azure.com)och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```