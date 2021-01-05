---
title: Snabb start – Azure Key Vault certifikat klient bibliotek för Java Script (version 4)
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure Key Vault med hjälp av Java Script Client library
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: a96c3a2dc3b013f79799f20595bf5b5f575b363b
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826091"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Snabb start: Azure Key Vault certifikat klient bibliotek för Java Script (version 4)

Kom igång med klient biblioteket för Azure Key Vault-certifikat för Java Script. [Azure Key Vault](../general/overview.md) är en moln tjänst som tillhandahåller en säker lagring för certifikat. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabb starten får du lära dig hur du skapar, hämtar och tar bort certifikat från ett Azure Key Vault med hjälp av Java Script Client library

Key Vault klient biblioteks resurser:

[API-referens dokumentation](/javascript/api/overview/azure/key-vault-index)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (NPM)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Mer information om Key Vault och certifikat finns i:
- [Översikt över Key Vault](../general/overview.md)
- [Certifikat översikt](about-certificates.md).

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

```azurecli
mkdir key-vault-node-app
```

1. Ändra till den nyligen skapade *Key-valvet-Node-app-* katalogen och kör kommandot init för att initiera ett Node-projekt:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installera Key Vault-paket

I konsol fönstret installerar du [biblioteket Azure Key Vault certifikat](https://www.npmjs.com/package/@azure/keyvault-certificates) för Node.js.

```azurecli
npm install @azure/keyvault-certificates
```

Installera [Azure. Identity](https://www.npmjs.com/package/@azure/identity) -paketet för att autentisera till en Key Vault

```azurecli
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
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS eller Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckel valv

Skapa en åtkomst princip för nyckel valvet som beviljar certifikat behörigheter till ditt användar konto

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Kodexempel

I kod exemplen nedan visas hur du skapar en-klient, ställer in ett certifikat, hämtar ett certifikat och tar bort ett certifikat. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabb starten används inloggad användare för att autentisera till nyckel valv, vilket är önskad metod för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas App Service eller virtuell dator. mer information finns i [Översikt över hanterade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

I det här exemplet expanderas namnet på nyckel valvet till Key Vault-URI: n i formatet "https:// \<your-key-vault-name\> . Vault.Azure.net". Det här exemplet använder klassen ["DefaultAzureCredential ()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om autentisering till Key Vault finns i [Developer ' s guide](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Lägg till följande kod till Main ()-funktionen

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Spara ett certifikat

Nu när ditt program är autentiserat kan du ange ett certifikat i ditt nyckel valv med BeginCreateCertificate- [metoden](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) detta kräver ett namn för certifikatet och[certifikat principen för certifikat principen med](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) [certifikat princip egenskaper](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Om det finns ett certifikat namn skapas en ny version av certifikatet i koden ovan.
### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Du kan nu hämta det tidigare angivna värdet med [metoden getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Slutligen tar vi bort och rensar certifikatet från nyckel valvet med metoderna [beginDeleteCertificate] https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) och [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) .

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Exempelkod

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Testa och verifiera

Kör följande kommandon för att köra appen.

```azurecli
npm install
npm index.js
```

En variant av följande utdata visas:

```azurecli
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nyckel valv, lagrat ett certifikat och hämtade det certifikatet. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Läs en [Översikt över certifikat](about-certificates.md)
- Se en [åtkomst Key Vault från självstudien för App Service program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se en [åtkomst Key Vault från den virtuella datorns självstudie](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)
