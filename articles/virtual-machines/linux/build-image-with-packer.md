---
title: Skapa linux Azure VM-avbildningar med Packer
description: Lär dig hur du använder Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 1b40646109265b803945b43d7cc855688c5b47c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764665"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Så här använder du Packer för att skapa avbildningar av virtuella Linux-datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Linux-distributionen och OS-versionen. Avbildningar kan innehålla förinstallerade program och konfigurationer. I Azure Marketplace många avbildningar från första och tredje part för de vanligaste distributionerna och programmiljöerna, eller så kan du skapa egna anpassade avbildningar som skräddarsytts efter dina behov. Den här artikeln beskriver hur du använder verktyget [Packer](https://www.packer.io/) med öppen källkod för att definiera och skapa anpassade avbildningar i Azure.

> [!NOTE]
> Azure har nu en tjänst, Azure Image Builder (förhandsversion) för att definiera och skapa egna anpassade avbildningar. Azure Image Builder bygger på Packer, så du kan även använda dina befintliga Packer Shell-etableringsskript med den. Information om hur du kommer igång med Azure Image Builder finns i [Skapa en virtuell Linux-dator med Azure Image Builder](image-builder.md).


## <a name="create-azure-resource-group"></a>Skapa en Azure-resursgrupp
Under byggprocessen skapar Packer tillfälliga Azure-resurser när den virtuella källdatorn skapas. Om du vill avbilda den virtuella källdatorn för användning som en avbildning måste du definiera en resursgrupp. Utdata från Packer-byggprocessen lagras i den här resursgruppen.

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiserar med Azure med hjälp av ett huvudnamn för tjänsten. Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som du kan använda med appar, tjänster och automatiseringsverktyg som Packer. Du styr och definierar behörigheterna för vilka åtgärder tjänstens huvudnamn kan utföra i Azure.

Skapa ett huvudnamn för tjänsten [med az ad sp create-for-rbac och](/cli/azure/ad/sp) mata ut de autentiseringsuppgifter som Packer behöver:

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

För att autentisera till Azure måste du också hämta ditt Prenumerations-ID för Azure med [az account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

Du använder utdata från dessa två kommandon i nästa steg.


## <a name="define-packer-template"></a>Definiera Packer-mall
Om du vill skapa avbildningar skapar du en mall som en JSON-fil. I mallen definierar du byggare och etablerare som utför själva byggprocessen. Packer har en [etableringsprincip för Azure](https://www.packer.io/docs/builders/azure.html) som gör att du kan definiera Azure-resurser, till exempel autentiseringsuppgifterna för tjänstens huvudnamn som skapades i föregående steg.

Skapa en fil med *ubuntu.jspå* och klistra in följande innehåll. Ange egna värden för följande:

| Parameter                           | Var du kan hämta |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Den första raden med utdata `az ad sp` från kommandot create – *appId* |
| *client_secret*                     | Andra raden med utdata från `az ad sp` kommandot create – *password* |
| *tenant_id*                         | Tredje raden med utdata från `az ad sp` kommandot create – *tenant* |
| *subscription_id*                   | Utdata från `az account show` kommandot |
| *managed_image_resource_group_name* | Namnet på resursgruppen som du skapade i det första steget |
| *managed_image_name*                | Namn på den hanterade diskavbildningen som skapas |


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

Den här mallen skapar en Ubuntu 16.04 LTS-avbildning, installerar NGINX och avetablerar sedan den virtuella datorn.

> [!NOTE]
> Om du expanderar den här mallen för att etablera autentiseringsuppgifter för användare justerar du etableringskommandot som avetablerar Azure-agenten så att den läser `-deprovision` i stället för `deprovision+user` .
> Flaggan `+user` tar bort alla användarkonton från den virtuella källdatorn.


## <a name="build-packer-image"></a>Skapa Packer-avbildning
Om du inte redan har Packer installerat på den lokala datorn följer [du installationsanvisningarna för Packer.](https://www.packer.io/docs/install)

Skapa avbildningen genom att ange Packer-mallfilen på följande sätt:

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

Det tar några minuter för Packer att skapa den virtuella datorn, köra etableringen och rensa distributionen.


## <a name="create-vm-from-azure-image"></a>Skapa en virtuell dator från En Azure-avbildning
Nu kan du skapa en virtuell dator från avbildningen med [az vm create](/cli/azure/vm). Ange den avbildning som du skapade med `--image` parametern . I följande exempel skapas en virtuell dator *med namnet myVM* *från myPackerImage* och SSH-nycklar genereras om de inte redan finns:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Om du vill skapa virtuella datorer i en annan resursgrupp eller region än Packer-avbildningen anger du avbildnings-ID:t i stället för avbildningsnamnet. Du kan hämta avbildnings-ID:t [med az image show](/cli/azure/image#az_image_show).

Det tar några minuter att skapa den virtuella datorn. När den virtuella datorn har skapats noterar du den `publicIpAddress` som visas av Azure CLI. Den här adressen används för att komma åt NGINX-webbplatsen via en webbläsare.

För att låta webbtrafik nå din virtuella dator öppnar du port 80 från Internet med [az vm open-port](/cli/azure/vm):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testa virtuell dator och NGINX
Nu kan du öppna en webbläsare och ange `http://publicIpAddress` i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Nginx-standardsidan visas som i följande exempel:

![NGINX-standardwebbplats](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga Packer-etableringsskript med [Azure Image Builder](image-builder.md).
