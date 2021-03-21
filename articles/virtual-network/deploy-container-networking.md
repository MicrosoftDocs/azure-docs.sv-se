---
title: Distribuera Azure Virtual Network CNI (Container Network Interface) | Microsoft Docs
description: Lär dig hur du distribuerar plugin-programmet för Azure Virtual Network container Network Interface (CNI) för Kubernetes-kluster.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222929"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Distribuera plugin-programmet Azure Virtual Network CNI

Plugin-programmet Azure Virtual Network CNI installerar en virtuell Azure-dator och tillhandahåller funktioner för virtuella nätverk i Kubernetes-poddar och Docker-containrar. Du kan läsa mer om plugin-programmet i [Enable containers to use Azure Virtual Network capabilities](container-networking-overview.md) (Aktivera Azure Virtual Network-funktioner i containrar). Du kan också använda plugin-programmet med Azure Kubernetes Service (AKS) genom att välja alternativet [Advanced Networking](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Avancerat nätverk). Om du gör det placeras AKS-containrar automatiskt i ett virtuella nätverk.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Distribuera plugin-program för ACS-Engine Kubernetes-kluster

ACS-Engine distribuerar ett Kubernetes-kluster med en Azure Resource Manager-mall. Klusterkonfigurationen anges i en JSON-fil som överförs till verktyget när mallen skapas. Läs mer om hela listan med klusterinställningar som stöds och beskrivningar av dessa i [Microsoft Azure Container Service Engine - Cluster Definition](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). Plugin-programmet är standardplugin-program för nätverk för de kluster som skapas med ACS-Engine. Följande inställningar för nätverkskonfiguration är viktiga när du konfigurerar plugin-programmet:

  | Inställning                              | Beskrivning                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | IP-adressen som har allokerats till den överordnade noden. Det här är en obligatorisk inställning.                                     |
  | clusterSubnet under kubernetesConfig | CIDR för det virtuella nätverkets undernät där klustret distribueras, och från vilket IP-adresser tilldelas till poddar   |
  | vnetSubnetId under masterProfile     | Anger resurs-ID för Azure Resource Manager för undernätet där klustret ska distribueras                    |
  | vnetCidr                             | CIDR för det virtuella nätverket där klustret distribueras                                                             |
  | max-Pods under kubeletConfig         | Maximalt antal poddar på varje Virtual Machine-agent. Standardvärdet är 30 för plugin-programmet. Du kan ange upp till 250  |

### <a name="example-configuration"></a>Exempelkonfiguration

Json-exemplet nedan är för ett kluster med följande egenskaper:
-   1 överordnad nod och 2 agentnoder 
-   Distribueras i ett undernät med namnet *KubeClusterSubnet* (10.0.0.0/20) som innehåller både överordnad nod och agentnoder.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Distribuera plugin-program för ett Kubernetes-kluster

Utför följande steg för att installera plugin-programmet på varje virtuell Azure-dator i ett Kubernetes-kluster:

1. [Hämta och installera plugin-programmet](#download-and-install-the-plug-in).
2. Förallokera en IP-adresspool för virtuellt nätverk på alla virtuella datorer från vilka IP-adresser ska tilldelas till poddar. Varje virtuell Azure-dator har en primär privat IP-adress i varje nätverksgränssnitt. Adresserna i IP-adresspoolen för poddar läggs till som sekundära adresser (*ipconfigs*) i nätverksgränssnittet med ett av följande alternativ:

   - **CLI**: [Tilldela flera IP-adresser med Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [Tilldela flera IP-adresser med PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **CLI**: [Tilldela flera IP-adresser med Azure-portalen](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager-mall**: [Tilldela flera IP-adresser med mallar](./template-samples.md)

   Se till att du lägger till tillräckligt med IP-adresser för alla poddar som du tänker ha på den virtuella datorn.

3. Välj plugin-programmet för att ge ditt kluster nätverksfunktion genom att skicka kommandoradsalternativet `–network-plugin=cni` till Kubelet när klustret skapas. Kubernetes söker som standard efter plugin-programmet och konfigurationsfilen i de kataloger där dessa redan är installerade.
4. Om du vill att poddarna ska ha åtkomst till Internet lägger du till följande *iptables*-regel i din Internettrafik från virtuella Linux-datorer till käll-NAT. I följande exempel är det angivna IP-intervallet 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Reglerna för NAT-trafik som inte är avsedd för de angivna IP-intervallen. Här antas att all trafik utanför de föregående intervallen är Internettrafik. Du kan välja att ange IP-intervall för den virtuella datorns virtuella nätverk, för peerkopplade virtuella nätverk och lokala nätverk.

   Virtuella Windows-datorer identifierar automatiskt källan för NAT-trafik som har ett mål som är utanför det undernät som den virtuell datorn tillhör. Det går inte att ange anpassade IP-intervall.

När du har slutfört föregående steg tilldelas de poddar som visas på virtuella Kubernetes Agent-datorer automatiskt privata IP-adresser från det virtuella nätverket.

## <a name="deploy-plug-in-for-docker-containers"></a>Distribuera plugin-program för Docker-containrar

1. [Hämta och installera plugin-programmet](#download-and-install-the-plug-in).
2. Skapa Docker-containrar med följande kommando:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Containrarna börjar automatiskt att ta emot IP-adresser från den allokerade poolen. Om du vill belastningsutjämna trafik till Docker-containrar måste de placeras bakom en lastbalanserare (programvara), och du måste konfigurera en lastbalanseraravsökning på samma sätt som när du skapar en princip och avsökningar för en virtuell dator.

### <a name="cni-network-configuration-file"></a>CNI-nätverkskonfigurationsfil

CNI-nätverkskonfigurationsfilen beskrivs i JSON-format. Den finns som standard i `/etc/cni/net.d` för Linux och i `c:\cni\netconf` för Windows. Filen anger konfigurationen för plugin-programmet. Olika filer används för Windows och Linux. Json nedan är ett exempel på en Linux-konfigurationsfil, följt av en förklaring till vissa viktiga inställningar. Du behöver inte göra några ändringar i filen:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Förklaring till inställningar

- **cniVersion**: Azure Virtual Network CNI-plugin-program stöder version 0.3.0 och 0.3.1 av [CNI spec](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: Namnet på nätverket. Den här egenskapen kan anges till ett unikt värde.
- **type**: Namnet på nätverksplugin-programmet. Ange *azure-vnet*.
- **mode**: Funktionsläge. Det här fältet är valfritt. Det enda läge som stöds är ”bridge”. Mer information finns i [funktionslägen](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md).
- **bridge**: Namnet på bryggan som ska användas för att ansluta containrar till ett virtuellt nätverk. Det här fältet är valfritt. Om fältet utelämnas väljer plugin-programmet automatiskt ett unikt namn, baserat på huvudgränssnittsindex.
- **ipam type**: Namnet på IPAM-plugin-programmet. Ange alltid *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>Ladda ned och installera plugin-programmet

Hämta plugin-programmet från [GitHub](https://github.com/Azure/azure-container-networking/releases). Hämta den senaste versionen för den plattform som du använder:

- **Linux**: [Azure-VNet-cni-linux-amd64- \<version no.\> . tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [Azure-VNet-cni-Windows-amd64- \<version no.\> . zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Kopiera installationsskriptet för [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) eller [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) till datorn. Spara skriptet till en `scripts`-katalog på din dator och ge filen namnet `install-cni-plugin.sh` för Linux eller `install-cni-plugin.ps1` för Windows. Du installerar plugin-programmet genom att köra lämpligt skript för din plattform och ange versionen för det plugin-program som du använder. Du kan till exempel ange *v1.0.12-rc3*:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Skriptet installerar plugin-programmet under `/opt/cni/bin` för Linux och `c:\cni\bin` för Windows. Det installerade plugin-programmet levereras med en enkel nätverkskonfigurationsfil som kan användas efter installationen. Den behöver inte uppdateras. Du kan läsa mer om inställningarna i filen i [CNI-nätverkskonfigurationsfil](#cni-network-configuration-file).