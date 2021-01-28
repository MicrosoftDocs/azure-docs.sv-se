---
title: 'Självstudie: Konfigurera TLS-avslutning i portalen – Azure Application Gateway'
description: I den här självstudien får du lära dig hur du konfigurerar en Programgateway och lägger till ett certifikat för TLS-avslutning med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: c976ea236ae1d37cc0a543b10a9de55609035632
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986759"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Självstudie: Konfigurera en Programgateway med TLS-avslutning med hjälp av Azure Portal

Du kan använda Azure Portal för att konfigurera en [Programgateway](overview.md) med ett certifikat för TLS-avslutning som använder virtuella datorer för backend-servrar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett självsignerat certifikat
> * Skapa en programgateway med certifikatet
> * Skapa de virtuella datorer som används som backend-servrar
> * Testa programgatewayen

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

I det här avsnittet använder du [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) för att skapa ett självsignerat certifikat. Du laddar upp certifikatet till Azure Portal när du skapar lyssnaren för programgatewayen.

Öppna ett Windows PowerShell-fönster som administratör på den lokala datorn. Kör följande kommando för att skapa certifikatet:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något som liknar det här svaret:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd [export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades för att exportera en PFX-fil från certifikatet. Se till att ditt lösen ord är 4-12 tecken långt:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **nätverk** och välj sedan **Application Gateway** i listan **aktuella** .

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **grundläggande** anger du dessa värden för följande Programgateway-inställningar:

   - **Resurs grupp**: Välj **myResourceGroupAG** för resurs gruppen. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på Application Gateway**: ange *myAppGateway* som namn på Application Gateway.

        ![Skapa ny Application Gateway: grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet ska du skapa ett nytt virtuellt nätverk på samma tidpunkt som du skapar programgatewayen. Application Gateway instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Under **Konfigurera virtuellt nätverk** skapar du ett nytt virtuellt nätverk genom att välja **Skapa nytt**. I fönstret **Skapa virtuellt nätverk** som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn**: ange *myVNet* som namn på det virtuella nätverket.

    - **Under näts namn** (Application Gateway undernät): **under nätets** rutnät visas ett undernät med namnet *default*. Ändra namnet på det här under nätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Under näts namn** (backend-serverns undernät): i den andra raden i rutnätet för **undernät** anger du *myBackendSubnet* i kolumnen **under nät namn** .

    - **Adress intervall** (backend-serverns undernät): Ange ett adress intervall som inte överlappar adress intervallet för *myAGSubnet* på den andra raden i **under nätets** rutnät. Om adress intervallet för *myAGSubnet* till exempel är 10.0.0.0/24, anger du *10.0.1.0/24* för adress intervallet för *myBackendSubnet*.

    Välj **OK** för att stänga fönstret **Skapa virtuellt nätverk** och spara inställningarna för det virtuella nätverket.

    ![Skapa ny Application Gateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På fliken **grundläggande** accepterar du standardvärdena för de andra inställningarna och väljer sedan **Nästa: frontend**-klienter.

### <a name="frontends-tab"></a>Fliken frontend

1. På fliken **klient** delar kontrollerar du att **IP-adress typen för klient delen** är **offentlig**. <br>Du kan konfigurera klient delens IP-adress så att den är offentlig eller privat per användnings fall. I det här exemplet väljer du en offentlig IP-adress för klient delen.
   > [!NOTE]
   > För Application Gateway v2 SKU: n kan du bara välja **offentlig** IP-konfiguration för klient delen. Den privata klient delens IP-konfiguration är för närvarande inte aktive rad för denna v2-SKU

2. Välj **Lägg till ny** för den **offentliga IP-adressen** och ange *myAGPublicIPAddress* för den offentliga IP-adressen och välj sedan **OK**. 

   ![Skapa ny Application Gateway: klient delar](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa:** Server delar.

### <a name="backends-tab"></a>Fliken Server delar

Backend-poolen används för att dirigera begär anden till backend-servrar som hanterar begäran. Backend-pooler kan bestå av nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service. I det här exemplet ska du skapa en tom backend-pool med din Application Gateway och sedan lägga till Server dels mål i backend-poolen.

1. **På fliken Server** delar väljer du **Lägg till en backend-pool**.

2. I fönstret **Lägg till en server dels grupp** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: ange *myBackendPool* som namn på backend-poolen.
    - **Lägg till backend-pool utan mål**: Välj **Ja** om du vill skapa en backend-pool utan mål. Du kommer att lägga till Server dels mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en server dels grupp** väljer du **Lägg till** för att spara konfigurationen av backend-poolen och återgår till fliken back **ändar** .

   ![Skapa ny Application Gateway: Server delar](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. **På fliken Server** delar väljer du **Nästa: konfiguration**.

### <a name="configuration-tab"></a>Fliken konfiguration

På fliken **konfiguration** ansluter du klient dels-och backend-poolen som du skapade med en regel för routning.

1. Välj **Lägg till en regel för routning** i kolumnen **routningsregler** .

2. I fönstret **Lägg till regel för routning** som öppnas anger du *myRoutingRule* som **regel namn**.

3. En regel för routning kräver en lyssnare. Ange följande värden för lyssnaren på fliken **lyssnare** i fönstret **Lägg till regel för vidarebefordran** :

    - **Namn på lyssnare**: *Ange en lyssnare* för namnet på lyssnaren.
    - **IP-adress för klient** del: Välj **offentlig** för att välja den offentliga IP-adress som du skapade för klient delen.
    - **Protokoll**: Välj **https**.
    - **Port**: kontrol lera att 443 har angetts för porten.

   Under **https-inställningar**:

   - **Välj ett certifikat** – Välj **Ladda upp ett certifikat**.
   - **PFX-certifikat fil** – Bläddra till och välj den c:\appgwcert.pfx-fil som du skapade tidigare.
   - **Certifikat namn** – Skriv *mycert1* som namn på certifikatet.
   - **Password** – ange lösen ordet som du använde för att skapa certifikatet.
  
        Acceptera standardvärdena för de andra inställningarna på fliken **lyssnare** och välj sedan fliken **backend-mål** för att konfigurera resten av regeln.

   ![Skapa ny Application Gateway: lyssnare](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. På fliken **backend-mål** väljer du **MyBackendPool** för **Server dels målet**.

5. För **http-inställningen** väljer du **Lägg till ny** för att skapa en ny http-inställning. HTTP-inställningen avgör hur routningsregler fungerar. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *myHTTPSetting* som **namn på http-inställningen**. Acceptera standardvärdena för de andra inställningarna i fönstret **Lägg till en HTTP-inställning** och välj sedan **Lägg till** för att återgå till fönstret **Lägg till regel för routning** . 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Skapa ny Application Gateway: HTTP-inställning":::

6. I fönstret **Lägg till regel för routning** väljer du **Lägg till** för att spara regeln för Routning och återgå till fliken **konfiguration** .

   ![Skapa ny Application Gateway: regel för routning](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och **Nästa: granska + skapa**.

### <a name="review--create-tab"></a>Granska + fliken Skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägg till Server dels mål

I det här exemplet ska du använda virtuella datorer som mål Server del. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som backend-servrar för programgatewayen.

Det gör du genom att:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2*, som ska användas som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrol lera att Application Gateway har skapats.
3. Lägg till backend-servrarna i backend-poolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Data Center** i listan **populär** . Sidan **Skapa en virtuell dator** visas.

   Application Gateway kan dirigera trafik till vilken typ av virtuell dator som helst som används i dess backend-pool. I det här exemplet använder du ett Windows Server 2016 Data Center.

1. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resurs grupp**: Välj **myResourceGroupAG** som resurs grupps namn.
    - **Namn på virtuell dator**: ange *myVM* som namn på den virtuella datorn.
    - **Användar** namn: Ange ett namn för administratörs användar namnet.
    - **Lösen ord**: Ange ett lösen ord för administratörs kontot.
1. Godkänn de andra standardinställningarna och välj sedan **Nästa: diskar**.  
2. Godkänn standardvärdena på fliken **diskar** och välj sedan **Nästa: nätverk**.
3. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Godkänn de andra standardinställningarna och välj sedan **Nästa: hantering**.

   Application Gateway kan kommunicera med instanser utanför det virtuella nätverk som det finns i, men du måste se till att det finns en IP-anslutning.
1. På fliken **hantering** ställer du in **startdiagnostik** som ska **inaktive ras**. Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
2. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
3. Vänta tills distributionen är klar innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du bara IIS på de virtuella datorerna för att kontrol lera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Ändra plats inställningen för din miljö och kör sedan följande kommando för att installera IIS på den virtuella datorn: 

   ```azurepowershell-interactive
          Set-AzVMExtension `
            -ResourceGroupName myResourceGroupAG `
            -ExtensionName IIS `
            -VMName myVM `
            -Publisher Microsoft.Compute `
            -ExtensionType CustomScriptExtension `
            -TypeHandlerVersion 1.4 `
            -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
            -Location <location>
   ```

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* för det virtuella dator namnet och **VMName** -inställningen för cmdleten **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Lägg till backend-servrar i backend-poolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **myBackendPool**.

4. Under **måltyp** väljer du **virtuell dator** i den nedrullningsbara listan.

5. Under **mål** väljer du nätverks gränssnittet under **myVM** i list rutan.

6. Upprepa om du vill lägga till nätverks gränssnittet för **myVM2**.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **alla resurser** och välj sedan **myAGPublicIPAddress**.

    ![Registrera programgatewayens offentliga IP-adress](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Skriv *https:// \<your application gateway ip address\>* i adress fältet i webbläsaren.

   Om du vill acceptera säkerhets varningen om du använde ett självsignerat certifikat väljer du **information** (eller **Avancerat** på krom) och går sedan till webb sidan:

    ![Säkerhetsvarning](./media/create-ssl-portal/application-gateway-secure.png)

    Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

    ![Testa basadressen i programgatewayen](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resurs gruppen och välja **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om stöd för Application Gateway TLS](ssl-overview.md)