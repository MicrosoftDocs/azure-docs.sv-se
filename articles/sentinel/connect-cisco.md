---
title: Anslut Cisco-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Cisco ASA-enheten till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566036"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Anslut Cisco ASA till Azure Sentinel



Den här artikeln förklarar hur du ansluter Cisco ASA-installationen till Azure Sentinel. Med Cisco ASA data Connector kan du enkelt ansluta dina Cisco ASA-loggar med Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Om du använder Cisco ASA i Azure Sentinel får du mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Vidarebefordra Cisco ASA-loggar till syslog-agenten

Cisco ASA stöder inte CEF, så loggarna skickas som syslog och Azure Sentinel-agenten vet hur de kan parsas som om de är CEF-loggar. Konfigurera Cisco ASA för att vidarebefordra syslog-meddelanden till Azure-arbetsytan via syslog-agenten:

1. Gå till [Skicka syslog-meddelanden till en extern Syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)och följ anvisningarna för att konfigurera anslutningen. Använd följande parametrar när du uppmanas till det:
    - Ange **port** till 514 eller porten som du anger i agenten.
    - Ange **SYSLOG_IP** IP-adressen för agenten.

1. Om du vill använda det relevanta schemat i Log Analytics för Cisco-händelserna söker du efter `CommonSecurityLog` .

1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Cisco ASA-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


