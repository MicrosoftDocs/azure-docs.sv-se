---
title: Konfigurera privat länk
description: Konfigurera en privat slut punkt i ett behållar register och aktivera åtkomst över en privat länk i ett lokalt virtuellt nätverk. Åtkomst till privata länkar är en funktion i Premium service-nivån.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93342308"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Anslut privat till ett Azure Container Registry med hjälp av Azure Private Link


Begränsa åtkomsten till ett register genom att tilldela privata IP-adresser för virtuella nätverk till register slut punkter och använda [Azures privata länk](../private-link/private-link-overview.md). Nätverks trafik mellan klienterna i det virtuella nätverket och registrets privata slut punkter går igenom det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet. Privat länk möjliggör även privat register åtkomst lokalt via [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) Private peering eller en [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

Du kan [Konfigurera DNS-inställningar](../private-link/private-endpoint-overview.md#dns-configuration) för registrets privata slut punkter så att inställningarna matchas mot registrets allokerade privata IP-adress. Med DNS-konfiguration kan klienter och tjänster i nätverket fortsätta att komma åt registret i registrets fullständigt kvalificerade domän namn, till exempel *myregistry.azurecr.io*. 

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. För närvarande kan högst 10 privata slut punkter konfigureras för ett register. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry-nivåer](container-registry-skus.md).

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Förutsättningar

* För att kunna använda Azure CLI-stegen i den här artikeln rekommenderas Azure CLI version 2.6.0 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett behållar register skapar du ett (Premium-nivå krävs) och [importerar](container-registry-import-images.md) en offentlig exempel bild, till exempel `mcr.microsoft.com/hello-world` från Microsoft container Registry. Använd till exempel [Azure Portal][quickstart-portal] eller [Azure CLI][quickstart-cli] för att skapa ett register.
* Om du vill konfigurera register åtkomst med en privat länk i en annan Azure-prenumeration måste du registrera resurs leverantören för Azure Container Registry i den prenumerationen. Exempel:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Azure CLI-exemplen i den här artikeln använder följande miljövariabler. Ersätt värden som passar din miljö. Alla exempel är formaterade för bash-gränssnittet:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Konfigurera privat länk – CLI

### <a name="get-network-and-subnet-names"></a>Hämta nätverks-och under nät namn

Om du inte redan har dem behöver du namnen på ett virtuellt nätverk och undernät för att skapa en privat länk. I det här exemplet använder du samma undernät för den virtuella datorn och registrets privata slut punkt. I många scenarier skulle du dock konfigurera slut punkten i ett separat undernät. 

När du skapar en virtuell dator skapar Azure som standard ett virtuellt nätverk i samma resurs grupp. Namnet på det virtuella nätverket baseras på namnet på den virtuella datorn. Om du till exempel namnger din virtuella dator *myDockerVM* är det virtuella standard nätverks namnet *myDockerVMVNET*, med ett undernät som heter *myDockerVMSubnet*. Ange dessa värden i miljövariabler genom att köra kommandot [AZ Network VNet List][az-network-vnet-list] :

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

### <a name="disable-network-policies-in-subnet"></a>Inaktivera nätverks principer i undernät

[Inaktivera nätverks principer](../private-link/disable-private-endpoint-network-policy.md) som nätverks säkerhets grupper i under nätet för den privata slut punkten. Uppdatera din under näts konfiguration med [AZ Network VNet Subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

Skapa en [privat DNS-zon](../dns/private-dns-privatednszone.md) för den privata Azure Container Registry-domänen. I senare steg skapar du DNS-poster för din register domän i den här DNS-zonen.

Om du vill använda en privat zon för att åsidosätta standard-DNS-matchningen för Azure Container Registry måste zonen ha namnet **privatelink.azurecr.io**. Kör följande [AZ-nätverk privat-DNS Zone-][az-network-private-dns-zone-create] kommando för att skapa den privata zonen:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Skapa en kopplings länk

Kör [AZ Network Private-DNS Link VNet Create][az-network-private-dns-link-vnet-create] för att associera din privata zon med det virtuella nätverket. I det här exemplet skapas en länk som heter *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Skapa en privat slut punkt för registret

I det här avsnittet skapar du registrets privata slut punkt i det virtuella nätverket. Börja med att hämta resurs-ID för registret:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Kör kommandot [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] för att skapa registrets privata slut punkt.

I följande exempel skapas *myPrivateEndpoint* för slut punkt och tjänst *anslutning.* Om du vill ange en container Registry-resurs för slut punkten, pass `--group-ids registry` :

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

### <a name="get-private-ip-addresses"></a>Hämta privata IP-adresser

Kör [AZ Network Private-Endpoint show][az-network-private-endpoint-show] för att fråga slut punkten för nätverks gränssnittets ID:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associerad med nätverks gränssnittet i det här exemplet är två privata IP-adresser för behållar registret: ett för själva registret och ett för registrets data slut punkt. Följande [AZ-resurs visar][az-resource-show] kommandon hämtar de privata IP-adresserna för behållar registret och registrets data slut punkt:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Om ditt register är [geo-replikerat](container-registry-geo-replication.md)frågar du efter den ytterligare data slut punkten för varje register replik.

### <a name="create-dns-records-in-the-private-zone"></a>Skapa DNS-poster i den privata zonen

Följande kommandon skapar DNS-poster i den privata zonen för register slut punkten och dess data slut punkt. Om du till exempel har ett register med namnet " *register* " i *westeurope* -regionen är slut punkts namnen `myregistry.azurecr.io` och `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Om registret är [geo-replikerat](container-registry-geo-replication.md)skapar du ytterligare DNS-poster för varje repliks data slut punkt IP.

Börja med att köra [AZ Network Private – DNS Record-set a Create][az-network-private-dns-record-set-a-create] för att skapa tomma a post uppsättningar för register slut punkten och data slut punkten:

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

Kör kommandot [AZ Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] för att skapa en post för register slut punkten och data slut punkten:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

Den privata länken har nu kon figurer ATS och är redo att användas.

## <a name="set-up-private-link---portal"></a>Konfigurera privat länk – Portal

Konfigurera en privat länk när du skapar ett register eller Lägg till en privat länk i ett befintligt register. Följande steg förutsätter att du redan har ett virtuellt nätverk och undernät konfigurerat med en virtuell dator för testning. Du kan också [skapa ett nytt virtuellt nätverk och undernät](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Skapa en privat slut punkt – nytt register

1. När du skapar ett register i portalen väljer du **Premium** i **SKU** på fliken **grundläggande** .
1. Välj fliken **Nätverk**.
1. I **nätverks anslutning** väljer du **privat slut punkt**  >  **+ Lägg till**.
1. Ange eller Välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | Name | Ange ett unikt namn. |
    | Underresurs |Välj **register**|
    | **Nätverk** | |
    | Virtuellt nätverk| Välj det virtuella nätverk där den virtuella datorn distribueras, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn har distribuerats. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink.azurecr.io* |
    |||
1. Konfigurera de återstående register inställningarna och välj sedan **Granska + skapa**.

  ![Skapa registret med privat slut punkt](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Skapa en privat slut punkt – befintligt register

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar** väljer du **nätverk**.
1. På fliken **privata slut punkter** väljer du **+ privat slut punkt**.
1. På fliken **grundläggande** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Ange namnet på en befintlig grupp eller skapa en ny.|
    | **Instansinformation** |  |
    | Name | Ange ett namn. |
    |Region|Välj en region.|
    |||
5. Välj **Nästa: resurs**.
6. Ange eller Välj följande information:

    | Inställning | Värde |
    | ------- | ----- |
    |Anslutningsmetod  | Välj **Anslut till en Azure-resurs i min katalog**.|
    | Prenumeration| Välj din prenumeration. |
    | Resurstyp | Välj **Microsoft. ContainerRegistry/register**. |
    | Resurs |Välj namnet på ditt register|
    |Mål under resurs |Välj **register**|
    |||
7. Välj **Nästa: konfiguration**.
8. Ange eller Välj informationen:

    | Inställning | Värde |
    | ------- | ----- |
    |**Nätverk**| |
    | Virtuellt nätverk| Välj det virtuella nätverk där den virtuella datorn distribueras, till exempel *myDockerVMVNET*. |
    | Undernät | Välj ett undernät, till exempel *myDockerVMSubnet* där den virtuella datorn har distribuerats. |
    |**Privat DNS-integrering**||
    |Integrera med privat DNS-zon |Välj **Ja**. |
    |Privat DNS-zon |Välj *(ny) privatelink.azurecr.io* |
    |||

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure verifierar din konfiguration. 
2. När du ser ett meddelande som anger att **valideringen har slutförts** klickar du på **Skapa**.

När den privata slut punkten har skapats visas DNS-inställningarna i den privata zonen på sidan **privata slut punkter** i portalen:

1. I portalen navigerar du till ditt behållar register och väljer **inställningar > nätverk**.
1. På fliken **privata slut punkter** väljer du den privata slut punkt som du skapade.
1. På sidan **Översikt** granskar du länk inställningar och anpassade DNS-inställningar.

  ![DNS-inställningar för slut punkt](./media/container-registry-private-link/private-endpoint-overview.png)

Din privata länk har nu kon figurer ATS och är redo att användas.

## <a name="disable-public-access"></a>Inaktivera offentlig åtkomst

För många scenarier inaktiverar du register åtkomst från offentliga nätverk. Den här konfigurationen förhindrar att klienter utanför det virtuella nätverket når register slut punkter. 

### <a name="disable-public-access---cli"></a>Inaktivera offentlig åtkomst – CLI

Om du vill inaktivera offentlig åtkomst med hjälp av Azure CLI kör du [AZ ACR Update][az-acr-update] och set `--public-network-enabled` till `false` . 

> [!NOTE]
> `public-network-enabled`Argumentet kräver Azure CLI 2.6.0 eller senare. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Inaktivera offentlig åtkomst – Portal

1. I portalen navigerar du till ditt behållar register och väljer **inställningar > nätverk**.
1. På fliken **offentlig åtkomst** , i **Tillåt offentligt nätverks åtkomst**, väljer du **inaktive rad**. Välj sedan **Spara**.

## <a name="validate-private-link-connection"></a>Verifiera anslutning till privat länk

Du bör kontrol lera att resurserna i under nätet för den privata slut punkten är anslutna till registret via en privat IP-adress och att du har rätt integrering av privata DNS-zoner.

För att verifiera anslutningen till den privata länken, kan SSH till den virtuella dator som du konfigurerar i det virtuella nätverket.

Kör ett verktyg som `nslookup` eller `dig` för att leta upp IP-adressen för registret via den privata länken. Exempel:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Exempel på utdata visar registerets IP-adress i under nätets adress utrymme:

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

Jämför det här resultatet med den offentliga IP-adressen i `dig` utdata för samma register över en offentlig slut punkt:

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

### <a name="registry-operations-over-private-link"></a>Register åtgärder via privat länk

Kontrol lera också att du kan utföra register åtgärder från den virtuella datorn i under nätet. Skapa en SSH-anslutning till den virtuella datorn och kör [AZ ACR login][az-acr-login] för att logga in i registret. Beroende på konfigurationen av din virtuella dator kan du behöva använda prefixet för följande kommandon med `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Utföra register åtgärder, till exempel `docker pull` för att hämta en exempel avbildning från registret. Ersätt `hello-world:v1` med en avbildning och tagga lämplig för registret, med prefixet till inloggnings Server namnet för registret (alla gemener):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker hämtar avbildningen till den virtuella datorn.

## <a name="manage-private-endpoint-connections"></a>Hantera anslutningar för privata slut punkter

Hantera ett registers privata slut punkts anslutningar med hjälp av Azure Portal eller med hjälp av kommandon i kommando gruppen [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . Åtgärderna inkluderar Godkänn, ta bort, Visa, avvisa eller Visa information om ett registers privata slut punkts anslutningar.

Om du till exempel vill visa en lista över anslutningar för privata slut punkter för ett register kör du kommandot [AZ ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] . Exempel:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

När du konfigurerar en privat slut punkts anslutning med hjälp av stegen i den här artikeln, accepterar registret automatiskt anslutningar från klienter och tjänster som har Azure RBAC-behörighet för registret. Du kan ställa in slut punkten för att kräva manuellt godkännande av anslutningar. Information om hur du godkänner och avvisar anslutningar för privata slut punkter finns i [hantera en privat slut punkts anslutning](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Lägg till zon poster för repliker

Som du ser i den här artikeln när du lägger till en privat slut punkts anslutning till ett register, skapar du DNS-poster i `privatelink.azurecr.io` zonen för registret och dess data slut punkter i de regioner där registret [replikeras](container-registry-geo-replication.md). 

Om du senare lägger till en ny replik måste du manuellt lägga till en ny zon post för data slut punkten i den regionen. Om du till exempel skapar en replik av *registret* på *europanorra* -platsen lägger du till en zon post för `myregistry.northeurope.data.azurecr.io` . Anvisningar finns i [Skapa DNS-poster i den privata zonen](#create-dns-records-in-the-private-zone) i den här artikeln.

## <a name="dns-configuration-options"></a>Alternativ för DNS-konfiguration

Den privata slut punkten i det här exemplet integreras med en privat DNS-zon som är associerad med ett grundläggande virtuellt nätverk. Den här installationen använder den Azure-angivna DNS-tjänsten direkt för att matcha registrets offentliga domän namn till dess privata IP-adress i det virtuella nätverket. 

Privat länk stöder ytterligare scenarier för DNS-konfiguration som använder den privata zonen, inklusive anpassade DNS-lösningar. Du kan till exempel ha en anpassad DNS-lösning som har distribuerats i det virtuella nätverket eller lokalt i ett nätverk som du ansluter till det virtuella nätverket med en VPN-gateway eller Azure-ExpressRoute. 

Om du vill matcha registrets offentliga FQDN till den privata IP-adressen i de här scenarierna måste du konfigurera en vidarebefordrare på server nivå till Azure DNSs tjänsten (168.63.129.16). De exakta konfigurations alternativen och-stegen beror på dina befintliga nätverk och DNS. Exempel finns i [DNS-konfiguration för privat slutpunkt i Azure](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Om du har skapat privata slut punkter i flera regioner med hög tillgänglighet rekommenderar vi att du använder en separat resurs grupp i varje region och placerar det virtuella nätverket och den tillhör ande privata DNS-zonen i den. Den här konfigurationen förhindrar också oväntad DNS-matchning som orsakas av delning av samma privata DNS-zon.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat alla Azure-resurser i samma resurs grupp och inte längre behöver dem, kan du välja att ta bort resurserna med hjälp av ett enda [AZ Group Delete](/cli/azure/group) -kommando:

```azurecli
az group delete --name $RESOURCE_GROUP
```

Om du vill rensa dina resurser i portalen navigerar du till din resurs grupp. När resurs gruppen har lästs in klickar du på **ta bort resurs** grupp för att ta bort resurs gruppen och resurserna som lagras där.

## <a name="next-steps"></a>Nästa steg

* Mer information om privat länk finns i dokumentationen till [Azures privata länkar](../private-link/private-link-overview.md) .
* Om du behöver konfigurera åtkomst regler för registret bakom en klient brand vägg, se [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
