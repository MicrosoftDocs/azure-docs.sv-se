---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070290"
---
Använd kommandot Azure CLI [AZ](/cli/azure/keyvault#az_keyvault_create) för att skapa en Key Vault i resurs gruppen från föregående steg. Du måste ange en del information:

- Nyckel valvets namn: en sträng på 3 till 24 tecken som bara får innehålla siffror (0-9), bokstäver (a-z, A-Z) och bindestreck (-)

  > [!Important]
  > Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

- Resurs grupp namn: **myResourceGroup**.
- Platsen: **öster**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Utdata från det här kommandot visar egenskaper för det nyligen skapade nyckel valvet. Anteckna de två egenskaperna som visas nedan:

- **Valv namn**: det namn du angav för parametern--name ovan.
- **Valv-URI**: i exemplet är detta https:// &lt; ditt-Unique-Vault.Azure.net/-valv &gt; .. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.
