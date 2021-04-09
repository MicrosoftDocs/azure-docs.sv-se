---
title: Omdirigering av HTTP till HTTPS i portalen – Azure Application Gateway
description: Lär dig hur du skapar en Programgateway med Omdirigerad trafik från HTTP till HTTPS med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397288"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en Application Gateway med HTTP till HTTPS-omdirigering med hjälp av Azure Portal

Du kan använda Azure Portal för att skapa en [Programgateway](overview.md) med ett certifikat för TLS-avslutning. En regel för routning används för att omdirigera HTTP-trafik till HTTPS-porten i din Programgateway. I det här exemplet skapar du också en [skalnings uppsättning för virtuella datorer](../virtual-machine-scale-sets/overview.md) för den Programgateway som innehåller två instanser av virtuella datorer.

I den här artikeln kan du se hur du:

* Skapa ett självsignerat certifikat
* Konfigurera ett nätverk
* Skapa en programgateway med certifikatet
* Lägg till en regel för avlyssning och omdirigering
* Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I den här självstudien krävs Azure PowerShell module version 1.0.0 eller senare för att skapa ett certifikat och installera IIS. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du vill köra kommandona i den här självstudien måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

För produktions användning bör du importera ett giltigt certifikat signerat av en betrodd Provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Du bör se något som liknar det här resultatet:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Använd tumavtrycket till att skapa pfx-filen:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Det krävs ett virtuellt nätverk för kommunikation mellan de resurser som du skapar. I det här exemplet skapas två undernät: ett för programgatewayen och ett annat för serverdelen. Du kan skapa ett virtuellt nätverk samtidigt som du skapar programgatewayen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
3. Välj **Nätverk** och sedan **Application Gateway** i listan Aktuella.
4. Ange följande värden för programgatewayen:

   - *myAppGateway* – Namnet på programgatewayen.
   - *myResourceGroupAG* – Den nya resursgruppen.

     ![Skapa en ny programgateway](./media/create-url-route-portal/application-gateway-create.png)

5. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
6. Klicka på **Välj ett virtuellt nätverk**, klicka på **Skapa nytt** och ange sedan följande värden för det virtuella nätverket:

   - *myVnet* – Det virtuella nätverkets namn.
   - *10.0.0.0/16* – Det virtuella nätverkets adressutrymme.
   - *myBackendSubnet* – Undernätsnamnet.
   - *10.0.0.0/24* – Undernätets adressutrymme.

     ![Skapa det virtuella nätverket](./media/create-url-route-portal/application-gateway-vnet.png)

7. Klicka på **OK** för att skapa det virtuella nätverket och undernätet.
8. Under **IP-konfiguration för klient** del kontrollerar du att **IP-adress typen** är **offentlig** och att **Skapa ny** är markerat. Ange *myAGPublicIPAddress* som namn. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
9. Under **Listener Configuration** väljer du **https**. Välj sedan **Välj en fil** och navigera till *c:\appgwcert.pfx* -filen och välj **Öppna**.
10. Skriv *appgwcert* som certifikat namn och *Azure123456!* som lösenord.
11. Låt brand väggen för webbaserade program vara inaktive rad och välj sedan **OK**.
12. Granska inställningarna på sidan Sammanfattning och välj sedan **OK** för att skapa nätverks resurserna och programgatewayen. Det kan ta flera minuter innan Application Gateway skapas, vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **alla resurser** i den vänstra menyn och välj sedan **myVNet** i listan resurser.
2. Välj **undernät** och klicka sedan på **undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Skriv *myBackendSubnet* som namn på under nätet.
4. Skriv *10.0.2.0/24* för adress intervallet och välj sedan **OK**.

## <a name="add-a-listener-and-redirection-rule&quot;></a>Lägg till en regel för avlyssning och omdirigering

### <a name=&quot;add-the-listener&quot;></a>Lägg till lyssnaren

Lägg först till lyssnaren med namnet min *lyssnare* för port 80.

1. Öppna resurs gruppen **myResourceGroupAG** och välj **myAppGateway**.
2. Välj **lyssnare** och välj sedan **+ Basic**.
3. Skriv en *Belyssnare* för namnet.
4. Skriv *httpPort* för det nya port namnet för klient delen och *80* för porten.
5. Se till att protokollet är inställt på **http** och välj sedan **OK**.

### <a name=&quot;add-a-routing-rule-with-a-redirection-configuration&quot;></a>Lägg till en regel för routning med en omdirigerings konfiguration

1. Välj **regler** på **myAppGateway** och välj sedan **+ begär routningsregler**.
2. Skriv *regel 2* som **regel namn**.
3. Se till att en **lyssnare** har valts för lyssnaren.
4. Klicka på fliken **Server dels mål** och välj **måltyp** som *omdirigering*.
5. För **typ av omdirigering** väljer du **permanent**.
6. För **omdirigerings mål** väljer du **lyssnare**.
7. Se till att **mål lyssnaren** är inställd på **appGatewayHttpListener**.
8. För **include-frågesträngen** och **Inkludera sökväg** väljer du *Ja*.
9. Välj **Lägg till**.

## <a name=&quot;create-a-virtual-machine-scale-set&quot;></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen.

1. Välj **+ skapa en resurs** i det övre vänstra hörnet i portalen.
2. Välj **Beräkna**.
3. Skriv *skalnings uppsättning* i rutan Sök och tryck på RETUR.
4. Välj **skalnings uppsättning för virtuell dator** och välj sedan **skapa**.
5. För **skalnings uppsättnings namn för virtuell dator** skriver du *myvmss*.
6. För operativ system disk avbildning, * * se till att **Windows Server 2016 Data Center** är markerat.
7. För **resurs grupp** väljer du **myResourceGroupAG**.
8. För **användar namn** skriver du *azureuser*.
9. För **lösen ord** skriver du *Azure123456!* och bekräfta lösen ordet.
10. Kontrol lera att värdet är **2** för **instans antal**.
11. Välj **D2s_v3** för **instans storlek**.
12. Under **nätverk**, se till att **Välj alternativ för belastnings utjämning** är inställt på **Application Gateway**.
13. Se till att **Application Gateway** är inställt på **myAppGateway**.
14. Se till att **under nätet** är inställt på **myBackendSubnet**.
15. Välj **Skapa**.

### <a name=&quot;associate-the-scale-set-with-the-proper-backend-pool&quot;></a>Koppla skalnings uppsättningen till rätt backend-pool

Användar gränssnittet för skalnings uppsättningen för virtuell dator skapar en ny backend-pool för skalnings uppsättningen, men du vill associera den med din befintliga appGatewayBackendPool.

1. Öppna resurs gruppen **myResourceGroupAg** .
2. Välj **myAppGateway**.
3. Välj **backend-pooler**.
4. Välj **myAppGatewaymyvmss**.
5. Välj **ta bort alla mål från backend-poolen**.
6. Välj **Spara**.
7. När den här processen är klar väljer du **myAppGatewaymyvmss** -adresspoolen, väljer **ta bort** och sedan **OK** för att bekräfta.
8. Välj **appGatewayBackendPool**.
9. Under **mål** väljer du **VMSS**.
10. Under **VMSS** väljer du **myvmss**.
11. Under **konfigurationer för nätverks gränssnitt** väljer du **myvmssNic**.
12. Välj **Spara**.

### <a name=&quot;upgrade-the-scale-set&quot;></a>Uppgradera skalnings uppsättningen

Slutligen måste du uppgradera skalnings uppsättningen med dessa ändringar.

1. Välj skalnings uppsättningen **myvmss** .
2. Gå till **Inställningar** och välj **Instanser**.
3. Välj båda instanserna och välj sedan **Uppgradera**.
4. Bekräfta genom att välja **Ja**.
5. När detta är klart går du tillbaka till **myAppGateway** och väljer **backend-pooler**. Nu bör du se att **appGatewayBackendPool** har två mål och att  **myAppGatewaymyvmss** har noll mål.
6. Välj **myAppGatewaymyvmss** och välj sedan **ta bort**.
7. Välj **Ja** för att bekräfta.

### <a name=&quot;install-iis&quot;></a>Installera IIS

Ett enkelt sätt att installera IIS på skalnings uppsättningen är att använda PowerShell. I portalen klickar du på ikonen Cloud Shell och kontrollerar att **PowerShell** är markerat.

Klistra in följande kod i PowerShell-fönstret och tryck på RETUR.

```azurepowershell
$publicSettings = @{ &quot;fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>Uppgradera skalnings uppsättningen

När du har ändrat instanserna med IIS måste du återigen uppgradera skalnings uppsättningen med den här ändringen.

1. Välj skalnings uppsättningen **myvmss** .
2. Gå till **Inställningar** och välj **Instanser**.
3. Välj båda instanserna och välj sedan **Uppgradera**.
4. Bekräfta genom att välja **Ja**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan hämta den offentliga IP-adressen för programmet från översikts sidan för Application Gateway.

1. Välj **myAppGateway**.
2. På sidan **Översikt** noterar du IP-adressen under **klient DELENs offentliga IP-adress**.

3. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http://52.170.203.149

   ![Säkerhetsvarning](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**. Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

   ![Testa basadressen i programgatewayen](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar en Programgateway med intern omdirigering](redirect-internal-site-powershell.md).