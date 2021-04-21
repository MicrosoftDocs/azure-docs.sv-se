---
author: baanders
description: inkludera fil för att rensa en Azure Digital Twins instans
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800332"
---
* **Om du inte behöver** någon av de resurser som du skapade i den här självstudien kan du ta bort Azure Digital Twins-instansen och alla andra resurser från den här artikeln med [kommandot az group delete.](/cli/azure/group#az_group_delete) Detta tar bort alla Azure-resurser i en resursgrupp samt själva resursgruppen.
    
    > [!IMPORTANT]
    > Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag.
    
    Öppna [Azure Cloud Shell](https://shell.azure.com)och kör följande kommando för att ta bort resursgruppen och allt den innehåller.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```