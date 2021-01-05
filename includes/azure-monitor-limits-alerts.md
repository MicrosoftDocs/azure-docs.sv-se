---
title: ta med fil
description: ta med fil
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0c3cdc21425e0634a725000efb27b3cde0ccd718
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97844759"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Mått varningar (klassisk) |100 aktiva varnings regler per prenumeration. | Ring support |
| Måttaviseringar |5 000 aktiva varnings regler per prenumeration i Azure offentlig, Azure Kina 21Vianet och Azure Government moln. Om du når den här gränsen kan du utforska om du kan använda [samma typ av aviseringar med flera resurser](../articles/azure-monitor/platform/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 Metric Time-Series per varnings regel. | Kontakta supporten. |
| Aktivitetsloggaviseringar | 100 aktiva varnings regler per prenumeration (kan inte ökas). | Samma som standard |
| Loggaviseringar | 512 aktiva varnings regler per prenumeration. 200 aktiva varnings regler per resurs. | Ring support |
| Beskrivnings längd för varnings regler och åtgärds regler| Loggs ökning varningar 4096 tecken<br/>Alla andra 2048-tecken | Samma som standard |
