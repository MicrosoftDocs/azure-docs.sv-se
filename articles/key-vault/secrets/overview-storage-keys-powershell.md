---
title: Azure Key Vault hanterat lagringskonto – PowerShell-version
description: Funktionen för hanterat lagringskonto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748492"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Hantera lagringskontonycklar med Key Vault och Azure PowerShell
> [!IMPORTANT]
> Vi rekommenderar Azure Storage integrering med Azure Active Directory (Azure AD), Microsofts molnbaserade tjänst för identitets- och åtkomsthantering. Azure AD-integrering är tillgängligt för [Azure-blobar](../../storage/common/storage-auth-aad.md)och köer och ger OAuth2-tokenbaserad åtkomst till Azure Storage (precis som Azure Key Vault).
> Med Azure AD kan du autentisera klientprogrammet med hjälp av ett program eller en användaridentitet i stället för autentiseringsuppgifterna för lagringskontot. Du kan använda en [hanterad Azure AD-identitet](../../active-directory/managed-identities-azure-resources/index.yml) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagring av autentiseringsuppgifter i eller med ditt program. Använd endast lösningen nedan när Azure AD-autentisering inte är möjligt.

Ett Azure Storage-konto använder autentiseringsuppgifter som består av ett kontonamn och en nyckel. Nyckeln är automatiskt genererad och fungerar som ett lösenord, i stället för en som en kryptografisk nyckel. Key Vault hanterar lagringskontonycklar genom att regelbundet återskapa dem i lagringskontot och tillhandahåller signaturer för delad åtkomst för delegerad åtkomst till resurser i ditt lagringskonto.

Du kan använda funktionen Key Vault för hanterade lagringskontonycklar för att lista (synkronisera) nycklar med ett Azure-lagringskonto och återskapa (rotera) nycklarna regelbundet. Du kan hantera nycklar för både lagringskonton och klassiska lagringskonton.

Tänk på följande när du använder nyckelfunktionen för hanterat lagringskonto:

- Nyckelvärden returneras aldrig som svar på en anropare.
- Endast Key Vault hantera dina lagringskontonycklar. Hantera inte nycklarna själv och undvik att störa Key Vault processer.
- Endast ett enskilt Key Vault ska hantera lagringskontonycklar. Tillåt inte nyckelhantering från flera objekt.
- Återskapa nycklar med hjälp Key Vault endast. Återskapa inte dina lagringskontonycklar manuellt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Program-ID för tjänstens huvudnamn

En Azure AD-klientorganisation ger varje registrerat program ett [huvudnamn för tjänsten.](../../active-directory/develop/developer-glossary.md#service-principal-object) Tjänstens huvudnamn fungerar som program-ID som används vid auktoriseringskonfigurationen för åtkomst till andra Azure-resurser via Azure RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID i varje Azure-moln.

| Klientorganisationer | Moln | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Övrigt  | Valfri | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden måste du först göra följande:

- [Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).
- [Skapa ett nyckelvalv](quick-create-powershell.md)
- [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md?tabs=azure-powershell). Lagringskontots namn får bara innehålla gemener och siffror. Namnet måste vara mellan 3 och 24 tecken långt.


## <a name="manage-storage-account-keys"></a>Hantera lagringskontonycklar

### <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto

Autentisera Din PowerShell-session med [hjälp av cmdleten Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```azurepowershell-interactive
Connect-AzAccount
```
Om du har flera Azure-prenumerationer kan du visa dem med hjälp av cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) och ange den prenumeration som du vill använda med cmdleten [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdletarna i följande steg. Se till att uppdatera platshållarna "YourResourceGroupName", "YourStorageAccountName" och "YourKeyVaultName" och ange $keyVaultSpAppId till (enligt vad som anges i program-ID för tjänstens huvudnamn `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` , ovan). [](#service-principal-application-id)

Vi använder även cmdletarna Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext) och [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) för att hämta ditt användar-ID och kontexten för ditt Azure Storage-konto.

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
> För klassiskt lagringskonto använder du "primär" och "sekundär" för $storageAccountKey <br>
> Använd "Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName" i stället för "Get-AzStorageAccount" för klassiskt lagringskonto

### <a name="give-key-vault-access-to-your-storage-account"></a>Ge Key Vault åtkomst till ditt lagringskonto

Innan Key Vault kan komma åt och hantera dina lagringskontonycklar måste du ge åtkomst till lagringskontot. Programmet Key Vault kräver behörighet att lista *och* *återskapa nycklar* för ditt lagringskonto. Dessa behörigheter aktiveras via den inbyggda Azure-rollen [Tjänstroll för lagringskontonyckeloperatör.](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)

Tilldela den här rollen Key Vault tjänstens huvudnamn, vilket begränsar omfånget till ditt lagringskonto med hjälp Azure PowerShell cmdleten [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment)

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Vid lyckad rolltilldelning bör du se utdata som liknar följande exempel:

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

Om Key Vault redan har lagts till i rollen på ditt lagringskonto får du ett meddelande om *att rolltilldelningen redan finns.* error. Du kan också verifiera rolltilldelningen med hjälp av lagringskontots sida "Åtkomstkontroll (IAM)" i Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Ge användarkontot behörighet till hanterade lagringskonton

Använd cmdleten [Azure PowerShell Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att uppdatera Key Vault-åtkomstprincipen och bevilja lagringskontobehörigheter till ditt användarkonto.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Observera att behörigheter för lagringskonton inte är tillgängliga på sidan Åtkomstprinciper för lagringskontot i Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Lägga till ett hanterat lagringskonto till din Key Vault instans

Använd cmdlet Azure PowerShell en [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) för att skapa ett hanterat lagringskonto i din Key Vault instans. Växeln  `-DisableAutoRegenerateKey` anger INTE att lagringskontonycklarna ska återskapas.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

När lagringskontot har tillägg utan nyckelgenerering bör du se utdata som liknar följande exempel:

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

### <a name="enable-key-regeneration"></a>Aktivera återskapande av nycklar

Om du Key Vault vill återskapa lagringskontonycklarna med jämna mellanrum kan du använda cmdleten Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) för att ange en återskapandeperiod. I det här exemplet anger vi en återskapandeperiod på tre dagar. När det är dags att rotera Key Vault den nyckel som inte är aktiv och anger sedan den nya nyckeln som aktiv. Endast en av nycklarna används för att utfärda SAS-token åt gången. Det här är den aktiva nyckeln.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

När lagringskontot har tilläggts med nyckelåtergenerering bör du se utdata som liknar följande exempel:

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

## <a name="shared-access-signature-tokens"></a>Token för signatur för delad åtkomst

Du kan också be Key Vault att generera token för signaturer för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. Du kan ge klienter åtkomst till resurser i ditt lagringskonto utan att dela dina kontonycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagringsresurser utan att kompromissa med dina kontonycklar.

Kommandona i det här avsnittet utför följande åtgärder:

- Ange en signaturdefinition för delad åtkomst för ett konto.
- Skapa en signaturtoken för delad åtkomst för blob-, fil-, tabell- och kötjänster. Token skapas för resurstyper, tjänst, container och objekt. Token skapas med alla behörigheter, över https, och med de angivna start- och slutdatumen.
- Ange en Key Vault signaturdefinition för delad åtkomst för hanterad lagring i valvet. Definitionen har mall-URI för den signaturtoken för delad åtkomst som skapades. Definitionen har signaturtypen för delad åtkomst `account` och är giltig i N dagar.
- Kontrollera att signaturen för delad åtkomst har sparats i nyckelvalvet som en hemlighet.
-
### <a name="set-variables"></a>Ange variabler

Ange först de variabler som ska användas av PowerShell-cmdletarna i följande steg. Se till att uppdatera <YourStorageAccountName> <YourKeyVaultName> platshållarna och .

Vi använder även cmdletarna Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) för att hämta kontexten för ditt Azure Storage-konto.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Skapa en signaturtoken för delad åtkomst

Skapa en signaturdefinition för delad åtkomst med hjälp Azure PowerShell [cmdletarna New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken)

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

Använd cmdleten Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) för att skapa en signaturdefinition för delad åtkomst.  Du kan ange det namn du väljer för `-Name` parametern .

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifiera definitionen för signatur för delad åtkomst

Du kan kontrollera att definitionen för signaturen för delad åtkomst har lagrats i ditt nyckelvalv med hjälp Azure PowerShell [cmdleten Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret)

Leta först reda på definitionen för signatur för delad åtkomst i nyckelvalvet.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Hemligheten som motsvarar din SAS-definition har följande egenskaper:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Nu kan du använda cmdleten [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) med parametrarna och `VaultName` för att visa innehållet i `Name` hemligheten.

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

Utdata från det här kommandot visar SAS-definitionssträngen.

## <a name="next-steps"></a>Nästa steg

- [Exempel på nyckel för hanterat lagringskonto](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell-referens](/powershell/module/az.keyvault/#key_vault)
