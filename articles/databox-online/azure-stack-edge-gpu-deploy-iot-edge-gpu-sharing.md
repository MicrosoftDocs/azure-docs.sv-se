---
title: Distribuera IoT Edge arbets belastning med GPU-delning på Azure Stack Edge Pro GPU-enhet
description: Beskriver hur du kan distribuera en GPU-delad arbets belastning via IoT Edge på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565405"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>Distribuera en IoT Edge arbets belastning med GPU-delning på din Azure Stack Edge Pro

Den här artikeln beskriver hur arbets belastningar i behållare kan dela GPU: er på din Azure Stack Edge Pro GPU-enhet. Metoden omfattar att aktivera multi-process service (MPS) och sedan ange GPU-arbetsbelastningar via en IoT Edge distribution. 

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har åtkomst till en Azure Stack Edge Pro GPU-enhet som är [aktive rad](azure-stack-edge-gpu-deploy-activate.md) och har en [konfigurerad beräkning](azure-stack-edge-gpu-deploy-configure-compute.md). Du har [KUBERNETES API-slutpunkten](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) och du har lagt till den här slut punkten till `hosts` filen på klienten som kommer att ha åtkomst till enheten.

1. Du har åtkomst till ett klient system med ett [operativ system som stöds](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Om du använder en Windows-klient ska systemet köra PowerShell 5,0 eller senare för att få åtkomst till enheten.

1. Spara följande distribution `json` på det lokala systemet. Du använder information från den här filen för att köra IoT Edge-distributionen. Den här distributionen baseras på [enkla CUDA behållare](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) som är allmänt tillgängliga från NVIDIA. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>Verifiera GPU-drivrutin, CUDA-version

Det första steget är att kontrol lera att enheten kör nödvändiga GPU-drivrutiner och CUDA-versioner.

1. [Anslut till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Kör följande kommando:

    `Get-HcsGpuNvidiaSmi`

1. Anteckna GPU-versionen och CUDA-versionen på enheten i NVIDIA SMI-utdata. Om du kör Azure Stack Edge 2102-programvara motsvarar den här versionen följande driv rutins versioner:

    - Driv rutins version för GPU: 460.32.03
    - CUDA-version: 11,2
    
    Här är ett exempel på utdata:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Se till att den här sessionen är öppen eftersom du kommer att använda den för att visa Nvidia SMI-utdata i artikeln.


## <a name="deploy-without-context-sharing"></a>Distribuera utan kontext delning

Nu kan du distribuera ett program på enheten när multi-process-tjänsten inte körs och det inte finns någon kontext delning. Distributionen sker via Azure Portal i `iotedge` namn området som finns på enheten.

### <a name="create-user-in-iot-edge-namespace"></a>Skapa användare i IoT Edge namnrymd

Först ska du skapa en användare som kommer att ansluta till `iotedge` namn området. IoT Edge modulerna distribueras i namn området iotedge. Mer information finns i [Kubernetes-namnområden på enheten](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Följ de här stegen för att skapa en användare och ge användaren åtkomst till `iotedge` namn området. 

1. [Anslut till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Skapa en ny användare i `iotedge` namn området. Kör följande kommando:

    `New-HcsKubernetesUser -UserName <user name>`

    Här är ett exempel på utdata:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Kopiera utdata som visas som oformaterad text. Spara utdata som en *konfigurations* fil (utan tillägg) i `.kube` mappen i din användar profil på din lokala dator, till exempel `C:\Users\<username>\.kube` . 

1. Bevilja den användare du skapade, åtkomst till `iotedge` namn området. Kör följande kommando:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Här är ett exempel på utdata:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Detaljerade anvisningar finns i [ansluta till och hantera ett Kubernetes-kluster via kubectl på din Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Distribuera moduler via portalen

Distribuera IoT Edge moduler via Azure Portal. Du distribuerar offentligt tillgängliga NVIDIA CUDA-exempelprogram som kör simulering av n-Body. Mer information finns i [simulering av N-Body](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Kontrol lera att tjänsten IoT Edge körs på enheten.

    ![IoT Edge tjänsten körs.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Välj panelen IoT Edge i den högra rutan. Gå till **IoT Edge > egenskaper**. I den högra rutan väljer du den IoT Hub resurs som är kopplad till din enhet.

    ![Visa egenskaper.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. I IoT Hub resurs går du till **Automatisk enhets hantering > IoT Edge**. I den högra rutan väljer du den IoT Edge enhet som är kopplad till enheten.

    ![Gå till IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Välj **Ange moduler**.

    ![Gå till ange moduler.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Välj **+ Lägg till > + IoT Edge modul**.

    ![Lägg till IoT Edge-modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. På fliken **Modulnamn** anger du **IoT Edge-modulens namn** och **bild-URI**. Ställ in **bild hämtnings principen** **på Skapa**.

    ![Inställningar för modulen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. På fliken **miljövariabler** anger du **NVIDIA_VISIBLE_DEVICES** som **0**.

    ![Miljövariabler.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. På fliken **behållare skapa alternativ** anger du följande alternativ:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    Alternativen visas på följande sätt:

    ![Alternativ för att skapa behållare.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Välj **Lägg till**.

1. Modulen som du har lagt till ska visas som **körs**. 

    ![Granska och skapa distribution.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Upprepa alla steg för att lägga till en modul som du följde när du lade till den första modulen. I det här exemplet anger du namnet på modulen som `cuda-sample2` . 

    ![Inställningar för modulen för den andra modulen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Använd samma miljö variabel som båda modulerna delar samma GPU.

    ![Miljö variabel för den andra modulen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Använd samma behållare skapa alternativ som du angav för den första modulen och välj **Lägg till**.

    ![Behållare för att skapa alternativ för andra moduler.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. På sidan **Ange moduler** väljer du **Granska + skapa** och väljer sedan **skapa**. 

    ![Granska och skapa en andra distribution.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. **Körnings status** för båda modulerna bör nu visas som **körs**.  

    ![status för andra distributioner.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Övervaka arbets belastnings distribution

1. Öppna en ny PowerShell-session.

1. Visar en lista över poddar som körs i `iotedge` namn området. Kör följande kommando:

    `kubectl get pods -n iotedge`   

    Här är ett exempel på utdata:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Det finns två poddar `cuda-sample1-97c494d7f-lnmns` och `cuda-sample2-d9f6c4688-2rld9` körs på enheten.

1. Även om båda behållarna kör simuleringen i n-Body, se GPU-användningen från NVIDIA SMI-utdata. Gå till PowerShell-gränssnittet för enheten och kör `Get-HcsGpuNvidiaSmi` .

    Här är ett exempel på utdata när båda behållarna kör simuleringen av n-Body:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Som du kan se finns det två behållare som kör med en simulering av n-Body på GPU 0. Du kan också visa motsvarande minnes användning.

1. När simuleringen är klar visar NVIDIA SMI-utdata att det inte finns några processer som körs på enheten.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. När simuleringen av n-Body har slutförts kan du visa loggarna för att förstå distributionens information och den tid som krävs för att simuleringen ska slutföras. 

    Här är ett exempel på utdata från den första behållaren:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Här är ett exempel på utdata från den andra behållaren:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Stoppa modulen distribution. I IoT Hub resurs för din enhet:
    1. Gå till **Automatisk enhets distribution > IoT Edge**. Välj den IoT Edge enhet som motsvarar enheten.

    1. Gå till **Ange moduler** och välj en modul. 

        ![Välj Ange modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. Välj en modul på fliken **moduler** .
    
        ![Välj en modul.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  På fliken **Modulnamn** anger du **önskad status** till stoppad. Välj **Uppdatera**.

        ![Ändra inställningar för modulen.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Upprepa stegen för att stoppa den andra modulen som distribuerats på enheten. Välj **Granska + skapa** och välj sedan **Skapa**. Detta bör uppdatera distributionen.

        ![Granska och skapa uppdaterad distribution.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Sidan uppdatera **uppsättnings moduler** flera gånger. tills modulens **körnings status** visas som **stoppad**.

        ![Kontrol lera distributions status.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Distribuera med kontext delning

Nu kan du distribuera n-Body-simuleringen på två CUDA-behållare när MPS körs på enheten. Först ska du aktivera MPS på enheten.


1. [Anslut till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Kör kommandot för att aktivera MPS på enheten `Start-HcsGpuMPS` .

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Hämta NVIDIA SMI-utdata från PowerShell-gränssnittet på enheten. Du kan se `nvidia-cuda-mps-server` processen eller att MPS-tjänsten körs på enheten.

    Här är ett exempel på utdata:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Distribuera de moduler som du stoppade tidigare. Ange **önskad status** som körs via **set modules**.

    Här är exempel på utdata:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Du kan se att modulerna har distribuerats och körs på enheten.

1. När modulerna distribueras börjar inte simuleringen med n-Body att köras på båda behållarna. Här är exempel på utdata när simuleringen har slutförts på den första behållaren:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    Här är exempel på utdata när simuleringen har slutförts på den andra behållaren:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Hämta NVIDIA SMI-utdata från PowerShell-gränssnittet på enheten när båda behållarna kör simuleringen i n-Body. Här är ett exempel på utdata. Det finns tre processer: `nvidia-cuda-mps-server` processen (typ C) motsvarar MPS-tjänsten och `/tmp/nbody` processerna (typ M + C) motsvarar de n-Body-arbetsbelastningar som distribueras av modulerna. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Nästa steg

- [Distribuera en delad GPU-Kubernetes arbets belastning på din Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
