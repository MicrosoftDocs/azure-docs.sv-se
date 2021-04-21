---
title: 'Självstudie: Skydda en webbserver med TLS/SSL-certifikat'
description: I den här självstudien får du lära dig hur du använder Azure CLI till att skydda en virtuell Linux-dator som kör webbservern NGINX med SSL-certifikat som lagras i Azure Key Vault.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4b2290d29d1f6fbf8acd2e2652cd47a321fe674f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813172"
---
# <a name="tutorial-use-tlsssl-certificates-to-secure-a-web-server"></a>Självstudie: Använda TLS/SSL-certifikat för att skydda en webbserver
För att skydda webbservrar kan ett Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL), användas för att kryptera webbtrafik. Dessa TLS/SSL-certifikat kan lagras i Azure Key Vault och tillåta säkra distributioner av certifikat till virtuella Linux-datorer (VM) i Azure. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera eller ladda upp ett certifikat till Key Vault
> * Skapa en virtuell dator och installera NGINX-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera NGINX med en TLS-bindning

I den här självstudien används [CLI i Azure Cloud Shell](../../cloud-shell/overview.md), som ständigt uppdateras till den senaste versionen. Om du vill Cloud Shell väljer **du Prova** längst upp i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="overview"></a>Översikt
Azure Key Vault skyddar krypteringsnycklar och hemligheter, som certifikat och lösenord. Key Vault förenklar certifikathanteringen och låter dig behålla kontrollen över nycklar som kommer åt certifikaten. Du kan skapa ett självsignerat certifikat i Key Vault eller ladda upp ett befintligt, betrott certifikat som du redan äger.

Istället för att använda en anpassad VM-avbildning med inbyggda certifikat matar du in certifikat till en virtuell dator som körs. Den här processen garanterar att de mest uppdaterade certifikaten är installerade på en webbserver under distributionen. Om du förnyar eller ersätter ett certifikat behöver du inte skapa en ny anpassad VM-avbildning. De senaste certifikaten matas ut automatiskt när du skapar fler virtuella datorer. Under hela processen lämnar certifikaten aldrig Azure-plattformen och exponeras inte i något skript, kommandoradshistorik eller mall.


## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault
Innan du kan skapa ett Key Vault och certifikat skapar du en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroupSecureWeb* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Därefter skapar du ett Key Vault med [az keyvault create](/cli/azure/keyvault) och aktiverar den för användning när du distribuerar en virtuell dator. För varje Key Vault krävs ett unikt namn som ska skrivas med gemener. Ersätt *\<mykeyvault>* i följande exempel med ditt eget unika Key Vault namn:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generera ett certifikat och lagra det i Key Vault
För produktion bör du importera ett giltigt certifikat som är signerat av en betrodd provider med [az keyvault certificate import](/cli/azure/keyvault/certificate). För den här självstudien visar följande exempel hur du kan generera ett självsignerat certifikat med [az keyvault certificate create](/cli/azure/keyvault/certificate) som använder standardprincipen för certifikat:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Förbereda ett certifikat för användning med en virtuell dator
För att använda certifikatet medan den virtuella datorn skapas ska du hämta certifikatets ID med [az keyvault secret list-versions](/cli/azure/keyvault/secret). Konvertera certifikatet med [az vm secret format](/cli/azure/vm/secret#az_vm_secret_format). Följande exempel tilldelar kommandonas resultat till variabler, vilket gör dem enklare att använda i nästa steg:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Skapa en cloud-init-konfiguration för att skydda NGINX
[Cloud-init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

När du skapar en virtuella dator lagras certifikat och nycklar i den skyddade katalogen */var/lib/waagent/*. Om du vill automatisera tillägg av certifikat till den virtuella datorn och konfigurera webbservern använder du cloud-init. I det här exemplet installerar och konfigurerar du NGINX-webbservern. Du kan använda samma process för att installera och konfigurera Apache. 

Skapa en fil med namnet *cloud-init-web-server.txt* och klistra in följande konfiguration:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Skapa en säker virtuell dator
Skapa nu en virtuell dator med [az vm create](/cli/azure/vm). Certifikatdata matas in från Key Vault med parametern `--secrets`. Du skickar in cloud-init-konfigurationen med parametern `--custom-data`:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

Det tar några minuter innan den virtuella datorn skapas, paketen installeras och appen startar. När den virtuella datorn har skapats ska du anteckna `publicIpAddress` som visas av Azure CLI. Adressen används för att få åtkomst till din webbplats i en webbläsare.

För att låta säker webbtrafik nå din virtuella dator ska du öppna port 443 från Internet med [az vm open-port](/cli/azure/vm):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Testa den säkra webbappen
Nu kan du öppna en webbläsare och ange *https: \/ \/ \<publicIpAddress>* i adressfältet. Ange din offentliga IP-adress från skapandeprocessen av den virtuella datorn. Om du använder ett självsignerat certifikat ska du acceptera säkerhetsvarningen:

![Acceptera webbläsarens säkerhetsvarning](./media/tutorial-secure-web-server/browser-warning.png)

Din skyddade NGINX-webbplats visas sedan som i exemplet nedan:

![Visa skyddad NGINX-webbplats som körs](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien skyddade du en NGINX-webbserver med ett TLS/SSL-certifikat som lagras i Azure Key Vault. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure Key Vault
> * Generera eller ladda upp ett certifikat till Key Vault
> * Skapa en virtuell dator och installera NGINX-webbservern
> * Mata in certifikatet i den virtuella datorn och konfigurera NGINX med en TLS-bindning

Klicka på den här länken om du vill se inbyggda skriptexempel för virtuella datorer.

> [!div class="nextstepaction"]
> [Skriptexempel för virtuella Linux-datorer](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)