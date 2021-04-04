---
title: Använd Visual Studio Code för att ansluta och fråga
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du ansluter till Azure SQL Database eller SQL-hanterad instans i Azure med hjälp av Visual Studio Code. Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor och redigera data.
keywords: ansluta till sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: f823b6d04a217328fe2e825e64906460cd9cbae9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92672484"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query"></a>Snabb start: Använd Visual Studio Code för att ansluta och fråga 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Visual Studio Code](https://code.visualstudio.com/docs) är en grafisk kodredigerare för Linux, macOS och Windows. Det stöder tillägg, inklusive [MSSQL-tillägget](https://aka.ms/mssql-marketplace) för att skicka frågor till en SQL Server instans, Azure SQL Database, en hanterad Azure SQL-instans och en databas i Azure Synapse Analytics. I den här snabb starten ska du använda Visual Studio Code för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans och sedan köra Transact-SQL-uttryck för att fråga, infoga, uppdatera och ta bort data.

## <a name="prerequisites"></a>Förutsättningar

- En databas i Azure SQL Database eller Azure SQL-hanterad instans. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  | Action | Azure SQL Database | Hanterad Azure SQL-instans |
  |:--- |:--- |:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) |
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) |
  | Konfigurera | [Regel för IP-brandvägg på server nivå](firewall-create-server-level-portal-quickstart.md))| [Anslutning från en virtuell dator (VM)](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från lokal plats](../managed-instance/point-to-site-p2s-configure.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md)
  |||Återställa eller importera Adventure Works från en [BACPAC](database-import.md) -fil från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en SQL-hanterad instans måste du antingen importera Adventure Works-databasen till en instans databas eller ändra skripten i den här artikeln för att använda Wide World imports-databasen.

## <a name="install-visual-studio-code"></a>Installera Visual Studio Code

Kontrollera att du har installerat den senaste versionen av [Visual Studio Code](https://code.visualstudio.com/Download) och har läst in [mssql-tillägget](https://aka.ms/mssql-marketplace). Anvisningar om hur du installerar MSSQL-tillägget finns i [Installera Visual Studio Code](/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) och [MSSQL för Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Konfigurera Visual Studio Code

### <a name="macos"></a>**macOS**

För macOS måste du installera OpenSSL, som är en förutsättning för .NET Core som MSSQL-tillägget använder. Ange följande kommandon för att installera **brew** och **OpenSSL**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Ingen särskild konfiguration behövs.

### <a name="windows"></a>**Windows**

Ingen särskild konfiguration behövs.

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta den anslutnings information som du behöver för att ansluta till Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **server namnet** för SQL Database eller det fullständigt kvalificerade Server namnet bredvid **värd** för en SQL-hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

## <a name="set-language-mode-to-sql"></a>Ange språkläge till SQL

Ställ in språkläget på **SQL** i Visual Studio Code för att aktivera mssql-kommandon och T-SQL IntelliSense.

1. Öppna ett Visual Studio Code-fönster.

2. Tryck på **CTRL** + **N**. En ny oformaterad textfil öppnas.

3. Välj **Oformaterad text** nere till höger i statusfältet.

4. Välj **SQL** i den nedrullningsbara menyn **Välj språkläge** som öppnas.

## <a name="connect-to-your-database"></a>Ansluta till databasen

Använd Visual Studio Code för att upprätta en anslutning till servern.

> [!IMPORTANT]
> Kontrollera att du har din server- och inloggningsinformation till hands innan du fortsätter. Om du ändrar fokus från Visual Studio Code när du har börjat ange information om anslutningsprofilen måste du börja om med att skapa profilen.

1. I Visual Studio Code trycker du på **Ctrl+Shift+P** (eller **F1**) för att öppna kommandopaletten.

2. Välj **MS SQL:Anslut** och välj **Retur**.

3. Välj **Skapa anslutningsprofil**.

4. Följ anvisningarna för att ange anslutningsegenskaper för den nya profilen. När du har angett varje värde så väljer du **Retur** för att fortsätta.

   | Egenskap       | Föreslaget värde | Beskrivning |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Ungefär så här: **mynewserver20170313.database.windows.net**. |
   | **Databasnamn** | mySampleDatabase | Databasen du ska ansluta till. |
   | **Autentisering** | SQL-inloggning| Den här självstudien använder SQL-autentisering. |
   | **Användarnamn** | Användarnamn | Användarnamnet från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord (SQL-inloggning)** | Lösenord | Lösenordet från det serveradministratörskonto som användes när servern skapades. |
   | **Spara lösenordet?** | Ja eller nej | Välj **Ja** om du inte vill ange lösen ordet varje tillfälle. |
   | **Ange ett namn för den här profilen** | Ett profilnamn, t.ex. **mySampleProfile** | En sparad profil förbättrar anslutningen på efterföljande inloggningar. |

   Om det lyckas visas ett meddelande om att din profil har skapats och anslutits.

## <a name="query-data"></a>Söka i data

Kör följande [SELECT](/sql/t-sql/queries/select-transact-sql) Transact-SQL-instruktion för att fråga efter de 20 främsta produkterna per kategori.

1. Klistra in följande SQL-fråga i redigeringsfönstret.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Tryck på **CTRL** + **Shift** + **E** för att köra frågan och Visa resultat från `Product` `ProductCategory` tabellerna och.

    ![Fråga för att hämta data från 2 tabeller](./media/connect-query-vscode/query.png)

## <a name="insert-data"></a>Infoga data

Kör följande [INSERT](/sql/t-sql/statements/insert-transact-sql) Transact-SQL-instruktion för att lägga till en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den tidigare frågan med denna.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Tryck på **CTRL** + **Shift** + **E** för att infoga en ny rad i `Product` tabellen.

## <a name="update-data"></a>Uppdatera data

Kör följande [UPDATE](/sql/t-sql/queries/update-transact-sql) Transact-SQL-instruktion för att uppdatera den tillagda produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **CTRL** + **Shift** + **E** för att uppdatera den angivna raden i `Product` tabellen.

## <a name="delete-data"></a>Ta bort data

Kör följande [DELETE](/sql/t-sql/statements/delete-transact-sql) Transact-SQL-instruktion för att ta bort den nya produkten.

1. Ersätt den föregående frågan med denna:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Tryck på **CTRL** + **Shift** + **E** för att ta bort den angivna raden i `Product` tabellen.

## <a name="next-steps"></a>Nästa steg

- Information om hur du ansluter och frågar med hjälp av SQL Server Management Studio finns i [snabb start: använd SQL Server Management Studio för att ansluta till en databas i Azure SQL Database och fråga efter data](connect-query-ssms.md).
- Information om hur du ansluter och frågar med hjälp av Azure Portal finns i [snabb start: Använd SQL-Frågeredigeraren i Azure Portal för att ansluta och fråga efter data](connect-query-portal.md).
- En artikel från MSDN-magazine om hur du använder Visual Studio Code finns i [Skapa en IDE-databas med MSSQL-tillägget blogginlägg](/archive/msdn-magazine/2017/june/data-points-visual-studio-code-create-a-database-ide-with-mssql-extension).