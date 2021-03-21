---
title: Självstudie – använda Azure Key Vault med en virtuell dator i python | Microsoft Docs
description: I den här självstudien konfigurerar du en virtuell dator ett python-program för att läsa en hemlighet från ditt nyckel valv.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2fc77d0cdfb6bd8a62555951c0b6dc7e9b732f93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203546"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Självstudie: använda Azure Key Vault med en virtuell dator i python

Azure Key Vault hjälper dig att skydda nycklar, hemligheter och certifikat, till exempel API-nycklar och databas anslutnings strängar.

I den här självstudien ställer du in ett python-program för att läsa information från Azure Key Vault med hjälp av hanterade identiteter för Azure-resurser. Lär dig att:

> [!div class="checklist"]
> * Skapa ett nyckelvalv
> * Lagra en hemlighet i Key Vault
> * Skapa en virtuell Azure Linux-dator
> * Aktivera en [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för den virtuella datorn
> * Ge nödvändiga behörigheter för konsol programmet för att läsa data från Key Vault
> * Hämta en hemlighet från Key Vault

Läs [Key Vault grundläggande koncept](basic-concepts.md)innan du börjar. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Förutsättningar

För Windows, Mac och Linux:
  * [Git](https://git-scm.com/downloads)
  * Den här självstudien kräver att du kör Azure CLI lokalt. Du måste ha Azure CLI-versionen 2.0.4 eller senare installerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera CLI kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Logga in på Azure

Om du vill logga in i Azure med hjälp av Azure CLI anger du:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Fyll i nyckel valvet med en hemlighet

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med namnet **myVM** med någon av följande metoder:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure-portalen](../../virtual-machines/linux/quick-create-portal.md) | [Azure-portalen](../../virtual-machines/windows/quick-create-portal.md) |

Om du vill skapa en virtuell Linux-dator med Azure CLI använder du kommandot [AZ VM Create](/cli/azure/vm) .  I följande exempel lägger du till ett användar konto med namnet *azureuser*. Parametern `--generate-ssh-keys` används för att automatiskt generera en SSH-nyckel och placera den på standardnyckelplatsen (*~/.ssh*). 

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

Skapa en systemtilldelad identitet för den virtuella datorn med hjälp av kommandot Azure CLI [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Observera den systemtilldelade identiteten som visas i följande kod. Utdata från föregående kommando skulle vara: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Tilldela behörigheter till VM-identiteten

Nu kan du tilldela de tidigare skapade identitets behörigheterna till ditt nyckel valv genom att köra följande kommando:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logga in på den virtuella datorn

Logga in på den virtuella datorn genom att följa anvisningarna i [Anslut och logga in på en virtuell Azure-dator som kör Linux](../../virtual-machines/linux/login-using-aad.md) eller [Anslut och logga in på en virtuell Azure-dator som kör Windows](../../virtual-machines/windows/connect-logon.md).


Om du vill logga in på en virtuell Linux-dator kan du använda SSH-kommandot med alternativet " <publicIpAddress> " som angetts i steget [skapa en virtuell dator](#create-a-virtual-machine) :

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Installera Python-bibliotek på den virtuella datorn

På den virtuella datorn installerar du de två python-bibliotek som vi kommer att använda i python-skriptet: `azure-keyvault-secrets` och `azure.identity` .  

På en virtuell Linux-dator kan du till exempel installera dessa med `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Skapa och redigera exempel på python-skriptet

Skapa en python-fil med namnet **Sample.py** på den virtuella datorn. Redigera filen så att den innehåller följande kod och Ersätt "<ditt unika nyckel valv-namn>" med namnet på ditt nyckel valv:

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

## <a name="run-the-sample-python-app"></a>Kör exempel på python-appen

Kör slutligen **Sample.py**. Om alla har varit väl, ska det returnera värdet för din hemlighet:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort den virtuella datorn och nyckel valvet.  Du kan göra detta snabbt genom att helt enkelt ta bort resurs gruppen som de tillhör:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[REST API för Azure Key Vault](/rest/api/keyvault/)