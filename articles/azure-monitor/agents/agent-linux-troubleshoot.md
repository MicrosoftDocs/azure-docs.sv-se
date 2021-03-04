---
title: Felsöka Azure Log Analytics Linux-agenten | Microsoft Docs
description: Beskriv symptom, orsaker och lösningar för de vanligaste problemen med Log Analytics-agenten för Linux i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 202182d6485bc340a7f9bb34622e483dcc9ff891
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041883"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Felsöka problem med Log Analytics-agenten för Linux 

Den här artikeln innehåller fel söknings fel som kan uppstå i Log Analytics-agenten för Linux i Azure Monitor och ger förslag på möjliga lösningar för att lösa dem.

Om inget av dessa steg fungerar för dig, är följande Support kanaler också tillgängliga:

* Kunder med Premier Support-förmåner kan öppna en support förfrågan med [Premier](https://premier.microsoft.com/).
* Kunder med support avtal för Azure kan öppna en support förfrågan [i Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* Diagnostisera OMI-problem med [fel söknings guiden för OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Besök sidan Log Analytics feedback och granska skickade idéer och buggar [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) eller skicka en ny fil. 

## <a name="log-analytics-troubleshooting-tool"></a>Log Analytics fel söknings verktyg

Fel söknings verktyget Log Analytics agent Linux är ett skript som har utformats för att hjälpa till att hitta och diagnostisera problem med Log Analytics-agenten. Den medföljer automatiskt med agenten vid installationen. Att köra verktyget bör vara det första steget när du diagnostiserar ett problem.

### <a name="how-to-use"></a>Använda
Du kan köra fel söknings verktyget genom att klistra in följande kommando i ett terminalfönster på en dator med Log Analytics agent: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Manuell installation
Fel söknings verktyget ingår automatiskt när du installerar Log Analytics agenten. Men om installationen Miss lyckas på något sätt kan den också installeras manuellt genom att följa stegen nedan.

1. Kopiera fel söknings paketet till datorn: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Packa upp paketet: `tar -xzvf omsagent_tst.tar.gz`
3. Kör den manuella installationen: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Scenarier som omfattas
Nedan visas en lista över scenarier som kontrol leras av fel söknings verktyget:

1. Agenten är inte felfri, pulsslag fungerar inte korrekt
2. Agenten startar inte, det går inte att ansluta till logg analys tjänster
3. Syslog-agenten fungerar inte
4. Agenten har hög processor-/minnes användning
5. Agent som har installations problem
6. Anpassade loggar för agent fungerar inte
7. Samla in agent loggar

Se vår [GitHub-dokumentation](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md)för mer information.

 >[!NOTE]
 >Kör verktyget för logg insamlaren när ett problem uppstår. Om loggarna inlednings vis hjälper vårt support team att felsöka problemet snabbare.

## <a name="purge-and-re-install-the-linux-agent"></a>Rensa och Re-Install Linux-agenten

Vi har sett att en ren ominstallation av agenten kommer att åtgärda de flesta problem. Detta kan i själva verket vara det första förslaget från support för att hämta agenten till ett uncurropted-tillstånd från vårt support team. Genom att köra fel sökaren, logga in och försöka med en ren installation kan du snabbt lösa problem.

1. Hämta rensnings skriptet:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Kör rensnings skriptet (med sudo-behörigheter):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Viktiga logg platser och logg insamlings verktyg

 Fil | Sökväg
 ---- | -----
 Log Analytics agent för Linux-loggfil | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics logg filen för agent konfiguration | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Vi rekommenderar att du använder vårt logg insamlings verktyg för att hämta viktiga loggar för fel sökning eller innan du skickar ett GitHub-problem. Du kan läsa mer om verktyget och hur du kör det [här](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Viktiga konfigurationsfiler

 Kategori | Filplats
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` eller `/etc/rsyslog.conf` eller `/etc/rsyslog.d/95-omsagent.conf`
 Prestanda, nagios, zabbix, Log Analytics utdata och allmän agent | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Ytterligare konfigurationer | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Att redigera konfigurationsfiler för prestanda räknare och syslog skrivs över om samlingen konfigureras från [Data-menyn Log Analytics avancerade inställningar](../agents/agent-data-sources.md#configuring-data-sources) i Azure Portal för din arbets yta. Om du vill inaktivera konfigurationen för alla agenter inaktiverar du samling från Log Analytics **Avancerade inställningar** eller för en enskild agent kör följande:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Installations fel koder

| Felkod | Innebörd |
| --- | --- |
| NOT_DEFINED | Eftersom nödvändiga beroenden inte är installerade kommer auoms-granskade plugin-programmet inte att installeras | Det gick inte att installera auoms, installations paketet har granskats. |
| 2 | Ett ogiltigt alternativ angavs för gränssnitts paketet. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning |
| 3 | Inget alternativ angavs för gränssnitts paketet. Kör `sudo sh ./omsagent-*.universal*.sh --help` för användning. |
| 4 | Ogiltig pakettyp eller ogiltiga proxyinställningar. omsagent-*rpm*. sh-paket kan bara installeras på RPM-baserade system och omsagent-*deb*. sh-paket kan bara installeras på Debian-baserade system. Vi rekommenderar att du använder det universella installations programmet från den [senaste versionen](../vm/quick-collect-linux-computer.md#install-the-agent-for-linux). Se även till att verifiera proxyinställningarna. |
| 5 | Shell-paketet måste köras som rot eller så uppstod ett 403-fel under inregistreringen. Kör kommandot med hjälp av `sudo` . |
| 6 | Ogiltig paket arkitektur eller så uppstod fel 200 vid registrering. omsagent-*x64.sh-paket kan bara installeras på 64-bitars system, och omsagent-x86.sh-* paket kan bara installeras på 32-bitars system. Hämta rätt paket för din arkitektur från den [senaste versionen](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | Installationen av OMS-paketet misslyckades. Titta igenom kommandots utdata för rot felen. |
| 18 | Det gick inte att installera OMSConfig-paketet. Titta igenom kommandots utdata för rot felen. |
| 19 | Det gick inte att installera OMI-paketet. Titta igenom kommandots utdata för rot felen. |
| 20 | Det gick inte att installera SCX-paketet. Titta igenom kommandots utdata för rot felen. |
| 21 | Det gick inte att installera Provider-paket. Titta igenom kommandots utdata för rot felen. |
| 22 | Det gick inte att installera paketet. Titta igenom kommandots utdata för rot felen |
| 23 | SCX-eller OMI-paketet har redan installerats. Använd `--upgrade` i stället för `--install` för att installera Shell-paketet. |
| 30 | Internt paket fel. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 55 | Openssl-versionen stöds inte eller så går det inte att ansluta till Azure Monitor eller så är dpkg låst eller så saknas ett sväng program. |
| 61 | Python ctypes-biblioteket saknas. Installera python ctypes Library eller Package (python-ctypes). |
| 62 | Tar programmet som saknas, installera tar. |
| 63 | Ett sed-program saknas, installera sed. |
| 64 | Ett sväng program saknas, installera sväng. |
| 65 | Gpg-programmet saknas, installera GPG. |

## <a name="onboarding-error-codes"></a>Fel koder för onboarding

| Felkod | Innebörd |
| --- | --- |
| 2 | Ett ogiltigt alternativ har angetts för omsadmin-skriptet. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 3 | En ogiltig konfiguration angavs för omsadmin-skriptet. Kör `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` för användning. |
| 4 | Ogiltig proxy angavs för omsadmin-skriptet. Verifiera proxyn och se vår [dokumentation för att använda en HTTP-proxy](./log-analytics-agent.md#firewall-requirements). |
| 5 | 403 HTTP-fel togs emot från Azure Monitor. Mer information finns i omsadmin-skriptets fullständiga utdata. |
| 6 | HTTP-fel som inte är 200 togs emot från Azure Monitor. Mer information finns i omsadmin-skriptets fullständiga utdata. |
| 7 | Det går inte att ansluta till Azure Monitor. Mer information finns i omsadmin-skriptets fullständiga utdata. |
| 8 | Fel vid registrering till Log Analytics-arbetsyta. Mer information finns i omsadmin-skriptets fullständiga utdata. |
| 30 | Internt skript fel. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 31 | Fel vid generering av agent-ID. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 32 | Fel vid generering av certifikat. Mer information finns i omsadmin-skriptets fullständiga utdata. |
| 33 | Det gick inte att skapa metaconfiguration för omsconfig. Fil a [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) med information från utdata. |
| 34 | Skript för Metaconfiguration-generering saknas. Gör om onboarding med `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` . |

## <a name="enable-debug-logging"></a>Aktivera fel söknings loggning
### <a name="oms-output-plugin-debug"></a>Fel sökning av OMS output-plugin
 Det går att använda plugin-bara loggnings nivåer för att ange olika loggnings nivåer för indata och utdata. Om du vill ange en annan loggnings nivå för OMS-utdata redigerar du den allmänna agent konfigurationen på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .  

 I OMS-utdata-plugin-programmet, innan konfigurations filen har slutförts, ändrar du `log_level` egenskapen från `info` till `debug` :

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Med fel söknings loggning kan du se batch-överföringar till Azure Monitor avgränsade med typ, antal data objekt och tids åtgång för att skicka:

*Exempel på fel söknings aktive rad logg:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Utförlig utdata
I stället för att använda OMS-utdata-plugin-programmet kan du också mata ut data objekt direkt till `stdout` , som visas i logg filen Log Analytics agent för Linux.

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`Kommentera ut plugin-programmet för OMS-utdata i Log Analytics allmän agent konfigurations fil genom att lägga till ett framför `#` varje rad:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Under plugin-programmet för utdata tar du bort kommentaren till följande avsnitt genom att ta bort framför `#` varje rad:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problem: det går inte att ansluta via proxy till Azure Monitor

### <a name="probable-causes"></a>Troliga orsaker
* Proxyn som angavs vid onboarding var felaktig
* Tjänst slut punkterna Azure Monitor och Azure Automation ingår inte i den godkända listan i ditt data Center 

### <a name="resolution"></a>Lösning
1. Återaktivering till Azure Monitor med Log Analytics-agenten för Linux med hjälp av följande kommando med alternativet `-v` aktiverat. Den tillåter utförlig utmatning av agenten som ansluter via proxyservern till Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Granska [proxyinställningarna](agent-manage.md#update-proxy-settings) för att kontrol lera att du har konfigurerat agenten korrekt för att kommunicera via en proxyserver.    

3. Kontrol lera att de slut punkter som beskrivs i listan över Azure Monitor [nätverks brand Väggs krav](./log-analytics-agent.md#firewall-requirements) har lagts till korrekt i listan över tillåtna. Om du använder Azure Automation länkas de nödvändiga stegen för nätverks konfigurationen ovan också.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problem: du får ett 403-fel vid försök att publicera

### <a name="probable-causes"></a>Troliga orsaker
* Datum och tid är felaktigt på Linux-servern 
* Den arbetsyte-ID och den arbets ytans nyckel som används är inte rätt

### <a name="resolution"></a>Lösning

1. Kontrol lera tiden på Linux-servern med kommandots datum. Om tiden är +/-15 minuter från aktuell tid, Miss lyckas onboarding. Korrigera uppdateringen för datum och/eller tidszon för Linux-servern. 
2. Kontrol lera att du har installerat den senaste versionen av Log Analytics agent för Linux.  Den senaste versionen meddelar nu dig om tids skevningen orsakar onboarding-felet.
3. Återställ med rätt arbetsyte-ID och arbets ytans nyckel efter installations anvisningarna tidigare i den här artikeln.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problem: du ser ett 500-och 404-fel i logg filen direkt efter onboarding
Detta är ett känt problem som uppstår vid första uppladdning av Linux-data till en Log Analytics-arbetsyta. Detta påverkar inte data som skickas eller tjänstens erfarenhet.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problem: du ser omiagent med 100% processor

### <a name="probable-causes"></a>Troliga orsaker
En regression i NSS-PEM Package [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) orsakade ett allvarligt prestanda problem, men vi har sett upp mycket i RedHat/CentOS 7. x-distributioner. Mer information om det här problemet finns i följande dokumentation: bugg [1667121-prestanda regression i](https://bugzilla.redhat.com/show_bug.cgi?id=1667121)lib-sväng.

Prestanda relaterade buggar sker inte hela tiden och de är mycket svåra att återskapa. Om du upplever det här problemet med omiagent bör du använda skriptet omiHighCPUDiagnostics.sh som samlar in stack spårningen för omiagent när en viss tröskel överskrids.

1. Hämta skriptet <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Kör diagnostik i 24 timmar med 30% processor tröskel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstacken kommer att dumpas i omiagent_trace-filen, om du märker många NSS-funktions anrop följer du lösnings stegen nedan.

### <a name="resolution-step-by-step"></a>Lösning (steg för steg)

1. Uppgradera NSS-PEM-paketet till [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Om NSS-PEM inte är tillgänglig för uppgradering (de används huvudsakligen på CentOS), nedgradera du sväng nedåt till 7.29.0-46. Om du av misstag kör "yum update" uppgraderas spiralen till 7.29.0-51 och problemet inträffar igen. <br/>
`sudo yum downgrade curl libcurl`

3. Starta om OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problem: du ser inte vidarebefordrade syslog-meddelanden 

### <a name="probable-causes"></a>Troliga orsaker
* Konfigurationen som tillämpas på Linux-servern tillåter inte insamling av skickade anläggningar och/eller logg nivåer
* Syslog vidarebefordras inte korrekt till Linux-servern
* Antalet meddelanden som vidarebefordras per sekund är för bra för den grundläggande konfigurationen av Log Analytics agent för Linux att hantera

### <a name="resolution"></a>Lösning
* Kontrol lera att konfigurationen i Log Analytics arbets ytan för syslog har alla funktioner och rätt logg nivåer. Granska [Konfigurera syslog-samling i Azure Portal](data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Verifiera att de inbyggda daemonarna för syslog-meddelanden ( `rsyslog` , `syslog-ng` ) kan ta emot vidarebefordrade meddelanden
* Kontrol lera brand Väggs inställningarna på syslog-servern för att säkerställa att meddelanden inte blockeras
* Simulera ett syslog-meddelande för att Log Analytics med `logger` kommandot
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problem: du tar emot errno-adressen som redan används i omsagent-logg filen
Om du ser `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` i omsagent. log.

### <a name="probable-causes"></a>Troliga orsaker
Det här felet indikerar att LAD (Linux Diagnostic Extension) är installerat sida vid sida med Log Analytics Linux VM-tillägget och att den använder samma port för syslog-datainsamling som omsagent.

### <a name="resolution"></a>Lösning
1. Som rot kör du följande kommandon (Observera att 25224 är ett exempel och det är möjligt att i din miljö ser du ett annat port nummer som används av LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Sedan måste du redigera rätt `rsyslogd` eller `syslog_ng` config-fil och ändra den lad-relaterade konfigurationen för att skriva till port 25229.

2. Om den virtuella datorn körs `rsyslogd` är filen som ska ändras: `/etc/rsyslog.d/95-omsagent.conf` (om den finns, annars `/etc/rsyslog` ). Om den virtuella datorn körs `syslog_ng` är filen som ska ändras: `/etc/syslog-ng/syslog-ng.conf` .
3. Starta om omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart` .
4. Starta om syslog-tjänsten.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problem: du kan inte avinstallera omsagent med rensnings alternativet

### <a name="probable-causes"></a>Troliga orsaker

* Linux-diagnostiskt tillägg är installerat
* Linux diagnostiskt tillägg har installerats och avinstallerats, men du ser fortfarande ett fel om att omsagent används av mdsd och inte kan tas bort.

### <a name="resolution"></a>Lösning
1. Avinstallera Linux Diagnostic-tillägget (LAD).
2. Ta bort filer för Linux-diagnostik från datorn om de finns på följande plats: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` och `/var/opt/microsoft/omsagent/LAD/` .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problem: du kan inte se data i någon nagios-data 

### <a name="probable-causes"></a>Troliga orsaker
* Omsagent-användaren har inte behörighet att läsa från nagios-logg filen
* Nagios-källan och-filtret har inte kommenterats från omsagent. conf-filen

### <a name="resolution"></a>Lösning
1. Lägg till omsagent-användare för att läsa från nagios-filen genom att följa dessa [anvisningar](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. I Log Analytics agent för allmän konfigurations fil för Linux på `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` , se till att **både** nagios-källan och-filtret är avkommenterade.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problem: du ser inga Linux-data 

### <a name="probable-causes"></a>Troliga orsaker
* Det gick inte att registrera till Azure Monitor
* Anslutningen till Azure Monitor blockeras
* Den virtuella datorn har startats om
* OMI-paketet uppgraderades manuellt till en senare version jämfört med vad som har installerats av Log Analytics agent för Linux-paket
* OMI är fryst, blockerar OMS-agenten
* Det gick *inte att hitta* ett fel i `omsconfig.log` logg filen för DSC-resursens logg klass
* Log Analytics agent för data säkerhets kopie ras
* Det *finns ingen aktuell konfiguration för DSC-loggar. Kör Start-DscConfiguration kommando med parametern-Path för att ange en konfigurations fil och skapa en aktuell konfiguration först.* i `omsconfig.log` logg filen, men det finns inga logg meddelanden om `PerformRequiredConfigurationChecks` åtgärder.

### <a name="resolution"></a>Lösning
1. Installera alla beroenden som det granskade paketet.
2. Kontrol lera om onboarding till Azure Monitor lyckades genom att kontrol lera om följande fil finns: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Om det inte var det kan du återanvända kommando rads [anvisningarna](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)för omsadmin.sh.
4. Om du använder en proxyserver, kontrollerar du fel söknings stegen ovan.
5. I vissa Azure-distributions system startar inte Omid OMI server daemon efter att den virtuella datorn har startats om. Detta leder till att inte ser gransknings-, ChangeTracking-eller UpdateManagement-relaterade data. Lösningen är att starta OMI-servern manuellt genom att köra `sudo /opt/omi/bin/service_control restart` .
6. När OMI-paketet har uppgraderats manuellt till en nyare version måste det startas om manuellt för att Log Analytics agenten ska fortsätta att fungera. Det här steget krävs för vissa distributioner där OMI Server inte startar automatiskt när den har uppgraderats. Kör `sudo /opt/omi/bin/service_control restart` för att starta om OMI.
* I vissa situationer kan OMI bli fryst. OMS-agenten kan ange ett blockerat tillstånd som väntar på OMI, vilket blockerar all data insamling. Processen för OMS-agenten körs, men det kommer inte att finnas någon aktivitet, och det finns inga nya logg rader (till exempel skickade pulsslag) i `omsagent.log` . Starta om OMI med `sudo /opt/omi/bin/service_control restart` för att återställa agenten.
7. Om det inte finns något fel i DSC-resurs *klassen* i omsconfig. log kör du `sudo /opt/omi/bin/service_control restart` .
8. I vissa fall, när Log Analytics agent för Linux inte kan kommunicera med Azure Monitor, säkerhets kopie ras data på agenten till den fulla buffertstorleken: 50 MB. Agenten ska startas om genom att köra följande kommando `/opt/microsoft/omsagent/bin/service_control restart` .

    >[!NOTE]
    >Det här problemet har åtgärd ATS i agent version 1.1.0-28 eller senare
    >

* Om `omsconfig.log` logg filen inte indikerar att `PerformRequiredConfigurationChecks` åtgärder körs regelbundet på systemet kan det uppstå problem med cron-jobbet/-tjänsten. Se till att cron-jobbet finns under `/etc/cron.d/OMSConsistencyInvoker` . Om det behövs kör du följande kommandon för att skapa cron-jobbet:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Kontrol lera också att cron-tjänsten körs. Du kan använda `service cron status` med Debian, Ubuntu, SUSE eller `service crond status` med RHEL, CentOS Oracle Linux för att kontrol lera status för den här tjänsten. Om tjänsten inte finns kan du installera binärfilerna och starta tjänsten med hjälp av följande:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problem: när du konfigurerar samling från portalen för syslog-eller Linux-prestandaräknare tillämpas inte inställningarna

### <a name="probable-causes"></a>Troliga orsaker
* Log Analytics agent för Linux har inte valt den senaste konfigurationen
* De ändrade inställningarna i portalen tillämpades inte

### <a name="resolution"></a>Lösning
**Bakgrund:** `omsconfig` är Log Analytics agent för konfigurations agenten för Linux som söker efter ny portal konfiguration var femte minut. Den här konfigurationen tillämpas sedan på Log Analytics agent för Linux-konfigurationsfiler som finns på/etc/opt/Microsoft/omsagent/conf/omsagent.conf.

* I vissa fall kanske Log Analytics agenten för konfigurations agenten för Linux inte kan kommunicera med Portal konfigurations tjänsten, vilket innebär att den senaste konfigurationen inte tillämpas.
  1. Kontrol lera att `omsconfig` agenten har installerats genom att köra `dpkg --list omsconfig` eller `rpm -qi omsconfig` .  Om den inte är installerad installerar du om den senaste versionen av Log Analytics agent för Linux.

  2. Kontrol lera att `omsconfig` agenten kan kommunicera med Azure Monitor genom att köra följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Det här kommandot returnerar konfigurationen som agenten tar emot från tjänsten, inklusive syslog-inställningar, Linux-prestandaräknare och anpassade loggar. Om det här kommandot Miss lyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problem: du ser inte några anpassade loggdata 

### <a name="probable-causes"></a>Troliga orsaker
* Det gick inte att registrera till Azure Monitor.
* Inställningen **tillämpa följande konfiguration på mina Linux-servrar** har inte valts.
* omsconfig har inte valt den senaste anpassade logg konfigurationen från tjänsten.
* Log Analytics-agenten för Linux-användare `omsagent` kan inte komma åt den anpassade loggen på grund av behörigheter eller inte hittas.  Följande fel kan visas:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Känt problem med ett tävlings villkor som åtgärd ATS i Log Analytics agent för Linux-version 1.1.0 – 217

### <a name="resolution"></a>Lösning
1. Det gick att verifiera att registreringen till Azure Monitor lyckades genom att kontrol lera om följande fil finns: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Om inte, antingen:  

  1. Återpublicera med hjälp av kommando rads [anvisningarna](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)för omsadmin.sh.
  2. Under **Avancerade inställningar** i Azure Portal kontrollerar du att inställningen **tillämpa följande konfiguration på mina Linux-servrar** är aktive rad.  

2. Kontrol lera att `omsconfig` agenten kan kommunicera med Azure Monitor genom att köra följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Det här kommandot returnerar konfigurationen som agenten tar emot från tjänsten, inklusive syslog-inställningar, Linux-prestandaräknare och anpassade loggar. Om det här kommandot Miss lyckas kör du följande kommando `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` . Det här kommandot tvingar omsconfig-agenten att prata med Azure Monitor och hämta den senaste konfigurationen.

**Bakgrund:** I stället för Log Analytics-agenten för Linux som körs som privilegie rad användare `root` , körs agenten som `omsagent` användaren. I de flesta fall måste explicit behörighet beviljas till den här användaren för att vissa filer ska kunna läsas. Om du vill bevilja behörighet till `omsagent` användare kör du följande kommandon:

1. Lägg till `omsagent` användaren i en speciell grupp `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Bevilja Universal Read-åtkomst till den begärda filen `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Det finns ett känt problem med ett tävlings tillstånd med Log Analytics agent för Linux tidigare versioner än 1.1.0-217. När du har uppdaterat till den senaste agenten kör du följande kommando för att hämta den senaste versionen av plugin-programmet för utdata `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` .

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problem: du försöker att återpublicera till en ny arbets yta
När du försöker återställa en agent till en ny arbets yta måste Log Analytics agent-konfigurationen rensas före återregistrering. Om du vill rensa upp den gamla konfigurationen från agenten kör du Shell-paketet med `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Eller

```
sudo sh ./onboard_agent.sh --purge
```

Du kan fortsätta att publicera igen när du har använt `--purge` alternativet

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics agent-tillägg i Azure Portal har marker ATS med ett felaktigt tillstånd: etableringen misslyckades

### <a name="probable-causes"></a>Troliga orsaker
* Log Analytics agent har tagits bort från operativ systemet
* Log Analytics Agent-tjänsten är avstängd, inaktive rad eller inte konfigurerad

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.
1. Ta bort tillägget från Azure Portal.
2. Installera agenten genom att följa [anvisningarna](../vm/quick-collect-linux-computer.md).
3. Starta om agenten genom att köra följande kommando: `sudo /opt/microsoft/omsagent/bin/service_control restart` .
* Vänta några minuter och etablerings statusen ändras till **etableringen har slutförts**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problem: uppgraderingen av Log Analytics-agenten på begäran

### <a name="probable-causes"></a>Troliga orsaker

Log Analytics agent-paket på värden är inaktuella.

### <a name="resolution"></a>Lösning 
Utför följande steg för att åtgärda problemet.

1. Sök efter den senaste versionen på [sidan](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Hämta installations skript (1.4.2-124 som exempel version):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Uppgradera paketen genom att köra `sudo sh ./omsagent-*.universal.x64.sh --upgrade` .