---
title: Konfigurera privat slutpunkt med privat länk
description: Konfigurera en privat slutpunkt i ett containerregister och aktivera åtkomst via en privat länk i ett lokalt virtuellt nätverk. Privat länkåtkomst är en funktion på Premium-tjänstnivån.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: d3c7c573b0ffc08a85f5cbe5cc62d3f7c052f0af
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781441"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Ansluta privat till ett Azure-containerregister med Azure Private Link


Begränsa åtkomsten till ett register genom att tilldela privata IP-adresser för virtuella nätverk till registerslutpunkterna och använda [Azure Private Link](../private-link/private-link-overview.md). Nätverkstrafiken mellan klienterna i det virtuella nätverket och registrets privata slutpunkter passerar det virtuella nätverket och en privat länk i Microsofts stamnätverk, vilket eliminerar exponeringen från det offentliga Internet. Private Link ger även åtkomst till privata register från en lokal plats [via Azure ExpressRoute](../expressroute/expressroute-introduction.MD) privat peering eller en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Du kan [konfigurera DNS-inställningar](../private-link/private-endpoint-overview.md#dns-configuration) för registrets privata slutpunkter så att inställningarna matchar registrets allokerade privata IP-adress. Med DNS-konfiguration kan klienter och tjänster i nätverket fortsätta att komma åt registret på registrets fullständigt kvalificerade domännamn, till exempel *myregistry.azurecr.io*. 

Den här funktionen är tillgänglig på **tjänstnivån** premiumcontainerregister. För närvarande kan högst 10 privata slutpunkter konfigureras för ett register. Information om registertjänstnivåer och begränsningar finns i [Azure Container Registry nivåer](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Förutsättningar

* Om du vill använda Azure CLI-stegen i den här artikeln rekommenderas Azure CLI version 2.6.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett containerregister skapar du [](container-registry-import-images.md) ett (Premium-nivå krävs) och importerar en offentlig exempelavbildning, till `mcr.microsoft.com/hello-world` exempel från Microsoft Container Registry. Du kan till exempel använda [Azure Portal][quickstart-portal] eller [Azure CLI för][quickstart-cli] att skapa ett register.
* Om du vill konfigurera registeråtkomst med hjälp av en privat länk i en annan Azure-prenumeration måste du registrera resursprovidern för Azure Container Registry i prenumerationen. Exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Azure CLI-exemplen i den här artikeln använder följande miljövariabler. Ersätt de värden som är lämpliga för din miljö. Alla exempel är formaterade för Bash-gränssnittet:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Konfigurera privat länk – CLI

### <a name="get-network-and-subnet-names"></a>Hämta nätverks- och undernätsnamn

Om du inte redan har dem behöver du namnen på ett virtuellt nätverk och undernät för att konfigurera en privat länk. I det här exemplet använder du samma undernät för den virtuella datorn och registrets privata slutpunkt. I många scenarier skulle du dock konfigurera slutpunkten i ett separat undernät. 

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resursgrupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel ger den virtuella datorn namnet *myDockerVM* är standardnamnet för det virtuella nätverket *myDockerVMVNET*, med ett undernät med namnet *myDockerVMSubnet*. Ange dessa värden i miljövariabler genom att köra [kommandot az network vnet list:][az-network-vnet-list]

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Inaktivera nätverksprinciper i undernät

[Inaktivera nätverksprinciper,](../private-link/disable-private-endpoint-network-policy.md) till exempel nätverkssäkerhetsgrupper i undernätet för den privata slutpunkten. Uppdatera undernätskonfigurationen med [az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

Skapa en [privat Azure DNS för](../dns/private-dns-privatednszone.md) den privata Azure-containerregisterdomänen. I senare steg skapar du DNS-poster för registerdomänen i den här DNS-zonen. Mer information finns i [DNS-konfigurationsalternativ senare](#dns-configuration-options)i den här artikeln.

Om du vill använda en privat zon för att åsidosätta DNS-standardmatchningen för Ditt Azure-containerregister måste zonen ha **namnet privatelink.azurecr.io**. Kör följande az [network private-dns zone create-kommando][az-network-private-dns-zone-create] för att skapa den privata zonen:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Skapa en kopplingslänk

Kör [az network private-dns link vnet create för][az-network-private-dns-link-vnet-create] att associera din privata zon med det virtuella nätverket. I det här exemplet skapas en länk med *namnet myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Skapa en slutpunkt för privat register

I det här avsnittet skapar du registrets privata slutpunkt i det virtuella nätverket. Hämta först resurs-ID:t för ditt register:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Kör kommandot [az network private-endpoint create][az-network-private-endpoint-create] för att skapa registrets privata slutpunkt.

I följande exempel skapas slutpunkten *myPrivateEndpoint och* tjänstanslutningen *myConnection*. Om du vill ange en containerregisterresurs för slutpunkten skickar du `--group-ids registry` :

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>Hämta IP-konfiguration för slutpunkt

Om du vill konfigurera DNS-poster hämtar du IP-konfigurationen för den privata slutpunkten. Associerade med den privata slutpunktens nätverksgränssnitt i det här exemplet är två privata IP-adresser för containerregistret: en för själva registret och en för registrets dataslutpunkt. 

Kör först [az network private-endpoint show för att][az-network-private-endpoint-show] fråga den privata slutpunkten efter nätverksgränssnitts-ID:t:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Följande [az network nic show-kommandon][az-network-nic-show] hämtar de privata IP-adresserna för containerregistret och registrets dataslutpunkt:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

> [!NOTE]
> Om registret är [geo-replikerat frågar](container-registry-geo-replication.md)du efter den ytterligare dataslutpunkten för varje registerreplik.

### <a name="create-dns-records-in-the-private-zone"></a>Skapa DNS-poster i den privata zonen

Följande kommandon skapar DNS-poster i den privata zonen för registerslutpunkten och dess dataslutpunkt. Om du till exempel har ett register med *namnet myregistry* i *regionen westeurope* är slutpunktsnamnen `myregistry.azurecr.io` och `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Om registret är [geo-replikerat skapar](container-registry-geo-replication.md)du ytterligare DNS-poster för varje repliks dataslutpunkts-IP.

Kör först [az network private-dns record-set a create för][az-network-private-dns-record-set-a-create] att skapa tomma A-postuppsättningar för registrets slutpunkt och dataslutpunkt:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Kör kommandot [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] för att skapa A-posterna för registerslutpunkten och dataslutpunkten:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Den privata länken är nu konfigurerad och klar att användas.

## <a name="set-up-private-link---portal"></a>Konfigurera privat länk – portalen

Konfigurera en privat länk när du skapar ett register eller lägg till en privat länk till ett befintligt register. Följande steg förutsätter att du redan har ett virtuellt nätverk och ett undernät som har ställts in med en virtuell dator för testning. Du kan också [skapa ett nytt virtuellt nätverk och undernät](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Skapa en privat slutpunkt – nytt register

1. När du skapar ett register i portalen går du **till fliken Grundläggande** inställningar och väljer **Premium** i **SKU.**
1. Välj fliken **Nätverk**.
1. I **Nätverksanslutning väljer** du **Privat slutpunkt**+  >  **Lägg till**.
1. Ange eller välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | Name | Ange ett unikt namn. |
    | Underkälla |Välj **register**|
    | **Nätverk** | |
    | Virtuellt nätverk| Välj det virtuella nätverk där den virtuella datorn har distribuerats, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn distribueras. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(nytt) privatelink.azurecr.io* |
    |||
1. Konfigurera de återstående registerinställningarna och välj sedan **Granska + skapa.**

  ![Skapa register med privat slutpunkt](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Skapa en privat slutpunkt – befintligt register

1. Navigera till containerregistret i portalen.
1. Under **Inställningar** väljer du **Nätverk.**
1. På fliken **Privata slutpunkter** väljer du **+ Privat slutpunkt.**
1. På **fliken Grundläggande anger** eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | **Instansinformation** |  |
    | Name | Ange ett namn. |
    |Region|Välj en region.|
    |||
5. Välj **Nästa: Resurs**.
6. Ange eller välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i min katalog**.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft.ContainerRegistry/registries**. |
    | Resurs |Välj namnet på registret|
    |Målunderkälla |Välj **register**|
    |||
7. Välj **Nästa: Konfiguration.**
8. Ange eller välj informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj det virtuella nätverk där den virtuella datorn har distribuerats, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn distribueras. |
    |**Privat DNS integration**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink.azurecr.io* |
    |||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration. 
2. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

När den privata slutpunkten har skapats visas DNS-inställningarna i den privata zonen på sidan **Privata** slutpunkter i portalen:

1. I portalen navigerar du till containerregistret och väljer **Inställningar > Nätverk**.
1. På fliken **Privata slutpunkter** väljer du den privata slutpunkt som du skapade.
1. På sidan **Översikt** granskar du länkinställningarna och de anpassade DNS-inställningarna.

  ![DNS-inställningar för slutpunkt](./media/container-registry-private-link/private-endpoint-overview.png)

Din privata länk är nu konfigurerad och klar att användas.

## <a name="disable-public-access"></a>Inaktivera offentlig åtkomst

I många scenarier inaktiverar du registeråtkomst från offentliga nätverk. Den här konfigurationen förhindrar att klienter utanför det virtuella nätverket når registerslutpunkterna. 

### <a name="disable-public-access---cli"></a>Inaktivera offentlig åtkomst – CLI

Om du vill inaktivera offentlig åtkomst med hjälp av Azure CLI kör [du az acr update][az-acr-update] och anger till `--public-network-enabled` `false` . 

> [!NOTE]
> Argumentet `public-network-enabled` kräver Azure CLI 2.6.0 eller senare. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Inaktivera offentlig åtkomst – portalen

1. I portalen navigerar du till containerregistret och väljer **Inställningar > Nätverk**.
1. På fliken **Offentlig åtkomst** går du till Tillåt **offentlig nätverksåtkomst och** väljer **Inaktiverad.** Välj sedan **Spara**.

## <a name="validate-private-link-connection"></a>Verifiera privat länkanslutning

Du bör kontrollera att resurserna i undernätet för den privata slutpunkten ansluter till ditt register via en privat IP-adress och har rätt integrering av den privata DNS-zonen.

För att verifiera den privata länkanslutningen SSH till den virtuella dator som du konfigurerade i det virtuella nätverket.

Kör ett verktyg som `nslookup` eller för att leta upp `dig` IP-adressen för ditt register via den privata länken. Exempel:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Exempelutdata visar registrets IP-adress i undernätets adressutrymme:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Jämför det här resultatet med den offentliga IP-adressen `dig` i utdata för samma register över en offentlig slutpunkt:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Registeråtgärder via privat länk

Kontrollera också att du kan utföra registeråtgärder från den virtuella datorn i undernätet. Skapa en SSH-anslutning till den virtuella datorn och kör [az acr login för][az-acr-login] att logga in i registret. Beroende på din VM-konfiguration kan du behöva lägga till prefixet för följande kommandon med `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Utför registeråtgärder som att `docker pull` hämta en exempelavbildning från registret. Ersätt `hello-world:v1` med en avbildning och tagg som är lämplig för registret, med prefixet registerinloggningsserverns namn (endast gemener):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

## <a name="manage-private-endpoint-connections"></a>Hantera privata slutpunktsanslutningar

Hantera ett registers privata slutpunktsanslutningar med Azure Portal eller med hjälp av kommandon i [kommandogruppen az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Åtgärderna omfattar att godkänna, ta bort, lista, avvisa eller visa information om ett registers privata slutpunktsanslutningar.

Om du till exempel vill visa en lista över privata slutpunktsanslutningar för ett register kör du kommandot [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Exempel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

När du ställer in en privat slutpunktsanslutning med hjälp av stegen i den här artikeln accepterar registret automatiskt anslutningar från klienter och tjänster som har Azure RBAC-behörigheter i registret. Du kan konfigurera slutpunkten så att den kräver manuellt godkännande av anslutningar. Information om hur du godkänner och avvisar privata slutpunktsanslutningar finns i [Hantera en privat slutpunktsanslutning.](../private-link/manage-private-endpoint.md)

> [!IMPORTANT]
> Om du tar bort en privat slutpunkt från ett register kan du för närvarande också behöva ta bort det virtuella nätverkets länk till den privata zonen. Om länken inte tas bort kan du se ett fel som liknar `unresolvable host` .

## <a name="dns-configuration-options"></a>DNS-konfigurationsalternativ

Den privata slutpunkten i det här exemplet integreras med en privat DNS-zon som är associerad med ett grundläggande virtuellt nätverk. Den här konfigurationen använder den Azure-tillhandahållna DNS-tjänsten direkt för att matcha registrets offentliga FQDN till dess privata IP-adresser i det virtuella nätverket. 

Private Link stöder ytterligare DNS-konfigurationsscenarier som använder den privata zonen, inklusive med anpassade DNS-lösningar. Du kan till exempel ha en anpassad DNS-lösning distribuerad i det virtuella nätverket eller lokalt i ett nätverk som du ansluter till det virtuella nätverket med hjälp av en VPN-gateway eller Azure ExpressRoute. 

För att lösa registrets offentliga FQDN till den privata IP-adressen i dessa scenarier måste du konfigurera en vidarebefordrare på servernivå till Azure DNS-tjänsten (168.63.129.16). De exakta konfigurationsalternativen och stegen beror på dina befintliga nätverk och DNS. Exempel finns i [DNS-konfiguration för privata slutpunkter i Azure.](../private-link/private-endpoint-dns.md)

> [!IMPORTANT]
> Om du för hög tillgänglighet har skapat privata slutpunkter i flera regioner rekommenderar vi att du använder en separat resursgrupp i varje region och placerar det virtuella nätverket och den associerade privata DNS-zonen i den. Den här konfigurationen förhindrar också oförutsägbar DNS-upplösning som orsakas av delning av samma privata DNS-zon.

### <a name="manually-configure-dns-records"></a>Konfigurera DNS-poster manuellt

I vissa fall kan du behöva konfigurera DNS-poster manuellt i en privat zon i stället för att använda den privata Azure-zonen. Se till att skapa poster för var och en av följande slutpunkter: registerslutpunkten, registrets dataslutpunkt och dataslutpunkten för ytterligare regionala repliker. Om alla poster inte har konfigurerats kan registret inte nås.

> [!IMPORTANT]
> Om du senare lägger till en ny replik måste du manuellt lägga till en ny DNS-post för dataslutpunkten i den regionen. Om du till exempel skapar en replik av *myregistry* på platsen northeurope lägger du till en post för `myregistry.northeurope.data.azurecr.io` .

FQDN och privata IP-adresser som du behöver för att skapa DNS-poster är associerade med den privata slutpunktens nätverksgränssnitt. Du kan hämta den här informationen med hjälp av Azure CLI eller från portalen:

* Använd Azure CLI och kör [kommandot az network nic show.][az-network-nic-show] Exempel på kommandon finns i Hämta [ip-konfiguration för slutpunkt](#get-endpoint-ip-configuration)tidigare i den här artikeln.

* I portalen navigerar du till din privata slutpunkt och väljer **DNS-konfiguration.**

När du har skapat DNS-poster kontrollerar du att registrets FQDN matchar deras respektive privata IP-adresser.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resursgrupp och inte längre behöver dem kan du ta bort resurserna med hjälp av ett enda [az group delete-kommando:](/cli/azure/group)

```azurecli
az group delete --name $RESOURCE_GROUP
```

Om du vill rensa dina resurser i portalen går du till resursgruppen. När resursgruppen har lästs in klickar du på **Ta bort resursgrupp för** att ta bort resursgruppen och de resurser som lagras där.

## <a name="next-steps"></a>Nästa steg

* Mer information om Private Link finns i [Azure Private Link](../private-link/private-link-overview.md) dokumentationen.

* Om du behöver konfigurera regler för registeråtkomst bakom en klientbrandvägg kan du se Konfigurera regler för åtkomst till [ett Azure-containerregister bakom en brandvägg.](container-registry-firewall-access-rules.md)

* [Felsöka anslutningsproblem för privata slutpunkter i Azure](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
