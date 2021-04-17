---
title: Skapa en Azure Time Series Insights Gen2-miljö med hjälp av Azure CLI – Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig att konfigurera en miljö i Azure Time Series Insights Gen2 med hjälp av Azure CLI.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 17c1ea19f3879f5490922ab4b54f21773191eebd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484241"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-cli"></a>Skapa en Azure Time Series Insights Gen2-miljö med Hjälp av Azure CLI

Det här dokumentet vägleder dig genom skapandet av en ny Time Series Insights Gen2-miljö.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar

* Skapa ett Azure-lagringskonto för din miljös [kalllager](./concepts-storage.md#cold-store). Det här kontot är utformat för långsiktig kvarhållning och analys av historiska data.

> [!NOTE]
> Ersätt med ett unikt `mytsicoldstore` namn för ditt kalllagringskonto i koden.

Skapa först lagringskontot:

```azurecli-interactive
storage=mytsicoldstore
rg=-my-resource-group-name
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv
```

## <a name="creating-the-environment"></a>Skapa miljön

Nu när lagringskontot har skapats och dess namn och hanteringsnyckel har tilldelats till variablerna kör du kommandot nedan för att skapa Azure Time Series Insights Miljö:

> [!NOTE]
> Ersätt följande med unika namn för ditt scenario i koden:
>
> * `my-tsi-env` med namnet på din miljö.
> * `my-ts-id-prop` med namnet på din egenskap för tidsserie-ID.

> [!IMPORTANT]
> Tidsserie-ID:t för din miljö är som en databaspartitionsnyckel. Tidsserie-ID:t fungerar också som primärnyckel för din tidsseriemodell.
>
> Mer information finns i [Metodtips för att välja ett tidsserie-ID.](./how-to-select-tsid.md)

```azurecli-interactive
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=my-ts-id-prop type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

## <a name="remove-an-azure-time-series-insights-environment"></a>Ta bort en Azure Time Series Insights miljö

Du kan använda Azure CLI för att ta bort en enskild resurs, till exempel en Time Series Insights-miljö, eller ta bort en resursgrupp och alla dess resurser, inklusive Time Series Insights miljöer.

Om [du vill Time Series Insights en miljö](/cli/azure/ext/timeseriesinsights/tsi/environment#ext_timeseriesinsights_az_tsi_environment_delete)Time Series Insights kör du följande kommando:

```azurecli-interactive
az tsi environment delete --name "my-tsi-env" --resource-group $rg
```

Kör [följande kommando för att](/cli/azure/storage/account#az_storage_account_delete)ta bort lagringskontot:

```azurecli-interactive
az storage account delete --name $storage --resource-group $rg
```

Om [du vill ta bort en](/cli/azure/group#az-group-delete) resursgrupp och alla dess resurser kör du följande kommando:

```azurecli-interactive
az group delete --name $rg
```

## <a name="next-steps"></a>Nästa steg

* Lär dig [mer om strömningsinmatningshändelsekällor](./concepts-streaming-ingestion-event-sources.md) för din Azure Time Series Insights Gen2-miljö.
* Lär dig hur du ansluter till en [IoT Hub](./how-to-ingest-data-iot-hub.md)
