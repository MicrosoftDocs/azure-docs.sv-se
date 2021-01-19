---
title: Konfigurera din säkerhets lösning för att ansluta CEF-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du konfigurerar din säkerhets lösning för att ansluta CEF-data till Azure Sentinel.
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
ms.openlocfilehash: 80e5cc18888b0e014fc1f617ca27dee05146472e
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567679"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STEG 2: Konfigurera säkerhets lösningen för att skicka CEF-meddelanden

I det här steget ska du utföra nödvändiga konfigurations ändringar för själva säkerhetslösningen för att skicka loggar till CEF-agenten.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurera en lösning med en koppling

Om din säkerhetslösning redan har en befintlig koppling använder du de anslutningsspecifika instruktionerna på följande sätt:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise – valv för lösenord](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Forcepoint-produkter](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic hemlig Server](connect-thycotic-secret-server.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [WireX Network data utredning Platform](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Konfigurera andra lösningar

Om det inte finns någon koppling för din säkerhets lösning använder du följande allmänna instruktioner för att vidarebefordra loggar till CEF-agenten.

1. Gå till den speciella konfigurations artikeln för steg om hur du konfigurerar din lösning för att skicka CEF-meddelanden. Om din lösning inte finns med på den här enheten måste du ange dessa värden så att installations programmet skickar nödvändiga loggar i det format som krävs till Azure Sentinel syslog-agenten, baserat på Log Analytics agenten. Du kan ändra dessa parametrar i din installation så länge du också ändrar dem i syslog-daemonen på Azure Sentinel-agenten.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-adress – se till att skicka CEF-meddelanden till IP-adressen för den virtuella dator som du dedikerat för detta ändamål.

   Den här lösningen stöder Syslog RFC 3164 eller RFC 5424.

1. Om du vill söka efter CEF-händelser i Log Analytics anger `CommonSecurityLog` du i frågefönstret.

1. Fortsätt till steg 3: [Verifiera anslutningen](connect-cef-verify.md).

> [!NOTE]
> **Ändra källa för fältet TimeGenerated**
>
> - Som standard fyller Log Analytics-agenten i fältet *TimeGenerated* i schemat med den tidpunkt då agenten tog emot händelsen från syslog-daemonen. Därför registreras inte tiden då händelsen genererades i källsystemet i Azure Sentinel.
>
> - Du kan dock köra följande kommando för att ladda ned och köra `TimeGenerated.py` skriptet. Det här skriptet konfigurerar Log Analytics-agenten så att den fyller i fältet *TimeGenerated* med händelsens ursprungliga tid på käll systemet, i stället för den tid det tog emot av agenten.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).