---
title: Självstudie – Använda Azure Key Vault med en virtuell dator i Python-| Microsoft Docs
description: I den här självstudien konfigurerar du en virtuell dator ett Python-program för att läsa en hemlighet från ditt nyckelvalv.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 25182105db831724565c6bf3dbbbb79832b677f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772068"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Självstudie: Använda Azure Key Vault med en virtuell dator i Python

Azure Key Vault hjälper dig att skydda nycklar, hemligheter och certifikat, till exempel API-nycklar och databasanslutningssträngar.

I den här självstudien konfigurerade du ett Python-program för att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv
> * Lagra en hemlighet i Key Vault
> * Skapa en virtuell Linux-dator i Azure
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn
> * Bevilja de behörigheter som krävs för att konsolprogrammet ska kunna läsa data från Key Vault
> * Hämta en hemlighet från Key Vault

Innan du börjar bör du läsa [Key Vault grundläggande begreppen](basic-concepts.md). 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI version 2.0.4 eller senare installerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Fylla i nyckelvalvet med en hemlighet

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator **med namnet myVM** med någon av följande metoder:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure-portalen](../../virtual-machines/linux/quick-create-portal.md) | [Azure-portalen](../../virtual-machines/windows/quick-create-portal.md) |

Om du vill skapa en virtuell Linux-dator med hjälp av Azure CLI använder du [kommandot az vm create.](/cli/azure/vm)  I följande exempel läggs ett användarkonto med namnet *azureuser till.* Parametern `--generate-ssh-keys` används för att automatiskt generera en SSH-nyckel och placera den på standardnyckelplatsen (*~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Observera värdet för `publicIpAddress` i utdata.

## <a name="assign-an-identity-to-the-vm"></a>Tilldela en identitet till den virtuella datorn

Skapa en system tilldelad identitet för den virtuella datorn med hjälp av kommandot Azure CLI [az vm identity assign:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Observera den system tilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till den virtuella datorns identitet

Nu kan du tilldela de tidigare skapade identitetsbehörigheterna till ditt nyckelvalv genom att köra följande kommando:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logga in på den virtuella datorn

Om du vill logga in på den virtuella datorn följer du anvisningarna i Ansluta och logga in på en virtuell [Azure-dator](../../virtual-machines/linux/login-using-aad.md) som kör Linux eller Anslut och logga in på en virtuell [Azure-dator som kör Windows.](../../virtual-machines/windows/connect-logon.md)


Om du vill logga in på en virtuell Linux-dator kan du använda ssh-kommandot med " " som anges <publicIpAddress> i steget Skapa en [virtuell](#create-a-virtual-machine) dator:

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Installera Python-bibliotek på den virtuella datorn

Installera de två Python-bibliotek som vi ska använda i python-skriptet på den virtuella datorn: `azure-keyvault-secrets` och `azure.identity` .  

På en virtuell Linux-dator kan du till exempel installera dessa med hjälp av `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Skapa och redigera Python-exempelskriptet

På den virtuella datorn skapar du en Python-fil med **namnet sample.py**. Redigera filen så att den innehåller följande kod och ersätt "<your-unique-keyvault-name>" med namnet på ditt nyckelvalv:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Köra Python-exempelappen

Kör slutligen **sample.py**. Om allt har gått bra bör det returnera värdet för din hemlighet:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort den virtuella datorn och nyckelvalvet när de inte längre behövs.  Du kan göra detta snabbt genom att helt enkelt ta bort resursgruppen som de tillhör:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[REST API för Azure Key Vault](/rest/api/keyvault/)