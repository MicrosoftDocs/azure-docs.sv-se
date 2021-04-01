---
title: Ansluta Forcepoint-produkter till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Forcepoint-produkter till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77588237"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Anslut dina Forcepoint-produkter till Azure Sentinel

> [!IMPORTANT]
> Forcepoint Products data Connector i Azure Sentinel är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Den här artikeln förklarar hur du ansluter dina Forcepoint-produkter till Azure Sentinel. 

Med Forcepoint data Connectors kan du ansluta Forcepoint Cloud Access Security Broker och Forcepoint nästa generations brand Väggs loggar med Azure Sentinel. På så sätt kan du automatiskt exportera användardefinierade loggar till Azure Sentinel i real tid. Anslutningen ger dig bättre insyn i användar aktiviteter som registrerats av Forcepoint-produkter. Det möjliggör också ytterligare korrelation med data från Azure-arbetsbelastningar och andra feeds och förbättrar övervaknings kapaciteten med arbets böcker i Azure Sentinel.

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Vidarebefordra Forcepoint produkt loggar till syslog-agenten 

Konfigurera Forcepoint-produkten för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten.

1. Konfigurera Forcepoint-produkten till Azure Sentinel-integrering enligt beskrivningen i följande installations guider:
 - [Forcepoint CASB-integrerings guide](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW-integrerings guide](https://frcpnt.com/ngfw-sentinel)

2. Sök efter CommonSecurityLog för att använda det relevanta schemat i Log Analytics med DeviceVendor namn innehåller ' Forcepoint '. 

3. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).



## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Forcepoint-produkter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).

- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.