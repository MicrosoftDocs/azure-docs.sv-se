---
title: Generera ett självsignerat certifikat med en anpassad rot certifikat utfärdare
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar ett självsignerat Azure Application-Gateway-certifikat med en anpassad rot certifikat utfärdare
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396948"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generera ett självsignerat certifikat för Azure Application Gateway med en egen rotcertifikatutfärdare

Application Gateway v2-SKU: n introducerar användningen av betrodda rot certifikat för att tillåta backend-servrar. Detta tar bort autentiseringscertifikat som krävs i v1 SKU. *Rot certifikatet* är en Base-64-kodad X. 509 (. CER) formatera rot certifikat från Server dels certifikat servern. Den identifierar rot certifikat utfärdaren (CA) som utfärdade Server certifikatet och Server certifikatet används sedan för TLS/SSL-kommunikation.

Application Gateway litar på din webbplats certifikat som standard om det är signerat av en välkänd certifikat utfärdare (till exempel GoDaddy eller DigiCert). Du behöver inte uttryckligen ladda upp rot certifikatet i detta fall. Mer information finns i [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md). Men om du har en utvecklings-/test miljö och inte vill köpa ett verifierat CA-signerat certifikat, kan du skapa en egen anpassad certifikat utfärdare och skapa ett självsignerat certifikat med det. 

> [!NOTE]
> Självsignerade certifikat är inte betrodda som standard och de kan vara svåra att underhålla. De kan också använda inaktuella hash-och chiffersviter som kanske inte är starka. Köp ett certifikat som signerats av en välkänd certifikat utfärdare för bättre säkerhet.

I den här artikeln får du lära dig att:

- Skapa din egen anpassade certifikat utfärdare
- Skapa ett självsignerat certifikat signerat av din anpassade certifikat utfärdare
- Ladda upp ett självsignerat rot certifikat till en Application Gateway för att autentisera backend-servern

## <a name="prerequisites"></a>Förutsättningar

- **[Openssl](https://www.openssl.org/) på en dator som kör Windows eller Linux** 

   Även om andra verktyg är tillgängliga för certifikat hantering, använder den här självstudien OpenSSL. Du kan hitta OpenSSL med många Linux-distributioner, till exempel Ubuntu.
- **En webb server**

   Till exempel Apache, IIS eller NGINX för att testa certifikaten.

- **En Application Gateway v2-SKU**
   
  Om du inte har en befintlig Application Gateway, se [snabb start: direkt webb trafik med Azure Application Gateway-Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Skapa ett rot certifikat för certifikat utfärdare

Skapa ditt rot certifikat för certifikat utfärdare med hjälp av OpenSSL.

### <a name="create-the-root-key"></a>Skapa rot nyckeln

1. Logga in på datorn där OpenSSL är installerat och kör följande kommando. Detta skapar en lösenordsskyddad nyckel.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Ange ett starkt lösen ord i prompten. Till exempel, minst nio tecken, med versaler, gemener, siffror och symboler.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Skapa ett rot certifikat och registrera det själv

1. Använd följande kommandon för att skapa CSR och certifikat.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Föregående kommandon skapar rot certifikatet. Du kommer att använda den här för att signera Server certifikatet.

1. När du uppmanas till det anger du lösen ordet för rot nyckeln och organisations informationen för den anpassade certifikat utfärdaren, till exempel land/region, tillstånd, org, OU och det fullständigt kvalificerade domän namnet (detta är domän för utfärdaren).

   ![Skapa rot certifikat](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Skapa ett Server certifikat

Därefter skapar du ett Server certifikat med hjälp av OpenSSL.

### <a name="create-the-certificates-key"></a>Skapa Certifikatets nyckel

Använd följande kommando för att generera nyckeln för Server certifikatet.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Skapa CSR (certifikat signerings förfrågan)

CSR är en offentlig nyckel som tilldelas en certifikat utfärdare när ett certifikat begärs. CA: n utfärdar certifikatet för den här specifika begäran.

> [!NOTE]
> CN (eget namn) för Server certifikatet måste skilja sig från utfärdarens domän. I det här fallet är till exempel CN för utfärdaren `www.contoso.com` och Server certifikatets CN `www.fabrikam.com` .


1. Använd följande kommando för att generera CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. När du uppmanas till det anger du lösen ordet för rot nyckeln och organisationens information för den anpassade certifikat utfärdaren: land/region, tillstånd, org, OU och det fullständigt kvalificerade domän namnet. Det här är domänen för webbplatsen och den bör inte vara samma som utfärdaren.

   ![Server certifikat](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generera certifikatet med CSR och nyckeln och signera det med certifikat utfärdarens rot nyckel

1. Använd följande kommando för att skapa certifikatet:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verifiera det nyligen skapade certifikatet

1. Använd följande kommando för att skriva ut utdata från CRT-filen och kontrol lera dess innehåll:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Certifikat verifiering](media/self-signed-certificates/verify-cert.png)

1. Kontrol lera filerna i din katalog och se till att du har följande filer:

   - contoso. CRT
   - contoso. Key
   - Fabrikam. CRT
   - Fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Konfigurera certifikatet på webb serverns TLS-inställningar

Konfigurera TLS i webb servern med hjälp av filerna fabrikam. CRT och fabrikam. Key. Om webb servern inte kan ta två filer kan du kombinera dem till en enda. pem-eller. pfx-fil med hjälp av OpenSSL-kommandon.

### <a name="iis"></a>IIS

Anvisningar om hur du importerar certifikat och laddar upp dem som server certifikat i IIS finns i [så här gör du: Installera importerade certifikat på en webb server i Windows server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Instruktioner för TLS-bindning finns i [så här konfigurerar du SSL i IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Följande konfiguration är ett exempel på en [virtuell värd som kon figurer ATS för SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) i Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Följande konfiguration är ett exempel på [nginx Server block](https://nginx.org/docs/http/configuring_https_servers.html) med TLS-konfiguration:

![NGINX med TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Få åtkomst till servern för att verifiera konfigurationen

1. Lägg till rot certifikatet i datorns betrodda rot arkiv. När du ansluter till webbplatsen kontrollerar du att hela certifikat kedjan visas i webbläsaren.

   ![Betrodda rotcertifikat](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Det förutsätts att DNS har kon figurer ATS för att peka på webb serverns namn (i det här exemplet www.fabrikam.com) till din webb servers IP-adress. Om inte, kan du redigera [hosts-filen](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) för att matcha namnet.
1. Bläddra till din webbplats och klicka på Lås ikonen i webbläsarens Adress fält för att kontrol lera plats-och certifikat information.

## <a name="verify-the-configuration-with-openssl"></a>Verifiera konfigurationen med OpenSSL

Du kan också använda OpenSSL för att verifiera certifikatet.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verifiering av OpenSSL-certifikat](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Ladda upp rot certifikatet till Application Gateway HTTP-inställningar

Om du vill överföra certifikatet i Application Gateway måste du exportera. CRT-certifikatet till ett. cer-format Base-64-kodat. Eftersom. CRT redan innehåller den offentliga nyckeln i formatet Base-64-kodat byter du bara namn på fil namns tillägget från. CRT till. cer. 

### <a name="azure-portal"></a>Azure Portal

Om du vill ladda upp det betrodda rot certifikatet från portalen väljer du **http-inställningar** och sedan **https** -protokollet.

![Lägga till ett certifikat med hjälp av portalen](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Du kan också använda Azure CLI eller Azure PowerShell för att ladda upp rot certifikatet. Följande kod är ett Azure PowerShell exempel.

> [!NOTE]
> Följande exempel lägger till ett betrott rot certifikat till Application Gateway, skapar en ny HTTP-inställning och lägger till en ny regel, förutsatt att Server dels-poolen och lyssnaren redan finns.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Verifiera Server delen för Application Gateway

1. Klicka på **Server delens hälso** läge för din Application Gateway för att kontrol lera om avsökningen är felfri.
1. Du bör se att statusen är **felfri** för https-avsökningen.

![HTTPS-avsökning](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Nästa steg

Mer information om SSL\TLS i Application Gateway finns i [Översikt över TLS-terminering och slut punkt till slut punkt för TLS med Application Gateway](ssl-overview.md).