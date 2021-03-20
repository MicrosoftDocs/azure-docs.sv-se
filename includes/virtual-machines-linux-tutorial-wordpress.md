---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 76a080d40721fa78ad703f77f6dbe7a3363ab77e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68857503"
---
## <a name="install-wordpress"></a>Installera WordPress

Om du vill testa din stack, installerar du en exempelapp. Till exempel installerar följande steg [WordPress](https://wordpress.org/)-plattformen med öppen källkod för att skapa webbplatser och bloggar. Andra arbetsbelastningar du kan testa är [Drupal](http://www.drupal.org) och [Moodle](https://moodle.org/). 

Den här WordPress-installationen är endast avsedd för ”Proof of concept”. Om du vill installera senaste WordPress i produktion med rekommenderade säkerhetsinställningar läser du [WordPress-dokumentationen](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Installera WordPress-paketet

Kör följande kommando:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Konfigurera WordPress

Konfigurera WordPress att använda MySQL och PHP.

Skapa en textfil `wordpress.sql` i en arbetskatalog för att konfigurera MySQL-databasen för WordPress: 

```bash
sudo sensible-editor wordpress.sql
```

Lägg till följande kommandon för att ersätta ett valfritt databaslösenord mot *yourPassword* (lämna alla andra värden oförändrade). Om du tidigare har konfigurerat en MySQL-säkerhetsprincip för att verifiera lösenordsstyrkan ser du till att lösenordet uppfyller kraven på styrka. Spara filen.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
```

Kör följande kommando för att skapa databasen:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Eftersom filen `wordpress.sql` innehåller databasautentiseringsuppgifter tar du bort den efter användning:

```bash
sudo rm wordpress.sql
```

Om du vill konfigurera PHP kör du följande kommando för att öppna valfri textredigerare och skapa filen `/etc/wordpress/config-localhost.php`:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Kopiera följande rader i filen för att ersätta ditt WordPress-databaslösenord mot *yourPassword* (lämna alla andra värden oförändrade). Spara sedan filen.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Flytta WordPress-installationen till webbserverns dokumentrot:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Nu kan du slutföra WordPress-installationen och publicera på plattformen. Öppna en webbläsare och navigera till `http://yourPublicIPAddress/wordpress`. Ersätt den offentliga IP-adressen för den virtuella datorn. Det bör se ut ungefär som den här bilden:

![Installationssidan för WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)
