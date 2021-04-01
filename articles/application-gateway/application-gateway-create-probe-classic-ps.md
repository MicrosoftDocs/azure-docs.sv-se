---
title: Skapa en anpassad avsökning med hjälp av den klassiska distributions modellen – Azure Application Gateway
description: Lär dig hur du skapar en anpassad avsökning för Application Gateway med hjälp av PowerShell i den klassiska distributions modellen
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397866"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Skapa en anpassad avsökning för Azure Application Gateway (klassisk) med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [Azure-portalen](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln lägger du till en anpassad avsökning i en befintlig Application Gateway med PowerShell. Anpassade avsökningar är användbara för program som har en specifik hälso kontroll sida eller för program som inte ger ett lyckat svar på standard webb programmet.

> [!IMPORTANT]
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Så här skapar du en programgateway:

1. Skapa en resurs för en programgateway.
2. Skapa en XML-konfigurationsfil eller ett konfigurationsobjekt.
3. Bekräfta konfigurationen för den nyligen skapade programgatewayresursen.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Skapa en Application Gateway-resurs med en anpassad avsökning

Du skapar en gateway genom att köra cmdleten `New-AzureApplicationGateway`, där du ersätter värdena med dina egna. Faktureringen för gatewayen startar inte i det här läget. Faktureringen börjar i ett senare skede när gatewayen har startats.

I följande exempel skapar vi en programgateway genom att använda ett virtuellt nätverk med namnet ”testvnet1” och undernätet ”subnet-1”.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Du kontrollerar att gatewayen har skapats genom att köra cmdleten `Get-AzureApplicationGateway`.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Standardvärdet för *InstanceCount* är 2, och det högsta värdet är 10. Standardvärdet för *GatewaySize* är Medium. Du kan välja mellan liten, medel och stor.
> 
> 

*VirtualIPs* och *DnsName* är tomma eftersom gatewayen inte har startat än. Dessa värden skapas när gatewayen är i körnings läge.

### <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurera en Programgateway med hjälp av XML

I följande exempel använder vi en XML-fil för att konfigurera alla inställningar för programgatewayen och för att skicka dem till programgatewayresursen.  

Kopiera följande text till Anteckningar.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Redigera värdena mellan parenteserna för konfigurationsobjekten. Spara filen med filnamnstillägget .xml.

I följande exempel visas hur du använder en konfigurations fil för att konfigurera programgatewayen för belastnings utjämning av HTTP-trafik på den offentliga porten 80 och skicka nätverks trafik till backend-port 80 mellan två IP-adresser med hjälp av en anpassad avsökning.

> [!IMPORTANT]
> Protokollobjekten Http och Https är skiftlägeskänsliga.

Ett nytt konfigurations objekt \<Probe\> läggs till för att konfigurera anpassade avsökningar.

Konfigurations parametrarna är:

|Parameter|Beskrivning|
|---|---|
|**Namn** |Referens namn för anpassad avsökning. |
| **Protokoll** | Protokoll som används (möjliga värden är HTTP eller HTTPS).|
| **Värd** och **sökväg** | Fullständig URL-sökväg som anropas av Application Gateway för att fastställa instansens hälso tillstånd. Om du till exempel har en webbplats http: \/ /contoso.com/, kan den anpassade avsökningen konfigureras för "http: \/ /contoso.com/Path/custompath.htm" för avsöknings kontroller för att få ett lyckat http-svar.|
| **Intervall** | Konfigurerar avsöknings intervallet i sekunder.|
| **Standardvärde** | Definierar avsöknings tids gränsen för en HTTP-svars kontroll.|
| **UnhealthyThreshold** | Antalet misslyckade HTTP-svar som krävs för att flagga backend-instansen som *ohälsosam*.|

Avsöknings namnet refereras till i \<BackendHttpSettings\> konfigurationen för att tilldela vilken backend-pool som använder anpassade avsöknings inställningar.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Lägga till en anpassad avsökning i en befintlig Application Gateway

Att ändra den aktuella konfigurationen av en Application Gateway kräver tre steg: hämta den aktuella XML-konfigurationsfilen, ändra till att ha en anpassad avsökning och konfigurera programgatewayen med de nya XML-inställningarna.

1. Hämta XML-filen med hjälp av `Get-AzureApplicationGatewayConfig` . Denna cmdlet exporterar konfigurations-XML som ska ändras för att lägga till en avsöknings inställning.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Öppna XML-filen i en text redigerare. Lägg till ett `<probe>` avsnitt efter `<frontendport>` .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   I avsnittet backendHttpSettings i XML-filen lägger du till namnet på avsökningen som visas i följande exempel:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Spara XML-filen.

1. Uppdatera Application Gateway-konfigurationen med den nya XML-filen med hjälp av `Set-AzureApplicationGatewayConfig` . Den här cmdleten uppdaterar programgatewayen med den nya konfigurationen.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Nästa steg

Om du vill konfigurera Transport Layer Security (TLS), som tidigare kallades Secure Sockets Layer (SSL) avlastning, se [Konfigurera en Application Gateway för TLS-avläsning](./tutorial-ssl-powershell.md).

Om du vill konfigurera en programgateway för användning med en intern lastbalanserare läser du [Skapa en programgateway med en intern lastbalanserare (ILB)](./application-gateway-ilb-arm.md).