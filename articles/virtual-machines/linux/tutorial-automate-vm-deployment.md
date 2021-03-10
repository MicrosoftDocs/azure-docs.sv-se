---
title: Självstudie – anpassa en virtuell Linux-dator med Cloud-Init i Azure
description: I den här självstudien får du lära dig hur du använder Cloud-Init och Key Vault för att anpassa virtuella Linux-datorer första gången de startas i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6dd10039ba5c71d3a787761914b111e7828c47d3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552617"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Självstudiekurs – Så här använder du cloud-init för att anpassa en virtuell Linux-dator i Azure vid den första starten

I en tidigare självstudiekurs lärde du dig hur du anslöt till en virtuell dator med SSH och installerade NGINX manuellt. Om du vill skapa virtuella datorer på ett snabbt och konsekvent sätt, kan det vara användbart med någon form av automatisering. Ett vanligt sätt att anpassa en virtuell dator första gången den startar är att använda [cloud-init](https://cloudinit.readthedocs.io). I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en cloud-init-konfigurationsfil
> * Skapa en virtuell dator som använder en cloud-init-fil
> * Visa en Node.js-app som körs efter att den virtuella datorn skapats
> * Använda Key Vault för att förvara certifikat säkert
> * Automatisera säker distribution av NGINX med cloud-init

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Översikt över Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Vi arbetar med våra partners och försöker göra så att cloud-init inkluderas och fungerar i de avbildningar de tillhandahåller till Azure. För detaljerad information Cloud-Init-stöd för varje distribution, se [Cloud-Init-stöd för virtuella datorer i Azure](using-cloud-init.md).


## <a name="create-cloud-init-config-file"></a>Skapa en cloud-init-konfigurationsfil
Om du vill se hur cloud-init fungerar i praktiken skapar du en virtuell dator som installerar NGINX och kör en enkel ”Hello World” Node.js-app. Den här cloud-init-konfigurationen installerar de paket som krävs, skapar en Node.js-app och initierar och startar appen.

I bash-prompten eller på Cloud Shell skapar du en fil med namnet *cloud-init.txt* och klistrar in följande konfiguration. Skriv till exempel om `sensible-editor cloud-init.txt` du vill skapa filen och visa en lista över tillgängliga redigerare. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Mer information om konfigurationsalternativ för cloud-init finns i [konfigurationsexempel för cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Innan du kan skapa en virtuell dator skapar du en resursgrupp med [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupAutomate* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Använd parametern `--custom-data` för att skicka in din cloud-init-konfigurationsfil. Ange den fullständiga sökvägen till *cloud-init.txt* om du sparat filen utanför din aktuella arbetskatalog. I följande exempel skapas en virtuell dator med namnet *myVM*:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Det tar några minuter innan den virtuella datorn skapas, paketen installeras och appen startar. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Det kan ta några minuter innan du kan öppna appen. När den virtuella datorn har skapats ska du anteckna `publicIpAddress` som visas av Azure CLI. Adressen används för att komma åt Node.js i en webbläsare.

För att låta webbtrafik nå din virtuella dator öppnar du port 80 från Internet med [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Testa webbappen
Nu kan du öppna en webbläsare och ange *http: \/ \/ \<publicIpAddress>* i adress fältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Din Node.js-app visas som den visas i det här exemplet:

![Visa NGINX-webbplats som körs](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Mata in certifikat från Key Vault
Det här valfria avsnittet visar hur du kan lagra certifikat säkert i Azure Key Vault och mata in dem under distributionen av den virtuella datorn. Till skillnad från att använda en anpassad avbildning som innehåller certifikaten gör den här processen att de senaste certifikaten matas in i den virtuella datorn vid första start. Under processen lämnar certifikatet aldrig Azure-plattformen och exponeras inte i något skript, någon kommandoradshistorik eller mall.

Azure Key Vault skyddar krypteringsnycklar och hemligheter, som certifikat och lösenord. Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Det här scenariot beskriver en del Key Vault-koncept som används för att skapa och använda ett certifikat, det är inte en utförlig beskrivning av hur du använder Key Vault.

Följande steg visar hur du kan:

- Skapa ett Azure Key Vault
- Generera eller ladda upp ett certifikat till Key Vault
- Skapa en hemlighet från certifikatet som kan matas in i en virtuell dator
- Skapa en virtuell dator och mata in certifikatet

### <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Skapa först ett Key Vault med [az keyvault create](/cli/azure/keyvault#az-keyvault-create) och aktivera det för användning när du distribuerar en virtuell dator. För varje Key Vault krävs ett unikt namn som ska skrivas med gemener. Ersätt `mykeyvault` i följande exempel med ditt eget unika Key Vault-namn:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generera ett certifikat och lagra det i Key Vault
För produktion bör du importera ett giltigt certifikat som är signerat av en betrodd provider med [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import). För den här självstudien visar följande exempel hur du kan generera ett självsignerat certifikat med [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) som använder standardprincipen för certifikat:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Förbereda ett certifikat för användning med en virtuell dator
För att använda certifikatet medan den virtuella datorn skapas ska du hämta certifikatets ID med [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions). Den virtuella datorn behöver certifikatet i ett visst format för att det ska kunna injiceras vid uppstart. Du konverterar certifikatet med [az vm secret format](/cli/azure/vm). Följande exempel tilldelar kommandonas resultat till variabler, vilket gör dem enklare att använda i nästa steg:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Skapa en cloud-init-konfiguration för att skydda NGINX
När du skapar en virtuella dator lagras certifikat och nycklar i den skyddade katalogen */var/lib/waagent/*. Om du vill automatisera inmatningen av certifikatet i den virtuella datorn och konfigurera NGINX kan du använda en uppdaterad cloud-init-konfigurationsfil från föregående exempel.

Skapa en fil med namnet *cloud-init-secured.txt* och klistra in följande konfiguration. Om du använder Cloud Shell skapar du konfigurations filen för Cloud-Init där och inte på den lokala datorn. Skriv till exempel om `sensible-editor cloud-init-secured.txt` du vill skapa filen och visa en lista över tillgängliga redigerare. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Skapa en säker virtuell dator
Skapa nu en virtuell dator med [az vm create](/cli/azure/vm#az-vm-create). Certifikatdata matas in från Key Vault med parametern `--secrets`. Som i föregående exempel skickar du in cloud-init-konfigurationen med parametern `--custom-data`:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Det tar några minuter innan den virtuella datorn skapas, paketen installeras och appen startar. Det finns bakgrundsaktiviteter som fortsätter att köras när Azure CLI återgår till kommandotolken. Det kan ta några minuter innan du kan öppna appen. När den virtuella datorn har skapats ska du anteckna `publicIpAddress` som visas av Azure CLI. Adressen används för att komma åt Node.js i en webbläsare.

För att låta säker webbtrafik nå din virtuella dator ska du öppna port 443 från Internet med [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>Testa säker webbapp
Nu kan du öppna en webbläsare och ange *https: \/ \/ \<publicIpAddress>* i adress fältet. Ange din egen offentliga IP-adress som visas i utdata från den tidigare Skapa virtuell dator-processen. Om du använder ett självsignerat certifikat ska du acceptera säkerhetsvarningen:

![Acceptera webbläsarens säkerhetsvarning](./media/tutorial-automate-vm-deployment/browser-warning.png)

Din skyddade NGINX-webbplats och Node.js-appen visas sedan som i exemplet nedan:

![Visa skyddad NGINX-webbplats som körs](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du cloud-init för att konfigurera virtuella datorer vid första start. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en cloud-init-konfigurationsfil
> * Skapa en virtuell dator som använder en cloud-init-fil
> * Visa en Node.js-app som körs efter att den virtuella datorn skapats
> * Använda Key Vault för att förvara certifikat säkert
> * Automatisera säker distribution av NGINX med cloud-init

Gå vidare till nästa självstudie där du får lära dig att skapa anpassade avbildningar för virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa anpassade avbildningar av en virtuell dator](./tutorial-custom-images.md)
