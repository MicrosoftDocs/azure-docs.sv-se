---
title: Konfigurera inställningar för nätverksroutning
titleSuffix: Azure Storage
description: Konfigurera inställningar för nätverksroutning för ditt Azure Storage-konto för att ange hur nätverkstrafiken dirigeras till ditt konto från klienter via Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790463"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurera inställningar för nätverksroutning för Azure Storage

I den här artikeln beskrivs hur du kan konfigurera inställningar för nätverksroutning och routningsspecifika slutpunkter för ditt lagringskonto. 

Inställningen för nätverksroutning anger hur nätverkstrafik dirigeras till ditt konto från klienter via Internet. Vägspecifika slutpunkter är nya slutpunkter som Azure Storage skapar för ditt lagringskonto. Dessa slutpunkter dirigerar trafik via en önskad sökväg utan att ändra standardinställningen för routning. Mer information finns i Inställningar [för nätverksroutning för Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Konfigurera routningsinställningen för den offentliga standardslutpunkten

Som standard är routningsinställningen för den offentliga slutpunkten för lagringskontot inställd på Microsofts globala nätverk. Du kan välja mellan Microsofts globala nätverk och Internetroutning som standardinställning för routning för den offentliga slutpunkten för ditt lagringskonto. Mer information om skillnaden mellan dessa två typer av routning finns i Inställningar [för nätverksroutning för Azure Storage](network-routing-preference.md). 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Så här ändrar du routningsinställningarna till Internetroutning:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Gå till ditt lagringskonto i portalen.

3. Under **Inställningar** väljer du **Nätverk.**

    > [!div class="mx-imgBorder"]
    > ![Menyalternativet Nätverk](./media/configure-network-routing-preference/networking-option.png)

4.  På fliken **Brandväggar och virtuella nätverk,** under Nätverksroutning, ändrar du inställningen **Routningsinställning** till **Internetroutning**. 

5.  Klicka på **Spara**.

    > [!div class="mx-imgBorder"]
    > ![Internetroutningsalternativ](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Logga in på Din Azure-prenumeration med `Connect-AzAccount` kommandot och följ anvisningarna på skärmen för att autentisera.

   ```powershell
   Connect-AzAccount
   ```

2. Om din identitet är associerad med mer än en prenumeration anger du den aktiva prenumerationen till prenumerationen på det lagringskonto som ska vara värd för din statiska webbplats.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

3. Om du vill ändra routningsinställningarna till Internetroutning använder du [kommandot Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger `--routing-choice` parametern till `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen som innehåller lagringskontot.

   Ersätt `<storage-account-name>` platshållarvärdet med namnet på lagringskontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Logga in på din Azure-prenumeration.

   - Starta Azure Cloud Shell genom att logga in på [Azure Portal](https://portal.azure.com).

   - Logga in på din lokala installation av CLI genom att köra [kommandot az login:](/cli/azure/reference-index#az_login)

     ```azurecli
     az login
     ```
2. Om din identitet är associerad med mer än en prenumeration anger du din aktiva prenumeration till prenumerationen på det lagringskonto som ska vara värd för din statiska webbplats.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

3. Om du vill ändra routningsinställningarna till Internetroutning använder du [kommandot az storage account update](/cli/azure/storage/account#az_storage_account_update) och anger `--routing-choice` parametern till `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Ersätt platshållarvärdet `<storage-account-name>` med namnet på ditt lagringskonto.

---

## <a name="configure-a-route-specific-endpoint"></a>Konfigurera en vägspecifik slutpunkt

Du kan också konfigurera en vägspecifik slutpunkt. Du kan till exempel ange routningsinställningen för standardslutpunkten till *Internetroutning* och sedan publicera en vägspecifik slutpunkt som gör att trafik mellan klienter på Internet och ditt lagringskonto kan dirigeras via Microsofts globala nätverk.

Den här inställningen påverkar endast den vägspecifika slutpunkten. Den här inställningen påverkar inte standardinställningen för routning.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Gå till ditt lagringskonto i portalen.

2.  Under **Inställningar** väljer du **Nätverk.**

3.  På fliken Brandväggar och virtuella nätverk, under Publicera **routningsspecifika slutpunkter,** väljer du routningsinställningarna för din **routningsspecifika** slutpunkt och klickar sedan på **Spara.**

    Följande bild visar det valda **microsoft-alternativet för** nätverksroutning.

    > [!div class="mx-imgBorder"]
    > ![Microsofts alternativ för nätverksroutning](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Om du vill konfigurera en vägspecifik slutpunkt använder du kommandot [Set-AzStorageAccount.](/powershell/module/az.storage/set-azstorageaccount) 

   - Om du vill skapa en routningsspecifik slutpunkt som använder Microsofts inställningar för nätverksroutning anger du `-PublishMicrosoftEndpoint` parametern till `true` . 

   - Om du vill skapa en vägspecifik slutpunkt som använder Internetroutningsinställningarna anger du `-PublishInternetEndpointTo` parametern till `true` .  

   I följande exempel skapas en routningsspecifik slutpunkt som använder Microsofts inställningar för nätverksroutning.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen som innehåller lagringskontot.

   Ersätt `<storage-account-name>` platshållarvärdet med namnet på lagringskontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du vill konfigurera en vägspecifik slutpunkt använder du [kommandot az storage account update.](/azure/storage/account#az_storage_account_update) 

   - Om du vill skapa en routningsspecifik slutpunkt som använder Microsofts inställningar för nätverksroutning anger du `--publish-microsoft-endpoints` parametern till `true` . 

   - Om du vill skapa en vägspecifik slutpunkt som använder Internetroutningsinställningarna anger du `--publish-internet-endpoints` parametern till `true` .  

   I följande exempel skapas en routningsspecifik slutpunkt som använder Microsofts inställningar för nätverksroutning.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Ersätt `<storage-account-name>` platshållarvärdet med namnet på lagringskontot.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Hitta slutpunktsnamnet för en vägspecifik slutpunkt

Om du har konfigurerat en vägspecifik slutpunkt hittar du slutpunkten i egenskaperna för ditt lagringskonto.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Under **Inställningar** väljer du **Egenskaper.**

    > [!div class="mx-imgBorder"]
    > ![menyalternativet egenskaper](./media/configure-network-routing-preference/properties.png)

2.  Microsofts **slutpunkt för nätverksroutning** visas för varje tjänst som stöder routningsinställningar. Den här bilden visar slutpunkten för blob- och filtjänsterna.

    > [!div class="mx-imgBorder"]
    > ![Microsofts alternativ för nätverksroutning för routningsspecifika slutpunkter](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Om du vill skriva ut slutpunkterna till konsolen använder du `PrimaryEndpoints` egenskapen för lagringskontoobjektet.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen som innehåller lagringskontot.

   Ersätt `<storage-account-name>` platshållarvärdet med namnet på lagringskontot.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Om du vill skriva ut slutpunkterna till konsolen använder du [egenskapen az storage account show](/cli/azure/storage/account#az_storage_account_show) för lagringskontoobjektet.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Ersätt `<resource-group-name>` platshållarvärdet med namnet på resursgruppen som innehåller lagringskontot.

   Ersätt `<storage-account-name>` platshållarvärdet med namnet på lagringskontot.

---

## <a name="see-also"></a>Se även

- [Inställningar för nätverksroutning](network-routing-preference.md)
- [Konfigurera brandväggar och virtuella nätverk i Azure Storage](storage-network-security.md)
- [Säkerhetsrekommendationer för Blob Storage](../blobs/security-recommendations.md)
