---
title: 'Ange ACL: er rekursivt för Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Du kan lägga till, uppdatera och ta bort åtkomst kontrol listor rekursivt för befintliga underordnade objekt i en överordnad katalog.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 6b48b156ca8d4c64d26d96d7bed525f251832554
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98046064"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Ange åtkomst kontrol listor (ACL) rekursivt för Azure Data Lake Storage Gen2

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Nu kan du lägga till, uppdatera och ta bort ACL rekursivt för befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt.

[Bibliotek](#libraries)  |  [Exempel](#code-samples)  |  [Metod tips](#best-practice-guidelines)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Rätt behörighet för att köra den rekursiva ACL-processen. Rätt behörighet inkluderar något av följande: 

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.   

  - Ägande användare av den mål behållare eller katalog till vilken du planerar att tillämpa den rekursiva ACL-processen. Detta inkluderar alla underordnade objekt i mål behållaren eller katalogen. 

- En förståelse för hur ACL: er tillämpas på kataloger och filer. Se [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Se avsnittet **Konfigurera ditt projekt** i den här artikeln för att Visa installations vägledning för PowerShell, .NET SDK och python SDK.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera de nödvändiga biblioteken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Kontrol lera att PowerShell-versionen som har installerats är `5.1` eller högre genom att använda följande kommando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](/powershell/scripting/install/installing-windows-powershell)
    
2. Installera **AZ. Storage** -modulen.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera att den version av Azure CLI som har installerats är `2.14.0` eller högre genom att använda följande kommando.

   ```azurecli
    az --version
   ```
   Om din version av Azure CLI är lägre än `2.14.0` kan du installera en senare version. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Öppna ett kommando fönster (till exempel: Windows PowerShell).

2. Från projekt katalogen ska du installera paketet Azure. Storage. files. DataLake. för hands version med hjälp av `dotnet add package` kommandot.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Lägg till dessa med-instruktioner överst i din kod fil.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Kom igång genom att öppna [den här sidan](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) och hitta den senaste versionen av Java-biblioteket. Öppna sedan *pom.xml* -filen i text redigeraren. Lägg till ett beroende element som refererar till den versionen.

Om du planerar att autentisera ditt klient program med hjälp av Azure Active Directory (AD) lägger du till ett beroende till klient biblioteket för Azure Secret. Se  [lägga till det hemliga klient biblioteks paketet i projektet](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Lägg sedan till dessa import uttryck i din kod fil.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Ladda ned [Azure Data Lake Storage klient biblioteket för python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D).

2. Installera biblioteket som du laddade ned med hjälp av [pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Lägg till dessa import uttryck längst upp i din kod fil.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Anslut till ditt konto

Du kan ansluta genom att använda Azure Active Directory (AD) eller genom att använda en konto nyckel. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Med den här metoden säkerställer systemet att ditt användar konto har rätt Azure RBAC-tilldelning (Azure-rollbaserad åtkomst kontroll) och ACL-behörigheter. 

Följande tabell visar var och en av de roller som stöds och deras inställnings funktion för ACL.

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

1. Öppna ett Windows PowerShell-kommando fönster och logga sedan in på Azure-prenumerationen med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill skapa och hantera kataloger i. I det här exemplet ersätter du `<subscription-id>` plats hållarens värde med ID: t för din prenumeration.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```
3. Hämta lagrings konto kontexten.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

#### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Med den här metoden kontrollerar systemet inte Azure RBAC-eller ACL-behörigheter. Hämta lagrings kontots kontext med hjälp av en konto nyckel.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du använder Azure CLI lokalt kan du köra inloggnings kommandot.

   ```azurecli
   az login
   ```

   Om CLI kan öppna din standard webbläsare så gör den det och läser in en Azure-inloggnings sida.

   Annars öppnar du en webb sida på [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och anger den auktoriseringskod som visas i din terminal. Logga sedan in med dina konto uppgifter i webbläsaren.

   Mer information om olika autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

> [!NOTE]
> Exemplet som presenteras i den här artikeln visar Azure Active Directory (AD) auktorisering. Mer information om autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

### <a name="net"></a>[.NET](#tab/dotnet)

Om du vill använda kodfragmenten i den här artikeln måste du skapa en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans som representerar lagrings kontot.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Du kan använda [Azure Identity client-biblioteket för .net för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) att autentisera ditt program med Azure AD.

När du har installerat paketet lägger du till denna using-instruktion överst i din kod fil.

```csharp
using Azure.Identity;
```

Hämta ett klient-ID, en klient hemlighet och ett klient-ID. Information om hur du gör detta finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den processen måste du tilldela en av följande roller för [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) till ditt säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

I det här exemplet skapas en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

#### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Den här metoden är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans med hjälp av en konto nyckel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> Fler exempel finns i dokumentationen [för Azure Identity Client library för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

#### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      
```

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [klient biblioteket för Azure Identity för Java för](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md).

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity-klienten för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -dokumentation.

### <a name="python"></a>[Python](#tab/python)

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

Du kan använda [klient biblioteket för Azure Identity för python för](https://pypi.org/project/azure-identity/) att autentisera ditt program med Azure AD.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  Om du vill hämta dessa värden läser du [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den processen måste du tilldela en av följande roller för [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) till ditt säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity för python](https://pypi.org/project/azure-identity/) -dokumentation.

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` placeholder-värdet med din åtkomst nyckel för lagrings kontot.

---

## <a name="set-an-acl-recursively"></a>Ange en ACL rekursivt

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera en ACL rekursivt](#update-an-acl-recursively) i den här artikeln.  

Om du väljer att *Ange* ACL måste du lägga till en post för den ägande användaren, en post för den ägande gruppen och en post för alla andra användare. Mer information om ägande användare, ägande grupp och andra användare finns i [användare och identiteter](data-lake-storage-access-control.md#users-and-identities). 

Det här avsnittet innehåller exempel på hur du ställer in en ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ange en ACL rekursivt med hjälp av cmdleten **set-AzDataLakeGen2AclRecursive** .

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Om du vill ange en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ange en ACL rekursivt med hjälp av kommandot [AZ Storage FS-åtkomst uppsättning-rekursivt](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill ange en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Exempel: `default:user::rwx` eller `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Ange en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post. 

Om du vill ange en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**. 

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill ange standard-ACL. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn. Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Ange en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. setAccessControlRecursive** . Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post. 

Om du vill ange en **standard** -ACL-post kan du anropa **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**. 

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill ange standard-ACL. Den parametern används i varje anrop till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```java
static public void SetACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){
    
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create owner entry.
    PathAccessControlEntry ownerEntry = new PathAccessControlEntry();

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    ownerEntry.setDefaultScope(isDefaultScope);
    ownerEntry.setAccessControlType(AccessControlType.USER);
    ownerEntry.setPermissions(ownerPermission);

    pathAccessControlEntries.add(ownerEntry);

    // Create group entry.
    PathAccessControlEntry groupEntry = new PathAccessControlEntry();

    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    groupEntry.setDefaultScope(isDefaultScope);
    groupEntry.setAccessControlType(AccessControlType.GROUP);
    groupEntry.setPermissions(groupPermission);

    pathAccessControlEntries.add(groupEntry);

    // Create other entry.
    PathAccessControlEntry otherEntry = new PathAccessControlEntry();

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setExecutePermission(false).setReadPermission(false).setWritePermission(false);

    otherEntry.setDefaultScope(isDefaultScope);
    otherEntry.setAccessControlType(AccessControlType.OTHER);
    otherEntry.setPermissions(otherPermission);

    pathAccessControlEntries.add(otherEntry);

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(false);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.setAccessControlRecursive(pathAccessControlEntries);        

}
```

### <a name="python"></a>[Python](#tab/python)

Ange en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient.set_access_control_recursive** .

Om du vill ange en **standard** -ACL-post lägger du till strängen `default:` i början av varje ACL-anslutningssträng. 

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . 

Den här metoden accepterar en boolesk parameter med namnet `is_default_scope` som anger om du vill ange standard-ACL. om den parametern är `True` , föregås listan över ACL-poster med strängen `default:` . 

Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet. Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="update-an-acl-recursively"></a>Uppdatera en ACL rekursivt

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Information om hur du ersätter ACL i stället för att uppdatera den finns i avsnittet [Ange en ACL rekursivt](#set-an-acl-recursively) i den här artikeln. 

Om du vill uppdatera en ACL skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras.

Det här avsnittet innehåller exempel på hur du uppdaterar en ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Uppdatera en ACL rekursivt med hjälp av cmdleten  **Update-AzDataLakeGen2AclRecursive** . 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Om du vill uppdatera en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Om du vill se ett exempel som uppdaterar ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) referens.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Uppdatera en ACL rekursivt med hjälp av kommandot  [AZ Storage FS Access Update-rekursivt](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill uppdatera en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Ett exempel är `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post. 

Om du vill uppdatera en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**. 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill uppdatera standard-ACL: en. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Om du vill se ett exempel som uppdaterar ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. updateAccessControlRecursive** .  Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post. 

Om du vill uppdatera en **standard** -ACL-post kan du **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**. 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill uppdatera standard-ACL: en. Den parametern används i anropet till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

```java
static public void UpdateACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathAccessControlEntry> pathAccessControlEntries = 
        new ArrayList<PathAccessControlEntry>();

    // Create named user entry.
    PathAccessControlEntry userEntry = new PathAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx");
    userEntry.setPermissions(userPermission);    
    
    pathAccessControlEntries.add(userEntry);
    
    directoryClient.updateAccessControlRecursive(pathAccessControlEntries);          
}
```

### <a name="python"></a>[Python](#tab/python)

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient.update_access_control_recursive** . Om du vill uppdatera en **standard** -ACL-post lägger du till strängen `default:` i början av varje ACL-anslutningssträng. 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet.

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `is_default_scope` som anger om du vill uppdatera standard-ACL: en. om den parametern är `True` , föregås den uppdaterade ACL-posten med strängen `default:` .  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Du kan ta bort en eller flera ACL-poster rekursivt. Om du vill ta bort en ACL-post skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort. 

Det här avsnittet innehåller exempel på hur du tar bort en ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ta bort ACL-poster med hjälp av cmdleten **Remove-AzDataLakeGen2AclRecursive** . 

Det här exemplet tar bort en ACL-post från rot katalogen för behållaren.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Om du vill ta bort en **standard** -ACL-post använder du parametern **-DefaultScope** när du kör kommandot **set-AzDataLakeGen2ItemAclObject** . Exempel: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Om du vill se ett exempel som tar bort ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) referens.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta bort ACL-poster med hjälp av kommandot [AZ Storage FS Access Remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

Det här exemplet tar bort en ACL-post från rot katalogen för behållaren.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill ta bort en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Ett exempel är `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post. 

Om du vill ta bort en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**. 

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om posten ska tas bort från standard-ACL: en. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Om du vill se ett exempel som tar bort ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. removeAccessControlRecursive** . Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post. 

Om du vill ta bort en **standard** -ACL-post kan du **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**.  

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om posten ska tas bort från standard-ACL: en. Den parametern används i anropet till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).


```java
static public void RemoveACLRecursively(DataLakeFileSystemClient fileSystemClient, Boolean isDefaultScope){

    DataLakeDirectoryClient directoryClient =
    fileSystemClient.getDirectoryClient("my-parent-directory");

    List<PathRemoveAccessControlEntry> pathRemoveAccessControlEntries = 
        new ArrayList<PathRemoveAccessControlEntry>();

    // Create named user entry.
    PathRemoveAccessControlEntry userEntry = new PathRemoveAccessControlEntry();

    RolePermissions userPermission = new RolePermissions();
    userPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    userEntry.setDefaultScope(isDefaultScope);
    userEntry.setAccessControlType(AccessControlType.USER);
    userEntry.setEntityId("xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"); 
    
    pathRemoveAccessControlEntries.add(userEntry);
    
    directoryClient.removeAccessControlRecursive(pathRemoveAccessControlEntries);      

}
```

### <a name="python"></a>[Python](#tab/python)

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient.remove_access_control_recursive** . Om du vill ta bort en **standard** -ACL-post lägger du till strängen `default:` i början av posten ACL-poststrängen. 

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `is_default_scope` som anger om posten ska tas bort från standard-ACL: en. om den parametern är `True` , föregås den uppdaterade ACL-posten med strängen `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Det här exemplet returnerar resultat till variabeln och rör sedan felaktiga poster till en formaterad tabell.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Baserat på utdata från tabellen kan du åtgärda eventuella behörighets fel och sedan återuppta körningen med hjälp av en fortsättnings-token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om ett problem uppstår kan du returnera en fortsättnings-token genom att ange `--continue-on-failure` parametern till `false` . När du har åtgärdat felen kan du återuppta processen från fel punkten genom att köra kommandot igen och sedan ange parametern till den över gångs- `--continuation` token. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde `null` för parametern för en tilläggs-token. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde `null` för parametern för en tilläggs-token. 

```java
static public String ResumeSetACLRecursively(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList, 
String continuationToken){

    try{
        PathSetAccessControlRecursiveOptions options = new PathSetAccessControlRecursiveOptions(accessControlList);
        
        options.setContinuationToken(continuationToken);
    
        Response<AccessControlChangeResult> accessControlChangeResult =  
            directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

        if (accessControlChangeResult.getValue().getCounters().getFailedChangesCount() > 0)
        {
            continuationToken =
                accessControlChangeResult.getValue().getContinuationToken();
        }
    
        return continuationToken;

    }
    catch(Exception ex){
    
        System.out.println(ex.toString());
        return continuationToken;
    }
}
```

### <a name="python"></a>[Python](#tab/python)

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde ``None`` för parametern för en tilläggs-token. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I det här exemplet används `ContinueOnFailure` parametern så att körningen fortsätter även om åtgärden påträffar ett behörighets fel. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se artikeln [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) referens.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ange parametern till om du vill se till att processen slutförs utan avbrott `--continue-on-failure` `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

För att säkerställa att processen slutförs utan avbrott skickar du ett **AccessControlChangedOptions** -objekt och anger egenskapen **ContinueOnFailure** för objektet till ``true`` .

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

### <a name="java"></a>[Java](#tab/java)

För att säkerställa att processen slutförs utan avbrott anropar du **setContinueOnFailure** -metoden för ett [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt och skickar värdet **Sant**.

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen. 

```java
static public void ContinueOnFailure(DataLakeFileSystemClient fileSystemClient,
DataLakeDirectoryClient directoryClient,
List<PathAccessControlEntry> accessControlList){
    
    PathSetAccessControlRecursiveOptions options = 
        new PathSetAccessControlRecursiveOptions(accessControlList);
        
    options.setContinueOnFailure(true);
    
    Response<AccessControlChangeResult> accessControlChangeResult =  
        directoryClient.setAccessControlRecursiveWithResponse(options, null, null);

    AccessControlChangeCounters counters = accessControlChangeResult.getValue().getCounters();

    System.out.println("Number of directories changes: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of files changed: " + 
        counters.getChangedDirectoriesCount());

    System.out.println("Number of failures: " + 
        counters.getChangedDirectoriesCount());
}
```

### <a name="python"></a>[Python](#tab/python)

För att säkerställa att processen slutförs utan avbrott ska du inte skicka en fortsättnings-token till **DataLakeDirectoryClient.set_access_control_recursive** -metoden.

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen. 

```python
def continue_on_failure():
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r--'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl)

        print("Summary: {} directories and {} files were updated successfully, {} failures were counted."
          .format(acl_change_result.counters.directories_successful, acl_change_result.counters.files_successful,
                  acl_change_result.counters.failure_count))
        
    except Exception as e:
     print(e)
```

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

---

## <a name="resources"></a>Resurser

Det här avsnittet innehåller länkar till bibliotek och kod exempel.

#### <a name="libraries"></a>Bibliotek

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [Azure CLI](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Kodexempel

- PowerShell: [](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [exempel](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D) på README

- Azure CLI: [exempel](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: [README](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)-  |  [exempel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: [README](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)-  |  [exempel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Rikt linjer för bästa praxis

Det här avsnittet innehåller rikt linjer för bästa praxis för att ställa in ACL: er rekursivt. 

#### <a name="handling-runtime-errors"></a>Hantera körnings fel

Ett körnings fel kan uppstå i många olika orsaker (till exempel: ett avbrott eller problem med klient anslutning). Om du stöter på ett körnings fel startar du om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="handling-permission-errors-403"></a>Hantering av behörighets fel (403)

Om du stöter på ett åtkomst kontroll undantag när du kör en rekursiv ACL-process kanske ditt [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) i AD inte har tillräcklig behörighet för att tillämpa en ACL på ett eller flera av de underordnade objekten i mapphierarkin. När ett behörighets fel inträffar stoppas processen och en fortsättnings-token anges. Åtgärda behörighets problemet och Använd sedan den fortsättnings-token för att bearbeta den återstående data mängden. De kataloger och filer som redan har bearbetats behöver inte bearbetas igen. Du kan också välja att starta om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="credentials"></a>Autentiseringsuppgifter 

Vi rekommenderar att du etablerar ett säkerhets objekt för Azure AD som har tilldelats rollen som [lagrings-BLOB-dataägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för mål lagrings kontot eller behållaren. 

#### <a name="performance"></a>Prestanda 

För att minska svars tiden rekommenderar vi att du kör den rekursiva ACL-processen på en virtuell Azure-dator (VM) som finns i samma region som ditt lagrings konto. 

#### <a name="acl-limits"></a>ACL-gränser

Det maximala antalet ACL: er som du kan tillämpa på en katalog eller fil är 32 åtkomst-ACL: er och 32 standard-ACL: er. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Se även

- [Åtkomstkontroll i Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
- [Kända problem](data-lake-storage-known-issues.md)