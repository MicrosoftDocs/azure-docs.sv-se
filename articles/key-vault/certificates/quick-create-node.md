---
title: Snabbstart – Azure Key Vault-certifikatklientbibliotek för JavaScript (version 4)
description: Lär dig hur du skapar, hämtar och tar bort certifikat från ett Azure-nyckelvalv med hjälp av JavaScript-klientbiblioteket
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3f933a564e62bf3aae1ec05c6dde048100c22967
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815854"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Snabbstart: Azure Key Vault certifikatklientbibliotek för JavaScript (version 4)

Kom igång med Azure Key Vault för JavaScript. [Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert arkiv för certifikat. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Du kan skapa och hantera Azure-nyckelvalv via Azure Portal. I den här snabbstarten lär du dig att skapa, hämta och ta bort certifikat från ett Azure-nyckelvalv med hjälp av JavaScript-klientbiblioteket

Key Vault klientbiblioteksresurser:

[API-referensdokumentation](/javascript/api/overview/azure/key-vault-index)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault)  |  [Paket (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Mer information om Key Vault och certifikat finns i:
- [Key Vault översikt](../general/overview.md)
- [Översikt över certifikat.](about-certificates.md)

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

Från konsolfönstret installerar du biblioteket Azure Key Vault [certifikat för](https://www.npmjs.com/package/@azure/keyvault-certificates) Node.js.

```azurecli
npm install @azure/keyvault-certificates
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

Skapa en åtkomstprincip för ditt nyckelvalv som beviljar certifikatbehörighet till ditt användarkonto

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Kodexempel

Kodexe exemplen nedan visar hur du skapar en klient, anger ett certifikat, hämtar ett certifikat och tar bort ett certifikat. 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient

I den här snabbstarten används den inloggade användaren för att autentisera till nyckelvalvet, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska hanterad identitet tilldelas till App Service eller virtuell dator. Mer information finns i Översikt över [hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder [klassen "DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) från [Azure Identity Library](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identitet. Mer information om hur du autentiserar till Key Vault finns [i Utvecklarguide.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

Lägg till följande kod i funktionen "main()"

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Spara ett certifikat

Nu när programmet har autentiserats kan du placera ett certifikat i ditt nyckelvalv med metoden [beginCreateCertificate.](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_) Detta [](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties) kräver ett namn på certifikatet och certifikatprincipens certifikatprincip med certifikatprincipegenskaper[](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Om certifikatnamnet finns skapar koden ovan en ny version av certifikatet.
### <a name="retrieve-a-certificate"></a>Hämta ett certifikat

Nu kan du hämta det tidigare inställda värdet med [metoden getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Ta bort ett certifikat

Slutligen tar vi bort och rensar certifikatet från nyckelvalvet med metoderna [beginDeleteCertificate] och https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) [purgeDeletedCertificate.](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_)

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

Kör appen genom att köra följande kommandon.

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

I den här snabbstarten skapade du ett nyckelvalv, lagrade ett certifikat och hämtade certifikatet. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs en [översikt över certifikat](about-certificates.md)
- Se [självstudien åtkomst Key Vault från App Service-program](../general/tutorial-net-create-vault-azure-web-app.md)
- Se [självstudien åtkomst Key Vault från virtuell dator](../general/tutorial-net-virtual-machine.md)
- Se [Azure Key Vault utvecklarhandbok](../general/developers-guide.md)
- Granska Key Vault [säkerhetsöversikten](../general/security-features.md)
