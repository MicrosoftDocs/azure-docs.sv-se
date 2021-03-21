---
title: Konfigurera VPN på din Azure Stack Edge-Mini R-enhet med Azure PowerShell
description: Beskriver hur du konfigurerar VPN på din Azure Stack Edge-Mini R-enhet med ett Azure PowerShell-skript för att skapa Azure-resurser.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102630970"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Konfigurera VPN på din Azure Stack Edge-Mini R-enhet via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

VPN-alternativet ger ett sekundärt krypterings lager för data i rörelse över *TLS* från din Azure Stack Edge mini r-eller Azure Stack Edge Pro r-enhet till Azure. Du kan konfigurera VPN på din Azure Stack Edge-Mini R-enhet via Azure Portal eller via Azure PowerShell. 

I den här artikeln beskrivs de steg som krävs för att konfigurera en punkt-till-plats (P2S) VPN på din Azure Stack Edge Mini R-enhet med ett Azure PowerShell-skript för att skapa konfigurationen i molnet. Konfigurationen på Azure Stack Edge-enheten görs via det lokala användar gränssnittet.

## <a name="about-vpn-setup"></a>Om VPN-konfiguration

Med en P2S VPN gateway-anslutning kan du skapa en säker anslutning till ditt virtuella nätverk från en enskild klient dator eller din Azure Stack Edge Mini R-enhet. Du startar P2S-anslutningen från klient datorn eller enheten. P2S-anslutningen i det här fallet använder IKEv2 VPN, en standardbaserad IPsec VPN-lösning.

Vanliga arbets flöden innehåller följande steg:

1. Konfigurera förutsättningar.
2. Konfigurera nödvändiga resurser i Azure.
    1. Skapa och konfigurera ett virtuellt nätverk och nödvändiga undernät. 
    2. Skapa och konfigurera en Azure VPN-gateway (virtuell nätverksgateway).
    3. Konfigurera Azure-brandväggen och Lägg till nätverks-och app-regler.
    4. Skapa Azure vägvals tabeller och Lägg till vägar.
    5. Aktivera punkt-till-plats i VPN-gateway.
        1. Lägg till klientens adresspool.
        2. Konfigurera tunnel typ.
        3. Konfigurera autentiseringstyp.
        4. Skapa certifikat.
        5. Ladda upp certifikat.
    6. Hämta telefon bok.
3. Konfigurera VPN i det lokala webb gränssnittet på enheten. 
    1. Ange telefon bok.
    2. Ange service tag-filen (JSON).


De detaljerade stegen beskrivs i följande avsnitt.

## <a name="configure-prerequisites"></a>Konfigurera krav

- Du bör ha åtkomst till en Azure Stack Edge-Mini R-enhet som är installerad enligt anvisningarna i [installera din Azure Stack Edge mini r-enhet](azure-stack-edge-mini-r-deploy-install.md). Enheten kommer att upprätta en P2S-anslutning med Azure. 

- Du bör ha åtkomst till en giltig Azure-prenumeration som är aktive rad för Azure Stack Edge-tjänsten i Azure. Använd den här prenumerationen för att skapa en motsvarande resurs i Azure för att hantera din Azure Stack Edge Mini R-enhet.  

- Du har åtkomst till en Windows-klient som du kommer att använda för att få åtkomst till din Azure Stack Edge Mini R-enhet. Du använder den här klienten för att program mässigt skapa konfigurationen i molnet.

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
        Ange namnet på den Azure-prenumeration som du använder med din Azure Stack Edge Mini R-enhet för att konfigurera VPN.

    3. [Hämta skriptet](https://aka.ms/ase-vpn-deployment) som krävs för att skapa en konfiguration i molnet. Skriptet kommer att:
        
        - Skapa ett virtuellt Azure-nätverk och följande undernät: *GatewaySubnet* och *AzureFirewallSubnet*.
        - Skapa och konfigurera en Azure VPN-gateway.
        - Skapa och konfigurera en lokal Azure-nätverksgateway.
        - Skapa och konfigurera en Azure VPN-anslutning mellan Azure VPN-gatewayen och den lokala Nätverksgatewayen.
        - Skapa en Azure-brandvägg och Lägg till nätverks regler, app-regler.
        - Skapa en Azure vägvals tabell och Lägg till vägar i den.

    4. Skapa resurs gruppen i den Azure Portal som du vill att Azure-resurserna ska skapas under. Gå till listan över tjänster i Azure Portal, Välj **resurs grupp** och välj sedan **+ Lägg till**. Ange prenumerations informationen och namnet på resurs gruppen och välj sedan **skapa**. Om du går till den här resurs gruppen bör den inte ha några resurser under den här tiden.

        ![Azure-resursgrupp](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Du måste ha ett bas 64-kodat certifikat i `.cer` formatet för din Azure Stack Edge Mini R-enhet. Det här certifikatet ska överföras till din Azure Stack Edge-enhet som `pfx` en privat nyckel. Det här certifikatet måste också installeras i den betrodda roten i butiken på klienten som försöker upprätta P2S-anslutningen.

## <a name="use-the-script"></a>Använd skriptet

Först ändrar du `parameters-p2s.json` filen för att mata in parametrarna. Sedan kör du skriptet med den ändrade JSON-filen.

Vart och ett av dessa steg beskrivs i följande avsnitt.

### <a name="download-service-tags-file"></a>Hämta service tag-filen

Du kanske redan har en `ServiceTags.json` fil i mappen där du laddade ned skriptet. Om inte kan du ladda ned filen med tjänst koder.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Ändra parameter fil

Det första steget är att ändra `parameters-p2s.json` filen och spara ändringarna. 

För de Azure-resurser som du skapar ger du följande namn:

|Parameternamn  |Beskrivning  |
|---------|---------|
|virtualNetworks_vnet_name    | Namn på Azure-Virtual Network        |
|azureFirewalls_firewall_name     | Namn på Azure-brandvägg        |
|routeTables_routetable_name     | Namn på Azure route-tabell        |
|publicIPAddresses_VNGW_public_ip_name     | Namn på offentlig IP-adress för din virtuella nätverksgateway       |
|virtualNetworkGateways_VNGW_name    | Namn på Azure VPN gateway (virtuell nätverksgateway)        |
|publicIPAddresses_firewall_public_ip_name     | Namn på offentlig IP-adress för din Azure-brandvägg         |
|location     |Det här är den region där du vill skapa ditt virtuella nätverk. Välj samma region som den som är kopplad till din enhet.         |
|RouteTables_routetable_onprem_name| Det här är namnet på den ytterligare routningstabellen som hjälper brand väggen att dirigera paket tillbaka till Azure Stack gräns enheten. Skriptet skapar två ytterligare vägar och kopplar *standard* -och *FirewallSubnet* till den här väg tabellen.|

Ange följande IP-adresser och adress utrymmen för de Azure-resurser som skapas inklusive det virtuella nätverket och associerade undernät (*standard*, *brand vägg*, *GatewaySubnet*).

|Parameternamn  |Beskrivning  |
|---------|---------|
|VnetIPv4AddressSpace    | Detta är adress utrymmet som är kopplat till det virtuella nätverket. Ange VNet IP-intervall som privat IP-intervall ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `Default` under nätet för det virtuella nätverket.         |
|FirewallSubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `Firewall` under nätet för det virtuella nätverket.          |
|GatewaySubnetIPv4AddressSpace    |Det här är adress utrymmet som är kopplat till `GatewaySubnet` för det virtuella nätverket.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Detta är den IP-adress som är reserverad för BGP-kommunikation och baseras på det adress utrymme som är kopplat till `GatewaySubnet` för det virtuella nätverket.          |
|ClientAddressPool    | Den här IP-adressen används för adresspoolen i P2S-konfigurationen i Azure Portal.         |
|PublicCertData     | Offentliga certifikat data används av VPN Gateway för att autentisera P2S-klienter som ansluter till den. Installera rot certifikatet för att hämta certifikat data. Kontrol lera att certifikatet är Base-64-kodat med fil namns tillägget. cer. Öppna det här certifikatet och kopiera texten i certifikatet mellan = = BEGIN CERTIFICATe = = och = = END CERTIFICATe = = på en sammanhängande rad.     |



### <a name="run-the-script"></a>Kör skriptet

Följ dessa steg om du vill använda den ändrade `parameters-p2s.json` och köra skriptet för att skapa Azure-resurser.

1. Kör PowerShell. Växla till den katalog där skriptet finns.

3. Kör skriptet.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > I den här versionen fungerar skriptet enbart på platsen för östra USA.

    Du måste mata in följande information när du kör skriptet:

    
    |Parameter  |Beskrivning  |
    |---------|---------|
    |Location     |Det här är den region där Azure-resurserna måste skapas.         |
    |AzureAppRuleFilePath     | Detta är fil Sök vägen för `appRule.json` .       |
    |AzureIPRangesFilePath     |Det här är den JSON-fil för service tag som du laddade ned i det tidigare steget.         |
    |ResourceGroupName     | Detta är namnet på den resurs grupp som alla Azure-resurser skapas under.        |
    |AzureDeploymentName    |Det här är namnet på din Azure-distribution.         |
    |NetworkRuleCollectionName            | Detta är namnet på samlingen av alla nätverks regler som skapas och läggs till i din Azure-brandvägg.             |
    |Prioritet            | Detta är den prioritet som tilldelas alla nätverks-och program regler som skapas.              |
    |AppRuleCollectionName            |Detta är namnet på samlingen av alla program regler som skapas och läggs till i din Azure-brandvägg.                |


    Ett exempel på utdata visas nedan.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Det tar ungefär 90 minuter att köra skriptet. Se till att logga in på din nätverks rättighet innan skriptet startar.
    > - Om det av någon anledning är en misslyckad session med skriptet, se till att ta bort resurs gruppen för att ta bort alla resurser som skapas under den.
  
    
    När skriptet har slutförts skapas en distributions logg i samma mapp som skriptet finns i.


## <a name="verify-the-azure-resources"></a>Verifiera Azure-resurserna

När du har kört skriptet kontrollerar du att alla resurser har skapats i Azure. Gå till den resurs grupp som du skapade. Du bör se följande resurser:

![Azure-resurser](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Ladda ned telefon bok för VPN-profil

I det här steget ska du ladda ned VPN-profilen för enheten.

1. I Azure Portal går du till resurs gruppen och väljer sedan den virtuella nätverksgateway som du skapade i föregående steg.

    ![Virtuell Azure-nätverksgateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Gå till **inställningar > punkt-till-plats-konfiguration**. Välj **Ladda ned VPN-klient**.

    ![Aktivera P2S-konfiguration 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Spara den zippade profilen och extrahera på Windows-klienten.

    ![Aktivera P2S-konfiguration 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Gå till mappen *WindowsAmd64* och extrahera sedan `.exe` : *VpnClientSetupAmd64.exe*.

    ![Aktivera P2S-konfiguration 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Skapa en tillfällig sökväg. Exempel:

    `C:\NewTemp\vnet\tmp`

4. Kör PowerShell och gå till den katalog där `.exe` finns. Kör genom att `.exe` skriva:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Den tillfälliga sökvägen kommer att ha nya filer. Här är exempel på utdata:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. *PBK* -filen är telefon boken för VPN-profilen. Du kommer att använda det här i det lokala användar gränssnittet.


## <a name="vpn-configuration-on-the-device"></a>VPN-konfiguration på enheten

Följ de här stegen i det lokala användar gränssnittet för din Azure Stack Edge-enhet.

1. I det lokala användar gränssnittet går du till **VPN-** sidan. Under VPN-tillstånd väljer du **Konfigurera**.

    ![Konfigurera VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. På bladet **Konfigurera VPN** :
    
    1. I Ladda upp telefon boks filen pekar du på PBK-filen som du skapade i föregående steg.
    2. I konfigurations filen Ladda upp offentlig IP-lista anger du JSON-filen för Azure Data Center IP-intervall som indata. Du har laddat ned filen i ett tidigare steg från: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Välj **öster** som region och välj **Använd**.

    ![Konfigurera VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. I avsnittet **IP-adressintervall som ska nås via endast VPN** anger du det virtuella nätverkets IPv4-intervall som du har valt för din Azure-Virtual Network.

    ![Konfigurera VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Verifiera klient anslutning

1. I Azure Portal går du till VPN-gatewayen.
2. Gå till **inställningar > punkt-till-plats-konfiguration**. Under **allokerade IP-adresser** ska IP-adressen för din Azure Stack gräns enhet visas.

## <a name="validate-data-transfer-through-vpn"></a>Verifiera data överföring via VPN

För att bekräfta att VPN fungerar kopierar du data till en SMB-resurs. Följ stegen i [Lägg till en resurs](azure-stack-edge-gpu-manage-shares.md#add-a-share) på din Azure Stack Edge-enhet. 

1. Kopiera en fil, till exempel \data\pictures\waterfall.jpg till den SMB-resurs som du monterade i klient systemet. 
2. För att verifiera att data går via VPN, medan data kopieras:

    1. Gå till VPN-gatewayen i Azure Portal. 

    2. Gå till **övervakning > mått**.

    3. I den högra rutan väljer du **omfånget** som VPN-gateway, **mått** som gateway P2s-bandbredd och **agg regering** som AVG.

    4. När data kopieras visas en ökning av bandbredds användningen och när data kopieringen är klar tas bandbredds användningen bort.

        ![Azure VPN-mått](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Kontrol lera att filen visas i ditt lagrings konto i molnet.
 
## <a name="debug-issues"></a>Felsöka problem

Använd följande kommandon för att felsöka eventuella problem:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Exemplet på utdata visas nedan:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[Konfigurera VPN via det lokala användar gränssnittet på din Azure Stack Edge-enhet](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).