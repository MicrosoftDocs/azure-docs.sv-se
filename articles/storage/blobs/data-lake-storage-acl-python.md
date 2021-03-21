---
title: 'Använd python för att hantera ACL: er i Azure Data Lake Storage Gen2'
description: Använd python hantera åtkomst kontrol listor (ACL) i lagrings konton som har hierarkiskt namn område (HNS) aktiverat.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654475"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd python för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder python för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer. 

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt. 

[Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Rekursiva ACL-exempel](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [API-referens](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Ge feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område (HNS) aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.6.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel.

## <a name="set-up-your-project"></a>Konfigurera projektet

Installera Azure Data Lake Storage klient bibliotek för python med hjälp av [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Lägg till dessa import uttryck längst upp i din kod fil.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Anslut till kontot

Om du vill använda kodfragmenten i den här artikeln måste du skapa en **DataLakeServiceClient** -instans som representerar lagrings kontot. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Anslut med hjälp av Azure Active Directory (AD)

> [!NOTE]
> Om du använder Azure Active Directory (Azure AD) för att auktorisera åtkomst kontrollerar du att ditt säkerhets objekt har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Mer information om hur ACL-behörigheter tillämpas och effekterna av att ändra dem finns i  [åtkomst kontroll modell i Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Du kan använda [klient biblioteket för Azure Identity för python för](https://pypi.org/project/azure-identity/) att autentisera ditt program med Azure AD.

Hämta ett klient-ID, en klient hemlighet och ett klient-ID. Information om hur du gör detta finns i [Hämta en token från Azure AD för att auktorisera begär Anden från ett klient program](../common/storage-auth-aad-app.md). Som en del av den processen måste du tilldela en av följande roller för [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) till ditt säkerhets objekt. 

|Roll|Inställnings funktion för ACL|
|--|--|
|[Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alla kataloger och filer i kontot.|
|[Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Endast kataloger och filer som ägs av säkerhets objekt.|

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av ett klient-ID, en klient hemlighet och ett klient-ID.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Fler exempel finns i [klient biblioteket för Azure Identity för python](https://pypi.org/project/azure-identity/) -dokumentation.

### <a name="connect-by-using-an-account-key"></a>Anslut med hjälp av en konto nyckel

Detta är det enklaste sättet att ansluta till ett konto.

I det här exemplet skapas en **DataLakeServiceClient** -instans med hjälp av en konto nyckel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Ersätt platshållarvärdet `storage_account_name` med namnet på ditt lagringskonto.

- Ersätt `storage_account_key` placeholder-värdet med din åtkomst nyckel för lagrings kontot.

## <a name="set-acls"></a>Ange ACL: er

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er](#update-acls-recursively) i den här artikeln.  

I det här avsnittet visas hur du:

- Ange ACL för en katalog
- Ange ACL för en fil

### <a name="set-the-acl-of-a-directory"></a>Ange ACL för en katalog

Hämta ACL (Access Control List) för en katalog genom att anropa metoden **DataLakeDirectoryClient.get_access_control** och ange ACL genom att anropa metoden **DataLakeDirectoryClient.set_access_control** .

Det här exemplet hämtar och anger ACL: en för en katalog med namnet `my-directory` . Strängen `rwxr-xrw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Du kan också hämta och ange ACL: en för rot katalogen för en behållare. Om du vill hämta rot katalogen anropar du **FileSystemClient._get_root_directory_client** -metoden.

### <a name="set-the-acl-of-a-file"></a>Ange ACL för en fil

Hämta ACL (Access Control List) för en fil genom att anropa metoden **DataLakeFileClient.get_access_control** och ange ACL genom att anropa metoden **DataLakeFileClient.set_access_control** .

Det här exemplet hämtar och anger ACL: en för en fil med namnet `my-file.txt` . Strängen `rwxr-xrw-` ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra Läs-och Skriv behörighet.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Ange ACL:er rekursivt

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er rekursivt](#update-acls-recursively) i den här artikeln.

Ange ACL: er rekursivt genom att anropa metoden **DataLakeDirectoryClient.set_access_control_recursive** .

Om du vill ange en **standard** -ACL-post lägger du till strängen `default:` i början av varje ACL-anslutningssträng.

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` .

Den här metoden accepterar en boolesk parameter med namnet `is_default_scope` som anger om du vill ange standard-ACL. om den parametern är `True` , föregås listan över ACL-poster med strängen `default:` .

Posterna i ACL: en ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet. Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "" XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX "Läs-och körnings behörighet.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="update-acls-recursively"></a>Uppdatera ACL: er rekursivt

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Om du vill ersätta ACL: en i stället för att uppdatera den, se avsnittet [ange ACL: er](#set-acls) i den här artikeln.

Om du vill uppdatera en ACL rekursivt, skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras. Uppdatera en ACL rekursivt genom att anropa metoden **DataLakeDirectoryClient.update_access_control_recursive** . Om du vill uppdatera en **standard** -ACL-post lägger du till strängen `default:` i början av varje ACL-anslutningssträng.

Det här exemplet uppdaterar en ACL-post med Skriv behörighet.

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Den här metoden accepterar en boolesk parameter med namnet `is_default_scope` som anger om du vill uppdatera standard-ACL: en. om den parametern är `True` , föregås den uppdaterade ACL-posten med strängen `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

## <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Du kan ta bort en eller flera ACL-poster. Om du vill ta bort ACL-poster rekursivt, skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort. 

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

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

Det här exemplet returnerar en fortsättnings-token om ett problem uppstår. Programmet kan anropa den här exempel metoden igen när felet har åtgärd ATS och passera över fortsättnings-token. Om den här exempel metoden anropas för första gången kan programmet överföras som värde ``None`` för parametern för en tilläggs-token.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

För att säkerställa att processen slutförs utan avbrott ska du inte skicka en fortsättnings-token till **DataLakeDirectoryClient.set_access_control_recursive** -metoden.

I det här exemplet anges ACL-poster rekursivt. Om den här koden påträffar ett behörighets fel registrerar den felet och fortsätter att köra. Det här exemplet skriver ut antalet felaktiga försök till-konsolen.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Om du vill se ett exempel som bearbetar ACL rekursivt i batchar genom att ange en batchstorlek, se python- [exemplet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Se även

- [Referensdokumentation för API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Paket (python-paket index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1 till Gen2-mappning](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Lämna feedback](https://github.com/Azure/azure-sdk-for-python/issues)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)