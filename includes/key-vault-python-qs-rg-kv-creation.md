---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967135"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```