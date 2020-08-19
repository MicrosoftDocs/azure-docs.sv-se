---
title: Distribuera logg vidarebefordraren för att ansluta CEF-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du distribuerar agenten för att ansluta CEF-data till Azure Sentinel.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: a7d7c7b7236841835866ccb7786e7e4eab767c1f
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565595"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Steg 1: Distribuera logg vidarebefordraren


I det här steget ska du ange och konfigurera Linux-datorn som kommer att vidarebefordra loggarna från din säkerhets lösning till din Azure Sentinel-arbetsyta. Den här datorn kan vara en fysisk eller virtuell dator i din lokala miljö, en virtuell Azure-dator eller en virtuell dator i ett annat moln. Med hjälp av den angivna länken kör du ett skript på den avsedda datorn som utför följande uppgifter:
- Installerar Log Analytics agent för Linux (kallas även OMS-agenten) och konfigurerar den i följande syfte:
    - Lyssna efter CEF-meddelanden från den inbyggda Linux syslog-daemonen på TCP-port 25226
    - skicka meddelanden säkert över TLS till din Azure Sentinel-arbetsyta, där de är parsade och berikade

- Konfigurerar det inbyggda Linux syslog-daemonen (rsyslog. d/syslog-ng) i följande syfte:
    - Lyssna efter syslog-meddelanden från dina säkerhets lösningar på TCP-port 514
    - vidarebefordra enbart de meddelanden som identifieras som CEF till Log Analytics agent på localhost med TCP-port 25226
 
## <a name="prerequisites"></a>Förutsättningar

- Du måste ha förhöjd behörighet (sudo) på den utsedda Linux-datorn.
- Du måste ha python installerat på Linux-datorn.<br>Använd `python -version` kommandot för att kontrol lera.
- Linux-datorn får inte vara ansluten till några Azure-arbetsytor innan du installerar Log Analytics agenten.

## <a name="run-the-deployment-script"></a>Kör distributions skriptet
 
1. Klicka på **data kopplingar**på navigerings menyn i Azure Sentinel. I listan över anslutningar klickar du på panelen **common Event format (CEF)** och sedan på knappen **Öppna kopplings sida** längst ned till höger. 

1. Under **1,2 installerar du CEF-insamlaren på Linux-datorn**, kopierar länken som finns under **Kör följande skript för att installera och tillämpa CEF-insamlaren**eller från texten nedan:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. Kontrol lera att du inte får några fel eller varnings meddelanden medan skriptet körs.

> [!NOTE]
> **Använda samma dator för att vidarebefordra både vanliga syslog- *och* CEF-meddelanden**
>
> Om du planerar att använda den här logg vidarebefordraren för att vidarebefordra [syslog-meddelanden](connect-syslog.md) och CEF, så att du slipper duplicera händelser till syslog-och CommonSecurityLog-tabellerna:
>
> 1. På varje käll dator som skickar loggar till vidarebefordraren i CEF-format måste du redigera syslog-konfigurationsfilen för att ta bort de anläggningar som används för att skicka CEF-meddelanden. På så sätt kommer inte de funktioner som skickas i CEF också att skickas i syslog. Mer information om hur du gör detta finns i [Konfigurera syslog på Linux-agenten](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Du måste köra följande kommando på de datorerna för att inaktivera synkroniseringen av agenten med syslog-konfigurationen i Azure Sentinel. Detta säkerställer att konfigurations ändringen du gjorde i föregående steg inte blir överskriven.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Fortsätt till [steg 2: Konfigurera säkerhets lösningen för att vidarebefordra CEF-meddelanden](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Förklarat distributions skript

Följande är en beskrivning av kommando-till-kommando för distributions skriptets åtgärder.

Välj en syslog-daemon för att se lämplig beskrivning.

# <a name="rsyslog-daemon"></a>[rsyslog daemon](#tab/rsyslog)

1. **Hämta och installera Log Analytics agenten:**

    - Laddar ned installations skriptet för Linux-agenten Log Analytics (OMS)<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installerar Log Analytics agent<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurera syslog-daemon:**

    1. Öppnar port 514 för TCP-kommunikation med syslog-konfigurationsfilen `/etc/rsyslog.conf` .

    1. Konfigurerar daemonen att vidarebefordra CEF-meddelanden till Log Analytics agent på TCP-port 25226 genom att infoga en särskild konfigurations fil `security-config-omsagent.conf` i syslog-katalogen `/etc/rsyslog.d/` .

        `security-config-omsagent.conf`Filens innehåll:

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```

1. **Starta om syslog-daemon**

    `service rsyslog restart`

1. **Konfigurera Log Analytics agent-konfigurationen så att den lyssnar på port 25226 och vidarebefordrar CEF-meddelanden till Azure Sentinel**

    1. Laddar ned konfigurationen från Log Analytics-agentens GitHub-lagringsplats<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Startar om Log Analytics agenten<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng-daemon](#tab/syslogng)

1. **Hämta och installera Log Analytics agenten:**

    - Laddar ned installations skriptet för Linux-agenten Log Analytics (OMS)<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Installerar Log Analytics agent<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Konfigurera syslog-daemon:**

    1. Öppnar port 514 för TCP-kommunikation med syslog-konfigurationsfilen `/etc/syslog-ng/syslog-ng.conf` .

    1. Konfigurerar daemonen att vidarebefordra CEF-meddelanden till Log Analytics agent på TCP-port 25226 genom att infoga en särskild konfigurations fil `security-config-omsagent.conf` i syslog-katalogen `/etc/syslog-ng/conf.d/` .

        `security-config-omsagent.conf`Filens innehåll:

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Starta om syslog-daemon**

    `service syslog-ng restart`

1. **Konfigurera Log Analytics agent-konfigurationen så att den lyssnar på port 25226 och vidarebefordrar CEF-meddelanden till Azure Sentinel**

    1. Laddar ned konfigurationen från Log Analytics-agentens GitHub-lagringsplats<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Startar om Log Analytics agenten<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du distribuerar Log Analytics agent för att ansluta CEF-enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

