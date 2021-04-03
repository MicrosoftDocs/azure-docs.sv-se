---
title: 'Snabb start: skapa en server-Azure PowerShell-Azure Database for MySQL'
description: I den här snabb starten beskrivs hur du använder PowerShell för att skapa en Azure Database for MySQL-server i en Azure-resurs grupp.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d12d447acb3b6bf2b6f84e9768e9f063a9a36b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542328"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Snabb start: skapa en Azure Database for MySQL-server med PowerShell

I den här snabb starten beskrivs hur du använder PowerShell för att skapa en Azure Database for MySQL-server i en Azure-resurs grupp. Du kan använda PowerShell för att skapa och hantera Azure-resurser interaktivt eller i skript.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om modulen AZ. MySql PowerShell är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MySql -AllowPrerelease` .
> När AZ. MySql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-moduler och är tillgängliga internt från Azure Cloud Shell.

Om det här är första gången du använder tjänsten Azure Database for MySQL måste du registrera Resource-providern för **Microsoft. DBforMySQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj ett angivet prenumerations-ID med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp med namnet **myresourcegroup** i regionen **USA, västra** .

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server

Skapa en Azure Database for MySQL-server med `New-AzMySqlServer` cmdleten. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

Följande tabell innehåller en lista över parametrar och exempel värden som används ofta för `New-AzMySqlServer` cmdleten.

|        **Inställning**         | **Exempelvärde** |                                                                                                                                                             **Beskrivning**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Välj ett globalt unikt namn i Azure som identifierar din Azure Database for MySQL-server. Server namnet får bara innehålla bokstäver, siffror och bindestreck (-). Alla versaler som anges konverteras automatiskt till gemener under skapande processen. Det måste innehålla mellan 3 och 63 tecken. |
| ResourceGroupName          | myresourcegroup  | Ange namnet på Azure-resursgruppen.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | Namnet på SKU:n. Följer konventions **pris – nivå för \_ beräknings skapande \_ virtuella kärnor** i korthet. Mer information om SKU-parametern finns i informationen som följer den här tabellen.                                                                                                                                           |
| BackupRetentionDay         | 7                | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervallet är 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled          | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte. Det går inte att aktivera det här värdet för servrar i den grundläggande pris nivån och det kan inte ändras efter att servern har skapats. Tillåtna värden: Enabled, Disabled.                                                                                                      |
| Location                   | westus           | Azure-regionen för-servern.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled          | Om SSL ska vara aktiverat eller inte för den här servern. Tillåtna värden: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Serverns lagringskapacitet (enheten är megabyte). Giltig StorageInMb är minst 5120 MB och ökar i steg om 1024 MB. Mer information om lagrings storleks gränser finns i [Azure Database for MySQL pris nivåer](./concepts-pricing-tiers.md).                                                                               |
| Version                    | 5.7              | Huvudversion för MySQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Lösen ordet för administratörs användaren i form av en säker sträng. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.                                       |

Värdet för **SKU** -parametern följer konventions **pris-nivå \_ beräknings generationens \_ virtuella kärnor** som visas i följande exempel.

- `-Sku B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
- `-Sku GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
- `-Sku MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga **SKU** -värden per region och för nivåer finns i [Azure Database for MySQL pris nivåer](./concepts-pricing-tiers.md).

I följande exempel skapas en MySQL-server i regionen **västra USA** med namnet **mydemoserver** i resurs gruppen **myresourcegroup** med en server Administratörs inloggning för **administratören**. Det är en gen 5-Server i den allmänna pris nivån med 2 virtuella kärnor och geo-redundanta säkerhets kopieringar aktiverade. Dokumentera lösen ordet som används på den första raden i exemplet, eftersom det här är lösen ordet för MySQL-serverns administratörs konto.

> [!TIP]
> Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Överväg att använda den grundläggande pris nivån om ljus Compute och I/O passar din arbets belastning.

> [!IMPORTANT]
> Servrar som skapats på den grundläggande pris nivån kan inte senare skalas till allmänt eller minnesoptimerade och kan inte vara geo-replikerade.

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Skapa Azure Database for MySQL en brand Väggs regel på server nivå med hjälp av `New-AzMySqlFirewallRule` cmdleten. En brand Väggs regel på server nivå gör att ett externt program, till exempel `mysql` kommando rads verktyget eller MySQL Workbench, kan ansluta till servern via Azure Database for MySQL tjänstens brand vägg.

I följande exempel skapas en brand Väggs regel med namnet **AllowMyIP** som tillåter anslutningar från en speciell IP-adress, 192.168.0.1. Ersätt en IP-adress eller ett intervall med IP-adresser som motsvarar den plats som du ansluter från.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I det här scenariot kan du bara ansluta till servern om din IT-avdelning öppnar port 3306.

## <a name="configure-ssl-settings"></a>Konfigurera SSL-inställningar

Som standard verkställs SSL-anslutningar mellan servern och dina klientprogram. Det här standardvärdet säkerställer säkerheten för _rörelse_ data genom att kryptera data strömmen via Internet. För den här snabbstarten inaktiverar du SSL-anslutningar för din server. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database för MySQL](./howto-configure-ssl.md) för mer information.

> [!WARNING]
> Avaktivering av SSL rekommenderas inte för produktionsservrar.

I följande exempel inaktive ras SSL på din Azure Database for MySQL-server.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna. Använd följande exempel för att ta reda på anslutnings informationen. Anteckna värdena för **FullyQualifiedDomainName** och **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Anslut till servern med kommando rads verktyget mysql

Anslut till servern med hjälp av `mysql` kommando rads verktyget. Information om hur du hämtar och installerar kommando rads verktyget finns i [MySQL community downloads](https://dev.mysql.com/downloads/shell/). Du kan också komma åt en förinstallerad version av `mysql` kommando rads verktyget i Azure Cloud Shell genom att välja knappen **prova** på ett kod exempel i den här artikeln. Andra sätt att komma åt Azure Cloud Shell är att välja knappen **>_** i det övre högra verktygsfältet i Azure Portal eller genom att besöka [Shell.Azure.com](https://shell.azure.com/).

1. Anslut till servern med hjälp av `mysql` kommando rads verktyget.

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Visa Server status.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Fler kommandon finns i [referenshandboken för MySQL 5.7 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mysql-workbench"></a>Ansluta till servern med MySQL Workbench

1. Starta programmet MySQL Workbench på klientdatorn. Information om hur du hämtar och installerar MySQL Workbench finns i [Hämta MySQL Workbench](https://dev.mysql.com/downloads/workbench/).

1. I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png" alt-text="konfigurera ny anslutning":::

    |    **Inställning**    |           **Föreslaget värde**           |                      **Beskrivning**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Anslutningsnamn   | Min anslutning                           | Ange en etikett för den här anslutningen                        |
    | Anslutningsmetod | Standard (TCP/IP)                       | Använd TCP/IP-protokoll för att ansluta till Azure Database for MySQL |
    | Värdnamn          | `mydemoserver.mysql.database.azure.com` | Server namn som du antecknade tidigare                           |
    | Port              | 3306                                    | Standard porten för MySQL                                 |
    | Användarnamn          | myadmin@mydemoserver                    | Den inloggning för Server administratör som du antecknade tidigare                |
    | Lösenord          | *************                           | Använd administratörs kontots lösen ord som du konfigurerade tidigare      |

1. Om du vill testa om parametrarna är korrekt konfigurerade klickar du på knappen **Testa anslutning** .

1. Välj anslutningen för att ansluta till servern.

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här snabb starten inte behövs för en annan snabb start eller självstudier kan du ta bort dem genom att köra följande exempel.

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här snabb starten finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Om du bara vill ta bort den server som skapats i den här snabb starten utan att ta bort resurs gruppen använder du `Remove-AzMySqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en Azure Database for MySQL med hjälp av PowerShell](tutorial-design-database-using-powershell.md)