---
title: Skapa en offentlig belastningsutjämnare med IPv6 – Azure CLI
titleSuffix: Azure Load Balancer
description: Med den här utbildnings vägen börjar du skapa en offentlig belastningsutjämnare med IPv6 med Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, dubbel stack, offentlig IP, inbyggd IPv6, mobil, IoT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 714fb92e8757fed6e11a09528b60cda3e945175e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218901"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Skapa en offentlig belastningsutjämnare med IPv6 med Azure CLI

>[!NOTE] 
>I den här artikeln beskrivs en inledande IPv6-funktion för att tillåta grundläggande belastningsutjämnare att tillhandahålla både IPv4-och IPv6-anslutning. En omfattande IPv6-anslutning är nu tillgänglig med [IPv6 för Azure-virtuella nätverk](../virtual-network/ipv6-overview.md) som integrerar IPv6-anslutningar med dina virtuella nätverk och innehåller viktiga funktioner som IPv6-regler för nätverks säkerhets grupper, IPv6-användardefinierad routning, IPv6 Basic och standard belastnings utjämning med mera.  IPv6 för Azure virtuella nätverk är den rekommenderade standarden för IPv6-program i Azure. Se [IPv6 för Azure VNet PowerShell-distribution](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 

En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Belastnings utjämning ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänst instanser i moln tjänster eller virtuella datorer i en belastnings Utjämnings uppsättning. Belastnings utjämning kan också presentera dessa tjänster på flera portar eller flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempel på distributions scenario

Följande diagram illustrerar belastnings Utjämnings lösningen som distribueras med hjälp av exempel mal len som beskrivs i den här artikeln.

![Lastbalanseringsscenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

I det här scenariot skapar du följande Azure-resurser:

* Två virtuella datorer (VM)
* Ett virtuellt nätverks gränssnitt för varje virtuell dator med både IPv4-och IPv6-adresser tilldelade
* En offentlig belastningsutjämnare med en offentlig IP-adress för IPv4 och IPv6
* En tillgänglighets uppsättning som innehåller de två virtuella datorerna
* Två belastnings Utjämnings regler för att mappa offentliga VIP-adresser till privata slut punkter

## <a name="deploy-the-solution-by-using-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI

Följande steg visar hur du skapar en offentlig belastningsutjämnare med hjälp av Azure CLI. Med CLI skapar du och konfigurerar varje enskilt objekt individuellt, och sätter sedan samman dem för att skapa en resurs.

Om du vill distribuera en belastningsutjämnare skapar och konfigurerar du följande objekt:

* **IP-konfiguration på klient** sidan: innehåller offentliga IP-adresser för inkommande nätverks trafik.
* **Backend-adresspool**: innehåller nätverks gränssnitt (NIC) för de virtuella datorerna för att ta emot nätverks trafik från belastningsutjämnaren.
* **Belastnings Utjämnings regler**: innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen.
* **Ingående NAT-regler**: innehåller Network Address TRANSLATION (NAT) regler som mappar en offentlig port på belastningsutjämnaren till en port för en speciell virtuell dator i backend-adresspoolen.
* **Avsökningar**: innehåller hälso avsökningar som används för att kontrol lera tillgängligheten för virtuella dator instanser i backend-adresspoolen.

## <a name="set-up-azure-cli"></a>Konfigurera Azure CLI

I det här exemplet kör du Azure CLI-verktygen i ett PowerShell-kommando fönster. För att förbättra läsbarheten och återanvändandet kan du använda PowerShell: s skript funktioner, inte Azure PowerShell-cmdlet: ar.

1. [Installera och konfigurera Azure CLI](/cli/azure/install-azure-cli) genom att följa stegen i den länkade artikeln och logga in på ditt Azure-konto.

2. Konfigurera PowerShell-variabler för användning med Azure CLI-kommandon:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Skapa en resurs grupp, en belastningsutjämnare, ett virtuellt nätverk och undernät

1. Skapa en resursgrupp:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Skapa en belastningsutjämnare:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Skapa ett virtuellt nätverk:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Skapa två undernät i det här virtuella nätverket:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Skapa offentliga IP-adresser för klient delens pool

1. Konfigurera PowerShell-variablerna:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Skapa en offentlig IP-adress för IP-poolen på klient sidan:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Belastningsutjämnaren använder domän etiketten för den offentliga IP-adressen som fullständigt kvalificerat domän namn (FQDN). Detta är en förändring från klassisk distribution som använder moln tjänst namnet som belastningsutjämnare-FQDN.
    >
    > I det här exemplet är det fullständiga domän namnet *contoso09152016.southcentralus.cloudapp.Azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Skapa klient dels-och Server dels grupper

I det här avsnittet skapar du följande IP-pooler:
* Den IP-adresspool på klient sidan som tar emot inkommande nätverks trafik i belastningsutjämnaren.
* Server delens IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.

1. Konfigurera PowerShell-variablerna:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Skapa en IP-adresspool på klient sidan och koppla den till den offentliga IP-adress som du skapade i föregående steg och belastningsutjämnaren.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Skapa regler för avsökning, NAT-regler och belastnings utjämning

I det här exemplet skapas följande objekt:

* En avsöknings regel för att kontrol lera anslutningen till TCP-port 80.
* En NAT-regel som översätter all inkommande trafik på port 3389 till port 3389 för RDP.\*
* En NAT-regel som översätter all inkommande trafik på port 3391 till port 3389 för Remote Desktop Protocol (RDP).\*
* En belastnings Utjämnings regel som balanserar all inkommande trafik på port 80 till port 80 på adresserna i backend-poolen.

\* NAT-regler är associerade med en angiven instans av en virtuell dator bakom belastningsutjämnaren. Nätverks trafiken som anländer på port 3389 skickas till den angivna virtuella datorn och porten som är associerad med NAT-regeln. Du måste ange ett protokoll (UDP eller TCP) för en NAT-regel. Du kan inte tilldela båda protokollen till samma port.

1. Konfigurera PowerShell-variablerna:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Skapa avsökningen.

    I följande exempel skapas en TCP-avsökning som kontrollerar om anslutningen till Server delens TCP-port 80 var 15: e sekund. Efter två efterföljande avbrott markerar den backend-resursen som otillgänglig.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Skapa inkommande NAT-regler som tillåter RDP-anslutningar till Server dels resurserna:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Skapa regler för belastnings utjämning som skickar trafik till olika Server dels portar, beroende på klient delen som tog emot begäran.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Kontrol lera inställningarna:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Förväntad utdata:

    ```output
    info:    Executing command network lb show
    info:    Looking up the load balancer "myIPv4IPv6Lb"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
    data:    Name                            : myIPv4IPv6Lb
    data:    Type                            : Microsoft.Network/loadBalancers
    data:    Location                        : southcentralus
    data:    Provisioning state              : Succeeded
    data:
    data:    Frontend IP configurations:
    data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
    data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
    data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
    data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
    data:
    data:    Probes:
    data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
    data:    -------------------  ------------------  --------  ----  ----  --------  -----
    data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
    data:
    data:    Backend Address Pools:
    data:    Name             Provisioning state
    data:    ---------------  ------------------
    data:    BackendPoolIPv4  Succeeded
    data:    BackendPoolIPv6  Succeeded
    data:
    data:    Load Balancing Rules:
    data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
    data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
    data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
    data:
    data:    Inbound NAT Rules:
    data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
    data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
    data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
    data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
    info:    network lb show
    ```

## <a name="create-nics"></a>Skapa nätverkskort

Skapa nätverkskort och koppla dem till NAT-regler, belastnings Utjämnings regler och avsökningar.

1. Konfigurera PowerShell-variablerna:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Skapa ett nätverkskort för varje server del och Lägg till en IPv6-konfiguration:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Skapa de virtuella backend-resurserna och koppla varje nätverkskort

Om du vill skapa virtuella datorer måste du ha ett lagrings konto. De virtuella datorerna måste vara medlemmar i en tillgänglighets uppsättning för belastnings utjämning. Mer information om hur du skapar virtuella datorer finns i [skapa en virtuell Azure-dator med hjälp av PowerShell](../virtual-machines/windows/quick-create-powershell.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Konfigurera PowerShell-variablerna:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > I det här exemplet används användar namn och lösen ord för de virtuella datorerna i klartext. Ta lämplig försiktighet när du använder dessa autentiseringsuppgifter i klartext. En säkrare metod för att hantera autentiseringsuppgifter i PowerShell finns i [`Get-Credential`](/powershell/module/microsoft.powershell.security/get-credential) cmdleten.

2. Skapa tillgänglighets uppsättningen:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Skapa de virtuella datorerna med tillhör ande nätverkskort:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```