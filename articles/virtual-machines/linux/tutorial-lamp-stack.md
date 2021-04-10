---
title: Självstudie – distribuera LAMPAn på en virtuell Linux-dator i Azure
description: I den här självstudiekursen lär du dig hur du installerar LAMP-stacken på en virtuell Linux-dator i Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.collection: linux
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: cynthn
ms.openlocfilehash: 3813931f47c110abcfb595065c1415ca9ed84c9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564721"
---
# <a name="tutorial-install-a-lamp-web-server-on-a-linux-virtual-machine-in-azure"></a>Självstudier: Installera en LAMP-webbserver på en virtuell Linux-dator i Azure

I den här artikeln får du veta hur du distribuerar en Apache-webbserver, MySQL och PHP (LAMP-stacken) på en virtuell Ubuntu-dator i Azure. Om du vill se LAMP-servern i praktiken kan du installera och konfigurera en WordPress-webbplats. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en virtuell Ubuntu-dator (L:et i LAMP-stacken)
> * Öppna port 80 för webbtrafik
> * Installera Apache, MySQL och PHP
> * Verifiera installation och konfiguration
> * Installera WordPress på LAMP-servern

Den här installationen är avsedd för snabbtester och konceptbevis. Mer information om LAMP-stacken samt rekommendationer kring produktionsmiljön finns i [dokumentationen om Ubuntu](https://help.ubuntu.com/community/ApacheMySQLPHP).

I den här självstudien används CLI i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-linux-tutorial-stack-intro.md](../../../includes/virtual-machines-linux-tutorial-stack-intro.md)]

## <a name="install-apache-mysql-and-php"></a>Installera Apache, MySQL och PHP

Kör följande kommando för att uppdatera Ubuntu-paketkällorna och installera Apache, MySQL och PHP. Observera textmarkören (^) i slutet av kommandot, som är en del av paketnamnet `lamp-server^`. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Du uppmanas att installera paketen och andra beroenden. Den här processen installerar lägsta nödvändiga PHP-tillägg för användning av PHP med MySQL.  

## <a name="verify-installation-and-configuration"></a>Verifiera installation och konfiguration


### <a name="verify-apache"></a>Verifiera Apache

Kontrollera versionen av Apache med följande kommando:
```bash
apache2 -v
```

När Apache är installerat och port 80 är öppen för den virtuella datorn kan webbservern nås från internet. När du vill visa standardsidan för Apache2 Ubuntu öppnar du en webbläsare och anger den virtuella datorns offentliga IP-adress. Använd den offentliga IP-adressen som du använde för SSH till den virtuella datorn:

![Apache-standardsida][3]


### <a name="verify-and-secure-mysql"></a>Verifiera och skydda MySQL

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

### <a name="verify-php"></a>Verifiera PHP

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

Gå vidare till nästa självstudie och lär dig hur du skyddar webb servrar med TLS/SSL-certifikat.

> [!div class="nextstepaction"]
> [Säker webb server med TLS](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
