---
title: Telemetri för Azure peering service-anslutning
description: Läs mer om hur du Microsoft Azure telemetri för peering service-anslutning
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84872010"
---
# <a name="peering-service-connection-telemetry"></a>Telemetri för peering service-anslutning

Telemetri för anslutning ger insikter som samlats in för anslutningen mellan kundens plats och Microsoft-nätverket. Kunder kan få telemetri för Azure peering service-anslutningen genom att registrera anslutningen i Azure Portal. Den här funktionen ger prefix säkerhet och insikter om nätverks prestanda.


Telemetri för anslutning består av följande omfattningar:

### <a name="latency-measurement"></a>Svars tids mätning

 Svars tiden mäts från klienten till Microsoft Edge PoP för de registrerade prefixen i peering-tjänsten.

### <a name="route-prefix-monitoring-and-protection"></a>Dirigera prefix övervakning och skydd

Vägar för routning övervakas för misstänkt aktivitet som sedan fångas in i händelse loggar. Till exempel skapas händelse loggar för några av dessa faktorer:

- Kapning av prefix
- Återkalla prefix
- Dirigera läckor

## <a name="next-steps"></a>Nästa steg

- Mer information om peering service-anslutning finns i [peering service-anslutning](connection.md).
- Information om hur du publicerar en peering service-anslutning finns i [onboarding a peering service Model](onboarding-model.md).
- För att mäta telemetri, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
