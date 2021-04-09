---
title: Anslut F5 BIG-IP-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter F5 BIG-IP-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092799"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Anslut din F5 BIG-IP-apparat 

Med F5 BIG-IP-anslutningen kan du enkelt ansluta alla dina F5 stora IP-loggar med Azure Sentinel, för att Visa arbets böcker, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig bättre insikt i din organisations nätverk och förbättrar dina säkerhets åtgärder. Integreringen mellan F5 BIG-IP och Azure Sentinel använder REST API.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="configure-and-connect-f5-big-ip"></a>Konfigurera och ansluta F5 BIG-IP 

F5 BIG-IP kan integrera och exportera loggar direkt till Azure Sentinel.

1. I Azure Sentinel-portalen klickar du på **data kopplingar** och väljer **F5 Big-IP** och **öppnar sedan kopplings sidan**. 
1. Om du vill ansluta din F5 BIG-IP måste du skicka en JSON-deklaration till systemets API-slutpunkt. Instruktioner för hur du gör detta finns i [integrera F5 Big-IP med Azure Sentinel](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel).
8. På sidan F5 BIG-IP-anslutning kopierar du arbetsyte-ID och primär nyckel och klistrar in dem enligt anvisningarna under [strömmande data till Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. När du har slutfört de stora IP-instruktionerna på sidan för Azure Sentinel Connector ser du de anslutna data typerna.
1. Om du vill använda det relevanta schemat i Log Analytics för de stora IP-händelserna i F5 söker du efter **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** och **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter F5 BIG-IP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


