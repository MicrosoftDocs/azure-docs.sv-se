---
title: Konfigurera SQL Data Sync
description: Den här självstudien visar hur du konfigurerar SQL Data Sync för Azure
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
ms.openlocfilehash: d6b5bab1c1b6c8db4821fdf84728eb66eb55b899
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882237"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Självstudie: Konfigurera SQL Data Sync mellan databaser i Azure SQL Database och SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du lära dig hur du konfigurerar SQL Data Sync genom att skapa en Sync-grupp som innehåller både Azure SQL Database-och SQL Server instanser. Sync-gruppen är anpassad konfigurerad och synkroniseras enligt det schema du anger.

Självstudien förutsätter att du har minst en tidigare erfarenhet av SQL Database och SQL Server.

En översikt över SQL Data Sync finns i [synkronisera data i molnet och i lokala databaser med SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

PowerShell-exempel på hur du konfigurerar SQL Data Sync finns i [så här synkroniserar du mellan databaser i SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md) eller [mellan databaser i Azure SQL Database och SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> SQL Data Sync stöder **inte** Azure SQL-hanterad instans för tillfället.

## <a name="create-sync-group"></a>Skapa sync-grupp

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta din databas i SQL Database. Sök efter och välj **SQL-databaser**.

    ![Sök efter databaser, Microsoft Azure-portalen](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Välj den databas som du vill använda som hubb databas för datasynkronisering.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Hub-databasen är en central slut punkt för synkronisering, där en synkroniserad grupp har flera databas slut punkter. Alla andra medlems databaser med slut punkter i Sync-gruppen synkroniseras med Hub-databasen.

1. På **SQL Database** -menyn för den valda databasen väljer du **synkronisera till andra databaser**.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. På sidan **synkronisera till andra databaser** väljer du **ny Sync-grupp**. Sidan **ny Sync-grupp** öppnas med **create Sync Group (steg 1)**.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   På sidan **skapa data Sync-grupp** ändrar du följande inställningar:

   | Inställning                        | Beskrivning |
   | ------------------------------ | ------------------------------------------------- |
   | **Synkroniseringsgruppens namn** | Ange ett namn för den nya Sync-gruppen. Namnet skiljer sig från namnet på själva databasen. |
   | **Synka metadata-databas** | Välj att skapa en databas (rekommenderas) eller Använd en befintlig databas.<br/><br/>Om du väljer **ny databas** väljer du **Skapa ny databas.** På sidan **SQL Database** namnger och konfigurerar du den nya databasen och väljer **OK**.<br/><br/>Om du väljer **Använd befintlig databas** väljer du databasen i listan. |
   | **Automatisk synkronisering** | Välj **på** eller **av**.<br/><br/>Om du väljer **på** anger du ett nummer och väljer **sekunder**, **minuter**, **timmar** eller **dagar** i avsnittet **Synkroniseringsfrekvens** .<br/> Den första synkroniseringen börjar efter den valda intervall perioden från den tidpunkt då konfigurationen sparades.|
   | **Konflikt lösning** | Välj **hubben Win** eller **member Win**.<br/><br/>**Hubben Win** innebär att data i Hub-databasen skriver över motstridiga data i medlems databasen när konflikter uppstår.<br/><br/>**Medlemmen Win** innebär att om konflikter uppstår skriver data i medlems databasen över motstridiga data i Hubbs databasen. |
   | **Använd privat länk** | Välj en tjänst hanterad privat slut punkt för att upprätta en säker anslutning mellan synkroniseringstjänsten och Hub-databasen. |

   > [!NOTE]
   > Microsoft rekommenderar att du skapar en ny, tom databas för användning som **databasen för synkronisering av metadata**. Datasynkronisering skapar tabeller i den här databasen och kör en frekvent arbets belastning. Den här databasen delas som **databasen för synkronisering av metadata** för alla Sync-grupper i en vald region och prenumeration. Du kan inte ändra databasen eller dess namn utan att ta bort alla synkroniserade grupper och synkronisera agenter i regionen.

   Välj **OK** och vänta tills Sync-gruppen har skapats och distribuerats.
   
1. Om du har valt **Använd privat länk** på sidan **ny Sync-grupp** måste du godkänna anslutningen till den privata slut punkten. Länken i informations meddelandet tar dig till den privata slut punkts anslutningar där du kan godkänna anslutningen. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Lägg till medlemmar i synkronisering

När den nya synkroniseringsresursen har skapats och distribuerats lägger du **till Sync-medlemmar (steg 2)** på sidan **ny Sync** -grupp.

I avsnittet **Hubbs databas** anger du befintliga autentiseringsuppgifter för den server där Hub-databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Lägga till en databas i Azure SQL Database

I avsnittet **medlems databas** lägger du till en databas i Azure SQL Database till Sync-gruppen genom att välja **lägg till en Azure SQL Database**. Sidan **konfigurera Azure SQL Database** öppnas.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  Ändra följande inställningar på sidan **konfigurera Azure SQL Database** :

  | Inställning                       | Beskrivning |
  | ----------------------------- | ------------------------------------------------- |
  | **Synkronisera medlems namn** | Ange ett namn för den nya synkroniserings medlemmen. Namnet skiljer sig från själva databas namnet. |
  | **Prenumeration** | Välj den associerade Azure-prenumerationen för fakturerings syfte. |
  | **Azure SQL-server** | Välj den befintliga servern. |
  | **Azure SQL Database** | Välj den befintliga databasen i SQL Database. |
  | **Anvisningar för synkronisering** | Välj **dubbelriktad synkronisering**, **till hubben** eller **från hubben**. |
  | **Användar namn** och **lösen ord** | Ange de befintliga autentiseringsuppgifterna för servern där medlems databasen finns. Ange inte *nya* autentiseringsuppgifter i det här avsnittet. |
  | **Använd privat länk** | Välj en tjänst hanterad privat slut punkt för att upprätta en säker anslutning mellan Sync-tjänsten och medlems databasen. |

  Välj **OK** och vänta tills den nya synkroniserings medlemmen har skapats och distribuerats.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Lägga till en SQL Server databas

I avsnittet **medlems databas** lägger du till en SQL Server databas i Sync-gruppen genom att välja **Lägg till en lokal databas**. Sidan **Konfigurera lokalt** öppnas där du kan göra följande saker:

1. Välj **Välj Sync agent-Gateway**. Sidan **Välj Sync-agent** öppnas.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. På sidan **Välj agent för synkronisering** väljer du om du vill använda en befintlig agent eller skapa en agent.

   Om du väljer **befintliga agenter** väljer du den befintliga agenten i listan.

   Om du väljer **skapa en ny agent** gör du följande:

   1. Ladda ned agenten för data synkronisering från den angivna länken och installera den på den dator där SQL Server finns. Du kan också hämta agenten direkt från [Azure SQL Data Sync-agenten](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > Du måste öppna utgående TCP-port 1433 i brand väggen så att klient agenten kan kommunicera med servern.

   1. Ange ett namn för agenten.

   1. Välj **skapa och generera nyckel** och kopiera Agent nyckeln till Urklipp.

   1. Välj **OK** för att stänga sidan **Välj Sync-agent** .

1. Leta upp och kör Klientsynkroniseringsagent-appen på SQL Server dator.

   ![Appen för klient agent för data synkronisering](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. I appen Sync-agent väljer du **Skicka agent nyckel**. Dialog rutan **konfiguration av databas för synkronisering av metadata** öppnas.

    1. I dialog rutan **konfiguration av databas för synkronisering av metadata** klistrar du in Agent nyckeln som kopierats från Azure Portal. Ange även de befintliga autentiseringsuppgifterna för servern där metadata-databasen finns. (Om du har skapat en metadata-databas finns den här databasen på samma server som Hub-databasen.) Välj **OK** och vänta tills konfigurationen har slutförts.

        ![Ange agent nyckel och autentiseringsuppgifter för servern](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Om du får ett brand Väggs fel skapar du en brand Väggs regel på Azure för att tillåta inkommande trafik från den SQL Server datorn. Du kan skapa regeln manuellt i portalen eller i SQL Server Management Studio (SSMS). I SSMS ansluter du till Hub-databasen på Azure genom att ange dess namn som <hub_database_name>. database.windows.net.

    1. Välj **Registrera** för att registrera en SQL Server databas med agenten. Dialog rutan **SQL Server konfiguration** öppnas.

        ![Lägga till och konfigurera en SQL Server databas](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. I dialog rutan **SQL Server konfiguration** väljer du att ansluta med SQL Server autentisering eller Windows-autentisering. Om du väljer SQL Server autentisering anger du de befintliga autentiseringsuppgifterna. Ange SQL Server namn och namnet på den databas som du vill synkronisera och välj **Testa anslutning** för att testa inställningarna. Välj sedan **Spara** och den registrerade databasen visas i listan.

        ![SQL Server Database har nu registrerats](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Stäng Klientsynkroniseringsagent-appen.

1. I portalen på sidan **Konfigurera lokalt** väljer **du Välj databas**.

1. På sidan **Välj databas** i fältet **Synkronisera medlems namn** anger du ett namn för den nya synkroniserings medlemmen. Namnet skiljer sig från namnet på själva databasen. Välj databasen i listan. I fältet **Synkronisera vägvisningar** väljer du **dubbelriktad synkronisering**, **till hubben** eller **från hubben**.

    ![Välj den lokala databasen](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Klicka på **OK** för att stänga sidan **Välj databas** . Välj **OK** för att stänga sidan **Konfigurera lokalt** och vänta tills den nya synkroniserings medlemmen skapas och distribueras. Välj slutligen **OK** för att stänga sidan **Välj synkronisera medlemmar** .

> [!NOTE]
> Om du vill ansluta till SQL Data Sync och den lokala agenten lägger du till ditt användar namn i roll *DataSync_Executor*. DataSync skapar den här rollen på SQL Server-instansen.

## <a name="configure-sync-group"></a>Konfigurera Sync-grupp

När de nya medlemmarna i Sync-gruppen har skapats och distribuerats, är **Konfigurera Sync-grupp (steg 3)** markerat på sidan **ny Sync** -grupp.

![Steg 3 inställningar](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. På sidan **tabeller** väljer du en databas i listan över synkroniserade grupp medlemmar och väljer **Uppdatera schema**.

1. I listan väljer du de tabeller som du vill synkronisera. Som standard är alla kolumner markerade, så inaktivera kryss rutan för de kolumner som du inte vill synkronisera. Se till att lämna kolumnen primär nyckel vald.

1. Välj **Spara**.

1. Som standard synkroniseras inte databaser förrän schemalagda eller manuellt körs. Om du vill köra en manuell synkronisering går du till databasen i SQL Database i Azure Portal väljer du **synkronisera till andra databaser** och väljer sedan Sync-gruppen. Sidan **datasynkronisering** öppnas. Välj **Synkronisera**.

    ![Manuell synkronisering](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Vanliga frågor

**Skapar SQL Data Sync tabeller helt?**

Om tabeller i synkroniseringsschemat saknas i destinationsdatabasen skapar SQL Data Sync dem med de kolumner du har valt. Detta resulterar dock inte i ett schema med fullständig åter givning av följande orsaker:

- Endast de kolumner som du väljer skapas i måltabellen. Kolumner som inte valts ignoreras.
- Endast markerade kolumnindex skapas i måltabellen. Index för kolumner som inte valts ignoreras.
- Index för kolumner med XML-typ skapas inte.
- KONTROLL begränsningar skapas inte.
- Utlösare i käll tabellerna skapas inte.
- Vyer och lagrade procedurer skapas inte.

På grund av dessa begränsningar rekommenderar vi följande:

- För produktions miljöer skapar du schemat full Fidelity själv.
- Använd funktionen för automatisk etablering när du experimenterar med tjänsten.

**Varför visas tabeller som jag inte har skapat?**

Datasynkronisering skapar ytterligare tabeller i databasen för ändrings spårning. Ta inte bort dessa eller så slutar datasynkroniseringen att fungera.

**Är mina data Convergent efter en synkronisering?**

Inte nödvändigt vis. Ta en Sync-grupp med en hubb och tre ekrar (A, B och C) där synkroniseringarna är hubb till en, hubb till B och hubb till C. Om en ändring görs i databasen A *efter* navet till en synkronisering, skrivs den ändringen inte till databas B eller databas C förrän nästa synkroniseringsuppgift.

**Hur gör jag för att få schema ändringar till en Sync-grupp?**

Gör och sprid alla schema ändringar manuellt.

1. Replikera schema ändringarna manuellt till hubben och till alla synkroniserade medlemmar.
1. Uppdatera synkroniseringsschemat.

För att lägga till nya tabeller och kolumner:

Nya tabeller och kolumner påverkar inte den aktuella synkroniseringen och datasynkroniseringen ignorerar dem tills de läggs till i synkroniseringsschemat. När du lägger till nya databas objekt följer du sekvensen:

1. Lägg till nya tabeller eller kolumner i hubben och alla synkroniserade medlemmar.
1. Lägg till nya tabeller eller kolumner i synkroniseringsschemat.
1. Börja infoga värden i nya tabeller och kolumner.

För att ändra data typen för en kolumn:

När du ändrar data typen för en befintlig kolumn fortsätter data synkroniseringen att fungera så länge de nya värdena passar den ursprungliga data typen som definierats i synkroniseringsschemat. Om du till exempel ändrar typen i käll databasen från **int** till **bigint** fortsätter datasynkroniseringen att fungera tills du sätter in värdet för stor för data typen **int** . Du slutför ändringen genom att replikera schema ändringen manuellt till hubben och till alla synkroniserade medlemmar och sedan uppdatera synkroniseringsschemat.

**Hur kan jag exportera och importera en databas med datasynkronisering?**

När du har exporterat en databas som en *. bacpac* -fil och importera filen för att skapa en databas, gör du följande för att använda datasynkronisering i den nya databasen:

1. Rensa DataSync-objekten och ytterligare tabeller i den nya databasen med hjälp av [det här skriptet](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Skriptet tar bort alla nödvändiga data synkroniseringsobjekt från databasen.
1. Återskapa Sync-gruppen med den nya databasen. Om du inte längre behöver den gamla Sync-gruppen tar du bort den.

**Var kan jag hitta information om klient agenten?**

Vanliga frågor och svar om klient agenten finns i [vanliga frågor och svar om agent](sql-data-sync-agent-overview.md#agent-faq).

**Är det nödvändigt att godkänna den privata länken manuellt innan jag kan börja använda den?**

Ja, du måste godkänna den tjänst hanterade privata slut punkten manuellt på sidan anslutningar för privata slut punkter i Azure Portal under distributionen av synkroniseringen eller med PowerShell.

## <a name="next-steps"></a>Nästa steg

Grattis! Du har skapat en Sync-grupp som innehåller både en SQL Database-instans och en SQL Server-databas.

Mer information om SQL Data Sync finns i:

- [Data Sync-agent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)
- [Metod tips](sql-data-sync-best-practices.md) och [fel sökning av problem med Azure SQL Data Sync](sql-data-sync-troubleshoot.md)
- [Övervaka SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
- [Uppdatera synkroniseringsschemat med Transact-SQL](sql-data-sync-update-sync-schema.md) eller [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
