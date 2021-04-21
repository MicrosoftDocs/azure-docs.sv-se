---
title: Snabbstart – Azure Key Vault hemligt klientbibliotek för JavaScript (version 4)
description: Lär dig hur du skapar, hämtar och tar bort hemligheter från ett Azure-nyckelvalv med hjälp av JavaScript-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3d8f20f598243754e3fd39f649e7c84d55b64817
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751678"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>Snabbstart: Azure Key Vault hemligt klientbibliotek för JavaScript (version 4)

Kom igång med Azure Key Vault hemligt klientbibliotek för JavaScript. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten lär du dig att skapa, hämta och ta bort hemligheter från ett Azure-nyckelvalv med hjälp av JavaScript-klientbiblioteket

Key Vault klientbiblioteksresurser:

[API-referensdokumentation](/javascript/api/overview/azure/key-vault-index)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Mer information om Key Vault och hemligheter finns i:
- [Key Vault översikt](../general/overview.md)
- [Översikt över hemligheter.](about-secrets.md)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Aktuella [Node.js](https://nodejs.org) för ditt operativsystem.
- [Azure CLI](/cli/azure/install-azure-cli)
- En Key Vault – du kan skapa en [med hjälp Azure Portal](../general/quick-create-portal.md) Azure [CLI](../general/quick-create-cli.md)eller [Azure PowerShell](../general/quick-create-powershell.md)

Den här snabbstarten förutsätter att du [kör Azure CLI](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

    Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

## <a name="create-new-nodejs-application"></a>Skapa ett nytt Node.js program

Skapa sedan ett Node.js som kan distribueras till molnet. 

1. I ett kommandogränssnitt skapar du en mapp med namnet `key-vault-node-app` :

```terminal
mkdir key-vault-node-app
```

1. Ändra till den nyligen skapade *katalogen key-vault-node-app* och kör kommandot "init" för att initiera nodprojektet:

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installera Key Vault paket

Från konsolfönstret installerar du biblioteket Azure Key Vault [hemligheter för](https://www.npmjs.com/package/@azure/keyvault-secrets) Node.js.

```terminal
npm install @azure/keyvault-secrets
```

Installera [paketet azure.identity](https://www.npmjs.com/package/@azure/identity) för att autentisera till en Key Vault

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Ange miljövariabler

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

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Skapa en åtkomstprincip för ditt nyckelvalv som beviljar hemliga behörigheter till ditt användarkonto

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>Kodexempel

Kodexe exemplen nedan visar hur du skapar en klient, anger en hemlighet, hämtar en hemlighet och tar bort en hemlighet. 

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

1. Skapa en ny textfil och spara den som "index.js"

1. Lägga till Kräv anrop för att läsa in Azure Node.js moduler

1. Skapa strukturen för programmet, inklusive grundläggande undantagshantering

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

### <a name="add-directives"></a>Lägga till direktiv

Lägg till följande -direktiv överst i koden:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder klassen ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), vilket gör att vi kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om autentisering för Key Vault finns i [Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Lägg till följande kod i funktionen "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Spara en hemlighet

Nu när ditt program har autentiserats kan du placera en hemlighet i ditt nyckelvalv med hjälp av [metoden setSecret.](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_) Detta kräver ett namn på hemligheten – vi använder "mySecret" i det här exemplet.  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>Hämta en hemlighet

Du kan nu hämta det tidigare inställda värdet med [getSecret-metoden](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Din hemlighet sparas nu som `retrievedSecret.value` .

### <a name="delete-a-secret"></a>Ta bort en hemlighet

Slutligen tar vi bort och rensar hemligheten från nyckelvalvet med metoderna [beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) och [purgeDeletedSecret.](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_)

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

1. Kör appen genom att köra följande kommandon.

    ```terminal
    npm install
    node index.js
    ```

1. Ange ett hemligt värde när du uppmanas till det. Till exempel mySecretPassword.

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

I den här snabbstarten har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat hemligheten. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs en [översikt över Azure Key Vault hemligheter](about-secrets.md)
- Skydda [åtkomsten till ett nyckelvalv](../general/security-overview.md)
- Se [Azure Key Vault för utvecklare](../general/developers-guide.md)
- Granska [Key Vault säkerhetsöversikten](../general/security-overview.md)
