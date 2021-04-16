---
title: Hitta och använda information om marketplace-köpplan med hjälp av CLI
description: Lär dig hur du använder Azure CLI för att hitta avbildnings-URL:er och köpplansparametrar som utgivare, erbjudande, SKU och version för VM-avbildningar på Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484208"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Hitta Azure Marketplace bildinformation med hjälp av Azure CLI

Det här avsnittet beskriver hur du använder Azure CLI för att hitta VM-avbildningar i Azure Marketplace. Använd den här informationen för att ange en Marketplace-avbildning när du skapar en virtuell dator programmatiskt med CLI, Resource Manager-mallar eller andra verktyg.

Du kan också bläddra bland tillgängliga bilder och erbjudanden med [hjälp av Azure Marketplace](https://azuremarketplace.microsoft.com/) eller  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologi

En Marketplace-avbildning i Azure har följande attribut:

* **Utgivare:** Organisationen som skapade avbildningen. Exempel: Canonical, Microsoft Windows Server
* **Erbjudande:** Namnet på en grupp med relaterade avbildningar som skapats av en utgivare. Exempel: UbuntuServer, WindowsServer
* **SKU:** En instans av ett erbjudande, till exempel en större distribution. Exempel: 18.04-LTS, 2019-Datacenter
* **Version:** Versionsnumret för en avbildnings-SKU. 

Dessa värden kan skickas individuellt eller som en *bild-URN* genom att kombinera värdena avgränsade med kolon (:). Exempel: *Utgivare:**Erbjudande*:*SKU:**Version*. Du kan ersätta versionsnumret i URN med om `latest` du vill använda den senaste versionen av avbildningen. 

Om avbildningsutgivaren tillhandahåller ytterligare licens- och köpvillkor måste du godkänna dem innan du kan använda avbildningen.  Mer information finns i [Kontrollera informationen om köpplanen.](#check-the-purchase-plan-information)



## <a name="list-popular-images"></a>Lista populära avbildningar

Kör kommandot [az vm image list](/cli/azure/vm/image) utan alternativet för att se en lista över populära `--all` VM-avbildningar i Azure Marketplace. Kör till exempel följande kommando för att visa en cachelagrad lista över populära bilder i tabellformat:

```azurecli
az vm image list --output table
```

Utdata innehåller bild-URN. Du kan också använda *UrnAlias som* är en förkortad version som skapats för populära avbildningar som *UbuntuLTS.*

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Söka efter specifika avbildningar

Om du vill hitta en specifik VM-avbildning på Marketplace `az vm image list` använder du kommandot med alternativet `--all` . Den här versionen av kommandot tar lite tid att slutföra och kan returnera långa utdata, så du filtrerar vanligtvis listan efter `--publisher` eller någon annan parameter. 

Följande kommando visar till exempel alla Debian-erbjudanden (kom ihåg att utan växeln söker det bara i `--all` den lokala cachen med vanliga avbildningar):

```azurecli
az vm image list --offer Debian --all --output table 
```

Delvisa utdata: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Titta på alla tillgängliga avbildningar
 
Ett annat sätt att hitta en avbildning på en plats är att köra kommandona [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image)och [az vm image list-skus](/cli/azure/vm/image) i följd. Med dessa kommandon fastställer du följande värden:

1. Visa en lista över avbildningsutgivare för en plats. I det här exemplet tittar vi på regionen *USA,* västra.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Visa en lista över erbjudanden från en viss utgivare. I det här exemplet lägger vi *till Canonical* som utgivare.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Visa en lista över SKU:er för ett visst erbjudande. I det här exemplet lägger vi *till UbuntuServer* som erbjudande.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. För en viss utgivare, ett erbjudande och en SKU visar du alla versioner av avbildningen. I det här exemplet lägger vi *till 18.04-LTS* som SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Skicka det här värdet för URN-kolumnen med `--image` parametern när du skapar en virtuell dator med [kommandot az vm create.](/cli/azure/vm) Du kan också ersätta versionsnumret i URN med "senaste" om du bara vill använda den senaste versionen av avbildningen. 

Om du distribuerar en virtuell dator Resource Manager en mall anger du avbildningsparametrarna individuellt i `imageReference` egenskaperna. Se [mallreferensen](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Kontrollera informationen om köpplanen

Vissa VM-avbildningar i Azure Marketplace har ytterligare licens- och inköpsvillkor som du måste godkänna innan du kan distribuera dem programmatiskt.  

Om du vill distribuera en virtuell dator från en sådan avbildning måste du godkänna avbildningens villkor första gången du använder den, en gång per prenumeration. Du måste också ange parametrar för *inköpsplanen för att* distribuera en virtuell dator från avbildningen

Om du vill visa köpinformationen för en avbildning kör du [kommandot az vm image show](/cli/azure/image) med avbildningens URN. Om egenskapen `plan` i utdata inte är `null` har avbildningen termer som du måste godkänna före programmatisk distribution.

Till exempel har Canonical Ubuntu Server 18.04 LTS-avbildningen inga ytterligare termer eftersom `plan` informationen är `null` :

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Utdata:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Om du kör ett liknande kommando för RabbitMQ Certified by Bitnami-avbildningen visas `plan` följande egenskaper: `name` , och `product` `publisher` . (Vissa bilder har också en `promotion code` -egenskap.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Utdata:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

Om du vill distribuera den här avbildningen måste du godkänna villkoren och ange parametrarna för inköpsplanen när du distribuerar en virtuell dator med hjälp av avbildningen.

## <a name="accept-the-terms"></a>Acceptera villkoren

Om du vill visa och godkänna licensvillkoren använder du [kommandot az vm image accept-terms.](/cli/azure/vm/image/terms) När du godkänner villkoren aktiverar du programmatisk distribution i din prenumeration. Du behöver bara godkänna villkoren en gång per prenumeration för avbildningen. Exempel:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

Utdata innehåller `licenseTextLink` en i licensvillkoren och anger att värdet `accepted` för är `true` :

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

Om du vill godkänna villkoren skriver du:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Distribuera en ny virtuell dator med hjälp av avbildningsparametrarna

Med information om avbildningen kan du distribuera den med hjälp av `az vm create` kommandot . 

Om du vill distribuera en avbildning som inte har någon planinformation, t.ex. den senaste Ubuntu Server 18.04-avbildningen från Canonical, skickar du URN:et för `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


För en avbildning med parametrar för inköpsplanen, till exempel RabbitMQ Certified by Bitnami-avbildningen, skickar du URN för och anger även parametrarna `--image` för inköpsplanen:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Om du får ett meddelande om att godkänna villkoren i bilden kan du läsa avsnittet [Acceptera villkoren](#accept-the-terms). Kontrollera att utdata från `az vm image accept-terms` returnerar värdet `"accepted": true,` som visar att du har accepterat villkoren för bilden.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Använda en befintlig virtuell hårddisk med köpplaninformation

Om du har en befintlig virtuell hårddisk från en virtuell dator som har skapats med en betald Azure Marketplace-avbildning kan du behöva ange information om inköpsplanen när du skapar en ny virtuell dator från den virtuella hårddisken. 

Om du fortfarande har den ursprungliga virtuella datorn eller en annan virtuell dator som skapats med samma Marketplace-avbildning kan du hämta planens namn, utgivare och produktinformation från den med [hjälp av az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view). Det här exemplet hämtar en virtuell dator med namnet *myVM* i *resursgruppen myResourceGroup* och visar sedan informationen om köpplanen.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Om du inte fick planinformationen innan den ursprungliga virtuella datorn togs bort kan du skicka en [supportbegäran.](https://ms.portal.azure.com/#create/Microsoft.Support) De behöver vm-namnet, prenumerations-ID:t och tidsstämpeln för borttagningsåtgärden.

När du har planinformationen kan du skapa den nya virtuella datorn med hjälp av `--attach-os-disk` parametern för att ange den virtuella hårddisken.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Nästa steg
Information om hur du snabbt skapar en virtuell dator med hjälp av avbildningsinformationen finns i [Skapa och hantera virtuella Linux-datorer med Azure CLI.](tutorial-manage-vm.md)
