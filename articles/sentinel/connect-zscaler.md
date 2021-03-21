---
title: Anslut Zscaler-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Zscaler-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: d788219e36ac94b1d13b2f4819c3e546622ddff1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655263"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Anslut Zscaler Internet åtkomst till Azure Sentinel

Den här artikeln förklarar hur du ansluter din Zscaler-enhet med Internet åtkomst till Azure Sentinel. Med Zscaler data Connector kan du enkelt ansluta dina ZIA-loggar (Zscaler Internet Access) med Azure Sentinel, om du vill visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Zscaler på Azure Sentinel får du mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>Konfigurera din Zscaler för att skicka CEF-meddelanden

1. På Zscaler-installationen måste du ange dessa värden så att installations programmet skickar nödvändiga loggar i det format som krävs till Azure Sentinel syslog-agenten, baserat på Log Analytics agenten. Du kan ändra dessa parametrar i din installation så länge du också ändrar dem i syslog-daemonen på Azure Sentinel-agenten.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-adress – se till att skicka CEF-meddelanden till IP-adressen för den virtuella dator som du dedikerat för detta ändamål.
 Mer information finns i [distributions hand boken för Zscaler och Azure Sentinel](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Den här lösningen stöder Syslog RFC 3164 eller RFC 5424.


1. Om du vill använda det relevanta schemat i Log Analytics för CEF-händelserna söker du efter `CommonSecurityLog` .
1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Zscaler Internet åtkomst till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.