---
title: Omdirigering från HTTP till HTTPS i portalen – Azure Application Gateway
description: Lär dig hur du skapar en programgateway med omdirigerad trafik från HTTP till HTTPS med hjälp av Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e7fb40d95c4659bf353366770da7c903ffa1bd09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867231"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Skapa en programgateway med omdirigering från HTTP till HTTPS med hjälp av Azure Portal

Du kan använda Azure Portal för att skapa en [programgateway](overview.md) med ett certifikat för TLS-avslutning. En routningsregel används för att omdirigera HTTP-trafik till HTTPS-porten i programgatewayen. I det här exemplet [](../virtual-machine-scale-sets/overview.md) skapar du också en VM-skalningsuppsättning för serverpoolen för programgatewayen som innehåller två virtuella datorinstanser.

I den här artikeln kan du se hur du:

* Skapa ett självsignerat certifikat
* Konfigurera ett nätverk
* Skapa en programgateway med certifikatet
* Lägga till en lyssnare och omdirigeringsregel
* Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här självstudien kräver Azure PowerShell version 1.0.0 eller senare för att skapa ett certifikat och installera IIS. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du vill köra kommandona i den här självstudien måste du också `Login-AzAccount` köra för att skapa en anslutning till Azure.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

För produktionsanvändning bör du importera ett giltigt certifikat som signerats av en betrodd provider. I den här självstudiekursen skapar du ett självsignerat certifikat med [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate). Du kan använda [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) med det tumavtryck som returnerades och exportera en pfx-fil från certifikatet.

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
8. Under **IP-konfiguration för frontend** ser du **till att IP-adresstypen** är **Offentlig** och att **Skapa ny** har valts. Ange *myAGPublicIPAddress* som namn. Godkänn standardvärdena för de andra inställningarna och klicka sedan på **OK**.
9. Under **Listener configuration**(Lyssnarkonfiguration) väljer du **HTTPS** och väljer sedan Select **a file** (Välj en fil) och navigerar till filen *c:\appgwcert.pfx* och väljer **Öppna**.
10. Skriv *appgwcert* som certifikatnamn och *Azure123456!* som lösenord.
11. Lämna brandväggen för webbaserade program inaktiverad och välj **sedan OK.**
12. Granska inställningarna på sammanfattningssidan och välj sedan **OK för** att skapa nätverksresurserna och programgatewayen. Det kan ta flera minuter för programgatewayen att skapas. Vänta tills distributionen har slutförts innan du går vidare till nästa avsnitt.

### <a name="add-a-subnet"></a>Lägga till ett undernät

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **myVNet** i resurslistan.
2. Välj **Undernät och** klicka sedan på **Undernät**.

    ![Skapa undernät](./media/create-url-route-portal/application-gateway-subnet.png)

3. Skriv *myBackendSubnet* som namn på undernätet.
4. Skriv *10.0.2.0/24* som adressintervall och välj sedan **OK.**

## <a name="add-a-listener-and-redirection-rule"></a>Lägga till en lyssnare och omdirigeringsregel

### <a name="add-the-listener"></a>Lägga till lyssnaren

Lägg först till lyssnaren med namnet *myListener* för port 80.

1. Öppna **resursgruppen myResourceGroupAG** och välj **myAppGateway.**
2. Välj **Lyssnare** och sedan **+ Grundläggande.**
3. Skriv *MyListener* som namn.
4. Skriv *httpPort* som det nya portnamnet för frontend och *80* som port.
5. Kontrollera att protokollet är inställt **på HTTP** och välj sedan **OK.**

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Lägga till en routningsregel med en omdirigeringskonfiguration

1. På **myAppGateway** väljer du **Regler och** sedan **+Begär routningsregel**.
2. Som **Regelnamn skriver** du *Rule2*.
3. Se **till att MyListener** har valts för lyssnaren.
4. Klicka på **fliken Backend targets (Backend-mål)** **och välj Måltyp** *som Omdirigering*.
5. För **Omdirigeringstyp** väljer du **Permanent.**
6. För **Omdirigeringsmål** väljer du **Lyssnare.**
7. Kontrollera att **mållyssnaren** är **inställd på appGatewayHttpListener**.
8. För Inkludera **frågesträng och** **Inkludera sökväg väljer** du *Ja.*
9. Välj **Lägg till**.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som ska tillhandahålla servrar till serverdelspoolen i programgatewayen.

1. I portalens övre vänstra hörn väljer du **+Skapa en resurs**.
2. Välj **Beräkna**.
3. I sökrutan skriver du *skalningsuppsättning och* trycker på Retur.
4. Välj **VM-skalningsuppsättning** och välj sedan **Skapa.**
5. Som **namn på VM-skalningsuppsättningen** skriver *du myvmss*.
6. Kontrollera att Windows Server **2016 Datacenter** är valt för Operativsystemdiskavbildning.
7. För **Resursgrupp** väljer du **myResourceGroupAG**.
8. Som **Användarnamn skriver** du *azureuser*.
9. För **Lösenord** skriver du *Azure123456!* och bekräfta lösenordet.
10. För **Antal instanser** ser du till att värdet är **2**.
11. För **Instansstorlek** väljer **du D2s_v3**.
12. Under **Nätverk ser** du till att Välj alternativ för **belastningsutjämning** är **inställt på Application Gateway**.
13. Kontrollera **att Application Gateway** är inställt på **myAppGateway**.
14. Se **till att Undernät** är **inställt på myBackendSubnet**.
15. Välj **Skapa**.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>Associera skalningsuppsättningen med rätt backend-pool

Portalen för VM-skalningsuppsättningsportalen skapar en ny serverpool för skalningsuppsättningen, men du vill associera den med din befintliga appGatewayBackendPool.

1. Öppna **resursgruppen myResourceGroupAg.**
2. Välj **myAppGateway**.
3. Välj **Backend pools (Backend-pooler).**
4. Välj **myAppGatewaymyvmss**.
5. Välj **Ta bort alla mål från backend-poolen**.
6. Välj **Spara**.
7. När den här processen är klar väljer du backend-poolen **myAppGatewaymyvmss,** väljer **Ta bort** och sedan **OK för** att bekräfta.
8. Välj **appGatewayBackendPool**.
9. Under **Mål** väljer du **VMSS**.
10. Under **VMSS** väljer du **myvmss**.
11. Under **Konfigurationer för nätverksgränssnitt** väljer **du myvmssNic**.
12. Välj **Spara**.

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

Slutligen måste du uppgradera skalningsuppsättningen med dessa ändringar.

1. Välj **skalningsuppsättningen myvmss.**
2. Gå till **Inställningar** och välj **Instanser**.
3. Markera båda instanserna och välj sedan **Uppgradera**.
4. Bekräfta genom att välja **Ja**.
5. När det är klart går du tillbaka till **myAppGateway och** väljer **Backend-pooler**. Nu bör du se att **appGatewayBackendPool** har två mål och  **att myAppGatewaymyvmss** har noll mål.
6. Välj **myAppGatewaymyvmss** och välj sedan Ta **bort.**
7. Välj **Ja** för att bekräfta.

### <a name="install-iis"></a>Installera IIS

Ett enkelt sätt att installera IIS på skalningsuppsättningen är att använda PowerShell. Från portalen klickar du på ikonen Cloud Shell och ser till att **PowerShell** är markerat.

Klistra in följande kod i PowerShell-fönstret och tryck på Retur.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
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

### <a name="upgrade-the-scale-set"></a>Uppgradera skalningsuppsättningen

När du har ändrat instanserna med IIS måste du återigen uppgradera skalningsuppsättningen med den här ändringen.

1. Välj **skalningsuppsättningen myvmss.**
2. Gå till **Inställningar** och välj **Instanser**.
3. Markera båda instanserna och välj sedan **Uppgradera**.
4. Bekräfta genom att välja **Ja**.

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Du kan hämta programmets offentliga IP-adress från översiktssidan för programgatewayen.

1. Välj **myAppGateway.**
2. På sidan **Översikt noterar** du IP-adressen under Frontend public IP address (Offentlig **IP-adress för frontend).**

3. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Till exempel http://52.170.203.149

   ![Säkerhetsvarning](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Om du använde ett självsignerat certifikat och vill acceptera säkerhetsvarningen väljer du **Information** och sedan **Fortsätt till webbsidan**. Din skyddade IIS-webbplats visas sedan som i exemplet nedan:

   ![Testa basadressen i programgatewayen](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du skapar en programgateway med intern omdirigering](redirect-internal-site-powershell.md).
