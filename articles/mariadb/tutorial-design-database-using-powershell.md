---
title: 'Självstudie: utforma en server – Azure PowerShell-Azure Database for MariaDB'
description: I den här självstudien beskrivs hur du skapar och hanterar Azure Database for MariaDB Server och databas med PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664509"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Självstudie: utforma en Azure Database for MariaDB med hjälp av PowerShell

Azure Database for MariaDB är en relationsdatabastjänst i Microsoft-molnet som är baserad på databasmotorn MariaDB Community Edition. I den här självstudien använder du PowerShell och andra verktyg för att lära dig att:

> [!div class="checklist"]
> - Skapa en Azure Database for MariaDB
> - Konfigurera serverbrandväggen
> - Använd [kommando rads verktyget MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) för att skapa en databas
> - Läsa in exempeldata
> - Söka i data
> - Uppdatera data
> - Återställa data

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. MariaDb är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När AZ. MariaDb PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om det här är första gången du använder tjänsten Azure Database for MariaDB måste du registrera Resource-providern för **Microsoft. DBforMariaDB** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
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

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server

Skapa en Azure Database for MariaDB-server med `New-AzMariaDbServer` cmdleten. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en MariaDB-server i regionen **västra USA** med namnet **mydemoserver** i resurs gruppen **myresourcegroup** med en server Administratörs inloggning för **administratören**. Det är en gen 5-Server i den allmänna pris nivån med 2 virtuella kärnor och geo-redundanta säkerhets kopieringar aktiverade. Dokumentera lösen ordet som används på den första raden i exemplet, eftersom det här är lösen ordet för MariaDB-serverns administratörs konto.

> [!TIP]
> Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Värdet för **SKU** -parametern följer konventions **pris-nivå \_ beräknings generationens \_ virtuella kärnor** som visas i följande exempel.

- `-Sku B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
- `-Sku GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
- `-Sku MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga **SKU** -värden per region och för nivåer finns i [Azure Database for MariaDB pris nivåer](./concepts-pricing-tiers.md).

Överväg att använda den grundläggande pris nivån om ljus Compute och I/O passar din arbets belastning.

> [!IMPORTANT]
> Servrar som skapats på den grundläggande pris nivån kan inte senare skalas till allmänt eller minnesoptimerade och kan inte vara geo-replikerade.

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Skapa Azure Database for MariaDB en brand Väggs regel på server nivå med hjälp av `New-AzMariaDbFirewallRule` cmdleten. En brand Väggs regel på server nivå gör det möjligt för ett externt program, till exempel `mysql` kommando rads verktyget eller MariaDB Workbench, att ansluta till servern via Azure Database for MariaDB tjänstens brand vägg.

I följande exempel skapas en brand Väggs regel med namnet **AllowMyIP** som tillåter anslutningar från en speciell IP-adress, 192.168.0.1. Ersätt en IP-adress eller ett intervall med IP-adresser som motsvarar den plats som du ansluter från.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database for MariaDB kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I det här scenariot kan du bara ansluta till servern om din IT-avdelning öppnar port 3306.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna. Använd följande exempel för att ta reda på anslutnings informationen. Anteckna värdena för **FullyQualifiedDomainName** och **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Anslut till servern med kommando rads verktyget mysql

Anslut till servern med hjälp av `mysql` kommando rads verktyget. Information om hur du hämtar och installerar kommando rads verktyget finns i [MariaDB community downloads](https://dev.mysql.com/downloads/shell/). Du kan också komma åt en förinstallerad version av `mysql` kommando rads verktyget i Azure Cloud Shell genom att välja knappen **prova** på ett kod exempel i den här artikeln. Andra sätt att komma åt Azure Cloud Shell är att välja knappen **>_** i det övre högra verktygsfältet i Azure Portal eller genom att besöka [Shell.Azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Skapa en databas

När du är ansluten till servern ska du skapa en tom databas.

```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen

Nu när du vet hur du ansluter till Azure Database for MariaDB-databasen kan du utföra några grundläggande uppgifter.

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data i tabellerna

Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna

Kör följande frågor för att hämta information från databastabellen.

```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern lämnas oförändrad. Om en tabell t. ex. har släppts av misstag kan du återställa till den tid då bara den här minskningen ägde rum. Sedan kan du hämta den saknade tabellen och data från den återställda kopian av servern.

Använd PowerShell-cmdleten för att återställa servern `Restore-AzMariaDbServer` .

### <a name="run-the-restore-command"></a>Kör kommandot Restore

Om du vill återställa servern kör du följande exempel från PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

När du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats-och pris nivå värden för den återställda servern förblir samma som den ursprungliga servern.

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen startades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Säkerhetskopiera och återställa en Azure Database for MariaDB-server med hjälp av PowerShell](howto-restore-server-powershell.md)