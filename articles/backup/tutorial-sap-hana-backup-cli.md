---
title: Självstudie – SAP HANA DB-säkerhetskopiering i Azure med Hjälp av Azure CLI
description: I den här självstudien lär du dig hur du SAP HANA databaser som körs på en virtuell Azure-dator till ett Azure Backup Recovery Services-valv med Hjälp av Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: bebfe852aaac965fc7d07371be889fe515e3da3a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768531"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: Back up SAP HANA databases in an Azure VM using Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller via skript. Den här dokumentationen beskriver hur du säkerhetskopierar en SAP HANA databas och utlöser säkerhetskopieringar på begäran – allt med Hjälp av Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](./backup-azure-sap-hana-database.md).

Det här dokumentet förutsätter att du redan har en SAP HANA-databas installerad på en virtuell Azure-dator. (Du kan också skapa [en virtuell dator med hjälp av Azure CLI](../virtual-machines/linux/quick-create-cli.md)). När du är färdig med självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * skapar ett Recovery Services-valv
> * Registrera SAP HANA instans och identifiera databaser på den
> * Aktivera säkerhetskopiering på en SAP HANA databas
> * Utlösa en säkerhetskopiering på begäran

Kolla in de [scenarier som vi för närvarande stöder](./sap-hana-backup-support-matrix.md#scenario-support) för SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.30 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services-valv är en logisk container som lagrar säkerhetskopierade data för varje skyddad resurs, till exempel virtuella Azure-datorer eller arbetsbelastningar som körs på virtuella Azure-datorer, till exempel SQL- eller HANA-databaser. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [az backup vault create](/cli/azure/backup/vault#az_backup_vault_create). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Lär dig hur du skapar en virtuell dator med Hjälp av Azure CLI med den här [virtuella datorns snabbstart.](../virtual-machines/linux/quick-create-cli.md)

I den här självstudien använder vi följande:

* en resursgrupp med *namnet saphanaResourceGroup*
* en virtuell dator *med namnet saphanaVM*
* resurser på *platsen westus2.*

Vi skapar ett valv med namnet *saphanaVault.*

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Recovery Services-valvet är som standard inställt på geo-redundant lagring. Geo-Redundant lagring säkerställer att dina säkerhetskopierade data replikeras till en sekundär Azure-region som ligger hundratals kilometer från den primära regionen. Om inställningen för lagringsredundans behöver ändras använder du cmdleten [az backup vault backup-properties set.](/cli/azure/backup/vault/backup-properties#az_backup_vault_backup_properties_set)

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Om du vill se om valvet har skapats använder du [cmdleten az backup vault list.](/cli/azure/backup/vault#az_backup_vault_list) Du ser följande svar:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrera och skydda SAP HANA instansen

För att SAP HANA-instansen (den virtuella datorn med SAP HANA installerad) som ska identifieras av [Azure-tjänsterna](https://aka.ms/scriptforpermsonhana) måste ett förregistreringsskript köras på den SAP HANA datorn. Kontrollera att alla krav [är uppfyllda](./tutorial-backup-sap-hana-db.md#prerequisites) innan du kör skriptet. Mer information om vad skriptet gör finns i avsnittet [Vad förregistreringsskriptet gör.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

När skriptet har körts kan SAP HANA-instansen registreras med Recovery Services-valvet som vi skapade tidigare. Registrera instansen med hjälp av [cmdleten az backup container register.](/cli/azure/backup/container#az_backup_container_register) *VMResourceId är* resurs-ID:t för den virtuella dator som du skapade för att installera SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Om den virtuella datorn inte finns i samma resursgrupp som valvet refererar *saphanaResourceGroup* till den resursgrupp där valvet skapades.

När du registrerar SAP HANA instansen automatiskt alla dess aktuella databaser. Om du vill identifiera nya databaser som kan läggas till i framtiden kan du dock gå till avsnittet Identifiera nya databaser som lagts till [i den SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) instansen.

Om du vill kontrollera SAP HANA instansen har registrerats med ditt valv använder du cmdleten [az backup container list.](/cli/azure/backup/container#az_backup_container_list) Du ser följande svar:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumnen "name" i ovanstående utdata refererar till containernamnet. Det här containernamnet används i nästa avsnitt för att aktivera säkerhetskopieringar och utlösa dem. Vilket i det här fallet är *VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Aktivera säkerhetskopiering på SAP HANA databas

Cmdleten [az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) visar en lista över alla databaser som identifierats på SAP HANA instansen som du registrerade i föregående steg.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Du bör hitta den databas som du vill backa upp i den här listan, som ser ut så här:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Som du ser i ovanstående utdata är SID för SAP HANA-systemet HXE. I den här självstudien konfigurerar vi säkerhetskopiering för *databasen saphanadatabase;hxe;hxe* som finns på *hxehost-servern.*

För att skydda och konfigurera säkerhetskopiering på en databas, en i taget, använder vi cmdleten [az backup protection enable-for-azurewl.](/cli/azure/backup/protection#az_backup_protection_enable_for_azurewl) Ange namnet på den princip som du vill använda. Om du vill skapa en princip med CLI använder du [cmdleten az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create) I den här självstudien använder vi *sapahanaPolicy-principen.*

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Du kan kontrollera om säkerhetskopieringskonfigurationen ovan har slutförts med hjälp av [cmdleten az backup job list.](/cli/azure/backup/job#az_backup_job_list) Utdata visas på följande sätt:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

Cmdleten [az backup job list](/cli/azure/backup/job#az_backup_job_list) visar en lista över alla säkerhetskopieringsjobb (schemalagda eller på begäran) som har körts eller som för närvarande körs på den skyddade databasen, förutom andra åtgärder som att registrera, konfigurera säkerhetskopiering och ta bort säkerhetskopierade data.

>[!NOTE]
>Azure Backup justeras inte automatiskt för ändringar i sommartid vid sparande av en SAP HANA som körs på en virtuell Azure-dator.
>
>Ändra principen manuellt efter behov.

## <a name="trigger-an-on-demand-backup"></a>Utlösa en säkerhetskopiering på begäran

Avsnittet ovan beskriver hur du konfigurerar en schemalagd säkerhetskopiering, men det här avsnittet handlar om att utlösa en säkerhetskopiering på begäran. För att göra detta använder vi [cmdleten az backup protection backup-now.](/cli/azure/backup/protection#az_backup_protection_backup_now)

>[!NOTE]
> Bevarandeprincipen för en säkerhetskopiering på begäran bestäms av den underliggande bevarandeprincipen för databasen.

```azurecli-interactive
az backup protection backup-now --resource-group saphanaResourceGroup \
    --item-name saphanadatabase;hxe;hxe \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --backup-type Full
    --retain-until 01-01-2040
    --output table
```

Utdata visas på följande sätt:

```output
Name                                  ResourceGroup
------------------------------------  -------------
e0f15dae-7cac-4475-a833-f52c50e5b6c3  saphanaResourceGroup
```

Du får jobbnamnet i svaret. Det här jobbnamnet kan användas för att spåra jobbstatusen med hjälp av cmdleten [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

>[!NOTE]
>Loggsäkerhetskopior utlöses och hanteras automatiskt av SAP HANA internt.

## <a name="next-steps"></a>Nästa steg

* Om du vill lära dig hur du återställer en SAP HANA-databas i en virtuell Azure-dator med hjälp av CLI fortsätter du till självstudien – Återställa en SAP HANA-databas i en virtuell [Azure-dator med HJÄLP](tutorial-sap-hana-restore-cli.md) av CLI

* Information om hur du säkerhetskopierar en SAP HANA-databas som körs på en virtuell Azure-dator med Azure Portal finns i Säkerhetskopiera SAP HANA databaser på virtuella [Azure-datorer](./backup-azure-sap-hana-database.md)
