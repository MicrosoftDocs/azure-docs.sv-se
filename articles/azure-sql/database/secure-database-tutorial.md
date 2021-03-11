---
title: Skydda en databas
description: I den här självstudien får du lära dig om tekniker och funktioner för att skydda en Azure SQL Database, oavsett om det är en enskild databas eller pool.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: 8cdf95dd3c0e801896328136b15e4bd4efe53005
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563820"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Självstudie: skydda en databas i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> - Skapa brandväggsregler på servernivå och databasnivå
> - Konfigurera en Azure Active Directory (Azure AD)-administratör
> - Hantera användaråtkomst med SQL-autentisering, Azure AD-autentisering och säkra anslutningssträngar
> - Aktivera säkerhetsfunktioner, till exempel Azure Defender för SQL, granskning, data maskning och kryptering

Azure SQL Database skyddar data genom att göra det möjligt för dig att:

- Begränsa åtkomst med hjälp av brandväggsregler
- Använd autentiseringsmekanismer som kräver identitet
- Använda auktorisering med rollbaserade medlemskap och behörigheter
- Aktivera säkerhetsfunktioner

> [!NOTE]
> Den hanterade Azure SQL-instansen skyddas med nätverks säkerhets regler och privata slut punkter enligt beskrivningen i [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md) och [anslutnings arkitektur](../managed-instance/connectivity-architecture-overview.md).

Mer information finns i artiklarna [Säkerhetsöversikt för Azure SQL Database](./security-overview.md) och [Funktioner](security-overview.md).

> [!TIP]
> Följande Microsoft Learn modul hjälper dig att lära dig kostnads fritt om hur du [skyddar databasen i Azure SQL Database](/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- En [Server](logical-servers.md) och en enkel databas
  - Skapa dem med [Azure Portal](single-database-create-quickstart.md), [CLI](az-cli-script-samples-content-guide.md)eller [PowerShell](powershell-script-content-guide.md)

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

För alla steg i självstudien loggar du in på [Azure Portal](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Skapa brandväggsregler

Databaser i SQL Database skyddas av brand väggar i Azure. Som standard avvisas alla anslutningar till servern och databasen. Läs mer i [brand Väggs regler på server nivå och databas nivå](firewall-configure.md).

Ställ in **Tillåt åtkomst till Azure-tjänster** på **AV** för den säkraste konfigurationen. Skapa sedan en [reserverad IP (klassisk distribution)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) för resursen som behöver anslutas, till exempel en virtuell Azure-dator eller molntjänst, och tillåt endast att den IP-adressen får åtkomst via brandväggen. Om du använder distributions modellen för [Resource Manager](../../virtual-network/public-ip-addresses.md) krävs en dedikerad offentlig IP-adress för varje resurs.

> [!NOTE]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till servern om administratören öppnar port 1433.

### <a name="set-up-server-level-firewall-rules"></a>Konfigurera brandväggsregler på servernivå

Regler för IP-brandvägg på server nivå gäller för alla databaser på samma server.

Konfigurera en brandväggsregel på servernivå:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

    ![brandväggsregler för server](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Se till att kopiera det fullständigt kvalificerade servernamnet (till exempel *yourserver.database.windows.net*) för användning senare i självstudien.

1. På **översiktssidan** väljer du **Konfigurera serverns brandvägg**. Sidan **brand Väggs inställningar** för servern öppnas.

   1. Välj **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny brandväggsregel. Med regeln kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall. Välj **Spara**.

      ![ange brandväggsregel för server](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Välj **OK** och stäng sidan **Brandväggsinställningar**.

Nu kan du ansluta till valfri databas på servern med IP-adressen eller IP-adressintervallet som angetts.

### <a name="setup-database-firewall-rules"></a>Konfigurera brandväggsregler för databaser

Brandväggsregler på databasnivå gäller endast för enskilda databaser. Databasen behåller dessa regler under en redundansväxling av servern. Brandväggsregler på databasnivå kan bara konfigureras med instruktioner för Transact-SQL (T-SQL), och bara efter att du har konfigurerat en brandväggsregel på servernivå.

Konfigurera en brand Väggs regel på databas nivå:

1. Anslut till databasen med exempelvis [SQL Server Management Studio](connect-query-ssms.md).

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Ändra IP-adressen till din offentliga IP-adress i frågefönstret och lägg till instruktionen:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Välj **Kör** i verktygsfältet för att skapa brandväggsregeln.

> [!NOTE]
> Du kan också skapa en brandväggsregel på servernivå i SSMS med kommandot [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), men du måste vara ansluten till *master*-databasen.

## <a name="create-an-azure-ad-admin"></a>Skapa en Azure AD-administratör

Kontrollera att du använder den lämpliga hanterade domänen med Azure Active Directory (AD). Välj AD-domänen längst upp till höger i Azure-portalen. Den här processen bekräftar att samma prenumeration används för både Azure AD och den logiska SQL-Server som är värd för din databas eller informations lager.

   ![choose-ad](./media/secure-database-tutorial/8choose-ad.png)

Så här anger du Azure AD-administratör:

1. I Azure Portal på **SQL Server** -sidan väljer du **Active Directory admin**. Välj sedan **Ange administratör**.

    ![Välj active directory](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Du måste vara en global administratör för att kunna utföra den här uppgiften.

1. På sidan **Lägg till administratör** söker du efter och väljer AD-användaren eller gruppen och därefter **Välj**. Alla medlemmar och grupper för din Active Directory visas, och nedtonade poster stöds inte som Azure AD-administratörer. Se avsnittet om [Azure AD-funktioner och begränsningar](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![välja administratör](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Rollbaserad åtkomst kontroll i Azure (Azure RBAC) gäller bara för portalen och har inte spridits till SQL Server.

1. Välj **Spara** längst upp på sidan **Active Directory administratör** .

    Processen med att ändra administratör kan ta några minuter. Sedan visas den nya administratören i rutan **Active Directory-administratör**.

> [!NOTE]
> När du anger en Azure AD-administratör kan det nya administratörs namnet (användare eller grupp) inte finnas som SQL Server inloggning eller användare i *huvud* databasen. Om den gör det misslyckas installationen och ändringarna återställs, vilket indikerar att det redan finns ett administratörsnamn. Eftersom SQL Server inloggning eller användare inte är en del av Azure AD, Miss lyckas alla åtgärder för att ansluta användaren med Azure AD-autentisering.

Information om hur du konfigurerar Azure AD finns i:

- [Integrera dina lokala identiteter med Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Lägga till ditt eget domännamn i Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure har nu stöd för federation med Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrera Azure AD-katalogen](../../active-directory/fundamentals/active-directory-whatis.md)
- [Hantera Azure AD med hjälp av PowerShell](/powershell/azure/)
- [Portar och protokoll som krävs för Hybrid identitet](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Hantera databasåtkomst

Hantera åtkomst till databasen genom att lägga till användare i databasen eller tillåta användaråtkomst med säkra anslutningssträngar. Anslutningssträngar är användbara för externa program. Mer information finns i [Hantera inloggningar och användar konton](logins-create-manage.md) och [AD-autentisering](authentication-aad-overview.md).

Välj autentiseringstyp för databasen för att lägga till användare:

- **SQL-autentisering**, som använder användarnamn och lösenord för inloggning, och användarna är endast behöriga i kontexten av en specifik databas inom servern

- **Azure AD-autentisering**, använder identiteter som hanteras av Azure AD

### <a name="sql-authentication"></a>SQL-autentisering

Så här lägger du till en användare med SQL-autentisering:

1. Anslut till databasen med exempelvis [SQL Server Management Studio](connect-query-ssms.md).

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. Skriv följande kommando i frågefönstret:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Välj **Kör** i verktygsfältet för att skapa användaren.

1. Som standard kan användaren ansluta till databasen, men har inte behörighet att läsa eller skriva data. Om du vill bevilja de här behörigheterna kör du följande två kommandon i ett nytt frågefönster:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Skapa icke-administratörskonton på databasnivå, såvida de inte behöver köra administratörsåtgärder som att skapa nya användare.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

Azure Active Directory-autentisering kräver att databasanvändare skapas som användare av oberoende databas. En oberoende databasanvändare mappar till en identitet i Azure AD-katalogen som är kopplad till databasen och har ingen inloggning i *master*-databasen. Azure AD-identiteten kan vara antingen ett enskilt användarkonto eller en grupp. Mer information finns i avsnittet om [oberoende databasanvändare, göra databasen portabel](/sql/relational-databases/security/contained-database-users-making-your-database-portable) och läs [Azure AD-självstudien](authentication-aad-configure.md) om hur du autentiserar med hjälp av Azure AD.

> [!NOTE]
> Databasanvändare (exklusive administratörer) kan inte skapas med hjälp av Azure-portalen. Azure-roller sprids inte till SQL-servrar, databaser eller informations lager. De används endast för att hantera Azure-resurser och gäller inte för databasbehörigheter.
>
> Till exempel beviljar rollen *SQL Server-deltagare* inte åtkomst för att ansluta till en databas eller ett datalager. Den här behörigheten måste beviljas i databasen med hjälp av T-SQL-instruktioner.

> [!IMPORTANT]
> Specialtecken som kolon `:` eller et-tecken `&` stöds inte i användarnamn i T-SQL `CREATE LOGIN` och `CREATE USER` instruktioner.

Så här lägger du till en användare med Azure AD-autentisering:

1. Anslut till din server i Azure med hjälp av ett Azure AD-konto med minst behörigheten *ändra alla användare* .

1. I **Object Explorer** högerklickar du på databasen och väljer **Ny fråga**.

1. I frågefönstret anger du följande kommando och ändrar `<Azure_AD_principal_name>` till Azure AD-användarens huvudnamn eller visningsnamnet för Azure AD-gruppen:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Azure AD-användare har markerats i databasens metadata med typ `E (EXTERNAL_USER)` och typ `X (EXTERNAL_GROUPS)` för grupper. Mer information finns i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Skydda anslutningssträngar

För att säkerställa en säker, krypterad anslutning mellan klient programmet och SQL Database måste du konfigurera en anslutnings sträng för att:

- Begära en krypterad anslutning
- Inte lita på servercertifikatet

Anslutningen upprättas med TLS (Transport Layer Security) och risken för MITM-attacker (man-in-the-middle) minskar. Anslutningssträngar är tillgängliga per databas och har redan konfigurerats för att stödja klientdrivrutiner som ADO.NET, JDBC, ODBC och PHP. Information om TLS och anslutningar finns i avsnittet om [TLS-specifika överväganden](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Så här kopierar du en säker anslutningssträng:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

1. På databasens **översiktssida** klickar du på **Visa databasanslutningssträngar**.

1. Välj en drivrutinsflik och kopiera den fullständiga anslutningssträngen.

    ![ADO.NET-anslutningssträng](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Aktivera säkerhetsfunktioner

Azure SQL Database innehåller säkerhetsfunktioner som nås med hjälp av Azure-portalen. Dessa funktioner är tillgängliga för både databasen och servern, förutom datamaskning, som endast är tillgängligt för databasen. Läs mer i [Azure Defender for SQL](azure-defender-for-sql.md), [granskning](../../azure-sql/database/auditing-overview.md), [dynamisk data maskning](dynamic-data-masking-overview.md)och [transparent data kryptering](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender för SQL

Funktionen Azure Defender för SQL identifierar potentiella hot när de inträffar och ger säkerhets aviseringar om avvikande aktiviteter. Användare kan utforska misstänkta händelser med granskningsfunktionen och avgöra om händelsen orsakades av ett försök att komma åt, tränga in i eller utnyttja data i databasen. Användare kan också få en översikt över säkerhet som innehåller en sårbarhetsbedömning och verktyget för dataidentifiering och klassificering.

> [!NOTE]
> Ett exempel på hot är SQL-inmatning, en process där angripare matar in skadliga SQL i indataprogrammet. Ett program kan sedan omedvetet köra skadlig SQL och ge angripare åtkomst till att tränga in i eller ändra data i databasen.

Så här aktiverar du Azure Defender för SQL:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

1. På **översiktssidan** väljer du **Servernamn**. Sidan Server öppnas.

1. På **SQL Server** -sidan, leta upp avsnittet **säkerhet** och välj **Security Center**.

   1. Aktivera funktionen genom att välja **på** under **Azure Defender for SQL** . Välj ett lagringskonto för att spara resultat av sårbarhetsbedömning. Välj sedan **Spara**.

      ![Navigeringsfönster](./media/secure-database-tutorial/threat-settings.png)

      Du kan också konfigurera e-postmeddelanden för att få säkerhetsaviseringar, lagringsinformation och typer för identifiering av hot.

1. Gå tillbaka till sidan **SQL-databaser** i databasen och välj **Security Center** under avsnittet **säkerhet** . Här hittar du olika säkerhetsindikatorer som är tillgängliga för databasen.

    ![Hotstatus](./media/secure-database-tutorial/threat-status.png)

Om avvikande aktiviteter identifieras får du ett e-postmeddelande med information om händelsen. Detta inkluderar typen av aktivitet, databas, server, tidpunkt för händelsen, möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet. Om du får ett sådant e-postmeddelande väljer du länken för **Azure SQL-granskningsloggen** för att starta Azure-portalen och visa relevanta granskningsposter för händelsens tidpunkt.

   ![E-post om hotidentifiering](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Granskning

Gransknings funktionen spårar databas händelser och skriver händelser till en Gransknings logg i antingen Azure Storage, Azure Monitor loggar eller till en händelsehubben. Granskning hjälper till att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på potentiella säkerhetsöverträdelser.

Så här aktiverar du granskning:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

1. I avsnittet **Säkerhet** väljer du **Granskning**.

1. Under **granskningsinställningarna** anger du följande värden:

   1. Ställ in **Granskning** på **PÅ**.

   1. Välj **Mål för spårningsloggen** som något av följande:

       - **Storage**, ett Azure Storage-konto där händelseloggar sparas och kan laddas ned som *.xel*-filer

          > [!TIP]
          > Använd samma lagringskonto för alla granskade databaser för att få ut mesta möjliga av granskningsrapportmallarna.

       - **Log Analytics**, som automatiskt lagrar händelser för frågor eller ytterligare analys

           > [!NOTE]
           > En **Log Analytics arbets yta** krävs för att stödja avancerade funktioner som analys, anpassade aviserings regler och Excel eller Power BI exporter. Utan en arbetsyta är endast frågeredigeraren tillgänglig.

       - **Event Hub**, som tillåter att händelser dirigeras för användning i andra program

   1. Välj **Spara**.

      ![Granska inställningar](./media/secure-database-tutorial/audit-settings.png)

1. Nu kan du välja **Visa granskningsloggar** för att visa databashändelsers data.

    ![Granskningsposter](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> Se [SQL Database granskning](../../azure-sql/database/auditing-overview.md) för att ytterligare anpassa gransknings händelser med PowerShell eller REST API.

### <a name="dynamic-data-masking"></a>Dynamisk datamaskning

Funktionen för datamaskning kommer automatiskt att dölja känsliga data i databasen.

Så här aktiverar du datamaskning:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

1. I avsnittet **Säkerhet** väljer du **Dynamisk datamaskning**.

1. Under inställningarna för **Dynamisk datamaskning** väljer du **Lägg till mask** för att lägga till en maskningsregel. Azure fyller automatiskt i tillgängliga databasscheman, tabeller och kolumner att välja bland.

    ![Maskinställningar](./media/secure-database-tutorial/mask-settings.png)

1. Välj **Spara**. Den valda informationen maskeras nu för sekretess.

    ![Maskexempel](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Transparent datakryptering

Krypteringsfunktionen krypterar automatiskt dina vilande data och kräver inga ändringar i program som har åtkomst till den krypterade databasen. Kryptering är standard för nya databaser. Du kan också kryptera data med hjälp av SSMS och funktionen [Alltid krypterad](always-encrypted-certificate-store-configure.md).

Så här aktiverar eller kontrollerar du kryptering:

1. I Azure Portal väljer du **SQL-databaser** på den vänstra menyn och väljer din databas på sidan **SQL-databaser** .

1. I avsnittet **Säkerhet** väljer du **Transparent datakryptering**.

1. Om det behövs väljer du **PÅ** för **Datakryptering**. Välj **Spara**.

    ![Transparent datakryptering](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Om du vill visa krypteringsstatus ansluter du till databasen med [SSMS](connect-query-ssms.md) och frågar efter kolumnen `encryption_state` i vyn [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). Tillståndet `3` anger att databasen är krypterad.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att förbättra säkerheten för din databas med bara några få enkla steg. Du har lärt dig att:

> [!div class="checklist"]
>
> - Skapa brandväggsregler på servernivå och databasnivå
> - Konfigurera en administratör för Azure Active Directory (AD Azure)
> - Hantera användaråtkomst med SQL-autentisering, Azure AD-autentisering och säkra anslutningssträngar
> - Aktivera säkerhetsfunktioner, till exempel Azure Defender för SQL, granskning, data maskning och kryptering

I nästa självstudie får du lära dig hur du implementerar en geo-distribution.

> [!div class="nextstepaction"]
>[Implementera en geo-distribuerad databas](geo-distributed-application-configure-tutorial.md)