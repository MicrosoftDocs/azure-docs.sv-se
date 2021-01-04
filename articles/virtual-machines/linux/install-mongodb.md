---
title: Installera MongoDB på en virtuell Linux-dator med Azure CLI
description: Lär dig hur du installerar och konfigurerar MongoDB på en virtuell Linux-dator iusing Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3bc8ed2745e06096e05f17319a8f7896f87f80f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702046"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Så här installerar och konfigurerar du MongoDB på en virtuell Linux-dator

[MongoDB](https://www.mongodb.org) är en populär NoSQL-databas med öppen källkod och hög prestanda. Den här artikeln visar hur du installerar och konfigurerar MongoDB på en virtuell Linux-dator med Azure CLI. Exempel på hur du kan:

* [Installera och konfigurera en Basic MongoDB-instans manuellt](#manually-install-and-configure-mongodb-on-a-vm)
* [Skapa en grundläggande MongoDB-instans med en Resource Manager-mall](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Skapa ett komplext MongoDB shardade-kluster med replik uppsättningar med en Resource Manager-mall](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installera och konfigurera MongoDB manuellt på en virtuell dator
MongoDB [innehåller installations anvisningar](https://docs.mongodb.com/manual/administration/install-on-linux/) för Linux distributioner, inklusive Red Hat/CENTOS, SUSE, Ubuntu och Debian. I följande exempel skapas en virtuell *CentOS* -dator. Om du vill skapa den här miljön måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en virtuell dator med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVM* med en användare med namnet *azureuser* med hjälp av autentisering med offentlig SSH-nyckel

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH till den virtuella datorn med ditt eget användar namn och listan `publicIpAddress` i resultatet från föregående steg:

```bash
ssh azureuser@<publicIpAddress>
```

Om du vill lägga till installations källorna för MongoDB skapar du en **yum** -lagringsplats enligt följande:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Öppna filen MongoDB lagrings platsen för redigering, till exempel med `vi` eller `nano` . Lägg till följande rader:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installera MongoDB med **yum** på följande sätt:

```bash
sudo yum install -y mongodb-org
```

Som standard tillämpas SELinux på CentOS-avbildningar som förhindrar åtkomst till MongoDB. Installera princip hanterings verktyg och konfigurera SELinux så att MongoDB kan användas på TCP-port 27017 på följande sätt:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starta MongoDB-tjänsten på följande sätt:

```bash
sudo service mongod start
```

Verifiera MongoDB-installationen genom att ansluta med den lokala `mongo` klienten:

```bash
mongo
```

Testa nu MongoDB-instansen genom att lägga till några data och sedan söka:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Om du vill kan du konfigurera MongoDB så att det startar automatiskt under en omstart av systemet:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Skapa en grundläggande MongoDB-instans på CentOS med hjälp av en mall
Du kan skapa en grundläggande MongoDB-instans på en enskild CentOS VM med hjälp av följande Azure snabb starts mall från GitHub. Den här mallen använder tillägget för anpassat skript för Linux för att lägga till en **yum** -lagringsplats till den nyskapade CentOS VM och sedan installera MongoDB.

* [Basic MongoDB-instans på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Om du vill skapa den här miljön måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Sedan distribuerar du MongoDB-mallen med [AZ-distributions gruppen Skapa](/cli/azure/deployment/group). När du uppmanas till det anger du dina egna unika värden för användar namn och lösen ord för *newStorageAccountName*, *dnsNameForPublicIP* och admin:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Logga in på den virtuella datorn med den offentliga DNS-adressen för den virtuella datorn. Du kan visa den offentliga DNS-adressen med [AZ VM show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH till den virtuella datorn med ditt eget användar namn och offentliga DNS-adress:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Verifiera MongoDB-installationen genom att ansluta med hjälp av den lokala `mongo` klienten på följande sätt:

```bash
mongo
```

Testa nu instansen genom att lägga till några data och söka enligt följande:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Skapa ett komplext MongoDB shardade-kluster på CentOS med hjälp av en mall
Du kan skapa ett komplext MongoDB shardade-kluster med hjälp av följande Azure snabb starts mall från GitHub. Den här mallen följer [metod tipsen för MongoDB shardade-kluster](https://docs.mongodb.com/manual/core/sharded-cluster-components/) för att ge redundans och hög tillgänglighet. Mallen skapar två Shards med tre noder i varje replik uppsättning. En replik uppsättning av konfigurations servern med tre noder skapas också, plus två **mongos** -router-servrar för att tillhandahålla konsekvens till program från hela Shards.

* [MongoDB horisontell partitionering-kluster på CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Distribution av det här komplexa MongoDB shardade-klustret kräver fler än 20 kärnor, vilket vanligt vis är standard antalet kärnor per region för en prenumeration. Öppna en support förfrågan för Azure om du vill öka antalet kärnor.

Om du vill skapa den här miljön måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index). Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Sedan distribuerar du MongoDB-mallen med [AZ-distributions gruppen Skapa](/cli/azure/deployment/group). Definiera egna resurs namn och storlekar där det behövs, till exempel för *mongoAdminUsername*, *sizeOfDataDiskInGB* och *configNodeVmSize*:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Distributionen kan ta över en timme för att distribuera och konfigurera alla VM-instanser. `--no-wait`Flaggan används i slutet av föregående kommando för att returnera kontroll till kommando tolken när mall distributionen har godkänts av Azure-plattformen. Du kan sedan Visa distributions status med [AZ distributions grupp Visa](/cli/azure/deployment/group). Följande exempel visar status för *myMongoDBCluster* -distributionen i *myResourceGroup* -resurs gruppen:

```azurecli
az deployment group show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Nästa steg
I de här exemplen ansluter du till MongoDB-instansen lokalt från den virtuella datorn. Om du vill ansluta till MongoDB-instansen från en annan virtuell dator eller ett nätverk, se till att rätt [regler för nätverks säkerhets grupper skapas](nsg-quickstart.md).

De här exemplen distribuerar kärnan MongoDB-miljön i utvecklings syfte. Använd de säkerhets konfigurations alternativ som krävs för din miljö. Mer information finns i [säkerhets dokumenten MongoDB](https://docs.mongodb.com/manual/security/).

Mer information om hur du skapar med hjälp av mallar finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Azure Resource Manager-mallarna använder tillägget för anpassat skript för att ladda ned och köra skript på dina virtuella datorer. Mer information finns i [använda Azures anpassade skript tillägg med virtuella Linux-datorer](../extensions/custom-script-linux.md).
