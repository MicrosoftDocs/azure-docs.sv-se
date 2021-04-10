---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244735"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Skapa en Docker-aktiverad virtuell dator

I test syfte använder du en Docker-aktiverad virtuell Ubuntu-dator för att få åtkomst till ett Azure Container Registry. Om du vill använda Azure Active Directory autentisering i registret installerar du även [Azure CLI][azure-cli] på den virtuella datorn. Hoppa över det här steget om du redan har en virtuell Azure-dator.

Du kan använda samma resurs grupp för din virtuella dator och behållar registret. Den här installationen fören klar rensningen, men är inte obligatorisk. Om du väljer att skapa en separat resurs grupp för den virtuella datorn och det virtuella nätverket kör du [AZ Group Create][az-group-create]. I följande exempel förutsätter vi att du har ställt in miljövariabler för resurs gruppens namn och register platsen:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Distribuera nu en standard virtuell Azure-Ubuntu med [AZ VM Create][az-vm-create]. I följande exempel skapas en virtuell dator med namnet *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn. När kommandot har slutförts noterar du det `publicIpAddress` som visas av Azure CLI. Använd den här adressen för att göra SSH-anslutningar till den virtuella datorn.

### <a name="install-docker-on-the-vm"></a>Installera Docker på den virtuella datorn

När den virtuella datorn har körts skapar du en SSH-anslutning till den virtuella datorn. Ersätt *publicIpAddress* med den offentliga IP-adressen för den virtuella datorn.

```bash
ssh azureuser@publicIpAddress
```

Kör följande kommandon för att installera Docker på den virtuella datorn Ubuntu:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Efter installationen kör du följande kommando för att kontrol lera att Docker körs korrekt på den virtuella datorn:

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

Följ stegen i [Installera Azure CLI med apt](/cli/azure/install-azure-cli-apt) för att installera Azure CLI på den virtuella Ubuntu-datorn. Exempel:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Avsluta SSH-anslutningen.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group