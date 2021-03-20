---
title: Säkerhetskopiera Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att säkerhetskopiera Azure-filresurser i Recovery Services valvet
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 34eea8daa6a0a8920c842178664055838b06a78a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565899"
---
# <a name="back-up-azure-file-shares-with-azure-cli"></a>Säkerhetskopiera Azure-filresurser med Azure CLI

Kommando rads gränssnittet för Azure (CLI) tillhandahåller kommando tolken för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln beskriver hur du säkerhetskopierar Azure-filresurser med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](./backup-azure-afs-automation.md) eller [Azure Portal](backup-afs.md).

I slutet av den här självstudien får du lära dig hur du utför åtgärderna nedan med Azure CLI:

* skapar ett Recovery Services-valv
* Aktivera säkerhets kopiering för Azure-filresurser
* Utlös en säkerhets kopiering på begäran för fil resurser

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.18 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services valv är en entitet som ger dig en samlad vy och hanterings funktion för alla säkerhets kopierings objekt. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Följ de här stegen för att skapa ett Recovery Services-valv:

1. Ett valv placeras i en resurs grupp. Om du inte har en befintlig resurs grupp skapar du en ny med [AZ Group Create](/cli/azure/group#az-group-create) . I den här självstudien skapar vi den nya resurs gruppen *migreringsåtgärden* i regionen USA, östra.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

1. Använd [AZ Backup Vault Create](/cli/azure/backup/vault#az-backup-vault-create) cmdlet för att skapa valvet. Ange samma plats för valvet som användes för resurs gruppen.

    I följande exempel skapas ett Recovery Services-valv med namnet *azurefilesvault* i regionen USA, östra.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Aktivera säkerhets kopiering för Azure-filresurser

Det här avsnittet förutsätter att du redan har en Azure-filresurs som du vill konfigurera säkerhets kopiering för. Om du inte har ett kan du skapa en Azure-filresurs med hjälp av kommandot [AZ Storage Share Create](/cli/azure/storage/share#az-storage-share-create) .

Om du vill aktivera säkerhets kopiering för fil resurser måste du skapa en skydds princip som definierar när ett säkerhets kopierings jobb körs och hur länge återställnings punkter lagras. Du kan skapa en säkerhets kopierings princip med hjälp av [AZ säkerhets kopierings princip skapa](/cli/azure/backup/policy#az-backup-policy-create) cmdlet.

I följande exempel används [AZ backup Protection Enable-to-azurefileshare](/cli/azure/backup/protection#az-backup-protection-enable-for-azurefileshare) för att aktivera säkerhets kopiering för *migreringsåtgärden* -filresursen i *afsaccount* -lagrings kontot med hjälp av säkerhets kopierings principen för *schema 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapades av säkerhets kopierings tjänsten för åtgärden **Aktivera säkerhets kopiering** . Om du vill spåra status för jobbet använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlös en säkerhets kopiering på begäran för fil resurs

Om du vill utlösa en säkerhets kopiering på begäran för fil resursen i stället för att vänta på att säkerhets kopierings policyn ska köra jobbet vid den schemalagda tiden använder du cmdleten [AZ backup Protection backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

Du måste definiera följande parametrar för att utlösa en säkerhets kopiering på begäran:

* **--container-Name** är namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) .
* **--objekt-Name** är namnet på den fil resurs som du vill aktivera en säkerhets kopiering på begäran för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](/cli/azure/backup/item#az-backup-item-list) .
* **--Behåll-tills** anger det datum då du vill behålla återställnings punkten. Värdet ska anges i UTC-timmarsformat (dd-mm-åååå).

I följande exempel utlöses en säkerhets kopiering på begäran för *migreringsåtgärden* -fileshare i *afsaccount* Storage-kontot med kvarhållning till *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på jobbet som skapats av säkerhets kopierings tjänsten för åtgärden "säkerhets kopiering på begäran". Om du vill spåra status för ett jobb använder du [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer Azure-filresurser med CLI](restore-afs-cli.md)
* Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser med CLI](manage-afs-backup-cli.md)
