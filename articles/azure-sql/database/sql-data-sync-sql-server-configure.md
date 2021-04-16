---
title: Konfigurera SQL Data Sync
description: Den här självstudien visar hur du SQL Data Sync för Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 75de7b122bff75ea13e3b66bb0b79452142dc36c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500098"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Självstudie: Konfigurera SQL Data Sync mellan databaser i Azure SQL Database och SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien lär du dig att konfigurera SQL Data Sync genom att skapa en synkroniseringsgrupp som innehåller både Azure SQL Database och SQL Server instanser. Synkroniseringsgruppen är anpassad konfigurerad och synkroniseras enligt det schema som du anger.

Självstudien förutsätter att du har minst viss erfarenhet av SQL Database och SQL Server.

En översikt över SQL Data Sync finns i [Synkronisera data över molndatabaser och lokala databaser med SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

PowerShell-exempel på hur du konfigurerar SQL Data Sync finns i Synkronisera mellan databaser [i SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md) eller mellan databaser i Azure SQL Database [och SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> SQL Data Sync **stöder inte** Azure SQL Managed Instance just nu.

## <a name="create-sync-group"></a>Skapa synkroniseringsgrupp

1. Gå till [Azure Portal för](https://portal.azure.com) att hitta databasen i SQL Database. Sök efter och välj **SQL-databaser**.

    ![Sök efter databaser, Microsoft Azure-portalen](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Välj den databas som du vill använda som navdatabas för Data Sync.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Hubbdatabasen är en synkroniseringstopologis centrala slutpunkt, där en synkroniseringsgrupp har flera databasslutpunkter. Alla andra medlemsdatabaser med slutpunkter i synkroniseringsgruppen synkroniserar med hubbdatabasen.

1. På **SQL Database-menyn** för den valda databasen väljer du **Synkronisera till andra databaser.**

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. På sidan **Synkronisera till andra databaser** väljer du Ny **synkroniseringsgrupp.** Sidan **Ny synkroniseringsgrupp** öppnas med **Skapa synkroniseringsgrupp (steg 1).**

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   På sidan **Skapa Data Sync grupp** ändrar du följande inställningar:

   | Inställning                        | Beskrivning |
   | ------------------------------ | ------------------------------------------------- |
   | **Synkroniseringsgruppens namn** | Ange ett namn för den nya synkroniseringsgruppen. Det här namnet skiljer sig från namnet på själva databasen. |
   | **Synkronisera metadatadatabas** | Välj att skapa en databas (rekommenderas) eller använda en befintlig databas.<br/><br/>Om du väljer **Ny databas** väljer du Skapa **ny databas.** På sidan **SQL Database** namn och konfigurerar du den nya databasen och väljer **OK.**<br/><br/>Om du väljer **Använd befintlig databas** väljer du databasen i listan. |
   | **Automatisk synkronisering** | Välj **På** eller **Av.**<br/><br/>Om du väljer **På** anger du ett tal och **väljer Sekunder,** **Minuter,** **Timmar** **eller Dagar** i **avsnittet Synkroniseringsfrekvens.**<br/> Den första synkroniseringen börjar efter att den valda intervallperioden har gått från den tidpunkt då konfigurationen sparas.|
   | **Konfliktlösning** | Välj **Hub win (Hubbvinst)** **eller Member win (Medlem vinner).**<br/><br/>**Hubbvinst** innebär att när konflikter uppstår skriver data i hubbdatabasen över motstridiga data i medlemsdatabasen.<br/><br/>**Vinst av** medlem innebär att när konflikter uppstår skriver data i medlemsdatabasen över motstridiga data i hubbdatabasen. |
   | **Använda private link** | Välj en tjänst hanterad privat slutpunkt för att upprätta en säker anslutning mellan synkroniseringstjänsten och hubbdatabasen. |

   > [!NOTE]
   > Microsoft rekommenderar att du skapar en ny, tom databas för användning som **Sync Metadata Database**. Data Sync skapar tabeller i den här databasen och kör en frekvent arbetsbelastning. Den här databasen delas som **synkroniseringsmetadatadatabasen** för alla synkroniseringsgrupper i en vald region och prenumeration. Du kan inte ändra databasen eller dess namn utan att ta bort alla synkroniseringsgrupper och synkroniseringsagenter i regionen. Dessutom kan en databas för elastiska jobb inte användas som SQL Data Sync Metadata-databas och vice versa.  

   Välj **OK** och vänta tills synkroniseringsgruppen har skapats och distribuerats.
   
1. Om du **har valt** Använd privat länk på sidan Ny **synkroniseringsgrupp** måste du godkänna anslutningen till den privata slutpunkten. Länken i informationsmeddelandet tar dig till upplevelsen för privata slutpunktsanslutningar där du kan godkänna anslutningen. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Lägga till synkroniseringsmedlemmar

När den nya synkroniseringsgruppen har skapats och distribuerats markeras Lägg till synkroniseringsmedlemmar **(steg 2)** på **sidan Ny synkroniseringsgrupp.**

I avsnittet **Hub Database** (Hubbdatabas) anger du befintliga autentiseringsuppgifter för den server där hubbdatabasen finns. Ange inte nya *autentiseringsuppgifter* i det här avsnittet.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Lägga till en databas i Azure SQL Database

I avsnittet **Medlemsdatabas kan** du lägga till en databas i Azure SQL Database i synkroniseringsgruppen genom att välja **Lägg till en Azure SQL Database**. Sidan **Konfigurera Azure SQL Database** öppnas.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  På sidan **Konfigurera Azure SQL Database** ändrar du följande inställningar:

  | Inställning                       | Beskrivning |
  | ----------------------------- | ------------------------------------------------- |
  | **Synkronisera medlemsnamn** | Ange ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från själva databasnamnet. |
  | **Prenumeration** | Välj den associerade Azure-prenumerationen i faktureringssyfte. |
  | **Azure SQL-server** | Välj den befintliga servern. |
  | **Azure SQL Database** | Välj den befintliga databasen i SQL Database. |
  | **Synkroniseringsriktningar** | Välj **Dubbelriktad synkronisering,** **Till hubben** eller **Från hubben**. |
  | **Användarnamn** och **lösenord** | Ange de befintliga autentiseringsuppgifterna för servern där medlemsdatabasen finns. Ange inte nya *autentiseringsuppgifter* i det här avsnittet. |
  | **Använda private link** | Välj en tjänst hanterad privat slutpunkt för att upprätta en säker anslutning mellan synkroniseringstjänsten och medlemsdatabasen. |

  Välj **OK** och vänta tills den nya synkroniseringsmedlemmen har skapats och distribuerats.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Lägga till en SQL Server databas

I avsnittet **Medlemsdatabas** kan du lägga till SQL Server databas i synkroniseringsgruppen genom att välja **Lägg till en lokal databas**. Sidan **Konfigurera lokalt öppnas** där du kan göra följande:

1. Välj **Välj synkroniseringsagentgatewayen.** Sidan **Välj synkroniseringsagent** öppnas.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. På sidan **Välj synkroniseringsagent** väljer du om du vill använda en befintlig agent eller skapa en agent.

   Om du väljer **Befintliga agenter** väljer du den befintliga agenten i listan.

   Om du väljer **Skapa en ny agent** gör du följande:

   1. Ladda ned datasynkroniseringsagenten från länken och installera den på den dator där SQL Server finns. Du kan också ladda ned agenten direkt [från Azure SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Du måste öppna utgående TCP-port 1433 i brandväggen för att klientagenten ska kunna kommunicera med servern.

   1. Ange ett namn för agenten.

   1. Välj **Skapa och generera nyckel** och kopiera agentnyckeln till Urklipp.

   1. Välj **OK** för att stänga **sidan Välj synkroniseringsagent.**

1. På den SQL Server datorn letar du upp och kör appen Klientsynkroniseringsagent.

   ![Klientagentappen för datasynkronisering](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. I synkroniseringsagentappen väljer du **Skicka agentnyckel.** Dialogrutan **Synkronisera metadatadatabaskonfiguration** öppnas.

    1. I dialogrutan **Synkronisera konfiguration av metadatadatabas** klistrar du in agentnyckeln som kopierades från Azure Portal. Ange även befintliga autentiseringsuppgifter för servern där metadatadatabasen finns. (Om du har skapat en metadatadatabas finns den här databasen på samma server som hubbdatabasen.) Välj **OK** och vänta tills konfigurationen har avslutats.

        ![Ange agentnyckeln och serverautentiseringsuppgifterna](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Om du får ett brandväggsfel skapar du en brandväggsregel i Azure som tillåter inkommande trafik från SQL Server datorn. Du kan skapa regeln manuellt i portalen eller i SQL Server Management Studio (SSMS). I SSMS ansluter du till hubbdatabasen på Azure genom att ange dess namn som <hub_database_name>.database.windows.net.

    1. Välj **Registrera** för att SQL Server en databas med agenten. Dialogrutan **SQL Server Konfiguration** öppnas.

        ![Lägga till och konfigurera en SQL Server databas](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. I dialogrutan **SQL Server väljer** du att ansluta med hjälp av SQL Server autentisering eller Windows-autentisering. Om du väljer SQL Server autentisering anger du de befintliga autentiseringsuppgifterna. Ange SQL Server namnet och namnet på den databas som du vill synkronisera och välj **Testa anslutning för** att testa dina inställningar. Välj sedan **Spara** så visas den registrerade databasen i listan.

        ![SQL Server databasen är nu registrerad](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Stäng appen Klientsynkroniseringsagent.

1. I portalen går du **till sidan Konfigurera lokalt** och väljer Välj **databasen**.

1. På sidan **Välj databas** i fältet **Synkronisera medlemsnamn anger** du ett namn för den nya synkroniseringsmedlemmen. Det här namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I fältet **Synkroniseringsriktningar** väljer **du Dubbelriktad synkronisering,** **Till hubben** **eller Från hubben**.

    ![Välj den lokala databasen](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Välj **OK** för att stänga **sidan Välj** databas. Välj sedan **OK** för att **stänga sidan Konfigurera** lokalt och vänta tills den nya synkroniseringsmedlemmen har skapats och distribuerats. Välj slutligen **OK för** att stänga sidan Välj **synkroniseringsmedlemmar.**

> [!NOTE]
> Om du vill SQL Data Sync och den lokala agenten lägger du till ditt användarnamn i rollen *DataSync_Executor*. Data Sync skapar den här rollen på SQL Server instansen.

## <a name="configure-sync-group"></a>Konfigurera synkroniseringsgrupp

När de nya medlemmarna i synkroniseringsgruppen har skapats och distribuerats markeras Konfigurera synkroniseringsgrupp **(steg 3)** på **sidan Ny synkroniseringsgrupp.**

![Steg 3– inställningar](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. På sidan **Tabeller** väljer du en databas i listan över medlemmar i synkroniseringsgruppen och väljer **Uppdatera schema.**

1. Välj de tabeller som du vill synkronisera i listan. Som standard markeras alla kolumner, så inaktivera kryssrutan för de kolumner som du inte vill synkronisera. Se till att lämna primärnyckelkolumnen markerad.

1. Välj **Spara**.

1. Som standard synkroniseras inte databaser förrän de har schemalagts eller körts manuellt. Om du vill köra en manuell synkronisering går du till databasen i SQL Database i Azure Portal, väljer **Synkronisera** till andra databaser och väljer synkroniseringsgruppen. Sidan **Data Sync** öppnas. Välj **Synkronisera**.

    ![Manuell synkronisering](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Vanliga frågor

**Skapar SQL Data Sync tabeller helt?**

Om tabeller i synkroniseringsschemat saknas i destinationsdatabasen skapar SQL Data Sync dem med de kolumner du har valt. Detta resulterar dock inte i ett fullständigt återgivningsschema av följande skäl:

- Endast de kolumner som du väljer skapas i måltabellen. Kolumner som inte valts ignoreras.
- Endast markerade kolumnindex skapas i måltabellen. Index för kolumner som inte valts ignoreras.
- Index för kolumner av XML-typ skapas inte.
- CHECK-begränsningar skapas inte.
- Utlösare i källtabellerna skapas inte.
- Vyer och lagrade procedurer skapas inte.

På grund av dessa begränsningar rekommenderar vi följande:

- För produktionsmiljöer skapar du schemat för fullständig återgivning själv.
- När du experimenterar med tjänsten använder du funktionen för automatisk etablering.

**Varför ser jag tabeller som jag inte har skapat?**

Data Sync skapar ytterligare tabeller i databasen för ändringsspårning. Ta inte bort dessa eller Data Sync slutar fungera.

**Konvergerar mina data efter en synkronisering?**

Inte nödvändigtvis. Ta en synkroniseringsgrupp med en hubb och tre ekrar (A, B och C) där synkroniseringarna är Hubb till A, Hubb till B och Hubb till C. Om en ändring görs i databas *A* efter hubb till A-synkronisering skrivs inte ändringen till databas B eller databas C förrän nästa synkroniseringsuppgift.

**Hur gör jag för att hämta schemaändringar i en synkroniseringsgrupp?**

Gör och sprida alla schemaändringar manuellt.

1. Replikera schemaändringarna manuellt till hubben och till alla synkroniseringsmedlemmar.
1. Uppdatera synkroniseringsschemat.

För att lägga till nya tabeller och kolumner:

Nya tabeller och kolumner påverkar inte den aktuella synkroniseringen och Data Sync ignorerar dem tills de läggs till i synkroniseringsschemat. När du lägger till nya databasobjekt följer du sekvensen:

1. Lägg till nya tabeller eller kolumner i hubben och till alla synkroniseringsmedlemmar.
1. Lägg till nya tabeller eller kolumner i synkroniseringsschemat.
1. Börja infoga värden i de nya tabellerna och kolumnerna.

För att ändra datatypen för en kolumn:

När du ändrar datatypen för en befintlig kolumn fortsätter Data Sync att fungera så länge de nya värdena passar den ursprungliga datatypen som definierats i synkroniseringsschemat. Om du till exempel ändrar typen i källdatabasen från **int** till **bigint** fortsätter Data Sync att fungera tills du infogar ett värde som är för stort för **datatypen int.** För att slutföra ändringen replikerar du schemaändringen manuellt till hubben och till alla synkroniseringsmedlemmar och uppdaterar sedan synkroniseringsschemat.

**Hur kan jag exportera och importera en databas med Data Sync?**

När du har exporterat en databas som *en .bacpac-fil* och importerat filen för att skapa en databas gör du följande för att använda Data Sync i den nya databasen:

1. Rensa bort Data Sync och ytterligare tabeller i den nya databasen med hjälp av det [här skriptet](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skriptet tar bort alla nödvändiga Data Sync objekt från databasen.
1. Återskapa synkroniseringsgruppen med den nya databasen. Om du inte längre behöver den gamla synkroniseringsgruppen tar du bort den.

**Var hittar jag information om klientagenten?**

Vanliga frågor om klientagenten finns i Vanliga frågor [och svar om agenten.](sql-data-sync-agent-overview.md#agent-faq)

**Måste jag godkänna den privata länken manuellt innan jag kan börja använda den?**

Ja, du måste manuellt godkänna den tjänst hanterade privata slutpunkten på sidan Privata slutpunktsanslutningar i Azure Portal under distributionen av synkroniseringsgruppen eller med hjälp av PowerShell.

**Varför får jag ett brandväggsfel när synkroniseringsjobbet etablerar min Azure-databas?**

Detta kan inträffa eftersom Azure-resurser inte har behörighet att komma åt servern. Kontrollera att brandväggen på Azure-databasen har inställningen "Tillåt Azure-tjänster och resurser att komma åt den här servern" inställd på "Ja".


## <a name="next-steps"></a>Nästa steg

Grattis! Du har skapat en synkroniseringsgrupp som innehåller både en SQL Database instans och en SQL Server databas.

Mer information om SQL Data Sync finns i:

- [Data Sync Agent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)
- [Metodtips](sql-data-sync-best-practices.md) och [Så här felsöker du problem med Azure SQL Data Sync](sql-data-sync-troubleshoot.md)
- [Övervaka SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
- [Uppdatera synkroniseringsschemat med Transact-SQL](sql-data-sync-update-sync-schema.md) eller [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
