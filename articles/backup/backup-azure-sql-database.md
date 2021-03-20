---
title: Säkerhetskopiera SQL Server-databaser till Azure
description: Den här artikeln beskriver hur du säkerhetskopierar SQL Server till Azure. Den här artikeln beskriver även återställning av SQL Server.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 510d9637031928e31abaa5f82a5bf58c6ef44719
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91316857"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Om SQL Server-säkerhetskopiering i virtuella Azure-datorer

[Azure Backup](backup-overview.md) erbjuder en strömmande, specialiserad lösning för att säkerhetskopiera SQL Server som körs i virtuella Azure-datorer. Den här lösningen anpassas med Azure Backup fördelarna med en säkerhets kopiering med noll-infrastruktur, långsiktig kvarhållning och central hantering. Den ger också följande fördelar specifikt för SQL Server:

1. Säkerhets kopiering av arbets belastningar som har stöd för alla säkerhets kopierings typer – fullständig, differentiell och logg
2. 15 minuters återställnings punkt mål (återställnings punkt mål) som ofta loggar säkerhets kopior
3. Tidpunkts återställning till en sekund
4. Säkerhets kopiering och återställning av en enskild databas nivå

Om du vill visa de scenarier för säkerhets kopiering och återställning som vi stöder idag, se [support mat ris](sql-support-matrix.md#scenario-support).

## <a name="backup-process"></a>Säkerhetskopieringsprocessen

Den här lösningen utnyttjar SQL-inbyggda API: er för att säkerhetskopiera SQL-databaser.

* När du har angett SQL Server VM som du vill skydda och fråga efter databaserna i den, kommer Azure Backup-tjänsten att installera ett tillägg för säkerhets kopiering av arbets belastning på den virtuella datorn med namn `AzureBackupWindowsWorkload` tillägget.
* Tillägget består av en koordinator och ett SQL-plugin-program. Även om koordinatorn ansvarar för att utlösa arbets flöden för olika åtgärder som att konfigurera säkerhets kopiering, säkerhets kopiering och återställning, är plugin-programmet ansvarigt för det faktiska data flödet.
* Azure Backup skapar kontot för att kunna identifiera databaser på den här virtuella datorn `NT SERVICE\AzureWLBackupPluginSvc` . Det här kontot används för säkerhets kopiering och återställning och kräver SQL sysadmin-behörigheter. `NT SERVICE\AzureWLBackupPluginSvc`Kontot är ett [virtuellt tjänst konto](/windows/security/identity-protection/access-control/service-accounts#virtual-accounts)och kräver inte någon lösen ords hantering. Azure Backup använder `NT AUTHORITY\SYSTEM` kontot för databas identifiering/-förfrågan, så det här kontot måste vara en offentlig inloggning på SQL. Om du inte skapade den virtuella SQL Server-datorn från Azure Marketplace, kan felet **UserErrorSQLNoSysadminMembership** inträffa. Om det inträffar [följer du de här instruktionerna](#set-vm-permissions).
* När du har aktiverat konfigurera skydd för de valda databaserna konfigurerar säkerhets kopierings tjänsten koordinatorn med säkerhets kopierings scheman och annan princip information, som tillägget cachelagrar lokalt på den virtuella datorn.
* Vid den schemalagda tiden kommunicerar koordinatorn med plugin-programmet och börjar strömma säkerhetskopierade data från SQL-servern med VDI.  
* Plugin-programmet skickar data direkt till Recovery Services valvet, vilket eliminerar behovet av en mellanlagringsplats. Data krypteras och lagras av Azure Backups tjänsten i lagrings konton.
* När data överföringen är slutförd bekräftar koordinatorn incheckningen med säkerhets kopierings tjänsten.

  ![Säkerhets kopierings arkitektur för SQL](./media/backup-azure-sql-database/azure-backup-sql-overview.png)

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar ska du kontrol lera följande krav:

1. Se till att du har en SQL Server-instans som körs i Azure. Du kan [snabbt skapa en SQL Server-instans](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md) på Marketplace.
2. Granska [funktions överväganden](sql-support-matrix.md#feature-considerations-and-limitations) och [scenario supporten](sql-support-matrix.md#scenario-support).
3. [Granska vanliga frågor](faq-backup-sql-server.md) om det här scenariot.

## <a name="set-vm-permissions"></a>Ange VM-behörigheter

  När du kör identifieringen på en SQL Server gör Azure Backup följande:

* Lägger till tillägget AzureBackupWindowsWorkload.
* Skapar ett NT SERVICE\AzureWLBackupPluginSvc-konto för att identifiera databaser på den virtuella datorn. Det här kontot används för att säkerhetskopiera och återställa och kräver SQL sysadmin-behörigheter.
* Identifierar databaser som körs på en virtuell dator, Azure Backup använder NT instans\system-kontot. Detta konto måste vara ett offentligt inloggnings konto på SQL.

Om du inte skapade SQL Server VM på Azure Marketplace eller om du är på SQL 2008 eller 2008 R2 kan du få ett **UserErrorSQLNoSysadminMembership** -fel.

Om du vill ge behörighet för **SQL 2008** och **2008 R2** som körs på Windows 2008 R2 kan du läsa [här](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

För alla andra versioner måste du åtgärda behörigheterna med följande steg:

  1. Använd ett konto med SQL Server-sysadmin-behörighet för att logga in till SQL Server Management Studio (SSMS). Såvida du inte behöver specialbehörigheter bör Windows-autentisering fungera.
  2. Öppna mappen **säkerhets-/inloggningar** på SQL Server.

      ![Öppna mappen Security/inloggningar för att se konton](./media/backup-azure-sql-database/security-login-list.png)

  3. Högerklicka på mappen **inloggningar** och välj **ny inloggning**. I **Login - New** (Inloggning – ny) väljer du **Sök**.

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
> Om SQL Server har flera instanser av SQL Server installerade måste du lägga till sysadmin-behörighet för **NT Service\AzureWLBackupPluginSvc** -kontot till alla SQL-instanser.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>Ge SQL-sysadmin-behörigheter för SQL 2008 och SQL 2008 R2

Lägg till **NT instans\system** -och **NT Service\AzureWLBackupPluginSvc** -inloggningar till SQL Server-instansen:

1. Gå SQL Server-instansen i Object Explorer.
2. Navigera till säkerhets-> inloggningar
3. Högerklicka på inloggningarna och välj *ny inloggning...*

    ![Ny inloggning med SSMS](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Gå till fliken Allmänt och ange **NT instans\system** som inloggnings namn.

    ![Inloggnings namn för SSMS](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Gå till *Server roller* och välj roller för *offentliga* och *sysadmin* .

    ![Välja roller i SSMS](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Gå till *status*. *Ge* behörighet att ansluta till databas motorn och logga in som *aktive rad*.

    ![Bevilja behörigheter i SSMS](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Välj OK.
8. Upprepa samma steg (1-7 ovan) för att lägga till NT Service\AzureWLBackupPluginSvc-inloggning till SQL Server-instansen. Om inloggningen redan finns kontrollerar du att den har Server rollen sysadmin och under status har den behörighet att ansluta till databas motorn och logga in som aktive rad.
9. När du har beviljat behörighet kan du **identifiera databaser** i portalen: **->** säkerhets kopiering av valv infrastruktur **->** arbets belastningen i Azure VM:

    ![Identifiera databaser i Azure Portal](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Du kan också automatisera att ge behörighet genom att köra följande PowerShell-kommandon i administratörs läge. Instans namnet är inställt på MSSQLSERVER som standard. Ändra argumentet instans namn i skriptet om det behövs:

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

* [Lär dig mer om](backup-sql-server-database-azure-vms.md) att säkerhetskopiera SQL Server-databaser.
* [Lär dig om att](restore-sql-database-azure-vm.md) återställa säkerhetskopierade SQL Server-databaser.
* [Lär dig om att](manage-monitor-sql-database-backup.md) hantera säkerhetskopierade SQL Server-databaser.
