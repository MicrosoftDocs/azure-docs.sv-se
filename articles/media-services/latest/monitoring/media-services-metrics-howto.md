---
title: Visa mått med Azure Monitor
description: Den här artikeln visar hur du övervakar mått med Azure Portal-diagram och Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 306381465919d3fde7135315b69d450a496c2898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104610068"
---
# <a name="monitor-media-services-metrics"></a>Övervaka Media Services-mått

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Med [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program presterar. En detaljerad beskrivning av den här funktionen och för att förstå varför du bör använda Azure Media Services mått och diagnostikloggar finns i [övervaka Media Services mått och diagnostikloggar](monitor-media-services-data-reference.md).

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av Azure CLI. Den här artikeln visar hur du övervakar mått med Azure Portal-diagram och Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](../create-account-howto.md)
- Granska  [övervaknings Media Services mått och diagnostikloggar](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Visa mått i Azure Portal

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till ditt Azure Media Services konto och välj **mått**.
1. Klicka i rutan **omfattning** och välj den resurs som du vill övervaka.

    Fönstret **Välj ett omfång** visas till höger med listan över tillgängliga resurser. I det här fallet visas:

    * &lt;Media Services konto namn&gt;
    * &lt;Media Services konto namn &gt; / &lt; strömmande slut punkts namn&gt;
    * &lt;lagrings konto namn&gt;

    Filtrera sedan och Välj resursen och tryck på **Använd**. Mer information om resurser och mått som stöds finns i [övervaka Media Services mått](monitor-media-services-data-reference.md).

    > [!NOTE]
    > Om du vill växla mellan resurser som du vill övervaka klickar du på rutan **källa** igen och upprepar det här steget.

1. Valfritt: ge ditt diagram ett namn (redigera namnet genom att trycka på penna överst).
1. Lägg till de mått som du vill visa.
1. Du kan fästa diagrammet på instrument panelen.

## <a name="view-metrics-with-azure-cli"></a>Visa mått med Azure CLI

Om du vill hämta mått för "utgående" med Azure CLI kör du följande `az monitor metrics` kommando:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Om du vill ha andra mått ersätter du "utgående" för det mått namn som du är intresse rad av.

## <a name="see-also"></a>Se även

- [Azure Monitor-statistik](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
- [Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Nästa steg

[Diagnostikloggar](../media-services-diagnostic-logs-howto.md)
