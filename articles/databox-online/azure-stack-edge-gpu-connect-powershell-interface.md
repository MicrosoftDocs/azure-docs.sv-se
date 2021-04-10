---
title: Ansluta till och hantera Microsoft Azure Stack Edge Pro GPU-enhet via Windows PowerShell-gränssnittet | Microsoft Docs
description: Beskriver hur du ansluter till och sedan hanterar Azure Stack Edge Pro GPU via Windows PowerShell-gränssnittet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562169"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Hantera en Azure Stack Edge Pro GPU-enhet via Windows PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Med Azure Stack Edge Pro-lösningen kan du bearbeta data och skicka dem via nätverket till Azure. I den här artikeln beskrivs några konfigurations-och hanterings uppgifter för din Azure Stack Edge Pro-enhet. Du kan använda Azure Portal, det lokala webb gränssnittet eller Windows PowerShell-gränssnittet för att hantera enheten.

Den här artikeln fokuserar på hur du kan ansluta till PowerShell-gränssnittet på enheten och de uppgifter som du kan utföra med hjälp av det här gränssnittet. 


## <a name="connect-to-the-powershell-interface"></a>Ansluta till PowerShell-gränssnittet

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Skapa ett support paket

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Visa enhets information
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Visa information om GPU-drivrutinen

Om Compute-rollen har kon figurer ATS på din enhet kan du också hämta information om GPU-drivrutinen via PowerShell-gränssnittet. 

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-HcsGpuNvidiaSmi` för att hämta information om GPU-drivrutinen för enheten.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Anteckna driv rutins informationen från exempel resultatet av denna cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Aktivera multi-process service (MPS)

En multi-process service (MPS) på NVIDIA GPU: er tillhandahåller en mekanism där GPU: er kan delas av flera jobb, där varje jobb har allokerat en viss procent andel av GPU-resurserna. MPS är en förhands gransknings funktion på din Azure Stack Edge Pro GPU-enhet. Följ dessa steg om du vill aktivera MPS på enheten:

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Återställa din enhet

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Hämta beräknings loggar

Om Compute-rollen har kon figurer ATS på din enhet kan du också hämta beräknings loggarna via PowerShell-gränssnittet.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Använd `Get-AzureDataBoxEdgeComputeRoleLogs` för att hämta beräknings loggarna för din enhet.

    I följande exempel visas användningen av denna cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Här följer en beskrivning av parametrarna som används för cmdleten:
    - `Path`: Ange en nätverks Sök väg till den resurs där du vill skapa beräknings logg paketet.
    - `Credential`: Ange användar namnet för nätverks resursen. När du kör den här cmdleten måste du ange resurs lösen ordet.
    - `FullLogCollection`: Den här parametern säkerställer att logg paketet innehåller alla beräknings loggar. Som standard innehåller logg paketet bara en delmängd av loggarna.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Ändra Kubernetes-pod och -tjänstundernät

Som standard använder Kubernetes på din Azure Stack Edge-enhet undernät 172.27.0.0/16 och 172.28.0.0/16 för Pod respektive tjänst. Om dessa undernät redan används i nätverket kan du köra `Set-HcsKubeClusterNetworkInfo` cmdleten för att ändra dessa undernät.

Du vill utföra den här konfigurationen innan du konfigurerar beräkning från Azure Portal när Kubernetes-klustret skapas i det här steget.

1. [Anslut till enhetens PowerShell-gränssnitt](#connect-to-the-powershell-interface).
1. Kör följande från PowerShell-gränssnittet på enheten:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Ersätt <subnet details> med det under näts intervall som du vill använda. 

1. När du har kört det här kommandot kan du använda `Get-HcsKubeClusterNetworkInfo` kommandot för att kontrol lera att Pod-och tjänst under näten har ändrats.

Här är ett exempel på utdata för kommandot.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Felsöka Kubernetes problem som rör IoT Edge

Innan du börjar måste du ha:

- Compute-nätverket har kon figurer ATS. Se [självstudie: Konfigurera nätverk för Azure Stack Edge Pro med GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Compute-rollen har kon figurer ATS på enheten.
    
På en Azure Stack Edge Pro-enhet där beräknings rollen har kon figurer ATS kan du felsöka eller övervaka enheten med två olika kommando uppsättningar.

- Använda `iotedge` kommandon. De här kommandona är tillgängliga för grundläggande åtgärder för din enhet.
- Använda `kubectl` kommandon. De här kommandona är tillgängliga för en omfattande uppsättning åtgärder för din enhet.

Om du vill köra någon av ovanstående kommando uppsättningar måste du [ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Använda `iotedge` kommandon

Om du vill se en lista över tillgängliga kommandon [ansluter du till PowerShell-gränssnittet](#connect-to-the-powershell-interface) och använder `iotedge` funktionen.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

Följande tabell innehåller en kort beskrivning av de kommandon som är tillgängliga för `iotedge` :

|command  |Beskrivning |
|---------|---------|
|`list`     | Lista med moduler         |
|`logs`     | Hämta loggarna för en modul        |
|`restart`     | Stoppa och starta om en modul         |

#### <a name="list-all-iot-edge-modules"></a>Visa alla IoT Edge moduler

Om du vill visa alla moduler som körs på enheten använder du `iotedge list` kommandot.

Här är ett exempel på utdata från kommandot. Det här kommandot visar alla moduler, tillhör ande konfiguration och de externa IP-adresser som är kopplade till modulerna. Du kan till exempel komma åt **webserver** -appen på `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Starta om moduler

Du kan använda `list` kommandot för att visa en lista över alla moduler som körs på enheten. Identifiera sedan namnet på den modul som du vill starta om och Använd det med `restart` kommandot.

Här är ett exempel på hur du startar om en modul. Utifrån beskrivningen av hur länge modulen körs för kan du se att `cuda-sample1` startades om.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Hämta Modulnamn

Använd `logs` kommandot för att hämta loggar för alla IoT Edge modul som körs på enheten. 

Om det uppstod ett fel när behållar avbildningen skulle skapas eller när avbildningen hämtades kör du `logs edgeagent` . `edgeagent` är den IoT Edge runtime-behållare som ansvarar för att tillhandahålla andra behållare. Eftersom `logs edgeagent` dumpar alla loggar, är ett bra sätt att se de senaste felen att använda alternativet `--tail ` 0. 

Här är ett exempel på utdata.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Använda kubectl-kommandon

På en Azure Stack Edge Pro-enhet som har beräknings rollen konfigurerad, är alla `kubectl` kommandon tillgängliga för att övervaka eller felsöka moduler. Om du vill se en lista över tillgängliga kommandon kan du köra `kubectl --help` från kommando fönstret.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

En fullständig lista över `kubectl` kommandona finns på [ `kubectl` fusklapp](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Så här hämtar du IP-adress eller modul som exponeras utanför Kubernetes-kluster

Kör följande kommando för att hämta IP-adressen för en belastnings Utjämnings tjänst eller moduler som exponeras utanför Kubernetes:

`kubectl get svc -n iotedge`

Följande är ett exempel på utdata från alla tjänster eller moduler som exponeras utanför Kubernetes-klustret. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
IP-adressen i den externa IP-kolumnen motsvarar den externa slut punkten för tjänsten eller modulen. Du kan också [Hämta den externa IP-adressen i Kubernetes-instrumentpanelen](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Kontrol lera om modulen har distribuerats korrekt

Compute-moduler är behållare som har en affärs logik implementerad. En Kubernetes-Pod kan ha flera behållare som kör. 

Du kan kontrol lera om en Compute-modul har distribuerats genom att ansluta till PowerShell-gränssnittet på enheten.
Kör `get pods` kommandot och kontrol lera om behållaren (som motsvarar Compute-modulen) körs.

Kör följande kommando för att hämta en lista över alla poddar som körs i en angiven namnrymd:

`get pods -n <namespace>`

Kör följande kommando för att kontrol lera de moduler som distribueras via IoT Edge:

`get pods -n iotedge`

Följande är ett exempel på utdata från alla poddar som körs i `iotedge` namn området.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

Status **status** anger att alla poddar i namn området körs och att det är **klart** anger hur många behållare som distribueras i en pod. I föregående exempel körs alla poddar och alla moduler som distribueras i var och en av poddar körs. 

Kör följande kommando för att kontrol lera de moduler som distribueras via Azure Arc:

`get pods -n azure-arc`

Alternativt kan du [ansluta till Kubernetes-instrumentpanelen för att se IoT Edge-eller Azure Arc-distributioner](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Du kan köra följande kommando för att få en mer utförlig utdata av en specifik Pod för en angiven namnrymd:

`kubectl describe pod <pod name> -n <namespace>` 

Exempel på utdata visas här.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Hämta behållar loggar

Hämta loggarna för en modul genom att köra följande kommando från PowerShell-gränssnittet på enheten:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Eftersom `all-containers` flagga dumpar alla loggar för alla behållare, är ett bra sätt att se de senaste felen att använda alternativet `--tail 10` .

Följande är ett exempel på utdata. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Ändra gränser för minne och processorer för Kubernetes-arbetsnod

Gör så här om du vill ändra minnes-eller processor gränser för Kubernetes Worker-noden:

1. [Anslut till enhetens PowerShell-gränssnitt](#connect-to-the-powershell-interface).
1. Kör följande kommando för att hämta de aktuella resurserna för Worker-noden och roll alternativen:

    `Get-AzureDataBoxEdgeRole`

    Här är ett exempel på utdata. Anteckna värdena för `Name` och `Compute` under `Resources` avsnitt. `MemoryInBytes` och `ProcessorCount` anger de nuvarande tilldelade värdena minne och antal processorer för Kubernetes Worker-noden.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Om du vill ändra värdena för minne och processorer för arbetsnoden kör du följande kommando:

    Set-AzureDataBoxEdgeRoleCompute-namn <Name value from the output of Get-AzureDataBoxEdgeRole> -minne <Value in Bytes> -ProcessorCount <nej. av kärnor>

    Här är ett exempel på utdata. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Följ dessa rikt linjer när du ändrar minnes-och processor användning.

- Standard minnet är 25% av enhets specifikationen.
- Standard antalet processorer är 30% av enhets specifikationen.
- När du ändrar värdena för minne och antal processorer rekommenderar vi att du varierar värdena mellan 15% och 60% av enhets minnet och antalet processorer. 
- Vi rekommenderar en övre gräns på 60% så att det finns tillräckligt med resurser för system komponenter. 

## <a name="connect-to-bmc"></a>Anslut till BMC

BMC (Baseboard Management Controller) används för att fjärrövervaka och hantera enheten. I det här avsnittet beskrivs de cmdletar som kan användas för att hantera BMC-konfiguration. Innan du kör någon av dessa cmdlets [ansluter du till PowerShell-gränssnittet på enheten](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Använd denna cmdlet för att hämta egenskaperna för nätverks konfiguration för BMC, till exempel,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` . 
    
    Här är exempel på utdata:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: Du kan använda denna cmdlet på följande två sätt.

    - Använd cmdleten för att aktivera eller inaktivera DHCP-konfiguration för BMC med lämpligt värde för `UseDhcp` parameter. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Här är exempel på utdata: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Använd denna cmdlet för att konfigurera den statiska konfigurationen för BMC. Du kan ange värden för `IPv4Address` , `IPv4Gateway` och `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>&quot; -IPv4Gateway &quot;<IPv4 address of the gateway>&quot; -IPv4SubnetMask &quot;<IPv4 address for the subnet mask>"
        ```        
        
        Här är exempel på utdata: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Använd denna cmdlet för att ändra BMC-lösenordet för `EdgeUser` . Användar namnet- `EdgeUser` -är Skift läges känsligt.

    Här är exempel på utdata: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Avsluta fjärrsessionen

Stäng PowerShell-fönstret om du vill avsluta PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

- Distribuera [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) i Azure Portal.