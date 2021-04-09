---
title: Kopiera data från Blob Storage till SQL Database – Azure
description: Den här självstudien visar hur du använder kopierings aktivitet i en Azure Data Factory pipeline för att kopiera data från Blob Storage till SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376895"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Självstudie: kopiera data från Blob Storage till SQL Database med Data Factory
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här självstudien skapar du en data fabrik med en pipeline för att kopiera data från Blob Storage till SQL Database.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.  

> [!NOTE]
> En detaljerad översikt över Data Factorys tjänsten finns i artikeln [Introduktion till Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Krav för självstudien
Innan du påbörjar den här självstudien måste du ha följande krav:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i artikeln om [kostnads fri utvärdering](https://azure.microsoft.com/pricing/free-trial/) .
* **Azure Storage konto**. Du använder Blob Storage som **käll** data lager i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
* **Azure SQL Database**. Du använder Azure SQL Database som **mål** data lager i den här självstudien. Om du inte har en databas i Azure SQL Database som du kan använda i självstudien, se [så här skapar och konfigurerar du en databas i Azure SQL Database](../../azure-sql/database/single-database-create-quickstart.md) för att skapa en.
* **SQL Server 2012/2014 eller Visual Studio 2013**. Du kan använda SQL Server Management Studio eller Visual Studio för att skapa en exempel databas och Visa resultat data i-databasen.  

## <a name="collect-blob-storage-account-name-and-key"></a>Samla in Blob Storage-kontots namn och nyckel
Du behöver konto namnet och konto nyckeln för ditt Azure Storage-konto för att göra den här självstudien. Anteckna **konto namnet** och **konto nyckeln** för ditt Azure Storage-konto.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **alla tjänster** på den vänstra menyn och välj **lagrings konton**.

    ![Bläddra – lagrings konton](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. På bladet **lagrings konton** väljer du det **Azure Storage-konto** som du vill använda i den här självstudien.
4. Välj länken **åtkomst nycklar** under **Inställningar**.
5. Klicka på knappen **Kopiera** (bild) bredvid text rutan **lagrings konto namn** och spara/klistra in den någonstans (till exempel: i en textfil).
6. Upprepa föregående steg för att kopiera eller anteckna **KEY1**.

    ![Lagrings åtkomst nyckel](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Stäng alla blad genom att klicka på **X**.

## <a name="collect-sql-server-database-user-names"></a>Samla in SQL Server, databas, användar namn
Du behöver namnen på den logiska SQL-servern, databasen och användaren för att göra den här självstudien. Anteckna namn på **Server**, **databas** och **användare** för Azure SQL Database.

1. Klicka på **alla tjänster** till vänster i **Azure Portal** och välj SQL- **databaser**.
2. På **bladet SQL-databaser** väljer du den **databas** som du vill använda i den här självstudien. Anteckna namnet på **databasen**.  
3. Klicka på **Egenskaper** under **Inställningar** på **SQL Database** -bladet.
4. Anteckna värdena för **Server namn** och **Server Administratörs inloggning**.
5. Stäng alla blad genom att klicka på **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Ge Azure-tjänster åtkomst till SQL Server
Kontrol lera att inställningen **Tillåt åtkomst till Azure-tjänster** **är aktive** rad för servern så att Data Factory-tjänsten kan komma åt servern. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **alla tjänster** -hubben till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
4. Stäng alla blad genom att klicka på **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Förbereda Blob Storage och SQL Database
Förbered nu Azure Blob Storage och Azure SQL Database för självstudien genom att utföra följande steg:  

1. Öppna Anteckningar. Kopiera följande text och spara den som **emp.txt** till **C:\ADFGetStarted** -mappen på hård disken.

    ```
    John, Doe
    Jane, Doe
    ```
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://storageexplorer.com/) till att skapa containern **adftutorial** och för att ladda upp filen **emp.txt** till containern.

3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL-databas.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Om du har SQL Server 2012/2014 installerat på datorn:** Följ anvisningarna i [hantera Azure SQL Database med SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) för att ansluta till servern och köra SQL-skriptet.

    Om klienten inte har behörighet att komma åt den logiska SQL-servern måste du konfigurera brand väggen för servern så att den tillåter åtkomst från din dator (IP-adress). I [den här artikeln](../../azure-sql/database/firewall-configure.md) beskrivs hur du konfigurerar brand väggen för servern.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Du har slutfört kraven. Du kan skapa en data fabrik på något av följande sätt. Klicka på något av alternativen i list rutan längst upp eller följande länkar för att utföra självstudien.     

* [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster).
>
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).