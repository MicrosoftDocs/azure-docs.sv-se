---
title: 'Självstudie: utforma en Azure Database for PostgreSQL-enskild server – Azure PowerShell'
description: Den här självstudien visar hur du skapar, konfigurerar och frågar din första Azure Database for PostgreSQL-enskilda server med Azure PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 84b52ec43dc5fec4671459720af1b218b7f176d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605851"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-powershell"></a>Självstudie: utforma en Azure Database for PostgreSQL-enskild server med PowerShell

Azure Database for PostgreSQL är en Relations databas tjänst i Microsoft-molnet baserat på PostgreSQL Community Edition-databasmotor. I den här självstudien använder du PowerShell och andra verktyg för att lära dig att:

> [!div class="checklist"]
> - Skapa en Azure Database för PostgreSQL
> - Konfigurera serverbrandväggen
> - Använd [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget för att skapa en databas
> - Läsa in exempeldata
> - Söka i data
> - Uppdatera data
> - Återställa data

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. PostgreSql är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> När AZ. PostgreSql PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om det här är första gången du använder tjänsten Azure Database for PostgreSQL måste du registrera Resource-providern för **Microsoft. DBforPostgreSQL** .

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

Skapa en Azure Database for PostgreSQL-server med `New-AzPostgreSqlServer` cmdleten. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en PostgreSQL-server i regionen **västra USA** med namnet **mydemoserver** i resurs gruppen **myresourcegroup** med en server Administratörs inloggning för **administratören**. Det är en gen 5-Server i den allmänna pris nivån med 2 virtuella kärnor och geo-redundanta säkerhets kopieringar aktiverade. Dokumentera lösen ordet som används på den första raden i exemplet, eftersom det här är lösen ordet för PostgreSQL-serverns administratörs konto.

> [!TIP]
> Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Värdet för **SKU** -parametern följer konventions **pris-nivå \_ beräknings generationens \_ virtuella kärnor** som visas i följande exempel.

- `-Sku B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
- `-Sku GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
- `-Sku MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga **SKU** -värden per region och för nivåer finns i [Azure Database for PostgreSQL pris nivåer](./concepts-pricing-tiers.md).

Överväg att använda den grundläggande pris nivån om ljus Compute och I/O passar din arbets belastning.

> [!IMPORTANT]
> Servrar som skapats på den grundläggande pris nivån kan inte senare skalas till allmänt eller minnesoptimerade och kan inte vara geo-replikerade.

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Skapa Azure Database for PostgreSQL en brand Väggs regel på server nivå med hjälp av `New-AzPostgreSqlFirewallRule` cmdleten. En brand Väggs regel på server nivå gör det möjligt för ett externt program, till exempel `psql` kommando rads verktyget eller postgresql Workbench, att ansluta till servern via Azure Database for PostgreSQL tjänstens brand vägg.

I följande exempel skapas en brand Väggs regel med namnet **AllowMyIP** som tillåter anslutningar från en speciell IP-adress, 192.168.0.1. Ersätt en IP-adress eller ett intervall med IP-adresser som motsvarar den plats som du ansluter från.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database for PostgreSQL kommunicera via port 5432. Om du försöker ansluta inifrån ett företags nätverk kanske utgående trafik via port 5432 inte tillåts. I det här scenariot kan du bara ansluta till servern om din IT-avdelning öppnar port 5432.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna. Använd följande exempel för att ta reda på anslutnings informationen. Anteckna värdena för **FullyQualifiedDomainName** och **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgresql.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Du kan också komma åt en förinstallerad version av `psql` kommando rads verktyget i Azure Cloud Shell genom att välja knappen **prova** på ett kod exempel i den här artikeln. Andra sätt att komma åt Azure Cloud Shell är att välja knappen **>_** i det övre högra verktygsfältet i Azure Portal eller genom att besöka [Shell.Azure.com](https://shell.azure.com/).

1. Anslut till din Azure PostgreSQL-server med hjälp av `psql` kommando rads verktyget.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Till exempel ansluter följande kommando till standard databasen som heter **postgres** på postgresql-servern `mydemoserver.postgres.database.azure.com` med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Om du föredrar att använda en URL-sökväg för att ansluta till postgres, kodar URL @-tecknet i användar namnet med `%40` . Anslutnings strängen för psql skulle exempelvis vara, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. När du är ansluten till servern, skapar du en blank databas i prompten.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:

   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen

Nu när du vet hur du ansluter till Azure Database for PostgreSQL databasen, slutför några grundläggande uppgifter.

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

Använd PowerShell-cmdleten för att återställa servern `Restore-AzPostgreSqlServer` .

### <a name="run-the-restore-command"></a>Kör kommandot Restore

Om du vill återställa servern kör du följande exempel från PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

När du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats-och pris nivå värden för den återställda servern förblir samma som den ursprungliga servern.

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen startades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Säkerhetskopiera och återställa en Azure Database for PostgreSQL-server med hjälp av PowerShell](howto-restore-server-powershell.md)
