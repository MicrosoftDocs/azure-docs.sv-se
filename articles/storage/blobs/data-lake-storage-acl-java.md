---
title: 'Använd Java för att ange ACL: er i Azure Data Lake Storage Gen2'
description: Använd Azure Storage bibliotek för Java för att hantera åtkomst kontrol listor (ACL) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654478"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd Java för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder Java för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer. 

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. 

[Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-referens](/java/api/overview/azure/storage-file-datalake-readme)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel.

## <a name="set-up-your-project"></a>Konfigurera projektet

Kom igång genom att öppna [den här sidan](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) och hitta den senaste versionen av Java-biblioteket. Öppna sedan *pom.xml* -filen i text redigeraren. Lägg till ett beroende element som refererar till den versionen.

Om du planerar att autentisera ditt klient program med hjälp av Azure Active Directory (AD) lägger du till ett beroende till klient biblioteket för Azure Secret. Se  [lägga till det hemliga klient biblioteks paketet i projektet](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Lägg sedan till dessa import uttryck i din kod fil.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Anslut med hjälp av Azure Active Directory (Azure AD)

Du kan använda [klient biblioteket för Azure Identity för Java för](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) att autentisera ditt program med Azure AD.

Hämta ett klient-ID, en klient hemlighet och ett klient-ID. Information om hur du gör detta finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den processen måste du tilldela en av följande roller för [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) till ditt säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity-klienten för Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -dokumentation.

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto. 

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Ange ACL: er

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er](#update-acls) i den här artikeln.  

Om du väljer att *Ange* ACL måste du lägga till en post för den ägande användaren, en post för den ägande gruppen och en post för alla andra användare. Mer information om ägande användare, ägande grupp och andra användare finns i [användare och identiteter](data-lake-storage-access-control.md#users-and-identities).

I det här avsnittet visas hur du:

- Ange ACL för en katalog
- Ange ACL för en fil
- Ange ACL:er rekursivt 

### <a name="set-the-acl-of-a-directory"></a>Ange ACL för en katalog

Det här exemplet hämtar och anger sedan ACL för en katalog med namnet `my-directory` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `""` ) till metoden **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>Ange ACL för en fil

Det här exemplet hämtar och anger sedan ACL för en fil med namnet `upload-file.txt` . Det här exemplet ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs behörighet.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Ange ACL:er rekursivt

Ange ACL: er rekursivt genom att anropa metoden **DataLakeDirectoryClient. setAccessControlRecursive** . Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post.

Om du vill ange en **standard** -ACL-post kan du anropa **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**. 

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill ange standard-ACL. Den parametern används i varje anrop till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Uppdatera ACL: er

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Om du vill ersätta ACL: en i stället för att uppdatera den, se avsnittet [ange ACL: er](#set-acls) i den här artikeln.

I det här avsnittet visas hur du:

- Uppdatera en ACL
- Uppdatera ACL: er rekursivt

### <a name="update-an-acl"></a>Uppdatera en ACL

Börja med att hämta ACL för en katalog genom att anropa metoden [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Kopiera listan över ACL-poster till ett nytt **list** objekt av typen [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Leta sedan upp den post som du vill uppdatera och ersätt den i listan. Ange ACL genom att anropa metoden [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Det här exemplet uppdaterar ACL: en för en katalog med namnet `my-parent-directory` genom att ersätta posten för alla andra användare. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen skickar du en tom sträng ( `""` ) till metoden **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Uppdatera ACL: er rekursivt

Om du vill uppdatera en ACL rekursivt, skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras.

Uppdatera ACL: er rekursivt genom att anropa metoden **DataLakeDirectoryClient. updateAccessControlRecursive** .  Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post. 

Om du vill uppdatera en **standard** -ACL-post kan du **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**. 

Det här exemplet uppdaterar en ACL-post med Skriv behörighet. Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om du vill uppdatera standard-ACL: en. Den parametern används i anropet till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Ta bort ACL-poster

Du kan ta bort en eller flera ACL-poster. I det här avsnittet visas hur du:

- Ta bort en ACL-post
- Ta bort ACL-poster rekursivt

### <a name="remove-an-acl-entry"></a>Ta bort en ACL-post

Börja med att hämta ACL för en katalog genom att anropa metoden [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Kopiera listan över ACL-poster till ett nytt **list** objekt av typen [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Leta sedan upp posten som du vill ta bort och anropa metoden **Remove** för **list** -objektet. Ange den uppdaterade ACL: en genom att anropa metoden [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Om du vill ta bort ACL-poster rekursivt, skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort.

Ta bort ACL-poster genom att anropa metoden **DataLakeDirectoryClient. removeAccessControlRecursive** . Skicka den här metoden en [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) över [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt. Varje [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definierar en ACL-post. 

Om du vill ta bort en **standard** -ACL-post kan du **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) och skicka värdet **Sant**.  

Det här exemplet tar bort en ACL-post från ACL: en för katalogen med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `isDefaultScope` som anger om posten ska tas bort från standard-ACL: en. Den parametern används i anropet till **setDefaultScope** -metoden för [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde `null` för parametern för en tilläggs-token.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

För att säkerställa att processen slutförs utan avbrott anropar du **setContinueOnFailure** -metoden för ett [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objekt och skickar värdet **Sant**.

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Se även

- [Referensdokumentation för API](/java/api/overview/azure/storage-file-datalake-readme)
- [Paket (maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Lämna feedback](https://github.com/Azure/azure-sdk-for-java/issues)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)