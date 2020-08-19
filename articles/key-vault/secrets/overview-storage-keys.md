---
title: Hantera lagrings konto nycklar med Azure Key Vault och Azure CLI
description: Lagrings konto nycklar ger sömlös integrering mellan Azure Key Vault och nyckelbaserad åtkomst till ett Azure Storage-konto.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20418dbc7432000116dcbe73a64987d83f38419d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585074"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Hantera lagrings konto nycklar med Key Vault och Azure CLI

Ett Azure Storage-konto använder autentiseringsuppgifter bestående av ett konto namn och en nyckel. Nyckeln genereras automatiskt och fungerar som ett lösen ord i stället för en kryptografisk nyckel. Key Vault hanterar lagrings konto nycklar genom att regelbundet återskapa dem i lagrings kontot och tillhandahålla signaturer för delad åtkomst för delegerad åtkomst till resurser i ditt lagrings konto.

Du kan använda den Key Vault hanterade lagrings konto nyckeln för att lista (synkronisera) nycklar med ett Azure Storage-konto och återskapar (rotera) nycklarna med jämna mellanrum. Du kan hantera nycklar för både lagrings konton och klassiska lagrings konton.

Tänk på följande när du använder funktionen för hanterad lagrings konto nyckel:

- Nyckel värden returneras aldrig som svar på en anropare.
- Endast Key Vault bör hantera dina lagrings konto nycklar. Hantera inte nycklarna själv och Undvik att störa Key Vault processer.
- Endast ett enda Key Vault-objekt bör hantera lagrings konto nycklar. Tillåt inte nyckel hantering från flera objekt.
- Du kan begära Key Vault att hantera ditt lagrings konto med ett huvud namn för användaren, men inte med ett huvud namn för tjänsten.
- Återskapa nycklar med hjälp av Key Vault. Återskapa inte dina lagrings konto nycklar manuellt.

Vi rekommenderar att du använder Azure Storage-integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Azure AD-integrering är tillgänglig för [Azure-blobbar och köer](../../storage/common/storage-auth-aad.md)och ger OAuth2-tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault).

Med Azure AD kan du autentisera klient programmet med hjälp av ett program eller en användar identitet, i stället för autentiseringsuppgifterna för lagrings kontot. Du kan använda en [hanterad Azure AD-identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagrar autentiseringsuppgifter i eller med ditt program.

Azure AD använder rollbaserad åtkomst kontroll (RBAC) för att hantera auktorisering, som också stöds av Key Vault.

## <a name="service-principal-application-id"></a>Program-ID för tjänstens huvud namn

En Azure AD-klient tillhandahåller varje registrerat program med ett [huvud namn för tjänsten](/azure/active-directory/develop/developer-glossary#service-principal-object). Tjänstens huvud namn fungerar som program-ID, som används vid konfiguration av auktorisering för åtkomst till andra Azure-resurser via RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Moln | Program-ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Övrigt  | Valfri | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du först göra följande:

- [Installera Azure CLI](/cli/azure/install-azure-cli).
- [Skapa ett nyckel valv](quick-create-cli.md)
- [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md?tabs=azure-cli). Lagrings konto namnet får bara innehålla gemena bokstäver och siffror. Namnet måste innehålla mellan 3 och 24 tecken.
      
## <a name="manage-storage-account-keys"></a>Hantera lagrings konto nycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera din Azure CLI-session med [AZ-inloggnings](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) kommandona.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagrings konto

Använd kommandot [skapa roll tilldelning](/cli/azure/role/assignment?view=azure-cli-latest) för Azure CLI-AZ för att ge Key Vault åtkomst till ditt lagrings konto. Ange följande parameter värden för kommandot:

- `--role`: Överför Azure-rollen "lagrings kontots nyckel operatörs tjänst roll". Den här rollen begränsar åtkomstscope till ditt lagrings konto. För ett klassiskt lagrings konto skickar du i stället "klassisk lagrings kontots nyckel operatörs tjänst roll".
- `--assignee`: Skicka värdet " https://vault.azure.net ", som är URL: en för Key Vault i det offentliga Azure-molnet. (För Azure goverment Cloud use "--asingee-Object-ID" i stället, se [program-ID för tjänstens huvud namn](#service-principal-application-id).)
- `--scope`: Skicka ditt lagrings kontos resurs-ID, som är i formatet `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Du hittar ditt prenumerations-ID genom att använda kommandot Azure CLI [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) . Om du vill hitta resurs gruppen för ditt lagrings konto namn och lagrings konto använder du kommandot Azure CLI [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användar konto behörighet till hanterade lagrings konton

Använd Azure CLI [-AZ](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) för att uppdatera den Key Vault åtkomst principen och tilldela lagrings kontots behörigheter till ditt användar konto.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Observera att behörigheter för lagrings konton inte är tillgängliga på sidan åtkomst principer för lagrings kontot i Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Skapa ett Key Vault hanterat lagrings konto

 Skapa ett Key Vault hanterat lagrings konto med hjälp av kommandot Azure CLI-AZ för nyckel [valv](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) . Ange en tids period på 90 dagar. När det är dags att rotera återskapar Key Vault den nyckel som inte är aktiv och anger sedan den nya nyckeln som aktiv. Det är bara en av nycklarna som används för att utfärda SAS-token vid ett tillfälle, det här är den aktiva nyckeln. Ange följande parameter värden för kommandot:

- `--vault-name`: Skicka namnet på ditt nyckel valv. Om du vill hitta namnet på nyckel valvet använder du kommandot Azure CLI [AZ Key Vault List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`: Skicka namnet på ditt lagrings konto. Du hittar namnet på ditt lagrings konto med kommandot Azure CLI [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
- `--resource-id`: Skicka ditt lagrings kontos resurs-ID, som är i formatet `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Du hittar ditt prenumerations-ID genom att använda kommandot Azure CLI [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) . Om du vill hitta resurs gruppen för ditt lagrings konto namn och lagrings konto använder du kommandot Azure CLI [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Token för signaturer för delad åtkomst

Du kan också be Key Vault att generera token för signaturer för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto. Du kan bevilja klienter åtkomst till resurser i ditt lagrings konto utan att dela dina konto nycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagrings resurser utan att kompromissa med dina konto nycklar.

Kommandona i det här avsnittet Slutför följande åtgärder:

- Ange en definition för signaturen för delad åtkomst till kontot `<YourSASDefinitionName>` . Definitionen anges för ett Key Vault hanterat lagrings konto `<YourStorageAccountName>` i ditt nyckel valv `<YourKeyVaultName>` .
- Skapa en signatur-token för delad åtkomst för BLOB-, fil-, tabell-och Queue-tjänster. Token skapas för resurs typ tjänst, behållare och objekt. Token skapas med alla behörigheter, över https, och med de angivna start-och slutdatumen.
- Ange en definition av signaturen för delad åtkomst för en Key Vault hanterad lagring i valvet. Definitionen har mall-URI: n för signaturen för signaturen för delad åtkomst som skapades. Definitionen har signaturen för delad åtkomst `account` och är giltig i N dagar.
- Kontrol lera att signaturen för delad åtkomst har sparats i nyckel valvet som en hemlighet.

### <a name="create-a-shared-access-signature-token"></a>Skapa en token för signatur för delad åtkomst

Skapa en definition av signaturen för delad åtkomst med hjälp av Azure CLI [-AZ Storage Account generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Den här åtgärden kräver `storage` `setsas` behörigheterna och.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Kopiera utdata när åtgärden har körts.

```console
"se=2020-01-01&sp=***"
```

De här utdata skickas till- `--template-uri` parametern i nästa steg.

### <a name="generate-a-shared-access-signature-definition"></a>Generera en definition för signatur för delad åtkomst

Använd Azure CLI-AZ för att skapa SAS-definition med hjälp av kommandot för [SAS-definition](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) i Azure CLI och skicka utdata från föregående steg till `--template-uri` -parametern för att skapa en definition av en signatur för delad åtkomst.  Du kan ange namnet på ditt val av- `-n` parameter.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera definitionen av signaturen för delad åtkomst

Du kan kontrol lera att definitionen av signaturen för delad åtkomst har lagrats i ditt nyckel valv med hjälp av Azure CLI-AZ för nyckel [valv](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) och [AZ Key Vault Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) -kommandon.

Börja med att leta upp signaturen för delad åtkomst i ditt nyckel valv med hjälp av kommandot [AZ Key Vault Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Den hemlighet som motsvarar din SAS-definition kommer att ha följande egenskaper:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Du kan nu använda AZ-kommandot för att [Visa hemligheten show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) och `id` egenskapen för att visa innehållet i hemligheten.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Utdata från det här kommandot visar din SAS-definitions sträng som `value` .


## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/).
- Läs artiklarna i [Azure Key Vault teamets blogg](https://blogs.technet.microsoft.com/kv/).
- Se [AZ Storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) Reference Documentation.
