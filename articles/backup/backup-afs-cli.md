---
title: Back up Azure file shares with Azure CLI
description: Lär dig hur du använder Azure CLI för att valva Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: a5f7472c511a5a50415a6ceb47497dd6f4f1e60b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773629"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Back up Azure file shares with Azure CLI

Azures kommandoradsgränssnitt (CLI) ger en kommandoradsupplevelse för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. I den här artikeln beskrivs hur du kan backa upp Azure-filresurser med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](./backup-azure-afs-automation.md) eller [Azure Portal](backup-afs.md).

I slutet av den här självstudien lär du dig att utföra åtgärderna nedan med Azure CLI:

* skapar ett Recovery Services-valv
* Aktivera säkerhetskopiering för Azure-filresurser
* Utlösa en säkerhetskopiering på begäran för filresurser

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.18 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en entitet som ger dig en samlad vy- och hanteringsfunktion för alla säkerhetskopieringsobjekt. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Följ dessa steg för att skapa ett Recovery Services-valv:

1. Ett valv placeras i en resursgrupp. Om du inte har en befintlig resursgrupp skapar du en ny med [az group create](/cli/azure/group#az_group_create) . I den här självstudien skapar vi den nya *resursgruppen azurefiles* i regionen USA, östra.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Använd [cmdleten az backup vault create](/cli/azure/backup/vault#az_backup_vault_create) för att skapa valvet. Ange samma plats för valvet som användes för resursgruppen.

    I följande exempel skapas ett Recovery Services-valv *med namnet azurefilesvault* i regionen USA, östra.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Aktivera säkerhetskopiering för Azure-filresurser

Det här avsnittet förutsätter att du redan har en Azure-filresurs som du vill konfigurera säkerhetskopiering för. Om du inte har någon kan du skapa en Azure-filresurs med kommandot [az storage share create.](/cli/azure/storage/share#az_storage_share_create)

Om du vill aktivera säkerhetskopiering för filresurser måste du skapa en skyddsprincip som definierar när ett säkerhetskopieringsjobb körs och hur länge återställningspunkter lagras. Du kan skapa en säkerhetskopieringspolicy med [hjälp av cmdleten az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

I följande exempel används cmdleten [az backup protection enable-for-azurefileshare](/cli/azure/backup/protection#az_backup_protection_enable_for_azurefileshare) för att aktivera säkerhetskopiering för *azurefiles-filresursen* i *lagringskontot afsaccount* med hjälp av säkerhetskopieringspolicyn *schema 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för din **aktivera säkerhetskopieringsåtgärd.** Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlösa en säkerhetskopiering på begäran för filresurs

Om du vill utlösa en säkerhetskopiering på begäran för filresursen i stället för att vänta på att säkerhetskopieringsprincipen ska köra jobbet vid den schemalagda tiden använder du cmdleten [az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

Du måste definiera följande parametrar för att utlösa en säkerhetskopiering på begäran:

* **--container-name** är namnet på lagringskontot som är värd för filresursen. Om du vill **hämta namnet** eller det **egna namnet** på din container använder du kommandot az backup [container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name** är namnet på den filresurs som du vill utlösa en säkerhetskopiering på begäran för. Om du vill **hämta namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot az backup [item list.](/cli/azure/backup/item#az_backup_item_list)
* **--retain-until** anger datumet tills du vill behålla återställningspunkten. Värdet ska anges i UTC-tidsformat (dd-mm-yyyyy).

I följande exempel utlöses en säkerhetskopiering på begäran för *filresursen azurefiles* i *afsaccount-lagringskontot* med kvarhållning fram *till 20-01-2020.*

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

**Name-attributet** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för din "säkerhetskopiering på begäran". Om du vill spåra status för ett jobb använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer Azure-filresurser med CLI](restore-afs-cli.md)
* Lär dig hur [du hanterar säkerhetskopior av Azure-filresursen med CLI](manage-afs-backup-cli.md)
