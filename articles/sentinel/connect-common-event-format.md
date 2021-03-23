---
title: Anslut CEF-data till Azure Sentinel Preview | Microsoft Docs
description: Anslut en extern lösning som skickar CEF-meddelanden (common Event format) till Azure Sentinel, med en Linux-dator som en logg vidarebefordrare.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772067"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Ansluta din externa lösning med hjälp av vanligt händelse format

När du ansluter en extern lösning som skickar CEF-meddelanden, finns det tre steg för att ansluta till Azure Sentinel:

STEG 1: [Anslut CEF genom att distribuera en syslog/CEF forwarder](connect-cef-agent.md) steg 2: [utföra lösnings bara](connect-cef-solution-config.md) steg 3: [kontrol lera anslutningen](connect-cef-verify.md)

Den här artikeln beskriver hur anslutningen fungerar, visar krav och visar stegen för att distribuera en mekanism för säkerhetslösningar för att skicka CEF-meddelanden (common Event format) ovanpå syslog. 

> [!NOTE] 
> Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.

För att du ska kunna ansluta till den här anslutningen måste du distribuera en syslog-vidarebefordrare som stöder kommunikationen mellan-enheten och Azure Sentinel.  -Servern består av en särskild Linux-dator (VM eller lokal) med Log Analytics agent för Linux installerat. 

Följande diagram beskriver installationen om det gäller en virtuell Linux-dator i Azure:

 ![CEF i Azure](./media/connect-cef/cef-syslog-azure.png)

Detta kan också finnas om du använder en virtuell dator i ett annat moln eller på en lokal dator: 

 ![CEF lokalt](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Säkerhetsöverväganden

Se till att konfigurera datorns säkerhet enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverket så att det överensstämmer med företagets nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med dina krav. Du kan använda följande instruktioner för att förbättra datorns säkerhets konfiguration:  [säker virtuell dator i Azure](../virtual-machines/security-policy.md), [metod tips för nätverks säkerhet](../security/fundamentals/network-best-practices.md).

Om du vill använda TLS-kommunikation mellan syslog-källan och syslog-vidarebefordraren måste du konfigurera syslog-daemonen (rsyslog eller syslog-ng) för att kommunicera i TLS: [kryptera syslog-trafik med TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [kryptera logg meddelanden med TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att Linux-datorn som du använder som en logg vidarebefordrare kör något av följande operativ system:

- 64-bitars
  - CentOS 7 och 8, inklusive lägre versioner (inte 6)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 och 8, inklusive lägre versioner (inte 6)
  - Debian GNU/Linux 8 och 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS och 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32-bitars
  - CentOS 7 och 8, inklusive lägre versioner (inte 6)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 och 8, inklusive lägre versioner (inte 6)
  - Debian GNU/Linux 8 och 9
  - Ubuntu Linux 14,04 LTS och 16,04 LTS
 
- Daemon-versioner
  - Syslog-ng: 2,1-3.22.1
  - Rsyslog: V8
  
- Syslog-RFC stöds
  - Syslog RFC 3164
  - Syslog RFC 5424
 
Kontrol lera att datorn också uppfyller följande krav: 

- Kapacitet
  - Datorn måste ha minst **4 processor kärnor och 8 GB RAM-minne**.

    > [!NOTE]
    > - En enskild logg vidarebefordrare med hjälp av **rsyslog** daemon har en kapacitet på **upp till 8500 händelser per sekund (EPS) som** samlats in.

- Behörigheter
  - Du måste ha förhöjd behörighet (sudo) på datorn. 

- Programvarukrav
  - Se till att du har python 2,7 eller 3 som körs på datorn.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur Azure Sentinel samlar in CEF-loggar från säkerhetslösningar och apparater. Information om hur du ansluter din lösning till Azure Sentinel finns i följande artiklar:

- STEG 1: [Anslut CEF genom att distribuera en syslog/CEF-vidarebefordrare](connect-cef-agent.md)
- STEG 2: [utföra lösningar – vissa steg](connect-cef-solution-config.md)
- STEG 3: [kontrol lera anslutningen](connect-cef-verify.md)

Mer information om vad du kan göra med de data som du har samlat in i Azure Sentinel finns i följande artiklar:

- Lär dig mer om [fält mappning för CEF och CommonSecurityLog](cef-name-mapping.md).
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).