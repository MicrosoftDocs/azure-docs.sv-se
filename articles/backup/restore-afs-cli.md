---
title: Återställa Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att återställa säkerhetskopierade Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 2edc2281c29023bb8dfe0268f23eacfe081d413e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768528"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Återställa Azure-filresurser med Azure CLI

Azure CLI ger en kommandoradsupplevelse för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för användning av Azure-resurser. Den här artikeln beskriver hur du återställer en hel filresurs eller specifika filer från en återställningspunkt som [skapats av Azure Backup](./backup-overview.md) med hjälp av Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](./backup-azure-afs-automation.md) eller [Azure Portal](backup-afs.md).

I slutet av den här artikeln får du lära dig hur du utför följande åtgärder med Azure CLI:

* Visa återställningspunkter för en säkerhetskopierad Azure-filresurs.
* Återställ en fullständig Azure-filresurs.
* Återställa enskilda filer eller mappar.

>[!NOTE]
> Azure Backup stöder nu återställning av flera filer eller mappar till den ursprungliga eller en alternativ plats med hjälp av Azure CLI. Mer information finns [i avsnittet Återställa flera filer eller mappar](#restore-multiple-files-or-folders-to-original-or-alternate-location) till en ursprunglig eller alternativ plats i det här dokumentet.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har en Azure-filresurs som säkerhetskopieras av Azure Backup. Om du inte har någon kan du gå till [Säkerhetskopiera Azure-filresurser med CLI för](backup-afs-cli.md) att konfigurera säkerhetskopiering för filresursen. I den här artikeln använder du följande resurser:

| Filresurs | Lagringskonto | Region | Information |
|---|---|---|---|
| *azurefiles* | *afsaccount* | USA, östra | Ursprunglig källa som säkerhetskopierats med hjälp av Azure Backup |
| *azurefiles1* | *afaccount1* | USA, östra | Målkälla som används för återställning av alternativ plats |

Du kan använda en liknande struktur för dina filresurser för att prova de olika typerna av återställningar som beskrivs i den här artikeln.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - Den här självstudien kräver version 2.0.18 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Hämta återställningspunkter för Azure-filresursen

Använd [cmdleten az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) för att lista alla återställningspunkter för den säkerhetskopierade filresursen.

I följande exempel hämtas listan över återställningspunkter för *azurefiles-filresursen* i *afsaccount-lagringskontot.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Du kan också köra den tidigare cmdleten med hjälp av det egna namnet för containern och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Resultatuppsättningen är en lista över återställningspunkter med information om tid och konsekvens för varje återställningspunkt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

**Name-attributet** i utdata motsvarar namnet på återställningspunkten som kan användas som ett värde för parametern **--rp-name** i återställningsåtgärder.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Fullständig resursåterställning med hjälp av Azure CLI

Du kan använda det här återställningsalternativet för att återställa hela filresursen på den ursprungliga eller en alternativ plats.

Definiera följande parametrar för att utföra återställningsåtgärder:

* **--container-name:** Namnet på det lagringskonto som är värd för den säkerhetskopierade ursprungliga filresursen. Om du vill hämta namnet eller det egna namnet på din container använder du [kommandot az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** Namnet på den säkerhetskopierade ursprungliga filresurs som du vill använda för återställningen. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du [kommandot az backup item list.](/cli/azure/backup/item#az_backup_item_list)

### <a name="restore-a-full-share-to-the-original-location"></a>Återställa en fullständig resurs till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange målrelaterade parametrar. Endast **lös konflikt** måste anges.

I följande exempel används [cmdleten az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) med återställningsläget *inställt* på originallocation för att återställa *azurefiles-filresursen* på den ursprungliga platsen. Du använder återställningspunkten 932883129628959823, som du fick i [Hämta återställningspunkter för Azure-filresursen](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för återställningsåtgärden. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-a-full-share-to-an-alternate-location"></a>Återställa en fullständig resurs till en alternativ plats

Du kan använda det här alternativet för att återställa en filresurs till en annan plats och behålla den ursprungliga filresursen som den är. Ange följande parametrar för återställning av alternativ plats:

* **--target-storage-account:** Lagringskontot som det säkerhetskopierade innehållet återställs till. Mållagringskontot måste finnas på samma plats som valvet.
* **--target-file-share:** Filresursen i mållagringskontot som det säkerhetskopierade innehållet återställs till.
* **--target-folder:** Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du målmappens värden som en tom sträng.
* **--resolve-conflict:** Instruktion om det finns en konflikt med återställda data. Godkänner **Överskrivning eller** **Hoppa över**.

I följande exempel används [az backup restore restore-azurefileshare](/cli/azure/backup/restore#az_backup_restore_restore_azurefileshare) med återställningsläget som *alternatelocation* för att återställa azurefiles-filresursen i *afsaccount-lagringskontot* till *filresursen azurefiles1"* i *lagringskontot* *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för återställningsåtgärden. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="item-level-recovery"></a>Återställning på objektnivå

Du kan använda det här återställningsalternativet för att återställa enskilda filer eller mappar på den ursprungliga eller en alternativ plats.

Definiera följande parametrar för att utföra återställningsåtgärder:

* **--container-name:** Namnet på det lagringskonto som är värd för den säkerhetskopierade ursprungliga filresursen. Om du vill hämta namnet eller det egna namnet på din container använder du [kommandot az backup container list.](/cli/azure/backup/container#az_backup_container_list)
* **--item-name:** Namnet på den säkerhetskopierade ursprungliga filresurs som du vill använda för återställningen. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du [kommandot az backup item list.](/cli/azure/backup/item#az_backup_item_list)

Ange följande parametrar för de objekt som du vill återställa:

* **SourceFilePath:** Den absoluta sökvägen till filen som ska återställas i filresursen som en sträng. Den här sökvägen är samma sökväg som används i [cli-kommandona az storage file download](/cli/azure/storage/file#az_storage_file_download) eller az storage file [show.](/cli/azure/storage/file#az_storage_file_show)
* **SourceFileType:** Välj om en katalog eller en fil ska väljas. Godkänner **Katalog** eller **Fil**.
* **ResolveConflict:** Instruktion om det finns en konflikt med återställda data. Godkänner **Överskrivning eller** **Hoppa över**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Återställa enskilda filer eller mappar till den ursprungliga platsen

Använd [cmdleten az backup restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) med återställningsläget inställt på *originallocation* för att återställa specifika filer eller mappar till deras ursprungliga plats.

I följande exempel återställs *RestoreTest.txt* på den ursprungliga platsen: *filresursen azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för återställningsåtgärden. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Återställa enskilda filer eller mappar till en alternativ plats

Om du vill återställa specifika filer eller mappar till en alternativ plats använder du cmdleten [az backup restore-azurefiles](/cli/azure/backup/restore#az_backup_restore_restore_azurefiles) med återställningsläget inställt på *alternatelocation* och anger följande målrelaterade parametrar:

* **--target-storage-account:** Det lagringskonto som det säkerhetskopierade innehållet återställs till. Mållagringskontot måste finnas på samma plats som valvet.
* **--target-file-share:** Filresursen i mållagringskontot som det säkerhetskopierade innehållet återställs till.
* **--target-folder:** Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du målmappens värde som en tom sträng.

I följande exempel återställs *RestoreTest.txt-filen* som ursprungligen fanns i *filresursen azurefiles* till en alternativ plats: *mappen restoredata* i *filresursen azurefiles1* som finns på *lagringskontot afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för återställningsåtgärden. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Återställa flera filer eller mappar till den ursprungliga eller alternativa platsen

Om du vill utföra återställning för flera objekt skickar du  värdet för parametern **source-file-path** som blankstegsavgränsade sökvägar för alla filer eller mappar som du vill återställa.

I följande exempel återställs *Restore.txt* *AFS-Report.docx* på den ursprungliga platsen.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Resultatet blir något som liknar:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Attributet **Namn** i utdata motsvarar namnet på jobbet som skapas av säkerhetskopieringstjänsten för återställningsåtgärden. Om du vill spåra jobbets status använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

Om du vill återställa flera objekt till en alternativ plats använder du kommandot ovan genom att ange målrelaterade parametrar enligt beskrivningen i avsnittet Återställa enskilda filer eller mappar till [en alternativ](#restore-individual-files-or-folders-to-an-alternate-location) plats.

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du hanterar säkerhetskopior av Azure-filresursen med Azure CLI.](manage-afs-backup-cli.md)
