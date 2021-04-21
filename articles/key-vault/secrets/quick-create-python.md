---
title: Snabbstart – Azure Key Vault Python-klientbibliotek – hantera hemligheter
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med hjälp av Python-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: d7b5144264b31e62eac43513d6a851d09a09c60f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814054"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Snabbstart: Azure Key Vault hemligt klientbibliotek för Python

Kom igång med Azure Key Vault hemligt klientbibliotek för Python. Följ stegen nedan för att installera paketet och prova exempelkoden för grundläggande uppgifter. Genom att Key Vault lagra hemligheter undviker du att lagra hemligheter i koden, vilket ökar säkerheten för din app.

[API-referensdokumentation](/python/api/overview/azure/keyvault-secrets-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Paket (Python-paketindex)](https://pypi.org/project/azure-keyvault-secrets/)

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
    pip install azure-identity
    ```


1. Installera Key Vault hemligheter:

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Skapa exempelkoden

Med Azure Key Vault hemligt klientbibliotek för Python kan du hantera hemligheter. Följande kodexempel visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet.

Skapa en fil med *namnet kv_secrets.py* som innehåller den här koden.

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

Kontrollera att koden i föregående avsnitt finns i en fil med namnet *kv_secrets.py*. Kör sedan koden med följande kommando:

```terminal
python kv_secrets.py
```

- Om du stöter på behörighetsfel kontrollerar du att du har kört [ `az keyvault set-policy` kommandot](#grant-access-to-your-key-vault).
- Om koden med samma hemlighetsnamn körs på nytt kan felet "(Conflict) Secret is currently in a deleted but recoverable state "(Conflict) Secret is currently <name> in a deleted but recoverable state". Använd ett annat hemligt namn.

## <a name="code-details"></a>Kodinformation

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterade identiteter tilldelas till App Service eller virtuell dator. Mer information finns i [Översikt över hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI:t i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder  [klassen "DefaultAzureCredential()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identiteter. Mer information finns i [Standardautentisering med Azure-autentiseringsuppgifter.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Spara en hemlighet

När du har fått klientobjektet för nyckelvalvet kan du lagra en hemlighet med hjälp set_secret [metoden:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) 

```python
client.set_secret(secretName, secretValue)
```

Anrop `set_secret` genererar ett anrop till Azure REST API för nyckelvalvet.

När du hanterar begäran autentiserar Azure anroparen (tjänstens huvudnamn) med hjälp av autentiseringsobjektet som du angav för klienten.

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Om du vill läsa en hemlighet Key Vault du använda [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-) metoden:

```python
retrieved_secret = client.get_secret(secretName)
 ```

Det hemliga värdet finns i `retrieved_secret.value` .

Du kan också hämta en hemlighet med Azure CLI-kommandot [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show).

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Om du vill ta bort en hemlighet [använder begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) metoden:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

Metoden `begin_delete_secret` är asynkron och returnerar ett avsökningsobjekt. När avsökningsmetoden anropas `result` väntar den på att den slutförs.

Du kan kontrollera att hemligheten har tagits bort med Azure CLI-kommandot [az keyvault secret show](/cli/azure/keyvault/secret?#az_keyvault_secret_show).

När en hemlighet har tagits bort förblir den i ett borttagna men återställningsbart tillstånd under en tid. Om du kör koden igen använder du ett annat hemligt namn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du även vill experimentera med certifikat [och](../certificates/quick-create-python.md) nycklar [kan](../keys/quick-create-python.md)du återanvända de Key Vault som skapades i den här artikeln.

När du är klar med resurserna som skapades i den här artikeln använder du annars följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Key Vault](../general/overview.md)
- [Säker åtkomst till ett nyckelvalv](../general/security-features.md)
- [Azure Key Vault utvecklarguide](../general/developers-guide.md)
- [Key Vault säkerhetsöversikt](../general/security-features.md)
- [Autentisera med Key Vault](../general/authentication.md)
