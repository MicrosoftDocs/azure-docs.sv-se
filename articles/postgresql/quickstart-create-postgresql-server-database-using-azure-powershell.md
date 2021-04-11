---
title: 'Snabb start: skapa server-Azure PowerShell-Azure Database for PostgreSQL-enskild server'
description: Snabb starts guide för att skapa en Azure Database for PostgreSQL-enskild server med Azure PowerShell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 79a070dbd49ed6ee00dd790c8157057aebde30df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606748"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Snabb start: skapa en Azure Database for PostgreSQL-enskild server med PowerShell

I den här snabb starten beskrivs hur du använder PowerShell för att skapa en Azure Database for PostgreSQL-server i en Azure-resurs grupp. Du kan använda PowerShell för att skapa och hantera Azure-resurser interaktivt eller i skript.

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

Följande tabell innehåller en lista över parametrar och exempel värden som används ofta för `New-AzPostgreSqlServer` cmdleten.

|        **Inställning**         | **Exempelvärde** |                                                                                                                                                             **Beskrivning**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Välj ett globalt unikt namn i Azure som identifierar din Azure Database for PostgreSQL-Server. Server namnet får bara innehålla bokstäver, siffror och bindestreck (-). Alla versaler som anges konverteras automatiskt till gemener under skapande processen. Det måste innehålla mellan 3 och 63 tecken. |
| ResourceGroupName          | myresourcegroup  | Ange namnet på Azure-resursgruppen.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | Namnet på SKU:n. Följer konventions **pris – nivå för \_ beräknings skapande \_ virtuella kärnor** i korthet. Mer information om SKU-parametern finns i informationen som följer den här tabellen.                                                                                                                                           |
| BackupRetentionDay         | 7                | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervallet är 7–35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Enabled          | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte. Det går inte att aktivera det här värdet för servrar i den grundläggande pris nivån och det kan inte ändras efter att servern har skapats. Tillåtna värden: Enabled, Disabled.                                                                                                      |
| Location                   | westus           | Azure-regionen för-servern.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Enabled          | Om SSL ska vara aktiverat eller inte för den här servern. Tillåtna värden: Enabled, Disabled.                                                                                                                                                                                                                                                 |
| StorageInMb                | 51200            | Serverns lagringskapacitet (enheten är megabyte). Giltig StorageInMb är minst 5120 MB och ökar i steg om 1024 MB. Mer information om lagrings storleks gränser finns i [Azure Database for PostgreSQL pris nivåer](./concepts-pricing-tiers.md).                                                                               |
| Version                    | 9.6              | Huvudversion för PostgreSQL.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Lösen ordet för administratörs användaren i form av en säker sträng. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.                                       |

Värdet för **SKU** -parametern följer konventions **pris-nivå \_ beräknings generationens \_ virtuella kärnor** som visas i följande exempel.

- `-Sku B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
- `-Sku GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
- `-Sku MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga **SKU** -värden per region och för nivåer finns i [Azure Database for PostgreSQL pris nivåer](./concepts-pricing-tiers.md).

I följande exempel skapas en PostgreSQL-server i regionen **västra USA** med namnet **mydemoserver** i resurs gruppen **myresourcegroup** med en server Administratörs inloggning för **administratören**. Det är en gen 5-Server i den allmänna pris nivån med 2 virtuella kärnor och geo-redundanta säkerhets kopieringar aktiverade. Dokumentera lösen ordet som används på den första raden i exemplet, eftersom det här är lösen ordet för PostgreSQL-serverns administratörs konto.

> [!TIP]
> Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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
mydemoserver.postgres.database.azure.com       myadmin
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

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Ansluta till PostgreSQL-servern med hjälp av pgAdmin

pgAdmin är ett verktyg med öppen källkod som används med PostgreSQL. Du kan installera pgAdmin från [pgAdmin-webbplatsen](https://www.pgadmin.org/). Den pgAdmin-version som du använder kan skilja sig från vad som används i den här snabbstarten. Läs dokumentationen för pgAdmin om du behöver ytterligare hjälp.

1. Öppna pgAdmin-programmet på klientdatorn.

1. Från verktygsfältet går du till **Objekt**, hovrar över **Skapa** och väljer **Server**.

1. I dialogrutan **Skapa – Server** på fliken **Allmänt** anger du ett unikt eget namn för servern, t.ex. **mydemoserver**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Fliken Allmänt":::

1. Fyll i inställningstabellen i dialogrutan **Skapa – Server** på fliken **Anslutning**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="Fliken anslutning":::

    pgAdmin-parameter |Värde|Beskrivning
    ---|---|---
    Värdnamn/-adress | Servernamn | Det värde för servernamn som du använde tidigare när du skapade Azure Database för PostgreSQL-server. Vår exempelserver är **mydemoserver.postgres.database.azure.com.** Använd det fullständigt kvalificerade domän namnet (**\* . postgres.Database.Azure.com**) som visas i exemplet. Om du inte kommer ihåg namnet på servern följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation.
    Port | 5432 | Porten som ska användas när du ansluter till Azure Database för PostgreSQL-servern.
    Underhållsdatabas | *postgres* | Systemgenererat standardnamn för databasen.
    Användarnamn | Inloggningsnamn för serveradministratör | Ange det användarnamn för serveradministratörsinloggning som du angav tidigare när du skapade Azure Database för PostgreSQL-server. Om du inte kommer ihåg användarnamnet följer du anvisningarna i föregående avsnitt för att hitta anslutningsinformation. Formatet är *användar namn \@ Server* namn.
    Lösenord | Ditt administratörslösenord | Det lösenord du angav när du skapade servern tidigare i den här snabbstarten.
    Roll | Lämna tomt | Du behöver inte ange ett rollnamn nu. Lämna fältet tomt.
    SSL-läge | *Kräv* | Du kan ställa in TLS/SSL-läget på SSL-fliken i pgAdmin. Som standard skapas alla Azure Database for PostgreSQL-servrar med TLS tvingande aktiverat. Om du vill inaktivera TLS-framtvingande, se [Konfigurera verk ställande av TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Välj **Spara**.

1. I fönstret **Webbläsare** till vänster expanderar du noden **Servrar**. Välj din server, till exempel **mydemoserver**. Klicka för att ansluta till den.

1. Expandera servernoden och expandera sedan **Databaser** under den. Listan bör innehålla din befintliga *postgres*-databas och eventuella andra databaser som du har skapat. Du kan skapa flera databaser per server med Azure Database for PostgreSQL.

1. Högerklicka på **Databaser**, välj menyn **Skapa** och välj sedan **Databas**.

1. Ange valfritt databasnamn i fältet **Databas**, till exempel **mypgsqldb2**.

1. Välj **Ägare** för databasen från den nedrullningsbara listan. Välj ditt inloggningsnamn som serveradministratör, förslagsvis som i vårt exempel: **my admin**.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Skapa en databas i pgAdmin":::

1. Välj **Spara** för att skapa en tom databas.

1. I fönstret **Webbläsare** ser du den databas som du skapade i listan med databaser under servernamnet.

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här snabb starten inte behövs för en annan snabb start eller självstudier kan du ta bort dem genom att köra följande exempel.

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här snabb starten finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Om du bara vill ta bort den server som skapats i den här snabb starten utan att ta bort resurs gruppen använder du `Remove-AzPostgreSqlServer` cmdleten.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en Azure Database for PostgreSQL med hjälp av PowerShell](tutorial-design-database-using-powershell.md)