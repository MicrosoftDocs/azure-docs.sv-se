---
title: Snabbstart – Azure Key Vault nyckelklientbibliotek för JavaScript (version 4)
description: Lär dig hur du skapar, hämtar och tar bort nycklar från ett Azure-nyckelvalv med hjälp av JavaScript-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 08de0045a19b0f07308778f8716fa48abded445b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815548"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-javascript-version-4"></a>Snabbstart: Azure Key Vault nyckelklientbibliotek för JavaScript (version 4)

Kom igång med Azure Key Vault-nyckelklientbiblioteket för JavaScript. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för kryptografiska nycklar. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten lär du dig att skapa, hämta och ta bort nycklar från ett Azure-nyckelvalv med hjälp av JavaScript-nyckelklientbiblioteket

Key Vault klientbiblioteksresurser:

[API-referensdokumentation](/javascript/api/overview/azure/key-vault-index)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-keys)

Mer information om Key Vault och nycklar finns i:
- [Key Vault översikt](../general/overview.md)
- [Översikt över nycklar.](about-keys.md)

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

```azurecli
mkdir key-vault-node-app
```

1. Ändra till den nyligen skapade *katalogen key-vault-node-app* och kör kommandot "init" för att initiera nodprojektet:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installera Key Vault paket

Från konsolfönstret installerar du biblioteket Azure Key Vault [nycklar för](https://www.npmjs.com/package/@azure/keyvault-keys) Node.js.

```azurecli
npm install @azure/keyvault-keys
```

Installera [paketet azure.identity](https://www.npmjs.com/package/@azure/identity) för att autentisera till en Key Vault

```azurecli
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

Skapa en åtkomstprincip för ditt nyckelvalv som beviljar nyckelbehörigheter till ditt användarkonto

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --key-permissions delete get list create purge
```

## <a name="code-examples"></a>Kodexempel

Kodexe exemplen nedan visar hur du skapar en klient, anger en nyckel, hämtar en nyckel och tar bort en nyckel. 

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
const { KeyClient } = require("@azure/keyvault-keys");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder klassen ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om hur du autentiserar till Key Vault finns [i Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Lägg till följande kod i funktionen "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new KeyClient(KVUri, credential);
```

### <a name="save-a-key"></a>Spara en nyckel

Nu när programmet har autentiserats kan du placera en nyckel i ditt nyckelvalv med hjälp av [createKey-metoden](/javascript/api/@azure/keyvault-keys/keyclient?#createKey_string__KeyType__CreateKeyOptions_) Metodens parametrar accepterar ett nyckelnamn och [nyckeltypen](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keytype)

```javascript
await client.createKey(keyName, keyType);
```

### <a name="retrieve-a-key"></a>Hämta en nyckel

Du kan nu hämta det tidigare inställda värdet med [getKey-metoden](/javascript/api/@azure/keyvault-keys/keyclient?#getKey_string__GetKeyOptions_).

```javascript
const retrievedKey = await client.getKey(keyName);
 ```

### <a name="delete-a-key"></a>Ta bort en nyckel

Slutligen tar vi bort och rensar nyckeln från nyckelvalvet med [metoderna beginDeleteKey](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#beginDeleteKey_string__BeginDeleteKeyOptions_) och [purgeDeletedKey.](https://docs.microsoft.com/javascript/api/@azure/keyvault-keys/keyclient?#purgeDeletedKey_string__PurgeDeletedKeyOptions_)

```javascript
const deletePoller = await client.beginDeleteKey(keyName);
await deletePoller.pollUntilDone();
await client.purgeDeletedKey(keyName);
```

## <a name="sample-code"></a>Exempelkod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { KeyClient } = require("@azure/keyvault-keys");

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
  const client = new KeyClient(KVUri, credential);

  const keyName = "myKey";
  
  console.log("Creating a key in " + keyVaultName + " called '" + keyName + "` ...");
  await client.createKey(keyName, "RSA");

  console.log("Done.");

  console.log("Retrieving your key from " + keyVaultName + ".");

  const retrievedKey = await client.getKey(keyName);

  console.log("Your key version is '" + retrievedKey.properties.version + "'.");

  console.log("Deleting your key from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteKey(keyName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your key from {keyVaultName} ...");
  await client.purgeDeletedKey(keyName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testa och verifiera

Kör appen genom att köra följande kommandon.

```azurecli
npm install
npm index.js
```

En variant av följande utdata visas:

```azurecli
Creating a key in <your-unique-keyvault-name> called 'myKey' ... done.
Retrieving your key from mykeyvault.
Your key version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your key from <your-unique-keyvault-name> ... done.  
Purging your key from <your-unique-keyvault-name> ... done.   
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett nyckelvalv, lagrade en nyckel och hämtade nyckeln. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs en [översikt över Azure Key Vault nycklar](about-keys.md)
- Skydda [åtkomsten till ett nyckelvalv](../general/security-features.md)
- Se [Azure Key Vault för utvecklare](../general/developers-guide.md)
- Granska [Key Vault säkerhetsöversikten](../general/security-features.md)
