---
title: Azure Key Vault hanterat lagrings konto – PowerShell-version
description: Funktionen hanterat lagrings konto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3bced101516e91259ea9018fe3c4aa44f867cbe6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023116"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Hantera lagrings konto nycklar med Key Vault och Azure PowerShell
> [!IMPORTANT]
> Vi rekommenderar att du använder Azure Storage-integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade identitets-och åtkomst hanterings tjänst. Azure AD-integrering är tillgänglig för [Azure-blobbar och köer](../../storage/common/storage-auth-aad.md)och ger OAuth2-tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault).
> Med Azure AD kan du autentisera klient programmet med hjälp av ett program eller en användar identitet, i stället för autentiseringsuppgifterna för lagrings kontot. Du kan använda en [hanterad Azure AD-identitet](../../active-directory/managed-identities-azure-resources/index.yml) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagrar autentiseringsuppgifter i eller med ditt program. Använd endast nedanstående lösning när det inte går att använda Azure AD-autentisering.

Ett Azure Storage-konto använder autentiseringsuppgifter bestående av ett konto namn och en nyckel. Nyckeln genereras automatiskt och fungerar som ett lösen ord i stället för en kryptografisk nyckel. Key Vault hanterar lagrings konto nycklar genom att regelbundet återskapa dem i lagrings kontot och tillhandahålla signaturer för delad åtkomst för delegerad åtkomst till resurser i ditt lagrings konto.

Du kan använda den Key Vault hanterade lagrings konto nyckeln för att lista (synkronisera) nycklar med ett Azure Storage-konto och återskapar (rotera) nycklarna med jämna mellanrum. Du kan hantera nycklar för både lagrings konton och klassiska lagrings konton.

Tänk på följande när du använder funktionen för hanterad lagrings konto nyckel:

- Nyckel värden returneras aldrig som svar på en anropare.
- Endast Key Vault bör hantera dina lagrings konto nycklar. Hantera inte nycklarna själv och Undvik att störa Key Vault processer.
- Endast ett enda Key Vault-objekt bör hantera lagrings konto nycklar. Tillåt inte nyckel hantering från flera objekt.
- Återskapa nycklar med hjälp av Key Vault. Återskapa inte dina lagrings konto nycklar manuellt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Program-ID för tjänstens huvudnamn

En Azure AD-klient tillhandahåller varje registrerat program med ett [huvud namn för tjänsten](../../active-directory/develop/developer-glossary.md#service-principal-object). Tjänstens huvud namn fungerar som program-ID, som används vid konfiguration av auktorisering för åtkomst till andra Azure-resurser via Azure RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Moln | Program-ID |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Annat  | Valfri | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du först göra följande:

- [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).
- [Skapa ett nyckelvalv](quick-create-powershell.md)
- [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md?tabs=azure-powershell). Lagrings konto namnet får bara innehålla gemena bokstäver och siffror. Namnet måste innehålla mellan 3 och 24 tecken.


## <a name="manage-storage-account-keys"></a>Hantera lagrings konto nycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera din PowerShell-session med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

```azurepowershell-interactive
Connect-AzAccount
```
Om du har flera Azure-prenumerationer kan du lista dem med hjälp av cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) och ange den prenumeration som du vill använda med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdletarna i följande steg. Se till att uppdatera plats hållarna "YourResourceGroupName", "YourStorageAccountName" och "YourKeyVaultName" och ange $keyVaultSpAppId till `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (enligt vad som anges i [program-ID för tjänstens huvud namn](#service-principal-application-id)ovan).

Vi kommer också att använda Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext) -och [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) -cmdlet: ar för att hämta ditt användar-ID och kontexten för ditt Azure Storage-konto.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> För klassiskt lagrings konto använder du "primär" och "sekundär" för $storageAccountKey <br>
> Använd "Get-AzResource-Name" ClassicStorageAccountName "-ResourceGroupName $resourceGroupName" i stället of'Get-AzStorageAccount "för det klassiska lagrings kontot

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagrings konto

Innan Key Vault kan komma åt och hantera dina lagrings konto nycklar måste du ge åtkomst till ditt lagrings konto. Key Vault programmet måste ha behörighet att *lista* och *Återskapa* nycklar för ditt lagrings konto. Dessa behörigheter aktive ras via den inbyggda Azure-rollen [lagrings konto nyckel operatör](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role).

Tilldela den här rollen till Key Vault tjänstens huvud namn, begränsa omfattningen till ditt lagrings konto med hjälp Azure PowerShell av cmdleten [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) .

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Vid lyckad roll tilldelning bör du se utdata som liknar följande exempel:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Om Key Vault redan har lagts till i rollen på ditt lagrings konto får du en *"roll tilldelningen finns redan".* error. Du kan också kontrol lera roll tilldelningen med hjälp av sidan för lagrings kontots åtkomst kontroll (IAM) i Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge ditt användar konto behörighet till hanterade lagrings konton

Använd Azure PowerShell [set-AzKeyVaultAccessPolicy-cmdlet:](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) en för att uppdatera Key Vault åtkomst princip och tilldela lagrings konto behörigheter till ditt användar konto.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observera att behörigheter för lagrings konton inte är tillgängliga på sidan åtkomst principer för lagrings kontot i Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Lägg till ett hanterat lagrings konto till din Key Vault-instans

Använd cmdleten Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) för att skapa ett hanterat lagrings konto i Key Vault-instansen. `-DisableAutoRegenerateKey`Växeln anger att lagrings konto nycklarna inte ska återskapas.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

När du lägger till lagrings kontot utan att skapa nya nycklar bör du se utdata som liknar följande exempel:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>Aktivera nyckel återskapande

Om du vill Key Vault återskapa dina lagrings konto nycklar med jämna mellanrum kan du använda cmdleten Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) för att ange en återställnings period. I det här exemplet ställer vi in en tids period på tre dagar. När det är dags att rotera återskapar Key Vault den nyckel som inte är aktiv och anger sedan den nya nyckeln som aktiv. Endast en av nycklarna används för att utfärda SAS-token vid ett och samma tillfälle. Det här är den aktiva nyckeln.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

När du har tillrättat tillägget av lagrings kontot när nyckeln har återskapats bör du se utdata som liknar följande exempel:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>Token för signaturer för delad åtkomst

Du kan också be Key Vault att generera token för signaturer för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto. Du kan bevilja klienter åtkomst till resurser i ditt lagrings konto utan att dela dina konto nycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagrings resurser utan att kompromissa med dina konto nycklar.

Kommandona i det här avsnittet Slutför följande åtgärder:

- Ange en definition för signaturen för delad åtkomst till kontot.
- Skapa en signatur-token för delad åtkomst för BLOB-, fil-, tabell-och Queue-tjänster. Token skapas för resurs typ tjänst, behållare och objekt. Token skapas med alla behörigheter, över https, och med de angivna start-och slutdatumen.
- Ange en definition av signaturen för delad åtkomst för en Key Vault hanterad lagring i valvet. Definitionen har mall-URI: n för signaturen för signaturen för delad åtkomst som skapades. Definitionen har signaturen för delad åtkomst `account` och är giltig i N dagar.
- Kontrol lera att signaturen för delad åtkomst har sparats i nyckel valvet som en hemlighet.
-
### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdletarna i följande steg. Se till att uppdatera <YourStorageAccountName> <YourKeyVaultName> plats hållarna och.

Vi kommer också att använda Azure PowerShell cmdlets för [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) för att skapa en kontext för ditt Azure Storage-konto.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Skapa en token för signatur för delad åtkomst

Skapa en definition av signaturen för delad åtkomst med hjälp av Azure PowerShell cmdletarna [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) .

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Värdet för $sasToken ser ut ungefär så här.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generera en definition för signatur för delad åtkomst

Använd cmdleten Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) för att skapa en definition av en signatur för delad åtkomst.  Du kan ange namnet på ditt val av- `-Name` parameter.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera definitionen av signaturen för delad åtkomst

Du kan kontrol lera att definitionen av signaturen för delad åtkomst har lagrats i ditt nyckel valv med hjälp av cmdleten Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) .

Börja med att söka efter signaturen för signatur för delad åtkomst i ditt nyckel valv.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Den hemlighet som motsvarar din SAS-definition kommer att ha följande egenskaper:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Nu kan du använda cmdleten [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) med `VaultName` parametrarna och `Name` för att visa innehållet i den hemligheten.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Utdata från det här kommandot visar din SAS-definitions sträng.

## <a name="next-steps"></a>Nästa steg

- [Nyckel exempel för hanterade lagrings konton](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell-referens](/powershell/module/az.keyvault/#key_vault)
