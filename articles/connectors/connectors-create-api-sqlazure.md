---
title: Ansluta till SQL Server, Azure SQL Database eller Azure SQL Managed Instance
description: Automatisera uppgifter för SQL-databaser lokalt eller i molnet med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: 2e06616914f1e78a71a540fbd64021c0e1bfcbab
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785981"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatisera arbetsflöden för en SQL-databas med hjälp av Azure Logic Apps

Den här artikeln visar hur du kan komma åt data i din SQL-databas från en logikapp med SQL Server anslutningsappen. På så sätt kan du automatisera uppgifter, processer eller arbetsflöden som hanterar dina SQL-data och -resurser genom att skapa logikappar. Anslutningsappen SQL Server fungerar [för SQL Server](/sql/sql-server/sql-server-technical-documentation) samt [för Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) och [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Du kan skapa logikappar som körs när de utlöses av händelser i din SQL-databas eller i andra system, till exempel Dynamics CRM Online. Dina logikappar kan också hämta, infoga och ta bort data tillsammans med SQL-frågor som körs och lagrade procedurer. Du kan till exempel skapa en logikapp som automatiskt söker efter nya poster i Dynamics CRM Online, lägger till objekt i SQL-databasen för nya poster och sedan skickar e-postaviseringar om de tillagda objekten.

Om logikappar är nytt för dig kan du läsa [Vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Snabbstart: Skapa din första logikapp.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Information om anslutningsappsspecifik teknisk information, begränsningar och kända problem finns på [SQL Server referenssidan för anslutningsappen.](/connectors/sql/)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [SQL Server databas,](/sql/relational-databases/databases/create-a-database) [Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md)eller [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  Dina tabeller måste ha data så att logikappen kan returnera resultat när åtgärder anropas. Om du använder Azure SQL Database kan du använda exempeldatabaser som ingår.

* Ditt SQL-servernamn, databasnamn, användarnamn och lösenord. Du behöver dessa autentiseringsuppgifter så att du kan auktorisera din logik för åtkomst till SQL-servern.

  * För lokala platser SQL Server du följande information i anslutningssträngen:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Du Azure SQL Database den här informationen i anslutningssträngen.
  
    Om du till exempel vill hitta den här strängen Azure Portal du öppna databasen. På databasmenyn väljer du antingen **Anslutningssträngar** eller **Egenskaper:**

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Beroende på om dina logikappar ska köras i globala Azure för flera innehavare eller i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)finns här andra krav för att ansluta till lokala SQL Server:

  * För logikappar i global Azure med flera klientorganisationer som ansluter till lokala SQL Server måste du ha den lokala [datagatewayen](../logic-apps/logic-apps-gateway-install.md) installerad på en lokal dator och en [datagatewayresurs](../logic-apps/logic-apps-gateway-connection.md)som redan har skapats i Azure .

  * För logikappar i en ISE som ansluter till lokala SQL Server och använder Windows-autentisering stöder ise-versionshanteringsappen SQL Server inte Windows-autentisering. Därför måste du fortfarande använda datagatewayen och den icke-ISE-SQL Server anslutningsappen. För andra autentiseringstyper behöver du inte använda datagatewayen och kan använda ise-versionsanslutningsappen.

* Logikappen där du behöver åtkomst till din SQL-databas. Om du vill starta logikappen med en SQL-utlösare behöver du [en tom logikapp.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Fortsätt nu med följande steg:

* [Ansluta till molnbaserade Azure SQL Database eller hanterad instans](#connect-azure-sql-db)
* [Ansluta till lokala SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Ansluta till Azure SQL Database eller hanterad instans

Om du vill Azure SQL Managed Instance utan att använda den lokala datagatewayen eller integreringstjänstmiljön måste du konfigurera den offentliga [slutpunkten på Azure SQL Managed Instance](../azure-sql/managed-instance/public-endpoint-configure.md). Den offentliga slutpunkten använder port 3342, så se till att du anger det här portnumret när du skapar anslutningen från logikappen.


Första gången du lägger [](#add-sql-trigger) till en SQL-utlösare eller [SQL-åtgärd,](#add-sql-action)och du inte tidigare har skapat en anslutning till databasen, uppmanas du att slutföra följande steg:

1. För **Autentiseringstyp** väljer du den autentisering som krävs och aktiveras på databasen i Azure SQL Database eller Azure SQL Managed Instance:

   | Autentisering | Description |
   |----------------|-------------|
   | [**Azure AD-integrerad**](../azure-sql/database/authentication-aad-overview.md) | – Stöder både ise- och ISE-SQL Server anslutningsappen. <p><p>– Kräver en giltig identitet i Azure Active Directory (Azure AD) som har åtkomst till din databas. <p>Mer information finns i de här ämnena: <p>- [Azure SQL säkerhetsöversikt – Autentisering](../azure-sql/database/security-overview.md#authentication) <br>- [Auktorisera databasåtkomst till Azure SQL – autentisering och auktorisering](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – Azure AD-integrerad autentisering](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server autentisering**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Stöder både ise- och ISE-SQL Server anslutningsappen. <p><p>– Kräver ett giltigt användarnamn och starkt lösenord som skapas och lagras i databasen. <p>Mer information finns i de här ämnena: <p>- [Azure SQL säkerhetsöversikt – Autentisering](../azure-sql/database/security-overview.md#authentication) <br>- [Auktorisera databasåtkomst till Azure SQL – autentisering och auktorisering](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Det här exemplet fortsätter med **Azure AD Integrated**:

   ![Skärmbild som visar fönstret "SQL Server"-anslutning med den öppna listan "Autentiseringstyp" och "Azure AD Integrated" valt.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. När du har **valt Azure AD Integrated (Azure AD-integrerad)** väljer du Sign In **(Logga in).** Beroende på om du använder Azure SQL Database eller Azure SQL Managed Instance väljer du dina autentiseringsuppgifter för autentisering.

1. Välj följande värden för databasen:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Servernamn** | Yes | Adressen för din SQL-server, till exempel `Fabrikam-Azure-SQL.database.windows.net` |
   | **Databasnamn** | Yes | Namnet på din SQL-databas, till exempel `Fabrikam-Azure-SQL-DB` |
   | **Tabellnamn** | Yes | Den tabell som du vill använda, till exempel `SalesLT.Customer` |
   ||||

   > [!TIP]
   > Om du vill ange databas- och tabellinformation har du följande alternativ:
   > 
   > * Hitta den här informationen i databasens anslutningssträng. I den här Azure Portal du till exempel leta upp och öppna databasen. På databasmenyn väljer du antingen **Anslutningssträngar** **eller Egenskaper**, där du hittar den här strängen:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Som standard filtreras tabeller i systemdatabaser ut, så de visas kanske inte automatiskt när du väljer en systemdatabas. Alternativt kan du ange tabellnamnet manuellt när du har valt **Ange anpassat värde** i databaslistan.
   >

   Det här exemplet visar hur dessa värden kan se ut:

   ![Skapa anslutning till SQL-databas](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Fortsätt nu med de steg som du inte har slutfört ännu i antingen Lägg till en [SQL-utlösare eller](#add-sql-trigger) [Lägg till en SQL-åtgärd](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Ansluta till lokala SQL Server

Första gången du lägger [](#add-sql-trigger) till en SQL-utlösare eller [SQL-åtgärd,](#add-sql-action)och du inte tidigare har skapat en anslutning till databasen, uppmanas du att slutföra följande steg:

1. För anslutningar till din lokala SQL-server som kräver den lokala datagatewayen kontrollerar du att du [har slutfört dessa krav.](#multi-tenant-or-ise)

   Annars visas inte datagatewayresursen i listan **Anslutningsgateway** när du skapar anslutningen.

1. För **Autentiseringstyp** väljer du den autentisering som krävs och är aktiverad på SQL Server:

   | Autentisering | Description |
   |----------------|-------------|
   | [**Windows-autentisering**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | – Stöder endast icke-ISE SQL Server anslutningsappen, vilket kräver en datagatewayresurs som tidigare har skapats i Azure för din anslutning, oavsett om du använder Azure för flera innehavare eller en ISE. <p><p>– Kräver ett giltigt Windows-användarnamn och lösenord för att bekräfta din identitet via ditt Windows-konto. <p>Mer information finns i [Windows-autentisering](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) |
   | [**SQL Server autentisering**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Stöder både ISE- och ISE-SQL Server anslutningsappen. <p><p>– Kräver ett giltigt användarnamn och starkt lösenord som skapas och lagras i din SQL Server. <p>Mer information finns i [SQL Server Autentisering](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Det här exemplet fortsätter med **Windows-autentisering:**

   ![Välj autentiseringstyp som ska användas](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Välj eller ange följande värden för din SQL-databas:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **SQL-servernamn** | Yes | Adressen för din SQL-server, till exempel `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL-databasens namn** | Yes | Namnet på din SQL Server databas, till exempel `Fabrikam-Azure-SQL-DB` |
   | **Användarnamn** | Yes | Ditt användarnamn för SQL-servern och databasen |
   | **Lösenord** | Yes | Ditt lösenord för SQL-servern och databasen |
   | **Prenumeration** |  Ja, för Windows-autentisering | Azure-prenumerationen för datagatewayresursen som du skapade tidigare i Azure |
   | **Anslutningsgateway** | Ja, för Windows-autentisering | Namnet på datagatewayresursen som du skapade tidigare i Azure <p><p>**Tips:** Om din gateway inte visas i listan kontrollerar du att du har [ställt in din gateway korrekt.](../logic-apps/logic-apps-gateway-connection.md) |
   |||

   > [!TIP]
   > Du hittar den här informationen i databasens anslutningssträng:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Det här exemplet visar hur dessa värden kan se ut:

   ![Skapa SQL Server anslutningen har slutförts](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. När du är klar väljer du **Skapa**.

1. Fortsätt nu med de steg som du inte har slutfört ännu i antingen Lägg till en [SQL-utlösare](#add-sql-trigger) eller [Lägg till en SQL-åtgärd](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Lägga till en SQL-utlösare

1. I [Azure Portal](https://portal.azure.com) eller i Visual Studio skapar du en tom logikapp som öppnar Logikappdesignern. Det här exemplet fortsätter med Azure Portal.

1. I sökrutan i designern anger du `sql server` . I listan med utlösare väljer du den SQL-utlösare som du vill använda. I det här exemplet **används utlösaren När ett objekt** skapas.

   ![Välj utlösaren "När ett objekt skapas"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Om du ansluter till DIN SQL-databas för första gången uppmanas du att [skapa din SQL Database-anslutning nu.](#create-connection) När du har skapat den här anslutningen kan du fortsätta med nästa steg.

1. I utlösaren anger du intervall och frekvens för hur ofta utlösaren kontrollerar tabellen.

1. Om du vill lägga till andra tillgängliga egenskaper för den här utlösaren öppnar du **listan Lägg till ny parameter.**

   Den här utlösaren returnerar bara en rad från den valda tabellen och inget annat. Om du vill utföra andra uppgifter fortsätter [](../connectors/apis-list.md) du genom att lägga till antingen en [SQL-anslutningsåtgärd](#add-sql-action) eller en annan åtgärd som utför nästa uppgift som du vill ha i logikappens arbetsflöde.

   Om du till exempel vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och sedan skicka e-postaviseringar. Mer information om andra tillgängliga åtgärder för den här anslutningen finns på [anslutningsappens referenssida.](/connectors/sql/)

1. Välj Spara i **designerverktygsfältet.**

   Även om det här steget automatiskt aktiverar och publicerar logikappen live i Azure, är den enda åtgärden som logikappen för närvarande vidtar att kontrollera databasen baserat på det angivna intervallet och frekvensen.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Utlösa upprepningsförskjutning och förskjutning

Anslutningsbaserade utlösare där du först behöver skapa en anslutning, till exempel SQL-utlösaren, skiljer sig från inbyggda [](../connectors/connectors-native-recurrence.md)utlösare som körs inbyggt i Azure Logic Apps, till exempel upprepningsutlösaren . I återkommande anslutningsbaserade utlösare är upprepningsschemat inte den enda drivrutinen som styr körningen, och tidszonen avgör bara den första starttiden. Efterföljande körningar beror på upprepningsschemat,  den senaste utlösarkörningen och andra faktorer som kan orsaka drifttidsavdrift eller generera oväntat beteende, till exempel att inte upprätthålla det angivna schemat när sommartid startar och slutar. För att se till att upprepningstiden inte ändras när DST har verkställts justerar du upprepningen manuellt så att logikappen fortsätter att köras vid den förväntade tiden. Annars flyttas starttiden en timme framåt när DST startar och en timme bakåt när DST avslutas. Mer information finns i [Upprepning för anslutningsbaserade utlösare.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Lägga till en SQL-åtgärd

I det här exemplet börjar logikappen med [upprepningsutlösaren](../connectors/connectors-native-recurrence.md)och anropar en åtgärd som hämtar en rad från en SQL-databas.

1. I [Azure Portal](https://portal.azure.com) eller i Visual Studio du logikappen i Logic App Designer. Det här exemplet fortsätter Azure Portal.

1. Under utlösaren eller åtgärden där du vill lägga till SQL-åtgärden väljer du **Nytt steg.**

   ![Lägga till en åtgärd i logikappen](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Om du vill lägga till en åtgärd mellan befintliga steg flyttar du musen över anslutningspilen. Välj plustecknet ( **+** ) som visas och välj sedan Lägg till en **åtgärd**.

1. Under **Välj en åtgärd** går du till sökrutan och anger `sql server` . Välj den SQL-åtgärd som du vill använda i åtgärdslistan. I det här exemplet **används åtgärden Hämta** rad, som hämtar en enda post.

   ![Välj SQL-åtgärd för att hämta rad](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Om du ansluter till DIN SQL-databas för första gången uppmanas du att [skapa din SQL Database-anslutning nu.](#create-connection) När du har skapat den här anslutningen kan du fortsätta med nästa steg.

1. Välj **tabellnamnet**, som finns i `SalesLT.Customer` det här exemplet. Ange **rad-ID för** den post som du vill använda.

   ![Välj tabellnamn och ange rad-ID](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Den här åtgärden returnerar bara en rad från den valda tabellen, inget annat. Om du vill visa data på den här raden kan du lägga till andra åtgärder som skapar en fil som innehåller fälten från den returnerade raden och lagra filen i ett molnlagringskonto. Mer information om andra tillgängliga åtgärder för den här anslutningen finns på [anslutningsappens referenssida.](/connectors/sql/)

1. När du är klar väljer du Spara i **designerverktygsfältet.**

   Det här steget aktiverar och publicerar automatiskt logikappen live i Azure.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Hantera bulkdata

Ibland måste du arbeta med resultatuppsättningar så stora att anslutningsappen inte returnerar alla resultat samtidigt, eller så vill du ha bättre kontroll över storleken och strukturen för dina resultatuppsättningar. Här är några sätt att hantera så stora resultatmängder:

* Aktivera sidnumrering för att hjälpa dig att hantera resultat som *mindre uppsättningar.* Mer information finns i Hämta [massdata, poster och objekt med hjälp av sidnumrering.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md) Mer information finns i [SQL-sidnumrering för massdataöverföring med Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Skapa en [*lagrad*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) procedur som ordnar resultatet på det sätt som du vill. SQL-anslutningsappen innehåller många serverfunktioner som du kan komma åt med hjälp av Azure Logic Apps så att du enklare kan automatisera affärsuppgifter som fungerar med SQL-databastabeller.

  När du hämtar eller infogar flera rader kan logikappen iterera genom dessa rader med hjälp av en [*until-loop inom*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dessa [gränser.](../logic-apps/logic-apps-limits-and-config.md) Men när logikappen måste arbeta med postuppsättningar som är så stora, till exempel tusentals eller miljontals rader, att du vill minimera kostnaderna från anrop till databasen.

  Om du vill ordna resultaten på det sätt som du vill kan du skapa en lagrad procedur som körs i din SQL-instans och använder **instruktionen SELECT - ORDER BY.** Den här lösningen ger dig mer kontroll över resultatets storlek och struktur. Logikappen anropar den lagrade proceduren med hjälp SQL Server anslutningsappens **åtgärd Kör lagrad** procedur. Mer information finns i [SELECT - ORDER BY-satsen](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > SQL-anslutningsappen har en tidsgräns för lagrade procedurer som är [mindre än 2 minuter](/connectors/sql/#known-issues-and-limitations). Vissa lagrade procedurer kan ta längre tid än den här gränsen att slutföra, vilket orsakar `504 Timeout` ett fel. Du kan lösa det här problemet genom att använda en SQL-slutförandeutlösare, intern SQL-direktfråga, en tillståndstabell och jobb på serversidan.
  > 
  > För den här uppgiften kan du använda [Azure Elastic Job Agent](../azure-sql/database/elastic-jobs-overview.md) för att [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). För [SQL Server lokalt](/sql/sql-server/sql-server-technical-documentation) och [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)kan du använda [SQL Server Agent](/sql/ssms/agent/sql-server-agent). Mer information finns i [Hantera timeouter för långvariga lagrade procedurer i SQL-anslutningsappen för Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Hantera dynamiska bulkdata

När du anropar en lagrad procedur med SQL Server anslutningsappen är returnerade utdata ibland dynamiska. I det här scenariot följer du dessa steg:

1. I [Azure Portal](https://portal.azure.com)du logikappen i Logikappdesignern.

1. Visa utdataformatet genom att utföra en testkörning. Kopiera och spara dina exempelutdata.

1. Välj Nytt steg under åtgärden där du anropar den lagrade proceduren **i designern.**

1. Under **Välj en åtgärd** hittar och väljer du åtgärden [**Parsa JSON.**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)

1. I åtgärden **Parsa JSON väljer** du **Använd exempelnyttolast för att generera schemat**.

1. I rutan **Enter or paste a sample JSON payload (Ange** eller klistra in en JSON-exempelnyttolast) klistrar du in dina exempelutdata och väljer **Done (Klar).**

   > [!NOTE]
   > Om du får ett felmeddelande Logic Apps inte kan generera ett schema kontrollerar du att exempelutdatans syntax är korrekt formaterad. Om du fortfarande inte kan generera schemat går du till **rutan Schema** och anger schemat manuellt.

1. Välj Spara i **designerverktygsfältet.**

1. Om du vill referera till JSON-innehållsegenskaperna klickar du i redigeringsrutorna där du vill referera till dessa egenskaper så att listan med dynamiskt innehåll visas. I listan, under rubriken [**Parsa JSON,**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) väljer du datatoken för de JSON-innehållsegenskaper som du vill använda.

## <a name="troubleshoot-problems"></a>Felsöka problem

<a name="connection-problems"></a>

### <a name="connection-problems"></a>Anslutningsproblem

Anslutningsproblem kan ofta inträffa, så om du vill felsöka och lösa dessa typer av problem kan du läsa [Lösa anslutningsfel för att SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Här är några exempel:

* `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

* `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

* `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Information om anslutningsappen

Teknisk information om utlösare, åtgärder och gränser för den här anslutningsappen finns på anslutningsappens [referenssida](/connectors/sql/), som genereras från Swagger-beskrivningen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [andra anslutningsappar för Azure Logic Apps](../connectors/apis-list.md)
