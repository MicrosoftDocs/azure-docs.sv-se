---
title: Konfigurera IP-brandvägg för Azure Event Grid ämnen eller domäner
description: I den här artikeln beskrivs hur du konfigurerar brand Väggs inställningar för Event Grid ämnen eller domäner.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: df6098df4817ee6c47378704c25d07433d6b9480
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509426"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Konfigurera IP-brandvägg för Azure Event Grid ämnen eller domäner 
Som standard är ämne och domän tillgängligt från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i CIDR-notation [(Classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Utgivare som härstammar från andra IP-adresser avvisas och får ett 403-svar (förbjuden). Mer information om nätverks säkerhets funktioner som stöds av Event Grid finns i [nätverks säkerhet för Event Grid](network-security.md).

I den här artikeln beskrivs hur du konfigurerar inställningar för IP-brandvägg för Azure Event Grid ämnen eller domäner.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet visar hur du använder Azure Portal för att skapa regler för inkommande IP-brandvägg. Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 

1. I [Azure Portal](https://portal.azure.com)navigerar du till ditt event Grid-ämne eller-domän och växlar till fliken **nätverk** .
2. Välj **offentliga nätverk** för att tillåta alla nätverk, inklusive Internet, för åtkomst till resursen. 

    Du kan begränsa trafiken med hjälp av IP-baserade brand Väggs regler. Ange en enskild IPv4-adress eller ett intervall med IP-adresser i CIDR-notation (Classless Inter-Domain routing). 

    ![Sidan offentliga nätverk](./media/configure-firewall/public-networks-page.png)
3. Välj **endast privata slut punkter** om du endast vill tillåta anslutningar för privata slut punkter för att få åtkomst till den här resursen. Använd fliken **anslutningar för privata slut punkter** på den här sidan för att hantera anslutningar. 

    ![Sidan offentliga nätverk](./media/configure-firewall/private-endpoints-page.png)
4. Välj **Spara** i verktygsfältet. 



## <a name="use-azure-cli"></a>Använda Azure CLI
Det här avsnittet visar hur du använder Azure CLI-kommandon för att skapa ämnen med inkommande IP-regler. Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 


### <a name="prerequisites"></a>Krav
Uppdatera Azure Event Grid-tillägget för CLI genom att köra följande kommando: 

```azurecli-interactive
az extension update -n eventgrid
```

Om tillägget inte är installerat kör du följande kommando för att installera det: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>Aktivera eller inaktivera offentlig nätverks åtkomst
Som standard är den offentliga nätverks åtkomsten aktive rad för ämnen och domäner. Du kan också aktivera det explicit eller inaktivera det. Du kan begränsa trafiken genom att konfigurera regler för inkommande IP-brandvägg. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Aktivera offentligt nätverks åtkomst när du skapar ett ämne

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Inaktivera offentlig nätverks åtkomst när du skapar ett ämne

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> När offentlig nätverks åtkomst har inaktiverats för ett ämne eller en domän tillåts inte trafik över offentliga Internet. Endast privata slut punkts anslutningar får åtkomst till dessa resurser. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Aktivera offentligt nätverks åtkomst för ett befintligt ämne

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Inaktivera offentlig nätverks åtkomst för ett befintligt ämne 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Skapa ett ämne med en enskild regel för inkommande IP-adress
Följande exempel på CLI-kommando skapar ett event Grid-ämne med inkommande IP-regler. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Skapa ett ämne med flera inkommande IP-regler

Följande exempel på CLI-kommando skapar ett event Grid-ämne två inkommande IP-regler i ett enda steg: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Uppdatera ett befintligt ämne för att lägga till inkommande IP-regler
Det här exemplet skapar ett event Grid-ämne först och lägger sedan till inkommande IP-regler för ämnet i ett separat kommando. Den uppdaterar också de inkommande IP-reglerna som angavs i det andra kommandot. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Ta bort en inkommande IP-regel
Följande kommando tar bort den andra regeln som du skapade i föregående steg genom att bara ange den första regeln när du uppdaterar inställningen. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>Använd PowerShell
Det här avsnittet visar hur du använder Azure PowerShell-kommandon för att skapa Azure Event Grid ämnen med regler för inkommande IP-brandvägg. Stegen som visas i det här avsnittet gäller för ämnen. Du kan använda liknande steg för att skapa inkommande IP-regler för **domäner**. 

### <a name="prerequisites"></a>Krav
Följ instruktionerna i [så här: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md) för att skapa ett Azure Active Directory program och anteckna följande värden:

- Katalog-ID (klient)
- Program-ID (klient)
- Program (klient) hemlighet

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Förbered token och rubriker för REST API-anrop 
Kör följande krav kommandon för att hämta en autentiseringstoken som ska användas med REST API-anrop, samt information om auktorisering och annan huvud information. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Aktivera offentligt nätverks åtkomst för ett befintligt ämne
Som standard är den offentliga nätverks åtkomsten aktive rad för ämnen och domäner. Du kan begränsa trafiken genom att konfigurera regler för inkommande IP-brandvägg. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Inaktivera offentlig nätverks åtkomst för ett befintligt ämne
När offentlig nätverks åtkomst har inaktiverats för ett ämne eller en domän tillåts inte trafik över offentliga Internet. Endast privata slut punkts anslutningar får åtkomst till dessa resurser. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Skapa ett event Grid-ämne med regler för inkommande trafik i ett steg

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Avsnittet Skapa händelse rutnät först och Lägg sedan till inkommande IP-regler

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Mer information om nyckeln för autentisering finns i [Event Grid säkerhet och autentisering](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* Information om hur du felsöker problem med nätverks anslutningen finns i [Felsöka problem med nätverks anslutningen](troubleshoot-network-connectivity.md)
