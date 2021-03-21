---
title: 'Utforma din första Relations databas C #'
description: Lär dig att utforma din första Relations databas i Azure SQL Database med C# med hjälp av ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 4b3235f457f1c6475c18045886c49d3dd2ca2242
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92671185"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Självstudie: utforma en Relations databas i Azure SQL Database C&#x23; och ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database är en relationsdatabas-som-tjänst (DBaaS) som bygger på Microsoft Cloud (Azure). I de här självstudierna får du lära dig att använda Azure-portalen och ADO.NET med Visual Studio för att:

> [!div class="checklist"]
>
> * Skapa en databas med hjälp av Azure Portal
> * Konfigurera en IP-brandväggsregel på servernivå med hjälp av Azure-portalen
> * Ansluta till databasen med ADO.NET och Visual Studio
> * Skapa tabeller med ADO.NET
> * Infoga, uppdatera och ta bort data med ADO.NET
> * Fråga efter data med ADO.NET

* Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.

> [!TIP]
> Följande Microsoft Learn-modul hjälper dig att lära dig kostnads fritt hur du [utvecklar och konfigurerar ett ASP.NET-program som skickar frågor till en Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), inklusive skapandet av en enkel databas.

## <a name="prerequisites"></a>Förutsättningar

En installation av [Visual Studio 2019](https://www.visualstudio.com/downloads/) eller senare.

## <a name="create-a-blank-database-in-azure-sql-database"></a>Skapa en tom databas i Azure SQL Database

En databas i Azure SQL Database skapas med en definierad uppsättning beräknings-och lagrings resurser. Databasen skapas i en Azure- [resurs grupp](../../active-directory-b2c/overview.md) och hanteras med hjälp av en [logisk SQL-Server](logical-servers.md).

Följ de här stegen för att skapa en tom databas.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Välj **Databaser** i avsnittet Azure Marketplace på sidan **Nytt** och klicka sedan på **SQL Database** i avsnittet **Aktuellt**.

   ![skapa tom databas](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. Fyll i **SQL Database** formuläret med följande information, som du ser på föregående bild:

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Databasnamn** | *yourDatabase* | För giltiga databasnamn, se [databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Prenumeration** | *yourSubscription*  | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
    | **Resursgrupp** | *yourResourceGroup* | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
    | **Välj källa** | Tom databas | Anger att en tom databas ska skapas. |

4. Klicka på **Server** för att använda en befintlig server eller skapa och konfigurera en ny server. Välj antingen en befintlig server eller klicka på **Skapa en ny server** och fyll i följande information i formuläret **Ny server**:

    | Inställning       | Föreslaget värde | Beskrivning |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servernamn** | Valfritt globalt unikt namn | Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming). |
    | **Inloggning för serveradministratör** | Valfritt giltigt namn | För giltiga inloggningsnamn, se [Databasidentifierare](/sql/relational-databases/databases/database-identifiers). |
    | **Lösenord** | Valfritt giltigt lösenord | Lösen ordet måste innehålla minst åtta tecken och måste innehålla tecken från tre av följande kategorier: versaler, gemener, siffror och icke-alfanumeriska tecken. |
    | **Plats** | Valfri giltig plats | För information om regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). |

    ![skapa databas-server](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Klicka på **Välj**.
6. Klicka på **Prisnivå** för att ange tjänstnivå, antalet DTU:er eller V-kärnor och mängden lagring. Du kan undersöka alternativen för antalet DTU:er/V-kärnor och lagringsutrymme som du har tillgång till på varje tjänstnivå.

    När du har valt tjänstnivå, antalet DTU:er eller virtuella kärnor samt mängden lagring klickar du på **Använd**.

7. Ange en **sortering** för den tomma databasen (använd standardvärdet för de här självstudierna). Mer information om sorteringar finns i [Sorteringar](/sql/t-sql/statements/collations).

8. Nu när du har fyllt i **SQL Database**-formuläret klickar du på **Skapa** så att databasen etableras. Det här steget kan ta några minuter.

9. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Skärm bild som visar meddelanden i Azure Portal med pågående distribution.](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Skapa en IP-brandväggsregel på servernivå

SQL Database skapar en IP-brandvägg på server nivå. Den här brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern såvida inte en brandväggsregel tillåter deras IP-adresser genom brandväggen. Om du vill aktivera extern anslutning till databasen måste du först lägga till en IP-brandväggsregel för din IP-adress (eller IP-adressintervall). Följ de här stegen för att skapa en [regel för IP-brandvägg på server nivå](firewall-configure.md).

> [!IMPORTANT]
> SQL Database kommunicerar via port 1433. Om du försöker ansluta till den här tjänsten från ett företagsnätverk kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till databasen om administratören öppnar port 1433.

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och sedan på *YourDatabase* på sidan **SQL-databaser** . Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade **servernamnet** (till exempel *yourserver.database.windows.net*) tillsammans med alternativ för ytterligare konfiguration.

2. Kopiera det här fullständigt kvalificerade servernamnet. Du behöver det när du ansluter till servern och dess databaser från SQL Server Management Studio.

   ![servernamn](./media/design-first-database-csharp-tutorial/server-name.png)

3. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet. Sidan **brand Väggs inställningar** för servern öppnas.

   ![IP-brandväggsregel på servernivå](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Klicka på **Lägg till klient-IP** i verktygsfältet och lägg till din aktuella IP-adress i en ny IP-brandväggsregel. Med en IP-brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

5. Klicka på **Spara**. En regel för IP-brandvägg på server nivå skapas för den aktuella IP-adressen som öppnar port 1433 på servern.

6. Klicka på **OK** och stäng sedan sidan **Brandväggsinställningar**.

Din IP-adress kan nu passera genom IP-brandväggen. Nu kan du ansluta till din databas med SQL Server Management Studio eller något annat verktyg som du själv väljer. Se till att använda serveradmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst via IP-brandväggen för SQL Database aktiverad för alla Azure-tjänster. Klicka på **av** på den här sidan om du vill inaktivera åtkomst för alla Azure-tjänster.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du lärt dig grundläggande databasuppgifter, till exempel att skapa en databas och tabeller, ansluta till databasen, läsa in data och köra frågor. Du har lärt dig att:

> [!div class="checklist"]
>
> * Skapa en databas med hjälp av Azure Portal
> * Konfigurera en IP-brandväggsregel på servernivå med hjälp av Azure-portalen
> * Ansluta till databasen med ADO.NET och Visual Studio
> * Skapa tabeller med ADO.NET
> * Infoga, uppdatera och ta bort data med ADO.NET
> * Fråga efter data med ADO.NET

Gå vidare till nästa självstudie om du vill lära dig mer om migrering av data.

> [!div class="nextstepaction"]
> [Migrera SQL Server till Azure SQL Database offline med DMS](../../dms/tutorial-sql-server-to-azure-sql.md)