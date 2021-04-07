---
title: Hantera en gen 2-miljö – Azure Time Series | Microsoft Docs
description: Lär dig hur du hanterar en Azure Time Series Insights gen 2-miljö.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: cbd28bdf5318bdaf932447f5d1f936d2c614a7f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461903"
---
# <a name="manage-azure-time-series-insights-gen2"></a>Hantera Azure Time Series Insights Gen2

När du har skapat din Azure Time Series Insights Gen2-miljö med hjälp av [Azure CLI](./how-to-create-environment-using-cli.md) eller [Azure Portal](./how-to-create-environment-using-portal.md)kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

## <a name="manage-the-environment"></a>Hantera miljön

Du kan hantera din Azure Time Series Insights Gen2-miljö med hjälp av [Azure Portal](https://portal.azure.com/). Det finns några viktiga skillnader mellan en Gen2 miljö och gen1 S1-eller gen1 S2-miljöer som du bör tänka på när du hanterar din miljö via Azure Portal:

* **Översikts** fönstret för Azure Portal Gen2 har följande ändringar:

  * Kapaciteten tas bort eftersom den inte gäller för Gen2-miljöer.
  * Egenskapen **Time Series ID** har lagts till. Den avgör hur dina data partitioneras.
  * Referens data uppsättningar tas bort.
  * Den URL som visas leder till [Azure Time Series Insights Explorer](./concepts-ux-panels.md).
  * Namnet på Azure Storages kontot visas.

* Den Azure Portal rutan **Konfigurera** är borttagen eftersom skalnings enheter inte gäller för Azure Time Series Insights Gen2-miljöer. Du kan dock använda **Storage-konfiguration** för att konfigurera den nyligen lanserade butiken.

* Azure Portalens **referens data** ruta tas bort i Azure Time Series Insights Gen2 eftersom referens data koncept har ersatts av [Time Series-modellen (TSM)](./concepts-model-overview.md).

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure Time Series Insights Gen2-miljö i Azure Portal":::

## <a name="next-steps"></a>Nästa steg

* Granska listan över [metod tips för strömnings](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) inmatning
* Förstå hur du [diagnostiserar och felsöker](./how-to-diagnose-troubleshoot.md)
