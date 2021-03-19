---
title: Anslut Illusive attack Management Systems-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Illusive angrepps hanterings system data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038125"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Anslut ditt Illusive-angrepps hanterings system till Azure Sentinel

> [!IMPORTANT]
> Illusive attack Management Systems data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln förklarar hur du ansluter ditt [Illusive-attack hanterings system](https://www.illusivenetworks.com/technology/platform/attack-detection-system) till Azure Sentinel. Med data anslutnings verktyget Illusive attack Management Systems kan du dela Illusives data och incident loggar med Azure Sentinel och visa den här informationen i dedikerade instrument paneler som ger inblick i din organisations risk för attack ytan (ASM-instrumentpanel) och spårar obehörig lateral förflyttning i din organisations nätverk (ADS instrument panel).

> [!NOTE]
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Vidarebefordra Illusive attack Management System-loggar till syslog-agenten  

Konfigurera angrepps hanterings system för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten.

1. Logga in på Illusive-konsolen och gå till inställningar->rapportering.

1. Hitta syslog-servrar.

1. Ange följande information:
   - Värdnamn: Linux syslog-agentens IP-adress eller FQDN-värdnamn
   - Port: 514
   - Protokoll: TCP
   - Gransknings meddelanden: skicka gransknings meddelanden till servern

1. Klicka på Lägg till om du vill lägga till syslog-servern.

1. Om du vill använda det relevanta schemat i **loggar** för Illusive attack Management Systems söker du efter **CommonSecurityLog**.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Illusive attack Management system till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
