---
title: Självstudie – återställa en virtuell dator med Azure CLI
description: Lär dig hur du återställer en disk och återskapar en virtuell dator i Azure med Backup och Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 999682c9bf4a4d70d886f0e85cede99f215aa046
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97694725"
---
# <a name="restore-a-vm-with-azure-cli"></a>Återställa en VM med Azure CLI

Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller enskilda filer. Den här artikeln förklarar hur du återställer hela den virtuella datorn med CLI. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

Information om hur du återställer en disk och skapar en återställd virtuell dator med PowerShell finns i [Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.18 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

 - För den här självstudiekursen måste du ha en virtuell Linux-dator som har skyddats med Azure Backup. Du simulerar en oavsiktlig borttagning och återställning av den virtuella datorn genom att skapa en virtuell dator från en disk i en återställningspunkt. Om du behöver en virtuell Linux-dator som har skyddats med Azure Backup kan du läsa mer i [Säkerhetskopiera en virtuell dator i Azure med CLI](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Översikt över Backup

När Azure initierar en säkerhetskopiering tar tillägget på den virtuella datorn en ögonblicksbild. Säkerhetskopieringstillägget installeras på den virtuella datorn när den första säkerhetskopieringen begärs. Azure Backup kan också ta en ögonblicks bild av den underliggande lagringen om den virtuella datorn inte körs när säkerhets kopieringen sker.

Som standard skapar Azure Backup en filsystemkonsekvent säkerhetskopia. När Azure Backup har tagit ögonblicksbilden överförs data till Recovery Services-valvet. För att maximera effektiviteten identifierar och överför Azure Backup endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.

## <a name="list-available-recovery-points"></a>Visa lista över tillgängliga återställningspunkter

Om du vill återställa en disk väljer du en återställningspunkt som källa för återställningsdata. Standardprincipen skapar en återställningspunkt varje dag och lagrar dem i 30 dagar, så du kan du behålla en uppsättning återställningspunkter och välja en viss tidpunkt för återställning.

Visa en lista över tillgängliga återställningspunkter kommandot [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). Återställningspunktens **namn** används för att återställa diskar. I den här självstudiekursen vill vi ha den senaste återställningspunkten. Parametern `--query [0].name` väljer det senaste återställningsnamnet enligt följande:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Återställa en disk från en virtuell dator

> [!IMPORTANT]
> Vi rekommenderar starkt att du använder AZ CLI version 2.0.74 eller senare för att få alla fördelar med en snabb återställning, inklusive hanterad disk återställning. Det är bäst om du alltid använder den senaste versionen.

### <a name="managed-disk-restore"></a>Återställning av hanterad disk

Om den säkerhetskopierade virtuella datorn har hanterade diskar och om avsikten är att återställa hanterade diskar från återställnings punkten ger du först ett Azure Storage-konto. Det här lagrings kontot används för att lagra VM-konfigurationen och distributions mal len som senare kan användas för att distribuera den virtuella datorn från de återställda diskarna. Sedan anger du också en mål resurs grupp för de hanterade diskarna som ska återställas till.

1. Skapa ett lagringskonto med [az storage account create](/cli/azure/storage/account#az-storage-account-create). Lagringskontonamnet måste vara med gemener endast och globalt unikt. Ersätt *mystorageaccount* med ditt eget unika namn:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Återställ disken från återställningspunkten med [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Ersätt *mystorageaccount* med namnet på det lagringskonto du skapade i föregående kommando. Ersätt *myRecoveryPointName* med återställnings punkt namnet som du fick i utdata från föregående [AZ backup recoverypoint List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) -kommando. ***Ange även mål resurs gruppen som de hanterade diskarna ska återställas till***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > Om **mål resurs gruppen** inte anges återställs de hanterade diskarna som ohanterade diskar till det angivna lagrings kontot. Detta kommer att ha betydande konsekvenser för återställnings tiden eftersom den tid det tar att återställa diskarna i sin helhet beror på det aktuella lagrings kontot. Du får bara fördelen med omedelbar återställning när parametern mål resurs-grupp anges. Om avsikten är att återställa hanterade diskar som ohanterade ska du inte ange parametern **-resurs-grupp** och i stället ange parametern **restore-as-unmanaged-disk** som visas nedan. Den här parametern är tillgänglig från AZ 3.4.0 och senare.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

Detta kommer att återställa hanterade diskar som ohanterade diskar till det aktuella lagrings kontot och kommer inte att använda funktionen "direkt återställning". I framtida versioner av CLI kommer det att vara obligatoriskt att ange antingen parameter för **mål resurs gruppen** eller parametern **restore-as-unmanaged-disk** .

### <a name="unmanaged-disks-restore"></a>Återställning av ohanterade diskar

Om den säkerhetskopierade virtuella datorn innehåller ohanterade diskar och om avsikten är att återställa diskar från återställnings punkten ger du först ett Azure Storage-konto. Det här lagrings kontot används för att lagra VM-konfigurationen och distributions mal len som senare kan användas för att distribuera den virtuella datorn från de återställda diskarna. Som standard kommer de ohanterade diskarna att återställas till sina ursprungliga lagrings konton. Om du vill återställa alla ohanterade diskar till en enda plats, kan det aktuella lagrings kontot även användas som mellanlagringsplats för dessa diskar.

I senare steg används den återställda disken för att skapa en virtuell dator.

1. Skapa ett lagringskonto med [az storage account create](/cli/azure/storage/account#az-storage-account-create). Lagringskontonamnet måste vara med gemener endast och globalt unikt. Ersätt *mystorageaccount* med ditt eget unika namn:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Återställ disken från återställningspunkten med [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks). Ersätt *mystorageaccount* med namnet på det lagringskonto du skapade i föregående kommando. Ersätt *myRecoveryPointName* med återställningspunktens namn som du fick i utdata från det tidigare kommandot [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list):

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

Som nämnts ovan kommer de ohanterade diskarna att återställas till det ursprungliga lagrings kontot. Detta ger bästa återställnings prestanda. Men om alla ohanterade diskar behöver återställas till ett lagrings konto kan du använda den relevanta flaggan som visas nedan.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De utdata som returneras liknar dem i följande exempel, som visar att återställningsjobbet har status *InProgress* (Pågår):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När *status* för återställnings jobbet har *slutförts* har nödvändig information (VM-konfiguration och distributions mal len) återställts till lagrings kontot.

## <a name="create-a-vm-from-the-restored-disk"></a>Skapa en virtuell dator från den återställda disken

Det sista steget är att skapa en virtuell dator från de återställda diskarna. Du kan använda distributions mal len som hämtats till det aktuella lagrings kontot för att skapa den virtuella datorn.

### <a name="fetch-the-job-details"></a>Hämta jobb informationen

Den resulterande jobb informationen ger den mall-URI som kan frågas och distribueras. Använd kommandot jobb show för att få mer information om det utlösde återställda jobbet.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Utdata från den här frågan ger all information, men vi är bara intresse rad av innehållet i lagrings kontot. Vi kan använda [fråge funktionen](/cli/azure/query-azure-cli) i Azure CLI för att hämta relevant information

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Hämta distributions mal len

Mallen är inte direkt tillgänglig eftersom den finns under en kunds lagrings konto och den aktuella behållaren. Vi behöver den fullständiga URL: en (tillsammans med en tillfällig SAS-token) för att få åtkomst till den här mallen.

Extrahera först mallen BLOB URI från jobb information

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Mallens BLOB-URI kommer att ha det här formatet och extrahera mallnamnet

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Därför blir mallnamnet från exemplet ovan ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` och behållar namnet ```myVM-daa1931199fd4a22ae601f46d8812276```

Hämta nu SAS-token för den här behållaren och mallen som beskrivs [här](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Distribuera mallen för att skapa den virtuella datorn

Distribuera nu mallen för att skapa den virtuella datorn enligt beskrivningen [här](../azure-resource-manager/templates/deploy-cli.md).

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Du kan bekräfta att den virtuella datorn har skapats från återställda disken genom att visa en lista över de virtuella datorerna i resursgruppen med [az vm list](/cli/azure/vm#az-vm-list) enligt följande:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du återställt en disk från en återställningspunkt och sedan skapat en virtuell dator från disken. Du har lärt dig att:

> [!div class="checklist"]
>
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

Gå vidare till nästa självstudiekurs där du lär dig att återställa enskilda filer från en återställningspunkt.

> [!div class="nextstepaction"]
> [Återställa filer till en virtuell dator i Azure](tutorial-restore-files.md)
