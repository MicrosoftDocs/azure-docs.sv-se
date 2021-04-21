---
title: Konfigurera privata slutpunkter för Azure Event Grid ämnen eller domäner
description: Den här artikeln beskriver hur du konfigurerar privata slutpunkter för Azure Event Grid ämnen eller domän.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770515"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Konfigurera privata slutpunkter för Azure Event Grid ämnen eller domäner
Du kan använda [privata slutpunkter](../private-link/private-endpoint-overview.md) för att tillåta ingress av händelser direkt [](../private-link/private-link-overview.md) från ditt virtuella nätverk till dina ämnen och domäner på ett säkert sätt via en privat länk utan att gå via det offentliga Internet. Den privata slutpunkten använder en IP-adress från VNet-adressutrymmet för ditt ämne eller din domän. Mer konceptuell information finns i [Nätverkssäkerhet.](network-security.md)

Den här artikeln beskriver hur du konfigurerar privata slutpunkter för ämnen eller domäner.

## <a name="use-azure-portal"></a>Använda Azure-portalen 
Det här avsnittet visar hur du använder Azure Portal för att skapa en privat slutpunkt för ett ämne eller en domän.

> [!NOTE]
> Stegen som visas i det här avsnittet är främst för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt ämne eller din domän.
2. Växla till **fliken Nätverk** på din ämnessida. Välj **+ Privat slutpunkt** i verktygsfältet.

    ![Lägg till privat slutpunkt](./media/configure-private-endpoints/add-button.png)
2. På sidan **Grundinställningar** följer du dessa steg: 
    1. Välj en **Azure-prenumeration** där du vill skapa den privata slutpunkten. 
    2. Välj en **Azure-resursgrupp** för den privata slutpunkten. 
    3. Ange ett **namn** för slutpunkten. 
    4. Välj **region för** slutpunkten. Din privata slutpunkt måste finnas i samma region som ditt virtuella nätverk, men kan finnas i en annan region än den privata länkresursen (i det här exemplet ett Event Grid-ämne). 
    5. Välj sedan **Knappen Nästa: >** resursresurs längst ned på sidan. 

      ![Privat slutpunkt – sidan Grundinställningar](./media/configure-private-endpoints/basics-page.png)
3. Följ dessa **steg** på sidan Resurs: 
    1. Om du väljer Anslut till en **Azure-resurs i min katalog som anslutningsmetod följer** du dessa steg. Det här exemplet visar hur du ansluter till en Azure-resurs i din katalog. 
        1. Välj den **Azure-prenumeration** där **ämnet/domänen** finns. 
        1. För **Resurstyp** väljer du **Microsoft.EventGrid/topics** eller **Microsoft.EventGrid/domains** som **Resurstyp.**
        2. För **Resurs** väljer du ett ämne/en domän i listrutan. 
        3. Bekräfta att **målunderresursen är** inställd på **ämne eller** **domän** (baserat på den resurstyp som du har valt).    
        4. Välj **Nästa: >** längst ned på sidan. 

            ![Skärmbild som visar sidan "Skapa en privat slutpunkt – Resurs".](./media/configure-private-endpoints/resource-page.png)
    2. Om du väljer **Anslut till en resurs med ett resurs-ID eller ett alias** följer du dessa steg:
        1. Ange ID för resursen. Exempel: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. För **Resurs** anger du **ämnet** eller **domänen**. 
        3. (valfritt) Lägg till ett meddelande om begäran. 
        4. Välj **Nästa: >** längst ned på sidan. 

            ![Privat slutpunkt – resurssida](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. På sidan **Konfiguration** väljer du undernätet i ett virtuellt nätverk dit du vill distribuera den privata slutpunkten. 
    1. Välj ett **virtuellt nätverk**. Endast virtuella nätverk i den valda prenumerationen och platsen visas i listrutan. 
    2. Välj ett **undernät i** det virtuella nätverk som du har valt. 
    3. Välj **Nästa: >** längst ned på sidan. 

    ![Privat slutpunkt – konfigurationssida](./media/configure-private-endpoints/configuration-page.png)
5. På sidan **Taggar** skapar du eventuella taggar (namn och värden) som du vill associera med den privata slutpunktsresursen. Välj sedan **knappen Granska +** skapa längst ned på sidan. 
6. På sidan **Granska + skapa** granskar du alla inställningar och väljer Skapa för **att** skapa den privata slutpunkten. 

    ![Privat slutpunkt – granska sidan & skapa](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Hantera privat länkanslutning

När du skapar en privat slutpunkt måste anslutningen godkännas. Om resursen som du skapar en privat slutpunkt för finns i katalogen kan du godkänna anslutningsbegäran förutsatt att du har tillräcklig behörighet. Om du ansluter till en Azure-resurs i en annan katalog måste du vänta tills ägaren av resursen godkänner din anslutningsbegäran.

Det finns fyra etablerings tillstånd:

| Tjänståtgärd | Tillstånd för privat slutpunkt för tjänstkonsument | Beskrivning |
|--|--|--|
| Ingen | Väntar | Anslutningen skapas manuellt och väntar på godkännande från den privata Link-resursägaren. |
| Godkänn | Godkända | Anslutningen godkändes automatiskt eller manuellt och är redo att användas. |
| Avvisa | Avslagen | Anslutningen avvisades av den privata länkens resursägare. |
| Ta bort | Frånkopplad | Anslutningen har tagits bort av resursägaren för den privata länken. Den privata slutpunkten blir informativ och bör tas bort för rensning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Så här hanterar du en privat slutpunktsanslutning
Följande avsnitt visar hur du godkänner eller avvisar en privat slutpunktsanslutning. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I sökfältet skriver du i Event Grid **ämnen eller** **Event Grid domäner**.
1. Välj det **ämne** **eller den** domän som du vill hantera.
1. Välj fliken **Nätverk**.
1. Om det finns några väntande anslutningar visas en anslutning i listan med **Väntar i** etableringstillståndet. 

### <a name="to-approve-a-private-endpoint"></a>Så här godkänner du en privat slutpunkt
Du kan godkänna en privat slutpunkt som är i väntande tillstånd. Följ dessa steg för att godkänna: 

> [!NOTE]
> Stegen som visas i det här avsnittet är främst för ämnen. Du kan använda liknande steg för att godkänna privata slutpunkter för **domäner**. 

1. Välj den **privata slutpunkt** som du vill godkänna och **välj Godkänn** i verktygsfältet.

    ![Privat slutpunkt – väntande tillstånd](./media/configure-private-endpoints/pending.png)
1. I dialogrutan **Godkänn anslutning** anger du en kommentar (valfritt) och väljer **Ja.** 

    ![Privat slutpunkt – godkänn](./media/configure-private-endpoints/approve.png)
1. Bekräfta att du ser statusen för slutpunkten som **Godkänd.** 

    ![Privat slutpunkt – godkänt tillstånd](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Avvisa en privat slutpunkt
Du kan avvisa en privat slutpunkt som är i väntande eller godkänt tillstånd. Om du vill avvisa följer du dessa steg: 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att avvisa privata slutpunkter för **domäner**. 

1. Välj den **privata slutpunkt** som du vill avvisa och välj **Avvisa** i verktygsfältet.

    ![Skärmbild som visar "Nätverk – Privata slutpunktsanslutningar" med "Avvisa" valt.](./media/configure-private-endpoints/reject-button.png)
1. I dialogrutan **Avvisa anslutning** anger du en kommentar (valfritt) och väljer **Ja.** 

    ![Privat slutpunkt – avvisa](./media/configure-private-endpoints/reject.png)
1. Bekräfta att du ser statusen för slutpunkten som **Avvisad.** 

    ![Privat slutpunkt – avvisat tillstånd](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Du kan inte godkänna en privat slutpunkt i Azure Portal när den avvisas. 


## <a name="use-azure-cli"></a>Använda Azure CLI
Om du vill skapa en privat slutpunkt använder du [metoden az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) enligt följande exempel:

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

Beskrivningar av de parametrar som används i exemplet finns i dokumentationen för [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create). Några saker att notera i det här exemplet är: 

- För `private-connection-resource-id` anger du resurs-ID:t för **ämnet** eller **domänen**. I föregående exempel används ämnet type: .
- för `group-ids` , ange eller `topic` `domain` . I föregående exempel `topic` används . 

Om du vill ta bort en privat slutpunkt använder [du metoden az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) enligt följande exempel:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 



### <a name="prerequisites"></a>Förutsättningar
Uppdatera Azure Event Grid för CLI genom att köra följande kommando: 

```azurecli-interactive
az extension update -n eventgrid
```

Om tillägget inte är installerat kör du följande kommando för att installera det: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt
Om du vill skapa en privat slutpunkt använder du [metoden az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) enligt följande exempel:

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

Beskrivningar av de parametrar som används i exemplet finns i dokumentationen för [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create). Några saker att notera i det här exemplet är: 

- För `private-connection-resource-id` anger du resurs-ID:t **för ämnet** eller **domänen**. I föregående exempel används ämnet type: .
- för `group-ids` , ange eller `topic` `domain` . I föregående exempel `topic` används. 

Om du vill ta bort en privat slutpunkt använder du [metoden az network private-endpoint delete](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) enligt följande exempel:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 

#### <a name="sample-script"></a>Exempelskript
Här är ett exempelskript som skapar följande Azure-resurser:

- Resursgrupp
- Virtuellt nätverk
- Undernät i det virtuella nätverket
- Azure Event Grid ämne
- Privat slutpunkt för ämnet

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för domäner.

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

### <a name="approve-a-private-endpoint"></a>Godkänna en privat slutpunkt
Följande CLI-exempelavsnitt visar hur du godkänner en privat slutpunktsanslutning. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Avvisa en privat slutpunkt
Följande CLI-exempelfragment visar hur du avvisar en privat slutpunktsanslutning. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Inaktivera åtkomst till offentligt nätverk
Som standard är offentlig nätverksåtkomst aktiverad för ett Event Grid ämne eller domän. Om du endast vill tillåta åtkomst via privata slutpunkter inaktiverar du offentlig nätverksåtkomst genom att köra följande kommando:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Använd PowerShell
Det här avsnittet visar hur du skapar en privat slutpunkt för ett ämne eller en domän med hjälp av PowerShell. 

### <a name="prerequisite"></a>Förutsättning
Följ anvisningarna i Gör så [här:](../active-directory/develop/howto-create-service-principal-portal.md) Använd portalen för att skapa ett Azure AD-program och tjänstens huvudnamn som kan komma åt resurser för att skapa ett Azure Active Directory-program och anteckna värdena för **katalog-ID (klient)**, program-ID **(klient) och** **programhemlighet (klient).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbereda token och huvuden för REST API anrop 
Kör följande nödvändiga kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop och auktorisering och annan huvudinformation. 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Skapa ett undernät med slutpunktsnätverksprinciper inaktiverade

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Skapa ett Event Grid-ämne med en privat slutpunkt

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att skapa privata slutpunkter för **domäner**. 


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

När du kontrollerar att slutpunkten har skapats bör du se resultatet som liknar följande:

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

### <a name="approve-a-private-endpoint-connection"></a>Godkänna en privat slutpunktsanslutning
Följande PowerShell-exempelfragment visar hur du godkänner en privat slutpunkt. 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att godkänna privata slutpunkter för **domäner**. 

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

### <a name="reject-a-private-endpoint-connection"></a>Avvisa en privat slutpunktsanslutning
I följande exempel visas hur du avvisar en privat slutpunkt med hjälp av PowerShell. Du kan hämta GUID för den privata slutpunkten från resultatet av föregående GET-kommando. 

> [!NOTE]
> Stegen som visas i det här avsnittet är för ämnen. Du kan använda liknande steg för att avvisa privata slutpunkter för **domäner**. 


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

Du kan godkänna anslutningen även efter att den avvisats via API. Om du Azure Portal kan du inte godkänna en slutpunkt som har avvisats. 

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du konfigurerar IP-brandväggsinställningar finns i [Konfigurera IP-brandvägg för Azure Event Grid eller domäner.](configure-firewall.md)
* Information om hur du felsöker problem med nätverksanslutningen finns [i Felsöka problem med nätverksanslutningen](troubleshoot-network-connectivity.md)
