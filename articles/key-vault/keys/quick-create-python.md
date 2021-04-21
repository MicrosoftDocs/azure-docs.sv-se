---
title: Snabbstart – Azure Key Vault Python-klientbibliotek – hantera nycklar
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure-nyckelvalv med hjälp av Python-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dbff065fc4ee0f4618cae3fa0b012a286d4a0645
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815422"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Snabbstart: Azure Key Vault klientbibliotek för Python

Kom igång med Azure Key Vault klientbibliotek för Python. Följ stegen nedan för att installera paketet och prova exempelkoden för grundläggande uppgifter. Genom att Key Vault lagra kryptografiska nycklar undviker du att lagra sådana nycklar i koden, vilket ökar säkerheten för din app.

[API-referensdokumentation](/python/api/overview/azure/keyvault-keys-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Paket (Python-paketindex)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ eller 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabbstarten förutsätter att du [kör Azure CLI](/cli/azure/install-azure-cli) i ett Linux-terminalfönster.

## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö

Den här snabbstarten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="install-the-packages"></a>Installera paketen

1. I en terminal eller kommandotolk skapar du en lämplig projektmapp och skapar och aktiverar sedan en virtuell Python-miljö enligt beskrivningen i [Använda virtuella Python-miljöer](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Installera Azure Active Directory identitetsbiblioteket:

    ```terminal
    pip install azure.identity
    ```


1. Installera klientbiblioteket Key Vault nyckel:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för ditt nyckelvalv som ger ditt användarkonto hemlig behörighet.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder nyckelvalvsnamnet som en miljövariabel med namnet `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS eller Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Skapa exempelkoden

Med Azure Key Vault nyckelklientbiblioteket för Python kan du hantera kryptografiska nycklar. Följande kodexempel visar hur du skapar en klient, anger en nyckel, hämtar en nyckel och tar bort en nyckel.

Skapa en fil med *namnet kv_keys.py* som innehåller den här koden.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrollera att koden i föregående avsnitt finns i en fil med namnet *kv_keys.py*. Kör sedan koden med följande kommando:

```terminal
python kv_keys.py
```

- Om du stöter på behörighetsfel kontrollerar du att du har kört [ `az keyvault set-policy` kommandot](#grant-access-to-your-key-vault).
- Om du kör koden igen med samma nyckelnamn kan felet "(Konflikt) Nyckeln är för närvarande i ett borttagna men <name> återställningsbart tillstånd". Använd ett annat nyckelnamn.

## <a name="code-details"></a>Kodinformation

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder  [klassen "DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information finns i [Standardautentisering med Azure-autentiseringsuppgifter.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Spara en nyckel

När du har fått klientobjektet för nyckelvalvet kan du lagra en nyckel med hjälp create_rsa_key [metoden:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Du kan också använda [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) eller [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

När en `create` metod anropas genereras ett anrop till Azure REST API för nyckelvalvet.

När du hanterar begäran autentiserar Azure anroparens identitet (tjänstens huvudnamn) med hjälp av autentiseringsobjektet som du angav för klienten.

## <a name="retrieve-a-key"></a>Hämta en nyckel

Om du vill läsa en Key Vault från en get_key [metoden:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-)

```python
retrieved_key = client.get_key(keyName)
 ```

Du kan också kontrollera att nyckeln har angetts med Azure CLI-kommandot [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show).

### <a name="delete-a-key"></a>Ta bort en nyckel

Om du vill ta bort en nyckel [använder begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) metoden:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Metoden `begin_delete_key` är asynkron och returnerar ett avsökningsobjekt. När avsökningsmetoden `result` anropas väntar den på att den slutförs.

Du kan kontrollera att nyckeln har tagits bort med Azure CLI-kommandot [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show).

När nyckeln har tagits bort finns den kvar i ett borttagna men återställningsbart tillstånd under en tid. Om du kör koden igen använder du ett annat nyckelnamn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du även vill experimentera med certifikat [och](../certificates/quick-create-python.md) [hemligheter kan du](../secrets/quick-create-python.md)återanvända de Key Vault som skapades i den här artikeln.

När du är klar med resurserna som skapades i den här artikeln använder du annars följande kommando för att ta bort resursgruppen och alla dess resurser:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Säker åtkomst till ett nyckelvalv](../general/security-features.md)
- [Azure Key Vault utvecklarguide](../general/developers-guide.md)
- [Key Vault säkerhetsöversikt](../general/security-features.md)
- [Autentisera med Key Vault](../general/authentication.md)
