---
title: Snabb start – Azure Key Vault python-klient bibliotek – hantera hemligheter
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av python-klient biblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 1eb3728e9697cefc3621221e4a918656efcba3c6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936642"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Snabb start: Azure Key Vault hemligt klient bibliotek för python

Kom igång med klient biblioteket Azure Key Vault Secret för python. Följ stegen nedan för att installera paketet och prova exempel koden för grundläggande uppgifter. Genom att använda Key Vault för att lagra hemligheter undviker du att lagra hemligheter i din kod, vilket ökar säkerheten för din app.

[API-referens dokumentation](/python/api/overview/azure/keyvault-secrets-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Paket (python-paket index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + eller 3.5.3 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli) i ett Linux-terminalfönster.


## <a name="set-up-your-local-environment"></a>Konfigurera din lokala miljö
Den här snabb starten använder Azure Identity Library med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. mer information finns i [autentisera klienten med klient biblioteket för Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="install-the-packages"></a>Installera paketen

1. I en terminal-eller kommando tolk skapar du en lämplig projektmapp och skapar och aktiverar sedan en virtuell python-miljö enligt beskrivningen i [använda python-virtuella miljöer](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Installera Azure Active Directory identitets bibliotek:

    ```terminal
    pip install azure-identity
    ```


1. Installera Key Vault hemligheter-biblioteket:

    ```terminal
    pip install azure-keyvault-secrets
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resurs grupp och ett nyckel valv

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som beviljar hemliga behörigheter till ditt användar konto.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Ange miljövariabler

Det här programmet använder Key Vault-namnet som en miljö variabel som kallas `KEY_VAULT_NAME` .

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

## <a name="create-the-sample-code"></a>Skapa exempel koden

Med Azure Key Vault hemliga klient biblioteket för python kan du hantera hemligheter. Följande kod exempel visar hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Skapa en fil med namnet *kv_secrets. py* som innehåller den här koden.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Kör koden

Kontrol lera att koden i föregående avsnitt finns i en fil med namnet *kv_secrets. py*. Kör sedan koden med följande kommando:

```terminal
python kv_secrets.py
```

- Om du får problem med behörigheten kontrollerar du att du körde [ `az keyvault set-policy` kommandot](#grant-access-to-your-key-vault).
- Att köra koden igen med samma hemliga namn kan orsaka felet, "(konflikt) hemligheten <name> är för närvarande i ett borttaget men återställnings Bart tillstånd." Använd ett annat hemligt namn.

## <a name="code-details"></a>Kod information

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Det här exemplet använder klassen  ["DefaultAzureCredential ()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) , som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information finns i [Azure Credential Authentication](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Spara en hemlighet

När du har fått klient objekt för nyckel valvet kan du lagra en hemlighet med hjälp av metoden [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

Anrop `set_secret` genererar ett anrop till Azure-REST API för nyckel valvet.

Vid hantering av begäran autentiserar Azure anroparens identitet (tjänstens huvud namn) med hjälp av det Credential-objekt som du angav för klienten.

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Om du vill läsa en hemlighet från Key Vault använder du [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) -metoden:

```python
retrieved_secret = client.get_secret(secretName)
 ```

Det hemliga värdet finns i `retrieved_secret.value` .

Du kan också hämta en hemlighet med Azure CLI-kommandot [AZ-valv hemligt show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Om du vill ta bort en hemlighet använder du metoden [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

`begin_delete_secret`Metoden är asynkron och returnerar ett avsöknings objekt. Att anropa avsöknings `result` metoden väntar på att slutföras.

Du kan kontrol lera att hemligheten har tagits bort med Azure CLI-kommandot [AZ-valv hemligt show](/cli/azure/keyvault/secret?#az-keyvault-secret-show).

När den har tagits bort kvarstår en hemlighet i ett borttaget, men ett återställnings Bart tillstånd för en tid. Om du kör koden igen ska du använda ett annat hemligt namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du också vill experimentera med [certifikat](../certificates/quick-create-python.md) och [nycklar](../keys/quick-create-python.md)kan du återanvända Key Vault som skapats i den här artikeln.

Annars, när du är klar med resurserna som du skapade i den här artikeln, använder du följande kommando för att ta bort resurs gruppen och alla resurser som finns i den:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Säker åtkomst till ett nyckel valv](../general/secure-your-key-vault.md)
- [Guide för Azure Key Vault utvecklare](../general/developers-guide.md)
- [Översikt över Key Vault säkerhet](../general/security-overview.md)
- [Autentisera med Key Vault](../general/authentication.md)
