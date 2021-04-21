---
title: Snabbstart – Backa upp en virtuell dator med Azure CLI
description: I den här snabbstarten lär du dig hur du skapar ett Recovery Services-valv, aktiverar skydd på en virtuell dator och skapar den första återställningspunkten med Azure CLI.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 521f9332590eca5cdf94fadc4b59ab4d482eb658
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768567"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-azure-cli"></a>Backa upp en virtuell dator i Azure med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som kan lagras i geo-redundanta återställningsvalv. Den här artikeln beskriver hur du säkerhetskopierar en virtuell dator i Azure med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](quick-backup-vm-powershell.md) eller [Azure Portal](quick-backup-vm-portal.md).

I den här snabbstarten sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure CLI](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabbstarten kräver version 2.0.18 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Recovery Services-valvet är en logisk container som lagrar säkerhetskopierade data för varje skyddad resurs, till exempel virtuella Azure-datorer. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Om du har använt [snabbstarten för virtuella datorer](../virtual-machines/linux/quick-create-cli.md) har du skapat:

- en resursgrupp som heter *myResourceGroup*,
- en virtuell dator med namnet *myVM*,
- resurser på platsen *eastus*.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Recovery Services-valvet är som standard inställt på geo-redundant lagring. Geo-Redundant lagring säkerställer att dina säkerhetskopierade data replikeras till en sekundär Azure-region som ligger hundratals kilometer från den primära regionen. Om inställningen för lagringsredundans behöver ändras använder du [cmdleten az backup vault backup-properties set.](/cli/azure/backup/vault/backup-properties#az_backup_vault_backup_properties_set)

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator

Skapa en skyddsprincip för att definiera: när ett säkerhetskopieringsjobb ska köras och hur länge återställningspunkterna ska sparas. Med standardskyddsprincipen körs ett säkerhetskopieringsjobb varje dag och återställningspunkterna sparas i 30 dagar. Du kan använda standardvärdena för att snabbt skydda din virtuella dator. Om du vill aktivera säkerhetskopieringsskydd för en virtuell dator använder [du az backup protection enable-for-vm](/cli/azure/backup/protection#az_backup_protection_enable_for_vm). Ange vilken resursgrupp och virtuell dator som ska skyddas och sedan vilken princip som ska användas:

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> Om den virtuella datorn inte finns i samma resursgrupp som valvet refererar myResourceGroup till resursgruppen där valvet skapades. Ange den virtuella datorns ID i stället för dess namn, så som anges nedan.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> När du använder CLI för att aktivera säkerhetskopiering för flera virtuella datorer samtidigt bör du se till att en enskild princip inte har fler än 100 virtuella datorer kopplade till sig. Det här är [en rekommenderad metod.](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy) PowerShell-klienten blockerar för närvarande inte uttryckligen om det finns fler än 100 virtuella datorer, men kontrollen planeras att läggas till i framtiden.

## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb

Du kan starta en säkerhetskopiering nu, i stället för att vänta tills jobbet körs vid standardprincipens schemalagda tidpunkt, med hjälp av [az backup protection backup-now](/cli/azure/backup/protection#az_backup_protection_backup_now). När det första säkerhetskopieringsjobbet körs skapas en fullständig återställningspunkt. Vid varje säkerhetskopiering, efter den första säkerhetskopieringen, skapas inkrementella återställningspunkter. Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Följande parametrar används för att säkerhetskopiera den virtuella datorn:

- `--container-name` är namnet på din virtuella dator
- `--item-name` är namnet på din virtuella dator
- Värdet `--retain-until` ska vara angivet till det senaste datumet, i UTC-tidsformat (**dd-mm-åååå**), som du vill att återställningspunkten ska vara tillgänglig

I följande exempel säkerhetskopieras den virtuella datorn *myVM* och utgångsdatumet för återställningspunkten är 18 oktober 2017:

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen

Om du vill övervaka status för säkerhetskopieringsjobb använder du [az backup job list](/cli/azure/backup/job#az_backup_job_list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De utdata som returneras liknar dem i följande exempel, som visar att säkerhetskopieringsjobbet har status *Pågår*:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När *status* för säkerhetskopieringsjobbet är *Slutförd* skyddas din virtuella dator med Recovery Services och en fullständig återställningspunkt har sparats.

## <a name="clean-up-deployment"></a>Rensa distribution

När det inte längre behövs kan du inaktivera skyddet av den virtuella datorn, ta bort återställningspunkterna och Recovery Services-valvet och sedan ta bort resursgruppen och de relaterade virtuella datorresurserna. Om du använde en befintlig virtuell dator kan du hoppa över det sista [az group delete](/cli/azure/group#az_group_delete)-kommandot och ha kvar resursgruppen och den virtuella datorn.

Om du vill prova en självstudie om säkerhetskopiering som förklarar hur man återställer data för en virtuell dator kan du gå till [Nästa steg](#next-steps).

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten. Om du vill lära dig mer om Azure Backup och Recovery Services kan du gå vidare till självstudiekurserna.

> [!div class="nextstepaction"]
> [Säkerhetskopiera flera virtuella Azure-datorer](./tutorial-backup-vm-at-scale.md)
