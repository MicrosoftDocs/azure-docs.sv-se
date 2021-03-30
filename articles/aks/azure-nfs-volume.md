---
title: Skapa NFS Ubuntu Linux Server volym
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du manuellt skapar en NFS-Ubuntu Linux Server volym för användning med poddar i Azure Kubernetes service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 4e817d572a98ffb8135adf58d13f50ccacbc8746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86252002"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Skapa och använda en NFS-volym (Network File System) manuellt med Azure Kubernetes service (AKS)
Att dela data mellan behållare är ofta en nödvändig komponent i behållar tjänster och program. Du har vanligt vis olika poddar som behöver åtkomst till samma information på en extern permanent volym.    
Även om Azure Files är ett alternativ kan du skapa en NFS-server på en virtuell Azure-dator med en annan typ av beständiga delade lagrings enheter. 

I den här artikeln visas hur du skapar en NFS-server på en virtuell Ubuntu-dator. Du kan också ge dina AKS-behållare åtkomst till det här delade fil systemet.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Ditt AKS-kluster måste vara Live i samma eller peer-kopplat virtuella nätverk som NFS-servern. Klustret måste skapas i ett befintligt virtuellt nätverk, vilket kan vara samma VNET som den virtuella datorn.

Stegen för att konfigurera med ett befintligt VNET beskrivs i dokumentationen: [skapa AKS-kluster i befintligt VNet][aks-virtual-network] och [ansluta virtuella nätverk med VNet-peering][peer-virtual-networks]

Det förutsätter också att du har skapat en Ubuntu Linux virtuell dator (till exempel 18,04 LTS). Inställningar och storlek kan vara till dina önskemål och kan distribueras via Azure. För Linux-snabb start, se [hantering av virtuella Linux-datorer][linux-create].

Om du distribuerar ditt AKS-kluster först fyller Azure automatiskt i fältet virtuellt nätverk när du distribuerar din Ubuntu-dator, vilket gör dem aktiva inom samma VNET. Men om du vill arbeta med peer-kopplade nätverk kan du läsa mer i dokumentationen ovan.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuera NFS-servern på en virtuell dator
Här är skriptet för att konfigurera en NFS-server i din virtuella Ubuntu-dator:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Servern kommer att startas om (på grund av skriptet) och du kan montera NFS-servern till AKS.

>[!IMPORTANT]  
>Se till att ersätta **AKS_SUBNET** med rätt namn från klustret, annars kommer "*" att öppna din NFS-server för alla portar och anslutningar.

När du har skapat den virtuella datorn kopierar du skriptet ovan till en fil. Sedan kan du flytta den från den lokala datorn eller vart och ett av skriptet till den virtuella datorn med hjälp av: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
När skriptet finns i den virtuella datorn kan du använda SSH i den virtuella datorn och köra det via kommandot:
```console
sudo ./nfs-server-setup.sh
```
Om körningen Miss lyckas på grund av ett fel vid nekad åtkomst anger du kör behörighet via kommandot:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Ansluta AKS-kluster till NFS-server
Vi kan ansluta NFS-servern till vårt kluster genom att tillhandahålla en beständig volym och ett beständigt volym anspråk som anger hur du ska få åtkomst till volymen.

Det krävs att du ansluter de två tjänsterna i samma eller peer-kopplade virtuella nätverk. Anvisningar för att konfigurera klustret i samma VNET finns här: [skapa AKS-kluster i befintligt VNet][aks-virtual-network]

När de finns i samma virtuella nätverk (eller peer-peer) måste du etablera en permanent volym och ett beständigt volym anspråk i ditt AKS-kluster. Behållarna kan sedan montera NFS-enheten i sin lokala katalog.

Här är ett exempel på en Kubernetes-definition för den permanenta volymen (den här definitionen förutsätter att ditt kluster och den virtuella datorn finns i samma VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Ersätt **NFS_INTERNAL_IP**, **NFS_NAME** och **NFS_EXPORT_FILE_PATH** med information om NFS-server.

Du behöver också en permanent volym anspråks fil. Här är ett exempel på vad du kan ta med:

>[!IMPORTANT]  
>**"storageClassName"** måste vara en tom sträng eller så fungerar inte anspråket.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Felsökning
Om du inte kan ansluta till servern från ett kluster kan ett problem vara den exporterade katalogen eller dess överordnade objekt har inte tillräcklig behörighet för att komma åt servern.

Kontrol lera att både export katalogen och dess överordnade katalog har 777 behörigheter.

Du kan kontrol lera behörigheter genom att köra kommandot nedan och katalogerna ska ha behörigheten *"drwxrwxrwx"* :
```console
ls -l
```

## <a name="more-information"></a>Mer information
För att få en fullständig genom gång eller för att hjälpa dig att felsöka konfigurationen av NFS-servern, här är en djupgående självstudie:
  - [Själv studie kurs om NFS][nfs-tutorial]

## <a name="next-steps"></a>Nästa steg

För associerade metod tips, se [metod tips för lagring och säkerhets kopiering i AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: ./configure-kubenet.md#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: ../virtual-network/tutorial-connect-virtual-networks-portal.md

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
