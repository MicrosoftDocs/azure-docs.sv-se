---
title: Självstudie – SAP HANA DB-säkerhetskopiering på Azure med Azure CLI
description: I den här självstudien får du lära dig hur du säkerhetskopierar SAP HANA databaser som körs på en virtuell Azure-dator till ett Azure Backup Recovery Services valv med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: ba06ef876f30dc51e04fe7491d491621f5d8e21b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710608"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: säkerhetskopiera SAP HANA databaser i en virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommando raden eller genom skript. Den här dokumentationen innehåller information om hur du säkerhetskopierar en SAP HANA-databas och aktiverar säkerhets kopiering på begäran – alla med Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](./backup-azure-sap-hana-database.md).

Det här dokumentet förutsätter att du redan har en SAP HANA-databas installerad på en virtuell Azure-dator. (Du kan också [skapa en virtuell dator med Azure CLI](../virtual-machines/linux/quick-create-cli.md)). När du är färdig med självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * skapar ett Recovery Services-valv
> * Registrera SAP HANA instans och identifiera databaser på den
> * Aktivera säkerhets kopiering på en SAP HANA databas
> * Utlös en säkerhets kopiering på begäran

Ta en titt på de [scenarier som vi för närvarande stöder](./sap-hana-backup-support-matrix.md#scenario-support) för SAP HANA.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.30 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

Ett Recovery Services valv är en logisk behållare som lagrar säkerhetskopierade data för varje skyddad resurs, till exempel virtuella Azure-datorer eller arbets belastningar som körs på virtuella Azure-datorer, t. ex. SQL-eller HANA-databaser När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [az backup vault create](/cli/azure/backup/vault#az-backup-vault-create). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Lär dig hur du skapar en virtuell dator med Azure CLI med den här snabb starten för [virtuella datorer](../virtual-machines/linux/quick-create-cli.md).

I den här självstudien ska vi använda följande:

* en resurs grupp med namnet *saphanaResourceGroup*
* en virtuell dator med namnet *saphanaVM*
* resurser på *westus2* -platsen.

Vi kommer att skapa ett valv med namnet *saphanaVault*.

```azurecli-interactive
az backup vault create --resource-group saphanaResourceGroup \
    --name saphanaVault \
    --location westus2
```

Recovery Services-valvet är som standard inställt på geo-redundant lagring. Geo-Redundant Storage garanterar att dina säkerhets kopierings data replikeras till en sekundär Azure-region som är hundratals mil bort från den primära regionen. Om inställningen för lagring av lagrings utrymme behöver ändras använder du [AZ säkerhets kopierings valv, egenskaper set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) cmdlet.

```azurecli
az backup vault backup-properties set \
    --name saphanaVault  \
    --resource-group saphanaResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

Använd cmdleten [AZ Backup Vault List](/cli/azure/backup/vault#az-backup-vault-list) för att se om valvet har skapats. Följande svar visas:

```output
Location   Name             ResourceGroup
---------  ---------------  -------------  
westus2    saphanaVault     saphanaResourceGroup
```

## <a name="register-and-protect-the-sap-hana-instance"></a>Registrera och skydda SAP HANA-instansen

För SAP HANA-instansen (den virtuella datorn med SAP HANA installerad) som ska identifieras av Azure-tjänsterna måste ett [för registrerings skript](https://aka.ms/scriptforpermsonhana) köras på SAP HANA datorn. Kontrol lera att alla [krav](./tutorial-backup-sap-hana-db.md#prerequisites) är uppfyllda innan du kör skriptet. Mer information om vad skriptet gör finns i avsnittet så här [fungerar skriptet för för registrering](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

När skriptet har körts kan SAP HANA-instansen registreras med Recovery Services-valvet som vi skapade tidigare. Registrera instansen med hjälp av [AZ för säkerhets kopierings behållare](/cli/azure/backup/container#az-backup-container-register) . *VMResourceId* är resurs-ID: t för den virtuella dator som du skapade för att installera SAP HANA.

```azurecli-interactive
az backup container register --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --location westus2 \
    --workload-type SAPHANA \
    --backup-management-type AzureWorkload \
    --resource-id VMResourceId
```

>[!NOTE]
>Om den virtuella datorn inte finns i samma resurs grupp som valvet refererar *saphanaResourceGroup* till resurs gruppen där valvet skapades.

När du registrerar SAP HANA-instansen upptäcks automatiskt alla befintliga databaser. Men för att identifiera nya databaser som kan läggas till i framtiden går du till [identifiera nya databaser som läggs till i avsnittet registrerad SAP HANA](tutorial-sap-hana-manage-cli.md#protect-new-databases-added-to-an-sap-hana-instance) instans.

Om du vill kontrol lera om SAP HANA-instansen har registrerats med ditt valv använder du cmdleten [AZ backup container List](/cli/azure/backup/container#az-backup-container-list) . Följande svar visas:

```output
Name                                                    Friendly Name    Resource Group        Type           Registration Status
------------------------------------------------------  --------------   --------------------  ---------      ----------------------
VMAppContainer;Compute;saphanaResourceGroup;saphanaVM   saphanaVM        saphanaResourceGroup  AzureWorkload  Registered
```

>[!NOTE]
> Kolumnen "name" i ovanstående utdata refererar till behållar namnet. Det här behållar namnet kommer att användas i nästa avsnitt för att aktivera säkerhets kopiering och utlösa dem. I det här fallet är *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*.

## <a name="enable-backup-on-sap-hana-database"></a>Aktivera säkerhets kopiering på SAP HANA databas

[AZ säkerhets kopierings skydds lista](/cli/azure/backup/protectable-item#az-backup-protectable-item-list) med flera objekt listar alla databaser som identifierats på den SAP HANA-instans som du registrerade i föregående steg.

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Du bör hitta den databas som du vill säkerhetskopiera i listan, vilket kommer att se ut så här:

```output
Name                           Protectable Item Type    ParentName    ServerName    IsProtected
-----------------------------  ----------------------   ------------  -----------   ------------
saphanasystem;hxe              SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb   SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;hxe        SAPHanaDatabase          HXE           hxehost       NotProtected
```

Som du kan se från ovanstående utdata, är SID för det SAP HANA systemet HXE. I den här självstudien konfigurerar vi säkerhets kopiering för den *saphanadatabase; hxe; hxe* -databas som finns på *hxehost* -servern.

För att skydda och konfigurera säkerhets kopiering för en databas, en i taget, använder vi [AZ backup Protection Enable-to-azurewl-](/cli/azure/backup/protection#az-backup-protection-enable-for-azurewl) cmdlet. Ange namnet på den princip som du vill använda. Om du vill skapa en princip med CLI använder du [AZ säkerhets kopierings princip skapa](/cli/azure/backup/policy#az-backup-policy-create) cmdlet. I den här självstudien använder vi *sapahanaPolicy* -principen.

```azurecli-interactive
az backup protection enable-for-azurewl --resource-group saphanaResourceGroup \
    --policy-name saphanaPolicy \
    --protectable-item-name "saphanadatabase;hxe;hxe"  \
    --protectable-item-type SAPHANADatabase \
    --server-name hxehost \
    --workload-type SAPHANA \
    --output table
```

Du kan kontrol lera om säkerhets kopierings konfigurationen ovan har slutförts med cmdleten [AZ backup Job List](/cli/azure/backup/job#az-backup-job-list) . Utdata visas på följande sätt:

```output
Name                                  Operation         Status     Item Name   Start Time UTC
------------------------------------  ---------------   ---------  ----------  -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup   Completed  hxe         2019-12-03T03:09:210831+00:00  
```

[AZ för säkerhets kopierings jobb lista](/cli/azure/backup/job#az-backup-job-list) visar en lista över alla säkerhets kopierings jobb (schemalagda eller på begäran) som har körts eller som för närvarande körs på den skyddade databasen, förutom andra åtgärder som registrera, konfigurera säkerhets kopiering och ta bort säkerhetskopierade data.

>[!NOTE]
>Azure Backup anpassas inte automatiskt för sommar tids ändringar vid säkerhets kopiering av en SAP HANA databas som körs på en virtuell Azure-dator.
>
>Ändra principen manuellt efter behov.

## <a name="trigger-an-on-demand-backup"></a>Utlös en säkerhets kopiering på begäran

Medan avsnittet ovan beskriver hur du konfigurerar en schemalagd säkerhets kopiering, pratar det här avsnittet om att utlösa en säkerhets kopiering på begäran. För att göra detta använder vi cmdleten [AZ backup Protection backup-Now](/cli/azure/backup/protection#az-backup-protection-backup-now) .

>[!NOTE]
> Bevarande principen för en säkerhets kopiering på begäran bestäms av den underliggande bevarande principen för databasen.

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

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb status med hjälp av [AZ säkerhets kopierings jobb show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

>[!NOTE]
>Logg säkerhets kopior aktive ras automatiskt och hanteras av SAP HANA internt.

## <a name="next-steps"></a>Nästa steg

* Om du vill lära dig hur du återställer en SAP HANA databas i virtuell Azure-dator med hjälp av CLI fortsätter du till självstudien – [återställa en SAP HANA databas i Azure VM med CLI](tutorial-sap-hana-restore-cli.md)

* Information om hur du säkerhetskopierar en SAP HANA databas som körs på en virtuell Azure-dator med Azure Portal finns i [säkerhetskopiera ett SAP HANA-databaser på virtuella Azure-datorer](./backup-azure-sap-hana-database.md)
