---
title: 'Självstudie: URL Path-baserade regler för routning med Portal-Azure Application Gateway'
description: I den här självstudien får du lära dig hur du skapar URL Path-baserade routningsregler för en Programgateway och en skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708959"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Självstudie: skapa en Programgateway med sökvägar baserade routningsregler med hjälp av Azure Portal

Du kan använda Azure Portal för att konfigurera [URL-sökvägar baserade routningsregler](./url-route-overview.md) när du skapar en [Application Gateway](./overview.md). I den här självstudien skapar du backend-pooler med virtuella datorer. Du skapar sedan routningsregler som kontrollerar att webb trafiken kommer till rätt servrar i poolerna.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en programgateway
> * Skapa virtuella datorer för backend-servrar
> * Skapa backend-pooler med backend-servrar
> * Skapa en server dels lyssnare
> * Skapa en regel för väg-baserad routning

![URL-routningsexempel](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

I det här exemplet skapar du tre virtuella datorer som ska användas som backend-servrar för programgatewayen. Du installerar även IIS på de virtuella datorerna för att kontrol lera att Application Gateway fungerar som förväntat.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Data Center** i listan populär.
3. Ange följande värden för den virtuella datorn:

    - **Prenumeration** – välj din prenumeration.
    - **Resurs grupp**, Välj **Skapa ny** och skriv sedan *myResourceGroupAG*.
    - **Namn på virtuell dator**: *myVM1*
    - **Region**: *(USA) USA, östra*
    - **Användar** namn: Ange ett användar namn
    - **Lösen ord**: Ange ett lösen ord


4. Välj **Nästa:Diskar**.
5. Välj **Nästa: nätverk**
6. För **virtuellt nätverk** väljer du **Skapa nytt** och anger sedan de här värdena för det virtuella nätverket:

   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* för det första under nätets namn
   - *10.0.1.0/24* – för adress utrymmet för under nätet.
   - *myAGSubnet* – för det andra under nätets namn.
   - *10.0.0.0/24* – Undernätets adressutrymme.
7. Välj **OK**.

8. Se till att under **undernät**, **myBackendSubnet** har valts för under nätet och välj sedan **Nästa: hantering**.
9. Välj **inaktivera** om du vill inaktivera startdiagnostik.
10. Välj **Granska + skapa**, granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.
11. Skapa två fler virtuella datorer, *myVM2* och *myVM3* och placera dem i *MyVNet* virtuella nätverk och *myBackendSubnet* -undernätet.

### <a name="install-iis"></a>Installera IIS

1. Öppna det interaktiva gränssnittet och kontrollera att det är inställt på **PowerShell**.

    ![Installera anpassat tillägg](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Kör följande kommando för att installera IIS på den virtuella datorn: 

    ```azurepowershell
         $publicSettings = @{ "fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. Installera IIS på de andra virtuella datorerna med hjälp av de steg som du precis har avslutat. Använd *myVM2* och *myVM3* för VMName-värden i set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Skapa en programgateway

1. Välj **Skapa en resurs** på den vänstra menyn på Azure-portalen. Fönstret **Nytt** visas.

2. Välj **nätverk** och välj sedan **Application Gateway** i listan **aktuella** .

### <a name="basics-tab"></a>Fliken Grundläggande

1. På fliken **grundläggande** anger du dessa värden för följande Programgateway-inställningar:

   - **Prenumeration**: Välj din prenumeration.
   - **Resurs grupp**: Välj **myResourceGroupAG** för resurs gruppen.
   - **Namn på Application Gateway**: Skriv *myAppGateway* som namn på Application Gateway.
   - **Region** – Välj **USA, östra**.

        ![Skapa ny Application Gateway: grunderna](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Under **Konfigurera virtuellt nätverk** väljer du **myVNet** som namn på det virtuella nätverket.
3. Välj **myAGSubnet** för under nätet.
3. Acceptera standardvärdena för de andra inställningarna och välj sedan **Nästa: frontend**-klienter.

### <a name="frontends-tab"></a>Fliken frontend

1. På fliken **klient** delar kontrollerar du att **IP-adress typen för klient delen** är **offentlig**.

   > [!NOTE]
   > För Application Gateway v2 SKU: n kan du bara välja **offentlig** IP-konfiguration för klient delen. Den privata klient delens IP-konfiguration är för närvarande inte aktive rad för denna v2-SKU

2. Välj **Lägg till ny** för den **offentliga IP-adressen** och ange *myAGPublicIPAddress* för den offentliga IP-adressen och välj sedan **OK**. 
3. Välj **Nästa:** Server delar.

### <a name="backends-tab"></a>Fliken Server delar

Backend-poolen används för att dirigera begär anden till backend-servrar som hanterar begäran. Backend-pooler kan bestå av nätverkskort, skalnings uppsättningar för virtuella datorer, offentliga IP-adresser, interna IP-adresser, fullständigt kvalificerade domän namn (FQDN) och backend-ändar för flera klienter som Azure App Service.

1. **På fliken Server** delar väljer du **Lägg till en backend-pool**.

2. I fönstret **Lägg till en server dels grupp** som öppnas anger du följande värden för att skapa en tom backend-pool:

    - **Namn**: ange *myBackendPool* som namn på backend-poolen.
3. Under **måltyp** väljer du **virtuell dator** i den nedrullningsbara listan.

5. Under **mål** väljer du nätverks gränssnittet för **myVM1**.
6. Välj **Lägg till**.
7. Upprepa om du vill lägga till en *avbildnings* Server del med *myVM2* som mål och en *video* -backend-pool med *myVM3* som mål.
8. Välj **Lägg till** för att spara konfigurationen av backend-poolen och gå tillbaka till fliken back **ändar** .

4. **På fliken Server** delar väljer du **Nästa: konfiguration**.

### <a name="configuration-tab"></a>Fliken konfiguration

På fliken **konfiguration** ansluter du klient dels-och backend-poolen som du skapade med en regel för routning.

1. Välj **Lägg till en regel för routning** i kolumnen **routningsregler** .

2. I fönstret **Lägg till regel för routning** som öppnas anger du *myRoutingRule* som **regel namn**.

3. En regel för routning kräver en lyssnare. Skriv följande värden för lyssnaren på fliken **lyssnare** i fönstret **Lägg till regel för vidarebefordran** :

    - **Namn på lyssnare**: *Ange en lyssnare* för namnet på lyssnaren.
    - **IP-adress för klient** del: Välj **offentlig** för att välja den offentliga IP-adress som du skapade för klient delen.
    - **Port**: typ *8080*
  
        Acceptera standardvärdena för de andra inställningarna på fliken **lyssnare** och välj sedan fliken **backend-mål** för att konfigurera resten av regeln.

4. På fliken **backend-mål** väljer du **MyBackendPool** för **Server dels målet**.

5. För **http-inställningen** väljer du **Lägg till ny** för att skapa en ny http-inställning. HTTP-inställningen avgör hur routningsregler fungerar. 

6. I fönstret **Lägg till en HTTP-inställning** som öppnas anger du *myHTTPSetting* som **namn på http-inställningen**. Acceptera standardvärdena för de andra inställningarna i fönstret **Lägg till en HTTP-inställning** och välj sedan **Lägg till** för att återgå till fönstret **Lägg till regel för routning** .
7. Under **Path-baserad routning** väljer **du Lägg till flera mål för att skapa en Sök vägs baserad regel**.
8. För **sökväg** skriver du */images/* \* .
9. För **mål namn** skriver du *bilder*.
10. För **http-inställning** väljer du **myHTTPSetting**
11. För **Server dels mål** väljer du **bilder**.
12. Välj **Lägg till** för att spara Sök vägs regeln och gå tillbaka till fliken **Lägg till en regel för routning** .
13. Upprepa om du vill lägga till en annan regel för video.
14. Välj **Lägg** till för att lägga till regeln för Routning och återgå till fliken **konfiguration** .
15. Välj **Nästa: Taggar** och **Nästa: granska + skapa**.

> [!NOTE]
> Du behöver inte lägga till en anpassad */* * Sök vägs regel för att hantera standard fall. Detta hanteras automatiskt av standard-backend-poolen.

### <a name="review--create-tab"></a>Granska + fliken Skapa

Granska inställningarna på fliken **Granska + skapa** och välj sedan **skapa** för att skapa det virtuella nätverket, den offentliga IP-adressen och programgatewayen. Det kan ta flera minuter för Azure att skapa programgatewayen. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.


## <a name="test-the-application-gateway"></a>Testa programgatewayen

1. Välj **Alla resurser** och välj sedan **myAppGateway**.

    ![Registrera programgatewayens offentliga IP-adress](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http: \/ /52.188.72.175:8080.

    ![Testa basadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   Lyssnaren på Port 8080 dirigerar den här begäran till standard-backend-poolen.

3. Ändra URL: en till *http:// &lt; IP-address &gt; : 8080/images/test.htm*, ersätta &lt; IP-adress &gt; med din IP-adress och du bör se något som liknar följande exempel:

    ![Testa bildadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   Lyssnaren på Port 8080 dirigerar den här begäran till *avbildningens* backend-pool.

4. Ändra URL: en till *http:// &lt; IP-address &gt; : 8080/video/test.htm*, ersätta &lt; IP-adress &gt; med din IP-adress och du bör se något som liknar följande exempel:

    ![Testa videoadressen i programgatewayen](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   Lyssnaren på Port 8080 dirigerar denna begäran till *video* -backend-poolen.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen och alla relaterade resurser när de inte längre behövs. Det gör du genom att markera resurs gruppen och välja **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera TLS från slut punkt till slut punkt på Azure Application Gateway](./ssl-overview.md)