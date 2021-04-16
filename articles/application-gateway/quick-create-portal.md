---
title: 'Snabbstart: Dirigera webbtrafik med hjälp av portalen'
titleSuffix: Azure Application Gateway
description: I den här snabbstarten lär du dig hur du använder Azure Portal för att skapa en Azure Application Gateway som dirigerar webbtrafik till virtuella datorer i en serverdelspool.
services: application-gateway
author: vhorne
ms.author: victorh
ms.date: 01/19/2021
ms.topic: quickstart
ms.service: application-gateway
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 92e966f5a9c4a21a6a55862ad582f0238802720f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538576"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal

I den här snabbstarten använder du Azure Portal för att skapa en programgateway. Sedan testar du den för att se till att den fungerar korrekt. 

Application Gateway dirigerar programwebbtrafik till specifika resurser i en backend-pool. Du tilldelar lyssnare till portar, skapar regler och lägger till resurser i en backend-pool. För enkelhetens skull använder den här artikeln en enkel konfiguration med en offentlig IP-adress i frontend, en grundläggande lyssnare som är värd för en enda plats på programgatewayen, en grundläggande regel för routning av förfrågningar och två virtuella datorer i serverpoolen.

Du kan också slutföra den här snabbstarten [med Azure PowerShell](quick-create-powershell.md) eller [Azure CLI.](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Du skapar programgatewayen med hjälp av flikarna på **sidan Skapa en programgateway.**

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.

2. Välj **Nätverk** och välj **Application Gateway** i **listan** Aktuella.

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **Grundläggande anger** du dessa värden för följande inställningar för programgatewayen:

   - **Resursgrupp:** Välj **myResourceGroupAG** som resursgrupp. Om den inte finns väljer du **Skapa ny** för att skapa den.
   - **Programgatewayens** namn: *Ange myAppGateway* som namn på programgatewayen.

     ![Skapa ny programgateway: Grunder](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. För att Azure ska kunna kommunicera mellan resurserna som du skapar krävs ett virtuellt nätverk. Du kan antingen skapa ett nytt virtuellt nätverk eller använda ett befintligt. I det här exemplet skapar du ett nytt virtuellt nätverk samtidigt som du skapar programgatewayen. Application Gateway-instanser skapas i separata undernät. Du skapar två undernät i det här exemplet: ett för programgatewayen och ett för backend-servrarna.

    > [!NOTE]
    > [Principer för tjänstslutpunkter för virtuellt](../virtual-network/virtual-network-service-endpoint-policies-overview.md) nätverk stöds för närvarande inte i Application Gateway undernät.

    Under **Konfigurera virtuellt nätverk** skapar du ett nytt virtuellt nätverk genom att välja Skapa **nytt.** I fönstret **Skapa virtuellt** nätverk som öppnas anger du följande värden för att skapa det virtuella nätverket och två undernät:

    - **Namn:** Ange *myVNet* som namn på det virtuella nätverket.

    - **Undernätsnamn** (Application Gateway undernät): **Undernätsrutnätet** visar ett undernät med namnet *Standard*. Ändra namnet på det här undernätet till *myAGSubnet*.<br>Undernätet för en programgateway kan endast innehålla programgatewayer. Inga andra resurser är tillåtna.

    - **Undernätsnamn** (undernät för serverserver): På  den andra raden i rutnätet Undernät anger du *myBackendSubnet* **i kolumnen Undernätsnamn.**

    - **Adressintervall** (undernät för serverserver): På den andra raden i **undernätsrutnätet** anger du ett adressintervall som inte överlappar adressintervallet *för myAGSubnet*. Om adressintervallet för *myAGSubnet* till exempel är 10.0.0.0/24 anger du *10.0.1.0/24* som adressintervall för *myBackendSubnet*.

    Välj **OK** för att stänga **fönstret Skapa virtuellt** nätverk och spara inställningarna för det virtuella nätverket.

     ![Skapa ny programgateway: virtuellt nätverk](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. På **fliken Grundläggande inställningar** godkänner du standardvärdena för de andra inställningarna och väljer **sedan Nästa: Frontends**.

### <a name="frontends-tab"></a>Fliken Frontends (Frontends)

1. På fliken **Frontends kontrollerar** du att **IP-adresstypen för frontend** är inställd på **Offentlig.** <br>Du kan konfigurera IP-adressen för frontend till Offentlig eller Privat enligt ditt användningsfall. I det här exemplet väljer du en IP-adress för offentlig frontend.
   > [!NOTE]
   > För den Application Gateway v2-SKU:n måste det finnas en **IP-konfiguration** för den offentliga serversidan. Du kan fortfarande ha både en offentlig och en privat IP-konfiguration på serversidan, men endast IP-konfiguration för privat server (endast ILB-läge) är inte aktiverat för v2-SKU:n. 

2. Välj **Lägg till ny** som Offentlig **IP-adress** och ange *myAGPublicIPAddress* som namn på offentlig IP-adress och välj **sedan OK.** 

     ![Skapa ny programgateway: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Välj **Nästa: Backends (Backends).**

### <a name="backends-tab"></a>Fliken Backends (Backends)

Serverpoolen används för att dirigera begäranden till de backend-servrar som betjänar begäran. Serverpooler kan bestå av nätverkskort, VM-skalningsuppsättningar, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domännamn (FQDN) och server delar för flera innehavare som Azure App Service. I det här exemplet skapar du en tom backend-pool med din programgateway och lägger sedan till mål för backend-poolen.

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
  
      Acceptera standardvärdena för de andra inställningarna på fliken **Lyssnare** och välj sedan fliken **Backend-mål för** att konfigurera resten av routningsregeln.

   ![Skapa ny programgateway: lyssnare](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. På fliken **Backend-mål** väljer **du myBackendPool** som **Backend-mål.**

5. För **HTTP-inställningen väljer du** Lägg **till ny för** att lägga till en ny HTTP-inställning. HTTP-inställningen avgör beteendet för routningsregeln. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *myHTTPSetting* som HTTP-inställningsnamn och *80* **för backend-porten**.  Acceptera standardvärdena för de andra inställningarna i fönstret  Lägg till en **HTTP-inställning** och välj sedan Lägg till för att återgå till **fönstret Lägg till en routningsregel.** 

     ![Skapa ny programgateway: HTTP-inställning](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. I fönstret **Lägg till en routningsregel** väljer **du Lägg till** för att spara routningsregeln och återgå till **fliken** Konfiguration.

     ![Skapa ny programgateway: routningsregel](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Välj **Nästa: Taggar** och sedan **Nästa: Granska + skapa**.

### <a name="review--create-tab"></a>Fliken Granska och skapa

Granska inställningarna på fliken **Granska +** skapa  och välj sedan Skapa för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

## <a name="add-backend-targets"></a>Lägga till mål för backend

I det här exemplet använder du virtuella datorer som mål-server. Du kan antingen använda befintliga virtuella datorer eller skapa nya. Du skapar två virtuella datorer som backend-servrar för programgatewayen.

Om du vill göra detta gör du följande:

1. Skapa två nya virtuella datorer, *myVM* och *myVM2,* som ska användas som backend-servrar.
2. Installera IIS på de virtuella datorerna för att kontrollera att programgatewayen har skapats.
3. Lägg till backend-servrarna i serverpoolen.

### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Fönstret **Nytt** visas.
2. Välj **Windows Server 2016 Datacenter** i **listan** Populära. Sidan **Skapa en virtuell dator** visas.<br>Application Gateway kan dirigera trafik till alla typer av virtuella datorer som används i serverpoolen. I det här exemplet använder du en virtuell dator med Windows Server 2016 Datacenter.
3. Ange dessa värden på fliken **Grundläggande inställningar** för följande inställningar för virtuella datorer:

    - **Resursgrupp:** Välj **myResourceGroupAG** som resursgruppnamn.
    - **Namn på virtuell** dator: *Ange myVM* som namn på den virtuella datorn.
    - **Region:** Välj samma region där du skapade programgatewayen.
    - **Användarnamn:** Ange ett namn för administratörens användarnamn.
    - **Lösenord:** Ange ett lösenord.
    - **Offentliga inkommande portar:** Ingen.
4. Acceptera de andra standardvärdena och välj **sedan Nästa: Diskar**.  
5. Acceptera **standardinställningarna för** fliken Diskar och välj **sedan Nästa: Nätverk.**
6. På fliken **Nätverk** kontrollerar du att **myVNet** har valts för **Virtuellt nätverk** och att **Undernät** är inställt på **myBackendSubnet**. Acceptera de andra standardvärdena och välj **sedan Nästa: Hantering.**<br>Application Gateway kan kommunicera med instanser utanför det virtuella nätverk som det finns i, men du måste se till att det finns EN IP-anslutning.
7. På fliken **Hantering** ställer du in **Startdiagnostik** på **Inaktivera.** Acceptera de övriga standardinställningarna och välj sedan **Granska + skapa**.
8. Gå igenom inställningarna på fliken **Granska + skapa** och åtgärda eventuella verifieringsfel och välj sedan **Skapa**.
9. Vänta på att skapandet av den virtuella datorn är klart innan du fortsätter.

### <a name="install-iis-for-testing"></a>Installera IIS för testning

I det här exemplet installerar du IIS på de virtuella datorerna för att verifiera att Azure har skapat programgatewayen.

1. Öppna Azure PowerShell.

   Välj **Cloud Shell** i det övre navigeringsfältet i Azure Portal och välj **sedan PowerShell** i listrutan. 

    ![Installera anpassat tillägg](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn. Ändra *parametern Plats* om det behövs: 

    ```azurepowershell
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Skapa en andra virtuell dator och installera IIS genom att följa stegen som du utförde tidigare. Använd *myVM2* som namn på den virtuella datorn och för **VMName-inställningen** för cmdleten **Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Lägga till backend-servrar i serverpoolen

1. På Azure Portal väljer du **Alla resurser eller** söker efter och väljer Alla *resurser.* Välj sedan **myAppGateway**.

2. Välj **Serverdelspooler** på den vänstra menyn.

3. Välj **myBackendPool.**

4. Under **Backend targets** **(Måltyp)** **väljer du Virtual machine** (Virtuell dator) i listrutan.

5. Under **Mål** väljer du de **virtuella datorerna myVM** och **myVM2** och deras associerade nätverksgränssnitt från listrutan.

   > [!div class="mx-imgBorder"]
   > ![Lägga till serverdelsservrar](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Välj **Spara**.

7. Vänta tills distributionen är klar innan du fortsätter till nästa steg.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Även om IIS inte krävs för att skapa programgatewayen så installerade du den i den här snabbstarten för att kontrollera om Azure har skapat programgatewayen. 

Använd IIS för att testa programgatewayen:

1. Hitta den offentliga IP-adressen för programgatewayen på **sidan** Översikt. ![ Registrera offentlig IP-adress för programgateway Eller så kan du välja Alla resurser, ange ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) *myAGPublicIPAddress* i sökrutan och sedan välja den i sökresultatet.  Azure visar den offentliga IP-adressen på sidan **Översikt**.
2. Kopiera den offentliga IP-adressen och klistra sedan in den i webbläsarens adressfält för att bläddra i DEN IP-adressen.
3. Kontrollera svaret. Ett giltigt svar verifierar att programgatewayen har skapats och kan ansluta med backend-datorn.

   ![Testa programgatewayen](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Uppdatera webbläsaren flera gånger så bör du se anslutningar till både myVM och myVM2.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med programgatewayen tar du bort resursgruppen. När du tar bort resursgruppen tar du även bort programgatewayen och alla relaterade resurser.

Så här tar du bort resursgruppen:

1. På Azure Portal väljer du **Resursgrupper eller** söker efter och väljer *Resursgrupper.*
2. På sidan **Resursgrupper** söker du efter och väljer **myResourceGroupAG**.
3. Välj **Ta bort resursgrupp** på sidan **Resursgrupp**.
4. Ange *myResourceGroupAG* under **SKRIV RESURSGRUPPENS NAMN och** välj sedan Ta **bort**

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Konfigurera en programgateway med TLS-avslutning med hjälp av Azure Portal](create-ssl-portal.md)
