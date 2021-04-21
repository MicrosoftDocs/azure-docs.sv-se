---
title: 'Självstudie: Hantera säkerhetskopierade SAP HANA DB med CLI'
description: I den här självstudien lär du dig att hantera säkerhetskopierade databaser SAP HANA som körs på en virtuell Azure-dator med Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7090701e3642fd9703737060e0876c8bbfc27994
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765187"
---
# <a name="tutorial-manage-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Självstudie: Hantera SAP HANA i en virtuell Azure-dator med Hjälp av Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller via skript. Den här dokumentationen beskriver hur du hanterar en säkerhetskopierad databas SAP HANA virtuella Azure-datorer – allt med hjälp av Azure CLI. Du kan också utföra de här stegen med [hjälp av Azure Portal](./sap-hana-db-manage.md).

Använd [Azure Cloud Shell för](tutorial-sap-hana-backup-cli.md) att köra CLI-kommandon.

När du är färdig med självstudien kommer du att kunna:

> [!div class="checklist"]
>
> * Övervaka säkerhetskopierings- och återställningsjobb
> * Skydda nya databaser som lagts till i en SAP HANA instans
> * Ändra principen
> * Sluta skydda
> * Återuppta skyddet

Om du har använt Back up an SAP HANA database in Azure using CLI to back up your SAP HANA database ( Back [up an SAP HANA database in Azure using CLI](tutorial-sap-hana-backup-cli.md) to back up your SAP HANA database, så använder du följande resurser:

* en resursgrupp med *namnet saphanaResourceGroup*
* ett valv *med namnet saphanaVault*
* skyddad container med *namnet VMAppContainer; Compute;saphanaResourceGroup;saphanaVM*
* säkerhetskopierad databas/objekt med namnet *saphanadatabase;hxe;hxe*
* resurser i *regionen westus2*

Azure CLI gör det enkelt att hantera en SAP HANA databas som körs på en virtuell Azure-dator som säkerhetskopieras med hjälp av Azure Backup. Den här självstudien beskriver var och en av hanteringsåtgärder.

## <a name="monitor-backup-and-restore-jobs"></a>Övervaka säkerhetskopierings- och återställningsjobb

Om du vill övervaka slutförda eller jobb som körs (säkerhetskopiering eller återställning) använder du cmdleten [az backup job list.](/cli/azure/backup/job#az_backup_job_list) Med CLI kan du också [pausa ett jobb som körs eller](/cli/azure/backup/job#az_backup_job_stop) vänta [tills ett jobb har slutförts.](/cli/azure/backup/job#az_backup_job_wait)

```azurecli-interactive
az backup job list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --output table
```

Utdata ser ut ungefär så här:

```output
Name                                  Operation              Status      Item Name       Start Time UTC
------------------------------------  ---------------        ---------   ----------      -------------------  
e0f15dae-7cac-4475-a833-f52c50e5b6c3  ConfigureBackup        Completed   hxe             2019-12-03T03:09:210831+00:00  
ccdb4dce-8b15-47c5-8c46-b0985352238f  Backup (Full)          Completed   hxe [hxehost]   2019-12-01T10:30:58.867489+00:00
4980af91-1090-49a6-ab96-13bc905a5282  Backup (Differential)  Completed   hxe [hxehost]   2019-12-01T10:36:00.563909+00:00
F7c68818-039f-4a0f-8d73-e0747e68a813  Restore (Log)          Completed   hxe [hxehost]   2019-12-03T05:44:51.081607+00:00
```

## <a name="change-policy"></a>Ändra princip

Om du vill ändra den underliggande principen SAP HANA säkerhetskopieringskonfigurationen använder du cmdleten [az backup policy set.](/cli/azure/backup/policy#az_backup_policy_set) Namnparametern i den här cmdleten refererar till säkerhetskopieringsobjektet vars princip vi vill ändra. I den här självstudien ersätter vi principen för vår SAP HANA-databas *saphanadatabase;hxe;hxe* med en ny *princip newsaphanaPolicy*. Nya principer kan skapas med hjälp av [cmdleten az backup policy create.](/cli/azure/backup/policy#az_backup_policy_create)

```azurecli-interactive
az backup item set policy --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name newsaphanaPolicy \
    --name saphanadatabase;hxe;hxe \
```

Resultatet bör se ut så här:

```output
Name                                  Resource Group
------------------------------------- --------------
cb110094-9b15-4c55-ad45-6899200eb8dd  SAPHANA
```

## <a name="create-incremental-backup-policy"></a>Skapa princip för inkrementell säkerhetskopiering

Om du vill skapa en princip för inkrementell [säkerhetskopiering](/cli/azure/backup/policy#az_backup_policy_create) kör du kommandot az backup policy create med följande parametrar:

* **--backup-management-type** – Azure Workload
* **--workload-type** – SAPHana
* **--name** – Namnet på principen
* **--policy** – JSON-fil med lämplig information för schema och kvarhållning
* **--resource-group** – Resursgrupp för valvet
* **--vault-name** – Namnet på valvet

Exempel:

```azurecli
az backup policy create --resource-group saphanaResourceGroup --vault-name saphanaVault --name sappolicy --backup-management-type AzureWorkload --policy sappolicy.json --workload-type SAPHana
```

Exempel på JSON (sappolicy.jspå):

```json
  "eTag": null,
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/saphanaResourceGroup/providers/Microsoft.RecoveryServices/vaults/saphanaVault/backupPolicies/sappolicy",
  "location": null,
  "name": "sappolicy",
  "properties": {
    "backupManagementType": "AzureWorkload",
    "makePolicyConsistent": null,
    "protectedItemsCount": 0,
    "settings": {
      "isCompression": false,
      "issqlcompression": false,
      "timeZone": "UTC"
    },
    "subProtectionPolicy": [
      {
        "policyType": "Full",
        "retentionPolicy": {
          "dailySchedule": null,
          "monthlySchedule": {
            "retentionDuration": {
              "count": 60,
              "durationType": "Months"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "retentionPolicyType": "LongTermRetentionPolicy",
          "weeklySchedule": {
            "daysOfTheWeek": [
              "Sunday"
            ],
            "retentionDuration": {
              "count": 104,
              "durationType": "Weeks"
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          },
          "yearlySchedule": {
            "monthsOfYear": [
              "January"
            ],
            "retentionDuration": {
              "count": 10,
              "durationType": "Years"
            },
            "retentionScheduleDaily": null,
            "retentionScheduleFormatType": "Weekly",
            "retentionScheduleWeekly": {
              "daysOfTheWeek": [
                "Sunday"
              ],
              "weeksOfTheMonth": [
                "First"
              ]
            },
            "retentionTimes": [
              "2021-01-19T00:30:00+00:00"
            ]
          }
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Sunday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2021-01-19T00:30:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Incremental",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 30,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "schedulePolicyType": "SimpleSchedulePolicy",
          "scheduleRunDays": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday"
          ],
          "scheduleRunFrequency": "Weekly",
          "scheduleRunTimes": [
            "2017-03-07T02:00:00+00:00"
          ],
          "scheduleWeeklyFrequency": 0
        }
      },
      {
        "policyType": "Log",
        "retentionPolicy": {
          "retentionDuration": {
            "count": 15,
            "durationType": "Days"
          },
          "retentionPolicyType": "SimpleRetentionPolicy"
        },
        "schedulePolicy": {
          "scheduleFrequencyInMins": 120,
          "schedulePolicyType": "LogSchedulePolicy"
        }
      }
    ],
    "workLoadType": "SAPHanaDatabase"
  },
  "resourceGroup": "saphanaResourceGroup",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
} 
```

När principen har skapats visar kommandots utdata den princip-JSON som du skickade som en parameter när du körde kommandot.

Du kan ändra följande avsnitt i principen för att ange önskad säkerhetskopieringsfrekvens och kvarhållning för inkrementella säkerhetskopieringar.

Exempel:

```json
{
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 30,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Monday",
      "Tuesday",
      "Wednesday",
      "Thursday",
      "Friday",
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

Exempel:

Om du bara vill ha inkrementella säkerhetskopieringar på lördagar och behålla dem i 60 dagar gör du följande ändringar i principen:

* Uppdatera **retentionDuration** count till 60 dagar
* Ange endast lördag som **ScheduleRunDays**

```json
 {
  "policyType": "Incremental",
  "retentionPolicy": {
    "retentionDuration": {
      "count": 60,
      "durationType": "Days"
    },
    "retentionPolicyType": "SimpleRetentionPolicy"
  },
  "schedulePolicy": {
    "schedulePolicyType": "SimpleSchedulePolicy",
    "scheduleRunDays": [
      "Saturday"
    ],
    "scheduleRunFrequency": "Weekly",
    "scheduleRunTimes": [
      "2017-03-07T02:00:00+00:00"
    ],
    "scheduleWeeklyFrequency": 0
  }
}
```

## <a name="protect-new-databases-added-to-an-sap-hana-instance"></a>Skydda nya databaser som lagts till i en SAP HANA instans

[När du registrerar SAP HANA instans med ett Recovery Services-valv](tutorial-sap-hana-backup-cli.md#register-and-protect-the-sap-hana-instance) upptäcks automatiskt alla databaser på den här instansen.

Men om nya databaser läggs till i SAP HANA instansen senare använder du cmdleten [az backup protectable-item initialize.](/cli/azure/backup/protectable-item#az_backup_protectable_item_initialize) Denna cmdlet identifierar de nya databaser som lagts till.

```azurecli-interactive
az backup protectable-item initialize --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --workload-type SAPHANA
```

Använd sedan cmdleten [az backup protectable-item list](/cli/azure/backup/protectable-item#az_backup_protectable_item_list) för att lista alla databaser som har identifierats på din SAP HANA instans. Den här listan utesluter dock de databaser där säkerhetskopieringen redan har konfigurerats. När databasen som ska säkerhetskopieras har identifierats kan du gå till  [Aktivera säkerhetskopiering SAP HANA databasen](tutorial-sap-hana-backup-cli.md#enable-backup-on-sap-hana-database).

```azurecli-interactive
az backup protectable-item list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --workload-type SAPHANA \
    --output table
```

Den nya databasen som du vill backa upp visas i den här listan, som ser ut så här:

```output
Name                            Protectable Item Type    ParentName    ServerName    IsProtected
---------------------------     ----------------------   ------------  -----------   ------------
saphanasystem;hxe               SAPHanaSystem            HXE           hxehost       NotProtected  
saphanadatabase;hxe;systemdb    SAPHanaDatabase          HXE           hxehost       NotProtected
saphanadatabase;hxe;newhxe      SAPHanaDatabase          HXE           hxehost       NotProtected
```

## <a name="stop-protection-for-an-sap-hana-database"></a>Stoppa skyddet för en SAP HANA databas

Du kan sluta skydda en SAP HANA databas på ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb och lämna återställningspunkterna intakta.

Om du väljer att lämna återställningspunkter bör du tänka på följande:

* Alla återställningspunkter förblir intakta för alltid och all rensning stoppas vid stoppskydd med kvarhållna data.
* Du debiteras för den skyddade instansen och den förbrukade lagringen.
* Om du tar bort en datakälla utan att stoppa säkerhetskopieringar misslyckas nya säkerhetskopieringar.

Nu ska vi titta närmare på vart och ett av sätten att stoppa skyddet.

### <a name="stop-protection-with-retain-data"></a>Avbryt skyddet med kvarhållning av data

Om du vill stoppa skyddet med kvarhållna data använder du [cmdleten az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --output table
```

Resultatet bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrollera statusen för den här åtgärden använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

### <a name="stop-protection-without-retain-data"></a>Stoppa skyddet utan att behålla data

Om du vill stoppa skyddet utan att behålla data använder du [cmdleten az backup protection disable.](/cli/azure/backup/protection#az_backup_protection_disable)

```azurecli-interactive
az backup protection disable --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --workload-type SAPHANA \
    --delete-backup-data true \
    --output table
```

Resultatet bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
g0f15dae-7cac-4475-d833-f52c50e5b6c3  saphanaResourceGroup
```

Om du vill kontrollera statusen för den här åtgärden använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="resume-protection"></a>Återuppta skyddet

När du stoppar skyddet för SAP HANA med kvarhållna data kan du senare återuppta skyddet. Om du inte behåller säkerhetskopierade data kan du inte återuppta skyddet.

Om du vill återuppta skyddet använder du [cmdleten az backup protection resume.](/cli/azure/backup/protection#az_backup_protection_resume)

```azurecli-interactive
az backup protection resume --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --policy-name saphanaPolicy \
    --output table
```

Resultatet bör se ut så här:

```output
Name                                  ResourceGroup
------------------------------------  ---------------  
b2a7f108-1020-4529-870f-6c4c43e2bb9e  saphanaResourceGroup
```

Om du vill kontrollera statusen för den här åtgärden använder du [cmdleten az backup job show.](/cli/azure/backup/job#az_backup_job_show)

## <a name="next-steps"></a>Nästa steg

* Information om hur du säkerhetskopierar en SAP HANA-databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i Säkerhetskopiering SAP HANA databaser på virtuella [Azure-datorer](./backup-azure-sap-hana-database.md)

* Information om hur du hanterar en säkerhetskopierad SAP HANA-databas som körs på en virtuell Azure-dator med hjälp av Azure Portal finns i Hantera säkerhetskopierade SAP HANA databaser på [virtuella Azure-datorer](./sap-hana-db-manage.md)