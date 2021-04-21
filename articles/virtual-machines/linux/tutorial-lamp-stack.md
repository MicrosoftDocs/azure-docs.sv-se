---
title: Självstudie – Distribuera LAMP och WordPress på en virtuell dator
description: I den här självstudien får du lära dig hur du installerar LAMP-stacken och WordPress på en virtuell Linux-dator i Azure.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816358"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Självstudie: Installera en LAMP-stack på en virtuell Linux-dator i Azure

I den här artikeln får du veta hur du distribuerar en Apache-webbserver, MySQL och PHP (LAMP-stacken) på en virtuell Ubuntu-dator i Azure. Om du vill se LAMP-servern i praktiken kan du installera och konfigurera en WordPress-webbplats. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator 
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress 

Den här installationen är avsedd för snabbtester och konceptbevis. Mer information om LAMP-stacken samt rekommendationer kring produktionsmiljön finns i [dokumentationen om Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

Den här självstudien använder CLI [i Azure Cloud Shell](../../cloud-shell/overview.md), som ständigt uppdateras till den senaste versionen. Om du vill Cloud Shell väljer **du Testa** längst upp i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm). 

Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`. Kommandot anger även *azureuser* som ett administratörsanvändarnamn. Du använder det här namnet senare för att ansluta till den virtuella datorn. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för att komma åt den virtuella datorn i senare steg.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts enbart SSH-anslutningar till virtuella Linux-datorer distribuerade i Azure. Eftersom den här virtuella datorn kommer att vara en webbserver behöver du öppna port 80 från Internet. Använd kommandot [az vm open-port](/cli/azure/vm) för att öppna önskad port.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Mer information om hur du öppnar portar till den virtuella datorn finns i [Öppna portar.](nsg-quickstart.md)

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Om du inte känner till den offentliga IP-adressen för den virtuella datorn kör du kommandot [az network public-ip list](/cli/azure/network/public-ip). Du behöver den här IP-adressen för flera senare steg.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt med den korrekta offentliga IP-adressen för den virtuella datorn. I det här exemplet är IP-adressen *40.68.254.142*. *azureuser* är det administratörsanvändarnamn som angavs när du skapade den virtuella datorn.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Installera Apache, MySQL och PHP

Kör följande kommando för att uppdatera Ubuntu-paketkällorna och installera Apache, MySQL och PHP. Observera textmarkören (^) i slutet av kommandot, som är en del av paketnamnet `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Du uppmanas att installera paketen och andra beroenden. Den här processen installerar lägsta nödvändiga PHP-tillägg för användning av PHP med MySQL.  

## <a name="verify-apache"></a>Verifiera Apache

Kontrollera versionen av Apache med följande kommando:
```bash
apache2 -v
```

När Apache är installerat och port 80 är öppen för den virtuella datorn kan webbservern nås från internet. När du vill visa standardsidan för Apache2 Ubuntu öppnar du en webbläsare och anger den virtuella datorns offentliga IP-adress. Använd den offentliga IP-adressen som du använde för SSH till den virtuella datorn:

![Apache-standardsida][3]


## <a name="verify-and-secure-mysql"></a>Verifiera och skydda MySQL

Kontrollera versionen av MySQL med följande kommando (observera versal i parameter `V`):

```bash
mysql -V
```

Om du vill skydda installationen av MySQL, inklusive att ange ett rotlösenord, kör du skriptet `mysql_secure_installation`. 

```bash
sudo mysql_secure_installation
```

Om du vill kan du även konfigurera plugin-programmet Validate Password (Verifiera lösenord; rekommenderas). Sedan anger du ett lösenord för MySQL-rotanvändaren och konfigurerar återstående säkerhetsinställningar för miljön. Vi rekommenderar att du svarar ”Y” (Ja) på alla frågor.

Om du vill prova MySQL-funktioner (skapa en MySQL-databas, lägga till användare eller ändra konfigurationsinställningar) loggar du in på MySQL. Det här steget krävs inte för att genomföra kursen.

```bash
sudo mysql -u root -p
```

När du är klar lämnar du mysql-frågan genom att skriva `\q`.

## <a name="verify-php"></a>Verifiera PHP

Kontrollera versionen av PHP med följande kommando:

```bash
php -v
```

Om du vill testa ytterligare skapar du en snabb PHP-informationssida som visas i en webbläsare. Följande kommando skapar PHP-informationssidan:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Nu kan du kontrollera PHP-informationssidan som du har skapat. Öppna en webbläsare och navigera till `http://yourPublicIPAddress/info.php`. Ersätt den offentliga IP-adressen för den virtuella datorn. Det bör se ut ungefär som den här bilden:

![PHP-informationssida][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Nästa steg

Under den här kursen distribuerade du en LAMP-server i Azure. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LAMP-servern

Gå vidare till nästa självstudie om du vill lära dig hur du skyddar webbservrar med TLS/SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webbserver med TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
