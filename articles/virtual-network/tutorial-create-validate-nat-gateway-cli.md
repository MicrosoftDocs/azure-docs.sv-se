---
title: Skapa och testa en NAT-gateway – Azure CLI
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med Azure CLI och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 457749b353b9b7fabfb137ebe907463bb17158ba
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98926942"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-cli-and-test-the-nat-service"></a>Självstudie: skapa en NAT-gateway med Azure CLI och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell käll-och mål dator. Du testar NAT-gatewayen genom att göra utgående anslutningar till en offentlig IP-adress. Anslutningarna kommer från källan till den virtuella mål datorn. I den här självstudien distribueras källa och mål i två olika virtuella nätverk i samma resurs grupp för enkelhetens skull.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.71 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
    
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att få åtkomst till det offentliga Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPsource \
  --sku standard
  
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller både och NAT-gateway. Vi lägger till en resurs för offentliga IP-prefix i det här scenariot för att demonstrera.   Använd [AZ Network Public-IP prefix Create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) för att skapa en offentlig IP-prefixlängd som heter **myPublicIPprefixsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip prefix create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPprefixsource \
  --length 31
  
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [AZ Network NAT gateway Create](/cli/azure/network/nat) med namnet **myNATgateway**. Kommandot använder både den offentliga IP- **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Kommandot ändrar även tids gränsen för inaktivitet till 10 minuter.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIPsource \
    --public-ip-prefixes myPublicIPprefixsource \
    --idle-timeout 10       
    
  ```

NAT-gatewayen är nu funktionell och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda det.

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbered källan för utgående trafik

Vi vägleder dig genom installationen av en fullständig test miljö. Du måste konfigurera ett test med hjälp av verktyg med öppen källkod för att verifiera NAT-gatewayen. Vi börjar med källan, som kommer att använda NAT-gatewayen som vi skapade tidigare.

### <a name="configure-virtual-network-for-source"></a>Konfigurera virtuellt nätverk för källa

Innan du distribuerar en virtuell dator och kan testa din NAT-gateway måste vi skapa det virtuella nätverket.

Skapa ett virtuellt nätverk med namnet **myVnetsource** med ett undernät med namnet **mySubnetsource** i **myResourceGroupNAT** med [AZ Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetsource \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetsource \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="configure-nat-service-for-source-subnet"></a>Konfigurera NAT-tjänst för käll under nät

Konfigurera käll under nätet **mySubnetsource** i det virtuella nätverket **myVnetsource** för att använda en speciell NAT-gateway resurs **myNATgateway** med [AZ Network Microsoft Azure Virtual Network Subnet Update](/cli/azure/network/vnet/subnet). Med det här kommandot aktive ras NAT-tjänsten på det angivna under nätet.

```azurecli-interactive
    az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnetsource \
    --name mySubnetsource \
    --nat-gateway myNATgateway
    
```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

Innan vi kan testa NAT-gatewayen måste vi skapa en virtuell käll dator.  Vi tilldelar en offentlig IP-adressresurs som en offentlig IP-adress på instans nivå för att få åtkomst till den här virtuella datorn från utsidan. Den här adressen används bara för att komma åt den för testet.  Vi visar hur NAT-tjänsten prioriteras över andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP-adress och skapa en annan virtuell dator som du kan använda som en hoppning utan en offentlig IP-adress som en övning.

### <a name="create-public-ip-for-source-vm"></a>Skapa en offentlig IP-adress för den virtuella käll datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella käll datorn. Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsourceVM** i **myResourceGroupNAT**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --sku standard
    
```

### <a name="create-an-nsg-for-source-vm"></a>Skapa en NSG för den virtuella käll datorn

Eftersom standard-offentliga IP-adresser är "säkra som standard", måste vi skapa en NSG för att tillåta inkommande åtkomst för SSH-åtkomst.  Azure NAT-tjänsten är en flödes riktning som är medveten. Den här NSG används inte för utgående trafik när NAT-gatewayen har kon figurer ATS i samma undernät. Använd [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSGsource** i **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGsource 
    
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>Exponera SSH-slutpunkt på den virtuella käll datorn

Vi skapar en regel i NSG för SSH-åtkomst till den virtuella käll datorn. Använd [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **SSH**. Den här regeln skapas i NSG med namnet **myNSGsource** i resurs gruppen **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGsource \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="create-nic-for-source-vm"></a>Skapa ett nätverkskort för den virtuella käll datorn

Skapa ett nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP-adressen och nätverks säkerhets gruppen. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicsource \
    --vnet-name myVnetsource \
    --subnet mySubnetsource \
    --public-ip-address myPublicIPSourceVM \
    --network-security-group myNSGsource
    
```

### <a name="create-a-source-vm"></a>Skapa en virtuell käll dator

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create).  Vi genererar SSH-nycklar för den här virtuella datorn och lagrar den privata nyckeln för senare användning.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMsource \
    --nics myNicsource \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```

Kommandot kommer att returneras omedelbart, men det kan ta några minuter innan den virtuella datorn har distribuerats.

## <a name="prepare-destination-for-outbound-traffic"></a>Förbered mål för utgående trafik

Nu ska vi skapa ett mål för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.

### <a name="configure-virtual-network-for-destination"></a>Konfigurera virtuellt nätverk för mål

 Vi måste skapa ett virtuellt nätverk där den virtuella mål datorn kommer att vara.  Dessa kommandon är samma steg som för den virtuella käll datorn med små ändringar för att exponera mål slut punkten.

Skapa ett virtuellt nätverk med namnet **myVnetdestination** med ett undernät med namnet **mySubnetdestination** i **myResourceGroupNAT** med [AZ Network Microsoft Azure Virtual Network Create](/cli/azure/network/vnet).  IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --name myVnetdestination \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetdestination \
    --subnet-prefix 192.168.0.0/24
    
```

### <a name="create-public-ip-for-destination-vm"></a>Skapa en offentlig IP-adress för den virtuella mål datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella käll datorn. Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPdestinationVM** i **myResourceGroupNAT**. 

```azurecli-interactive
  az network public-ip create \
  --resource-group myResourceGroupNAT \
  --name myPublicIPdestinationVM \
  --sku standard
  
```

### <a name="create-an-nsg-for-destination-vm"></a>Skapa en NSG för den virtuella mål datorn

Standard offentliga IP-adresser är säkra som standard. du måste skapa en NSG för att tillåta inkommande åtkomst för SSH. Tjänsten Azure NAT är en flödes riktning som är medveten om. Den här NSG används inte för utgående trafik när NAT-gatewayen har kon figurer ATS i samma undernät. Använd [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create) för att skapa en NSG-resurs med namnet **myNSGdestination** i **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSGdestination
    
```

### <a name="expose-ssh-endpoint-on-destination-vm"></a>Exponera SSH-slutpunkt på den virtuella mål datorn

Vi skapar en regel i NSG för SSH-åtkomst till den virtuella mål datorn. Använd [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **SSH**. Den här regeln skapas i NSG med namnet **myNSGdestination** i resurs gruppen **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
    
```

### <a name="expose-http-endpoint-on-destination-vm"></a>Exponera HTTP-slutpunkt på den virtuella mål datorn

Vi skapar en regel i NSG för HTTP-åtkomst till den virtuella mål datorn. Använd [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) för att skapa en NSG-regel med namnet **http** i NSG som heter **myNSGdestination** i **myResourceGroupNAT**.

```azurecli-interactive
    az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSGdestination \
    --priority 101 \
    --name http \
    --description "HTTP access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 80
    
```

### <a name="create-nic-for-destination-vm"></a>Skapa ett nätverkskort för den virtuella mål datorn

Skapa ett nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create) och associera med den offentliga IP- **myPublicIPdestinationVM** och nätverks säkerhets gruppen **myNSGdestination**. 

```azurecli-interactive
    az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNicdestination \
    --vnet-name myVnetdestination \
    --subnet mySubnetdestination \
    --public-ip-address myPublicIPdestinationVM \
    --network-security-group myNSGdestination
    
```

### <a name="create-a-destination-vm"></a>Skapa en virtuell måldator

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create).  Vi genererar SSH-nycklar för den här virtuella datorn och lagrar den privata nyckeln för senare användning.

 ```azurecli-interactive
    az vm create \
    --resource-group myResourceGroupNAT \
    --name myVMdestination \
    --nics myNicdestination \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
    
```
Kommandot kommer att returneras omedelbart, men det kan ta några minuter innan den virtuella datorn har distribuerats.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbered en webb server och testa nytto lasten på den virtuella mål datorn

Först måste vi identifiera IP-adressen för den virtuella mål datorn.  Använd [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show)för att hämta den offentliga IP-adressen för den virtuella mål datorn. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPdestinationVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella mål datorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på den virtuella mål datorn

SSH-autentiseringsuppgifterna ska lagras i Cloud Shell från föregående åtgärd.  Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn. 

```bash
ssh <ip-address-destination>
```

Kopiera och klistra in följande kommandon när du har loggat in.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

De här kommandona kommer att uppdatera den virtuella datorn, installera nginx och skapa en 100-KByte-fil. Den här filen kommer att hämtas från den virtuella käll datorn med hjälp av NAT-tjänsten.

Stäng SSH-sessionen med den virtuella mål datorn.

## <a name="prepare-test-on-source-vm"></a>Förbered testet på den virtuella käll datorn

Först måste vi identifiera IP-adressen för den virtuella käll datorn.  Om du vill hämta den offentliga IP-adressen för den virtuella käll datorn använder du [AZ Network Public-IP show](/cli/azure/network/public-ip#az-network-public-ip-show). 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPsourceVM \
    --query [ipAddress] \
    --output tsv
    
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella käll datorn.

### <a name="sign-in-to-source-vm"></a>Logga in på den virtuella käll datorn

SSH-autentiseringsuppgifterna lagras på nytt i Cloud Shell. Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn. 

```bash
ssh <ip-address-source>
```

Kopiera och klistra in följande kommandon för att förbereda för att testa NAT-tjänsten.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Det här kommandot kommer att uppdatera den virtuella datorn, installera Go, installera [Hej](https://github.com/rakyll/hey) från GitHub och uppdatera din gränssnitts miljö.

Nu är du redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Verifiera NAT-tjänst

När du har loggat in på den virtuella käll datorn kan du använda **sväng** och **Hej** för att generera begär anden till målets IP-adress.

Använd sväng för att hämta filen 100-KByte.  Ersätt **\<ip-address-destination>** i exemplet nedan med mål-IP-adressen som du har kopierat tidigare.  Parametern **--output** anger att den hämtade filen kommer att tas bort.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Du kan också skapa en serie förfrågningar med hjälp av **Hej**. Ersätt igen **\<ip-address-destination>** med mål-IP-adressen som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Det här kommandot genererar 100-begäranden, 10 samtidigt, med en tids gräns på 30 sekunder. TCP-anslutningen återanvänds inte.  Varje begäran kommer att hämta 100 kByte.  I slutet av körningen rapporterar **Hej** viss statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurecli-interactive 
  az group delete --name myResourceGroupNAT
  
```

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en NAT-gateway, skapat en källa och en virtuell måldator och testat NAT-gatewayen.

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs överbelastning av SNAT-portar kan enkelt åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller båda.

- Läs mer om [Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]
