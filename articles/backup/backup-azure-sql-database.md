---
title: Säkerhetskopiera SQL Server-databaser till Azure
description: I den här artikeln förklaras hur du SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b6daf631248958948e799b20284d84a1e59e5dfe
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518872"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

[Azure Backup](backup-overview.md) en strömbaserad, specialiserad lösning för att backa upp SQL Server som körs på virtuella Azure-datorer. Den här lösningen Azure Backup fördelarna med säkerhetskopiering utan infrastruktur, långsiktig kvarhållning och central hantering. Det ger dessutom följande fördelar specifikt för SQL Server:

1. Arbetsbelastningsmedvetna säkerhetskopieringar som stöder alla typer av säkerhetskopiering – fullständig, differentiell och logg
2. 15 minuters RPO (mål för återställningspunkt) med frekventa loggsäkerhetskopior
3. Återställning till tidpunkt upp till en sekund
4. Säkerhetskopiering och återställning på enskild databasnivå

Om du vill se scenarierna för säkerhetskopiering och återställning som vi stöder i dag kan du gå till [supportmatrisen](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Den här lösningen utnyttjar SQL-inbyggda API:er för att göra säkerhetskopior av dina SQL-databaser.

* När du har angett SQL Server VM som du vill skydda och fråga efter databaserna i den installerar Azure Backup-tjänsten ett tillägg för säkerhetskopiering av arbetsbelastningar på den virtuella datorn med hjälp av `AzureBackupWindowsWorkload` namntillägget.
* Det här tillägget består av en koordinator och ett SQL-plugin-program. Koordinatorn ansvarar för att utlösa arbetsflöden för olika åtgärder som att konfigurera säkerhetskopiering, säkerhetskopiering och återställning, men plugin-programmet ansvarar för det faktiska dataflödet.
* För att kunna identifiera databaser på den här virtuella Azure Backup skapar kontot `NT SERVICE\AzureWLBackupPluginSvc` . Det här kontot används för säkerhetskopiering och återställning och kräver SQL-sysadmin-behörigheter. Kontot `NT SERVICE\AzureWLBackupPluginSvc` är ett [virtuellt tjänstkonto](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)och kräver därför ingen lösenordshantering. Azure Backup använder kontot `NT AUTHORITY\SYSTEM` för databasidentifiering/-förfrågan, så det här kontot måste vara en offentlig inloggning på SQL. Om du inte skapade den virtuella SQL Server-datorn från Azure Marketplace, kan felet **UserErrorSQLNoSysadminMembership** inträffa. Om det inträffar [följer du de här instruktionerna](#set-vm-permissions).
* När du utlöser konfigurationsskydd på de valda databaserna konfigurerar säkerhetskopieringstjänsten koordinatorn med säkerhetskopieringsscheman och annan principinformation, som tillägget cachelagrar lokalt på den virtuella datorn.
* Vid den schemalagda tiden kommunicerar koordinatorn med plugin-programmet och börjar strömma säkerhetskopierade data från SQL-servern med hjälp av VDI.  
* Plugin-programmet skickar data direkt till Recovery Services-valvet, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras och lagras av Azure Backup i lagringskonton.
* När dataöverföringen är klar bekräftar koordinatorn flyttningen med säkerhetskopieringstjänsten.

  ![SQL Backup-arkitektur](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Kontrollera följande krav innan du börjar:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) på Marketplace.
2. Granska [funktionsöverväganden](sql-support-matrix.md#feature-considerations-and-limitations) och [scenariostöd.](sql-support-matrix.md#scenario-support)
3. [Granska vanliga frågor](faq-backup-sql-server.yml) om det här scenariot.

## <a name="set-vm-permissions"></a>Ange VM-behörigheter

  När du kör identifiering på en SQL Server gör Azure Backup följande:

* Lägger till tillägget AzureBackupWindowsWorkload.
* Skapar ett NT SERVICE\AzureWLBackupPluginSvc-konto för att identifiera databaser på den virtuella datorn. Det här kontot används för säkerhetskopiering och återställning och kräver SQL-sysadmin-behörigheter.
* Identifierar databaser som körs på en virtuell dator och Azure Backup kontot NT AUTHORITY\SYSTEM. Det här kontot måste vara en offentlig inloggning på SQL.

Om du inte skapade SQL Server VM i Azure Marketplace eller om du använder SQL 2008 eller 2008 R2 kan du få felet **UserErrorSQLNoSysadminMembership.**

För att ge behörigheter för **SQL 2008** och **2008 R2** som körs på Windows 2008 R2, se [här](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Åtgärda behörigheter för alla andra versioner med följande steg:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. På SQL Server öppnar du **mappen Säkerhet/inloggningar.**

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **Inloggningar** och välj **Ny inloggning.** I **Login - New** (Inloggning – ny) väljer du **Sök**.

      ![I dialogrutan Inloggning – ny väljer du Sök](./media/backup-azure-sql-database/new-login-search.png)

  4. Kontot för virtuell Windows-tjänst, **NT SERVICE\AzureWLBackupPluginSvc**, skapades under registreringen av den virtuell datorn och SQL-identifieringsfasen. Ange kontonamnet såsom det visas i **Ange ett objektnamn du vill markera**. Välj **Kontrollera namn** för att matcha namnet. Välj **OK**.

      ![Välj Kontrollera namn för att matcha det okända tjänstnamnet](./media/backup-azure-sql-database/check-name.png)

  5. I **Serverroller** kontrollerar du att serverrollen **sysadmin** har valts. Välj **OK**. De behörigheter som krävs bör nu finnas.

      ![Kontrollera att serverrollen sysadmin har valts](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Nu associerar du databasen med Recovery Services-valvet. I Azure-portalen går du till listan **Skyddade servrar**, högerklickar på den server som är i ett felaktigt tillstånd > **Identifiera databaser på nytt**.

      ![Kontrollera att servern har rätt behörigheter](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Kontrollera förloppet i området **Meddelanden**. När de valda databaserna hittas visas ett meddelande.

      ![Meddelande som anger att distributionen lyckades](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Om din SQL Server har flera instanser av SQL Server installerade måste du lägga till sysadmin-behörighet för **NT Service\AzureWLBackupPluginSvc-kontot** till alla SQL-instanser.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Ge SQL sysadmin-behörigheter för SQL 2008 och SQL 2008 R2

Lägg **till INLOGGNINGAR för NT AUTHORITY\SYSTEM** **och NT Service\AzureWLBackupPluginSvc** till SQL Server instansen:

1. Gå till SQL Server-instansen i Object Explorer.
2. Gå till Security -> Logins (Säkerhet – > inloggningar)
3. Högerklicka på inloggningarna och välj *Ny inloggning...*

    ![Ny inloggning med SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Gå till fliken Allmänt och ange **NT AUTHORITY\SYSTEM** som inloggningsnamn.

    ![Inloggningsnamn för SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Gå till *Serverroller* och välj *offentliga roller* *och sysadmin-roller.*

    ![Välja roller i SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Gå till *Status*. *Ge* behörighet att ansluta till databasmotorn och logga in som *aktiverad.*

    ![Bevilja behörigheter i SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Välj OK.
8. Upprepa samma stegsekvens (1–7 ovan) för att lägga till NT Service\AzureWLBackupPluginSvc-inloggning till SQL Server instansen. Om inloggningen redan finns kontrollerar du att den har sysadmin-serverrollen. Under Status har den Bevilja behörighet att ansluta till databasmotorn och Logga in som Aktiverad.
9. När du har beviljat behörighet **kan du omidentifiera DBs på** portalen: Arbetsbelastning för infrastruktur för **->** **->** säkerhetskopiering av valv i en virtuell Azure-dator:

    ![Ny upptäckt av DBs i Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Du kan också automatisera behörigheterna genom att köra följande PowerShell-kommandon i administratörsläge. Instansnamnet är inställt på MSSQLSERVER som standard. Ändra argumentet för instansnamn i skriptet om det behövs:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer](backup-sql-server-database-azure-vms.md) om att SQL Server databaser.
* [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
* [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.
