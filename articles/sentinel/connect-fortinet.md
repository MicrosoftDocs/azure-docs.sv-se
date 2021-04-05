---
title: Anslut Fortinet-data till Azure Sentinel | Microsoft Docs
description: Anslut din Fortinet-apparat till Azure Sentinel för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92914001"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Anslut Fortinet till Azure Sentinel



Den här artikeln förklarar hur du ansluter din Fortinet-installation till Azure Sentinel. Med Fortinet data Connector kan du enkelt ansluta dina Fortinet-loggar med Azure Sentinel, för att visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Genom att använda Fortinet på Azure Sentinel får du mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Vidarebefordra Fortinet-loggar till syslog-agenten

Konfigurera Fortinet så att syslog-meddelanden vidarebefordras i CEF-format till Azure-arbetsytan via syslog-agenten.

1. Öppna CLI på Fortinet-enheten och kör följande kommandon:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Ersätt serverns **IP-adress** med AGENTens IP-adress.
    - Ställ in **syslog-porten** på **514** eller port uppsättningen på agenten.
    - Om du vill aktivera CEF-format i tidiga FortiOS-versioner kan du behöva köra kommandot **CSV Disable**.
 
   > [!NOTE] 
   > Mer information finns i [Fortinet-dokumentbiblioteket](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Välj din version och Använd referensen **handböcker** och **logg meddelande**.

1. Om du vill använda det relevanta schemat i Azure Monitor Log Analytics för Fortinet-händelserna söker du efter `CommonSecurityLog` .

1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).


## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du ansluter Fortinet-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


