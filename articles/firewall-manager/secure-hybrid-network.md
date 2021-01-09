---
title: 'Självstudie: skydda ditt virtuella hubb nätverk med hjälp av Azure Firewall Manager'
description: I den här självstudien får du lära dig hur du skyddar ditt virtuella nätverk med Azure Firewall Manager med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 5d662efc927235711c4dd5e8a424d8775279c30b
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048053"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Självstudie: skydda ditt virtuella hubb nätverk med hjälp av Azure Firewall Manager

När du ansluter ditt lokala nätverk till ett virtuellt Azure-nätverk för att skapa ett hybridnätverk är förmågan att styra åtkomst till dina Azure-nätverksresurser en viktig del av den övergripande säkerhetsplanen.

Med hjälp av Azure Firewall Manager kan du skapa ett nav virtuellt nätverk för att skydda din hybrid nätverks trafik som är avsedd för privata IP-adresser, Azure-PaaS och Internet. Du kan använda Azure Firewall Manager för att kontrol lera nätverks åtkomst i ett hybrid nätverk med principer som definierar tillåten och nekad nätverks trafik.

Firewall Manager stöder också en säker arkitektur för virtuella hubbar. En jämförelse av arkitektur typerna för den säkra virtuella hubben och hubben för virtuella nätverk finns i [Vad är arkitektur alternativ för Azure Firewall Manager?](vhubs-and-vnets.md)

För den här självstudien skapar du tre virtuella nätverk:

- **VNet-Hub** – brandväggen finns i det här virtuella nätverket.
- **VNet-Spoke** – det virtuella ekernätverket representerar den arbetsbelastning som finns på Azure.
- **VNet-Onprem** – det lokala virtuella nätverket representerar ett lokalt nätverk. I en verklig distribution kan den anslutas antingen via en VPN-eller ExpressRoute-anslutning. För enkelhetens skull använder den här självstudien en VPN-gatewayanslutning, och ett virtuellt Azure-nätverk används för att representera ett lokalt nätverk.

![Hybridnätverk](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en brand Väggs princip
> * Skapa de virtuella nätverken
> * Konfigurera och distribuera brandväggen
> * Skapa och ansluta VPN-gatewayer
> * Peera de virtuella hubb- och ekernätverken
> * Skapa vägarna
> * Skapa de virtuella datorerna
> * testa brandväggen.


## <a name="prerequisites"></a>Förutsättningar

Ett hybrid nätverk använder arkitektur modellen hubb-och-eker för att dirigera trafik mellan Azure virtuella nätverk och lokala nätverk. NAV-och-eker-arkitekturen har följande krav:

- Ange **AllowGatewayTransit** när peering-VNet-Hub till VNet-eker. I en nav-och-eker-nätverks arkitektur gör en gateway-överföring de ekrar som virtuella nätverk kan dela VPN-gatewayen i hubben, i stället för att distribuera VPN-gatewayer i varje ekrar virtuellt nätverk. 

   Dessutom kommer vägar till de Gateway-anslutna virtuella nätverken eller lokala nätverken automatiskt att spridas till vägvals tabellerna för de peer-kopplade virtuella nätverken med hjälp av Gateway-överföringen. Mer information finns i [Konfigurera VPN gateway-överföring för virtuell nätverks-peering](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ange **UseRemoteGateways** när du peer-VNet-Spoke till VNet-hubb. Om **UseRemoteGateways** har angetts och **AllowGatewayTransit** på fjärr-peering också anges, använder det eker-virtuella nätverket gatewayer för det virtuella fjärrnätverket för överföring.
- Om du vill dirigera eker-undernätets trafik via nav brand väggen behöver du en användardefinierad väg (UDR) som pekar på brand väggen med inställningen för **väg spridningen av virtuell nätverks-Gateway** inaktive rad. Det här alternativet förhindrar väg distribution till ekrarnas undernät. Detta förhindrar att inlärda vägar hamnar i konflikt med din UDR.
- Konfigurera en UDR på hubb-gatewayens undernät som pekar på brand väggens IP-adress som nästa hopp till ekrarnas nätverk. Det krävs ingen UDR i Azure Firewall-undernätet eftersom det lär sig vägarna från BGP.

Se avsnittet [skapa vägar](#create-the-routes) i den här självstudien för att se hur dessa vägar skapas.

>[!NOTE]
>Azure Firewall måste ha direktanslutning till internet. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP måste du åsidosätta detta med en 0.0.0.0/0-UDR med **NextHopType** -värdet som **Internet** för att upprätthålla direkt Internet anslutning.
>
>Azure-brandväggen kan konfigureras för att stödja Tvingad tunnel trafik. Mer information finns i [Tvingad tunnel trafik i Azure Firewall](../firewall/forced-tunneling.md).

>[!NOTE]
>Trafiken mellan direkt peerkopplade virtuella nätverk dirigeras direkt även om en UDR pekar på Azure Firewall som standardgateway. För att undernät till undernät-trafik ska kunna skickas till brandväggen i det här scenariot måste en UDR uttryckligen innehålla nätverksprefixet för målundernätverket på båda undernäten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-firewall-policy"></a>Skapa en brand Väggs princip

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Skriv **Firewall Manager** i Azure Portal Sök fältet och tryck på **RETUR**.
3. På sidan Azure Firewall Manager väljer du **Visa Azure Firewall-principer**.

   ![Brand Väggs princip](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Välj **Skapa Azure Firewall-princip**.
1. Välj din prenumeration och för resurs grupp väljer du **Skapa ny** och skapa en resurs grupp med namnet **VB-hybrid-test**.
2. För princip namnet skriver du **pol-Net01**.
3. För region väljer du **östra USA**.
4. Välj **Nästa: regler**.
5. Välj **Lägg till en regel samling**.
6. I **namn** skriver du **RCNet01**.
7. För **regel samlings typ** väljer du **nätverk**.
8. För **prioritet**, Skriv **100**.
9. I fältet **Åtgärd** väljer du **Tillåt**.
10. Under **regler** anger du **AllowWeb** som **namn**.
11. För **käll adresser** skriver du **192.168.1.0/24**.
12. I fältet **Protokoll** väljer du **TCP**.
13. För **mål portar** skriver du **80**.
14. För **måltyp** väljer du **IP-adress**.
15. För **mål** skriver du **10.6.0.0/16**.
16. Ange följande information på raden nästa regel:
 
    Namn: Skriv **AllowRDP**<br>
    Käll-IP-adress: Skriv **192.168.1.0/24**.<br>
    Protokoll väljer du **TCP**<br>
    Mål portar, typ **3389**<br>
    Typ av mål, Välj **IP-adress**<br>
    För mål skriver du **10.6.0.0/16**

1. Välj **Lägg till**.
2. Välj **Granska + skapa**.
3. Granska informationen och välj sedan **skapa**.

## <a name="create-the-firewall-hub-virtual-network"></a>Skapa brandväggens virtuella hubbnätverk

> [!NOTE]
> Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk** under **nätverk**.
4. I **namn** skriver du **VNet-Hub**.
5. Skriv **10.5.0.0/16** för **adress utrymme**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resurs grupp** väljer du **VB-hybrid-test**.
8. För **plats** väljer du **östra USA**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
10. För **adress intervall** skriver du **10.5.0.0/26**. 
11. Godkänn de andra standardinställningarna och välj sedan **skapa**.

## <a name="create-the-spoke-virtual-network"></a>Skapa det virtuella ekernätverket

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk** under **nätverk**.
4. För **namn** skriver du **VNet-eker**.
5. Skriv **10.6.0.0/16** för **adress utrymme**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resurs grupp** väljer du **VB-hybrid-test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn** anger du **SN-Workload**.
10. För **adress intervall** skriver du **10.6.0.0/24**.
11. Godkänn de andra standardinställningarna och välj sedan **skapa**.

## <a name="create-the-on-premises-virtual-network"></a>Skapa det lokala virtuella nätverket

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Välj **virtuellt nätverk** under **nätverk**.
4. För **namn** skriver du **VNet-OnPrem**.
5. I fältet **Adressutrymme** skriver du **192.168.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resurs grupp** väljer du **VB-hybrid-test**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **undernät**, för **namn** Skriv **SN-Corp**.
10. I fältet **Adressintervall** skriver du **192.168.1.0/24**.
11. Godkänn de andra standardinställningarna och välj sedan **skapa**.

När det virtuella nätverket har distribuerats skapar du ett andra undernät för gatewayen.

1. På sidan **VNet-OnPrem** väljer du **undernät**.
2. Välj **+ undernät**.
3. I **namn** skriver du **GatewaySubnet**.
4. För **adress intervall (CIDR-block)** skriver du **192.168.2.0/24**.
5. Välj **OK**.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

Detta är den offentliga IP-adress som används för den lokala gatewayen.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv den **offentliga IP-adressen** i Sök text rutan och tryck på **RETUR**.
3. Välj **offentlig IP-adress** och välj sedan **skapa**.
4. Som namn skriver du **VNet-OnPrem-GW-pip**.
5. För resurs gruppen skriver du **VB-hybrid-test**.
6. För **plats** väljer du Välj **USA, östra**.
7. Godkänn de andra standardinställningarna och välj sedan **skapa**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurera och distribuera brandväggen

När säkerhets principer är associerade med en hubb kallas det ett *nav virtuellt nätverk*.

Konvertera det virtuella nätverket i **VNet-hubben** till ett *virtuellt hubb nätverk* och skydda det med Azure-brandväggen.

1. Skriv **Firewall Manager** i Azure Portal Sök fältet och tryck på **RETUR**.
3. På sidan Azure Firewall Manager, under **Lägg till säkerhet i virtuella nätverk**, väljer du **Visa virtuella nätverk i hubben**.
4. Välj **konvertera virtuella nätverk**.
5. Välj **VNet-hubb** och välj sedan **Nästa: Azure-brandvägg**.
6. För **brand Väggs principen** väljer du **pol-Net01**.
7. Välj **Nästa "granska och bekräfta**
8. Granska informationen och välj sedan **Bekräfta**.


   Det tar några minuter att distribuera.
7. När distributionen är klar går du till resurs gruppen **VB-hybrid-test** och väljer brand väggen.
9. Observera den **privata IP** -adressen för brand väggen på **översikts** sidan. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-and-connect-the-vpn-gateways"></a>Skapa och ansluta VPN-gatewayer

De virtuella hubbnätverken och de lokala virtuella nätverken ansluts via VPN-gatewayer.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Skapa en VPN-gateway för det virtuella hubbnätverket

Skapa nu VPN-gatewayen för det virtuella hubbnätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. I sökrutan skriver du **virtuell nätverksgateway** och trycker på **RETUR**.
3. Välj **virtuell nätverksgateway** och välj **skapa**.
4. I **namn** skriver du **GW-hubb**.
5. För **region** väljer du **(US) USA, östra**.
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
5. För **region** väljer du **(US) USA, östra**.
6. För **Gateway-typ** väljer du **VPN**.
7. För **VPN-typ** väljer du **Route-based**.
8. För **SKU** väljer du **Basic**.
9. För **virtuellt nätverk** väljer du **VNet-OnPrem**.
10. För **offentlig IP-adress** väljer du **Använd befintlig* och väljer **VNet-OnPrem-GW-pip** som namn.
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
4. Anslutnings namnet, Skriv **OnPrem-to-Hub**.
5. Välj **VNet-till-VNet** som **Anslutnings typ**.
6. För den **andra virtuella Nätverksgatewayen** väljer du **GW-hubb**.
7. För **delad nyckel (PSK)** skriver du **AzureA1b2C3**.
8. Välj **OK**.


#### <a name="verify-the-connection"></a>Verifiera anslutningen

Efter ungefär fem minuter bör statusen för båda anslutningarna vara **ansluten**.

![Gatewayanslutningar](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peera de virtuella hubb- och ekernätverken

Peera nu de virtuella hubb- och ekernätverken.

1. Öppna resurs gruppen **VB-hybrid-test** och välj det virtuella nätverk för **VNet-hubb** .
2. Välj **peering** i den vänstra kolumnen.
3. Välj **Lägg till**.
4. I **namn** skriver du **HubtoSpoke**.
5. För det **virtuella nätverket** väljer du **VNet-eker**
6. Som namn på peer koppling från VNetSpoke till VNet-hubb, skriver du **SpoketoHub**.
7. Välj **Tillåt Gateway-överföring**.
8. Välj **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Konfigurera ytterligare inställningar för SpoketoHub-peering

Du måste aktivera den **Tillåt vidarebefordrade trafiken** på SpoketoHub-peering.

1. Öppna resurs gruppen **VB-hybrid-test** och välj det virtuella nätverket för **VNet-ekrar** .
2. Välj **peering** i den vänstra kolumnen.
3. Välj **SpoketoHub** -peering.
4. Under **Tillåt vidarebefordrad trafik från VNet-hubb till VNet-eker** väljer du **aktive rad**.
5. Välj **Spara**.

## <a name="create-the-routes"></a>Skapa vägarna

Därefter skapar du några vägar:

- En väg från hubbgateway-undernätet till ekerundernätet via brandväggens IP-adress
- En standardväg från ekerundernätet via brandväggens IP-adress

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **routningstabell** i text rutan och tryck på **RETUR**.
3. Välj **routningstabell**.
4. Välj **Skapa**.
5. Skriv **UDR-Hub-eker** som namn.
6. Välj mappen **VB-hybrid-test** för resurs gruppen.
8. För **plats** väljer du **(US) USA, östra)**.
9. Välj **Skapa**.
10. När du har skapat routningstabellen väljer du den för att öppna sidan väg tabell.
11. Välj **vägar** i den vänstra kolumnen.
12. Välj **Lägg till**.
13. Skriv **ToSpoke** som väg namn.
14. För adressprefixet skriver du **10.6.0.0/16**.
15. För nästa hopp typ väljer du **virtuell** installation.
16. För nästa hopp adress skriver du brand väggens privata IP-adress som du noterade tidigare.
17. Välj **OK**.

Koppla nu vägen till under nätet.

1. På sidan **UDR-Hub-eker-routes** väljer du **undernät**.
2. Välj **associera**.
4. Under **virtuellt nätverk** väljer du **VNet-Hub**.
5. Under **undernät** väljer du **GatewaySubnet**.
6. Välj **OK**.

Skapa nu standard vägen från eker-undernätet.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Skriv **routningstabell** i text rutan och tryck på **RETUR**.
3. Välj **routningstabell**.
5. Välj **Skapa**.
6. Skriv **UDR-GD** som namn.
7. Välj mappen **VB-hybrid-test** för resurs gruppen.
8. För **plats** väljer du **(US) USA, östra)**.
4. För **väg spridning för virtuell nätverksgateway** väljer du **inaktive rad**.
1. Välj **Skapa**.
2. När du har skapat routningstabellen väljer du den för att öppna sidan väg tabell.
3. Välj **vägar** i den vänstra kolumnen.
4. Välj **Lägg till**.
5. Skriv **ToHub** som väg namn.
6. För adressprefixet skriver du **0.0.0.0/0**.
7. För nästa hopp typ väljer du **virtuell** installation.
8. För nästa hopp adress skriver du brand väggens privata IP-adress som du noterade tidigare.
9. Välj **OK**.

Koppla nu vägen till under nätet.

1. På sidan **UDR-DG-routes** väljer du **undernät**.
2. Välj **associera**.
4. Under **virtuellt nätverk** väljer du **VNet-eker**.
5. Under **undernät** väljer du **SN-arbets belastning**.
6. Välj **OK**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu ekerarbetsbelastningen och de lokala virtuella datorerna, och placera dem i respektive undernät.

### <a name="create-the-workload-virtual-machine"></a>Skapa den virtuella arbetsbelastningsdatorn

Skapa en virtuell dator i det virtuella eker-nätverket som kör IIS, utan offentlig IP-adress.

1. På Start sidan Azure Portal väljer du **skapa en resurs**.
2. Under **populär** väljer du **Windows Server 2016 Data Center**.
3. Ange följande värden för den virtuella datorn:
    - **Resurs grupp** – Välj **VB-hybrid-test**.
    - **Namn på virtuell dator**: *VM-eker-01*.
    - **Region**  -  *(USA) USA, östra)*.
    - **Användar namn**: *azureuser*.
    - **Lösen ord**: Ange lösen ordet

4. Välj **Nästa:Diskar**.
5. Acceptera standardvärdena och välj **Nästa: nätverk**.
6. Välj **VNet-eker** för det virtuella nätverket och under nätet är **SN – arbets belastning**.
7. För **offentlig IP-adress** väljer du **ingen**.
8. För **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **http (80)** och **RDP (3389)**
9. Välj **Nästa: hantering**.
10. För **startdiagnostik** väljer du **av**.
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
    - **Region**  -  *(USA) USA, östra)*.
    - **Användar namn**: *azureuser*.
    - **Lösen ord**: Ange lösen ordet.

4. Välj **Nästa:Diskar**.
5. Acceptera standardvärdena och välj **Nästa: nätverk**.
6. Välj **VNet-OnPrem** för virtuellt nätverk och kontrol lera att under nätet är **SN-Corp**.
7. För **offentliga inkommande portar** väljer du **Tillåt valda portar** och väljer sedan **RDP (3389)**
8. Välj **Nästa: hantering**.
9. För **startdiagnostik** väljer du **av**.
10. Välj **Granska + skapa**, granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

## <a name="test-the-firewall"></a>testa brandväggen.

1. Lägg först märke till den privata IP-adressen för den virtuella datorn med ekrar 01 på VM-sidan med översikts sidan för VM-ekrar.

2. Från Azure-portalen ansluter du till den virtuella datorn **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Öppna en webbläsare på **VM-OnPrem** och gå till http:// \<VM-spoke-01 private IP\> .

   Du bör se webb sidan **VM-eker-01** : ![ VM-eker-01-webbsida](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Från den virtuella datorn **OnPrem** den virtuella datorn öppnar du ett fjärr skrivbord till **VM-ekrar-01** på den privata IP-adressen.

   Anslutningen bör lyckas och du bör kunna logga in.

Nu har du verifierat att brand Väggs reglerna fungerar:

<!---- You can ping the server on the spoke VNet.--->
- Du kan bläddra i webbservern på det virtuella ekernätverket.
- Du kan ansluta till servern på det virtuella ekernätverket med hjälp av RDP.

Ändra sedan brandväggsnätverksregelns insamlingsåtgärd till **Neka** för att verifiera att brandväggsreglerna fungerar som förväntat.

1. Öppna resurs gruppen **VB-hybrid-test** och välj **Net01** brand Väggs princip.
2. Under **Inställningar** väljer du **regler**.
3. Välj regel samlingen **RCNet01** under **nätverks regler**, Välj ellipserna (...) och välj **Redigera**.
4. För **regel samlings åtgärd** väljer du **neka**.
5. Välj **Spara**.

Stäng befintliga fjärr skrivbord och webbläsare på **VM-OnPrem** innan du testar de ändrade reglerna. När uppdateringen av regel samlingen har slutförts kör du testerna igen. De bör alla misslyckas den här gången.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **FW-Hybrid-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: skydda ditt virtuella WAN med Azure Firewall Manager](secure-cloud-network.md)