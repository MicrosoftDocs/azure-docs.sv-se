---
title: Konfigurera VPN på din Azure Stack Edge Pro R-enhet med Azure PowerShell
description: Beskriver hur du konfigurerar VPN på din Azure Stack Edge Pro R-enhet med ett Azure PowerShell-skript för att skapa Azure-resurser.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634183"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Konfigurera VPN på din Azure Stack Edge Pro R-enhet via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN-alternativet ger ett sekundärt krypterings lager för data i rörelse över *TLS* från din Azure Stack Edge Pro R-enhet till Azure. Du kan konfigurera VPN på din Azure Stack Edge Pro R-enhet via Azure Portal eller via Azure PowerShell.

I den här artikeln beskrivs de steg som krävs för att konfigurera VPN på din Azure Stack Edge Pro R-enhet med hjälp av en Azure PowerShell för att skapa konfigurationen i molnet.

## <a name="about-vpn-setup"></a>Om VPN-konfiguration

En VPN-anslutning mellan olika platser består av en Azure VPN-gateway, en lokal VPN-enhet och en IPsec-S2S VPN-tunnel som ansluter två. Vanliga arbets flöden innehåller följande steg:

- Konfigurera förutsättningar.
- Konfigurera nödvändiga resurser i Azure.
    - Skapa och konfigurera en Azure VPN-gateway (virtuell nätverksgateway).
    - Skapa och konfigurera en lokal Azure-nätverksgateway som representerar ditt lokala nätverk och VPN-enhet.
    - Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala Nätverksgatewayen.
    - Konfigurera Azure-brandväggen och Lägg till nätverks-och app-regler.
    - Skapa Azure vägvals tabell och Lägg till vägar.
- Konfigurera VPN i det lokala webb gränssnittet på enheten. Du konfigurerar den lokala VPN-enheten som representeras av den lokala Nätverksgatewayen för att upprätta den faktiska S2S VPN-tunneln med Azure VPN-gatewayen.

De detaljerade stegen beskrivs i följande avsnitt.

## <a name="configure-prerequisites"></a>Konfigurera krav

1. Du bör ha åtkomst till en Azure Stack Edge Pro R-enhet som har installerats enligt anvisningarna i [installera din Azure Stack Edge Pro r-enhet](azure-stack-edge-pro-r-deploy-install.md). Den här enheten fungerar som den lokala VPN-enheten för att skapa en VPN-anslutning med Azure. 

2. Din VPN-enhet bör ha en statisk offentlig IP-adress (extern). Adressen får inte vara NAT.

3. Du bör ha åtkomst till en giltig Azure-prenumeration som är aktive rad för Azure Stack Edge-tjänsten i Azure. Använd den här prenumerationen för att skapa en motsvarande resurs i Azure för att hantera din Azure Stack Edge Pro R-enhet.  

4. Du har åtkomst till en Windows-klient som du kommer att använda för att få åtkomst till din Azure Stack Edge Pro R-enhet. Du använder den här klienten för att program mässigt skapa konfigurationen i molnet.

    1. Om du vill installera den version av PowerShell som krävs på Windows-klienten kör du följande kommandon:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Kör följande kommandon för att ansluta till ditt Azure-konto och din prenumeration:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Ange namnet på den Azure-prenumeration som du använder med din Azure Stack Edge Pro R-enhet för att konfigurera VPN.

    3. [Hämta skriptet](https://aka.ms/ase-vpn-deployment) som krävs för att skapa en konfiguration i molnet. Skriptet kommer att:
        
        - Skapa ett virtuellt Azure-nätverk och följande undernät: *GatewaySubnet* och *AzureFirewallSubnet*.
        - Skapa och konfigurera en Azure VPN-gateway.
        - Skapa och konfigurera en lokal Azure-nätverksgateway.
        - Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala Nätverksgatewayen.
        - Skapa en Azure-brandvägg och Lägg till nätverks regler, app-regler.
        - Skapa en Azure vägvals tabell och Lägg till vägar i den.


## <a name="use-the-script"></a>Använd skriptet

Först ändrar du `parameters.json` filen för att mata in parametrarna. Sedan kör du skriptet med den ändrade JSON-filen.

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="download-service-tags-file"></a>Hämta service tag-filen

Du kanske redan har en `ServiceTags.json` fil i mappen där du laddade ned skriptet. Om inte kan du ladda ned filen med tjänst koder.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Ändra parameter fil

Det första steget är att ändra `parameters.json` filen och spara ändringarna. 


För de Azure-resurser som du skapar ger du följande namn:

|Parameternamn  |Beskrivning  |
|---------|---------|
|virtualNetworks_vnet_name    | Namn på Azure-Virtual Network        |
|azureFirewalls_firewall_name     | Namn på Azure-brandvägg        |
|routeTables_routetable_name     | Namn på Azure route-tabell        |
|publicIPAddresses_VNGW_public_ip_name     | Namn på offentlig IP-adress för din virtuella nätverksgateway       |
|virtualNetworkGateways_VNGW_name    | Namn på Azure VPN gateway (virtuell nätverksgateway)        |
|publicIPAddresses_firewall_public_ip_name     | Namn på offentlig IP-adress för din Azure-brandvägg         |
|localNetworkGateways_LNGW_name    |Namn på Azure lokal nätverksgateway          |
|connections_vngw_lngw_name    | Namn på Azure VPN-anslutning. Detta är anslutningen mellan den virtuella Nätverksgatewayen och den lokala Nätverksgatewayen.       |
|location     |Det här är den region där du vill skapa ditt virtuella nätverk. Välj samma region som den som är kopplad till din enhet.         |

Följande IP-adresser och adress utrymmen avser de Azure-resurser som skapas inklusive det virtuella nätverket och associerade undernät (standard, brand vägg, GatewaySubnet).

|Parameternamn  |Beskrivning  |
|---------|---------|
|VnetIPv4AddressSpace    | Detta är adress utrymmet som är kopplat till det virtuella nätverket.       |
|DefaultSubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `Default` under nätet för det virtuella nätverket.         |
|FirewallSubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `Firewall` under nätet för det virtuella nätverket.          |
|GatewaySubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `GatewaySubnet` för det virtuella nätverket.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Detta är den IP-adress som är reserverad för BGP-kommunikation och baseras på det adress utrymme som är kopplat till `GatewaySubnet` för det virtuella nätverket.          |

Följande IP-adresser och adress utrymmen gäller för det lokala nätverket (där din Azure Stack Edge Pro R-enhet distribueras).

|Parameternamn  |Beskrivning  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Detta är adress utrymmet för din privata IP-adress.       |
|CustomerPublicNetworkAddressSpace    |  Detta är adress utrymmet för din offentliga IP-adress.       |
|DbeIOTNetworkAddressSpace    |Den här IP-adressen är reserverad för IoT-tjänsten. Ändra inte den här parametern.        |
|AzureVPNsharedKey    |Den här delade nyckeln används när resursen för Azure VPN-anslutningar skapas. Den här nyckeln anges också som VPN-delad hemlighet under den lokala VPN-konfigurationen för webb gränssnittet.         |
|DBE-Gateway-IPAddress   | Offentlig IP-adress för din Azure Stack Edge Pro R-enhet. Detta kanske inte är känt och du kan köra skriptet med en IP-adress för plats hållaren. Redigera den lokala Nätverksgatewayen senare med den faktiska IP-adressen.     |

#### <a name="caveats-to-keep-in-mind"></a>Varningar som bör vara i åtanke:

- Du har inte IP-adressen till Azure Stack Edge Pro R-enheten. Du kan använda en IP-adress för plats hållare för att skapa resursen och senare ändra den lokala Azure-Nätverksgatewayen för att tilldela den faktiska enhetens IP-adress och adress utrymmet för enhetens lokala nätverk.
- Baserat på riktningen från IETF on Numbers Authority (IANA) använder du valfritt undernät från 172.16. x. y till 172.24. z.a. Vi reserverar IPv4-adress intervallen för 172,24 för Azure-nätverket.

### <a name="run-the-script"></a>Kör skriptet

Följ dessa steg om du vill använda den ändrade `parameters.json` och köra skriptet för att skapa Azure-resurser.

1. Kör PowerShell som administratör.

2. Växla till den katalog där skriptet finns.

3. Kör skriptet.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Ett exempel på utdata visas nedan.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Det tar ungefär 90 minuter att köra skriptet. När skriptet har slutförts skapas en distributions logg i samma mapp som skriptet finns i.


## <a name="verify-the-azure-resources"></a>Verifiera Azure-resurserna

När du har kört skriptet kontrollerar du att alla resurser har skapats i Azure.

Sedan konfigurerar du VPN-nätverket på enhetens lokala webb gränssnitt.


## <a name="vpn-configuration-on-the-device"></a>VPN-konfiguration på enheten

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Verifiera VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Verifiera data överföring via VPN

För att bekräfta att VPN fungerar kopierar du data till en SMB-resurs. Följ stegen i [Lägg till en resurs](azure-stack-edge-gpu-manage-shares.md#add-a-share) på din Azure Stack Edge Pro R-enhet. 

1. Kopiera en fil, till exempel \data\pictures\waterfall.jpg till den SMB-resurs som du monterade i klient systemet. 
2. Kontrol lera att filen visas i ditt lagrings konto i molnet.

Kontrol lera att data skickas via VPN:

1. Öppna den anslutnings resurs som finns i resurs gruppen. 

2. Kontrol lera värdet för data in och data ut.
 
3. Öppna Virtual Network gateway i resurs gruppen. Visa diagrammen för **Total tunnel** ingångar och **Total tunnel utgående**.
 
## <a name="debug-issues"></a>Felsöka problem

Använd följande kommandon för att felsöka eventuella problem:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Exemplet på utdata visas nedan:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Nästa steg

[Konfigurera VPN via lokalt användar gränssnitt på din Azure Stack Edge Pro R-enhet](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
