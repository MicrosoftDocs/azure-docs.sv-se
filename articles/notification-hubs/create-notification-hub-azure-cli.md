---
title: Snabbstart – Skapa en Azure-meddelandehubb med hjälp av Azure CLI-| Microsoft Docs
description: I den här självstudien får du lära dig hur du skapar en Azure-meddelandehubb med hjälp av Azure CLI.
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
ms.openlocfilehash: d8400eb051c09fac4cb88863ad2fac12d2ca0a1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789894"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Snabbstart: Skapa en Azure-meddelandehubb med hjälp av Azure CLI

Azure Notification Hubs innehåller en lättanvänd och uppskalad push-motor som gör det möjligt för dig att skicka meddelanden till valfri plattform (iOS, Android, Windows, Kindle, Baidu osv) från valfri serverdel (molnet eller lokalt). Mer information om tjänsten finns i [Vad är Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

I den här snabbstarten skapar du en meddelandehubb med hjälp av Azure CLI. Det första avsnittet innehåller steg för att skapa ett Notification Hubs namnområde. Det andra avsnittet innehåller steg för att skapa en meddelandehubb i ett befintligt namnområde. Du får också lära dig hur du skapar en anpassad åtkomstprincip.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Notification Hubs kräver version 2.0.67 eller senare av Azure CLI. Kör [az version](/cli/azure/reference-index#az_version) om du vill hitta versionen och de beroende bibliotek som är installerade. Om du vill uppgradera till den senaste versionen kör du [az upgrade](/cli/azure/reference-index#az_upgrade).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Notification Hubs måste precis som alla Azure-resurser distribueras till en resursgrupp.  Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.  Mer [information om Azure Resource Manager](../azure-resource-manager/management/overview.md) finns i Vad finns det?

I den här snabbstarten skapar du en resursgrupp med namnet **spnhubrg** på **platsen eastus** med följande [az group create-kommando.](/cli/azure/group#az_group_create)

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Skapa ett Notification Hubs namnområde

1. Skapa ett namnområde för dina meddelandehubbbar.

   Ett namnområde innehåller en eller flera hubbar och namnet måste vara unikt för alla Azure-prenumerationer och innehålla minst sex tecken. Om du vill kontrollera tillgängligheten för ett namn använder du [kommandot az notification-hub namespace check-availability.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability)

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI svarar på din begäran om tillgänglighet genom att visa följande konsolutdata:

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

   Lägg märke till den andra raden i Azure CLI-svaret, `"isAvailable": true` . Den här raden läser `false` om det önskade namn som du angav för namnområdet inte är tillgängligt. När du har bekräftat tillgängligheten för namnet kör du kommandot [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) för att skapa namnområdet.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Om kommandot inte är tillgängligt eller inte uppfyller namngivningsreglerna och begränsningarna för Azure-resurser svarar Azure CLI med följande `--name` `az notification-hub namespace create` konsolutdata: [](../azure-resource-manager/management/resource-name-rules.md)

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Om det förnamn som du provade inte lyckas väljer du ett annat namn för det nya namnområdet och kör `az notification-hub namespace create` kommandot igen.

   > [!NOTE]
   > Från och med det här steget måste du ersätta värdet för `--namespace` parametern i varje Azure CLI-kommando som du kopierar från den här snabbstarten.

2. Hämta en lista över namnområden.

   Om du vill se information om det nya namnområdet använder du [kommandot az notification-hub namespace list.](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) Parametern `--resource-group` är valfri om du vill se alla namnområden för en prenumeration.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Skapa meddelandehubar

1. Skapa din första meddelandehubb.

   En eller flera meddelandehubar kan nu skapas i ditt nya namnområde. Kör kommandot [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) för att skapa en meddelandehubb.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Skapa en andra meddelandehubb.

   Flera meddelandehubar kan skapas i ett enda namnområde. Om du vill skapa en andra meddelandehubb i samma namnområde kör du `az notification-hub create` kommandot igen med ett annat hubbnamn.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Hämta en lista över meddelandehubbbar.

   Azure CLI returnerar antingen ett lyckat eller felmeddelande med varje kört kommando. Att kunna fråga efter en lista över meddelandehubbbar kan dock vara bra. Kommandot [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) har utformats för detta ändamål.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Arbeta med åtkomstprinciper

1. Azure Notification Hubs använder [säkerhet för signaturer för delad](./notification-hubs-push-notification-security.md) åtkomst med hjälp av åtkomstprinciper. Två principer skapas automatiskt när du skapar en meddelandehubb. Anslutningssträngarna från dessa principer behövs för att konfigurera push-meddelanden. Kommandot [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) innehåller en lista över principnamn och deras respektive resursgrupper.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Använd inte _principen DefaultFullSharedAccessSignature_ i ditt program. Den här principen är endast avsedd att användas i din backend-dator. Använd endast `Listen` åtkomstprinciper i klientprogrammet.

2. Om du vill skapa ytterligare auktoriseringsregler med beskrivande namn kan du skapa och anpassa din egen åtkomstprincip med hjälp av kommandot [az notification-hub authorization-rule create.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) Parametern `--rights` är en blankstegsavgränsad lista över de behörigheter som du vill tilldela.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Det finns två uppsättningar nycklar och anslutningssträngar för varje åtkomstprincip. Du behöver dem senare för att konfigurera [en meddelandehubb.](./configure-notification-hub-portal-pns-settings.md) Om du vill visa nycklar och anslutningssträngar för Notification Hubs åtkomstprincip använder du kommandot [az notification-hub authorization-rule list-keys.](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > En [Notification Hubs och en](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) [meddelandehubb har](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) separata åtkomstprinciper. Kontrollera att du använder rätt Azure CLI-referens när du frågar efter nycklar och anslutningssträngar.

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs använder du [kommandot az group delete](/cli/azure/group) för att ta bort resursgruppen och alla relaterade resurser:

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Nästa steg

* I den här snabbstarten har du skapat en meddelandehubb. Information om hur du konfigurerar hubben med inställningar för plattformsmeddelandesystem (PNS) finns i [Konfigurera push-meddelanden i en meddelandehubb](configure-notification-hub-portal-pns-settings.md)

* Upptäck de omfattande funktionerna för att hantera meddelandehubbbar med Azure CLI:

  [Notification Hubs fullständig referenslista](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs namnområdesreferenslista](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs lista över auktoriseringsregel](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs referenslista för autentiseringsuppgifter](/cli/azure/ext/notification-hub/notification-hub/credential)