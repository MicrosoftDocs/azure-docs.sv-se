---
title: Självstudie – SAP HANA DB-återställning på Azure med hjälp av CLI
description: I den här självstudien får du lära dig hur du återställer SAP HANA databaser som körs på en virtuell Azure-dator från ett Azure Backup Recovery Services valv med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e524bfe090f0d67b76c13e876f44e83986aeb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334811"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: återställa SAP HANA-databaser på en virtuell Azure-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommando raden eller genom skript. Den här dokumentationen innehåller information om hur du återställer en säkerhets kopie rad SAP HANA-databas på en virtuell Azure-dator – med Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](./sap-hana-db-restore.md).

Använd [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md) för att köra CLI-kommandon.

I slutet av den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Visa återställnings punkter för en säkerhets kopie rad databas
> * Återställ en databas

Den här självstudien förutsätter att du har en SAP HANA databas som körs på den virtuella Azure-datorn som säkerhets kopie ras med Azure Backup. Om du har använt [säkerhetskopiera en SAP HANA databas i Azure med hjälp av CLI](tutorial-sap-hana-backup-cli.md) för att säkerhetskopiera SAP HANA-databasen använder du följande resurser:

* en resurs grupp med namnet *saphanaResourceGroup*
* ett valv med namnet *saphanaVault*
* skyddad behållare med namnet *VMAppContainer; Compute; saphanaResourceGroup; saphanaVM*
* säkerhetskopierade databasen/objektet med namnet *saphanadatabase; hxe; hxe*
* resurser i regionen *westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Visa återställnings punkter för en säkerhets kopie rad databas

Om du vill visa en lista över alla återställnings punkter för en databas använder du [AZ backup recoverypoint List](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet enligt följande:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Listan över återställnings punkter kommer att se ut så här:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Som du kan se innehåller listan ovan tre återställnings punkter: en för fullständig, differentiell och logg säkerhets kopiering.

>[!NOTE]
>Du kan också visa start-och slut punkterna för varje skadad logg säkerhets kopierings kedja med hjälp av [AZ backup recoverypoint show-log-kedje](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) cmdlet.

## <a name="prerequisites-to-restore-a-database"></a>Krav för att återställa en databas

Kontrol lera att följande krav är uppfyllda innan du återställer en databas:

* Du kan bara återställa databasen till en SAP HANA instans i samma region
* Mål instansen måste registreras med samma valv som källan
* Azure Backup kan inte identifiera två olika SAP HANAs instanser på samma virtuella dator. Därför går det inte att återställa data från en instans till en annan på samma virtuella dator.

## <a name="restore-a-database"></a>Återställ en databas

Azure Backup kan återställa SAP HANA databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett visst datum eller en angiven tidpunkt (till den andra) med hjälp av logg säkerhets kopior. Azure Backup identifierar automatiskt lämpliga fullständiga, differentiella säkerhets kopior och kedja av logg säkerhets kopior som krävs för att återställa baserat på den valda tiden.
* Återställ till en viss fullständig eller differentiell säkerhets kopia för att återställa till en viss återställnings punkt.

Om du vill återställa en databas använder du cmdleten AZ Restore [-azurewl](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) , som kräver ett återställnings konfigurations objekt som en av indata. Det här objektet kan genereras med [AZ backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet. Återställnings konfigurations objekt innehåller all information för att utföra en återställning. En av dem är återställnings läge – **OriginalWorkloadRestore** eller **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – Återställ data till samma SAP HANA instans som den ursprungliga källan. Med det här alternativet skrivs den ursprungliga databasen över. <br>
> **AlternateWorkloadRestore** – Återställ databasen till en annan plats och behåll den ursprungliga käll databasen.

## <a name="restore-to-alternate-location"></a>Återställ till en annan plats

Om du vill återställa en databas till en annan plats använder du **AlternateWorkloadRestore** som återställnings läge. Du måste sedan välja återställnings punkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställnings punkterna.

I den här självstudien kommer du att återställa till en tidigare återställnings punkt. [Visa listan över återställnings punkter](#view-restore-points-for-a-backed-up-database) för databasen och välj den punkt som du vill återställa till. I den här självstudien används återställnings punkten med namnet *7660777527047692711*.

Med hjälp av återställnings punkt namnet ovan och återställnings läget ska vi skapa återställnings konfigurations objekt med hjälp av [AZ backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet. Nu ska vi titta på vad var och en av de återstående parametrarna i denna cmdlet innebär:

* **--mål objekt namn** Detta är det namn som den återställda databasen kommer att använda. I det här fallet använde vi namnet *restored_database*.
* **--mål-server namn** Detta är namnet på en SAP HANA-server som har registrerats till ett Recovery Services-valv och som finns i samma region som databasen som ska återställas. I den här självstudien kommer vi att återställa databasen till samma SAP HANA-server som vi har skyddat, med namnet *hxehost*.
* **--mål-server-typ** **HANAInstance** måste användas för att återställa SAP HANA-databaser.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

Svaret på ovanstående fråga är ett återställnings konfigurations objekt som ser ut ungefär så här:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

För att återställa databasen kör [AZ Restore-azurewl-](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdleten. Om du vill använda det här kommandot anger vi ovanstående JSON-utdata som sparas i en fil med namnet *recoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata kommer att se ut så här:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb statusen med [AZ backup Job show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-and-overwrite"></a>Återställ och skriv över

För att återställa till den ursprungliga platsen använder vi **OrignialWorkloadRestore** som återställnings läge. Du måste sedan välja återställnings punkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställnings punkterna.

I den här självstudien väljer vi den föregående tidpunkten "28-11-2019-09:53:00" för att återställa till. Du kan tillhandahålla den här återställnings punkten i följande format: dd-mm-åååå, dd-mm-åååå-hh: mm: SS. Du kan välja en giltig tidpunkt för återställning till genom att använda cmdleten [AZ backup recoverypoint show-log-kedje](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , som visar intervallen med brutna logg säkerhets kopior.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Svaret på ovanstående fråga är ett återställnings konfigurations objekt som ser ut så här:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

För att återställa databasen kör [AZ Restore-azurewl-](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdleten. Om du vill använda det här kommandot anger vi ovanstående JSON-utdata som sparas i en fil med namnet *recoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata kommer att se ut så här:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb status med hjälp av [AZ säkerhets kopierings jobb show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

## <a name="restore-as-files"></a>Återställ som filer

För att återställa säkerhets kopierings data som filer i stället för en databas använder vi **RestoreAsFiles** som återställnings läge. Välj sedan återställnings punkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställnings punkterna. När filerna har dumpas till en angiven sökväg kan du ta dessa filer till alla SAP HANA datorer där du vill återställa dem som en databas. Eftersom du kan flytta filerna till vilken dator som helst kan du nu återställa data mellan prenumerationer och regioner.

I den här självstudien väljer vi den tidigare tidpunkten `28-11-2019-09:53:00` för att återställa till och platsen för att dumpa säkerhetskopieringsfiler som `/home/saphana/restoreasfiles` på samma SAP HANA-Server. Du kan tillhandahålla den här återställnings punkten i något av följande format: **dd-mm-åååå** eller **dd-mm-åååå-hh: mm: SS**. Du kan välja en giltig tidpunkt för återställning till genom att använda cmdleten [AZ backup recoverypoint show-log-kedje](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-show-log-chain) , som visar intervallen med brutna logg säkerhets kopior.

Med återställnings punkt namnet ovan och återställnings läget ska vi skapa återställnings konfigurations objekt med hjälp av [AZ backup recoveryconfig show](/cli/azure/backup/recoveryconfig#az-backup-recoveryconfig-show) cmdlet. Nu ska vi titta på vad var och en av de återstående parametrarna i denna cmdlet innebär:

* **--mål-container-Name** Detta är namnet på en SAP HANA-server som har registrerats till ett Recovery Services-valv och som finns i samma region som databasen som ska återställas. I den här självstudien kommer vi att återställa databasen som filer till samma SAP HANA-server som vi har skyddat, med namnet *hxehost*.
* **--RP-Name** För en tidpunkt för återställning av återställnings punkter är namnet på återställnings punkten **DefaultRangeRecoveryPoint**

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode RestoreAsFiles \
    --log-point-in-time 28-11-2019-09:53:00 \
    --rp-name DefaultRangeRecoveryPoint \
    --target-container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --filepath /home/saphana/restoreasfiles \
    --output json
```

Svaret på frågan ovan är ett återställnings konfigurations objekt som ser ut så här:

```output
{
  "alternate_directory_paths": null,
  "container_id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupFabrics/Azure/protectionContainers/VMAppContainer;Compute;SAPHANA;hanamachine",
  "container_uri": "VMAppContainer;compute;saphana;hanamachine",
  "database_name": null,
  "filepath": "/home/",
  "item_type": "SAPHana",
  "item_uri": "SAPHanaDatabase;hxe;hxe",
  "log_point_in_time": "04-07-2020-09:53:00",
  "recovery_mode": "FileRecovery",
  "recovery_point_id": "DefaultRangeRecoveryPoint",
  "restore_mode": "AlternateLocation",
  "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/hanamachine"
}
```

Om du nu vill återställa databasen som filer kör du [AZ Restore-azurewl-](/cli/azure/backup/restore#az-backup-restore-restore-azurewl) cmdleten. För att kunna använda det här kommandot anger vi JSON-utdata ovanför som sparas till en fil med namnet *recoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Utdata kommer att se ut så här:

```output
{
  "eTag": null,
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/SAPHANARESOURCEGROUP/providers/Microsoft.RecoveryServices/vaults/SAPHANAVault/backupJobs/608e737e-c001-47ca-8c37-57d909c8a704",
  "location": null,
  "name": "608e737e-c001-47ca-8c37-57d909c8a704",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "7ddd3c3a-c0eb-11ea-a5f8-54ee75ec272a",
    "backupManagementType": "AzureWorkload",
    "duration": "0:00:01.781847",
    "endTime": null,
    "entityFriendlyName": "HXE [hxehost]",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Job Type": "Restore as files"
      },
      "tasksList": [
        {
          "status": "InProgress",
          "taskId": "Transfer data from vault"
        }
      ]
    },
    "jobType": "AzureWorkloadJob",
    "operation": "Restore",
    "startTime": "2020-07-08T07:20:29.336434+00:00",
    "status": "InProgress",
    "workloadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Med svaret får du jobb namnet. Det här jobb namnet kan användas för att spåra jobb status med hjälp av [AZ säkerhets kopierings jobb show](/cli/azure/backup/job#az-backup-job-show) cmdlet.

Filerna som dumpas till mål behållaren är:

* Säkerhetskopieringsfiler för databas
* Katalogfiler
* JSON-metadatafiler (för varje säkerhets kopierings fil som berörs)

Normalt möjliggör en nätverks resurs Sök väg eller sökväg till en monterad Azure-filresurs när den anges som mål Sök väg, vilket ger enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs som är monterad på dem.

>[!NOTE]
>Om du vill återställa säkerhets kopian av databasen på en Azure-filresurs som är monterad på den virtuella mål datorn, kontrollerar du att rot kontot har Läs-/Skriv behörighet för Azure-filresursen.

Baserat på vilken typ av återställnings punkt som valts (**tidpunkt** eller **fullständig & differentiell**) visas en eller flera mappar som skapats i mål Sök vägen. En av mapparna `Data_<date and time of restore>` innehåller fullständiga och differentiella säkerhets kopior och den andra mappen `Log` innehåller logg säkerhets kopiorna.

Flytta de återställda filerna till den SAP HANA server där du vill återställa dem som en databas. Följ sedan de här stegen för att återställa databasen:

1. Ange behörigheter för mappen/katalogen där de säkerhetskopierade filerna lagras med följande kommando:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Kör nästa uppsättning kommandon som `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Generera katalog filen för återställning. Extrahera **BackupId** från JSON-metadatafilen för fullständig säkerhets kopiering, vilket kommer att användas senare i återställnings åtgärden. Se till att fullständiga säkerhets kopior och logg säkerhets kopior finns i olika mappar och ta bort katalogfiler och JSON-metadatafiler i dessa mappar.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    I kommandot ovan:

    * `<DataFileDir>` – mappen som innehåller fullständiga säkerhets kopior
    * `<LogFilesDir>` – mappen som innehåller säkerhets kopiorna av loggen
    * `<PathToPlaceCatalogFile>` – mappen där katalog filen som genereras måste placeras

1. Återställ med den nyligen genererade katalog filen via HANA Studio eller kör HDBSQL Restore Query med den här nyligen genererade katalogen. HDBSQL-frågor visas nedan:

    * Så här återställer du till en tidpunkt:

        Om du skapar en ny återställd databas kör du kommandot HDBSQL för att skapa en ny databas `<DatabaseName>` och stoppar sedan databasen för återställning. Men om du bara återställer en befintlig databas kan du stoppa databasen genom att köra kommandot HDBSQL.

        Kör sedan följande kommando för att återställa databasen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – Namnet på den nya databasen eller en befintlig databas som du vill återställa
        * `<Timestamp>` -Exakt tidsstämpel för tidpunkten för återställning
        * `<DatabaseName@HostName>` – Namnet på den databas vars säkerhets kopia används för återställning och namnet på den **värd** /SAP HANA server som databasen finns på. `USING SOURCE <DatabaseName@HostName>`Alternativet anger att data säkerhets kopieringen (som används för återställning) är av en databas med ett annat sid eller namn än mål SAP HANA datorn. Det behöver därför inte anges för återställningar som har utförts på samma HANA-server som säkerhets kopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>` – Sökväg till katalog filen som genererades i **steg 3**
        * `<DataFileDir>` – mappen som innehåller fullständiga säkerhets kopior
        * `<LogFilesDir>` – mappen som innehåller säkerhets kopiorna av loggen
        * `<BackupIdFromJsonFile>` – **BackupId** extraherades i **steg 3**

    * Återställa till en viss fullständig eller differentiell säkerhets kopia:

        Om du skapar en ny återställd databas kör du kommandot HDBSQL för att skapa en ny databas `<DatabaseName>` och stoppar sedan databasen för återställning. Om du bara återställer en befintlig databas ska du dock köra kommandot HDBSQL för att stoppa databasen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – namnet på den nya databasen eller den befintliga databasen som du vill återställa
        * `<Timestamp>` – den exakta tidsstämpeln för tidpunkten i återställningen
        * `<DatabaseName@HostName>` – namnet på den databas vars säkerhets kopia används för återställning och namnet på den **värd** /SAP HANA server som databasen finns på. `USING SOURCE <DatabaseName@HostName>`Alternativet anger att data säkerhets kopieringen (som används för återställning) är av en databas med ett annat sid eller namn än mål SAP HANA datorn. Så det behöver inte anges för återställningar som har utförts på samma HANA-server som säkerhets kopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>` – sökvägen till katalog filen som genererades i **steg 3**
        * `<DataFileDir>` – mappen som innehåller fullständiga säkerhets kopior
        * `<LogFilesDir>` – mappen som innehåller säkerhets kopiorna av loggen
        * `<BackupIdFromJsonFile>` – **BackupId** extraherades i **steg 3**

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du hanterar SAP HANA databaser som säkerhets kopie ras med Azure CLI fortsätter du till självstudien [hantera en SAP HANA databas i virtuell Azure-dator med CLI](tutorial-sap-hana-backup-cli.md)

* Information om hur du återställer en SAP HANA databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i [återställa en SAP HANA-databas på virtuella Azure-datorer](./sap-hana-db-restore.md)
