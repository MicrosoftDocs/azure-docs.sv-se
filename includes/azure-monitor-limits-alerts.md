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
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734072"
---
| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| Mått varningar (klassisk) |100 aktiva varnings regler per prenumeration. | Ring support |
| Måttaviseringar |5 000 aktiva varnings regler per prenumeration i Azure offentlig, Azure Kina 21Vianet och Azure Government moln. Om du når den här gränsen kan du utforska om du kan använda [samma typ av aviseringar med flera resurser](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).<br/>5 000 Metric Time-Series per varnings regel. | Kontakta supporten. |
| Aktivitetsloggaviseringar | 100 aktiva varnings regler per prenumeration (kan inte ökas). | Samma som standard |
| Loggaviseringar | 512 aktiva varnings regler per prenumeration. 200 aktiva varnings regler per resurs. | Ring support |
| Beskrivnings längd för varnings regler och åtgärds regler| Loggs ökning varningar 4096 tecken<br/>Alla andra 2048-tecken | Samma som standard |