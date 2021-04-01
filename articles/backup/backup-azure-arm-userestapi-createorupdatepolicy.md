---
title: Skapa säkerhets kopierings principer med REST API
description: I den här artikeln får du lära dig hur du skapar och hanterar säkerhets kopierings principer (schema och kvarhållning) med hjälp av REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: e4e6f5b5cf28c3830a91a494ea60680eee1546f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89179614"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Skapa principer för Azure Recovery Services säkerhets kopiering med REST API

Stegen för att skapa en säkerhets kopierings princip för ett Azure Recovery Services-valv beskrivs i [princip REST API dokumentet](/rest/api/backup/protectionpolicies/createorupdate). Vi använder det här dokumentet som referens för att skapa en princip för säkerhets kopiering av virtuella Azure-datorer.

## <a name="create-or-update-a-policy"></a>Skapa eller uppdatera en princip

Om du vill skapa eller uppdatera en Azure Backup-princip använder *du följande åtgärd*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

`{policyName}`Och `{vaultName}` anges i URI: n. Ytterligare information finns i begär ande texten.

## <a name="create-the-request-body"></a>Skapa begär ande texten

Om du till exempel vill skapa en princip för säkerhets kopiering av virtuella Azure-datorer, följer du komponenterna i begär ande texten.

|Name  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|properties     |   Sant      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | Egenskaper för ProtectionPolicyResource        |
|tags     |         | Objekt        |  Resurstaggar       |

En fullständig lista över definitioner i begär ande texten finns i [säkerhets kopierings policyn REST API-dokument](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Exempel på begär ande text

Följande brödtext för begäran definierar en säkerhets kopierings policy för virtuella Azure-säkerhetskopieringar.

Principen säger:

- Ta en veckovis säkerhets kopia varje måndag, onsdag, torsdag vid 10:00 AM Stilla havs tid, normal tid.
- Behåll de säkerhets kopior som gjorts varje måndag, onsdag, torsdag i en vecka.
- Behåll de säkerhets kopior som har gjorts under varje första onsdag och tredje torsdag i månaden i två månader (åsidosätter de tidigare bevarande villkoren, om sådana finns).
- Behåll de säkerhets kopior som gjorts den fjärde måndagen och fjärde torsdagen i februari och november i fyra år (åsidosätter de tidigare bevarande villkoren, om sådana finns).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> Tids formaten för schema och kvarhållning stöder endast DateTime. De har inte stöd för själva tids formatet.

## <a name="responses"></a>Svar

Skapande/uppdatering av säkerhets kopierings policy är en [asynkron åtgärd](../azure-resource-manager/management/async-operations.md). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [Skydd PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 accepterad     |         |     Har godkänts    |

### <a name="example-responses"></a>Exempel svar

När du skickar in *begäran om att skapa* eller uppdatera en princip är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation huvud med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

När åtgärden har slutförts returneras 200 (OK) med princip innehållet i svars texten.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Om en princip redan används för att skydda ett objekt leder alla uppdateringar i principen till att [ändra skyddet](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) för alla sådana associerade objekt.

## <a name="next-steps"></a>Nästa steg

[Aktivera skydd för en oskyddad virtuell Azure-dator](backup-azure-arm-userestapi-backupazurevms.md).

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)
