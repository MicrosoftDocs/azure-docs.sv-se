---
title: 'Använd .NET för att ange ACL: er i Azure Data Lake Storage Gen2'
description: Använd .NET för att hantera åtkomst kontrol listor (ACL) i lagrings konton med hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654481"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd .NET för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder .NET för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer.

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt.

[Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [REKURSIV ACL-exempel](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [API-referens](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel.

## <a name="set-up-your-project"></a>Konfigurera projektet

Kom igång genom att installera NuGet-paketet för [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

1. Öppna ett kommando fönster (till exempel: Windows PowerShell).

2. Från projekt katalogen ska du installera paketet Azure. Storage. files. DataLake. för hands version med hjälp av `dotnet add package` kommandot.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Lägg sedan till dessa med-instruktioner överst i din kod fil.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll modell i Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

> [!NOTE]
> Fler exempel finns i dokumentationen [för Azure Identity Client library för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto.

I det här exemplet skapas en [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -instans med hjälp av en konto nyckel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Ange ACL: er

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er](#update-acls) i den här artikeln.  

Om du väljer att *Ange* ACL måste du lägga till en post för den ägande användaren, en post för den ägande gruppen och en post för alla andra användare. Mer information om ägande användare, ägande grupp och andra användare finns i [användare och identiteter](data-lake-storage-access-control.md#users-and-identities).

I det här avsnittet visas hur du:

- Ange ACL för en katalog
- Ange ACL för en fil
- Ange ACL:er rekursivt

### <a name="set-the-acl-of-a-directory"></a>Ange ACL för en katalog

Hämta ACL (Access Control List) för en katalog genom att anropa metoden [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Det här exemplet hämtar och anger ACL: en för en katalog med namnet `my-directory` . Strängen `user::rwx,group::r-x,other::rw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `""` ) till metoden [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>Ange ACL för en fil

Hämta ACL (Access Control List) för en fil genom att anropa metoden [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) och ange ACL genom att anropa metoden [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

Det här exemplet hämtar och anger ACL: en för en fil med namnet `my-file.txt` . Strängen `user::rwx,group::r-x,other::rw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Ange ACL:er rekursivt

Ange ACL: er rekursivt genom att anropa metoden **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post.

Om du vill ange en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**. 

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill ange standard-ACL. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn. Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="update-acls"></a>Uppdatera ACL: er

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Om du vill ersätta ACL: en i stället för att uppdatera den, se avsnittet [ange ACL: er](#set-acls) i den här artikeln.

I det här avsnittet visas hur du:

- Uppdatera en ACL
- Uppdatera ACL: er rekursivt

### <a name="update-an-acl"></a>Uppdatera en ACL

Börja med att hämta ACL för en katalog genom att anropa metoden [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Kopiera listan över ACL-poster till en ny [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) -objekt. Leta sedan upp den post som du vill uppdatera och ersätt den i listan. Ange ACL genom att anropa metoden [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Det här exemplet uppdaterar rot-ACL: en för en behållare genom att ersätta ACL-posten för alla andra användare. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Uppdatera ACL: er rekursivt

Om du vill uppdatera en ACL rekursivt, skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras.

Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post.

Om du vill uppdatera en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**.

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill uppdatera standard-ACL: en. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Om du vill se ett exempel som uppdaterar ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="remove-acl-entries"></a>Ta bort ACL-poster

Du kan ta bort en eller flera ACL-poster. I det här avsnittet visas hur du:

- Ta bort en ACL-post
- Ta bort ACL-poster rekursivt

### <a name="remove-an-acl-entry"></a>Ta bort en ACL-post

Börja med att hämta ACL för en katalog genom att anropa metoden [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Kopiera listan över ACL-poster till en ny [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) -objekt. Leta sedan upp posten som du vill ta bort och anropa metoden [Remove](/dotnet/api/system.collections.ilist.remove) för samlingen. Ange den uppdaterade ACL: en genom att anropa metoden [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Det här exemplet uppdaterar rot-ACL: en för en behållare genom att ersätta ACL-posten för alla andra användare. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Om du vill ta bort ACL-poster rekursivt, skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort. 

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Skicka den här metoden en [lista](/dotnet/api/system.collections.generic.list-1) över [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Varje [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definierar en ACL-post. 

Om du vill ta bort en **standard** -ACL-post kan du ange egenskapen [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) för [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) till **True**. 

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om posten ska tas bort från standard-ACL: en. Den parametern används i [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem)-konstruktorn.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Om du vill se ett exempel som tar bort ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel när du ändrar ACL rekursivt. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde `null` för parametern för en tilläggs-token. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

För att säkerställa att processen slutförs utan avbrott skickar du ett **AccessControlChangedOptions** -objekt och anger egenskapen **ContinueOnFailure** för objektet till ``true`` .

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Om du vill se ett exempel som anger ACL rekursivt i batchar genom att ange en batchstorlek, se .NET- [exemplet](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Se även

- [Referensdokumentation för API](/dotnet/api/azure.storage.files.datalake)
- [Paket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Ge feedback](https://github.com/Azure/azure-sdk-for-net/issues)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)