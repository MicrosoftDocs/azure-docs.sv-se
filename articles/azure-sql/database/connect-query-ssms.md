---
title: 'SSMS: Anslut och fråga efter data'
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du ansluter till Azure SQL Database eller SQL-hanterad instans med SQL Server Management Studio (SSMS). Kör sedan Transact-SQL-uttryck (T-SQL) för att skicka frågor mot och redigera data.
keywords: anslut till sql database, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/28/2020
ms.openlocfilehash: 60977b9388af3a93d0ebbbc6aad50628b79e0e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91598270"
---
# <a name="quickstart-use-ssms-to-connect-to-and-query-azure-sql-database-or-azure-sql-managed-instance"></a>Snabb start: Använd SSMS för att ansluta till och fråga Azure SQL Database eller Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I den här snabb starten får du lära dig hur du använder SQL Server Management Studio (SSMS) för att ansluta till Azure SQL Database eller Azure SQL-hanterad instans och köra vissa frågor.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten krävs följande objekt:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).

- En databas i Azure SQL Database. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

  | Action | SQL Database | SQL-hanterad instans | SQL Server på virtuella Azure-datorer |
  |:--- |:--- |:---|:---|
  | Skapa| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurera | [IP-brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md)| [Anslutning från en virtuell dator](../managed-instance/connect-vm-instance-configure.md)|
  |||[Anslutning från en lokal plats](../managed-instance/point-to-site-p2s-configure.md) | [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Läsa in data|AdventureWorks som lästs in per snabbstart|[Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Återställa Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Återställa eller importera Adventure Works från [BACPAC](database-import.md) -filen från [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Skripten i den här artikeln skrivs för att använda Adventure Works-databasen. Med en hanterad instans måste du antingen importera Adventure Works-databasen till en instansdatabas eller ändra skripten i den här artikeln om du vill använda Wide World Importers-databasen.

Om du bara vill köra vissa ad hoc-frågor utan att installera SSMS, se [snabb start: använd Azure portalens Frågeredigeraren för att fråga en databas i Azure SQL Database](connect-query-portal.md).

## <a name="get-server-connection-information"></a>Hämta information om Server anslutning

Hämta anslutnings informationen du behöver för att ansluta till databasen. Du behöver det fullständigt kvalificerade [Server](logical-servers.md) namnet eller värd namnet, databas namnet och inloggnings informationen för att slutföra den här snabb starten.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till **databasen** eller den **hanterade instans** som du vill fråga.

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för databasen i SQL Database eller det fullständigt kvalificerade Server namnet (eller IP-adressen) bredvid **värd** för din hanterade instans i SQL-hanterad instans eller SQL Server instansen på den virtuella datorn. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

> [!NOTE]
> Anslutnings information för SQL Server på den virtuella Azure-datorn finns i [Anslut till SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)

## <a name="connect-to-your-database"></a>Ansluta till databasen

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

I SSMS ansluter du till servern.

> [!IMPORTANT]
> En Server lyssnar på port 1433. Om du vill ansluta till en server från en företags brand vägg måste den här porten vara öppen.

1. Öppna SSMS.

2. Dialogrutan **Anslut till server** visas. Ange följande information:

   | Inställning      | Föreslaget värde    | Beskrivning |
   | ------------ | ------------------ | ----------- |
   | **Servertyp** | Databasmotor | Obligatoriskt värde. |
   | **Servernamn** | Fullständigt kvalificerat servernamn | Något som liknar: **servername.Database.Windows.net**. |
   | **Autentisering** | SQL Server-autentisering | Den här självstudien använder SQL-autentisering. |
   | **Inloggning** | Serveradministratörskontots användar-ID | Användar-ID från det serveradministratörskonto som användes när servern skapades. |
   | **Lösenord** | Lösenord för serveradministratörskontot | Lösenord från det serveradministratörskonto som användes när servern skapades. |
   ||||

   ![Anslut till server](./media/connect-query-ssms/connect.png)  

> [!NOTE]
> I den här självstudien används SQL Server autentisering.

3. Välj **Alternativ** i dialogrutan **Anslut till server**. I den nedrullningsbara menyn **Anslut till databas** väljer du **mySampleDatabase**. När du har slutfört snabb starten i [avsnittet krav](#prerequisites) skapas en AdventureWorksLT-databas med namnet mySampleDatabase. Om din arbets kopia av AdventureWorks-databasen har ett annat namn än mySampleDatabase, väljer du den i stället.

   ![ansluta till databas på server](./media/connect-query-ssms/options-connect-to-db.png)  

4. Välj **Anslut**. Object Explorer-fönstret öppnas.

5. Om du vill visa databasens objekt expanderar du **Databaser** och sedan databasnoden.

   ![mySampleDatabase-objekt](./media/connect-query-ssms/connected.png)  

## <a name="query-data"></a>Söka i data

Kör den här [SELECT](/sql/t-sql/queries/select-transact-sql/) Transact-SQL-koden för att fråga efter de 20 viktigaste produkterna efter kategori.

1. I Object Explorer högerklickar du på **mySampleDatabase**. Välj sedan **Ny fråga**. Ett nytt frågefönster öppnas som är anslutet till din databas.

2. Klistra in följande SQL-fr åga i frågefönstret:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. I verktygsfältet väljer du **Kör** för att köra frågan och hämta data från `Product` `ProductCategory` tabellerna och.

    ![fråga för att hämta data från tabell produkter och ProductCategory](./media/connect-query-ssms/query2.png)

### <a name="insert-data"></a>Infoga data

Kör den här [INSERT](/sql/t-sql/statements/insert-transact-sql/) Transact-SQL-koden för att skapa en ny produkt i tabellen `SalesLT.Product`.

1. Ersätt den tidigare frågan med denna.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Välj **Kör** för att infoga en ny rad i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

#### <a name="view-the-result"></a>Visa resultatet

1. Ersätt den tidigare frågan med denna.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Välj **Kör**. Följande resultat visas.

   ![resultat av produkt tabell fråga](./media/connect-query-ssms/result.png)

### <a name="update-data"></a>Uppdatera data

Kör den här [uppdateringen](/sql/t-sql/queries/update-transact-sql) av Transact-SQL-koden för att ändra din nya produkt.

1. Ersätt föregående fråga med denna som returnerar den nya posten som skapades tidigare:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

### <a name="delete-data"></a>Ta bort data

Kör den här [DELETE](/sql/t-sql/statements/delete-transact-sql/) Transact-SQL-koden för att ta bort din nya produkt.

1. Ersätt den tidigare frågan med denna.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Välj **Kör** för att uppdatera den angivna raden i `Product`-tabellen. Fönstret **Meddelanden** visas **(1 rad påverkas)**.

## <a name="next-steps"></a>Nästa steg

- Mer information om SSMS finns i [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms/).
- Information om hur du ansluter och frågar med hjälp av Azure Portal finns i [Ansluta och fråga med Azure Portal SQL-frågeredigeraren](connect-query-portal.md).
- Mer information om att ansluta och ställa frågor med Visual Studio Code finns i [Ansluta och fråga med Visual Studio Code](connect-query-vscode.md).
- Mer information om att ansluta och ställa frågor med .NET finns i [Ansluta och fråga med .NET](connect-query-dotnet-visual-studio.md).
- Mer information om att ansluta och ställa frågor med PHP finns i [Ansluta och fråga med PHP](connect-query-php.md).
- Mer information om att ansluta och ställa frågor med Node.js finns i [Ansluta och fråga med Node.js](connect-query-nodejs.md).
- Mer information om att ansluta och ställa frågor med Java finns i [Ansluta och fråga med Java](connect-query-java.md).
- Mer information om att ansluta och ställa frågor med Python finns i [Ansluta och fråga med Python](connect-query-python.md).
- Mer information om att ansluta och ställa frågor med Ruby finns i [Ansluta och fråga med Ruby](connect-query-ruby.md).
