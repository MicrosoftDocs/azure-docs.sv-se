---
title: Lösning för övervakning av behållare i Azure Monitor | Microsoft Docs
description: Lösningen för övervakning av behållare i Azure Monitor hjälper dig att visa och hantera dina Docker-och Windows-behållarobjekt på en enda plats.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: 59f81103e72be6c0f1b629981fdb53c8467000a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037429"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Lösning för övervakning av behållare i Azure Monitor

![Behållare symbol](./media/containers/containers-symbol.png)

Den här artikeln beskriver hur du konfigurerar och använder övervaknings lösningen för behållare i Azure Monitor, som hjälper dig att visa och hantera dina Docker-och Windows-behållarobjekt på en enda plats. Docker är ett system för programvirtualisering som används för att skapa behållare som automatiserar program distribution till IT-infrastrukturen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Lösningen visar vilka behållare som körs, vilken behållar avbildning de kör och var behållare körs. Du kan visa detaljerad gransknings information som visar kommandon som används med behållare. Du kan också felsöka behållare genom att visa och söka i centraliserade loggar utan att behöva fjärrans luta till Docker eller Windows-värdar. Du hittar behållare som kan vara störningar och förbruka överskott av resurser på en värd. Du kan också Visa centraliserad processor, minne, lagring och nätverks användning och prestanda information för behållare. På datorer som kör Windows kan du centralisera och jämföra loggar från Windows Server, Hyper-V och Docker-behållare. Lösningen stöder följande behållar dirigeringar:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Om du har behållare som distribuerats i [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)rekommenderar vi att du aktiverar både [Service Fabric lösning](../../service-fabric/service-fabric-diagnostics-oms-setup.md) och den här lösningen för att ta med övervakning av kluster händelser. Innan du aktiverar Service Fabric-lösningen kan du läsa mer i [använda Service Fabric-lösningen](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) för att förstå vad den innehåller och hur du använder den.

Om du är intresse rad av att övervaka prestanda för dina arbets belastningar som distribueras till Kubernetes-miljöer som finns i Azure Kubernetes service (AKS), se [övervaka Azure Kubernetes-tjänsten](../containers/container-insights-overview.md). Övervaknings lösningen för behållare stöder inte övervakning av den plattformen.  

Följande diagram visar relationerna mellan olika behållar värdar och agenter med Azure Monitor.

![Behållare diagram](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>System krav och plattformar som stöds

Innan du börjar läser du följande information för att kontrol lera att du uppfyller kraven.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Lösning för övervakning av behållare för Docker Orchestrator och OS-plattform

I följande tabell beskrivs stödet för Docker-dirigering och operativ system övervakning av behållar inventering, prestanda och loggar med Azure Monitor.   

|Docker-dirigering | ACS | Linux | Windows | Container<br>Inventering | Bild<br>Inventering | Nod<br>Inventering | Container<br>Prestanda | Container<br>Händelse | Händelse<br>Loggas | Container<br>Loggas |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Tjänst<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat öppen<br>Skift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>installations | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-Server<br>installations | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Docker-versioner som stöds i Linux

- Docker 1,11 till 1,13
- Docker CE och EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributioner som stöds som container värdar

- Ubuntu 14,04 LTS och 16,04 LTS
- Kärnor (stabilt)
- Amazon Linux-2016.09.0
- openSUSE 13,2
- openSUSE skottår 42,2
- CentOS 7,2 och 7,3
- SLES 12
- RHEL 7,2 och 7,3
- Red Hat OpenShift container Platform (OCP) 3,4 och 3,5
- ACS Mesosphere DC/OS 1.7.3 till 1.8.8
- ACS Kubernetes-1.4.5 till 1,6
    - Kubernetes-händelser, Kubernetes-inventering och container processer stöds bara med version 1.4.1-45 och senare av Log Analytics agent för Linux
- ACS Docker-Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Windows operativ system som stöds

- Windows Server 2016
- Windows 10-jubileums version (Professional eller Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versioner som stöds i Windows

- Docker 1,12 och 1,13
- Docker 17.03.0 och senare

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen

Använd följande information för att installera och konfigurera lösningen.

1. Lägg till lösningen för övervakning av behållare till din Log Analytics-arbetsyta från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till övervaknings lösningar från Lösningsgalleriet](../insights/solutions.md).

2. Installera och Använd Docker med en Log Analytics-agent. Du kan använda följande metoder för att konfigurera agenten baserat på operativ system och Docker Orchestrator.
   - För fristående värdar:
     - På Linux-operativsystem som stöds installerar du och kör Docker och installerar och konfigurerar sedan [Log Analytics-agenten för Linux](../vm/quick-collect-linux-computer.md).  
     - I Core kan du inte köra Log Analytics agent för Linux. I stället kör du en behållar version av Log Analytics agent för Linux. Granska Linux container-värdar inklusive Core-eller Azure Government Linux container-värdar, inklusive Core, om du arbetar med behållare i Azure Government molnet.
     - På Windows Server 2016 och Windows 10 installerar du Docker-motorn och-klienten och ansluter sedan en agent för att samla in information och skicka den till Azure Monitor. Granska [Installera och konfigurera Windows container hosts](#install-and-configure-windows-container-hosts) om du har en Windows-miljö.
   - För Docker multi-Host-dirigering:
     - Om du har en Red Hat OpenShift-miljö kan du läsa Konfigurera en Log Analytics agent för Red Hat OpenShift.
     - Om du har ett Kubernetes-kluster med hjälp av Azure Container Service:
       - Granska [Konfigurera en Log Analytics Linux-Agent för Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Granska [Konfigurera en Log Analytics Windows-agent för Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Granska Använd Helm för att distribuera Log Analytics agent på Linux-Kubernetes.
     - Om du har ett Azure Container Service DC/OS-kluster kan du läsa mer i [övervaka ett Azure Container Service DC/OS-kluster med Azure Monitor](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Om du har en Docker Swarm läges miljö kan du läsa mer i Konfigurera en Log Analytics-agent för Docker Swarm.
     - Om du har ett Service Fabric kluster kan du läsa mer på [Monitor-behållare med Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Läs avsnittet [Docker-motorn i Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) om du vill ha mer information om hur du installerar och konfigurerar Docker-motorer på datorer som kör Windows.

> [!IMPORTANT]
> Docker måste köras **innan** du installerar [Log Analytics agent för Linux](../vm/quick-collect-linux-computer.md) på behållar värdarna. Om du redan har installerat agenten innan du installerar Docker måste du installera om Log Analytics-agenten för Linux. Mer information om Docker finns på [Docker-webbplatsen](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Installera och konfigurera Linux container hosts

När du har installerat docker använder du följande inställningar för din behållar värd för att konfigurera agenten för användning med Docker. Först behöver du ditt Log Analytics arbetsyte-ID och nyckel, som du hittar i Azure Portal. I arbets ytan klickar du på **Snabbstart**  >  **datorer** för att visa ditt **arbetsyte-ID** och **primär nyckel**.  Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

**För alla Linux container-värdar förutom Core:**

- Mer information och anvisningar om hur du installerar Log Analytics agent för Linux finns i [Översikt över Log Analytics-agenten](../agents/log-analytics-agent.md).

**För alla Linux container-värdar inklusive Core:**

Starta den behållare som du vill övervaka. Ändra och Använd följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**För alla Azure Government Linux container-värdar inklusive Core:**

Starta den behållare som du vill övervaka. Ändra och Använd följande exempel:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Växla från att använda en installerad Linux-Agent till en i en behållare**

Om du tidigare använde den direkt installerade agenten och vill använda en agent som körs i en behållare måste du först ta bort den Log Analytics agenten för Linux. Mer information om hur du avinstallerar agenten finns i Avinstallera [Log Analytics agent för Linux](../vm/quick-collect-linux-computer.md) .  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurera en Log Analytics agent för Docker Swarm

Du kan köra Log Analytics agenten som en global tjänst i Docker Swarm. Använd följande information för att skapa en Log Analytics agent tjänst. Du måste ange Log Analytics arbetsyte-ID och primär nyckel.

- Kör följande på huvud-noden.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Skydda hemligheter för Docker Swarm

För Docker Swarm, när hemligheten för arbetsyte-ID och primär nyckel har skapats, använder du följande information för att skapa din hemliga information.

1. Kör följande på huvud-noden.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Verifiera att hemligheter har skapats på rätt sätt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Kör följande kommando för att montera hemligheterna till den behållare som Log Analytics agenten.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurera en Log Analytics agent för Red Hat OpenShift

Det finns tre sätt att lägga till Log Analytics agent i Red Hat OpenShift för att börja samla in övervaknings data för behållare.

* [Installera Log Analytics agent för Linux](../vm/quick-collect-linux-computer.md) direkt på varje OpenShift-nod  
* [Aktivera Log Analytics VM-tillägg](../vm/quick-collect-azurevm.md) på varje OpenShift-nod i Azure  
* Installera Log Analytics agenten som OpenShift daemon-uppsättning  

I det här avsnittet beskriver vi de steg som krävs för att installera Log Analytics agenten som OpenShift daemon-uppsättning.  

1. Logga in på noden OpenShift-huvud och kopiera yaml-filen [OCP-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) från GitHub till huvudnoden och ändra värdet med ditt Log Analytics arbetsyte-ID och med din primära nyckel.
2. Kör följande kommandon för att skapa ett projekt för Azure Monitor och ange användar kontot.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Kör följande för att distribuera daemon-uppsättningen:

    `oc create -f ocp-omsagent.yaml`

4. Kontrol lera att den är konfigurerad och fungerar som den ska genom att skriva följande:

    `oc describe daemonset omsagent`  

    och resultatet bör likna följande:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Om du vill använda hemligheter för att skydda din Log Analytics arbetsyte-ID och primär nyckel när du använder yaml-filen för Log Analytics agentens daemon-uppsättning utför du följande steg.

1. Logga in på noden OpenShift-huvud och kopiera yaml-filen [OCP-DS-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) och Secret genering script [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) från GitHub.  Det här skriptet genererar hemligheter yaml-filen för Log Analytics arbetsyte-ID och primär nyckel för att skydda din hemliga information.  
2. Kör följande kommandon för att skapa ett projekt för Azure Monitor och ange användar kontot. Skriptet för hemligt skapande frågar efter ditt Log Analytics arbetsyte-ID `<WSID>` och primär nyckel `<KEY>` och när det är klart skapas filen OCP-Secret. yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Distribuera den hemliga filen genom att köra följande:

    `oc create -f ocp-secret.yaml`

4. Verifiera distributionen genom att köra följande:

    `oc describe secret omsagent-secret`  

    och resultatet bör likna följande:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Distribuera Log Analytics agent daemon – ange yaml-filen genom att köra följande:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Verifiera distributionen genom att köra följande:

    `oc describe ds oms`

    och resultatet bör likna följande:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurera en Log Analytics Linux-Agent för Kubernetes

För Kubernetes använder du ett skript för att generera filen hemligheter yaml för ditt arbetsyte-ID och primär nyckel för att installera Log Analytics agent för Linux. På sidan [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) finns det filer som du kan använda med eller utan din hemliga information.

- Standard Log Analytics agenten för Linux-DaemonSet saknar hemlig information (omsagent. yaml)
- Log Analytics agent för Linux DaemonSet yaml-filen använder hemlig information (omsagent-DS-hemligheter. yaml) med skript för hemliga generationer för att generera filen hemligheter yaml (omsagentsecret. yaml).

Du kan välja att skapa omsagent-DaemonSets med eller utan hemligheter.

**Standard OMSagent DaemonSet yaml File utan hemligheter**

- För standard Log Analytics agent DaemonSet yaml-filen ersätter du `<WSID>` och `<KEY>` till din WSID och nyckel. Kopiera filen till huvud-noden och kör följande:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standard OMSagent DaemonSet yaml-fil med hemligheter**

1. Om du vill använda Log Analytics agent DaemonSet med hemlig information skapar du hemligheterna först.
    1. Kopiera skriptet och den hemliga mallfilen och se till att de finns i samma katalog.
        - Skript för hemligt skapande – secret-gen.sh
        - hemlig mall – Secret-Template. yaml
    2. Kör skriptet, som i följande exempel. Skriptet frågar efter Log Analytics arbetsyte-ID och primär nyckel och när du har angett dem skapar skriptet en hemlig yaml-fil så att du kan köra den.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Skapa hemligheterna Pod genom att köra följande:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Verifiera genom att köra följande:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Utdata bör likna följande:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Utdata bör likna följande:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Skapa din omsagent-daemon-uppsättning genom att köra ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Kontrol lera att Log Analytics agent-DaemonSet körs, ungefär så här:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

För Kubernetes använder du ett skript för att generera filen hemligheter yaml för arbetsyte-ID och primär nyckel för den Log Analytics agenten för Linux. Använd följande exempel information med [omsagent yaml-filen](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) för att skydda din hemliga information.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurera en Log Analytics Windows-agent för Kubernetes

För Windows-Kubernetes använder du ett skript för att generera filen hemligheter yaml för ditt arbetsyte-ID och primär nyckel för att installera Log Analytics agenten. På sidan [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) finns det filer som du kan använda med din hemliga information.  Du måste installera Log Analytics agent separat för huvud-och agent-noderna.  

1. Om du vill använda Log Analytics agent DaemonSet med hemlig information på huvud noden loggar du in och skapar hemligheterna först.
    1. Kopiera skriptet och den hemliga mallfilen och se till att de finns i samma katalog.
        - Skript för hemligt skapande – secret-gen.sh
        - hemlig mall – Secret-Template. yaml

    2. Kör skriptet, som i följande exempel. Skriptet frågar efter Log Analytics arbetsyte-ID och primär nyckel och när du har angett dem skapar skriptet en hemlig yaml-fil så att du kan köra den.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Skapa din omsagent-daemon-uppsättning genom att köra ```kubectl create -f omsagentsecret.yaml```
    4. Kontrol lera genom att köra följande:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Utdata bör likna följande:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Skapa din omsagent-daemon-uppsättning genom att köra ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Kontrol lera att Log Analytics agent-DaemonSet körs, ungefär så här:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Om du vill installera agenten på arbetsnoden, som kör Windows, följer du stegen i avsnittet [Installera och konfigurera Windows container hosts](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Använda Helm för att distribuera Log Analytics-agenten på Linux Kubernetes

Utför följande steg för att använda Helm för att distribuera Log Analytics agent i Linux Kubernetes-miljön.

1. Skapa din omsagent-daemon-uppsättning genom att köra ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Resultatet ser ut ungefär så här:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Du kan kontrol lera status för omsagent genom att köra: ```helm status "omsagent"``` så ser utdata ut ungefär så här:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Mer information finns i Helm- [diagrammet för container lösning](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installera och konfigurera värdar för Windows-behållare

Använd informationen i avsnittet för att installera och konfigurera Windows-behållar värdar.

#### <a name="preparation-before-installing-windows-agents"></a>Förberedelser innan du installerar Windows-agenter

Innan du installerar agenter på datorer som kör Windows måste du konfigurera Docker-tjänsten. Konfigurationen tillåter att Windows-agenten eller Azure Monitor virtuella dator tillägget använder Docker TCP-socket så att agenterna kan komma åt Docker-daemonen via fjärr anslutning och samla in data för övervakning.

##### <a name="to-configure-the-docker-service"></a>Konfigurera Docker-tjänsten  

Utför följande PowerShell-kommandon för att aktivera TCP-pipe och namngiven pipe för Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Mer information om konfiguration av Docker daemon som används med Windows-behållare finns i [Docker-motorn i Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Installera Windows-agenter

Om du vill aktivera övervakning av Windows-och Hyper-V-behållare installerar du Microsoft Monitoring Agent (MMA) på Windows-datorer som är behållar värdar. För datorer som kör Windows i din lokala miljö, se [Anslut Windows-datorer till Azure Monitor](../agents/agent-windows.md). För virtuella datorer som körs i Azure kan du ansluta dem till Azure Monitor med hjälp av [tillägget för virtuell dator](../vm/quick-collect-azurevm.md).

Du kan övervaka Windows-behållare som körs på Service Fabric. Det är dock bara [virtuella datorer som körs i Azure](../vm/quick-collect-azurevm.md) och [datorer som kör Windows i din lokala miljö](../agents/agent-windows.md) som stöds för Service Fabric.

Du kan kontrol lera att lösningen för övervakning av behållare är korrekt inställd för Windows. Du kan kontrol lera om hanterings paketet har laddats ned korrekt genom att leta efter *ContainerManagement.xxx*. Filerna bör finnas i mappen C:\Program Files\Microsoft Monitoring Agent\Agent\Health service State\Management Packs.

## <a name="solution-components"></a>Lösningskomponenter

Från Azure Portal navigerar du till *Lösningsgalleriet* och lägger till **lösningen för övervakning av behållare**. Om du använder Windows-agenter installeras följande hanterings paket på varje dator med en agent när du lägger till den här lösningen. Det krävs ingen konfiguration eller underhåll för hanterings paketet.

- *ContainerManagement.xxx* installerat i C:\Program Files\Microsoft Monitoring Agent\Agent\Health service State\Management Packs

## <a name="container-data-collection-details"></a>Information om container data insamling

Lösningen för övervakning av behållare samlar in olika prestanda mått och loggdata från behållar värdar och behållare med hjälp av agenter som du aktiverar.

Data samlas in var tredje minut av följande agent typer.

- [Log Analytics agent för Linux](../vm/quick-collect-linux-computer.md)
- [Windows-agent](../agents/agent-windows.md)
- [Log Analytics VM-tillägg](../vm/quick-collect-azurevm.md)

### <a name="container-records"></a>Container poster

I följande tabell visas exempel på poster som samlas in av lösningen för övervakning av behållare och de data typer som visas i loggs öknings resultat.

| Datatyp | Datatyp i loggs ökning | Fält |
| --- | --- | --- |
| Prestanda för värdar och behållare | `Perf` | Dator, ObjectName, CounterName &#40;% processor tid, disk läsningar MB, disk skrivningar MB, minnes användning MB, nätverks mottagning byte, nätverks sändning byte, processor användning SEC, nätverks&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Container inventering | `ContainerInventory` | TimeGenerated, dator, container namn, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventering av behållar avbildning | `ContainerImageInventory` | TimeGenerated, dator, avbildning, ImageTag, ImageSize, VirtualSize, igång, pausad, stoppad, misslyckad, SourceSystem, ImageID, TotalContainer |
| Behållar logg | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, behållar namn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Container Service-logg | `ContainerServiceLog`  | TimeGenerated, dator, TimeOfCommand, avbildning, kommando, SourceSystem, ContainerID |
| Inventering av container nod | `ContainerNodeInventory_CL`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes inventering | `KubePodInventory_CL` | TimeGenerated, dator, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Container process | `ContainerProcess_CL` | TimeGenerated, dator, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-händelser | `KubeEvents_CL` | TimeGenerated, dator, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, meddelande |

Etiketter som läggs till i *PodLabel* data typer är dina egna anpassade etiketter. De Lägg till PodLabel-etiketter som visas i tabellen är exempel. Därför `PodLabel_deployment_s` `PodLabel_deploymentconfig_s` `PodLabel_docker_registry_s` skiljer sig därför i din miljös data uppsättning och på ett allmänt sätt `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Övervaka containrar
När du har aktiverat lösningen i Azure Portal, visar **behållaren behållare** sammanfattnings information om dina behållar värdar och behållarna som körs i värdarna.

![Behållare panel](./media/containers/containers-title.png)

Panelen visar en översikt över hur många behållare du har i miljön och om de har misslyckats, körs eller stoppats.

### <a name="using-the-containers-dashboard"></a>Använda behållarens instrument panel

Klicka på **behållare** panelen. Där ser du vyer ordnade efter:

- **Container Events** – visar status för behållare och datorer med misslyckade behållare.
- **Container loggar** – visar ett diagram över behållar loggfiler som genererats med tiden och en lista över datorer med det högsta antalet loggfiler.
- **Kubernetes-händelser** – visar ett diagram över Kubernetes-händelser som genererats över tid och en lista över orsakerna till varför poddar genererade händelserna. *Den här data uppsättningen används endast i Linux-miljöer.*
- **Kubernetes namespace Inventory** -visar antalet namn områden och poddar och visar deras hierarki. *Den här data uppsättningen används endast i Linux-miljöer.*
- **Container Node Inventory** -visar antalet Dirigerings typer som används på behållar noder/-värdar. Datorns noder/värdar visas också i antal behållare. *Den här data uppsättningen används endast i Linux-miljöer.*
- **Inventering av behållar avbildningar** – visar det totala antalet behållar avbildningar som används och antalet avbildnings typer. Antalet bilder anges också av avbildnings tag gen.
- **Container status** – visar det totala antalet behållar-noder/värddatorer som kör behållare. Datorer listas också efter antalet värdar som körs.
- **Container process** – visar ett linje diagram över behållar processer som körs med tiden. Behållare visas också genom att köra kommandot/process i behållare. *Den här data uppsättningen används endast i Linux-miljöer.*
- **CPU-prestanda för behållare** – visar ett linje diagram över den genomsnittliga CPU-användningen över tid för dator-noder/-värdar. Visar även datorns noder/värdar baserat på genomsnittlig processor användning.
- **Minnes prestanda för behållare** – visar ett linje diagram över minnes användningen över tid. Visar även dator minnes användning baserat på instans namn.
- **Dator prestanda** – visar linje diagram över procent av CPU-prestanda över tid, procent av minnes användning över tid och megabyte ledigt disk utrymme över tid. Du kan hovra över vilken linje som helst i ett diagram om du vill visa mer information.

Varje område på instrument panelen är en visuell representation av en sökning som körs på insamlade data.

![Skärm bild som visar en instrument panel för att Visa insamlade data. ](./media/containers/containers-dash01.png)

![Behållare för behållare](./media/containers/containers-dash02.png)

I avsnittet **behållar status** klickar du på det övre fältet, som du ser nedan.

![Behållare status](./media/containers/containers-status.png)

Log Analytics öppnas och visar information om tillstånd för dina behållare.

![Log Analytics för behållare](./media/containers/containers-log-search.png)

Härifrån kan du redigera Sök frågan för att ändra den för att hitta den information som du är intresse rad av. Mer information om logg frågor finns [i logg frågor i Azure Monitor](../logs/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Felsöka genom att hitta en misslyckad behållare

Log Analytics markerar en behållare som **misslyckad** om den har avslut ATS med en slutkod som inte är noll. Du kan se en översikt över felen och felen i miljön i avsnittet **misslyckade behållare** .

### <a name="to-find-failed-containers"></a>Hitta misslyckade behållare

1. Klicka på avsnittet **container status** .  
   ![behållare status](./media/containers/containers-status.png)
2. Log Analytics öppnar och visar statusen för dina behållare, ungefär så här:  
   ![behållarens tillstånd](./media/containers/containers-log-search.png)
3. Expandera raden som misslyckades och klicka på + för att lägga till dess kriterier i frågan. Kommentera sedan upp raden sammanfatta i frågan.
   ![Skärm bild som visar raden som ska kommenteras ut.](./media/containers/containers-state-failed-select.png)  
1. Kör frågan och expandera sedan en rad i resultatet för att visa bild-ID: t.  
   ![Skärm bild som visar hur du visar bild-ID.](./media/containers/containers-state-failed.png)  
1. Skriv följande i logg frågan. `ContainerImageInventory | where ImageID == <ImageID>` för att se information om bilden, till exempel bild storlek och antal stoppade och misslyckade avbildningar.  
   ![misslyckade behållare](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Fråga efter loggar för behållar data

När du felsöker ett särskilt fel kan det hjälpa dig att se var det sker i din miljö. Med följande logg typer kan du skapa frågor för att returnera den information som du vill ha.

- **ContainerImageInventory** – Använd den här typen när du försöker hitta information som ordnats efter bild och för att visa bild information, till exempel bild-ID eller storlekar.
- **ContainerInventory** – Använd den här typen när du vill ha information om behållarens plats, vad deras namn är och vilka bilder de kör.
- **ContainerLog** – Använd den här typen om du vill hitta information om fel logg och poster.
- **ContainerNodeInventory_CL**  Använd den här typen när du vill ha information om värd/nod där behållare är bosatta. Den innehåller Docker version, Orchestration-typ, lagring och nätverksinformation.
- **ContainerProcess_CL** Använd den här typen för att snabbt se processen som körs i behållaren.
- **ContainerServiceLog** – Använd den här typen när du försöker hitta information om gransknings loggen för Docker daemon, t. ex. Starta, stoppa, ta bort eller pull-kommandon.
- **KubeEvents_CL**  Använd den här typen om du vill se Kubernetes-händelserna.
- **KubePodInventory_CL**  Använd den här typen när du vill förstå information om klustrets hierarki.

### <a name="to-query-logs-for-container-data"></a>Så här frågar du efter loggar för behållar data

* Välj en bild som du vet har misslyckats nyligen och hitta fel loggarna för den. Börja med att hitta ett behållar namn som kör avbildningen med en **ContainerInventory** -sökning. Sök till exempel efter `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Sök efter Ubuntu-behållare](./media/containers/search-ubuntu.png)

  Expandera alla rader i resultaten om du vill visa information om behållaren.

## <a name="example-log-queries"></a>Exempel på logg frågor

Det är ofta användbart att bygga frågor som börjar med ett exempel eller två och sedan ändra dem så att de passar din miljö. Som start punkt kan du experimentera med **exempel fråge** områdena längst till höger på lösnings sidan för att hjälpa dig att bygga mer avancerade frågor.

![Behållare frågor](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Spara logg frågor

Att spara frågor är en standard funktion i Azure Monitor. Genom att spara dem har du de som du har hittat användbart för framtida bruk.

När du har skapat en fråga som du tycker är användbar sparar du den genom att klicka på **Favoriter** överst på sidan loggs ökning. Sedan kan du enkelt komma åt den senare från sidan **min instrument panel** .

## <a name="next-steps"></a>Nästa steg

[Fråga loggar](../logs/log-query-overview.md) om du vill visa detaljerade data poster för behållare.

