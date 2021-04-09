---
title: 'Självstudie: är värd för flera webbplatser med hjälp av Azure Portal'
titleSuffix: Azure Application Gateway
description: I den här självstudien får du lära dig hur du skapar en Programgateway som är värd för flera webbplatser med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: cfbd5301bc2b24c4d5614e5f88c6ae18d4affc66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721638"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Självstudie: skapa och konfigurera en Application Gateway som värd för flera webbplatser med hjälp av Azure Portal

Du kan använda Azure Portal för att [Konfigurera värd för flera webbplatser](multiple-site-overview.md) när du skapar en [Programgateway](overview.md). I den här självstudien definierar du Server dels adress grupper med hjälp av virtuella datorer. Du konfigurerar sedan lyssnare och regler baserat på två domäner för att kontrol lera att webb trafiken kommer till rätt servrar i poolerna. I den här självstudien används exempel på *www.contoso.com* och *www.fabrikam.com*.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa backend-pooler med backend-servrar
> * Skapa lyssnare för serverdelen
> * Skapa routningsregler
> * Redigera hosts-filen för namn matchning

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Application Gateway för flera platser":::

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **nätverk** och välj sedan **Application Gateway** i listan **aktuella** .

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **grundläggande** anger du dessa värden för följande Programgateway-inställningar:

   - **Resurs grupp**: Välj **myResourceGroupAG** för resurs gruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på Application Gateway**: ange *myAppGateway* som namn på Application Gateway.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Skapa Application Gateway":::

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet ska du skapa ett nytt virtuellt nätverk på samma tidpunkt som du skapar programgatewayen. Application Gateway instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Under **Konfigurera virtuellt nätverk** väljer du **Skapa nytt** för att skapa ett nytt virtuellt nätverk. I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn**: ange *myVNet* som namn på det virtuella nätverket.

    - **Under näts namn** (Application Gateway undernät): **under nätets** rutnät visas ett undernät med namnet *default*. Ändra namnet på det här under nätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Under näts namn** (backend-serverns undernät): i den andra raden i rutnätet för **undernät** anger du *myBackendSubnet* i kolumnen **under nät namn** .

    - **Adress intervall** (backend-serverns undernät): Ange ett adress intervall som inte överlappar adress intervallet för *myAGSubnet* på den andra raden i **under nätets** rutnät. Om adress intervallet för *myAGSubnet* till exempel är 10.0.0.0/24, anger du *10.0.1.0/24* för adress intervallet för *myBackendSubnet*.

    Välj **OK** för att stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="Skapa VNet":::
    
3. På fliken **grundläggande** accepterar du standardvärdena för de andra inställningarna och väljer sedan **Nästa: frontend**-klienter.

### <a name="frontends-tab"></a>Fliken frontend

1. På fliken **klient** delar kontrollerar du att **IP-adress typen för klient delen** är **offentlig**. <br>Du kan konfigurera klient delens IP-adress så att den är offentlig eller privat per användnings fall. I det här exemplet väljer du en offentlig IP-adress för klient delen.
   > [!NOTE]
   > För Application Gateway v2 SKU: n kan du bara välja **offentlig** IP-konfiguration för klient delen. Den privata klient delens IP-konfiguration är för närvarande inte aktive rad för denna v2-SKU

2. Välj **Lägg till ny** för den **offentliga IP-adressen** och ange *myAGPublicIPAddress* för den offentliga IP-adressen och välj sedan **OK**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Skapa ett annat VNet":::

3. Välj **Nästa:** Server delar.

### <a name="backends-tab"></a>Fliken Server delar

Backend-poolen används för att dirigera begär anden till backend-servrar som hanterar begäran. Backend-pooler kan vara nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med din Application Gateway och sedan lägga till Server dels mål i backend-poolen.

1. **På fliken Server** delar väljer du **Lägg till en backend-pool**.

2. I fönstret **Lägg till en server dels grupp** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: ange *contosoPool* som namn på backend-poolen.
    - **Lägg till backend-pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du kommer att lägga till Server dels mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en server dels grupp** väljer du **Lägg till** för att spara konfigurationen av backend-poolen och återgår till fliken back **ändar** .
4. Lägg nu till en annan backend-pool med namnet *fabrikamPool* på samma sätt som du lade till i den tidigare poolen.
1. Välj **Lägg till**.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Skapa server delar":::

4. **På fliken Server** delar väljer du **Nästa: konfiguration**.

### <a name="configuration-tab"></a>Fliken konfiguration

På fliken **konfiguration** ansluter du klient dels-och backend-pooler som du skapade med en regel för routning.

1. Välj **Lägg till en regel för routning** i kolumnen **routningsregler** .

2. I fönstret **Lägg till regel för routning** som öppnas anger du *contosoRule* som **regel namn**.

3. En regel för routning kräver en lyssnare. Ange följande värden för lyssnaren på fliken **lyssnare** i fönstret **Lägg till regel för vidarebefordran** :

    - **Regel namn**: *contosoRule*.
    - **Lyssnar namn**: *contosoListener*.
    - **IP-adress för klient** del: Välj **offentlig** för att välja den offentliga IP-adress som du skapade för klient delen.

   Under **ytterligare inställningar**:
   - **Typ av lyssnare**: flera platser
   - **Värdnamn**: **www.contoso.com**

   Acceptera standardvärdena för de andra inställningarna på fliken **lyssnare** och välj sedan fliken **backend-mål** för att konfigurera resten av regeln.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Skapa regel för routning":::

4. På fliken **backend-mål** väljer du **ContosoPool** för **Server dels målet**.

5. För **http-inställningen** väljer du **Lägg till ny** för att skapa en ny http-inställning. HTTP-inställningen avgör hur routningsregler fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *contosoHTTPSetting* som **namn på http-inställningen**. Acceptera standardvärdena för de andra inställningarna i fönstret **Lägg till en HTTP-inställning** och välj sedan **Lägg till** för att återgå till fönstret **Lägg till regel för routning** . 

6. I fönstret **Lägg till regel för routning** väljer du **Lägg till** för att spara regeln för Routning och återgå till fliken **konfiguration** .
7. Välj **Lägg till en regel för routning** och Lägg till en liknande regel, lyssnare, Server dels mål och http-inställning för fabrikam.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam-regel":::

7. Välj **Nästa: Taggar** och **Nästa: granska + skapa**.

### <a name="review--create-tab"></a>Granska + fliken Skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen.

Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägg till Server dels mål

I det här exemplet ska du använda virtuella datorer som mål Server del. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som backend-servrar för programgatewayen.

Om du vill lägga till Server dels mål gör du följande:

1. Skapa två nya virtuella datorer, *contosoVM* och *fabrikamVM*, som ska användas som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrol lera att Application Gateway har skapats.
3. Lägg till backend-servrarna i backend-poolerna.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Data Center** i listan **populär** . Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till vilken typ av virtuell dator som helst som används i dess backend-pool. I det här exemplet använder du ett Windows Server 2016 Data Center.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Prenumeration**: Välj din prenumeration.
    - **Resurs grupp**: Välj **myResourceGroupAG** som resurs grupps namn.
    - **Namn på virtuell dator**: ange *contosoVM* som namn på den virtuella datorn.
    - **Region**: Välj samma region som du använde tidigare.
    - **Användar** namn: Ange ett namn för administratörs användar namnet.
    - **Lösen ord**: Ange ett lösen ord för administratören.
1. Godkänn de andra standardinställningarna och välj sedan **Nästa: diskar**.  
2. Godkänn standardvärdena på fliken **diskar** och välj sedan **Nästa: nätverk**.
3. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Godkänn de andra standardinställningarna och välj sedan **Nästa: hantering**.<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverk som det finns i, men du måste se till att det finns en IP-anslutning.
4. På fliken **hantering** ställer du in **startdiagnostik** som ska **inaktive ras**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
5. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
6. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du bara IIS på de virtuella datorerna för att kontrol lera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn, och ersätt resurs grupps regionen för <plats \> : 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Skapa en andra virtuell dator och installera IIS med hjälp av de steg som du har slutfört. Använd *fabrikamVM* för det virtuella dator namnet och **VMName** -inställningen för cmdleten **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pools"></a>Lägg till backend-servrar till backend-pooler

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **contosoPool**.

4. Under **måltyp** väljer du **virtuell dator** i den nedrullningsbara listan.

5. Under **mål** väljer du den virtuella **contosoVM** -datorns nätverks gränssnitt i list rutan.

    ![Lägga till serverdelsservrar](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Välj **Spara**.
7. Upprepa för att lägga till *fabrikamVM* och gränssnittet i *fabrikamPool*.

Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="edit-your-hosts-file-for-name-resolution"></a>Redigera hosts-filen för namn matchning

När Application Gateway har skapats med dess offentliga IP-adress kan du hämta IP-adressen och använda den för att redigera värd filen för att lösa `www.contoso.com` och `www.fabrikam.com` . I en produktions miljö kan du skapa en `CNAME` i DNS för namn matchning.

1. Klicka på **alla resurser** och sedan på **myAGPublicIPAddress**.

    ![Registrera DNS-adress för Application Gateway](./media/create-multiple-sites-portal/public-ip.png)

2. Kopiera IP-adressen och Använd den som värdet för nya poster i `hosts` filen.
1. Öppna en administrativ kommando tolk på den lokala datorn och gå till `c:\Windows\System32\drivers\etc` .
1. Öppna `hosts` filen och Lägg till följande poster, där `x.x.x.x` är programgatewayens offentliga IP-adress:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Spara filen.
1. Kör följande kommandon för att läsa in och visa ändringarna i värd filen:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Skriv ett domän namn i adress fältet i webbläsaren. Till exempel `http://www.contoso.com`.

    ![Testa contoso-webbplatsen i programgatewayen](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Ändra adressen till den andra domänen så ser du något som liknar följande exempel:

    ![Testa fabrikam-webbplatsen i programgatewayen](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen kan du ta bort resursgruppen. När du tar bort resurs gruppen tar du även bort programgatewayen och dess relaterade resurser.

Så här tar du bort resursgruppen:

1. Välj **Resursgrupper** på den vänstra menyn på Azure-portalen.
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* för **Skriv resurs gruppens namn** och välj sedan **ta bort**.

Så här återställer du värd filen:
1. Ta bort- `www.contoso.com` och- `www.fabrikam.com` raderna från hosts-filen och kör `ipconfig/registerdns` och `ipconfig/flushdns` från kommando tolken.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om vad du kan göra med Azure Application Gateway](./overview.md)