---
title: Installera Log Analytics-agenten på Linux-datorer
description: Den här artikeln beskriver hur du ansluter Linux-datorer som finns i andra moln eller lokalt för att Azure Monitor med Log Analytics-agenten för Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 5171cefdb82b958ae8148ff63f1daef5f67916c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044965"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Installera Log Analytics-agenten på Linux-datorer
Den här artikeln innehåller information om hur du installerar Log Analytics-agenten på Linux-datorer med följande metoder:

* [Installera agenten för Linux med hjälp av ett omslutnings skript som](#install-the-agent-using-wrapper-script) finns på GitHub. Detta är den rekommenderade metoden för att installera och uppgradera agenten när datorn har anslutning till Internet, direkt eller via en proxyserver.
* [Hämta och installera](#install-the-agent-manually) agenten manuellt. Detta krävs när Linux-datorn inte har åtkomst till Internet och kommer att kommunicera med Azure Monitor eller Azure Automation via [Log Analytics Gateway](./gateway.md). 

>[!IMPORTANT]
> Installations metoderna som beskrivs i den här artikeln används vanligt vis för virtuella datorer lokalt eller i andra moln. Se [installations alternativ](./log-analytics-agent.md#installation-options) för mer effektiva alternativ som du kan använda för Azure Virtual Machines.



## <a name="supported-operating-systems"></a>Operativsystem som stöds

Se [Översikt över Azure Monitor agenter](agents-overview.md#supported-operating-systems) för en lista över Linux-distributioner som stöds av log Analyticss agenten.

>[!NOTE]
>OpenSSL 1.1.0 stöds endast på x86_x64 plattformar (64-bitars) och OpenSSL tidigare än 1. x stöds inte på någon plattform.

>[!NOTE]
>Det finns inte stöd för att köra Log Analytics Linux-agenten i behållare. Om du behöver övervaka behållare kan du använda lösning för [övervakning av behållare](../containers/containers.md) för Docker-värdar eller [behållar insikter](../containers/container-insights-overview.md) för Kubernetes.

Från och med versioner som publicerats efter 2018 augusti gör vi följande ändringar i vår support modell:  

* Endast Server versionerna stöds, inte klienten.  
* Fokusera på en [Azure Linux-distributioner som har bekräftats](../../virtual-machines/linux/endorsed-distros.md). Observera att det kan uppstå en fördröjning mellan en ny distribution/version som är godkänd i Azure Linux och stöds för Log Analytics Linux-agenten.
* Alla mindre versioner stöds för varje huvud version som anges.
* Versioner som har passerat support datumet stöds inte.
* Stöder endast VM-avbildningar. behållare, även de som härletts från officiella distribution-utgivares avbildningar, stöds inte.
* Nya versioner av AMI stöds inte.  
* Endast versioner som kör OpenSSL 1. x stöds som standard.

>[!NOTE]
>Om du använder en distribution eller version som inte stöds och inte justeras till vår support modell, rekommenderar vi att du förlitar dig på den här lagrings platsen, vilket erkänner att Microsoft Support inte kommer att ge hjälp med förgrenade agent versioner.

### <a name="python-requirement"></a>Python-krav

Från och med agent version 1.13.27 kommer Linux-agenten att stödja både python 2 och 3. Vi rekommenderar alltid att du använder den senaste agenten. 

Om du använder en äldre version av agenten måste du använda python 2 som standard på den virtuella datorn. Om den virtuella datorn använder en distribution som inte innehåller python 2 som standard måste du installera den. Följande exempel kommandon kommer att installera python 2 på olika distributioner.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Den körbara filen python2 måste ha ett alias till *python*. Följande är en metod som du kan använda för att ange det här aliaset:

1. Kör följande kommando för att ta bort alla befintliga alias.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Kör följande kommando för att skapa aliaset.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Linux-härdning som stöds
OMS-agenten har begränsat anpassnings stöd för Linux. 

Följande stöds för närvarande: 
- FIPs

Följande beaktas men stöds ännu inte:
- CIS
- SELINUX

Andra härdnings-och anpassnings metoder stöds inte eller planeras inte för OMS-agenten.  

## <a name="agent-prerequisites"></a>Krav för agent

I följande tabell visas de paket som krävs för [Linux-distributioner som stöds](#supported-operating-systems) av-agenten som agenten ska installeras på.

|Nödvändigt paket |Beskrivning |Lägsta version |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotek | 2.5-12 
|Openssl    | OpenSSL-bibliotek | 1.0. x eller 1.1. x |
|Curl | webb klient för sväng | 7.15.5 |
|Python | | 2,7 eller 3,6 +
|Python – ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Antingen rsyslog eller syslog-ng krävs för att samla in syslog-meddelanden. Standard syslog-daemonen på version 5 av Red Hat Enterprise Linux, CentOS och Oracle Linux-version (sysklog) stöds inte för händelse insamling i syslog. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska rsyslog daemon installeras och konfigureras för att ersätta sysklog.

## <a name="network-requirements"></a>Nätverkskrav
Se [Översikt över Log Analytics agent](./log-analytics-agent.md#network-requirements) för Linux-agentens nätverks krav.

## <a name="agent-install-package"></a>Agent installations paket

Log Analytics agenten för Linux består av flera paket. Versions filen innehåller följande paket, som är tillgängliga genom att köra Shell-paketet med `--extract` parametern:

**Paket** | **Version** | **Beskrivning**
----------- | ----------- | --------------
omsagent | 1.13.9 | Log Analytics agent för Linux
omsconfig | 1.1.1 | Konfigurations agent för Log Analytics agent
OMI | 1.6.4 | Open Management Infrastructure (OMI) – en Lightweight CIM-server. *Observera att OMI kräver rot åtkomst för att köra ett cron-jobb som krävs för att tjänsten ska fungera*
SCX | 1.6.4 | OMI CIM-providers för prestanda mått för operativ system
Apache-cimprov | 1.0.1 | Apache HTTP server Performance Monitoring Provider för OMI. Installeras endast om Apache HTTP-servern identifieras.
MySQL-cimprov | 1.0.1 | Provider för prestanda övervakning av MySQL-server för OMI. Installeras endast om MySQL/MariaDB-servern har identifierats.
Docker-cimprov | 1.0.0 | Docker-Provider för OMI. Installeras endast om Docker har identifierats.

### <a name="agent-installation-details"></a>Information om agent installation

När du har installerat Log Analytics agent för Linux-paket tillämpas följande ytterligare konfigurations ändringar i hela systemet. Dessa artefakter tas bort när omsagent-paketet avinstalleras.

* En icke-privilegie rad användare med namnet: `omsagent` har skapats. Daemonen körs under denna autentiseringsuppgift. 
* En sudoers *include* -fil skapas i `/etc/sudoers.d/omsagent` . Detta ger tillstånd `omsagent` att starta om syslog-och omsagent-daemonarna. Om sudo *include* -direktiv inte stöds i den installerade versionen av sudo skrivs dessa poster till `/etc/sudoers` .
* Syslog-konfigurationen ändras för att vidarebefordra en delmängd av händelser till agenten. Mer information finns i [Konfigurera syslog-datainsamling](data-sources-syslog.md).

På en övervakad Linux-dator visas agenten som `omsagent` . `omsconfig` är Log Analytics agent för konfigurations agenten för Linux som söker efter ny portal konfiguration var 5: e minut. Den nya och uppdaterade konfigurationen tillämpas på konfigurationsfilerna för agenten som finns på `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Installera agenten med hjälp av omslutnings skript

Följande steg konfigurerar installationen av agenten för Log Analytics i Azure och Azure Government molnet med hjälp av omslutnings skriptet för Linux-datorer som kan kommunicera direkt eller via en proxyserver för att ladda ned agenten som finns på GitHub och installera agenten.  

Om Linux-datorn behöver kommunicera via en proxyserver till Log Analytics, kan den här konfigurationen anges på kommando raden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]` . *Protokoll* egenskapen accepterar `http` eller `https` , och egenskapen *ProxyHost* accepterar ett fullständigt KVALIFICERAt domän namn eller en IP-adress för proxyservern. 

Exempelvis: `https://proxy01.contoso.com:30443`

Om autentisering krävs i båda fallen måste du ange användar namn och lösen ord. Exempelvis: `https://user01:password@proxy01.contoso.com:30443`

1. Kör följande kommando för att ange arbetsyte-ID och primär nyckel för att konfigurera Linux-datorn för att ansluta till en Log Analytics-arbetsyta. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller proxy- `-p` parametern och exempel-syntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn så att den ansluter till Log Analytics arbets yta i Azure Government molnet kör du följande kommando som tillhandahåller arbetsyte-ID och primär nyckel som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller proxy- `-p` parametern och exempel-syntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Installera agenten manuellt

Log Analytics agenten för Linux finns i ett självextraherande och installerbart gränssnitts skript paket. Paketet innehåller Debian och RPM-paket för varje agent komponent och kan installeras direkt eller extraheras för att hämta de enskilda paketen. Ett paket tillhandahålls för x64 och ett för x86-arkitekturer. 

> [!NOTE]
> För virtuella Azure-datorer rekommenderar vi att du installerar agenten på dem med hjälp av [Azure Log Analytics VM-tillägget](../../virtual-machines/extensions/oms-linux.md) för Linux. 

1. [Hämta](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) och överför lämpligt paket (x64 eller x86) till din virtuella Linux-dator eller fysiska dator med hjälp av SCP/SFTP.

2. Installera paketet med hjälp av `--install` argumentet. Om du vill publicera till en Log Analytics arbets yta under installationen anger du `-w <WorkspaceID>` parametrarna och som du `-s <workspaceKey>` kopierade tidigare.

    >[!NOTE]
    >Du måste använda `--upgrade` argumentet om ett beroende paket som OMI, SCX, omsconfig eller deras äldre versioner är installerade, som skulle vara fallet om System Center Operations Manager-agenten för Linux redan har installerats. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Om du vill konfigurera Linux-agenten att installera och ansluta till en Log Analytics-arbetsyta via en Log Analytics Gateway kör du följande kommando som anger proxy, arbetsyte-ID och arbets ytans nyckel parametrar. Den här konfigurationen kan anges på kommando raden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]` . Egenskapen *ProxyHost* accepterar ett fullständigt kvalificerat domän namn eller en IP-adress för Log Analytics Gateway-servern.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Om autentisering krävs måste du ange användar namn och lösen ord. Exempel: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Om du vill konfigurera Linux-datorn för att ansluta till en Log Analytics arbets yta i Azure Government molnet kör du följande kommando som tillhandahåller arbetsyte-ID och primär nyckel som kopierats tidigare.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Om du vill installera agent paketen och konfigurera det att rapportera till en speciell Log Analytics arbets yta vid ett senare tillfälle kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Om du vill extrahera agent paketen från paketet utan att installera agenten kör du följande kommando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Uppgradera från en tidigare version

Att uppgradera från en tidigare version, från och med version 1.0.0-47, stöds i varje version. Utför installationen med- `--upgrade` parametern för att uppgradera alla komponenter i agenten till den senaste versionen.

## <a name="cache-information"></a>Cachelagra information
Data från Log Analytics agent för Linux cachelagras på den lokala datorn på *% STATE_DIR_WS%/out_oms_common*. Buffer * innan den skickas till Azure Monitor. Anpassade loggdata buffras i *% STATE_DIR_WS%/out_oms_blob*. Buffer *. Sökvägen kan vara olika för vissa [lösningar och data typer](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

Agenten försöker ladda upp var 20: e sekund. Om det Miss lyckas väntar det en exponentiellt ökande tid tills det lyckas: 30 sekunder före det andra försöket, 60 sekunder före den tredje, 120 sekunder... och så på upp till högst 16 minuter mellan återförsök tills anslutningen har upprättats igen. Agenten kommer att försöka igen till 6 gånger för ett angivet data segment innan det tas bort och flyttas till nästa. Detta fortsätter tills agenten kan laddas upp igen. Det innebär att data kan buffras upp till cirka 30 minuter innan de tas bort.

Standardvärdet för cacheminnet är 10 MB men kan ändras i [filen omsagent. conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Nästa steg

- Granska [hantering och underhåll av Log Analytics agent för Windows och Linux](agent-manage.md) för att lära dig hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [fel sökning av Linux-agenten](agent-linux-troubleshoot.md) om det uppstår problem när du installerar eller hanterar agenten.
