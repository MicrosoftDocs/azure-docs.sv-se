---
title: ta med fil
description: ta med fil
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504721"
---
När du skapar en virtuell nätverksgateway måste du ange vilken gateway-SKU som du vill använda. När du väljer en högre gateway-SKU allokeras fler processorer och mer nätverksbandbredd till gatewayen så att gatewayen kan hantera ett större nätverksgenomflöde till det virtuella nätverket. 

ExpressRoute virtuella nätverksgateway kan använda följande SKU: er:

|     | VPN Gateway och ExpressRoute samexisterar | FastPath | Högsta antal krets anslutningar |
| --- | --- | --- | --- |
| **Standard** | Ja | Inga | 4 |
| **HighPerformance** | Ja | Inga | 4 |
| **UltraPerformance** | Ja | Ja | 16 |