---
title: Installera Log Analytics-agenten på Linux-datorer
description: Den här artikeln beskriver hur du ansluter Linux-datorer som finns i andra moln eller lokalt till Azure Monitor med Log Analytics-agenten för Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725934"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Installera Log Analytics-agenten på Linux-datorer
Den här artikeln innehåller information om hur du installerar Log Analytics-agenten på Linux-datorer med hjälp av följande metoder:

* [Installera agenten för Linux med hjälp av ett omslutningsskript](#install-the-agent-using-wrapper-script) som finns på GitHub. Det här är den rekommenderade metoden för att installera och uppgradera agenten när datorn är ansluten till Internet, direkt eller via en proxyserver.
* [Ladda ned och installera agenten](#install-the-agent-manually) manuellt. Detta krävs när Linux-datorn inte har åtkomst till Internet och kommer att kommunicera med Azure Monitor eller Azure Automation via [Log Analytics-gatewayen](./gateway.md). 

>[!IMPORTANT]
> Installationsmetoderna som beskrivs i den här artikeln används vanligtvis för virtuella datorer lokalt eller i andra moln. Se [Installationsalternativ för](./log-analytics-agent.md#installation-options) mer effektiva alternativ som du kan använda för virtuella Azure-datorer.



## <a name="supported-operating-systems"></a>Operativsystem som stöds

Se [Översikt över Azure Monitor för en](agents-overview.md#supported-operating-systems) lista över Linux-distributioner som stöds av Log Analytics-agenten.

>[!NOTE]
>OpenSSL 1.1.0 stöds endast på x86_x64-plattformar (64-bitars) och OpenSSL tidigare än 1.x stöds inte på någon plattform.

>[!NOTE]
>Det finns inte stöd för att köra Log Analytics Linux-agenten i containrar. Om du behöver övervaka containrar kan du använda [övervakningslösningen för containrar](../containers/containers.md) för Docker-värdar eller [Container insights](../containers/container-insights-overview.md) för Kubernetes.

Från och med versioner som släpps efter augusti 2018 gör vi följande ändringar i vår supportmodell:  

* Endast serverversioner stöds, inte klientversioner.  
* Fokusera på stöd för någon av [de Azure Linux-godkända distributionerna.](../../virtual-machines/linux/endorsed-distros.md) Observera att det kan uppstå en fördröjning mellan att en ny distribution/version är Azure Linux-godkänd och att den stöds för Log Analytics Linux-agenten.
* Alla mindre versioner stöds för varje huvudversion som listas.
* Versioner som har passerat tillverkarens supportdatum stöds inte.
* Stöder endast VM-avbildningar; containrar, inte ens de som härleds från officiella distributioners avbildningar, stöds inte.
* Nya versioner av AMI stöds inte.  
* Endast versioner som kör OpenSSL 1.x som standard stöds.

>[!NOTE]
>Om du använder en distribution eller version som inte stöds för närvarande och inte överensstämmer med vår supportmodell, rekommenderar vi att du förför den här lagringsplatsen, med bekräftelse på att Microsoft Support inte kommer att ge hjälp med förked agentversioner.

### <a name="python-requirement"></a>Python-krav

Från och med agentversion 1.13.27 stöder Linux-agenten både Python 2 och 3. Vi rekommenderar alltid att du använder den senaste agenten. 

Om du använder en äldre version av agenten måste den virtuella datorn använda Python 2 som standard. Om den virtuella datorn använder en distribution som inte innehåller Python 2 som standard måste du installera den. Följande exempelkommandon installerar Python 2 på olika distributioner.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - Suse: `zypper install -y python2`

Den körbara python2-filen måste ha aliaset *python*. Följande är en metod som du kan använda för att ange det här aliaset:

1. Kör följande kommando för att ta bort alla befintliga alias.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Kör följande kommando för att skapa aliaset.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Linux-härdning som stöds
OMS-agenten har begränsat anpassnings- och härdningsstöd för Linux.

Följande stöds för närvarande: 
- Fips

Följande stöds inte:
- Cis
- Selinux

Stöd för CIS- och SELINUX-härdning planeras för [Azure Monitoring Agent](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview). Ytterligare härdning och anpassningsmetoder stöds inte eller planeras inte för OMS-agenten.  

## <a name="agent-prerequisites"></a>Agentkrav

I följande tabell visas de paket som krävs [för Linux-distributioner som](#supported-operating-systems) stöds och som agenten ska installeras på.

|Nödvändigt paket |Description |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0.x eller 1.1.x |
|Curl | cURL-webbklient | 7.15.5 |
|Python | | 2.7 eller 3.6+
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Syslog-standarddaemonen i version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-versionen (sysklog) stöds inte för syslog-händelseinsamling. För att samla in syslog-data från den här versionen av dessa distributioner måste rsyslog-daemonen installeras och konfigureras för att ersätta sysklog.

## <a name="network-requirements"></a>Nätverkskrav
Se [Översikt över Log Analytics-agenten](./log-analytics-agent.md#network-requirements) för nätverkskrav för Linux-agenten.

## <a name="agent-install-package"></a>Installationspaketet för agenten

Log Analytics-agenten för Linux består av flera paket. Versionen innehåller följande paket, som är tillgängliga genom att köra shell-paketet med `--extract` parametern :

**Paket** | **Version** | **Beskrivning**
----------- | ----------- | --------------
omsagent | 1.13.9 | Log Analytics-agenten för Linux
omsconfig | 1.1.1 | Konfigurationsagent för Log Analytics-agenten
Omi | 1.6.4 | Open Management Infrastructure (OMI) – en enkel CIM-server. *Observera att OMI kräver rotåtkomst för att köra ett cron-jobb som krävs för att tjänsten ska fungera*
Scx | 1.6.4 | OMI CIM-leverantörer för prestandamått för operativsystem
apache-cimprov | 1.0.1 | Apache HTTP Server-prestandaövervakningsprovider för OMI. Installeras endast om Apache HTTP Server identifieras.
mysql-cimprov | 1.0.1 | MySQL Server-provider för prestandaövervakning för OMI. Installeras endast om MySQL/MariaDB-server identifieras.
docker-cimprov | 1.0.0 | Docker-provider för OMI. Installeras endast om Docker identifieras.

### <a name="agent-installation-details"></a>Agentinstallationsinformation

När du har installerat Log Analytics-agenten för Linux-paket tillämpas följande ytterligare konfigurationsändringar för hela systemet. Dessa artefakter tas bort när omsagent-paketet avinstalleras.

* En icke-privilegierad användare med `omsagent` namnet: skapas. Daemonen körs under den här autentiseringssuppgifter. 
* En sudoers *include-fil* skapas i `/etc/sudoers.d/omsagent` . Detta ger behörighet `omsagent` att starta om syslog- och omsagent-daemons. Om sudo *include-direktiv* inte stöds i den installerade versionen av sudo skrivs dessa poster till `/etc/sudoers` .
* Syslog-konfigurationen ändras för att vidarebefordra en delmängd av händelserna till agenten. Mer information finns i [Konfigurera Syslog-datainsamling.](data-sources-syslog.md)

På en övervakad Linux-dator visas agenten som `omsagent` . `omsconfig` är Log Analytics-agenten för Linux-konfigurationsagenten som söker efter ny konfiguration på portalsidan var femte minut. Den nya och uppdaterade konfigurationen tillämpas på agentkonfigurationsfilerna som finns i `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Installera agenten med hjälp av omslutningsskript

Följande steg konfigurerar installationen av agenten för Log Analytics i Azure och Azure Government-molnet med hjälp av omslutningsskriptet för Linux-datorer som kan kommunicera direkt eller via en proxyserver för att ladda ned agenten som finns på GitHub och installera agenten.  

Om Linux-datorn behöver kommunicera via en proxyserver till Log Analytics kan den här konfigurationen anges på kommandoraden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]` . Protokollegenskapen accepterar eller och  proxyhost-egenskapen accepterar ett fullständigt domännamn eller en `http` `https` IP-adress för proxyservern.  

Exempelvis: `https://proxy01.contoso.com:30443`

Om autentisering krävs i båda fallen måste du ange användarnamn och lösenord. Exempelvis: `https://user01:password@proxy01.contoso.com:30443`

1. Om du vill konfigurera Linux-datorn att ansluta till en Log Analytics-arbetsyta kör du följande kommando som tillhandahåller arbetsytans ID och primärnyckel. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller `-p` proxyparametern och exempelsyntax när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn så att den ansluter till Log Analytics-arbetsytan Azure Government molnet kör du följande kommando med arbetsytans ID och primärnyckel som du kopierade tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller `-p` proxyparametern och exempelsyntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Installera agenten manuellt

Log Analytics-agenten för Linux finns i ett själv extraherande och installerbart gränssnittsskriptpaket. Det här paketet innehåller Debian- och RPM-paket för var och en av agentkomponenterna och kan installeras direkt eller extraheras för att hämta de enskilda paketen. Ett paket tillhandahålls för x64 och ett för x86-arkitekturer. 

> [!NOTE]
> För virtuella Azure-datorer rekommenderar vi att du installerar agenten på dem med hjälp av [Azure Log Analytics VM-tillägget](../../virtual-machines/extensions/oms-linux.md) för Linux. 

1. [Ladda](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) ned och överför lämpligt paket (x64 eller x86) till din virtuella Linux-dator eller fysiska dator med hjälp av scp/sftp.

2. Installera paketet med hjälp av `--install` argumentet . Om du vill publicera till en Log Analytics-arbetsyta under installationen anger du `-w <WorkspaceID>` `-s <workspaceKey>` parametrarna och som kopierades tidigare.

    >[!NOTE]
    >Du måste använda argumentet om beroende paket `--upgrade` som omi, scx, omsconfig eller deras äldre versioner är installerade, vilket skulle vara fallet om System Center Operations Manager-agenten för Linux redan är installerad. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Om du vill konfigurera Linux-agenten så att den installerar och ansluter till en Log Analytics-arbetsyta via en Log Analytics-gateway kör du följande kommando med parametrarna proxy, arbetsyte-ID och arbetsytenyckel. Den här konfigurationen kan anges på kommandoraden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]` . Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en IP-adress för Log Analytics-gatewayservern.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Om autentisering krävs måste du ange användarnamn och lösenord. Exempel: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Om du vill konfigurera Linux-datorn så att den ansluter till en Log Analytics-arbetsyta i Azure Government molnet kör du följande kommando med arbetsytans ID och primärnyckel som du kopierade tidigare.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Om du vill installera agentpaketen och konfigurera dem för att rapportera till en specifik Log Analytics-arbetsyta vid ett senare tillfälle kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Om du vill extrahera agentpaketen från paketet utan att installera agenten kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version

Uppgradering från en tidigare version, från och med version 1.0.0-47, stöds i varje version. Utför installationen med `--upgrade` parametern för att uppgradera alla komponenter i agenten till den senaste versionen.

## <a name="cache-information"></a>Cacheinformation
Data från Log Analytics-agenten för Linux cachelagras på den lokala datorn på *%STATE_DIR_WS%/out_oms_common*.buffer* innan de skickas till Azure Monitor. Anpassade loggdata buffrar i *%STATE_DIR_WS%/out_oms_blob*.buffer*. Sökvägen kan vara annorlunda för vissa [lösningar och datatyper.](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)

Agenten försöker ladda upp var 20:e sekund. Om den misslyckas väntar den en exponentiellt ökande tidslängd tills den lyckas: 30 sekunder före det andra försöket, 60 sekunder före det tredje, 120 sekunder ... och så vidare upp till högst 16 minuter mellan återförsöken tills den ansluter igen. Agenten försöker igen upp till 6 gånger för ett visst data segment innan den tas bort och flyttas till nästa. Detta fortsätter tills agenten kan ladda upp igen. Det innebär att data kan buffra upp till cirka 30 minuter innan de tas bort.

Standardstorleken för cacheminnet är 10 MB, men kan ändras i [filen omsagent.conf.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)


## <a name="next-steps"></a>Nästa steg

- Läs [Hantera och underhålla Log Analytics-agenten](agent-manage.md) för Windows och Linux om du vill veta mer om hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Läs [Felsöka Linux-agenten](agent-linux-troubleshoot.md) om du stöter på problem när du installerar eller hanterar agenten.
