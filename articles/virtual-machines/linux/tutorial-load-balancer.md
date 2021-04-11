---
title: Självstudie – belastningsutjämna virtuella Linux-datorer i Azure
description: I den här självstudien lär du dig hur du använder Azure CLI för att skapa en lastbalanserare för ett säkert program med hög tillgänglighet på tre virtuella Linux-datorer
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.subservice: networking
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 433bbd51618cfb5624c8ed2c549e1793488f0e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553773"
---
# <a name="tutorial-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application-with-the-azure-cli"></a>Självstudie: lastbalansera virtuella Linux-datorer i Azure för att skapa ett program med hög tillgänglighet med Azure CLI

Med belastningsutjämning får du högre tillgänglighet genom att inkommande begäranden sprids över flera virtuella datorer. I den här kursen får du lära dig mer om de olika komponenterna i Azure Load Balancer som distribuerar trafik och ger hög tillgänglighet. Lär dig att:

> [!div class="checklist"]
> * skapa en Azure Load Balancer
> * skapa en hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * Använda cloud-init för att skapa en grundläggande Node.js-app
> * skapa virtuella datorer och anslut dem till en lastbalanserare
> * visa en lastbalanserare i praktiken
> * lägga till och ta bort virtuella datorer från en lastbalanserare.

I den här självstudien används CLI i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="azure-load-balancer-overview"></a>Översikt över Azure Load Balancer
En Azure-lastbalanserare är en Layer-4-lastbalanserare (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik till felfria virtuella datorer. Lastbalanseraren har en hälsoavsökningsfunktion som övervakar en given port på varje virtuell dator och ser till att trafik endast distribueras till virtuella datorer som fungerar.

Du definierar en IP-konfiguration på klientdelen som innehåller en eller flera offentliga IP-adresser. IP-konfigurationen på klientdelen gör att det går att komma åt lastbalanseraren och program via Internet. 

Virtuella datorer ansluter till en lastbalanserare med ett virtuellt nätverkskort. För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren.

För att styra trafikflödet definierar du regler för lastbalanseraren för specifika portar och protokoll som mappar till dina virtuella datorer.

Om du följde den föregående självstudien [skapa en VM-skalningsuppsättning](tutorial-create-vmss.md) skapades en lastbalanserare. Alla dessa komponenter konfigurerades som en del av skalningsuppsättningen.


## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer
I det här avsnittet beskrivs hur du skapar och konfigurerar varje komponent i lastbalanseraren. Innan du kan skapa lastbalanseraren måste du skapa en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroupLoadBalancer* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att kunna komma åt din app på Internet behöver du en offentlig IP-adress för lastbalanseraren. Skapa en offentlig IP-adress med [az network public-ip create](/cli/azure/network/public-ip). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i resursgruppen *myResourceGroupLoadBalancer*:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Skapa en lastbalanserare
Skapa en lastbalanserare med [az network lb create](/cli/azure/network/lb). Det här exemplet skapar en lastbalanserare med namnet *myLoadBalancer* och tilldelar *myPublicIP*-adressen till IP-konfigurationen för klienten:

```azurecli-interactive
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning
Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Som standard tas en virtuell dator bort från lastbalanserarens distribution efter två fel i följd inom ett intervall på 15 sekunder. Du skapar en hälsoavsökning baserat på ett protokoll eller en specifik hälsokontrollsida för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar om du vill ha mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökning måste du skapa en hälsokontrollsida, till exempel *healthcheck.js*. Avsökningen måste returnera svaret **HTTP 200 OK** för att lastbalanseraren ska behålla värden i rotation.

Skapa en TCP-hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe). I följande exempel skapas en hälsoavsökning med namnet *myHealthProbe*:

```azurecli-interactive
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen för den inkommande trafiken och IP-poolen i serverdelen som ska ta emot trafiken, samt nödvändiga käll- och målportar. För att säkerställa att de virtuella datorerna endast tar emot felfri trafik definierar du också vilken hälsoavsökning som ska användas.

Använd [az network lb rule create](/cli/azure/network/lb/rule) för att skapa en regel för lastbalanseraren. I följande exempel skapas en belastningsutjämningsregel med namnet *myLoadBalancerRule* som använder hälsoavsökningen *myHealthProbe* och utjämnar trafiken på port *80*:

```azurecli-interactive
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du kan distribuera virtuella datorer och testa din belastningsutjämnare skapar du virtuella nätverksresurser. Mer information om virtuella nätverk finns i självstudiekursen [Hantera virtuella Azure-nätverk](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Skapa nätverksresurser
Skapa ett virtuellt nätverk med kommandot [az network vnet create](/cli/azure/network/vnet). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät med namnet *mySubnet*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Använd kommandot [az network nsg create](/cli/azure/network/nsg) för att lägga till en nätverkssäkerhetsgrupp. I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli-interactive
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Skapa en nätverkssäkerhetsgruppregel med [az network nsg rule create](/cli/azure/network/nsg/rule). I följande exempel skapas en nätverkssäkerhetsgruppregel med namnet *myNetworkSecurityGroupRule*:

```azurecli-interactive
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuella nätverkskort skapas med [az network nic create](/cli/azure/network/nic). I följande exempel skapas tre virtuella nätverkskort. (Det vill säga ett virtuellt nätverkskort för varje virtuell dator som du skapar för din app i följande steg.) Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när du vill och lägga till dem i lastbalanseraren:

```azurecli
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

När du skapat de tre virtuella nätverkskorten går du vidare till nästa steg


## <a name="create-virtual-machines"></a>Skapa virtuella datorer

### <a name="create-cloud-init-config"></a>Skapa en cloud-init-konfiguration
I en tidigare självstudie om [hur du anpassar en virtuell Linux-dator vid den första starten](tutorial-automate-vm-deployment.md) lärde du dig att automatisera VM-anpassning med cloud-init. Du kan använda samma konfigurationsfil för cloud-init när du installerar NGINX och kör en enkel ”Hello World” Node.js-app i nästa steg. I slutet av självstudien kan du se hur lastbalanseraren fungerar genom att öppna den här enkla appen i en webbläsare.

I ditt nuvarande gränssnitt skapar du en fil med namnet *cloud-init.txt* och klistrar in följande konfiguration. Skapa till exempel inte filen i Cloud Shell på din lokala dator. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app. Mer information om tillgänglighetsuppsättningar finns i den tidigare självstudien [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md).

Skapa en tillgänglighets uppsättning med [AZ VM Availability-set Create](/cli/azure/vm/availability-set). I följande exempel skapas en tillgänglighetsuppsättning med namnet *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Nu kan du skapa de virtuella datorerna med [az vm create](/cli/azure/vm). I följande exempel skapas tre virtuella datorer och SSH-nycklar om de inte redan finns:

```azurecli
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Parametern `--no-wait` väntar inte på att alla uppgifter slutförs. Det kan ta några minuter innan du kan öppna appen. Lastbalanserarens hälsoavsökning känner automatiskt av när appen körs på varje virtuell dator. När appen körs börjar lastbalanserarens regel att distribuera trafiken.


## <a name="test-load-balancer"></a>Testa lastbalanseraren
Hämta den offentliga IP-adressen för lastbalanseraren med [az network public-ip show](/cli/azure/network/public-ip). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```azurecli-interactive
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Kom ihåg att det kan ta några minuter innan de virtuella datorerna är klara och lastbalanseraren börjar distribuera trafik till dem. Appen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra Node.js-app](./media/tutorial-load-balancer/running-nodejs-app.png)

Om du vill se hur lastbalanseraren distribuerar trafik över alla tre virtuella datorer som kör din app, kan du framtvinga uppdatering av webbläsaren.


## <a name="add-and-remove-vms"></a>Lägga till och ta bort virtuella datorer
Du kan behöva utföra underhåll på de virtuella datorerna som kör appen, till exempel installera uppdateringar av operativsystemet. För att klara ökad trafik till din app kan du behöva lägga till fler virtuella datorer. I det här avsnittet visas hur du tar bort eller lägger till en virtuell dator från lastbalanseraren.

### <a name="remove-a-vm-from-the-load-balancer"></a>Ta bort en virtuell dator från lastbalanseraren
Du kan ta bort en virtuell dator från serverdelsadresspoolen med [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool). Följande exempel tar bort det virtuella nätverkskortet för **myVM2** från *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Om du vill se hur lastbalanseraren distribuerar trafik över de två återstående virtuella datorerna som kör din app, kan du framtvinga uppdatering av webbläsaren. Nu kan du utföra underhåll på den virtuella datorn, till exempel installera uppdateringar av operativsystemet eller göra en omstart av den virtuella datorn.

Om du vill visa en lista med virtuella nätverkskort som är anslutna till lastbalanseraren använder du [az network lb address-pool show](/cli/azure/network/lb/address-pool). Fråga och filtrera på det virtuella nätverkskortets ID på följande sätt:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

Utdata liknar följande exempel som visar att det virtuella nätverkskortet för VM 2 inte längre ingår i serverdelsadresspoolen:

```output
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Lägga till en virtuell dator i lastbalanseraren
När du utfört underhåll på en virtuell dator eller om kapaciteten måste utökas kan du lägga till en virtuell dator i serverdelsadresspoolen med [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool). Följande exempel lägger till det virtuella nätverkskortet för **myVM2** i *myLoadBalancer*:

```azurecli-interactive
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Verifiera att det virtuella nätverkskortet är kopplat till serverdelsadresspoolen genom att köra [az network lb address-pool show](/cli/azure/network/lb/address-pool) från föregående steg igen.


## <a name="next-steps"></a>Nästa steg
I den här kursen har du skapat en lastbalanserare och kopplat virtuella datorer till den. Du har lärt dig att:

> [!div class="checklist"]
> * skapa en Azure Load Balancer
> * skapa en hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * Använda cloud-init för att skapa en grundläggande Node.js-app
> * skapa virtuella datorer och anslut dem till en lastbalanserare
> * visa en lastbalanserare i praktiken
> * lägga till och ta bort virtuella datorer från en lastbalanserare.

Gå vidare till nästa självstudiekurs och lär dig mer om virtuella nätverkskomponenter i Azure.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer och virtuella nätverk](tutorial-virtual-network.md)
