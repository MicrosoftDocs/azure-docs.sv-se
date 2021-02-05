---
title: Migrera från v1 till v2 – Azure Application Gateway
description: Den här artikeln visar hur du migrerar Azure Application Gateway och brand vägg för webbaserade program från v1 till v2
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 4757a8237aa6226b78e7c1e79ba50710e31d28e3
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594273"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrera Azure Application Gateway och brand vägg för webbaserade program från v1 till v2

[Azure Application Gateway och brand vägg för webbaserade program (WAF) v2](application-gateway-autoscaling-zone-redundant.md) är nu tillgängligt och erbjuder ytterligare funktioner, till exempel autoskalning och redundans för tillgänglighets zon. Befintliga v1-gatewayer uppgraderas inte automatiskt till v2. Om du vill migrera från v1 till v2 följer du stegen i den här artikeln.

Det finns två steg i en migrering:

1. Migrera konfigurationen
2. Migrera klient trafiken

Den här artikeln beskriver migrering av konfiguration. Migreringen av klient trafiken varierar beroende på din miljö. Några [övergripande rekommendationer finns](#migrate-client-traffic)dock.

## <a name="migration-overview"></a>Översikt över migrering

Det finns ett Azure PowerShell-skript tillgängligt som gör följande:

* Skapar en ny Standard_v2 eller WAF_v2 gateway i ett undernät för virtuellt nätverk som du anger.
* Kopierar sömlöst den konfiguration som är kopplad till v1-standard-eller WAF-gatewayen till den nyligen skapade Standard_V2 eller WAF_V2 Gateway.

### <a name="caveatslimitations"></a>Caveats\Limitations

* Den nya v2-gatewayen har nya offentliga och privata IP-adresser. Det går inte att flytta IP-adresserna som är kopplade till den befintliga v1-gatewayen sömlöst till v2. Du kan dock allokera en befintlig offentlig eller privat IP-adress till den nya v2-gatewayen.
* Du måste ange ett IP-adressutrymme för ett annat undernät i ditt virtuella nätverk där v1-gatewayen finns. Skriptet kan inte skapa v2-gatewayen i några befintliga undernät som redan har en v1-Gateway. Men om det befintliga under nätet redan har en v2-Gateway, kan det fortfarande fungera om det finns tillräckligt med IP-adressutrymme.
* Om du har en nätverks säkerhets grupp eller användardefinierade vägar kopplade till v2 Gateway-undernätet, kontrollerar du att de följer kraven för [NSG](../application-gateway/configuration-infrastructure.md#network-security-groups) och [UDR](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) för en lyckad migrering
* [Slut punkts principer för virtuella nätverk](../virtual-network/virtual-network-service-endpoint-policies-overview.md) stöds för närvarande inte i ett Application Gateway-undernät.
* Om du vill migrera en TLS/SSL-konfiguration måste du ange alla TLS/SSL-certifikat som används i v1-gatewayen.
* Om FIPS-läge har Aktiver ATS för din v1-Gateway migreras den inte till din nya v2-Gateway. FIPS-läge stöds inte i v2.
* v2 stöder inte IPv6, så IPv6-aktiverade v1-gatewayer migreras inte. Om du kör skriptet kanske det inte slutförs.
* Om v1-gatewayen bara har en privat IP-adress skapar skriptet en offentlig IP-adress och en privat IP-adress för den nya v2-gatewayen. v2 gateways stöder för närvarande inte endast privata IP-adresser.
* Rubriker med namn som innehåller något annat än bokstäver, siffror, bindestreck och under streck skickas inte till ditt program. Detta gäller endast rubrik namn, inte rubrik värden. Detta är en avbrytande ändring från v1.

## <a name="download-the-script"></a>Hämta skriptet

Hämta migrerings skriptet från  [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAppGWMigration).

## <a name="use-the-script"></a>Använd skriptet

Det finns två alternativ för dig, beroende på din lokala PowerShell-Miljös konfiguration och inställningar:

* Om du inte har installerat Azure AZ-moduler, eller om du inte vill avinstallera Azure AZ-modulerna, är det bästa alternativet att använda `Install-Script` alternativet för att köra skriptet.
* Om du behöver behålla Azure AZ-modulerna är det bästa valet att ladda ned skriptet och köra det direkt.

Du kan ta reda på om du har installerat Azure AZ-moduler genom att köra `Get-InstalledModule -Name az` . Om du inte ser några installerade AZ-moduler kan du använda- `Install-Script` metoden.

### <a name="install-using-the-install-script-method"></a>Installera med hjälp av metoden Install-Script

Om du vill använda det här alternativet behöver du inte ha de Azure AZ-moduler som är installerade på datorn. Om de är installerade visar följande kommando ett fel. Du kan antingen avinstallera Azure AZ-moduler eller använda det andra alternativet för att ladda ned skriptet manuellt och köra det.
  
Kör skriptet med följande kommando för att hämta den senaste versionen:

`Install-Script -Name AzureAppGWMigration -Force`

Det här kommandot installerar även de AZ-moduler som krävs.  

### <a name="install-using-the-script-directly"></a>Installera med hjälp av skriptet direkt

Om du har några Azure AZ-moduler installerade och inte kan avinstallera dem (eller inte vill avinstallera dem) kan du hämta skriptet manuellt med hjälp av fliken **manuell hämtning** i länken för hämtning av skript. Skriptet laddas ned som en RAW nupkg-fil. Om du vill installera skriptet från den här nupkg-filen, se [manuell paket hämtning](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Kör skriptet så här:

1. Använd `Connect-AzAccount` för att ansluta till Azure.

1. Används `Import-Module Az` för att importera AZ-modulerna.

1. Kör `Get-Help AzureAppGWMigration.ps1` för att undersöka de nödvändiga parametrarna:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parametrar för skriptet:
   * **resourceId: [sträng]: krävs** – det här är Azure-resurs-ID: t för din befintliga standard v1-eller WAF v1-Gateway. Om du vill hitta strängvärdet navigerar du till Azure Portal, väljer din Application Gateway-eller WAF-resurs och klickar på länken **Egenskaper** för gatewayen. Resurs-ID finns på sidan.

     Du kan också köra följande Azure PowerShell-kommandon för att hämta resurs-ID:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [sträng]: krävs** – det här är det IP-adressutrymme som du har tilldelat (eller vill allokera) för ett nytt undernät som innehåller din nya v2-Gateway. Detta måste anges i CIDR-notation. Till exempel: 10.0.0.0/24. Du behöver inte skapa det här under nätet i förväg. Skriptet skapar det åt dig om det inte finns.
   * **appgwName: [sträng]: valfritt**. Det här är en sträng som du anger för att använda som namn på den nya Standard_v2 eller WAF_v2 Gateway. Om den här parametern inte anges kommer namnet på din befintliga v1-Gateway att användas med suffixet *_v2* bifogad.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: valfritt**.  En kommaavgränsad lista med PSApplicationGatewaySslCertificate-objekt som du skapar för att representera TLS/SSL-certifikat från din v1-Gateway måste överföras till den nya v2-gatewayen. För var och en av dina TLS/SSL-certifikat som har kon figurer ATS för din standard v1-eller WAF v1-Gateway, kan du skapa ett nytt PSApplicationGatewaySslCertificate-objekt via `New-AzApplicationGatewaySslCertificate` kommandot som visas här. Du behöver sökvägen till TLS/SSL-cert-filen och lösen ordet.

     Den här parametern är bara valfri om du inte har HTTPS-lyssnare som har kon figurer ATS för din v1-gateway eller WAF. Om du har minst en HTTPS Listener-installation måste du ange den här parametern.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Du kan skicka in `$mySslCert1, $mySslCert2` (kommaavgränsade) i föregående exempel som värden för den här parametern i skriptet.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: valfritt**. En kommaavgränsad lista med PSApplicationGatewayTrustedRootCertificate-objekt som du skapar för att representera de [betrodda rot certifikaten](ssl-overview.md) för autentisering av Server dels instanserna från v2-gatewayen.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Om du vill skapa en lista över PSApplicationGatewayTrustedRootCertificate-objekt, se [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate).
   * **privateIpAddress: [sträng]: valfritt**. En speciell privat IP-adress som du vill koppla till din nya v2-Gateway.  Detta måste vara från samma VNet som du allokerar för din nya v2-Gateway. Om detta inte anges allokerar skriptet en privat IP-adress för din v2-Gateway.
   * **publicIpResourceId: [sträng]: valfritt**. Resurs-ID för befintlig offentlig IP-adress (standard-SKU) i din prenumeration som du vill tilldela till den nya v2-gatewayen. Om detta inte anges allokerar skriptet en ny offentlig IP-adress i samma resurs grupp. Namnet är v2-gatewayens namn med *-IP* tillagt.
   * **validateMigration: [växel]: valfritt**. Använd den här parametern om du vill att skriptet ska utföra vissa grundläggande konfigurations jämförelser när v2-gatewayen har skapats och konfigurations kopian. Ingen validering görs som standard.
   * **enableAutoScale: [växel]: valfritt**. Använd den här parametern om du vill att skriptet ska aktivera autoskalning på den nya v2-gatewayen när den har skapats. Autoskalning är inaktiverat som standard. Du kan alltid aktivera den manuellt senare på den nyligen skapade v2-gatewayen.

1. Kör skriptet med lämpliga parametrar. Det kan ta fem till sju minuter att slutföra.

    **Exempel**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrera klient trafik

Börja med att kontrol lera att skriptet har skapat en ny v2-Gateway med den exakta konfigurationen som migrerats över från v1-gatewayen. Du kan kontrol lera detta från Azure Portal.

Skicka också en liten mängd trafik via v2-gatewayen som ett manuellt test.
  
Här följer några scenarier där din aktuella Application Gateway (standard) kan ta emot klient trafik och våra rekommendationer för var och en:

* **En anpassad DNS-zon (till exempel contoso.com) som pekar på klient delens IP-adress (med en A-post) som är associerad med din standard v1-eller WAF v1-Gateway**.

    Du kan uppdatera DNS-posten så att den pekar på klient delens IP-adress eller DNS-etikett som är associerad med Standard_v2 Application Gateway. Beroende på vilken TTL som kon figurer ATS på din DNS-post kan det ta en stund innan all klient trafik migreras till din nya v2-Gateway.
* **En anpassad DNS-zon (till exempel contoso.com) som pekar på DNS-etiketten (till exempel: *myappgw.eastus.cloudapp.Azure.com* med hjälp av en CNAME-post) som är associerad med din v1-Gateway**.

   Du kan välja mellan två alternativ:

  * Om du använder offentliga IP-adresser på din Application Gateway kan du göra en kontrollerad, detaljerad migrering med hjälp av en Traffic Manager-profil för att stegvis dirigera trafik (viktad Traffic routing-metod) till den nya v2-gatewayen.

    Du kan göra detta genom att lägga till DNS-etiketterna för både v1-och v2-programgatewayer i [Traffic Manager profilen](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)och skapa en CNAME-post (till exempel `www.contoso.com` ) till Traffic Manager-domänen (till exempel contoso.trafficmanager.net).
  * Eller så kan du uppdatera din DNS-post för anpassade domäner så att den pekar på DNS-etiketten för den nya v2 Application Gateway. Beroende på vilken TTL som kon figurer ATS på din DNS-post kan det ta en stund innan all klient trafik migreras till din nya v2-Gateway.
* **Klienterna ansluter till klient delens IP-adress för din Application Gateway**.

   Uppdatera klienterna så att de använder de IP-adresser som är associerade med den nyligen skapade v2-programgatewayen. Vi rekommenderar att du inte använder IP-adresser direkt. Överväg att använda DNS-namn etiketten (till exempel yourgateway.eastus.cloudapp.azure.com) som är associerad med din Application Gateway som du kan CNAME till din egen anpassade DNS-zon (till exempel contoso.com).

## <a name="common-questions"></a>Vanliga frågor

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Finns det några begränsningar med Azure PowerShell-skriptet för att migrera konfigurationen från v1 till v2?

Ja. Se [varningar/begränsningar](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Är den här artikeln och det Azure PowerShell skript som är tillämpligt för Application Gateway WAF-produkten också? 

Ja.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Växlar Azure PowerShell-skriptet också över trafiken från min v1-Gateway till den nyligen skapade v2-gatewayen?

Nej. Azure PowerShell-skriptet migrerar bara konfigurationen. Den faktiska trafikmigreringen är ditt ansvar och i din kontroll.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Är den nya v2-gatewayen skapad av Azure PowerShell skript storlek korrekt för att hantera all trafik som för närvarande betjänas av min v1-Gateway?

Azure PowerShell skriptet skapar en ny v2-Gateway med lämplig storlek för att hantera trafiken på din befintliga v1-Gateway. Autoskalning är inaktiverat som standard, men du kan aktivera autoskalning när du kör skriptet.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Jag konfigurerade min v1-Gateway för att skicka loggar till Azure Storage. Replikerar skriptet även den här konfigurationen för v2?

Nej. Skriptet replikerar inte den här konfigurationen för v2. Du måste lägga till logg konfigurationen separat till den migrerade v2-gatewayen.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Stöder det här skriptet certifikat som laddats upp till Azure-nyckel valv?

Nej. För närvarande stöder skriptet inte certifikat i nyckel valvet. Detta kommer dock att övervägas för framtida versioner.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Jag har stött på problem med att använda det här skriptet. Hur kan jag få hjälp?
  
Du kan kontakta Azure-supporten under avsnittet "konfiguration och installation/migrera till v2 SKU". Läs mer om [Azure-support här](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nästa steg

[Läs mer om Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)
