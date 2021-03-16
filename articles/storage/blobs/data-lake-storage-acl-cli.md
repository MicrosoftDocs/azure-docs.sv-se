---
title: 'Använd Azure CLI för att ange ACL: er i Azure Data Lake Storage Gen2'
description: Använd Azure CLI för att hantera åtkomst kontrol listor (ACL) i lagrings konton som har ett hierarkiskt namn område.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563172"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Använd Azure CLI för att hantera ACL: er i Azure Data Lake Storage Gen2

Den här artikeln visar hur du använder [CLI (Azure Command-Line Interface)](/cli/azure/) för att hämta, ange och uppdatera åtkomst kontrol listorna för kataloger och filer.

ACL-arv är redan tillgängligt för nya underordnade objekt som skapas under en överordnad katalog. Men du kan också lägga till, uppdatera och ta bort ACL rekursivt på befintliga underordnade objekt i en överordnad katalog utan att behöva göra dessa ändringar individuellt för varje underordnat objekt.

[Referens](/cli/azure/storage/fs/access)  |  [Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Ge feedback](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

- Ett lagrings konto med hierarkiskt namn område aktiverat. Följ [de här](create-data-lake-storage-account.md) anvisningarna för att skapa en.

- Azure CLI-version `2.14.0` eller högre.

- En av följande säkerhets behörigheter:

  - Ett etablerat Azure Active Directory (Azure AD) [säkerhets objekt](../../role-based-access-control/overview.md#security-principal) som har tilldelats rollen som ägare av [lagrings-BLOB-data](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för antingen mål behållaren, den överordnade resurs gruppen eller prenumerationen.  

  - Ägande användare av mål behållaren eller katalogen som du planerar att tillämpa ACL-inställningar på. Om du vill ange ACL: er rekursivt inkluderar detta alla underordnade objekt i mål behållaren eller katalogen.
  
  - Lagrings konto nyckel.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Kontrol lera att du har rätt version av Azure CLI installerad

1. Öppna [Azure Cloud Shell](../../cloud-shell/overview.md), eller om du har [installerat](/cli/azure/install-azure-cli) Azure CLI lokalt öppnar du ett kommando konsol program, till exempel Windows PowerShell.

2. Kontrol lera att den version av Azure CLI som har installerats är `2.14.0` eller högre genom att använda följande kommando.

   ```azurecli
    az --version
   ```

   Om din version av Azure CLI är lägre än `2.14.0` kan du installera en senare version. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Anslut till kontot

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
> Exemplet som presenteras i den här artikeln visar Azure Active Directory (Azure AD)-auktorisering. Mer information om autentiseringsmetoder finns i [bevilja åtkomst till BLOB-eller Queue-data med Azure CLI](./authorize-data-operations-cli.md).

## <a name="get-acls"></a>Hämta ACL: er

Hämta ACL för en **katalog** med hjälp av `az storage fs access show` kommandot.

Det här exemplet hämtar ACL: en för en katalog och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Hämta åtkomst behörigheterna för en **fil** med hjälp av `az storage fs access show` kommandot. 

Det här exemplet hämtar ACL: en för en fil och skriver sedan ut ACL: en till-konsolen.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Följande bild visar utdata när du har hämtat ACL för en katalog.

![Hämta ACL-utdata](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

I det här exemplet har ägande användaren Läs-, skriv-och körnings behörighet. Den ägande gruppen har bara Läs-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Ange ACL: er

När du *anger* en ACL **ersätter** du hela ACL: en med alla poster. Om du vill ändra behörighets nivån för ett säkerhets objekt eller lägga till ett nytt säkerhets objekt i ACL utan att påverka andra befintliga poster, bör du *Uppdatera* ACL i stället. Information om hur du uppdaterar en ACL i stället för att ersätta den finns i avsnittet [Uppdatera ACL: er](#update-acls) i den här artikeln.  

Om du väljer att *Ange* ACL måste du lägga till en post för den ägande användaren, en post för den ägande gruppen och en post för alla andra användare. Mer information om ägande användare, ägande grupp och andra användare finns i [användare och identiteter](data-lake-storage-access-control.md#users-and-identities).

I det här avsnittet visas hur du:

- Ange en ACL
- Ange ACL:er rekursivt

### <a name="set-an-acl"></a>Ange en ACL

Använd `az storage fs access set` kommandot för att ange ACL för en **katalog**. 

I det här exemplet anges ACL: en för en katalog för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

I det här exemplet anges *standard* -ACL: en för en katalog för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Använd `az storage fs access set` kommandot för att ange ACL för en **fil**. 

I det här exemplet anges ACL: en för en fil för ägande användare, ägande grupp eller andra användare, och sedan skrivs ACL: en ut till-konsolen.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill ange ACL för en speciell grupp eller användare använder du deras respektive objekt-ID. Exempel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` eller `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Följande bild visar utdata när du har angett ACL för en fil.

![Hämta ACL-utdata 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

I det här exemplet har ägande användare och ägande grupp bara Läs-och Skriv behörighet. Alla andra användare har Skriv-och körnings behörighet. Mer information om åtkomst kontrol listor finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Ange ACL:er rekursivt

Ange ACL: er rekursivt med hjälp av kommandot [AZ Storage FS-åtkomst uppsättning-rekursivt](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

I det här exemplet anges ACL: en för en katalog med namnet `my-parent-directory` . Dessa poster ger den ägande användaren Läs-, skriv-och körnings behörighet, ger den ägande gruppen endast Läs-och kör behörigheter och ger alla andra ingen åtkomst. Den sista ACL-posten i det här exemplet ger en speciell användare med objekt-ID: t "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" Läs-och körnings behörighet.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill ange en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Exempel: `default:user::rwx` eller `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Uppdatera ACL: er

När du *uppdaterar* en ACL ändrar du ACL i stället för att ersätta ACL: en. Du kan till exempel lägga till ett nytt säkerhets objekt till ACL utan att påverka andra säkerhets objekt som listas i åtkomst kontrol listan.  Om du vill ersätta ACL: en i stället för att uppdatera den, se avsnittet [ange ACL: er](#set-acls) i den här artikeln.

Om du vill uppdatera en ACL skapar du ett nytt ACL-objekt med ACL-posten som du vill uppdatera och använder sedan objektet i Update ACL-åtgärden. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska uppdateras.

I det här avsnittet visas hur du:

- Uppdatera en ACL
- Uppdatera ACL: er rekursivt

### <a name="update-an-acl"></a>Uppdatera en ACL

Ett annat sätt att ange den här behörigheten är att använda `az storage fs access set` kommandot. 

Uppdatera ACL: en för en katalog eller fil genom `-permissions` att ange parametern till kort form för en ACL.

I det här exemplet uppdateras ACL: en för en **katalog**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

I det här exemplet uppdateras ACL: en för en **fil**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill uppdatera åtkomst kontrol listan för en speciell grupp eller användare använder du deras respektive objekt-ID. Exempel: `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` eller `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Du kan också uppdatera den ägande användaren och gruppen av en katalog eller fil genom att ange `--owner` parametrarna eller i `group` enhets-ID: t eller användarens huvud namn (UPN) för en användare.

I det här exemplet ändras ägaren till en katalog.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Det här exemplet ändrar ägaren till en fil. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Uppdatera ACL: er rekursivt

Uppdatera ACL: er rekursivt med hjälp av kommandot  [AZ Storage FS Access Update-rekursivt](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

Det här exemplet uppdaterar en ACL-post med Skriv behörighet.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill uppdatera en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Till exempel `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Ta bort ACL-poster rekursivt

Du kan ta bort en eller flera ACL-poster rekursivt. Om du vill ta bort en ACL-post skapar du ett nytt ACL-objekt för ACL-posten som ska tas bort och använder sedan objektet i ta bort ACL-åtgärd. Hämta inte den befintliga ACL: en och ange bara ACL-poster som ska tas bort.

Ta bort ACL-poster med hjälp av kommandot [AZ Storage FS Access Remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) .

Det här exemplet tar bort en ACL-post från rot katalogen för behållaren.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Om du vill ta bort en **standard** -ACL-post lägger du till prefixet `default:` i varje post. Till exempel `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Återställa från fel

Du kan stöta på körnings-eller behörighets fel när du ändrar ACL rekursivt. Starta om processen från början för körnings fel. Behörighets fel kan uppstå om säkerhetsobjektet inte har tillräcklig behörighet för att ändra ACL för en katalog eller fil som finns i den katalogpartition som ändras. Åtgärda behörighets problemet och välj sedan att antingen återuppta processen från fel punkten genom att använda en fortsättnings-token eller starta om processen från början. Du behöver inte använda den fortsättnings-token om du vill starta om från början. Du kan tillämpa ACL-poster igen utan negativ påverkan.

Om ett problem uppstår kan du returnera en fortsättnings-token genom att ange `--continue-on-failure` parametern till `false` . När du har åtgärdat felen kan du återuppta processen från fel punkten genom att köra kommandot igen och sedan ange parametern till den över gångs- `--continuation` token.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Om du vill att processen ska slutföras oavbrutet genom behörighets fel kan du ange den.

Ange parametern till om du vill se till att processen slutförs utan avbrott `--continue-on-failure` `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Se även

- [Exempel](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Lämna feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Kända problem](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Åtkomst kontroll modell i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)