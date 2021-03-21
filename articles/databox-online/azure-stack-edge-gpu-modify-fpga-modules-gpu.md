---
title: Ändra IoT Edge moduler på FPGA-enheten som ska köras på Azure Stack Edge Pro GPU-enhet
description: Beskriver vilka ändringar som krävs för befintliga IoT Edge-moduler på befintliga FPGA-enheter som kan köras på din Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 660fbf7cc4dd28c800d8f49fd5d990c99f97c4c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443003"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Kör befintliga IoT Edge moduler från Azure Stack Edge Pro FPGA-enheter på Azure Stack Edge Pro GPU-enhet

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Den här artikeln beskriver de ändringar som krävs för en Docker-baserad IoT Edge-modul som körs på Azure Stack Edge Pro-FPGA så att den kan köras på en Kubernetes IoT Edge plattform på Azure Stack Edge Pro GPU-enhet. 

## <a name="about-iot-edge-implementation"></a>Om IoT Edge implementering 

IoT Edge-implementering skiljer sig på Azure Stack Edge Pro FPGA-enheter jämfört med Azure Stack Edge Pro GPU-enheter. För GPU-enheter används Kubernetes som en värd plattform för IoT Edge. IoT Edge på FPGA-enheter använder en Docker-baserad plattform. IoT Edge Docker-baserad program modell översätts automatiskt till den inbyggda program modellen Kubernetes. Vissa ändringar kan dock fortfarande behövas eftersom bara en liten del av Kubernetes-programmodellen stöds.

Om du migrerar dina arbets belastningar från en FPGA-enhet till en GPU-enhet måste du göra ändringar i de befintliga IoT Edge modulerna för att de ska kunna köras korrekt på Kubernetes-plattformen. Du kan behöva ange lagring, nätverk, resursanvändning och webbproxy-krav på olika sätt. 

## <a name="storage"></a>Storage

Tänk på följande när du anger lagring för IoT Edge moduler.

- Lagring för behållare på Kubernetes anges med volym monteringar.
- Distribution på Kubernetes kan inte ha bindningar för Association av beständiga lagrings vägar eller värd Sök vägar.
    - För beständig lagring använder du `Mounts` med-typ `volume` .
    - För värd Sök vägar använder du `Mounts` med-typ `bind` .
- För IoT Edge på Kubernetes, binder du `Mounts` bara Works för katalog och inte för fil.

#### <a name="example---storage-via-volume-mounts"></a>Exempel – lagring via volym monteringar 

För IoT Edge på Docker används värdsökväg-bindningar för att mappa resurserna på enheten till sökvägar inuti behållaren. Här är behållar skapande alternativen som används på FPGA-enheter:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
För värd Sök vägar för IoT Edge på Kubernetes visas ett exempel på att använda `Mounts` med typ `bind` här:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

För GPU-enheter som kör IoT Edge på Kubernetes används volym monteringar för att ange lagrings utrymme. Om du vill etablera lagring med hjälp av resurser `Mounts.Source` ska värdet vara namnet på den SMB-eller NFS-resurs som etablerades på GPU-enheten. `/home/input`Är sökvägen till den plats där volymen är tillgänglig i behållaren. Här är behållar skapande alternativen som används på GPU-enheterna:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Nätverk

Överväg följande information när du anger nätverk för IoT Edge moduler. 

- `HostPort` specifikation krävs för att exponera en tjänst både i och utanför klustret.
    - K8sExperimental alternativ för att begränsa exponering av tjänster till enbart kluster.
- Kommunikation mellan moduler kräver `HostPort` specifikation, och anslutning med hjälp av mappad port (och inte användning av container exponerad port).
- Värdbaserade nätverk fungerar med `dnsPolicy = ClusterFirstWithHostNet` , med att alla behållare (särskilt `edgeHub` ) inte behöver vara på värd nätverket också. <!--Need further clarifications on this one-->
- Det fungerar inte att lägga till port mappningar för TCP, UDP i samma begäran.

#### <a name="example---external-access-to-modules"></a>Exempel – extern åtkomst till moduler 

För alla IoT Edge moduler som anger port bindningar tilldelas en IP-adress med hjälp av IP-intervallet Kubernetes extern tjänst som angavs i det lokala användar gränssnittet för enheten. Det finns inga ändringar i behållaren skapa alternativ mellan IoT Edge på Docker eller IoT Edge på Kubernetes som visas i följande exempel.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

Men om du vill fråga IP-adressen som tilldelats till din modul kan du använda Kubernetes-instrumentpanelen enligt beskrivningen i [Hämta IP-adress för tjänster eller moduler](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules). 

Alternativt kan du [ansluta till PowerShell-gränssnittet på enheten](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) och använda `iotedge` list kommandot för att lista alla moduler som körs på enheten. [Kommandots utdata](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) visar även de externa IP-adresser som är kopplade till modulen.


## <a name="resource-usage"></a>Resursanvändning 

Med Kubernetes-baserade IoT Edge-inställningar på GPU-enheter anges resurserna som maskin varu acceleration, minne och CPU-krav på olika sätt än på FPGA-enheterna. 

#### <a name="compute-acceleration-usage"></a>Användning av beräknings acceleration

Om du vill distribuera moduler på FPGA använder du alternativen för att skapa behållare <!--with Device Bindings--> som du ser i följande konfiguration: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
För GPU använder du specifikationer för resurs förfrågningar i stället för enhets bindningar som visas i följande minimala konfiguration. Du begär NVIDIA-resurser i stället för Catapult och du behöver ange `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Minnes-och CPU-användning
 
Om du vill ange minne och CPU-användning använder du processor gränser för moduler i `k8s-experimental` avsnittet. <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
Minnes-och CPU-specifikationen är inte nödvändig, men i allmänhet bra praxis. Om `requests` inte anges används de värden som anges i gränser som minimi krav. 

Du måste också ha ett annat sätt att använda delat minne för moduler. Du kan till exempel använda värd IPC-läget för delad minnes åtkomst mellan live video analys och Härlednings lösningar enligt beskrivningen i [distribuera Real video analys på Azure Stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal).


## <a name="web-proxy"></a>Webbproxy 

Överväg följande information när du konfigurerar webbproxy:

Om du har en webbproxy som kon figurer ATS i nätverket konfigurerar du följande miljövariabler för `edgeHub` distributionen på den Docker-baserade IoT Edge installationen på FPGA enheter:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (om inte webbproxyn tillåter `Amqp` trafik)

För Kubernetes-baserade IoT Edge-inställningar på GPU-enheter måste du konfigurera den här ytterligare variabeln under distributionen:

- `no_proxy`: localhost

- IoT Edge proxy på Kubernetes-plattformen använder port 35000 och 35001. Kontrol lera att modulen inte körs på dessa portar eller orsaka port konflikter. 

## <a name="other-differences"></a>Andra skillnader

- **Distributions strategi**: du kan behöva ändra distributions beteendet för uppdateringar av modulen. Standard beteendet för IoT Edge moduler är rullande uppdatering. Det här beteendet förhindrar att den uppdaterade modulen startar om modulen använder resurser som maskin varu acceleration eller nätverks portar. Det här beteendet kan ha oväntade effekter, särskilt när du hanterar permanenta volymer på Kubernetes-plattformen för GPU-enheter. Om du vill åsidosätta det här standard beteendet kan du ange en `Recreate` i- `k8s-experimental` avsnittet i modulen.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- Modulnamn **: Modulnamn** bör följa Kubernetes namngivnings konventioner. Du kan behöva byta namn på modulerna som körs på IoT Edge med Docker när du flyttar modulerna till IoT Edge med Kubernetes. Mer information om namngivning finns i [namngivnings konventioner för Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Andra alternativ**: 
    - Vissa Docker-skapande alternativ som fungerade på FPGA-enheter fungerar inte i Kubernetes-miljön på GPU-enheterna. Exempel:, t. ex. – EntryPoint.<!--can we confirm what exactly is required here-->
    - Miljövariabler som `:` måste ersättas med `__` .
    - **Container skapar** status för en Kubernetes-Pod som leder till **backoff** -status för en modul på den IoT Hub resursen. Även om det finns ett antal orsaker till att pod är i denna status, är en vanlig orsak när en stor behållar avbildning hämtas via en anslutning med låg nätverks bandbredd. När pod är i det här tillståndet visas modulens status som **backoff** i IoT Hub, även om modulen bara startar.


## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [konfigurerar GPU för att använda en modul](azure-stack-edge-j-series-configure-gpu-modules.md).
