---
title: Samla in syslog-datakällor med Log Analytics agent i Azure Monitor
description: Syslog är ett händelse loggnings protokoll som är gemensamt för Linux. Den här artikeln beskriver hur du konfigurerar insamling av syslog-meddelanden i Log Analytics och information om de poster som de skapar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 2d7406c1e801a07f10342c47e7334e6a12bfd449
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623050"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Samla in syslog-datakällor med Log Analytics agent
Syslog är ett händelse loggnings protokoll som är gemensamt för Linux. Program kommer att skicka meddelanden som kan lagras på den lokala datorn eller levereras till en syslog-insamlare. När Log Analytics-agenten för Linux installeras konfigureras den lokala syslog-daemonen för att vidarebefordra meddelanden till agenten. Agenten skickar sedan meddelandet till Azure Monitor där en motsvarande post skapas.  

> [!IMPORTANT]
> Den här artikeln beskriver hur du samlar in Syslog-händelser med [Log Analytics agent](../platform/log-analytics-agent.md) som är en av de agenter som används av Azure Monitor. Andra agenter samlar in olika data och konfigureras på olika sätt. Se [Översikt över Azure Monitor agenter](../agents/agents-overview.md) för en lista över tillgängliga agenter och de data som de kan samla in.

> [!NOTE]
> Azure Monitor stöder insamling av meddelanden som skickats av rsyslog eller syslog-ng, där rsyslog är standard-daemon. Standard syslog-daemonen på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för händelse insamling i syslog. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska [rsyslog daemon](http://rsyslog.com) installeras och konfigureras för att ersätta sysklog.
>
>

![Syslog-samling](media/data-sources-syslog/overview.png)

Följande anläggningar stöds med syslog-insamlaren:

* kerning
* användare
* e-post
* program
* Factor
* syslog
* LPR
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

[Konfigurera en data källa för anpassade loggar](data-sources-custom-logs.md) i Azure Monitor för alla andra anläggningar.
 
## <a name="configuring-syslog"></a>Konfigurera syslog
Log Analytics agent för Linux samlar endast in händelser med de funktioner och allvarlighets grader som anges i konfigurationen. Du kan konfigurera syslog genom Azure Portal eller genom att hantera konfigurationsfiler på Linux-agenterna.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurera syslog i Azure Portal
Konfigurera syslog från [Data-menyn i avancerade inställningar](../agents/agent-data-sources.md#configuring-data-sources) för arbets ytan Log Analytics. Den här konfigurationen skickas till konfigurations filen på varje Linux-Agent.

Du kan lägga till en ny anläggning genom att först välja alternativet **Använd konfigurationen nedan för mina datorer** och sedan skriva in dess namn och klicka på **+** . För varje funktion samlas endast meddelanden med de valda allvarlighets graderna in.  Kontrol lera allvarlighets graderna för den specifika funktionen som du vill samla in. Du kan inte ange några ytterligare kriterier för att filtrera meddelanden.

![Konfigurera syslog](media/data-sources-syslog/configure.png)

Som standard flyttas alla konfigurations ändringar automatiskt till alla agenter. Om du vill konfigurera syslog manuellt på varje Linux-Agent avmarkerar du kryss rutan *Använd konfigurationen nedan för mina datorer*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurera syslog på Linux-agenten
När [Log Analytics-agenten är installerad på en Linux-klient](../learn/quick-collect-linux-computer.md)installerar den en standard konfigurations fil för syslog som definierar funktionen och allvarlighets graden för de meddelanden som samlas in. Du kan ändra konfigurationen genom att ändra den här filen. Konfigurations filen är olika beroende på den syslog-daemon som klienten har installerat.

> [!NOTE]
> Om du redigerar syslog-konfigurationen måste du starta om syslog-daemon för att ändringarna ska börja gälla.
>
>

#### <a name="rsyslog"></a>rsyslog
Konfigurations filen för rsyslog finns på **/etc/rsyslog.d/95-omsagent.conf**. Dess standard innehåll visas nedan. Detta samlar in syslog-meddelanden som skickas från den lokala agenten för alla anläggningar med en nivå av varning eller högre.

```config
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

Du kan ta bort en anläggning genom att ta bort dess del av konfigurations filen. Du kan begränsa de allvarlighets grader som samlas in för en viss anläggning genom att ändra anläggningens post. Om du till exempel vill begränsa användar funktionen till meddelanden med allvarlighets graden fel eller högre ändrar du den raden i konfigurations filen till följande:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog-ng
Konfigurations filen för syslog-ng är plats på **/etc/syslog-ng/syslog-ng.conf**.  Dess standard innehåll visas nedan. Detta samlar in syslog-meddelanden som skickas från den lokala agenten för alla anläggningar och alla allvarlighets grader.   

```config
#
# Warnings (except iptables) in one file:
#
destination warn { file("/var/log/warn" fsync(yes)); };
log { source(src); filter(f_warn); destination(warn); };

#OMS_Destination
destination d_oms { udp("127.0.0.1" port(25224)); };

#OMS_facility = auth
filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
log { source(src); filter(f_auth_oms); destination(d_oms); };

#OMS_facility = authpriv
filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
log { source(src); filter(f_authpriv_oms); destination(d_oms); };

#OMS_facility = cron
filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
log { source(src); filter(f_cron_oms); destination(d_oms); };

#OMS_facility = daemon
filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
log { source(src); filter(f_daemon_oms); destination(d_oms); };

#OMS_facility = kern
filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
log { source(src); filter(f_kern_oms); destination(d_oms); };

#OMS_facility = local0
filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
log { source(src); filter(f_local0_oms); destination(d_oms); };

#OMS_facility = local1
filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
log { source(src); filter(f_local1_oms); destination(d_oms); };

#OMS_facility = mail
filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
log { source(src); filter(f_mail_oms); destination(d_oms); };

#OMS_facility = syslog
filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
log { source(src); filter(f_syslog_oms); destination(d_oms); };

#OMS_facility = user
filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

Du kan ta bort en anläggning genom att ta bort dess del av konfigurations filen. Du kan begränsa de allvarlighets grader som samlas in för en viss funktion genom att ta bort dem från listan.  Om du till exempel vill begränsa användar funktionen till enbart aviseringar och kritiska meddelanden ändrar du avsnittet i konfigurations filen till följande:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Samla in data från ytterligare syslog-portar
Log Analytics agenten lyssnar efter syslog-meddelanden på den lokala klienten på port 25224.  När agenten installeras används en Standard syslog-konfiguration som finns på följande plats:

* Rsyslog `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

Du kan ändra port numret genom att skapa två konfigurationsfiler: en upprättad konfigurations fil och en rsyslog-eller-syslog-ng-fil beroende på den syslog-daemon som du har installerat.  

* Konfigurations filen för Office bör vara en ny fil som finns i: `/etc/opt/microsoft/omsagent/conf/omsagent.d` och Ersätt värdet i **port** posten med det anpassade port numret.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* För rsyslog bör du skapa en ny konfigurations fil som finns i: `/etc/rsyslog.d/` och ersätta värdet% SYSLOG_PORT% med det anpassade port numret.  

    > [!NOTE]
    > Om du ändrar det här värdet i konfigurations filen `95-omsagent.conf` kommer det att skrivas över när agenten använder en standard konfiguration.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* Konfigurationen syslog-ng ska ändras genom att du kopierar exempel konfigurationen som visas nedan och lägger till anpassade ändrade inställningar i slutet av konfigurations filen syslog-ng. conf i `/etc/syslog-ng/` . Använd **inte** standard etiketten **% WORKSPACE_ID% _oms** eller **% WORKSPACE_ID_OMS** och definiera en anpassad etikett som hjälper dig att skilja dina ändringar.  

    > [!NOTE]
    > Om du ändrar standardvärdena i konfigurations filen kommer de att skrivas över när agenten använder en standard konfiguration.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

När ändringarna har slutförts måste syslog och tjänsten Log Analytics agent startas om för att se till att konfigurations ändringarna börjar gälla.   

## <a name="syslog-record-properties"></a>Egenskaper för syslog-poster
Syslog-poster har en typ av **syslog** och har egenskaperna i följande tabell.

| Egenskap | Beskrivning |
|:--- |:--- |
| Dator |Datorn som händelsen samlades in från. |
| Anläggning |Definierar den del av systemet som skapade meddelandet. |
| HostIP |IP-adressen för systemet som skickar meddelandet. |
| HostName |Namnet på det system som skickar meddelandet. |
| SeverityLevel |Händelsens allvarlighets grad. |
| SyslogMessage |Meddelandets text. |
| ProcessID |ID för den process som skapade meddelandet. |
| EventTime |Datum och tid då händelsen genererades. |

## <a name="log-queries-with-syslog-records"></a>Logga frågor med syslog-poster
Följande tabell innehåller olika exempel på logg frågor som hämtar syslog-poster.

| Söka i data | Description |
|:--- |:--- |
| Syslog |Alla Syslogs. |
| Syslog-&#124; där SeverityLevel = = "Error" |Alla syslog-poster med allvarlighets graden fel. |
| Syslog &#124; sammanfatta AggregatedValue = Count () efter dator |Antal syslog-poster per dator. |
| Syslog &#124; sammanfatta AggregatedValue = Count () efter funktion |Antal syslog-poster per funktion. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.
* Använd [anpassade fält](./../platform/custom-fields.md) för att parsa data från syslog-poster i enskilda fält.
* [Konfigurera Linux-agenter](../learn/quick-collect-linux-computer.md) för att samla in andra typer av data.

