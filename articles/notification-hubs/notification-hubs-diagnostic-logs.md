---
title: Azure Notification Hubs-diagnostikloggar | Microsoft Docs
description: Den här artikeln innehåller en översikt över alla operativa och diagnostiska loggar som är tillgängliga för Azure Notification Hubs.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b532dca6ceba44a32132bf64b322e1b4764fd5fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418143"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Aktivera diagnostikloggar för Notification Hubs

När du börjar använda ditt Azure Notification Hubs-namnområde kanske du vill övervaka hur och när ditt namn område skapas, tas bort eller nås. Den här artikeln innehåller en översikt över alla operativa och diagnostiska loggar som är tillgängliga.

Azure Notification Hubs stöder för närvarande aktivitets-och drift loggar som samlar in *hanterings åtgärder* som utförs på Azure Notification Hubs-namnområdet.

## <a name="diagnostic-logs-schema"></a>Schema för diagnostiska loggar

Alla loggar lagras i JavaScript Object Notation (JSON)-format på följande två platser:

- **AzureActivity**: visar loggar från åtgärder och åtgärder som utförs mot ditt namn område i Azure Portal eller genom Azure Resource Manager mall distributioner.
- **AzureDiagnostics**: visar loggar från åtgärder och åtgärder som utförs mot ditt namn område med hjälp av API: et, eller via hanterings klienter på språk-SDK: n.

I JSON-strängarna för diagnostikloggar ingår de element som anges i följande tabell:

| Namn | Beskrivning |
| ------- | ------- |
| time | UTC-tidsstämpel för loggen |
| resourceId | Relativ sökväg till Azure-resursen |
| operationName | Namn på hanterings åtgärden |
| category | Logg kategori. Giltiga värden: `OperationalLogs` |
| callerIdentity | Identitet för den anropare som initierade hanterings åtgärden |
| resultType | Hanterings åtgärdens status. Giltiga värden: `Succeeded` eller `Failed` |
| resultDescription | Beskrivning av hanterings åtgärden |
| correlationId | Korrelations-ID för hanterings åtgärden (om det anges) |
| callerIpAddress | IP-adress för anroparen. Tomt för anrop som kommer från Azure Portal |

Här är ett exempel på en fungerande logg-JSON-sträng:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

`callerIdentity`Fältet kan vara tomt eller en JSON-sträng med något av följande format.

För anrop som härstammar från Azure Portal `identity` är fältet tomt. Loggen kan korreleras med aktivitets loggar för att fastställa den inloggade användaren.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

För anrop som görs via Azure Resource Manager `identity` kommer fältet att innehålla användar namnet för den inloggade användaren.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

För anrop till Notification Hubs REST API `identity` fältet innehåller namnet på åtkomst principen som används för att generera SharedAccessSignature-token.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Händelser och åtgärder som har registrerats i drift loggar

Drift loggar fångar alla hanterings åtgärder som utförs på Azure Notification Hubs-namnrymden. Data åtgärder samlas inte in på grund av den stora mängden data åtgärder som utförs på Azure Notification Hubs.

Följande hanterings åtgärder samlas in i drift loggar: 

| Omfång | Åtgärds namn | Åtgärds Beskrivning |
| :-- | :-- | :-- |
| Namnområde | Microsoft. NotificationHubs/namnrymder/authorizationRules/åtgärd | Lista auktoriseringsregler |
| Namnområde | Microsoft. NotificationHubs/Namespaces/authorizationRules/Delete | Ta bort auktoriseringsregel |
| Namnområde | Microsoft. NotificationHubs/Namespaces/authorizationRules/listnycklar/åtgärd | Lista nycklar |
| Namnområde | Microsoft. NotificationHubs/namnrymder/authorizationRules/Read | Hämta auktoriseringsregel |
| Namnområde | Microsoft. NotificationHubs/Namespaces/authorizationRules/regenerateKeys/åtgärd | Återskapa nycklar |
| Namnområde | Microsoft. NotificationHubs/namnrymder/authorizationRules/Write | Skapa eller uppdatera auktoriseringsregel |
| Namnområde | Microsoft. NotificationHubs/Namespaces/Delete | Ta bort namnrymd |
| Namnområde | Microsoft. NotificationHubs/namnrymder/läsa | Hämta namnrymd |
| Namnområde | Microsoft. NotificationHubs/namnrymder/skrivning | Skapa eller uppdatera namnrymd |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules/åtgärd | Lista auktoriseringsregler |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules/Delete | Ta bort auktoriseringsregel |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listnycklar/åtgärd | Lista nycklar |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules/Read | Läs auktoriseringsregel |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/åtgärd | Återskapa nycklar |
| Notification Hubs | Microsoft. NotificationHubs/namnrymder/NotificationHubs/authorizationRules/Write | Skapa eller uppdatera auktoriseringsregel |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub |
| Notification Hubs | Microsoft. NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/åtgärd | Skapa, uppdatera eller hämta PNS-autentiseringsuppgifter |
| Notification Hubs | Microsoft. NotificationHubs/namnrymder/NotificationHubs/Read | Hämta Notification Hub |
| Notification Hubs | Microsoft. NotificationHubs/namnrymder/NotificationHubs/Write | Skapa eller uppdatera Notification Hub |

## <a name="enable-operational-logs"></a>Aktivera drift loggar

Drift loggar är inaktiverade som standard. Gör så här för att aktivera loggar:

1. I [Azure Portal](https://portal.azure.com)går du till ditt Azure Notification Hubs-namnområde och väljer sedan **diagnostiska inställningar** under **övervakning**.

   ![Länken "diagnostikinställningar"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. I fönstret **diagnostikinställningar** väljer du **Lägg till diagnostisk inställning**.  

   ![Länken "Lägg till diagnostisk inställning"](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Konfigurera diagnostikinställningar genom att göra följande:

   a. I rutan **namn** anger du ett namn för diagnostikinställningar.  

   b. Välj någon av följande tre destinationer för dina diagnostikloggar:  
   - Om du väljer **Skicka till Log Analytics arbets yta** måste du ange vilken instans av Log Analytics diagnostiken ska skickas till.  
   - Om du väljer **Arkiv till ett lagrings konto** måste du konfigurera lagrings kontot där diagnostikloggar ska lagras.  
   - Om du väljer **Stream till en Event Hub** måste du konfigurera den händelsehubben som du vill strömma diagnostikloggar till.

   c. Markera kryss rutan **OperationalLogs** .

    ![Fönstret diagnostikinställningar](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Välj **Spara**.

De nya inställningarna börjar gälla om 10 minuter. Loggarna visas i det konfigurerade lagrings målet i fönstret **diagnostikloggar** .

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar diagnostikinställningar finns i:
* [Översikt över Azure Diagnostics-loggar](../azure-monitor/platform/platform-logs-overview.md).

Mer information om Azure Notification Hubs finns i:
* [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

