---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773481"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I testsyfte använder du en Docker-aktiverad virtuell Ubuntu-dator för att få åtkomst till ett Azure-containerregister. Om du Azure Active Directory autentisering till registret installerar du även [Azure CLI på den][azure-cli] virtuella datorn. Om du redan har en virtuell Azure-dator kan du hoppa över det här genereringssteget.

Du kan använda samma resursgrupp för den virtuella datorn och containerregistret. Den här konfigurationen förenklar rensningen i slutet men är inte obligatorisk. Om du väljer att skapa en separat resursgrupp för den virtuella datorn och det virtuella nätverket kör du [az group create][az-group-create]. I följande exempel förutsätter vi att du har angett miljövariabler för resursgruppens namn och registerplats:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Distribuera nu en virtuell Ubuntu Azure-standarddator med [az vm create][az-vm-create]. I följande exempel skapas en virtuell dator med *namnet myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det som `publicIpAddress` visas av Azure CLI. Använd den här adressen för att göra SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn körs upprättar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress med* den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommandon för att installera Docker på den virtuella Ubuntu-datorn:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrollera att Docker körs korrekt på den virtuella datorn:

```bash
sudo docker run -it hello-world
```

Utdata:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Följ stegen i Installera [Azure CLI med apt för](/cli/azure/install-azure-cli-apt) att installera Azure CLI på din virtuella Ubuntu-dator. Exempel:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Avsluta SSH-anslutningen.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group