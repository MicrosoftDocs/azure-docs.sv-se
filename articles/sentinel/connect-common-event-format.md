---
title: Anslut CEF-data till Azure Sentinel Preview | Microsoft Docs
description: Anslut en extern lösning som skickar CEF-meddelanden (common Event format) till Azure Sentinel genom att använda en Linux-dator som proxy.
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
ms.date: 11/26/2019
ms.author: yelevin
ms.openlocfilehash: b7f03c11b53c6dc61fad6b916e7c08086917b416
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565748"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ansluta din externa lösning med hjälp av vanligt händelse format


När du ansluter en extern lösning som skickar CEF-meddelanden, finns det tre steg för att ansluta till Azure Sentinel:

STEG 1: [Anslut CEF genom att distribuera agent](connect-cef-agent.md) steg 2: [utföra lösnings åtgärder](connect-cef-solution-config.md) steg 3: [kontrol lera anslutningen](connect-cef-verify.md)

Den här artikeln beskriver hur anslutningen fungerar, innehåller krav och ger dig stegen för att distribuera agenten på säkerhetslösningar som skickar common Event format-meddelanden (CEF) ovanpå syslog. 

> [!NOTE] 
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

För att kunna ansluta till den här anslutningen måste du distribuera en agent på en särskild Linux-dator (VM eller lokalt) för att stödja kommunikationen mellan-enheten och Azure Sentinel. Följande diagram beskriver installationen i händelse av en virtuell Linux-dator i Azure.

 ![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Detta kan också finnas om du använder en virtuell dator i ett annat moln eller på en lokal dator. 

 ![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Säkerhetsöverväganden

Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. Du kan använda följande instruktioner för att förbättra datorns säkerhets konfiguration:  [säker virtuell dator i Azure](../virtual-machines/linux/security-policy.md), [metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md).

Om du vill använda TLS-kommunikation mellan säkerhetslösningen och syslog-datorn måste du konfigurera syslog-daemonen (rsyslog eller syslog-ng) för att kommunicera i TLS: [kryptera syslog-trafik med TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [kryptera logg meddelanden med TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Förutsättningar
Kontrol lera att Linux-datorn som du använder som proxy kör något av följande operativ system:

- 64-bitars
  - CentOS 6 och 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 och 7
  - Red Hat Enterprise Linux Server 6 och 7
  - Debian GNU/Linux 8 och 9
  - Ubuntu Linux 14,04 LTS 16,04 LTS och 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32-bitars
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 och 9
   - Ubuntu Linux 14,04 LTS och 16,04 LTS
 
 - Daemon-versioner
   - Syslog-ng: 2,1-3.22.1
   - Rsyslog: V8
  
 - Syslog-RFC stöds
   - Syslog RFC 3164
   - Syslog RFC 5424
 
Kontrol lera att datorn också uppfyller följande krav: 
- Behörigheter
    - Du måste ha förhöjd behörighet (sudo) på datorn. 
- Programvarukrav
    - Kontrol lera att du har python igång på datorn



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

