---
title: Uppdatera den befintliga säkerhets kopierings principen för virtuella datorer med CLI
description: Lär dig hur du uppdaterar den befintliga säkerhets kopierings principen för virtuella datorer med hjälp av Azure CLI.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: ba4ded4c82a1eaea7c20ea94da580a8702467b85
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858843"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>Uppdatera den befintliga säkerhets kopierings principen för virtuella datorer med CLI

Du kan använda Azure CLI för att uppdatera en befintlig princip för säkerhets kopiering av virtuella datorer. Den här artikeln beskriver hur du exporterar den befintliga principen till en JSON-fil, ändrar filen och använder sedan Azure CLI för att uppdatera principen med den ändrade principen.

## <a name="modify-an-existing-policy"></a>Ändra en befintlig princip

Följ dessa steg om du vill ändra en befintlig princip för säkerhets kopiering av virtuella datorer:

1. Kör kommandot [AZ backup policy show](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_show) för att hämta information om den princip som du vill uppdatera.

    Exempel:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    Exemplet ovan visar information om en VM-princip med namnet *testing123*.

    Utdata:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. Spara ovanstående utdata i en. JSON-fil. Låt oss till exempel spara det som *Policy.jspå*.
1. Uppdatera JSON-filen baserat på dina krav och spara ändringarna.

    Exempel: om du vill uppdatera vecko kvarhållning till 60 dagar uppdaterar du följande avsnitt i JSON-filen genom att ändra antalet till 60.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. Spara ändringarna.
1. Kör kommandot [AZ backup princip set](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_set) och skicka den fullständiga sökvägen till den uppdaterade JSON-filen som värde för parametern **--policy** .

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>Du kan också hämta exempel-JSON-principen genom att köra [säkerhets kopierings principen AZ-standard-för-VM](https://docs.microsoft.com/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) .

## <a name="next-steps"></a>Nästa steg

- [Hantera virtuella Azure-säkerhetskopieringar med Azure Backup tjänsten](backup-azure-manage-vms.md)
