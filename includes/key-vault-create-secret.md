---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245123"
---
Nu ska vi skapa en hemlighet **som kallas hemlig**, med värdet **lyckades!**. En hemlighet kan vara ett lösen ord, en SQL-anslutningssträng eller annan information som du behöver för att hålla både säkra och tillgängliga för ditt program. 

Om du vill lägga till en hemlighet i det nya nyckel valvet använder du kommandot Azure CLI [AZ Key Vault Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) :

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```