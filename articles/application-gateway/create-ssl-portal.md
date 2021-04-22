---
title: 'Självstudie: Konfigurera TLS-avslutning i portalen – Azure Application Gateway'
description: I den här självstudien får du lära dig hur du konfigurerar en programgateway och lägger till ett certifikat för TLS-avslutning med Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: victorh
ms.openlocfilehash: aa7123a1c4dea5fcede3e94250576f6677671176
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872253"
---
# <a name="tutorial-configure-an-application-gateway-with-tls-termination-using-the-azure-portal"></a>Självstudie: Konfigurera en programgateway med TLS-avslutning med hjälp av Azure Portal

Du kan använda Azure Portal för att konfigurera en [programgateway](overview.md) med ett certifikat för TLS-avslutning som använder virtuella datorer för backend-servrar.

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

I det här avsnittet använder du [New-SelfSignedCertificate för](/powershell/module/pki/new-selfsignedcertificate) att skapa ett självsignerat certifikat. Du överför certifikatet till Azure Portal när du skapar lyssnaren för programgatewayen.

Öppna ett fönster Windows PowerShell administratör på den lokala datorn. Kör följande kommando för att skapa certifikatet:

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

Använd [Export-PfxCertificate med](/powershell/module/pki/export-pfxcertificate) tumavtrycket som returnerades för att exportera en pfx-fil från certifikatet. Kontrollera att lösenordet är mellan 4 och 12 tecken långt:


```powershell
$pwd = ConvertTo-SecureString -String <your password> -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj **Application Gateway** i **listan** Aktuella.

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grundläggande anger** du dessa värden för följande inställningar för programgatewayen:

   - **Resursgrupp:** Välj **myResourceGroupAG** som resursgrupp. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Namn på programgateway:** *Ange myAppGateway* som namn på programgatewayen.

        ![Skapa ny programgateway: Grunder](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet skapar du ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    Under **Konfigurera virtuellt nätverk** skapar du ett nytt virtuellt nätverk genom att välja Skapa **nytt.** I fönstret **Skapa virtuellt** nätverk som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn:** Ange *myVNet* som namn på det virtuella nätverket.

    - **Undernätsnamn** (Application Gateway undernät): **Undernätsrutnätet** visar ett undernät med namnet *Standard*. Ändra namnet på det här undernätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Undernätsnamn** (undernät för serverserver): På  den andra raden i rutnätet Undernät anger du *myBackendSubnet* i kolumnen **Undernätsnamn.**

    - **Adressintervall** (undernät för serverserver): På den andra raden i **undernätsrutnätet** anger du ett adressintervall som inte överlappar adressintervallet *för myAGSubnet*. Om adressintervallet för *myAGSubnet* till exempel är 10.0.0.0/24 anger du *10.0.1.0/24* som adressintervall för *myBackendSubnet*.

    Välj **OK** för att stänga **fönstret Skapa virtuellt** nätverk och spara inställningarna för det virtuella nätverket.

    ![Skapa ny programgateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På **fliken Grundläggande inställningar** godkänner du standardvärdena för de andra inställningarna och väljer **sedan Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends (Frontends)

1. På fliken **Frontends kontrollerar** du att **IP-adresstypen för frontend** är inställd på **Offentlig**. <br>Du kan konfigurera IP-adressen för frontend till Offentlig eller Privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress för offentlig frontend.
   > [!NOTE]
   > För SKU Application Gateway v2 kan du bara välja **IP-konfiguration** för offentlig frontend. Ip-konfigurationen för privat frontend är för närvarande inte aktiverad för denna v2-SKU.

2. Välj **Lägg till ny** som Offentlig **IP-adress** och ange *myAGPublicIPAddress* som namn på offentlig IP-adress och välj **sedan OK.** 

   ![Skapa ny programgateway: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Backends (Backends).**

### <a name="backends-tab"></a>Fliken Backends (Backends)

Serverpoolen används för att dirigera begäranden till de backend-servrar som betjänar begäran. Serverpooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och server delar för flera klienter som Azure App Service. I det här exemplet skapar du en tom backend-pool med din programgateway och lägger sedan till mål för backend-poolen.

1. På fliken **Backends (Backends)** väljer du Add a backend pool (Lägg **till en backend-pool).**

2. I fönstret **Lägg till en backend-pool** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn:** Ange *myBackendPool* som namn på backend-poolen.
    - **Lägg till en backend-pool utan** mål: **Välj Ja** för att skapa en backend-pool utan mål. Du lägger till backend-mål när du har skapat programgatewayen.

3. I fönstret **Lägg till en serverpool väljer** du Lägg till för att spara serverpoolens konfiguration och  **återgår till fliken Serverinställningar.**

   ![Skapa ny programgateway: backends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. På fliken **Serverinställningar väljer** du **Nästa: Konfiguration.**

### <a name="configuration-tab"></a>Fliken Konfiguration

På fliken **Konfiguration** ansluter du den server- och serverpool som du skapade med hjälp av en routningsregel.

1. Välj **Lägg till en routningsregel** i kolumnen **Routningsregler.**

2. I fönstret **Lägg till en routningsregel** som öppnas anger *du myRoutingRule* som **regelnamn.**

3. En routningsregel kräver en lyssnare. På fliken **Lyssnare** i fönstret **Lägg till en routningsregel** anger du följande värden för lyssnaren:

    - **Lyssnarnamn:** Ange *myListener* som namn på lyssnaren.
    - **Ip-adress för** **frontend: Välj** Offentlig för att välja den offentliga IP-adress som du skapade för frontend.
    - **Protokoll:** Välj **HTTPS.**
    - **Port**: Kontrollera att 443 har angetts för porten.

   Under **HTTPS-inställningar:**

   - **Välj ett certifikat –** Välj **Ladda upp ett certifikat.**
   - **PFX-certifikatfil** – Bläddra till och välj filen c:\appgwcert.pfx som du skapade tidigare.
   - **Certifikatnamn** – *Skriv mycert1* som namn på certifikatet.
   - **Lösenord** – Ange det lösenord som du använde för att skapa certifikatet.
  
        Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Backend-mål för** att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. På fliken **Backend-mål** väljer **du myBackendPool** som **Backend-mål.**

5. För **HTTP-inställningen väljer** du Lägg **till ny för** att skapa en ny HTTP-inställning. HTTP-inställningen avgör routningsregelns beteende. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *myHTTPSetting* som **HTTP-inställningsnamn.** Acceptera standardvärdena för de andra inställningarna i fönstret  Lägg till en **HTTP-inställning** och välj sedan Lägg till för att återgå till **fönstret Lägg till en routningsregel.** 

   :::image type="content" source="./media/create-ssl-portal/application-gateway-create-httpsetting.png" alt-text="Skapa ny programgateway: HTTP-inställning":::

6. I fönstret **Lägg till en routningsregel** väljer **du Lägg till** för att spara routningsregeln och återgå till **fliken** Konfiguration.

   ![Skapa ny programgateway: routningsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska och skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan Skapa för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen.  Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till mål för backend

I det här exemplet använder du virtuella datorer som mål-server. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som Azure använder som backend-servrar för programgatewayen.

Det gör du genom att:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2,* som ska användas som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till backend-servrarna i serverpoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på Azure-portalen. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Datacenter** i **listan** Populära. Sidan **Skapa en virtuell dator** visas.

   Application Gateway kan dirigera trafik till alla typer av virtuella datorer som används i serverpoolen. I det här exemplet använder du ett Windows Server 2016 Datacenter.

1. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp:** Välj **myResourceGroupAG** som resursgruppsnamn.
    - **Namn på virtuell** dator: *Ange myVM* som namn på den virtuella datorn.
    - **Användarnamn:** Ange ett namn för administratörens användarnamn.
    - **Lösenord:** Ange ett lösenord för administratörskontot.
1. Acceptera de andra standardvärdena och välj **sedan Nästa: Diskar**.  
2. Acceptera **standardinställningarna för** fliken Diskar och välj **sedan Nästa: Nätverk**.
3. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardvärdena och välj **sedan Nästa: Hantering.**

   Application Gateway kan kommunicera med instanser utanför det virtuella nätverket som det finns i, men du måste se till att det finns en IP-anslutning.
1. På fliken **Hantering** ställer du in **Startdiagnostik** på **Inaktivera.** Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
2. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
3. Vänta tills distributionen är klar innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du endast IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

1. Öppna [Azure PowerShell](../cloud-shell/quickstart-powershell.md). Det gör du genom att först välja **Cloud Shell** i det övre navigeringsfältet på Azure-portalen och sedan välja **PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Ändra platsinställningen för din miljö och kör sedan följande kommando för att installera IIS på den virtuella datorn: 

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

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* som namn på den virtuella datorn och för **inställningen VMName** för cmdleten **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Lägga till backend-servrar i serverpoolen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **myBackendPool**.

4. Under **Måltyp** väljer **du Virtuell** dator i listrutan.

5. Under **Mål** väljer du nätverksgränssnittet under **myVM** i listrutan.

6. Upprepa för att lägga till nätverksgränssnittet **för myVM2**.

    ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen har slutförts innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **Alla resurser** och sedan **myAGPublicIPAddress.**

    ![Registrera programgatewayens offentliga IP-adress](./media/create-ssl-portal/application-gateway-ag-address.png)

2. I webbläsarens adressfält skriver du *https:// \<your application gateway ip address\>*.

   Om du vill acceptera säkerhetsvarningen om du har använt ett själv signerat certifikat väljer du **Information** (eller **Avancerat** i Chrome) och går sedan vidare till webbsidan:

    ![Säkerhetsvarning](./media/create-ssl-portal/application-gateway-secure.png)

    Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

    ![Testa basadressen i programgatewayen](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resursgruppen och välja Ta **bort resursgrupp.**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Application Gateway TLS-stöd](ssl-overview.md)
