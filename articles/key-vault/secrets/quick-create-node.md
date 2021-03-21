---
title: Snabb start – Azure Key Vault hemligt klient bibliotek för Java Script (version 4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av Java Script Client library
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 769a63819925caa50ff364869b8d9a14c3258a2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214549"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Snabb start: Azure Key Vault hemligt klient bibliotek för Java Script (version 4)

Kom igång med det Azure Key Vault hemliga klient biblioteket för Java Script. [Azure Key Vault](../general/overview.md) är en moln tjänst som tillhandahåller en säker lagring för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabb starten får du lära dig hur du skapar, hämtar och tar bort hemligheter från ett Azure Key Vault med hjälp av Java Script Client library

Key Vault klient biblioteks resurser:

[API-referens dokumentation](/javascript/api/overview/azure/key-vault-index)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Mer information om Key Vault och hemligheter finns i:
- [Översikt över Key Vault](../general/overview.md)
- [Översikt över hemligheter](about-secrets.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nuvarande [Node.js](https://nodejs.org) för ditt operativ system.
- [Azure CLI](/cli/azure/install-azure-cli)
- En Key Vault – du kan skapa en med hjälp av [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)

Den här snabb starten förutsätter att du kör [Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

    Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

## <a name="create-new-nodejs-application"></a>Skapa nytt Node.js program

Skapa sedan ett Node.js-program som kan distribueras till molnet. 

1. I ett kommando gränssnitt skapar du en mapp med namnet `key-vault-node-app` :

```terminal
mkdir key-vault-node-app
```

1. Ändra till den nyligen skapade *Key-valvet-Node-app-* katalogen och kör kommandot init för att initiera ett Node-projekt:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installera Key Vault-paket

I konsol fönstret installerar du [biblioteket Azure Key Vault hemligheter](https://www.npmjs.com/package/@azure/keyvault-secrets) för Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Installera [Azure. Identity](https://www.npmjs.com/package/@azure/identity) -paketet för att autentisera till en Key Vault

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Ange miljövariabler

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

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som ger hemliga behörigheter till ditt användar konto

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Kodexempel

I kod exemplen nedan visas hur du skapar en-klient, ställer in en hemlighet, hämtar en hemlighet och tar bort en hemlighet. 

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

1. Skapa en ny textfil och spara den som index.js

1. Lägg till Kräv anrop för att läsa in Azure och Node.js moduler

1. Skapa strukturen för programmet, inklusive grundläggande undantags hantering

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Lägg till direktiv

Lägg till följande direktiv överst i koden:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". I det här exemplet används klassen ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), som gör att vi kan använda samma kod i olika miljöer med olika alternativ för att ge identitet. Mer information om att autentisera till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Lägg till följande kod till Main ()-funktionen

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program är autentiserat kan du ange en hemlighet i ditt nyckel valv med hjälp av [metoden setSecret](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) som kräver ett namn för hemligheten – vi använder "hemlig hemlighet" i det här exemplet.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare angivna värdet med [metoden getSecret](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Din hemlighet sparas nu som `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort och rensar hemligheten från nyckel valvet med metoderna [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) och [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) .

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
```

## <a name="sample-code"></a>Exempelkod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");

  console.log("Deleting your secret from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testa och verifiera

1. Kör följande kommandon för att köra appen.

    ```terminal
    npm install
    node index.js
    ```

1. När du uppmanas till det anger du ett hemligt värde. Till exempel mySecretPassword.

    En variant av följande utdata visas:

    ```azurecli
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.  
    Purging your secret from <your-unique-keyvault-name> ... done.   
    ```


## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat en hemlighet och hämtat hemligheten. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Läs en [Översikt över Azure Key Vault hemligheter](about-secrets.md)
- Så här [säkrar du åtkomst till ett nyckel valv](../general/secure-your-key-vault.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
