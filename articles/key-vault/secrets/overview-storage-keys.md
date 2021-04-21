---
title: Hantera lagringskontonycklar med Azure Key Vault och Azure CLI
description: Lagringskontonycklar ger sömlös integrering mellan Azure Key Vault och nyckelbaserad åtkomst till ett Azure Storage-konto.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 871c9f83d7bdc3f4c9e2f3b53c0b855483995152
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748618"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Hantera lagringskontonycklar med Key Vault och Azure CLI
> [!IMPORTANT]
> Vi rekommenderar att Azure Storage integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade tjänst för identitets- och åtkomsthantering. Azure AD-integrering är tillgängligt för [Azure-blobar](../../storage/common/storage-auth-aad.md)och köer och ger OAuth2-tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault). Med Azure AD kan du autentisera klientprogrammet med hjälp av ett program eller en användaridentitet i stället för autentiseringsuppgifterna för lagringskontot. Du kan använda en [azure AD-hanterad identitet](../../active-directory/managed-identities-azure-resources/index.yml) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagring av autentiseringsuppgifter i eller med ditt program. Använd endast lösningen nedan när Azure AD-autentisering inte är möjligt.

Ett Azure Storage-konto använder autentiseringsuppgifter som består av ett kontonamn och en nyckel. Nyckeln genereras automatiskt och fungerar som ett lösenord, i stället för en som en kryptografisk nyckel. Key Vault hanterar lagringskontonycklar genom att regelbundet återskapa dem i lagringskontot och tillhandahåller signaturtoken för delad åtkomst för delegerad åtkomst till resurser i ditt lagringskonto.

Du kan använda funktionen Key Vault nyckel för hanterat lagringskonto för att lista (synkronisera) nycklar med ett Azure-lagringskonto och återskapa (rotera) nycklarna regelbundet. Du kan hantera nycklar för både lagringskonton och klassiska lagringskonton.

Tänk på följande när du använder nyckelfunktionen för hanterat lagringskonto:

- Nyckelvärden returneras aldrig som svar på en anropare.
- Endast Key Vault ska hantera dina lagringskontonycklar. Hantera inte nycklarna själv och undvik att störa Key Vault processer.
- Endast ett enda Key Vault ska hantera lagringskontonycklar. Tillåt inte nyckelhantering från flera objekt.
- Återskapa nycklar med hjälp av Key Vault endast. Återskapa inte dina lagringskontonycklar manuellt.

## <a name="service-principal-application-id"></a>Program-ID för tjänstens huvudnamn

En Azure AD-klientorganisation ger varje registrerat program ett [huvudnamn för tjänsten.](../../active-directory/develop/developer-glossary.md#service-principal-object) Tjänstens huvudnamn fungerar som program-ID, som används vid auktoriseringskonfigurationen för åtkomst till andra Azure-resurser via Azure RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Moln | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Övrigt  | Valfri | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du först göra följande:

- [Installera Azure CLI.](/cli/azure/install-azure-cli)
- [Skapa ett nyckelvalv](quick-create-cli.md)
- [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md?tabs=azure-cli). Lagringskontots namn får bara innehålla gemener och siffror. Namnet måste vara mellan 3 och 24 tecken långt.
      
## <a name="manage-storage-account-keys"></a>Hantera lagringskontonycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera Din Azure CLI-session med hjälp av [az login-kommandona.](/powershell/module/az.accounts/connect-azaccount)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagringskonto

Använd kommandot Azure CLI [az role assignment create för](/cli/azure/role/assignment) att ge Key Vault åtkomst till ditt lagringskonto. Ange följande parametervärden för kommandot:

- `--role`: Skicka Azure-rollen "Tjänstroll för lagringskontonyckeloperatör". Den här rollen begränsar åtkomstomfånget till ditt lagringskonto. För ett klassiskt lagringskonto skickar du "Klassisk tjänstroll för lagringskontonyckeloperatör" i stället.
- `--assignee`: Skicka värdet " https://vault.azure.net ", som är URL:en för Key Vault i det offentliga Azure-molnet. (För Azure Goverment-molnet använder du "--asingee-object-id" i stället, se [Program-ID för tjänstens huvudnamn](#service-principal-application-id).)
- `--scope`: Skicka resurs-ID:t för ditt lagringskonto, som har formen `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Du hittar ditt prenumerations-ID med hjälp av kommandot Azure CLI [az account list;](/cli/azure/account?#az-account-list) För att hitta namnet på ditt lagringskonto och resursgruppen för lagringskontot använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användarkonto behörighet till hanterade lagringskonton

Använd cmdleten Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) för att uppdatera Key Vault-åtkomstprincipen och bevilja lagringskontobehörigheter till ditt användarkonto.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Observera att behörigheter för lagringskonton inte är tillgängliga på sidan Åtkomstprinciper för lagringskontot i Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Skapa ett Key Vault Hanterat lagringskonto

 Skapa ett Key Vault hanterat lagringskonto med hjälp av Azure [CLI-kommandot az keyvault storage.](/cli/azure/keyvault/storage?#az-keyvault-storage-add) Ange en återskapandeperiod på 90 dagar. När det är dags att rotera återskapar KeyVault nyckeln som inte är aktiv och anger sedan den nya nyckeln som aktiv. Endast en av nycklarna används för att utfärda SAS-token åt gången, det här är den aktiva nyckeln. Ange följande parametervärden för kommandot:

- `--vault-name`: Skicka namnet på ditt nyckelvalv. Om du vill hitta namnet på ditt nyckelvalv använder du kommandot Azure CLI [az keyvault list.](/cli/azure/keyvault?#az-keyvault-list)
- `-n`: Ange namnet på ditt lagringskonto. Om du vill hitta namnet på ditt lagringskonto använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?#az-storage-account-list)
- `--resource-id`: Skicka resurs-ID:t för ditt lagringskonto, som har formen `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Du hittar ditt prenumerations-ID med hjälp av kommandot Azure CLI [az account list;](/cli/azure/account?#az-account-list) För att hitta namnet på ditt lagringskonto och resursgruppen för lagringskontot använder du kommandot Azure CLI [az storage account list.](/cli/azure/storage/account?#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Token för signatur för delad åtkomst

Du kan också be Key Vault att generera token för signaturer för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan ge klienter åtkomst till resurser i ditt lagringskonto utan att dela dina kontonycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagringsresurser utan att äventyra dina kontonycklar.

Kommandona i det här avsnittet utför följande åtgärder:

- Ange en signaturdefinition för delad åtkomst för ett `<YourSASDefinitionName>` konto. Definitionen anges på ett Key Vault lagringskonto `<YourStorageAccountName>` i nyckelvalvet `<YourKeyVaultName>` .
- Skapa en signaturtoken för delad åtkomst för blob-, fil-, tabell- och kötjänster. Token skapas för resurstyper, tjänst, container och objekt. Token skapas med alla behörigheter, över https, och med de angivna start- och slutdatumen.
- Ange en Key Vault signaturdefinition för delad åtkomst för hanterad lagring i valvet. Definitionen har mall-URI för den signaturtoken för delad åtkomst som skapades. Definitionen har signaturtypen för delad åtkomst `account` och är giltig i N dagar.
- Kontrollera att signaturen för delad åtkomst har sparats i nyckelvalvet som en hemlighet.

### <a name="create-a-shared-access-signature-token"></a>Skapa en signaturtoken för delad åtkomst

Skapa en signaturdefinition för delad åtkomst med hjälp av kommandot Azure CLI [az storage account generate-sas.](/cli/azure/storage/account?#az-storage-account-generate-sas) Den här åtgärden kräver `storage` behörigheterna `setsas` och .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
När åtgärden har slutförts kopierar du utdata.

```console
"se=2020-01-01&sp=***"
```

Dessa utdata skickas till `--template-uri` parametern i nästa steg.

### <a name="generate-a-shared-access-signature-definition"></a>Generera en definition för signatur för delad åtkomst

Använd kommandot Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) och skicka utdata från föregående steg till parametern för att skapa en `--template-uri` signaturdefinition för delad åtkomst.  Du kan ange det namn du väljer för `-n` parametern .

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera definitionen för signatur för delad åtkomst

Du kan kontrollera att signaturen för delad åtkomst har lagrats i nyckelvalvet med hjälp av kommandot Azure CLI [az keyvault storage sas-definition show.](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show)

Nu kan du använda [kommandot az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) och egenskapen för att visa innehållet i `id` hemligheten.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat.](/rest/api/keyvault/)
- Läs artiklarna på [Azure Key Vault teambloggen](/archive/blogs/kv/).
- Se [referensdokumentationen för az keyvault](/cli/azure/keyvault/storage) storage.
