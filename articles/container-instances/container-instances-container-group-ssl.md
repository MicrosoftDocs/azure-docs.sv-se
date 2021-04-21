---
title: Aktivera TLS med sidovagnscontainer
description: Skapa en SSL- eller TLS-slutpunkt för en containergrupp som körs Azure Container Instances genom att köra Nginx i en sidovagnscontainer
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 906a1f239d7050ea17fd7d1425138049ebf045c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790985"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Aktivera en TLS-slutpunkt i en sidovagnscontainer

Den här artikeln visar hur du skapar en [containergrupp med](container-instances-container-groups.md) en programcontainer och en sidovagnscontainer som kör en TLS/SSL-provider. Genom att konfigurera en containergrupp med en separat TLS-slutpunkt aktiverar du TLS-anslutningar för ditt program utan att ändra programkoden.

Du ställer in en exempelcontainergrupp som består av två containrar:
* En programcontainer som kör en enkel webbapp med hjälp av den offentliga Microsoft [aci-helloworld-avbildningen.](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 
* En sidovagnscontainer som kör den [offentliga Nginx-avbildningen,](https://hub.docker.com/_/nginx) konfigurerad för att använda TLS. 

I det här exemplet exponerar containergruppen endast port 443 för Nginx med sin offentliga IP-adress. Nginx dirigerar HTTPS-begäranden till den tillhörande webbappen, som lyssnar internt på port 80. Du kan anpassa exemplet för containerappar som lyssnar på andra portar. 

Se [Nästa steg](#next-steps) för andra metoder för att aktivera TLS i en containergrupp.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.55 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat

Om du vill konfigurera Nginx som TLS-provider behöver du ett TLS/SSL-certifikat. Den här artikeln visar hur du skapar och ställer in ett själv signerat TLS/SSL-certifikat. I produktionsscenarier bör du skaffa ett certifikat från en certifikatutfärdare.

Om du vill skapa ett själv signerat TLS/SSL-certifikat använder du [OpenSSL-verktyget](https://www.openssl.org/) som finns i Azure Cloud Shell och många Linux-distributioner eller använder ett jämförbart klientverktyg i ditt operativsystem.

Skapa först en certifikatbegäran (CSR-fil) i en lokal arbetskatalog:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Följ anvisningarna för att lägga till identifieringsinformationen. För Eget namn anger du det värdnamn som är associerat med certifikatet. När du uppmanas att ange ett lösenord trycker du på Retur utan att skriva för att hoppa över att lägga till ett lösenord.

Kör följande kommando för att skapa det själv signerade certifikatet (.crt-filen) från certifikatbegäran. Exempel:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Du bör nu se tre filer i katalogen: certifikatbegäran ( `ssl.csr` ), den privata nyckeln ( ) och det själv `ssl.key` signerade certifikatet ( `ssl.crt` ). Du använder `ssl.key` och `ssl.crt` i senare steg.

## <a name="configure-nginx-to-use-tls"></a>Konfigurera Nginx att använda TLS

### <a name="create-nginx-configuration-file"></a>Skapa Nginx-konfigurationsfil

I det här avsnittet skapar du en konfigurationsfil för Nginx för att använda TLS. Börja med att kopiera följande text till en ny fil med namnet `nginx.conf` . I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i arbetskatalogen:

```console
code nginx.conf
```

I `location` måste du ange med rätt port för din `proxy_pass` app. I det här exemplet anger vi port 80 för `aci-helloworld` containern.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Base64-koda hemligheter och konfigurationsfil

Base64-koda Nginx-konfigurationsfilen, TLS/SSL-certifikatet och TLS-nyckeln. I nästa avsnitt anger du det kodade innehållet i en YAML-fil som används för att distribuera containergruppen.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Distribuera containergrupp

Distribuera nu containergruppen genom att ange containerkonfigurationerna i en [YAML-fil](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Skapa YAML-fil

Kopiera följande YAML till en ny fil med namnet `deploy-aci.yaml` . I Azure Cloud Shell kan du använda Visual Studio Code för att skapa filen i arbetskatalogen:

```console
code deploy-aci.yaml
```

Ange innehållet i de base64-kodade filerna där det anges under `secret` . Var och en `cat` av de base64-kodade filerna för att se dess innehåll. Under distributionen läggs dessa filer till på [en hemlig volym](container-instances-volume-secret.md) i containergruppen. I det här exemplet monteras den hemliga volymen till Nginx-containern.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Distribuera containergruppen

Skapa en resursgrupp med [kommandot az group](/cli/azure/group#az_group_create) create:

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Distribuera containergruppen med kommandot [az container create](/cli/azure/container#az_container_create) och skicka YAML-filen som ett argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visa distributionstillstånd

Om du vill visa status för distributionen använder du följande [az container show-kommando:](/cli/azure/container#az_container_show)

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

För en lyckad distribution ser utdata ut ungefär så här:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Verifiera TLS-anslutning

Använd webbläsaren för att navigera till containergruppens offentliga IP-adress. IP-adressen som visas i det här exemplet `52.157.22.76` är , så URL:en är **https://52.157.22.76** . Du måste använda HTTPS för att se programmet som körs på grund av Nginx-serverkonfigurationen. Försök att ansluta via HTTP misslyckas.

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Eftersom det här exemplet använder ett själv signerat certifikat och inte ett från en certifikatutfärdare visar webbläsaren en säkerhetsvarning när du ansluter till webbplatsen via HTTPS. Du kan behöva acceptera varningen eller justera inställningarna för webbläsare eller certifikat för att gå vidare till sidan. Det här beteendet är förväntat.

>

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du ställer in en Nginx-container för att aktivera TLS-anslutningar till en webbapp som körs i containergruppen. Du kan anpassa det här exemplet för appar som lyssnar på andra portar än port 80. Du kan också uppdatera Nginx-konfigurationsfilen för att automatiskt omdirigera serveranslutningar på port 80 (HTTP) för att använda HTTPS.

Den här artikeln använder Nginx i sidovagnen, men du kan använda en annan TLS-provider, till exempel [Caddy](https://caddyserver.com/).

Om du distribuerar containergruppen i ett virtuellt [Azure-nätverk](container-instances-vnet.md)kan du överväga andra alternativ för att aktivera en TLS-slutpunkt för en instans av en servercontainer, inklusive:

* [Azure Functions-proxyservrar](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) – se en [exempeldistributionsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
