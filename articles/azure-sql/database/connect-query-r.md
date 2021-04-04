---
title: Använd R med Azure SQL Database Machine Learning Services (för hands version) för att fråga en databas
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Den här artikeln visar hur du använder ett R-skript med Azure SQL Database Machine Learning Services för att ansluta till en databas i Azure SQL Database och fråga den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185631"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Snabb start: Använd R med Azure SQL Database Machine Learning Services (för hands version) för att fråga en databas 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här snabb starten använder du R med Azure SQL Database Machine Learning Services för att ansluta till en databas i Azure SQL Database och använda T-SQL-uttryck för att fråga data.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- En [Azure SQL Database](single-database-create-quickstart.md)
- [Machine Learning Services](../managed-instance/machine-learning-services-overview.md) med R aktiverat.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skripten i den här artikeln är skrivna för att använda **Adventure Works** -databasen.

Machine Learning Services med R är en funktion i Azure SQL Database som används för att köra in-Database R-skript. Mer information finns i R- [projektet](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>Hämta anslutnings informationen för SQL Server

Hämta anslutnings informationen du behöver för att ansluta till databasen i Azure SQL Database. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Gå till sidan **SQL-databaser**  eller **SQL-hanterade instanser** .

3. På sidan **Översikt** granskar du det fullständigt kvalificerade Server namnet bredvid **Server namnet** för en databas i Azure SQL Database eller det fullständigt kvalificerade Server namnet bredvid **värd** för en hanterad instans i Azure SQL-hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

## <a name="create-code-to-query-your-database"></a>Skapa en kod för att fråga databasen

1. Öppna **SQL Server Management Studio** och Anslut till din databas.

   Om du behöver hjälp med att ansluta, se [snabb start: använda SQL Server Management Studio för att ansluta och fråga en databas i Azure SQL Database](connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till den [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

   Skriptet skickas genom `@script` argumentet. Allting i `@script` argumentet måste vara en giltig R-kod.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Om du får fel kan det bero på att den offentliga för hands versionen av Machine Learning Services (med R) inte är aktive rad för din databas. Se [krav](#prerequisites) ovan.

## <a name="run-the-code"></a>Kör koden

1. Kör den lagrade proceduren [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) .

1. Kontrol lera att de 20 främsta kategorierna/produkt raderna returneras i fönstret **meddelanden** .

## <a name="next-steps"></a>Nästa steg

- [Utforma din första databas i Azure SQL Database](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (med R)](../managed-instance/machine-learning-services-overview.md)
- [Skapa och kör enkla R-skript i Azure SQL Database Machine Learning Services (förhands granskning)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)