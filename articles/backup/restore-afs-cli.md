---
title: Återställa Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att återställa säkerhets kopie rad Azure-filresurser i Recovery Services valvet
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562329"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Återställa Azure-filresurser med Azure CLI

Azure CLI innehåller en kommando rads upplevelse för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln förklarar hur du återställer en hel fil resurs eller vissa filer från en återställnings punkt som skapats av [Azure Backup](./backup-overview.md) med hjälp av Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](./backup-azure-afs-automation.md) eller [Azure Portal](backup-afs.md).

I slutet av den här artikeln får du lära dig hur du utför följande åtgärder med Azure CLI:

* Visa återställnings punkter för en säkerhets kopie rad Azure-filresurs.
* Återställa en fullständig Azure-filresurs.
* Återställa enskilda filer eller mappar.

>[!NOTE]
> Azure Backup stöder nu återställning av flera filer eller mappar till den ursprungliga eller en alternativ plats med hjälp av Azure CLI. Läs mer i avsnittet [återställa flera filer eller mappar till den ursprungliga eller alternativa platsen](#restore-multiple-files-or-folders-to-original-or-alternate-location) i det här dokumentet.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har en Azure-filresurs som har säkerhetskopierats av Azure Backup. Om du inte har en sådan, se [säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhets kopiering för din fil resurs. I den här artikeln använder du följande resurser:

| Filresurs | Lagringskonto | Region | Information |
|---|---|---|---|
| *migreringsåtgärden* | *afsaccount* | Platsen eastus | Original källan har säkerhetskopierats med Azure Backup |
| *azurefiles1* | *afaccount1* | Platsen eastus | Mål källa som används för återställning av alternativ plats |

Du kan använda en liknande struktur för dina fil resurser för att testa de olika typerna av återställningar som beskrivs i den här artikeln.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - I den här självstudien krävs version 2.0.18 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Hämta återställnings punkter för Azure-filresursen

Använd cmdleten [AZ backup recoverypoint List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) för att visa en lista över alla återställnings punkter för den säkerhetskopierade fil resursen.

I följande exempel hämtar listan över återställnings punkter för fil resursen *migreringsåtgärden* i *afsaccount* Storage-kontot.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Du kan också köra föregående cmdlet med hjälp av det egna namnet för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Resultat uppsättningen är en lista över återställnings punkter med tids-och konsekvens information för varje återställnings punkt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Namnattributet **i** utdata motsvarar återställnings punktens namn som kan användas som ett värde för parametern **--RP-Name** i återställnings åtgärder.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Fullständig delnings återställning med hjälp av Azure CLI

Du kan använda det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.

Definiera följande parametrar för att utföra återställnings åtgärder:

* **--container-Name**: namnet på det lagrings konto som är värd för säkerhets kopian av den ursprungliga fil resursen. Om du vill hämta namnet eller det egna namnet på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--objekt namn**: namnet på den säkerhetskopierade original fil resurs som du vill använda för återställnings åtgärden. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .

### <a name="restore-a-full-share-to-the-original-location"></a>Återställa en fullständig resurs till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange mål-relaterade parametrar. Endast **konflikt vid matchning** måste anges.

I följande exempel används AZ Backup Restore [-azurefileshare-](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) cmdleten med återställnings läge inställt på *originallocation* för att återställa *migreringsåtgärden* -filresursen på den ursprungliga platsen. Du använder återställnings punkten 932883129628959823 som du hämtade i [Hämta återställnings punkter för Azure-filresursen](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din återställnings åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Återställa en fullständig resurs till en annan plats

Du kan använda det här alternativet för att återställa en fil resurs till en annan plats och behålla den ursprungliga fil resursen som den är. Ange följande parametrar för alternativ plats återställning:

* **--mål-Storage – konto**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **--mål-fildelning**: fil resursen i det mål lagrings konto som det säkerhetskopierade innehållet återställs till.
* **--** målmapp: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
* **--resolve-konflikt**: instruktioner om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

I följande exempel används [AZ backup](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) Restore Restore-azurefileshare med Restore Mode som *alternatelocation* för att återställa *migreringsåtgärden* -filresursen i *afsaccount* lagrings konto till *azurefiles1* -filresursen i *afaccount1* Storage-kontot.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din återställnings åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Återställning på objektnivå

Du kan använda det här alternativet för återställning för att återställa enskilda filer eller mappar på den ursprungliga platsen eller en annan plats.

Definiera följande parametrar för att utföra återställnings åtgärder:

* **--container-Name**: namnet på det lagrings konto som är värd för säkerhets kopian av den ursprungliga fil resursen. Om du vill hämta namnet eller det egna namnet på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--objekt namn**: namnet på den säkerhetskopierade original fil resurs som du vill använda för återställnings åtgärden. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .

Ange följande parametrar för de objekt som du vill återställa:

* **SourceFilePath**: den absoluta sökvägen till filen som ska återställas inom fil resursen, som en sträng. Den här sökvägen är samma sökväg som används i [AZ Storage File Download](/cli/azure/storage/file#az-storage-file-download) eller [AZ Storage-filen show](/cli/azure/storage/file#az-storage-file-show) CLI-kommandon.
* **SourceFileType**: Välj om en katalog eller en fil är markerad. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: instruktion om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Återställa enskilda filer eller mappar till den ursprungliga platsen

Använd AZ Backup Restore [-migreringsåtgärden-](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdleten med återställnings läge inställt på *originallocation* för att återställa vissa filer eller mappar till sin ursprungliga plats.

I följande exempel återställs *RestoreTest.txt* -filen på den ursprungliga platsen: fil resursen *migreringsåtgärden* .

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din återställnings åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Återställa enskilda filer eller mappar till en annan plats

Om du vill återställa specifika filer eller mappar till en annan plats använder du AZ Backup Restore [-migreringsåtgärden-](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdleten med återställnings läge inställt på *alternatelocation* och anger följande Target-relaterade parametrar:

* **--mål-Storage – konto**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **--mål-fildelning**: fil resursen i det mål lagrings konto som det säkerhetskopierade innehållet återställs till.
* **--** målmapp: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du målmappens värde som en tom sträng.

I följande exempel återställs *RestoreTest.txt* -filen som ursprungligen fanns i *migreringsåtgärden* -filresursen till en alternativ plats: mappen *restoredata* i *azurefiles1* -filresursen som finns i *afaccount1* -lagrings kontot.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din återställnings åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Återställa flera filer eller mappar till en ursprunglig eller alternativ plats

Om du vill utföra återställningen för flera objekt skickar du värdet för parametern **Source-File-Path** som **blank steg avgränsade** sökvägar för alla filer eller mappar som du vill återställa.

I följande exempel återställs *Restore.txt* -och *AFS-test Report.docx* filer på den ursprungliga platsen.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Resultatet blir något som liknar:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapas av säkerhets kopierings tjänsten för din återställnings åtgärd. Om du vill spåra jobbets status använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

Om du vill återställa flera objekt till en annan plats använder du kommandot ovan genom att ange målattribut parametrar enligt beskrivningen i avsnittet [återställa enskilda filer eller mappar till en alternativ plats](#restore-individual-files-or-folders-to-an-alternate-location) .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser med Azure CLI](manage-afs-backup-cli.md).
