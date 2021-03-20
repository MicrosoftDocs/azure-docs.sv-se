---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89482153"
---
1. Använd `az group create` kommandot för att skapa en resurs grupp:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Du kan ändra "öster" till en plats nära till dig, om du vill.

1. Använd `az keyvault create` för att skapa nyckel valvet:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Ersätt `<your-unique-keyvault-name>` med ett namn som är unikt för alla Azure. Du använder vanligt vis ditt personliga eller företagets namn tillsammans med andra siffror och identifierare. 

1. Skapa en miljö variabel som tillhandahåller namnet på Key Vault till koden:

    # <a name="cmd"></a>[kommandot](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
