---
title: Snabb start – skapa en Azure Notification Hub med hjälp av Azure CLI | Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en Azure Notification Hub med hjälp av Azure CLI.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563757"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snabb start: skapa en Azure Notification Hub med Azure CLI

Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabb starten skapar du en Notification Hub med hjälp av Azure CLI. Det första avsnittet innehåller steg för steg hur du skapar ett Notification Hubs namn område. Det andra avsnittet innehåller steg för steg hur du skapar en Notification Hub i ett befintligt namn område. Du lär dig också hur du skapar en anpassad åtkomst princip.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs kräver version 2.0.67 eller senare av Azure CLI. Kör [az version](/cli/azure/reference-index#az_version) om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Notification Hubs, precis som alla Azure-resurser, måste distribueras till en resurs grupp.  Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.  Se [Vad är Azure Resource Manager](../azure-resource-manager/management/overview.md) för att lära dig mer om resurs grupper.

I den här snabb starten skapar du en resurs grupp med namnet **spnhubrg** på den **östra** platsen med följande [AZ Group Create](/cli/azure/group#az-group-create) -kommando.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Skapa ett Notification Hubs-namnområde

1. Skapa ett namn område för dina Notification Hub.

   Ett namn område innehåller ett eller flera hubbar, och namnet måste vara unikt för alla Azure-prenumerationer och vara minst sex tecken långt. Om du vill kontrol lera tillgängligheten för ett namn använder du kommandot [AZ Notification-Hub namn område check-Availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) .

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI svarar på din begäran om tillgänglighet genom att visa följande konsol utdata:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Lägg märke till den andra raden i Azure CLI-svaret, `"isAvailable": true` . Den här raden läser `false` om det önskade namnet som du har angett för namn området inte är tillgängligt. När du har bekräftat att namnet är tillgängligt kör du kommandot [AZ Notification-Hub namespace Create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) för att skapa ditt namn område.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Om `--name` du har angett `az notification-hub namespace create` kommandot inte är tillgängligt eller inte uppfyller [namngivnings reglerna och begränsningarna för Azure-resurser](../azure-resource-manager/management/resource-name-rules.md), svarar Azure CLI med följande konsol utdata:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Om det första namnet inte lyckas väljer du ett annat namn för det nya namn området och kör `az notification-hub namespace create` kommandot igen.

   > [!NOTE]
   > I det här steget framåt måste du ersätta värdet för `--namespace` parametern i varje Azure CLI-kommando som du kopierar från den här snabb starten.

2. Hämta en lista över namn områden.

   Om du vill se information om det nya namn området använder du kommandot [AZ Notification – Hub namespace List](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) . `--resource-group`Parametern är valfri om du vill visa alla namn områden för en prenumeration.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Skapa Notification Hub

1. Skapa din första Notification Hub.

   Du kan nu skapa ett eller flera Notification Hub i det nya namn området. Kör kommandot [AZ Notification-Hub Create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) för att skapa en Notification Hub.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Skapa en andra Notification Hub.

   Flera Notification Hub kan skapas i ett enda namn område. Om du vill skapa en andra Notification Hub i samma namnrymd kör du `az notification-hub create` kommandot igen med ett annat nav namn.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Hämta en lista över Notification Hub.

   Azure CLI returnerar antingen ett lyckat eller fel meddelande med varje exekverat kommando. men det går att fråga efter en lista över Notification Hub. Kommandot [AZ Notification – Hub List](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) har utformats för detta ändamål.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Arbeta med åtkomst principer

1. Azure Notification Hubs använder [signaturen för delad åtkomst](./notification-hubs-push-notification-security.md) med hjälp av åtkomst principer. Två principer skapas automatiskt när du skapar en Notification Hub. Anslutnings strängarna från dessa principer krävs för att konfigurera push-meddelanden. Kommandot [AZ Notification – Hub Authorization-Rule List](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) innehåller en lista över princip namn och deras respektive resurs grupper.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Använd inte _DefaultFullSharedAccessSignature_ -principen i ditt program. Den här principen är endast avsedd att användas i Server delen. Använd endast `Listen` åtkomst principer i klient programmet.

2. Om du vill skapa ytterligare auktoriseringsregler med meningsfulla namn kan du skapa och anpassa din egen åtkomst princip genom att använda kommandot [AZ Notification-Hub Authorization-Rule Create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) . `--rights`Parametern är en blankstegsavgränsad lista över de behörigheter som du vill tilldela.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Det finns två uppsättningar nycklar och anslutnings strängar för varje åtkomst princip. Du behöver dem senare för att [Konfigurera en Notification Hub](./configure-notification-hub-portal-pns-settings.md). Om du vill visa nycklar och anslutnings strängar för en Notification Hubs åtkomst princip använder du kommandot [AZ Notification-Hub Authorization-Rule List-Keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) .

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Ett [Notification Hubs-namnområde](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) och en [Notification Hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) har separata åtkomst principer. Kontrol lera att du använder rätt Azure CLI-referens när du frågar efter nycklar och anslutnings strängar.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du kommandot [AZ Group Delete](/cli/azure/group) för att ta bort resurs gruppen och alla relaterade resurser:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Nästa steg

* I den här snabbstarten har du skapat en meddelandehubb. Information om hur du konfigurerar hubben med PNS-inställningar (Platform notification system) finns i [Konfigurera push-meddelanden i en Notification Hub](configure-notification-hub-portal-pns-settings.md)

* Upptäck de omfattande funktionerna för att hantera Notification Hub med Azure CLI:

  [Notification Hubs fullständig referens lista](/cli/azure/ext/notification-hub/notification-hub)

  [Lista över Notification Hubs namn områdes referens](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Referens lista för Notification Hubs-auktoriseringsregel](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Referens lista för Notification Hubs referens](/cli/azure/ext/notification-hub/notification-hub/credential)