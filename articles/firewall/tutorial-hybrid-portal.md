---
title: 'Självstudie: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal'
description: I den här självstudien får du lära dig hur du distribuerar och konfigurerar Azure-brandväggen med Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 86e27c190b269763d8dd2f562a207b3f2020da29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98051079"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Självstudie: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal

När du ansluter ditt lokala nätverk till ett virtuellt Azure-nätverk för att skapa ett hybridnätverk är förmågan att styra åtkomst till dina Azure-nätverksresurser en viktig del av den övergripande säkerhetsplanen.

Du kan använda Azure Firewall för att styra nätverksåtkomst i ett hybridnätverk med hjälp av regler som definierar tillåten respektive nekad nätverkstrafik.

För den här självstudien skapar du tre virtuella nätverk:

- **VNet-Hub** – brandväggen finns i det här virtuella nätverket.
- **VNet-Spoke** – det virtuella ekernätverket representerar den arbetsbelastning som finns på Azure.
- **VNet-Onprem** – det lokala virtuella nätverket representerar ett lokalt nätverk. I en verklig distribution kan den anslutas antingen via en VPN-eller ExpressRoute-anslutning. För enkelhetens skull använder den här självstudien en VPN-gatewayanslutning, och ett virtuellt Azure-nätverk används för att representera ett lokalt nätverk.

![Brandvägg i ett hybridnätverk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Deklarera variablerna
> * Skapa brandväggens virtuella hubbnätverk
> * Skapa det virtuella ekernätverket
> * Skapa det lokala virtuella nätverket
> * Konfigurera och distribuera brandväggen
> * Skapa och ansluta VPN-gatewayer
> * Peera de virtuella hubb- och ekernätverken
> * Skapa vägarna
> * Skapa de virtuella datorerna
> * testa brandväggen.

Om du vill använda Azure PowerShell i stället för att slutföra den här proceduren, se [distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Förutsättningar

Ett hybrid nätverk använder arkitektur modellen hubb-och-eker för att dirigera trafik mellan Azure virtuella nätverk och lokala nätverk. NAV-och-eker-arkitekturen har följande krav:

- Ange **AllowGatewayTransit** när peering-VNet-Hub till VNet-eker. I en nav-och-eker-nätverks arkitektur gör en gateway-överföring de ekrar som virtuella nätverk kan dela VPN-gatewayen i hubben, i stället för att distribuera VPN-gatewayer i varje ekrar virtuellt nätverk. 

   Dessutom kommer vägar till de Gateway-anslutna virtuella nätverken eller lokala nätverken automatiskt att spridas till vägvals tabellerna för de peer-kopplade virtuella nätverken med hjälp av Gateway-överföringen. Mer information finns i [Konfigurera VPN gateway-överföring för virtuell nätverks-peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ange **UseRemoteGateways** när du peer-VNet-Spoke till VNet-hubb. Om **UseRemoteGateways** har angetts och **AllowGatewayTransit** på fjärr-peering också anges, använder det eker-virtuella nätverket gatewayer för det virtuella fjärrnätverket för överföring.
- Du kan använda en användardefinierad väg (UDR) som pekar på brand väggen med det **virtuella nätverkets gateway** -alternativ inaktiverat om du vill dirigera den ekrar som används för att dirigera under nätets trafik via nav brand väggen. Alternativet **väg spridning av virtuell nätverks-Gateway** förhindrar väg distribution till ekrarnas undernät. Detta förhindrar att inlärda vägar hamnar i konflikt med din UDR. Om du vill behålla **spridning av väg för virtuell nätverks-Gateway** aktive rad, se till att definiera vissa vägar i brand väggen för att åsidosätta de som publiceras lokalt via BGP.
- Konfigurera en UDR på hubb-gatewayens undernät som pekar på brand väggens IP-adress som nästa hopp till ekrarnas nätverk. Det krävs ingen UDR i Azure Firewall-undernätet eftersom det lär sig vägarna från BGP.

Se avsnittet [skapa vägar](#create-the-routes) i den här självstudien för att se hur dessa vägar skapas.

>[!NOTE]
>Azure Firewall måste ha direktanslutning till internet. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP måste du åsidosätta detta med en 0.0.0.0/0-UDR med **NextHopType** -värdet som **Internet** för att upprätthålla direkt Internet anslutning.
>
>Azure-brandväggen kan konfigureras för att stödja Tvingad tunnel trafik. Mer information finns i [Tvingad tunnel trafik i Azure Firewall](forced-tunneling.md).

>[!NOTE]
>Trafiken mellan direkt peerkopplade virtuella nätverk dirigeras direkt även om en UDR pekar på Azure Firewall som standardgateway. För att undernät till undernät-trafik ska kunna skickas till brandväggen i det här scenariot måste en UDR uttryckligen innehålla nätverksprefixet för målundernätverket på båda undernäten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-the-firewall-hub-virtual-network"></a>Skapa brandväggens virtuella hubbnätverk

Skapa först den resursgrupp som ska innehålla resurserna för den här självstudien:

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. På sidan Azure Portal start väljer du **resurs grupper**  >  **Lägg till**.
3. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resurs grupps namn** skriver du **VB-hybrid-test**.
2. För **region** väljer du **(US) USA, östra**. Alla resurser som du skapar senare måste finnas på samma plats.
3. Välj **Granska + skapa**.
4. Välj **Skapa**.

Skapa nu VNet:

> [!NOTE]
> Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk** under **nätverk**.
1. Välj **Skapa**.
1. För **resurs grupp** väljer du **VB-hybrid-test**.
1. I **namn** skriver du **VNet-Hub**.
1. Välj **Nästa: IP-adresser**.
1. Ta bort standard adressen och skriv **10.5.0.0/16** för **IPv4-adress utrymme**.
1. Under **under näts namn** väljer du **Lägg till undernät**.
1. För **under näts namn** skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
1. Skriv **10.5.0.0/26** för **under nätets adress intervall**. 
1. Välj **Lägg till**.
1. Välj **Granska + skapa**.
1. Välj **Skapa**.

## <a name="create-the-spoke-virtual-network"></a>Skapa det virtuella ekernätverket

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. I **nätverk** väljer du **virtuellt nätverk**.
7. För **resurs grupp** väljer du **VB-hybrid-test**.
1. För **namn** skriver du **VNet-eker**.
2. För **region** väljer du **(US) USA, östra**.
3. Välj **Nästa: IP-adresser**.
4. Ta bort standard adressen och skriv **10.6.0.0/16** för **IPv4-adress utrymme**.
6. Under **under näts namn** väljer du **Lägg till undernät**.
7. För **under näts namn** skriver du **SN-arbets belastning**.
8. Skriv **10.6.0.0/24** för **under nätets adress intervall**. 
9. Välj **Lägg till**.
10. Välj **Granska + skapa**.
11. Välj **Skapa**.

## <a name="create-the-on-premises-virtual-network"></a>Skapa det lokala virtuella nätverket

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. I **nätverk** väljer du **virtuellt nätverk**.
7. För **resurs grupp** väljer du **VB-hybrid-test**.
1. För **namn** skriver du **VNet-OnPrem**.
2. För **region** väljer du **(US) USA, östra**.
3. Välj **Nästa: IP-adresser**
4. Ta bort standard adressen för **IPv4-adress utrymmet** och skriv **192.168.0.0/16**.
5. Under **under näts namn** väljer du **Lägg till undernät**.
7. För **under näts namn** skriver du **SN-Corp**.
8. Skriv **192.168.1.0/24** för **under nätets adress intervall**. 
9. Välj **Lägg till**.
10. Välj **Granska + skapa**.
11. Välj **Skapa**.

Skapa nu ett andra undernät för gatewayen.

1. På sidan **VNet-OnPrem** väljer du **undernät**.
2. Välj **+ undernät**.
3. I **namn** skriver du **GatewaySubnet**.
4. För **under nätets adress intervall** skriver du **192.168.2.0/24**.
5. Välj **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurera och distribuera brandväggen

Distribuera nu brand väggen i brand Väggs hubbens virtuella nätverk.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **nätverk** i den vänstra kolumnen och Sök efter och välj sedan **brand vägg**.
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Prenumeration     |\<your subscription\>|
   |Resursgrupp     |**VB-hybrid-test** |
   |Name     |**AzFW01**|
   |Region     |**East US**|
   |Välj ett virtuellt nätverk     |**Använd befintlig**:<br> **VNet-hubb**|
   |Offentlig IP-adress     |Lägg till ny: <br>**VB-pip**. |

5. Välj **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **skapa** för att skapa brand väggen.

   Det tar några minuter att distribuera.
7. När distributionen är klar går du till resurs gruppen **VB-hybrid-test** och väljer **AzFW01** -brandväggen.
8. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

### <a name="configure-network-rules"></a>Konfigurera nätverksregler

Lägg först till en nätverks regel för att tillåta webb trafik.

1. På sidan **AzFW01** väljer du **regler**.
2. Välj fliken **nätverks regel samling** .
3. Välj **Lägg till regel samling för nätverk**.
4. I **namn** skriver du **RCNet01**.
5. För **prioritet**, Skriv **100**.
6. I fältet **Åtgärd** väljer du **Tillåt**.
6. Under **regler** anger du **AllowWeb** som **namn**.
7. I fältet **Protokoll** väljer du **TCP**.
8. I **typ av källa** väljer du **IP-adress**.
9. För **källa** skriver du **192.168.1.0/24**.
10. För **måltyp** väljer du **IP-adress**.
11. För **mål adress** skriver du **10.6.0.0/16**
12. För **mål portar** skriver du **80**.

Lägg nu till en regel för att tillåta RDP-trafik.

Skriv följande information på den andra regel raden:

1. **Namn**, Skriv **AllowRDP**.
2. I fältet **Protokoll** väljer du **TCP**.
3. I **typ av källa** väljer du **IP-adress**.
4. För **källa** skriver du **192.168.1.0/24**.
5. För **måltyp** väljer du **IP-adress**.
6. För **mål adress** skriver du **10.6.0.0/16**
7. För **mål portar** skriver du **3389**.
8. Välj **Lägg till**.

## <a name="create-and-connect-the-vpn-gateways"></a>Skapa och ansluta VPN-gatewayer

De virtuella hubbnätverken och de lokala virtuella nätverken ansluts via VPN-gatewayer.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Skapa en VPN-gateway för det virtuella hubbnätverket

Skapa nu VPN-gatewayen för det virtuella hubbnätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **virtuell nätverksgateway** i text rutan Sök.
3. Välj **virtuell nätverksgateway** och välj **skapa**.
4. I **namn** skriver du **GW-hubb**.
5. För **region** väljer du samma region som du använde tidigare.
6. För **Gateway-typ** väljer du **VPN**.
7. För **VPN-typ** väljer du **Route-based**.
8. För **SKU** väljer du **Basic**.
9. För **virtuellt nätverk** väljer du **VNet-Hub**.
10. För **offentlig IP-adress** väljer du **Skapa ny** och skriver **VNet-Hub-GW-pip** som namn.
11. Acceptera återstående standardvärden och välj sedan **Granska + skapa**.
12. Granska konfigurationen och välj sedan **skapa**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Skapa en VPN-gateway för det lokala virtuella nätverket

Skapa nu VPN-gatewayen för det lokala virtuella nätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. I sökrutan skriver du **virtuell nätverksgateway** och trycker på **RETUR**.
3. Välj **virtuell nätverksgateway** och välj **skapa**.
4. Som **namn** skriver du **GW-OnPrem**.
5. För **region** väljer du samma region som du använde tidigare.
6. För **Gateway-typ** väljer du **VPN**.
7. För **VPN-typ** väljer du **Route-based**.
8. För **SKU** väljer du **Basic**.
9. För **virtuellt nätverk** väljer du **VNet-OnPrem**.
10. För **offentlig IP-adress** väljer du **Skapa ny** och skriver **VNet-OnPrem-GW-pip** som namn.
11. Acceptera återstående standardvärden och välj sedan **Granska + skapa**.
12. Granska konfigurationen och välj sedan **skapa**.

### <a name="create-the-vpn-connections"></a>Skapa VPN-anslutningarna

Nu kan du skapa VPN-anslutningarna mellan hubben och lokala gatewayer.

I det här steget skapar du anslutningen från det virtuella hubbnätverket till det lokala virtuella nätverket. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

1. Öppna resurs gruppen **VB-hybrid-test** och välj **GW-hubb-** gatewayen.
2. Välj **anslutningar** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Anslutnings namnet, Skriv **hubb-till-OnPrem**.
5. Välj **VNet-till-VNet** som **Anslutnings typ**.
6. För den **andra virtuella Nätverksgatewayen** väljer du **GW-OnPrem**.
7. För **delad nyckel (PSK)** skriver du **AzureA1b2C3**.
8. Välj **OK**.

Skapa anslutningen mellan det lokala virtuella nätverket och det virtuella hubbnätverket. Det här steget liknar det föregående steget förutom att du skapar anslutningen från Vnet-Onprem till VNet-hub. Kontrollera att de delade nycklarna matchar. Anslutningen upprättas efter några minuter.

1. Öppna resurs gruppen **VB-hybrid-test** och välj **GW-OnPrem-** gatewayen.
2. Välj **anslutningar** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Skriv **OnPrem-to-Hub** som anslutnings namn.
5. Välj **VNet-till-VNet** som **Anslutnings typ**.
6. För den **andra virtuella Nätverksgatewayen** väljer du **GW-hubb**.
7. För **delad nyckel (PSK)** skriver du **AzureA1b2C3**.
8. Välj **OK**.


#### <a name="verify-the-connection"></a>Verifiera anslutningen

Efter ungefär fem minuter bör statusen för båda anslutningarna vara **ansluten**.

![Gatewayanslutningar](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peera de virtuella hubb- och ekernätverken

Peera nu de virtuella hubb- och ekernätverken.

1. Öppna resurs gruppen **VB-hybrid-test** och välj det virtuella nätverk för **VNet-hubb** .
2. Välj **peering** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. Under **det här virtuella nätverket**:
 
   
   |Inställningsnamn  |Värde  |
   |---------|---------|
   |Namn på peering-länk| HubtoSpoke|
   |Trafik till fjärranslutet virtuellt nätverk|   Tillåt (standard)      |
   |Trafik vidarebefordrad från fjärran slutet virtuellt nätverk    |   Tillåt (standard)      |
   |Virtuell nätverksgateway     |  Använd det här virtuella nätverkets gateway       |
    
5. Under **fjärran slutet virtuellt nätverk**:

   |Inställningsnamn  |Värde  |
   |---------|---------|
   |Namn på peering-länk | SpoketoHub|
   |Distributions modell för virtuellt nätverk| Resource manager|
   |Prenumeration|\<your subscription\>|
   |Virtuellt nätverk| VNet-Spoke
   |Trafik till fjärranslutet virtuellt nätverk     |   Tillåt (standard)      |
   |Trafik vidarebefordrad från fjärran slutet virtuellt nätverk    |   Tillåt (standard)      |
   |Virtuell nätverksgateway     |  Använda det virtuella fjärrnätverkets Gateway       |

5. Välj **Lägg till**.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="VNet-peering":::

## <a name="create-the-routes"></a>Skapa vägarna

Därefter skapar du några vägar:

- En väg från hubbgateway-undernätet till ekerundernätet via brandväggens IP-adress
- En standardväg från ekerundernätet via brandväggens IP-adress

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **routningstabell** i text rutan och tryck på **RETUR**.
3. Välj **routningstabell**.
4. Välj **Skapa**.
6. Välj mappen **VB-hybrid-test** för resurs gruppen.
8. För **region** väljer du samma plats som du använde tidigare.
1. Skriv **UDR-Hub-eker** som namn.
9. Välj **Granska + skapa**.
10. Välj **Skapa**.
11. När du har skapat routningstabellen väljer du den för att öppna sidan väg tabell.
12. Välj **vägar** i den vänstra kolumnen.
13. Välj **Lägg till**.
14. Skriv **ToSpoke** som väg namn.
15. För adressprefixet skriver du **10.6.0.0/16**.
16. För nästa hopp typ väljer du **virtuell** installation.
17. För nästa hopp adress skriver du brand väggens privata IP-adress som du noterade tidigare.
18. Välj **OK**.

Koppla nu vägen till under nätet.

1. På sidan **UDR-Hub-eker-routes** väljer du **undernät**.
2. Välj **associera**.
3. Under **virtuellt nätverk** väljer du **VNet-Hub**.
1. Under **undernät** väljer du **GatewaySubnet**.
2. Välj **OK**.

Skapa nu standard vägen från eker-undernätet.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **routningstabell** i text rutan och tryck på **RETUR**.
3. Välj **routningstabell**.
5. Välj **Skapa**.
7. Välj mappen **VB-hybrid-test** för resurs gruppen.
8. För **region** väljer du samma plats som du använde tidigare.
1. Skriv **UDR-GD** som namn.
4. För **spridning av Gateway-väg** väljer du **Nej**.
5. Välj **Granska + skapa**.
6. Välj **Skapa**.
7. När du har skapat routningstabellen väljer du den för att öppna sidan väg tabell.
8. Välj **vägar** i den vänstra kolumnen.
9. Välj **Lägg till**.
10. Skriv **ToHub** som väg namn.
11. För adressprefixet skriver du **0.0.0.0/0**.
12. För nästa hopp typ väljer du **virtuell** installation.
13. För nästa hopp adress skriver du brand väggens privata IP-adress som du noterade tidigare.
14. Välj **OK**.

Koppla nu vägen till under nätet.

1. På sidan **UDR-DG-routes** väljer du **undernät**.
2. Välj **associera**.
3. Under **virtuellt nätverk** väljer du **VNet-eker**.
1. Under **undernät** väljer du **SN-arbets belastning**.
2. Välj **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu ekerarbetsbelastningen och de lokala virtuella datorerna, och placera dem i respektive undernät.

### <a name="create-the-workload-virtual-machine"></a>Skapa den virtuella arbetsbelastningsdatorn

Skapa en virtuell dator i det virtuella eker-nätverket som kör IIS, utan offentlig IP-adress.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Under **populär** väljer du **Windows Server 2016 Data Center**.
3. Ange följande värden för den virtuella datorn:
    - **Resurs grupp** – Välj **VB-hybrid-test**.
    - **Namn på virtuell dator**: *VM-eker-01*.
    - **Region** – samma region som du har använt tidigare.
    - **Användar namn**: \<type a user name\> .
    - **Lösen ord**: \<type a password\>
4. För **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **http (80)** och **RDP (3389)**
4. Välj **Nästa:Diskar**.
5. Acceptera standardvärdena och välj **Nästa: nätverk**.
6. Välj **VNet-eker** för det virtuella nätverket och under nätet är **SN – arbets belastning**.
7. För **offentlig IP-adress** väljer du **ingen**. 
9. Välj **Nästa: hantering**.
10. För **startdiagnostik** väljer du **inaktivera**.
11. Välj **Granska + skapa**, granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

### <a name="install-iis"></a>Installera IIS

1. Öppna Cloud Shell från Azure Portal och kontrol lera att den är inställd på **PowerShell**.
2. Kör följande kommando för att installera IIS på den virtuella datorn och ändra platsen om det behövs:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Skapa den lokala virtuella datorn

Det här är en virtuell dator som du använder för att ansluta via fjärr skrivbord till den offentliga IP-adressen. Därifrån kan du sedan ansluta till den lokala servern via brandväggen.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Under **populär** väljer du **Windows Server 2016 Data Center**.
3. Ange följande värden för den virtuella datorn:
    - **Resurs grupp** – Välj befintlig och välj sedan **VB-hybrid-test**.
    - **Namn på**  -  virtuell dator *VM-OnPrem*.
    - **Region** – samma region som du har använt tidigare.
    - **Användar namn**: \<type a user name\> .
    - **Lösen ord**: \<type a user password\> .
7. För **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)**
4. Välj **Nästa:Diskar**.
5. Acceptera standardvärdena och välj **Nästa: nätverk**.
6. Välj **VNet-OnPrem** för det virtuella nätverket och under nätet är **SN-Corp**.
8. Välj **Nästa: hantering**.
10. För **startdiagnostik** väljer du **inaktivera**.
10. Välj **Granska + skapa**, granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

## <a name="test-the-firewall"></a>testa brandväggen.

1. Anteckna först den privata IP-adressen för den virtuella datorn med **ekrar 01** .

2. Från Azure-portalen ansluter du till den virtuella datorn **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Öppna en webbläsare på **VM-OnPrem** och gå till http:// \<VM-spoke-01 private IP\> .

   Du bör se webb sidan **VM-eker-01** : ![ VM-eker-01-webbsida](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Från den virtuella datorn **OnPrem** den virtuella datorn öppnar du ett fjärr skrivbord till **VM-ekrar-01** på den privata IP-adressen.

   Anslutningen bör lyckas och du bör kunna logga in.

Nu har du verifierat att brand Väggs reglerna fungerar:

<!---- You can ping the server on the spoke VNet.--->
- Du kan bläddra i webbservern på det virtuella ekernätverket.
- Du kan ansluta till servern på det virtuella ekernätverket med hjälp av RDP.

Ändra sedan brandväggsnätverksregelns insamlingsåtgärd till **Neka** för att verifiera att brandväggsreglerna fungerar som förväntat.

1. Välj **AzFW01** -brandväggen.
2. Välj **regler**.
3. Välj fliken **regel samling för nätverk** och välj regel samlingen **RCNet01** .
4. För **åtgärd** väljer du **neka**.
5. Välj **Spara**.

Stäng alla befintliga fjärrskrivbord innan du testar de ändrade reglerna. Kör nu testerna igen. De bör alla misslyckas den här gången.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **FW-Hybrid-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

Därefter kan du övervaka Azure Firewall-loggarna.

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)