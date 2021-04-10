---
title: Skapa virtuella Linux Azure-avbildningar med Packer
description: Lär dig hur du använder Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556748"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Använda Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Linux-distributionen och OS-versionen. Avbildningar kan omfatta förinstallerade program och konfigurationer. Azure Marketplace innehåller många första och tredje parts avbildningar för de flesta vanliga distributioner och program miljöer, eller så kan du skapa egna anpassade avbildningar som är anpassade efter dina behov. Den här artikeln beskriver [hur du använder verktyg för](https://www.packer.io/) öppen källkod för att definiera och skapa anpassade avbildningar i Azure.

> [!NOTE]
> Nu har Azure en tjänst, Azure Image Builder (för hands version), för att definiera och skapa egna anpassade avbildningar. Azure Image Builder bygger på Packer, så du kan till och med använda dina befintliga Packers Shell-Provisioning-skript med det. För att komma igång med Azure Image Builder, se [skapa en virtuell Linux-dator med Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Skapa Azure-resurs grupp
Under Bygg processen skapar Packern tillfälliga Azure-resurser när den skapar den virtuella käll datorn. Om du vill avbilda den virtuella käll datorn för användning som en avbildning måste du definiera en resurs grupp. Utdata från paketets Bygg process lagras i den här resurs gruppen.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiseras med Azure med hjälp av ett huvud namn för tjänsten. Ett huvud namn för Azure-tjänsten är en säkerhets identitet som du kan använda med appar, tjänster och automatiserings verktyg som Packer. Du styr och definierar behörigheterna för vilka åtgärder som tjänstens huvud namn kan utföra i Azure.

Skapa ett huvud namn för tjänsten med [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp) och generera de autentiseringsuppgifter som packare behöver:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Ett exempel på utdata från föregående kommandon är följande:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

För att kunna autentisera till Azure måste du också skaffa ditt prenumerations-ID för Azure med [AZ-kontot show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Du använder utdata från de här två kommandona i nästa steg.


## <a name="define-packer-template"></a>Definiera Packer-mall
För att skapa avbildningar skapar du en mall som en JSON-fil. I mallen definierar du skapare och provisioner som utför den faktiska Bygg processen. Packer har en [etablerings ansvarig för Azure](https://www.packer.io/docs/builders/azure.html) som gör att du kan definiera Azure-resurser, till exempel de autentiseringsuppgifter för tjänstens huvud namn som skapades i föregående steg.

Skapa en fil med namnet *ubuntu.jspå* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Var du kan hämta |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Första raden utdata från `az ad sp` kommandot CREATE- *appId* |
| *client_secret*                     | Andra raden utdata från `az ad sp` kommandot CREATE- *Password* |
| *tenant_id*                         | Tredje raden utdata från `az ad sp` skapa kommando *klient* |
| *subscription_id*                   | Utdata från `az account show` kommando |
| *managed_image_resource_group_name* | Namnet på den resurs grupp som du skapade i det första steget |
| *managed_image_name*                | Namn för den hanterade disk avbildning som skapas |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Den här mallen skapar en Ubuntu 16,04 LTS-avbildning, installerar NGINX och avetablerar den virtuella datorn.

> [!NOTE]
> Om du expanderar den här mallen för att etablera användarautentiseringsuppgifter kan du justera etablerings kommandot som avetablerar Azure-agenten för att läsa `-deprovision` i stället för `deprovision+user` .
> `+user`Flaggan tar bort alla användar konton från den virtuella käll datorn.


## <a name="build-packer-image"></a>Avbildning av bygg paket
Om du inte redan har installerat Packer på den lokala datorn [följer du installations anvisningarna för installations](https://www.packer.io/docs/install)guiden.

Bygg avbildningen genom att ange din Packer-mallfil enligt följande:

```bash
./packer build ubuntu.json
```

Ett exempel på utdata från föregående kommandon är följande:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Det tar några minuter för packare att skapa den virtuella datorn, köra provisionen och rensa distributionen.


## <a name="create-vm-from-azure-image"></a>Skapa en virtuell dator från Azure image
Nu kan du skapa en virtuell dator från avbildningen med [AZ VM Create](/cli/azure/vm). Ange den avbildning som du skapade med `--image` parametern. I följande exempel skapas en virtuell dator med namnet *myVM* från *MYPACKERIMAGE* och genererar SSH-nycklar om de inte redan finns:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Om du vill skapa virtuella datorer i en annan resurs grupp eller region än din Packer-avbildning anger du bild-ID i stället för avbildnings namn. Du kan hämta avbildnings-ID: t med [AZ image show](/cli/azure/image#az-image-show).

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats noterar du den `publicIpAddress` som visas av Azure CLI. Den här adressen används för att få åtkomst till NGINX-webbplatsen via en webbläsare.

För att låta webbtrafik nå din virtuella dator öppnar du port 80 från Internet med [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testa VM och NGINX
Nu kan du öppna en webbläsare och ange `http://publicIpAddress` i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Standard sidan för NGINX visas som i följande exempel:

![NGINX-standardwebbplats](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga paket med Provisioning-skript med [Azure Image Builder](image-builder.md).
