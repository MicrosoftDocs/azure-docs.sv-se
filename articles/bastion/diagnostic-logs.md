---
title: Aktivera och arbeta med resurs loggar för Azure skydds
description: I den här artikeln får du lära dig hur du aktiverar och arbetar med Azure skydds diagnostikloggar.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 1e76fffd17ee565d4103ca8a7bf1523bbd16209d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445379"
---
# <a name="enable-and-work-with-bastion-resource-logs"></a>Aktivera och arbeta med Bastion-resursloggar

När användarna ansluter till arbets belastningar med hjälp av Azure-skydds kan skydds logga diagnostik för fjärrsessionerna. Du kan sedan använda diagnostiken för att visa vilka användare som är anslutna till vilka arbets belastningar, vid vilken tidpunkt, och annan relevant loggnings information. Du måste aktivera diagnostikloggar i Azure skydds för att kunna använda diagnostiken. Den här artikeln hjälper dig att aktivera diagnostikloggar och sedan visa loggarna.

## <a name="enable-the-resource-log"></a><a name="enable"></a>Aktivera resurs loggen

1. I [Azure Portal](https://portal.azure.com)navigerar du till din Azure skydds-resurs och väljer **diagnostikinställningar** på sidan Azure skydds.

   ![Skärm bild som visar sidan "diagnostikinställningar".](./media/diagnostic-logs/1diagnostics-settings.png)
2. Välj **diagnostikinställningar** och välj sedan **+ Lägg till diagnostisk inställning** för att lägga till ett mål för loggarna.

   ![Skärm bild som visar sidan "diagnostikinställningar" med knappen "Lägg till diagnostikinställningar" vald.](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. På sidan **diagnostikinställningar** väljer du den typ av lagrings konto som ska användas för att lagra diagnostikloggar.

   ![Skärm bild av sidan "diagnostikinställningar" med avsnittet för att välja en lagrings plats markerad.](./media/diagnostic-logs/3add-storage-account.png)
4. När du har slutfört inställningarna ser det ut ungefär som i det här exemplet:

   ![exempel inställningar](./media/diagnostic-logs/4example-settings.png)

## <a name="view-diagnostics-log"></a><a name="view"></a>Visa diagnostisk logg

För att få åtkomst till dina diagnostikloggar kan du direkt använda det lagrings konto som du angav när du aktiverade diagnostikinställningar.

1. Navigera till lagrings konto resursen och sedan till **behållare**. Du ser den **Insights-logs-bastionauditlogs-** blob som skapats i BLOB-behållaren för lagrings kontot.

   ![diagnostikinställningar](./media/diagnostic-logs/1-navigate-to-logs.png)
2. När du navigerar till i behållaren visas olika mappar i din blogg. Dessa mappar anger resurs-hierarkin för din Azure skydds-resurs.

   ![Lägg till diagnostisk inställning](./media/diagnostic-logs/2-resource-h.png)
3. Navigera till den fullständiga hierarkin för din Azure skydds-resurs vars diagnostikloggar du vill få åtkomst till/Visa. Y =, ', = ', ' h = ' och ' = ' anger år, månad, dag, timme respektive minut för resurs loggarna.

   ![Välj lagrings plats](./media/diagnostic-logs/3-resource-location.png)
4. Leta upp den JSON-fil som skapats av Azure-skydds som innehåller diagnostikdata för den tids period som du har navigerat till.

5. Ladda ned JSON-filen från lagrings-BLOB-behållaren. En exempel post från JSON-filen visas nedan som referens:

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>Nästa steg

Läs [vanliga frågor och svar om skydds](bastion-faq.md).
