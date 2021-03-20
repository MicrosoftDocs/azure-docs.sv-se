---
title: Anslut Palo-nätverks data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du använder data anslutningen Palo RJ Networks för att enkelt ansluta dina Palo-nätverks loggar med Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85564566"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Anslut Palo-nätverk till Azure Sentinel



Den här artikeln beskriver hur du ansluter din Palo-enhet med-nätverk till Azure Sentinel. Med Palo-nätverkets data koppling kan du enkelt ansluta dina Palo-nätverks loggar med Azure Sentinel, Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Användning av Palo-nätverk i Azure Sentinel ger dig mer insikter om din organisations Internet användning och förbättrar säkerhets åtgärdens funktioner. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Vidarebefordra Palo-nätverk loggar till syslog-agenten

Konfigurera Palo-nätverk för att vidarebefordra syslog-meddelanden i CEF-format till Azure-arbetsytan via syslog-agenten:
1.  Gå till [konfigurations guider för common Event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) och ladda ned PDF-filen för din installations typ. Följ alla instruktioner i guiden för att konfigurera Palo-enheter för att samla in CEF-händelser. 

1.  Gå till [Konfigurera Syslog-övervakning](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) och följ steg 2 och 3 för att konfigurera CEF-händelse vidarebefordran från din Palo-enhets enhets nätverk till Azure Sentinel.

    1. Se till att ställa in **syslog-serverns format** på **BSD**.

       > [!NOTE]
       > Kopierings-och Inklistrings åtgärderna från PDF-filen kan ändra texten och infoga slumpmässiga tecken. Undvik detta genom att kopiera texten till en redigerare och ta bort eventuella tecken som kan bryta logg formatet innan du klistrar in det, som du kan se i det här exemplet.
 
        ![CEF text kopierings problem](./media/connect-cef/paloalto-text-prob1.png)

1. Om du vill använda det relevanta schemat i Log Analytics för Palo-nätverks händelser söker du efter **CommonSecurityLog**.

1. Fortsätt till [steg 3: verifiera anslutningen](connect-cef-verify.md).




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Palo-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.


