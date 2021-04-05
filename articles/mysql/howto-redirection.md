---
title: Anslut med omdirigering – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan konfigurera programmet för att ansluta till Azure Database for MySQL med omdirigering.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 199810f835924ccbb51948169244622a19c5e8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201441"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Anslut till Azure Database for MySQL med omdirigering

I det här avsnittet beskrivs hur du ansluter ett program till din Azure Database for MySQL-server med omdirigeringsläge. Omdirigering syftar till att minska nätverks fördröjningen mellan klient program och MySQL-servrar genom att tillåta att program ansluter direkt till backend-serverns noder.

## <a name="before-you-begin"></a>Innan du börjar
Logga in på [Azure-portalen](https://portal.azure.com). Skapa en Azure Database for MySQL-server med motor version 5,6, 5,7 eller 8,0. 

Mer information finns i så här skapar du en Azure Database for MySQL-server med hjälp av [Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) eller [Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT]
> Omdirigering stöds för närvarande inte med en [privat länk för Azure Database for MySQL](concepts-data-access-security-private-link.md).

## <a name="enable-redirection"></a>Aktivera omdirigering

På din Azure Database for MySQL-server konfigurerar du `redirect_enabled` parametern till `ON` att tillåta anslutningar med omdirigerings läge. Använd [Azure Portal](howto-server-parameters.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md)för att uppdatera denna server parameter.

## <a name="php"></a>PHP

Stöd för omdirigering i PHP-program är tillgängligt via [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) tillägget som utvecklats av Microsoft. 

Mysqlnd_azure-tillägget är tillgängligt för att läggas till i PHP-program via PECL och det är starkt rekommenderat att installera och konfigurera tillägget via det officiellt publicerade [PECL-paketet](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Stöd för omdirigering i PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -tillägget är för närvarande en för hands version.

### <a name="redirection-logic"></a>Logik för omdirigering

>[!IMPORTANT]
> Den omdirigerings logik/beteende som börjar version 1.1.0 uppdaterades och **vi rekommenderar att du använder version 1.1.0 +**.

Omdirigerings beteendet bestäms av värdet för `mysqlnd_azure.enableRedirect` . I tabellen nedan beskrivs beteendet för omdirigering baserat på värdet för den här parametern som börjar i **version 1.1.0 +**.

Om du använder en äldre version av mysqlnd_azure-tillägget (version 1.0.0-1.0.3) bestäms omdirigerings beteendet av värdet för `mysqlnd_azure.enabled` . De giltiga värdena är `off` (fungerar på samma sätt som de som beskrivs i tabellen nedan) och `on` (fungerar som `preferred` i tabellen nedan).  

|**värde för mysqlnd_azure. enableRedirect**| **Beteende**|
|----------------------------------------|-------------|
|`off` eller `0`|Omdirigering kommer inte att användas. |
|`on` eller `1`|– Om anslutningen inte använder SSL på driv rutins sidan görs ingen anslutning. Följande fel returneras: *"mysqlnd_azure. enableRedirect är aktiverat, men SSL-alternativet har inte angetts i anslutnings strängen. Omdirigering är bara möjlig med SSL. "*<br>-Om SSL används på driv rutins sidan, men omdirigering inte stöds på servern, avbryts den första anslutningen och följande fel returneras: *"anslutningen avbröts eftersom omdirigering inte har Aktiver ATS på MySQL-servern eller om nätverks paketet inte uppfyller omdirigerings protokollet."*<br>– Om MySQL-servern har stöd för omdirigering, men den omdirigerade anslutningen misslyckades av någon anledning, avbryts även den första proxyservern. Returnera felet för den omdirigerade anslutningen.|
|`preferred` eller `2`<br> (standardvärde)|-mysqlnd_azure kommer att använda omdirigering om möjligt.<br>– Om anslutningen inte använder SSL på driv rutins sidan har servern inte stöd för omdirigering, eller så kan den omdirigerade anslutningen inte ansluta till en icke-kritisk orsak, medan proxyanslutningar fortfarande är en giltig, den kommer att återgå till den första proxyservern.|

I följande avsnitt i dokumentet får du en översikt över hur du installerar `mysqlnd_azure` tillägget med PECL och anger värdet för den här parametern.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Förutsättningar 
- PHP-versioner 7.2.15 + och 7.3.2 +
- PHP PÄRON 
- php – mysql
- Azure Database for MySQL Server

1. Installera [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) med [PECL](https://pecl.php.net/package/mysqlnd_azure). Vi rekommenderar att du använder version 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Leta upp tilläggs katalogen ( `extension_dir` ) genom att köra följande:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Ändra kataloger till den returnerade mappen och se till att den finns `mysqlnd_azure.so` i den här mappen. 

4. Leta upp mappen för INI-filer genom att köra följande: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Ändra kataloger till den här returnerade mappen. 

6. Skapa en ny ini-fil för `mysqlnd_azure` . Se till att bokstavs ordningen för namnet är efter det att mysqnld, eftersom modulerna har lästs in enligt namn ordningen för ini-filerna. Om `mysqlnd` . ini till exempel heter `10-mysqlnd.ini` , namnger du mysqlnd-ini som `20-mysqlnd-azure.ini` .

7. I den nya ini-filen lägger du till följande rader för att aktivera omdirigering.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Förutsättningar 
- PHP-versioner 7.2.15 + och 7.3.2 +
- php – mysql
- Azure Database for MySQL Server

1. Ta reda på om du kör en x64-eller x86-version av PHP genom att köra följande kommando:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Hämta motsvarande x64-eller x86-version av [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -DLL-filen från [PECL](https://pecl.php.net/package/mysqlnd_azure) som matchar din version av php. Vi rekommenderar att du använder version 1.1.0 +.

3. Extrahera zip-filen och hitta DLL-filen med namnet `php_mysqlnd_azure.dll` .

4. Leta upp tilläggs katalogen ( `extension_dir` ) genom att köra nedanstående kommando:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopiera `php_mysqlnd_azure.dll` filen till den katalog som returnerades i steg 4. 

6. Leta upp PHP-mappen som innehåller `php.ini` filen med hjälp av följande kommando:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Ändra `php.ini` filen och Lägg till följande extra rader för att aktivera omdirigering. 

    Under avsnittet dynamiska tillägg: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Under avsnittet Modulnamn:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Bekräfta omdirigering

Du kan också bekräfta att omdirigering har kon figurer ATS med nedanstående exempel-PHP-kod. Skapa en PHP-fil med namnet `mysqlConnect.php` och klistra in nedanstående kod. Uppdatera Server namnet, användar namnet och lösen ordet med ditt eget. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Nästa steg
Mer information om anslutnings strängar finns i [anslutnings strängar](howto-connection-string.md).
