---
title: Självstudie – SAP HANA DB-återställning i Azure med HJÄLP av CLI
description: I den här självstudien lär du dig hur du återställer SAP HANA som körs på en virtuell Azure-dator från ett Azure Backup Recovery Services-valv med Hjälp av Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a249ab63aa72c1d39ab1626e72ff3b2037f3f723
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768459"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: Återställa SAP HANA i en virtuell Azure-dator med Hjälp av Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller via skript. Den här dokumentationen beskriver hur du återställer en säkerhetskopierad databas SAP HANA en virtuell Azure-dator – med hjälp av Azure CLI. Du kan också utföra dessa steg med hjälp av [Azure Portal](./sap-hana-db-restore.md).

Använd [Azure Cloud Shell för](tutorial-sap-hana-backup-cli.md) att köra CLI-kommandon.

I slutet av den här självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Visa återställningspunkter för en säkerhetskopierad databas
> * Återställ en databas

Den här självstudien förutsätter att SAP HANA en databas som körs på en virtuell Azure-dator som säkerhetskopieras med hjälp av Azure Backup. Om du har använt [Back up an SAP HANA database in Azure using CLI](tutorial-sap-hana-backup-cli.md) to back up your SAP HANA database ( Back up an SAP HANA database in Azure using CLI to back up your SAP HANA database, så använder du följande resurser:

* en resursgrupp med *namnet saphanaResourceGroup*
* ett valv *med namnet saphanaVault*
* skyddad container med *namnet VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* säkerhetskopierad databas/objekt med namnet *saphanadatabase;hxe;hxe*
* resurser i *regionen westus2*

## <a name="view-restore-points-for-a-backed-up-database"></a>Visa återställningspunkter för en säkerhetskopierad databas

Om du vill visa listan över alla återställningspunkter för en databas använder du cmdleten [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) enligt följande:

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Listan över återställningspunkter ser ut så här:

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

Som du ser innehåller listan ovan tre återställningspunkter: en för fullständig, differentiell och loggsäkerhetskopia.

>[!NOTE]
>Du kan också visa start- och slutpunkter för varje obruten loggsäkerhetskopieringskedja med hjälp av cmdleten [az backup recoverypoint show-log-chain.](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain)

## <a name="prerequisites-to-restore-a-database"></a>Krav för att återställa en databas

Kontrollera att följande krav är uppfyllda innan du återställer en databas:

* Du kan bara återställa databasen till en SAP HANA instans som finns i samma region
* Målinstansen måste vara registrerad med samma valv som källan
* Azure Backup kan inte identifiera två olika SAP HANA instanser på samma virtuella dator. Därför går det inte att återställa data från en instans till en annan på samma virtuella dator.

## <a name="restore-a-database"></a>Återställ en databas

Azure Backup kan återställa SAP HANA databaser som körs på virtuella Azure-datorer på följande sätt:

* Återställ till ett specifikt datum eller en viss tid (till den andra) med hjälp av loggsäkerhetskopior. Azure Backup automatiskt lämpliga fullständiga differentiella säkerhetskopior och den kedja av loggsäkerhetskopior som krävs för att återställa baserat på den valda tiden.
* Återställ till en specifik fullständig eller differentiell säkerhetskopia för att återställa till en specifik återställningspunkt.

Om du vill återställa en databas använder du [cmdleten az restore restore-azurewl,](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) som kräver ett konfigurationsobjekt för återställning som en av indata. Det här objektet kan genereras med hjälp av [cmdleten az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Konfigurationsobjektet för återställning innehåller all information för att utföra en återställning. En av dem är återställningsläget – **OriginalWorkloadRestore** eller **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** – Återställ data till samma SAP HANA som den ursprungliga källan. Det här alternativet skriver över den ursprungliga databasen. <br>
> **AlternateWorkloadRestore** – Återställ databasen till en alternativ plats och behåll den ursprungliga källdatabasen.

## <a name="restore-to-alternate-location"></a>Återställa till alternativ plats

Om du vill återställa en databas till en alternativ plats använder **du AlternateWorkloadRestore** som återställningsläge. Du måste sedan välja återställningspunkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställningspunkterna.

I den här självstudien får du återställa till en tidigare återställningspunkt. [Visa listan över återställningspunkter](#view-restore-points-for-a-backed-up-database) för databasen och välj den punkt som du vill återställa till. I den här självstudien används återställningspunkten med namnet *7660777527047692711*.

Med hjälp av ovanstående återställningspunktnamn och återställningsläge ska vi skapa konfigurationsobjektet för återställning med hjälp av cmdleten [az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Nu ska vi titta på vad var och en av de återstående parametrarna i denna cmdlet betyder:

* **--target-item-name** Det här är namnet som den återställda databasen kommer att använda. I det här fallet använde vi namnet *restored_database*.
* **--target-server-name** Det här är namnet på SAP HANA server som har registrerats i ett Recovery Services-valv och som finns i samma region som databasen som ska återställas. I den här självstudien återställer vi databasen till samma SAP HANA som vi har skyddat, med namnet *hxehost*.
* **--target-server-type** För återställning av SAP HANA måste **HANAInstance** användas.

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

Svaret på ovanstående fråga är ett konfigurationsobjekt för återställning som ser ut ungefär så här:

```output
{"restore_mode": "AlternateLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "7660777527047692711", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}
```

För att återställa databasen kör du nu [cmdleten az restore restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Om du vill använda det här kommandot anger vi ovanstående json-utdata som sparas i en fil *med namnetrecoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata ser ut så här:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Du får jobbnamnet i svaret. Det här jobbnamnet kan användas för att spåra jobbstatusen med [hjälp av cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-and-overwrite"></a>Återställa och skriva över

För att återställa till den ursprungliga platsen använder vi **OrignialWorkloadRestore** som återställningsläge. Du måste sedan välja återställningspunkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställningspunkterna.

I den här självstudien väljer vi den tidigare tidpunkten "28-11-2019-09:53:00" att återställa till. Du kan ange den här återställningspunkten i följande format: dd-mm-yyyy, dd-mm-yyyy-hh:mm:ss. Om du vill välja en giltig tidpunkt att återställa till använder du cmdleten [az backup recoverypoint show-log-chain,](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) som visar intervallen för obrutna säkerhetskopior av loggkedjan.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

Svaret på ovanstående fråga är ett konfigurationsobjekt för återställning som ser ut så här:

```output
{"restore_mode": "OriginalLocation", "container_uri": " VMAppContainer;Compute;saphanaResourceGroup;saphanaVM ", "item_uri": "SAPHanaDatabase;hxe;hxe", "recovery_point_id": "DefaultRangeRecoveryPoint", "log_point_in_time": "28-11-2019-09:53:00", "item_type": "SAPHana", "source_resource_id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm", "database_name": null, "container_id": null, "alternate_directory_paths": null}"
```

För att återställa databasen kör du nu [cmdleten az restore restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Om du vill använda det här kommandot anger vi ovanstående json-utdata som sparas i en fil *med namnetrecoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Utdata ser ut så här:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

Du får jobbnamnet i svaret. Det här jobbnamnet kan användas för att spåra jobbstatusen med hjälp av [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="restore-as-files"></a>Återställa som filer

För att återställa säkerhetskopierade data som filer i stället för en databas använder vi **RestoreAsFiles** som återställningsläge. Välj sedan återställningspunkten, som antingen kan vara en tidigare tidpunkt eller någon av de tidigare återställningspunkterna. När filerna har återställts till en angiven sökväg kan du ta filerna till valfri SAP HANA dator där du vill återställa dem som en databas. Eftersom du kan flytta dessa filer till valfri dator kan du nu återställa data mellan prenumerationer och regioner.

I den här självstudien väljer vi den tidigare tidpunkt som du vill återställa till och platsen för att dumpa säkerhetskopierade filer som på samma `28-11-2019-09:53:00` `/home/saphana/restoreasfiles` SAP HANA server. Du kan ange den här återställningspunkten i något av följande format: **dd-mm-yyyy eller** **dd-mm-yyyy-hh:mm:ss**. Om du vill välja en giltig tidpunkt att återställa till använder du cmdleten [az backup recoverypoint show-log-chain,](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_show_log_chain) som visar intervallen för obrutna säkerhetskopior av loggkedjan.

Med hjälp av namnet på återställningspunkten ovan och återställningsläget ska vi skapa konfigurationsobjektet för återställning med hjälp av cmdleten [az backup recoveryconfig show.](/cli/azure/backup/recoveryconfig#az_backup_recoveryconfig_show) Nu ska vi titta på vad var och en av de återstående parametrarna i denna cmdlet betyder:

* **--target-container-name** Det här är namnet på SAP HANA server som har registrerats i ett Recovery Services-valv och som finns i samma region som databasen som ska återställas. I den här självstudien återställer vi databasen som filer till samma SAP HANA som vi har skyddat, med namnet *hxehost*.
* **--rp-name** För en tidpunktsåterställning är återställningspunktens namn **DefaultRangeRecoveryPoint**

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

Svaret på frågan ovan är ett konfigurationsobjekt för återställning som ser ut så här:

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

Om du vill återställa databasen som filer kör du [nu cmdleten az restore-azurewl.](/cli/azure/backup/restore#az_backup_restore_restore_azurewl) Om du vill använda det här kommandot anger vi json-utdata ovan som sparas i en fil *med namnetrecoveryconfig.jspå*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output json
```

Utdata ser ut så här:

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

Du får jobbnamnet i svaret. Det här jobbnamnet kan användas för att spåra jobbstatusen med hjälp av cmdleten [az backup job show.](/cli/azure/backup/job#az_backup_job_show)

De filer som dumpar till målcontainern är:

* Säkerhetskopierade databasfiler
* Katalogfiler
* JSON-metadatafiler (för varje säkerhetskopia som ingår)

Normalt ger en nätverksresurssökväg eller sökväg till en monterad Azure-filresurs, när den anges som målsökväg, enklare åtkomst till dessa filer av andra datorer i samma nätverk eller med samma Azure-filresurs monterad på dem.

>[!NOTE]
>Om du vill återställa säkerhetskopierade databasfiler på en Azure-filresurs som monterats på den målregistrerade virtuella datorn kontrollerar du att rotkontot har läs- och skrivbehörighet för Azure-filresursen.

Baserat på vilken typ av återställningspunkt **som** valts ( Tidpunkt eller Fullständig **& Differentiell**) visas en eller flera mappar som skapats i målsökvägen. En av mapparna med `Data_<date and time of restore>` namnet innehåller fullständiga och differentiella säkerhetskopior, och den andra mappen med namnet innehåller `Log` loggsäkerhetskopiorna.

Flytta dessa återställda filer SAP HANA den server där du vill återställa dem som en databas. Följ sedan dessa steg för att återställa databasen:

1. Ange behörigheter för mappen/katalogen där säkerhetskopieringsfilerna lagras med följande kommando:

    ```bash
    chown -R <SID>adm:sapsys <directory>
    ```

1. Kör nästa uppsättning kommandon som `<SID>adm`

    ```bash
    su - <sid>adm
    ```

1. Generera katalogfilen för återställning. Extrahera **BackupId från** JSON-metadatafilen för den fullständiga säkerhetskopian, som kommer att användas senare i återställningsåtgärden. Kontrollera att fullständiga säkerhetskopior och loggsäkerhetskopior finns i olika mappar och ta bort katalogfilerna och JSON-metadatafilerna i dessa mappar.

    ```bash
    hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
    ```

    I kommandot ovan:

    * `<DataFileDir>` – den mapp som innehåller fullständiga säkerhetskopior
    * `<LogFilesDir>` – mappen som innehåller loggsäkerhetskopiorna
    * `<PathToPlaceCatalogFile>` – mappen där den genererade katalogfilen måste placeras

1. Återställ med hjälp av den nyligen genererade katalogfilen via HANA Studio eller kör HDBSQL-återställningsfrågan med den här nyligen genererade katalogen. HDBSQL-frågor visas nedan:

    * Så här återställer du till en tidpunkt:

        Om du skapar en ny återställd databas kör du HDBSQL-kommandot för att skapa en ny databas och stoppar sedan `<DatabaseName>` databasen för återställning. Men om du bara återställer en befintlig databas kör du HDBSQL-kommandot för att stoppa databasen.

        Kör sedan följande kommando för att återställa databasen:

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` – Namnet på den nya databasen eller den befintliga databas som du vill återställa
        * `<Timestamp>` – Exakt tidsstämpel för återställning till tidpunkt
        * `<DatabaseName@HostName>`– Namnet på den databas vars säkerhetskopia  används för återställning och värd-/SAP HANA servernamnet som den här databasen finns på. Alternativet anger att säkerhetskopieringen av data (används för återställning) är av en databas med ett annat SID eller namn än `USING SOURCE <DatabaseName@HostName>` SAP HANA datorn. Därför behöver den inte anges för återställningar som görs på samma HANA-server som säkerhetskopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>` – Sökväg till katalogfilen som genererades i **steg 3**
        * `<DataFileDir>` – den mapp som innehåller fullständiga säkerhetskopior
        * `<LogFilesDir>` – mappen som innehåller loggsäkerhetskopiorna
        * `<BackupIdFromJsonFile>` – **BackupId som** extraherades i **steg 3**

    * Så här återställer du till en viss fullständig eller differentiell säkerhetskopia:

        Om du skapar en ny återställd databas kör du kommandot HDBSQL för att skapa en ny databas och stoppar sedan `<DatabaseName>` databasen för återställning. Men om du bara återställer en befintlig databas kör du HDBSQL-kommandot för att stoppa databasen:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` – namnet på den nya databasen eller den befintliga databas som du vill återställa
        * `<Timestamp>` – den exakta tidsstämpeln för återställning till tidpunkt
        * `<DatabaseName@HostName>`– namnet på den databas vars säkerhetskopia  används för återställning och värd-/SAP HANA som den här databasen finns på. Alternativet anger att säkerhetskopieringen av data (används för återställning) är av en databas med ett annat SID eller namn än `USING SOURCE <DatabaseName@HostName>`  SAP HANA datorn. Därför behöver den inte anges för återställningar som görs på samma HANA-server som säkerhetskopieringen görs från.
        * `<PathToGeneratedCatalogInStep3>` – sökvägen till katalogfilen som genererades i **steg 3**
        * `<DataFileDir>` – den mapp som innehåller fullständiga säkerhetskopior
        * `<LogFilesDir>` – mappen som innehåller loggsäkerhetskopiorna
        * `<BackupIdFromJsonFile>` – **BackupId som** extraherades i **steg 3**

## <a name="next-steps"></a>Nästa steg

* Om du vill lära dig hur SAP HANA hantera databaser som säkerhetskopieras med Azure CLI fortsätter du till självstudien Hantera en SAP HANA-databas i en virtuell [Azure-dator med HJÄLP](tutorial-sap-hana-backup-cli.md) av CLI

* Information om hur du återställer en SAP HANA-databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i Återställa SAP HANA [databaser på virtuella Azure-datorer](./sap-hana-db-restore.md)
