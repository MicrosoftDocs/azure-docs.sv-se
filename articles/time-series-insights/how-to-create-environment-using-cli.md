---
title: Skapa en Azure Time Series Insights Gen2-miljö med Azure CLI-Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig hur du konfigurerar en miljö i Azure Time Series Insights Gen2 med hjälp av Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 150bf38ebb248f15bf1ed82186c16b6b3f7ac40b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728750"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Skapa en Azure Time Series Insights Gen2-miljö med Azure CLI

I det här dokumentet får du hjälp med att skapa en ny Time Series Insights Gen2-miljö.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

* Skapa ett Azure Storage-konto för din miljös [kall Arkiv](./concepts-storage.md#cold-store). Det här kontot är utformat för långsiktig kvarhållning och analys för historiska data.

> [!NOTE]
> Ersätt `mytsicoldstore` med ett unikt namn för ditt kalla lagrings konto i din kod.

Skapa först lagrings kontot:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Skapa miljön

Nu när lagrings kontot har skapats och dess namn och hanterings nyckel har tilldelats variablerna, kör du kommandot nedan för att skapa Azure Time Series Insightss miljön:

> [!NOTE]
> I din kod ersätter du följande med unika namn för ditt scenario:
>
> * `my-tsi-env` med ditt miljö namn.
> * `my-ts-id-prop` med namnet på din Time Series ID-egenskap.

> [!IMPORTANT]
> Din miljös Time Series-ID är som en databas partitionsnyckel. Time Series-ID fungerar också som primär nyckel för din tids serie modell.
>
> Mer information finns i [metod tips för att välja ett Time Series-ID.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Ta bort en Azure Time Series Insightss miljö

Du kan använda Azure CLI för att ta bort en enskild resurs, till exempel en Time Series Insights-miljö eller ta bort en resurs grupp och alla dess resurser, inklusive Time Series Insights miljöer.

Om du vill [ta bort en Time Series Insights miljöer](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete)kör du följande kommando:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Kör följande kommando för att [ta bort lagrings kontot](/cli/azure/storage/account#az_storage_account_delete):

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Om du vill [ta bort en resurs grupp](/cli/azure/group#az-group-delete) och alla dess resurser kör du följande kommando:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att strömma inmatnings [händelse källor](./concepts-streaming-ingestion-event-sources.md) för din Azure Time Series Insights Gen2-miljö.
* Lär dig hur du ansluter till en [IoT Hub](./how-to-ingest-data-iot-hub.md)
