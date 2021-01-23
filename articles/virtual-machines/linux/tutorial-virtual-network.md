---
title: Självstudie – Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer
description: I den här självstudien lär du dig hur du använder Azure CLI för att skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c806e5d1d9c9242b6ba57496327c672d7fed0654
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704497"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Självstudie: Skapa och hantera virtuella Azure-nätverk för virtuella Linux-datorer med Azure CLI

Azures virtuella datorer använder Azure-nätverk för intern och extern nätverkskommunikation. Den här självstudien visar hur du distribuerar två virtuella datorer och konfigurerar Azure-nätverk för dem. Exemplen i den här självstudien förutsätter att de virtuella datorerna är värd för ett webbprogram med databasens serverdel, men något program behöver inte ha distribuerats i självstudien. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen

I den här självstudien används CLI i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Nätverksöversikt för VM

Virtuella Azure-nätverk möjliggör säkra nätverks anslutningar mellan virtuella datorer, Internet och andra Azure-tjänster, till exempel Azure SQL Database. Virtuella nätverk är uppdelade i logiska segment som kallas undernät. Undernät används för att kontrollera nätverksflödet och som en säkerhetsgräns. När du distribuerar en virtuell dator ingår vanligtvis ett virtuellt nätverksgränssnitt som är anslutet till ett undernät.

I den här självstudiekursen skapas följande virtuella nätverksresurser:

![Virtuellt nätverk med två undernät](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* – Det virtuella nätverk som de virtuella datorerna använder för att kommunicera med varandra och Internet.
- *myFrontendSubnet* – Undernät i *myVNet* som används av klientdelsresurserna.
- *myPublicIPAddress* – Offentlig IP-adress som används för att få åtkomst till *myFrontendVM* från Internet.
- *myFrontentNic* – Nätverksgränssnitt som används av *myFrontendVM* till att kommunicera med *myBackendVM*.
- *myFrontendVM* – Den virtuella dator som används för att kommunicera mellan Internet och *myBackendVM*.
- *myBackendNSG* – Nätverkssäkerhetsgruppen som styr kommunikationen mellan *myFrontendVM* och *myBackendVM*.
- *myBackendSubnet* – Det undernät som är associerat med *myBackendNSG* och används av serverdelsresurserna.
- *myBackendNic* – Nätverksgränssnittet som används av *myBackendVM* till att kommunicera med *myFrontendVM*.
- *myBackendVM* – Den virtuella dator som använder port 22 och 3306 för att kommunicera med *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

I den här självstudien skapas ett enda virtuellt nätverk med två undernät. Ett klientdelsundernät som är värd för ett webbprogram och ett serverdelsundernät som är värd för en databasserver.

Innan du kan skapa ett virtuellt nätverk skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myRGNetwork* på platsen för öster.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

Använd kommandot [az network vnet create](/cli/azure/network/vnet) för att skapa ett virtuellt nätverk. I det här exemplet heter nätverket *mvVNet* och har adressprefixet *10.0.0.0/16*. Dessutom skapas ett undernät med namnet *myFrontendSubnet* och prefixet *10.0.1.0/24*. Senare i den här självstudien ansluts klientdelens VM till det här undernätet. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Skapa undernät

Ett nytt undernät läggs till i det virtuella nätverket med hjälp av kommandot [az network vnet subnet create](/cli/azure/network/vnet/subnet). I det här exemplet heter undernätet *myBackendSubnet* och har adressprefixet *10.0.2.0/24*. Det här undernätet används med alla serverdelstjänster.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Ett nätverk har nu skapats och delats upp i två undernät, ett för klientdelstjänster och ett för serverdelstjänster. I nästa avsnitt skapar vi virtuella datorer och ansluter dem till dessa undernät.

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Med en offentlig IP-adress blir Azure-resurser tillgängliga på Internet. Allokeringsmetoden för den offentliga IP-adressen kan konfigureras som dynamisk eller statisk. Som standard blir en offentlig IP-adress dynamiskt allokerad. Dynamiska IP-adresser släpps när de virtuella datorerna frigörs. Detta medför IP-adressen ändras vid åtgärder som innebär att en virtuell dator frigörs.

Allokeringsmetoden kan anges som statisk, vilket garanterar att IP-adressen förblir tilldelad till en virtuell dator, även när den frigjorts. Det går inte att ange IP-adressen när du använder en statiskt tilldelad IP-adress. I stället allokeras den från en pool med tillgängliga adresser.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

När du skapar en virtuell dator med kommandot [az vm create](/cli/azure/vm) är den offentliga IP-adressens allokeringsmetod dynamisk. När du skapar en virtuell dator med kommandot [az vm create](/cli/azure/vm) ska du inkludera argumentet `--public-ip-address-allocation static` som tilldelar en statisk offentlig IP-adress. Den här åtgärden visas inte i den här självstudien, men i nästa avsnitt ändras en dynamiskt allokerad IP-adress till en statiskt allokerad adress. 

### <a name="change-allocation-method"></a>Ändra allokeringsmetod

Allokeringsmetoden för IP-adresser kan ändras med kommandot [az network public-ip update](/cli/azure/network/public-ip). I det här exemplet ändras allokeringsmetoden för IP-adresser i klientdelen av den virtuella datorn till statisk.

Frigör först den virtuella datorn.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Använd kommandot [az network public-ip update](/cli/azure/network/public-ip) till att uppdatera allokeringsmetoden. I det här fallet anges `--allocation-method` som *statisk*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Starta den virtuella datorn.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Ingen offentlig IP-adress

Ofta behöver inte en virtuell dator vara tillgänglig via Internet. Om du vill skapa en virtuell dator utan en offentlig IP-adress, använder du argumentet `--public-ip-address ""` med en tom uppsättning av dubbla citattecken. Den här konfigurationen visas längre fram i självstudien.

## <a name="create-a-front-end-vm"></a>Skapa en virtuell dator för klientdelen

Använd kommandot [az vm create](/cli/azure/vm) för att skapa den virtuella datorn med namnet *myFrontendVM* med hjälp av *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Skydda nätverkstrafiken

En nätverkssäkerhetsgrupp (NSG) innehåller en lista över säkerhetsregler som tillåter eller nekar nätverkstrafik till resurser som är anslutna till virtuella Azure-nätverk (VNet). NSG:er kan vara associerade med undernät eller separata nätverksgränssnitt. När en NSG är associerad med ett nätverksgränssnitt, tillämpar den bara den associerade virtuella datorn. När en nätverkssäkerhetsgrupp är kopplad till ett undernät gäller reglerna för alla resurser som är anslutna till undernätet. 

### <a name="network-security-group-rules"></a>Regler för nätverkssäkerhetsgrupp

NSG-regler definierar nätverksportar där trafik tillåts eller nekas. Reglerna kan innehålla käll- och målintervall för IP-adresser så att trafiken styrs mellan specifika system eller undernät. NSG-regler kan även innehålla en prioritet (mellan 1 och 4 096). Reglerna utvärderas i prioritetsordning. En regel med en prioritet på 100 utvärderas före en regel med prioritet 200.

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar.

Standardreglerna för NSG:er är:

- **Virtuellt nätverk** – Trafik som kommer från eller som går till ett virtuellt nätverk tillåts både i inkommande och utgående riktning.
- **Internet** – Utgående trafik tillåts, men inkommande trafik blockeras.
- **Lastbalanserare** – Tillåter att Azures lastbalanserare avsöker hälsotillståndet för dina virtuella datorer och rollinstanser. Du kan åsidosätta den här regeln om du inte använder någon belastningsutjämnad uppsättning.

### <a name="create-network-security-groups"></a>Skapa nätverkssäkerhetsgrupper

En nätverkssäkerhetsgrupp kan skapas samtidigt som en virtuell dator med hjälp av kommandot [az vm create](/cli/azure/vm). När du gör detta associeras NSG:n till nätverksgränssnittet för de virtuella datorerna och en NSG-regel skapas automatiskt för att tillåta trafik på port *22* från andra källor. Tidigare i den här självstudien har NSG:n för klientdelen skapats automatiskt med den virtuella datorns klientdel. En NSG-regel har också skapats automatiskt för port 22. 

I vissa fall kan vara det bra att skapa en NSG i förväg, till exempel när standardreglerna för SSH inte går att skapa eller när NSG:n ska kopplas till ett undernät. 

Använd kommandot [az network nsg create](/cli/azure/network/nsg) för att skapa en nätverkssäkerhetsgrupp.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

I stället för att associera NSG med ett nätverksgränssnitt, är den associerad med ett undernät. I den här konfigurationen kommer alla virtuella datorer som är anslutna till undernätet ärva NSG-reglerna.

Uppdatera det befintliga undernätet med namnet *myBackendSubnet* med den nya NSG:n.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Skydda inkommande trafik

När klientdelen i den virtuella datorn skapades, skapades en NSG-regel som tillåter inkommande trafik på port 22. Den här regeln tillåter SSH-anslutningar till den virtuella datorn. I det här exemplet tillåts trafik även på port *80*. Med den här konfigurationen kan en webbapp nås på den virtuella datorn.

Använd kommandot [az network nsg rule create](/cli/azure/network/nsg/rule) för att skapa en regel för port *80*.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Klientdelen i den virtuella datorn är endast tillgänglig på port *22* och port *80*. All annan inkommande trafik blockeras i nätverkssäkerhetsgruppen. Det kan vara bra att visualisera regelkonfigurationerna för NSG. Returnera NSG-regelkonfigurationen med kommandot [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Säker trafik mellan virtuella datorer

Regler för nätverkssäkerhetsgrupper kan också användas mellan virtuella datorer. I det här exemplet behöver klientdelens VM kommunicera med serverdelens VM på port *22* och *3306*. Konfigurationen tillåter SSH-anslutningar från klientdelens VM och även att ett program på klientdelens virtuella dator kommunicerar med en MySQL-databas i serverdelen. All annan trafik blockeras mellan klientdelens och serverdelens virtuella datorer.

Använd kommandot [az network nsg rule create](/cli/azure/network/nsg/rule) för att skapa en regel för port 22. Observera att argumentet `--source-address-prefix` anger värdet *10.0.1.0/24*. Den här konfigurationen säkerställer att endast trafik från klientdelens undernät tillåts via NSG:n.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Lägg nu till regel för MySQL-trafik på port 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Eftersom NSG:er har en standardregel som tillåter all trafik mellan virtuella datorer i samma virtuella nätverk, skapas slutligen en regel för serverdelens NSG:er som blockerar all trafik. Observera att `--priority` får värdet *300*, vilket är lägre än både MySQL- och NSG-reglerna. Konfigurationen innebär att SSH- och MySQL-trafiken fortfarande tillåts via NSG:n.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Skapa en virtuell dator för serverdelen

Nu ska du skapa en virtuell dator som är ansluten till *myBackendSubnet*. Observera att argumentet `--nsg` har ett värde med tomma dubbla citattecken. Du behöver inte skapa någon NSG med den virtuella datorn. Den virtuella datorn är ansluten till serverdelens undernät, som skyddas med den NSG för serverdelen som skapades i förväg. Den här NSG:n gäller för den virtuella datorn. Observera också att argumentet `--public-ip-address` har ett värde med tomma dubbla citattecken. Den här konfigurationen skapar en virtuell dator utan en offentlig IP-adress. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Serverdelen i den virtuella datorn är endast tillgänglig på port *22* och port *3306* från klientdelens undernät. All annan inkommande trafik blockeras i nätverkssäkerhetsgruppen. Det kan vara bra att visualisera regelkonfigurationerna för NSG. Returnera NSG-regelkonfigurationen med kommandot [az network rule list](/cli/azure/network/nsg/rule). 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat och skyddat Azure-nätverk som är relaterade till virtuella datorer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och ett undernät
> * Skapa en offentlig IP-adress
> * Skapa en virtuell dator för klientdelen
> * Skydda nätverkstrafiken
> * Skapa en virtuell dator för serverdelen

Information om hur du skyddar dina virtuella dator diskar finns i [säkerhets kopiering och haveri beredskap för diskar](backup-and-disaster-recovery-for-azure-iaas-disks.md).
