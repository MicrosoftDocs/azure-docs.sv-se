---
title: Snabbstart – Azure Key Vault Key-klientbibliotek för Java
description: Innehåller en snabbstart för Azure Key Vault Keys-klientbiblioteket för Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 75cb7b6c9225e8579561f980df10da8994257133
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777193"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Snabbstart: Azure Key Vault Key-klientbibliotek för Java
Kom igång med Azure Key Vault Key-klientbiblioteket för Java. Följ stegen nedan för att installera paketet och prova exempelkoden för grundläggande uppgifter.

Ytterligare resurser:

* [Källkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Referensdokumentation för API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Produktdokumentation](index.yml)
* [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration [– skapa en utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/) version 8 eller senare
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Den här snabbstarten förutsätter att du [kör Azure CLI](/cli/azure/install-azure-cli) och Apache [Maven i](https://maven.apache.org) ett Linux-terminalfönster.

## <a name="setting-up"></a>Inrätta
Den här snabbstarten använder Azure Identity-biblioteket med Azure CLI för att autentisera användare till Azure-tjänster. Utvecklare kan också använda Visual Studio eller Visual Studio Code för att autentisera sina anrop. Mer information finns i Autentisera klienten med [Azure Identity-klientbiblioteket](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Logga in på Azure
1. Kör kommandot `login`.

    ```azurecli-interactive
    az login
    ```

   Om CLI kan öppna din standardwebbläsare gör den det och läser in en Inloggningssida för Azure.

   Annars öppnar du en webbläsarsida på och [https://aka.ms/devicelogin](https://aka.ms/devicelogin) anger auktoriseringskoden som visas i terminalen.

2. Logga in med dina autentiseringsuppgifter för kontot i webbläsaren.

### <a name="create-a-new-java-console-app"></a>Skapa en ny Java-konsolapp
I ett konsolfönster använder du kommandot `mvn` för att skapa en ny Java-konsolapp med namnet `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Utdata från genereringen av projektet ser ut ungefär så här:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Ändra katalogen till den nyligen skapade `akv-keys-java/` mappen.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Installera paketet
Öppna *pom.xml* i textredigeraren. Lägg till följande beroendeelement i gruppen med beroenden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Skapa en resursgrupp och ett nyckelvalv
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv
Skapa en åtkomstprincip för ditt nyckelvalv som ger nyckelbehörighet till ditt användarkonto.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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

## <a name="object-model"></a>Objektmodell
Med Azure Key Vault Key-klientbiblioteket för Java kan du hantera nycklar. Avsnittet [Kodexempel](#code-examples) visar hur du skapar en klient, skapar en nyckel, hämtar en nyckel och tar bort en nyckel.

Hela konsolappen finns [under](#sample-code).

## <a name="code-examples"></a>Kodexempel
### <a name="add-directives"></a>Lägga till direktiv
Lägg till följande -direktiv överst i koden:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Autentisera och skapa en klient
I den här snabbstarten används en inloggad användare för att autentisera till Key Vault, vilket är den bästa metoden för lokal utveckling. För program som distribueras till Azure ska en hanterad identitet tilldelas till en App Service eller virtuell dator. Mer information finns i Översikt [över hanterad identitet.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

I exemplet nedan expanderas namnet på ditt nyckelvalv till nyckelvalvs-URI:t i formatet "https:// \<your-key-vault-name\> .vault.azure.net". Det här exemplet använder klassen ["DefaultAzureCredential()"](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) som gör att du kan använda samma kod i olika miljöer med olika alternativ för att tillhandahålla identiteter. Mer information finns i [Standardautentisering med Azure-autentiseringsuppgifter.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Skapa en nyckel
Nu när programmet har autentiserats kan du skapa en nyckel i nyckelvalvet med hjälp av `keyClient.createKey` metoden . Detta kräver ett namn för nyckeln och en nyckeltyp – vi har tilldelat värdet "myKey" till variabeln och använder en RSA i `keyName` `KeyType` det här exemplet.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Du kan kontrollera att nyckeln har angetts med [kommandot az keyvault key](/cli/azure/keyvault/key?#az_keyvault_key_show) show:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Hämta en nyckel
Nu kan du hämta den tidigare skapade nyckeln med `keyClient.getKey` -metoden.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Nu kan du komma åt informationen om den hämtade nyckeln med åtgärder som `retrievedKey.getProperties` `retrievedKey.getKeyOperations` , osv.

### <a name="delete-a-key"></a>Ta bort en nyckel
Slutligen tar vi bort nyckeln från nyckelvalvet med `keyClient.beginDeleteKey` metoden .

Borttagning av nycklar är en långvarig åtgärd som du kan avse förloppet för eller vänta tills den har slutförts.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Du kan kontrollera att nyckeln har tagits bort med kommandot [az keyvault key](/cli/azure/keyvault/key?#az_keyvault_key_show) show:

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Rensa resurser
När det inte längre behövs kan du använda Azure CLI eller Azure PowerShell för att ta bort nyckelvalvet och motsvarande resursgrupp.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exempelkod
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du ett nyckelvalv, skapade en nyckel, hämtade det och tog sedan bort det. Mer information om Key Vault och hur du integrerar det med dina program finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs Key Vault [säkerhetsöversikt](../general/security-overview.md)
- Se [Azure Key Vault utvecklarhandbok](../general/developers-guide.md)
- Skydda [åtkomst till ett nyckelvalv](../general/security-overview.md)