---
title: Använd Azure Functions för att utföra en databas rensnings uppgift
description: Använd Azure Functions för att schemalägga en aktivitet som ansluter till Azure SQL Database för att regelbundet rensa rader.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88207255"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Använd Azure Functions för att ansluta till en Azure SQL Database

Den här artikeln visar hur du använder Azure Functions för att skapa ett schemalagt jobb som ansluter till en Azure SQL Database eller en Azure SQL-hanterad instans. Funktions koden rensar rader i en tabell i databasen. Den nya C#-funktionen skapas baserat på en fördefinierad timer-mall i Visual Studio 2019. För att stödja det här scenariot måste du också ange en databas anslutnings sträng som en app-inställning i Function-appen. För Azure SQL-hanterad instans måste du [Aktivera den offentliga slut punkten](../azure-sql/managed-instance/public-endpoint-configure.md) för att kunna ansluta från Azure Functions. I det här scenariot används en Mass åtgärd mot databasen. 

Om det är första gången du arbetar med C#-funktioner bör du läsa [referens för Azure Functions C#-utvecklare](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Förutsättningar

+ Slutför stegen i artikeln [skapa din första funktion med Visual Studio](functions-create-your-first-function-visual-studio.md) för att skapa en lokal Function-app som är riktad mot version 2. x eller en senare version av körnings miljön. Du måste också ha publicerat projektet till en Function-app i Azure.

+ Den här artikeln visar ett Transact-SQL-kommando som kör en Mass rensnings åtgärd i **SalesOrderHeader** -tabellen i AdventureWorksLT-exempel databasen. Skapa AdventureWorksLT-exempel databasen genom att följa anvisningarna i artikeln [skapa en databas i Azure SQL Database med hjälp av Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

+ Du måste lägga till en [brand Väggs regel på server nivå](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) för den offentliga IP-adressen för den dator som du använder för den här snabb starten. Den här regeln krävs för att kunna komma åt SQL Database-instansen från den lokala datorn.  

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Du måste hämta anslutnings strängen för databasen som du skapade när du slutförde [skapandet av en databas i Azure SQL Database med hjälp av Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **SQL-databaser** på den vänstra menyn och välj din databas på sidan SQL- **databaser** .

1. Välj **anslutnings strängar** under **Inställningar** och kopiera den fullständiga **ADO.net** -anslutningssträngen. För Azure SQL Managed instance Copy-anslutningssträng för offentlig slut punkt.

    ![Exempel på ADO.NET-anslutningssträng.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Ange anslutningssträngen

En funktionsapp är värd för körningen av dina funktioner i Azure. Som bästa säkerhets praxis lagrar du anslutnings strängar och andra hemligheter i dina funktioner i appens inställningar. Om du använder program inställningar förhindras oavsiktligt avslöjande av anslutnings strängen med koden. Du kan komma åt appinställningar för din Function-app direkt från Visual Studio.

Du måste ha publicerat din app tidigare på Azure. [Publicera din Function-app till Azure](functions-develop-vs.md#publish-to-azure)om du inte redan gjort det.

1. I Solution Explorer högerklickar du på programmets Function-projekt och väljer sedan **publicera**  >  **Redigera Azure App Service inställningar**. Välj **Lägg till inställning**, i **nytt inställnings namn för appen**, Skriv `sqldb_connection` och välj **OK**.

    ![Program inställningar för Function-appen.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. I den nya **sqldb_connection** -inställningen klistrar du in anslutnings strängen som du kopierade i föregående avsnitt i det **lokala** fältet och ersätter `{your_username}` `{your_password}` plats hållarna med verkliga värden. Välj **Infoga värde från lokal** för att kopiera det uppdaterade värdet till fältet **Remote** och välj sedan **OK**.

    ![Lägg till inställningen för SQL-anslutningssträng.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Anslutnings strängarna lagras krypterade i Azure (**fjärr**). För att förhindra att hemligheter används bör local.settings.jspå projekt filen (**lokal**) uteslutas från käll kontroll, till exempel med hjälp av en. gitignore-fil.

## <a name="add-the-sqlclient-package-to-the-project"></a>Lägg till SqlClient-paketet i projektet

Du måste lägga till NuGet-paketet som innehåller SqlClient-biblioteket. Det här data åtkomst biblioteket krävs för att ansluta till SQL Database.

1. Öppna det lokala projektet för Function-appen i Visual Studio 2019.

1. I Solution Explorer högerklickar du på programmets Function-projekt och väljer **Hantera NuGet-paket**.

1. På fliken **Bläddra** söker du efter ```System.Data.SqlClient``` och markerar det när du hittar det.

1. På sidan **system. data. SqlClient** väljer du version `4.5.1` och klickar sedan på **Installera**.

1. När installationen har slutförts granskar du ändringarna och klickar på **OK** för att stänga fönstret **Förhandsgranska**.

1. Om ett fönster för **Godkännande av licens** visas klickar du på **Jag accepterar**.

Nu kan du lägga till C#-funktions koden som ansluter till din SQL Database.

## <a name="add-a-timer-triggered-function"></a>Skapa en timerutlöst funktion

1. I Solution Explorer högerklickar du på programmets Function-projekt och väljer **Lägg till**  >  **ny Azure-funktion**.

1. När du har valt mallen **Azure Functions** väljer du `DatabaseCleanup.cs` **Lägg till** det nya objektet.

1. I dialog rutan **ny Azure Function** väljer du **timer-utlösare** och sedan **OK**. Den här dialog rutan skapar en kod fil för den timer-aktiverade funktionen.

1. Öppna den nya kod filen och Lägg till följande using-uttryck högst upp i filen:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Ersätt den befintliga `Run` funktionen med följande kod:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Den här funktionen körs var 15: e sekund för att uppdatera `Status` kolumnen baserat på speditions datum. Mer information om timer-utlösaren finns i [timer-utlösare för Azure Functions](functions-bindings-timer.md).

1. Tryck på **F5** för att starta Function-appen. Fönstret [Azure Functions Core tools](functions-develop-local.md) körning öppnas bakom Visual Studio.

1. Vid 15 sekunder efter starten körs funktionen. Titta på utdata och anteckna antalet rader som har uppdaterats i tabellen **SalesOrderHeader** .

    ![Visa funktions loggarna.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Vid den första körningen bör du uppdatera 32 rader med data. Följande kör uppdatering av inga data rader, om du inte gör ändringar i SalesOrderHeader-tabellens data så att fler rader väljs av `UPDATE` instruktionen.

Om du planerar att [publicera den här funktionen](functions-develop-vs.md#publish-to-azure)måste du komma ihåg att ändra `TimerTrigger` attributet till ett mer rimligt [cron-schema](functions-bindings-timer.md#ncrontab-expressions) än var 15: e sekund.

## <a name="next-steps"></a>Nästa steg

Nu ska du lära dig hur du använder. Funktioner med Logic Apps för att integrera med andra tjänster.

> [!div class="nextstepaction"]
> [Skapa en funktion som kan integreras med Logic Apps](functions-twitter-email.md)

Mer information om funktioner finns i följande artiklar:

+ [Azure Functions, info för utvecklare](functions-reference.md)  
  Info för programmerare om att koda funktioner och definiera utlösare och bindningar.
+ [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.  
