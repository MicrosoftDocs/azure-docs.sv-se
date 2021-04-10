---
title: Konfigurera inställningar för nätverksroutning
titleSuffix: Azure Storage
description: Konfigurera inställningar för nätverks routning för ditt Azure Storage-konto för att ange hur nätverks trafik dirigeras till ditt konto från klienter via Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589862"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurera inställningar för nätverks routning för Azure Storage

Den här artikeln beskriver hur du kan konfigurera inställningar för nätverks Routning och dirigerade slut punkter för ditt lagrings konto. 

Inställningen nätverks routning anger hur nätverks trafik dirigeras till ditt konto från klienter via Internet. Route-/regionsspecifika slut punkter är nya slut punkter som Azure Storage skapar för ditt lagrings konto. De här slut punkterna dirigerar trafik över en önskad sökväg utan att ändra standard inställningarna för routning. Mer information finns i [Inställningar för nätverks routning för Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurera Dirigerings inställningarna för den offentliga standard slut punkten

Som standard är cirkulations inställningen för lagrings kontots offentliga slut punkt inställd på Microsoft globalt nätverk. Du kan välja mellan Microsofts globala nätverk och Internet routning som standardinställning för routning för den offentliga slut punkten för ditt lagrings konto. Mer information om skillnaden mellan dessa två typer av routning finns i [Inställningar för nätverks routning för Azure Storage](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Så här ändrar du inställningarna för routning till Internet Routning:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Navigera till ditt lagrings konto i portalen.

3. Välj **nätverk** under **Inställningar**.

    > [!div class="mx-imgBorder"]
    > ![Meny alternativ för nätverk](./media/configure-network-routing-preference/networking-option.png)

4.  I fliken **brand väggar och virtuella nätverk** , under **nätverks routning**, ändrar du inställningarna för **routning** till **Internet routning**.

5.  Klicka på **Spara**.

    > [!div class="mx-imgBorder"]
    > ![alternativ för Internet routning](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Logga in på din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

3. Om du vill ändra inställningarna för routning till Internet routning använder du kommandot [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger `--routing-choice` parametern till `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Ersätt `<resource-group-name>` placeholder-värdet med namnet på den resurs grupp som innehåller lagrings kontot.

   Ersätt `<storage-account-name>` placeholder-värdet med namnet på lagrings kontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Logga in på din Azure-prenumeration.

   - Logga in på [Azure Portal](https://portal.azure.com)för att starta Azure Cloud Shell.

   - Logga in på den lokala installationen av CLI genom att köra kommandot [AZ login](/cli/azure/reference-index#az-login) :

     ```azurecli
     az login
     ```
2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` placeholder-värdet med ID: t för din prenumeration.

3. Om du vill ändra inställningarna för routning till Internet routning använder du kommandot [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) och anger `--routing-choice` parametern till `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurera en cirkulations bara slut punkt

Du kan också konfigurera en cirkulations bara slut punkt. Du kan till exempel ange cirkulations inställningar för standard slut punkten till *Internet routning* och sedan publicera en molnbaserad slut punkt som aktiverar trafik mellan klienter på Internet och ditt lagrings konto som ska dirigeras via Microsofts globala nätverk.

Den här inställningen påverkar bara den platsspecifika slut punkten. Inställningen påverkar inte standardvärdet för routning.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Navigera till ditt lagrings konto i portalen.

2.  Välj **nätverk** under **Inställningar**.

3.  I fliken **brand väggar och virtuella nätverk** under **publicera dirigerade slut punkter**, väljer du cirkulations inställningen för den dirigerade slut punkten och klickar sedan på **Spara**.

    Följande bild visar alternativet **Microsoft Network routing** .

    > [!div class="mx-imgBorder"]
    > ![Alternativ för Microsoft-nätverks routning](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Använd kommandot [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) för att konfigurera en vägkälla slut punkt. 

   - Om du vill skapa en molnbaserad slut punkt som använder inställningen Microsoft Network routing anger du `-PublishMicrosoftEndpoint` parametern till `true` . 

   - Om du vill skapa en vägkälla som använder inställningen Internet routning anger du `-PublishInternetEndpointTo` parametern till `true` .  

   I följande exempel skapas en dirigerad slut punkt som använder Microsoft Network routing-inställningen.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Ersätt `<resource-group-name>` placeholder-värdet med namnet på den resurs grupp som innehåller lagrings kontot.

   Ersätt `<storage-account-name>` placeholder-värdet med namnet på lagrings kontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du vill konfigurera en molnbaserad slut punkt använder du kommandot [AZ Storage Account Update](/azure/storage/account#az-storage-account-update) . 

   - Om du vill skapa en molnbaserad slut punkt som använder inställningen Microsoft Network routing anger du `--publish-microsoft-endpoints` parametern till `true` . 

   - Om du vill skapa en vägkälla som använder inställningen Internet routning anger du `--publish-internet-endpoints` parametern till `true` .  

   I följande exempel skapas en dirigerad slut punkt som använder Microsoft Network routing-inställningen.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Ersätt `<storage-account-name>` placeholder-värdet med namnet på lagrings kontot.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Hitta slut punkts namnet för en cirkulations bara slut punkt

Om du har konfigurerat en molnbaserad slut punkt kan du hitta slut punkten i egenskaperna för ditt lagrings konto.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Under **Inställningar** väljer du **Egenskaper**.

    > [!div class="mx-imgBorder"]
    > ![meny alternativet egenskaper](./media/configure-network-routing-preference/properties.png)

2.  Slut punkten för **Microsoft Network routing** visas för varje tjänst som stöder inställningar för routning. Den här bilden visar slut punkten för blob-och fil tjänsterna.

    > [!div class="mx-imgBorder"]
    > ![Alternativ för Microsoft-nätverks dirigering för dirigerade slut punkter](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Om du vill skriva ut slut punkterna till-konsolen använder du `PrimaryEndpoints` egenskapen för objektet lagrings konto.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Ersätt `<resource-group-name>` placeholder-värdet med namnet på den resurs grupp som innehåller lagrings kontot.

   Ersätt `<storage-account-name>` placeholder-värdet med namnet på lagrings kontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du vill skriva ut slut punkterna till-konsolen använder du egenskapen [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) för objektet lagrings konto.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Ersätt `<resource-group-name>` placeholder-värdet med namnet på den resurs grupp som innehåller lagrings kontot.

   Ersätt `<storage-account-name>` placeholder-värdet med namnet på lagrings kontot.

---

## <a name="see-also"></a>Se även

- [Inställningar för nätverks routning](network-routing-preference.md)
- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md)
- [Säkerhets rekommendationer för Blob Storage](../blobs/security-recommendations.md)
