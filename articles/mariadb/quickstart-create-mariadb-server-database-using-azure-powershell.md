---
title: 'Snabbstart: Skapa en server – Azure PowerShell – Azure Database for MariaDB'
description: Den här snabbstarten beskriver hur du använder PowerShell för att skapa Azure Database for MariaDB server i en Azure-resursgrupp.
author: savjani
ms.author: pariks
ms.date: 05/26/2020
ms.topic: quickstart
ms.service: mariadb
ms.devlang: azurepowershell
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 7bfbd223cb337ff7d908032c769afbbf5cafa38f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531391"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Snabbstart: Skapa en Azure Database for MariaDB server med PowerShell

Den här snabbstarten beskriver hur du använder PowerShell för att skapa Azure Database for MariaDB server i en Azure-resursgrupp. Du kan använda PowerShell för att skapa och hantera Azure-resurser interaktivt eller i skript.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> När PowerShell-modulen Az.MariaDb är i förhandsversion måste du installera den separat från Az PowerShell-modulen med följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När PowerShell-modulen Az.MariaDb är allmänt tillgänglig blir den en del av framtida versioner av Az PowerShell-modulen och blir tillgänglig inifrån Azure Cloud Shell.

Om det här är första gången du använder Azure Database for MariaDB-tjänsten måste du registrera resursprovidern **Microsoft.DBforOrderaDB.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska debiteras. Välj ett specifikt prenumerations-ID med hjälp av cmdleten [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med [hjälp av cmdleten New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet **myresourcegroup** i regionen **USA,** västra.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server

Skapa en Azure Database for MariaDB-server med `New-AzMariaDbServer` cmdleten . En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

Följande tabell innehåller en lista över vanliga parametrar och exempelvärden för `New-AzMariaDbServer` cmdleten .

|        **Inställning**         | **Exempelvärde** |                                                                                                                                                             **Beskrivning**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Välj ett globalt unikt namn i Azure som identifierar din Azure Database for MariaDB server. Servernamnet får bara innehålla bokstäver, siffror och bindestreck (-). Versaler som anges konverteras automatiskt till gemener under skapandeprocessen. Det måste innehålla mellan 3 och 63 tecken. |
| ResourceGroupName          | myresourcegroup  | Ange namnet på Azure-resursgruppen.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | Namnet på SKU:n. Följer **konventionens prisnivå för \_ beräkningsgenerering \_ av virtuella kärnor** i korthet. Mer information om SKU-parametern finns i informationen efter den här tabellen.                                                                                                                                           |
| BackupRetentionDay         | 7                | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervallet är 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled          | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte. Det här värdet kan inte aktiveras för servrar på den grundläggande prisnivån och kan inte ändras när servern har skapats. Tillåtna värden: Enabled, Disabled.                                                                                                      |
| Location                   | westus           | Azure-regionen för servern.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled          | Om SSL ska vara aktiverat eller inte för den här servern. Tillåtna värden: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Serverns lagringskapacitet (enheten är megabyte). Giltig StorageInMb är minst 5 120 MB och ökar i steg om 1 024 MB. Mer information om storleksgränser för lagring [finns Azure Database for MariaDB olika prisnivåer.](./concepts-pricing-tiers.md)                                                                               |
| Version                    | 5.7              | MariaDB-huvudversionen.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Lösenordet för administratörsanvändaren i form av en säker sträng. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.                                       |

Parametervärdet **för SKU** följer **konventionens prisnivå för \_ \_ beräkningsgenerering av virtuella kärnor** som visas i följande exempel.

- `-Sku B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
- `-Sku GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
- `-Sku MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga **SKU-värden** per region och för nivåer finns [Azure Database for MariaDB prisnivåer](./concepts-pricing-tiers.md).

I följande exempel skapas en  MariaDB-server i regionen USA, västra med namnet **mydemoserver** i resursgruppen **myresourcegroup** med serveradministratörsinloggningen **myadmin**. Det är en Gen 5-server på prisnivån generell användning med 2 virtuella kärnor och geo-redundanta säkerhetskopieringar aktiverade. Dokumentera lösenordet som används på den första raden i exemplet eftersom det är lösenordet för MariaDB-serverns administratörskonto.

> [!TIP]
> Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Överväg att använda den grundläggande prisnivån om lätt beräkning och I/O är tillräckliga för din arbetsbelastning.

> [!IMPORTANT]
> Servrar som skapas på den grundläggande prisnivån kan inte senare skalas till generell användning eller minnesoptimerad och kan inte geo-replikeras.

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Skapa en Azure Database for MariaDB brandväggsregel på servernivå med `New-AzMariaDbFirewallRule` hjälp av cmdleten . Med en brandväggsregel på servernivå kan ett externt program, till exempel kommandoradsverktyget eller MariaDB Workbench, ansluta till servern `mysql` via Azure Database for MariaDB-tjänstens brandvägg.

I följande exempel skapas en brandväggsregel med namnet **AllowMyIP** som tillåter anslutningar från en specifik IP-adress, 192.168.0.1. Ersätt en IP-adress eller ett intervall med IP-adresser som motsvarar den plats som du ansluter från.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database for MariaDB kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I det här scenariot kan du bara ansluta till servern om IT-avdelningen öppnar port 3306.

## <a name="configure-ssl-settings"></a>Konfigurera SSL-inställningar

Som standard verkställs SSL-anslutningar mellan servern och dina klientprogram. Den här standardinställningen garanterar _säkerheten för rörelsedata_ genom att dataströmmen krypteras via Internet. För den här snabbstarten inaktiverar du SSL-anslutningar för din server. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database for MariaDB](./howto-configure-ssl.md) för mer information.

> [!WARNING]
> Avaktivering av SSL rekommenderas inte för produktionsservrar.

I följande exempel inaktiveras SSL på din Azure Database for MariaDB server.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna. Använd följande exempel för att fastställa anslutningsinformationen. Anteckna värdena för **FullyQualifiedDomainName och** **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Ansluta till servern med mysql-kommandoradsverktyget

Anslut till servern med `mysql` hjälp av kommandoradsverktyget. Information om hur du laddar ned och installerar kommandoradsverktyget finns [i MySQL Community Downloads](https://dev.mysql.com/downloads/shell/). Du kan också komma åt en förinstallerad version av kommandoradsverktyget i Azure Cloud Shell genom att välja knappen Prova i ett `mysql` kodexempel i den här artikeln.  Andra sätt att komma Azure Cloud Shell är att **välja knappen>_** i det övre högra verktygsfältet i Azure Portal eller genom att gå [shell.azure.com](https://shell.azure.com/).

1. Anslut till servern med `mysql` hjälp av kommandoradsverktyget.

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Visa serverstatus.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

Fler kommandon finns i [referenshandboken för MySQL 5.7 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mariadb-workbench"></a>Ansluta till servern med MariaDB Workbench

1. Starta programmet MySQL Workbench på klientdatorn. Information om hur du laddar ned och installerar MySQL Workbench finns [i Ladda ned MySQL Workbench.](https://dev.mysql.com/downloads/workbench/)

1. I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

   ![konfigurera ny anslutning](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Inställning**    |            **Föreslaget värde**            |                      **Beskrivning**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Anslutningsnamn   | Min anslutning                             | Ange en etikett för den här anslutningen                        |
    | Anslutningsmetod | Standard (TCP/IP)                         | Använda TCP/IP-protokollet för att ansluta till Azure Database for MariaDB |
    | Värdnamn          | `mydemoserver.mariadb.database.azure.com` | Servernamn som du antecknade tidigare                           |
    | Port              | 3306                                      | Standardporten för MariaDB                                 |
    | Användarnamn          | myadmin@mydemoserver                      | Serveradministratörsinloggningen som du antecknade tidigare                |
    | Lösenord          | *************                             | Använd lösenordet för administratörskontot som du konfigurerade tidigare      |

1. Om du vill testa om parametrarna är korrekt konfigurerade klickar du på **knappen Testa** anslutning.

1. Välj anslutningen för att ansluta till servern.

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapades i den här snabbstarten inte behövs för en annan snabbstart eller självstudie kan du ta bort dem genom att köra följande exempel.

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här snabbstarten i den angivna resursgruppen tas de också bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Om du bara vill ta bort servern som skapades i den här snabbstarten utan att ta bort resursgruppen använder `Remove-AzMariaDbServer` du cmdleten .

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en Azure Database for MariaDB med PowerShell](tutorial-design-database-using-powershell.md)
