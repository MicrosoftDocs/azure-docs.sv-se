---
title: Konfigurera privata slut punkter för Azure Event Grid ämnen eller domäner
description: I den här artikeln beskrivs hur du konfigurerar privata slut punkter för Azure Event Grid ämnen eller domän.
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa67ba8dbe8106c0311bafec07a1510ca0c25c3f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508846"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Konfigurera privata slut punkter för Azure Event Grid ämnen eller domäner
Du kan använda [privata slut punkter](../private-link/private-endpoint-overview.md) för att tillåta ingress av händelser direkt från ditt virtuella nätverk till dina ämnen och domäner på ett säkert sätt över en [privat länk](../private-link/private-link-overview.md) utan att gå via det offentliga Internet. Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för ditt ämne eller din domän. Mer konceptuell information finns i [nätverks säkerhet](network-security.md).

I den här artikeln beskrivs hur du konfigurerar privata slut punkter för ämnen eller domäner.

## <a name="use-azure-portal"></a>Använda Azure-portalen 
Det här avsnittet visar hur du använder Azure Portal för att skapa en privat slut punkt för ett ämne eller en domän.

> [!NOTE]
> De steg som visas i det här avsnittet är oftast för ämnen. Du kan använda liknande steg för att skapa privata slut punkter för **domäner**. 

1. Logga in på [Azure Portal](https://portal.azure.com) och navigera till ditt ämne eller din domän.
2. Växla till fliken **nätverk** på din ämnes sida. Välj **+ privat slut punkt** i verktygsfältet.

    ![Lägg till privat slut punkt](./media/configure-private-endpoints/add-button.png)
2. Följ de här stegen för en sida med **grundläggande** information: 
    1. Välj en **Azure-prenumeration** där du vill skapa den privata slut punkten. 
    2. Välj en **Azure-resurs grupp** för den privata slut punkten. 
    3. Ange ett **namn** för slut punkten. 
    4. Välj **region** för slut punkten. Din privata slut punkt måste finnas i samma region som ditt virtuella nätverk, men kan i en annan region från den privata länk resursen (i det här exemplet ett event Grid-ämne). 
    5. Välj sedan **Nästa: resurs >s ** knappen längst ned på sidan. 

      ![Privat slut punkt – sidan grunder](./media/configure-private-endpoints/basics-page.png)
3. Följ de här stegen på sidan **resurs** : 
    1. För anslutnings metod, om du väljer **Anslut till en Azure-resurs i min katalog**, följer du dessa steg. Det här exemplet visar hur du ansluter till en Azure-resurs i din katalog. 
        1. Välj den **Azure-prenumeration** där **ämnet/domänen** finns. 
        1. För **resurs typ**väljer du **Microsoft. EventGrid/topics** eller **Microsoft. EventGrid/Domains** för **resurs typen**.
        2. För **resurs**väljer du ett ämne/en domän i den nedrullningsbara listan. 
        3. Bekräfta att **mål under resursen** har angetts till **ämne** eller **domän** (baserat på den resurs typ som du har valt).    
        4. Välj **Nästa: konfiguration >s ** knappen längst ned på sidan. 

            ![Privat slut punkt – resurs sida](./media/configure-private-endpoints/resource-page.png)
    2. Om du väljer **Anslut till en resurs med ett resurs-ID eller ett alias**följer du dessa steg:
        1. Ange resursens ID. Till exempel: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. För **resurs**anger du **ämne** eller **domän**. 
        3. valfritt Lägg till ett meddelande om begäran. 
        4. Välj **Nästa: konfiguration >s ** knappen längst ned på sidan. 

            ![Privat slut punkt – resurs sida](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. På sidan **konfiguration** väljer du det undernät i ett virtuellt nätverk som du vill distribuera den privata slut punkten till. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i list rutan. 
    2. Välj ett **undernät** i det virtuella nätverk som du har valt. 
    3. Välj **Nästa: tagg >** -knappen längst ned på sidan. 

    ![Privat slut punkt – konfigurations sida](./media/configure-private-endpoints/configuration-page.png)
5. På sidan **taggar** skapar du alla Taggar (namn och värden) som du vill koppla till den privata slut punkts resursen. Välj sedan **Granska + skapa** längst ned på sidan. 
6. Granska alla inställningar på sidan **Granska och skapa**och välj **skapa** för att skapa den privata slut punkten. 

    ![Privat slut punkt – granska & skapa sida](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Hantera anslutning till privat anslutning

När du skapar en privat slut punkt måste anslutningen godkännas. Om den resurs som du skapar en privat slut punkt för finns i din katalog kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen har godkänt din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Tjänst åtgärd | Status för privat slut punkt för tjänst förbrukare | Beskrivning |
|--|--|--|
| Inget | Väntar | Anslutningen skapas manuellt och väntar på godkännande från ägaren till den privata länk resursen. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av ägaren till den privata länk resursen. |
| Ta bort | Frånkopplad | Anslutningen togs bort av ägaren till den privata länk resursen, den privata slut punkten blir informativ och bör tas bort för rensning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Så här hanterar du en anslutning till en privat slutpunkt
I följande avsnitt visas hur du godkänner eller avvisar en anslutning till en privat slut punkt. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Sök fältet skriver du **Event Grid ämnen** eller **Event Grid domäner**.
1. Välj det **ämne** eller den **domän** som du vill hantera.
1. Välj fliken **nätverk** .
1. Om det finns några anslutningar som väntar, ser du en anslutning som anges i **väntan** på etablerings status. 

### <a name="to-approve-a-private-endpoint"></a>Godkänna en privat slut punkt
Du kan godkänna en privat slut punkt som är i vänte läge. Följ dessa steg om du vill godkänna: 

> [!NOTE]
> De steg som visas i det här avsnittet är oftast för ämnen. Du kan använda liknande steg för att godkänna privata slut punkter för **domäner**. 

1. Välj den **privata slut punkt** som du vill godkänna och välj **Godkänn** i verktygsfältet.

    ![Privat slut punkt – väntande tillstånd](./media/configure-private-endpoints/pending.png)
1. I dialog rutan **Godkänn anslutning** anger du en kommentar (valfritt) och väljer **Ja**. 

    ![Privat slut punkt-Godkänn](./media/configure-private-endpoints/approve.png)
1. Bekräfta att du ser statusen för slut punkten som **godkänd**. 

    ![Privat slut punkt – godkänt tillstånd](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Avvisa en privat slut punkt
Du kan avvisa en privat slut punkt som är i vänte läge eller godkänt tillstånd. Följ dessa steg om du vill avvisa: 

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att avvisa privata slut punkter för **domäner**. 

1. Välj den **privata slut punkt** som du vill avvisa och välj **avvisa** i verktygsfältet.

    ![Privat slut punkt-avvisa](./media/configure-private-endpoints/reject-button.png)
1. I dialog rutan **avvisa anslutning** anger du en kommentar (valfritt) och väljer **Ja**. 

    ![Privat slut punkt-avvisa](./media/configure-private-endpoints/reject.png)
1. Bekräfta att statusen för slut punkten visas som **avvisad**. 

    ![Privat slut punkt-avvisat tillstånd](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Du kan inte godkänna en privat slut punkt i Azure Portal när den har avvisats. 


## <a name="use-azure-cli"></a>Använda Azure CLI
Om du vill skapa en privat slut punkt använder du metoden [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) som visas i följande exempel:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Beskrivningar av de parametrar som används i exemplet finns i dokumentationen för [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Några punkter att Observera i det här exemplet är: 

- För `private-connection-resource-id` anger du resurs-ID för **ämnet** eller **domänen**. I föregående exempel används avsnittet Typ:.
- för `group-ids` , ange `topic` eller `domain` . I föregående exempel `topic` används. 

Om du vill ta bort en privat slut punkt använder du [borttagnings metoden AZ Network Private-Endpoint](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) som visas i följande exempel:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa privata slut punkter för **domäner**. 



### <a name="prerequisites"></a>Krav
Uppdatera Azure Event Grid-tillägget för CLI genom att köra följande kommando: 

```azurecli-interactive
az extension update -n eventgrid
```

Om tillägget inte är installerat kör du följande kommando för att installera det: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Om du vill skapa en privat slut punkt använder du metoden [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) som visas i följande exempel:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Beskrivningar av de parametrar som används i exemplet finns i dokumentationen för [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create). Några punkter att Observera i det här exemplet är: 

- För `private-connection-resource-id` anger du resurs-ID för **ämnet** eller **domänen**. I föregående exempel används avsnittet Typ:.
- för `group-ids` , ange `topic` eller `domain` . I föregående exempel `topic` används. 

Om du vill ta bort en privat slut punkt använder du [borttagnings metoden AZ Network Private-Endpoint](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) som visas i följande exempel:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa privata slut punkter för **domäner**. 

#### <a name="sample-script"></a>Exempelskript
Här är ett exempel skript som skapar följande Azure-resurser:

- Resursgrupp
- Virtuellt nätverk
- Undernät i det virtuella nätverket
- Azure Event Grid ämne
- Ämnets privata slut punkt

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa privata slut punkter för domäner.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Godkänn en privat slut punkt
Följande exempel på CLI-kodfragment visar hur du godkänner en privat slut punkts anslutning. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Avvisa en privat slut punkt
Följande exempel på CLI-kodfragment visar hur du avvisar en privat slut punkts anslutning. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Inaktivera offentlig nätverks åtkomst
Som standard är offentlig nätverks åtkomst aktive rad för ett Event Grid ämne eller en domän. Om du bara vill tillåta åtkomst via privata slut punkter inaktiverar du offentlig nätverks åtkomst genom att köra följande kommando:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Använd PowerShell
I det här avsnittet visas hur du skapar en privat slut punkt för ett ämne eller en domän med hjälp av PowerShell. 

### <a name="prerequisite"></a>Förutsättning
Följ anvisningarna nedan för [att: använda portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa ett Azure Active Directory program och anteckna värdena för **katalog-ID**, **program (klient) ID**och **program (klient) hemlighet**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbered token och rubriker för REST API-anrop 
Kör följande krav kommandon för att hämta en autentiseringstoken som ska användas med REST API samtal och auktorisering och annan huvud information. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Skapa ett undernät med slut punkts nätverks principer inaktiverade

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Skapa ett event Grid-ämne med en privat slut punkt

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa privata slut punkter för **domäner**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

När du verifierar att slut punkten har skapats bör du se resultatet som liknar följande:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slut punkts anslutning
Följande exempel på PowerShell-kodfragment visar hur du godkänner en privat slut punkt. 

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att godkänna privata slut punkter för **domäner**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slut punkts anslutning
I följande exempel visas hur du avvisar en privat slut punkt med hjälp av PowerShell. Du kan hämta GUID för den privata slut punkten från resultatet av föregående GET-kommando. 

> [!NOTE]
> Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att avvisa privata slut punkter för **domäner**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

Du kan godkänna anslutningen även efter att den har avvisats via API. Om du använder Azure Portal kan du inte godkänna en slut punkt som har avvisats. 

## <a name="next-steps"></a>Nästa steg
* Information om hur du konfigurerar inställningar för IP-brandvägg finns i [Konfigurera IP-brandvägg för Azure Event Grid ämnen eller domäner](configure-firewall.md).
* Information om hur du felsöker problem med nätverks anslutningen finns i [Felsöka problem med nätverks anslutningen](troubleshoot-network-connectivity.md)
