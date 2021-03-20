---
title: Utveckla ett U-SQL Database-projekt – Azure Data Lake
description: Lär dig hur du utvecklar en U-SQL-databas med hjälp av Azure Data Lake verktyg för Visual Studio.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015255"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Använd ett U-SQL Database-projekt för att utveckla en U-SQL-databas för Azure Data Lake

U-SQL Database tillhandahåller strukturerade vyer över ostrukturerade data och hanterade strukturerade data i tabeller. Det innehåller också ett allmänt katalog system för metadata för att organisera strukturerade data och anpassad kod. Databasen är det begrepp som grupperar dessa relaterade objekt tillsammans.

Läs mer om [U-SQL Database och DDL (Data Definition Language)](/u-sql/data-definition-language-ddl-statements).

U-SQL Database-projektet är en projekt typ i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt.

## <a name="create-a-u-sql-database-project"></a>Skapa ett U-SQL Database-projekt

Azure Data Lake verktyg för Visual Studio har lagt till en ny projektmall med namnet U-SQL Database Project efter version 2.3.3000.0. Om du vill skapa ett U-SQL-projekt väljer du **fil > nytt > projekt**. U-SQL Database-projektet finns under **Azure Data Lake > U-SQL-noden**.

![Data Lake verktyg för Visual Studio – skapa U-SQL Database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Utveckla U-SQL Database-objekt med hjälp av ett databas projekt

Högerklicka på U-SQL Database-projektet. Välj **Lägg till > nytt objekt**. Du kan hitta alla nya objekt typer som stöds i guiden **Lägg till nytt objekt** .

För ett icke-sammansättnings objekt (till exempel en tabell värdes funktion) skapas ett nytt U-SQL-skript när du har lagt till ett nytt objekt. Du kan börja utveckla DDL-instruktionen för objektet i redigeraren.

För ett sammansättnings objekt tillhandahåller verktyget en användarvänlig GRÄNSSNITTs redigerare som hjälper dig att registrera sammansättningen och distribuera DLL-filer och andra filer. Följande steg visar hur du lägger till en sammansättnings objekt definition i U-SQL Database-projektet:

1. Lägg till referenser till C#-projektet som innehåller UDO/UDAG/UDF för U-SQL Database-projektet.

   ![Data Lake verktyg för Visual Studio – Lägg till referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake verktyg för Visual Studio – Lägg till projekt referens för U-SQL-databasen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. I vyn sammansättnings design väljer du den refererade sammansättningen från **skapa sammansättning från referens** -menyn.

   ![Data Lake verktyg för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. Lägg till **hanterade beroenden** och **Ytterligare filer** om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att kontrol lera att det går att hitta sammansättningarna både på den lokala datorn och på Build-datorn senare.

@_DeployTempDirectory är en fördefinierad variabel som pekar på verktyget för mappen för att bygga utdata. Under mappen för att bygga utdata har varje sammansättning en undermapp med namnet med sammansättnings namnet. Alla DLL-filer och ytterligare filer finns i undermappen.

## <a name="build-a-u-sql-database-project"></a>Bygg ett U-SQL Database-projekt

Build-utdata för ett U-SQL Database-projekt är ett U-SQL-databas distributions paket med namnet med suffixet `.usqldbpack` . `.usqldbpack`Paketet är en. zip-fil som innehåller alla DDL-instruktioner i ett enda U-SQL-skript i **DDL** -mappen och alla DLL-filer och ytterligare filer för sammansättningar i **Temp** -mappen.

Lär dig mer om [hur du skapar ett U-SQL Database-projekt med kommando raden för MSBuild och en Azure DevOps Services build-uppgift](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

Paketet. usqldbpack kan distribueras till antingen ett lokalt konto eller ett Azure Data Lake Analytics konto med hjälp av Visual Studio eller SDK för distribution.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas via ett U-SQL Database-projekt eller ett. usqldbpack-paket i Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Distribuera genom ett U-SQL Database-projekt

1. Högerklicka på U-SQL Database-projektet och välj sedan **distribuera**.

1. I **guiden Distribuera U-SQL Database** väljer du det **ADLA-konto** som du vill distribuera databasen till. Både lokala konton och ADLA-konton stöds.

1. **Databas källan** fylls i automatiskt och pekar på. usqldbpack-paketet i projektets version av utdata-mappen.

1. Ange ett namn i **databas namn** för att skapa en databas. Om det redan finns en databas med samma namn i mål Azure Data Lake Analyticss kontot, skapas alla objekt som definieras i databas projektet utan att databasen återskapas.

1. Om du vill distribuera U-SQL-databasen väljer du **Skicka**. Alla resurser (sammansättningar och ytterligare filer) laddas upp och ett U-SQL-jobb som innehåller alla DDL-uttryck skickas.

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL Database-projekt](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL Database projekt guide](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Distribuera via ett U-SQL-databas distributions paket

1. Öppna **Server Explorer**. Expandera sedan det **Azure Data Lake Analytics konto** som du vill distribuera databasen till.

1. Högerklicka på **U-SQL-databaser** och välj sedan **distribuera databas**.

1. Ange **databas källa** till U-SQL-databasens distributions paket (. usqldbpack-fil).

1. Ange **databas namnet** för att skapa en databas. Om det finns en databas med samma namn som redan finns i mål Azure Data Lake Analyticss kontot, skapas alla objekt som definieras i databas projektet utan att databasen återskapas.

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL-databaspaketet](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL databas paket guiden](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Distribuera U-SQL-databas med hjälp av SDK

`PackageDeploymentTool.exe` tillhandahåller programmerings-och kommando rads gränssnitt som hjälper till att distribuera U-SQL-databaser. SDK ingår i [U-SQL SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)som finns på `build/runtime/PackageDeploymentTool.exe` .

[Lär dig mer om SDK och hur du konfigurerar CI/CD-pipeline för distribution av U-SQL-databasen](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Referera till ett U-SQL Database-projekt

Ett U-SQL-projekt kan referera till ett U-SQL Database-projekt. Referensen påverkar två arbets belastningar:

- *Projekt version*: Konfigurera refererade databas miljöer innan du skapar U-SQL-skripten.
- *Lokalt kör mot (ett lokalt projekt) konto*: de refererade databas miljöerna distribueras till (ett lokalt projekt) konto innan U-SQL-skript körs. [Lär dig mer om lokala körningar och skillnaden mellan (lokala datorer) och (ett lokalt projekt)-konto här](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Så här lägger du till en U-SQL Database-referens

1. Högerklicka på U-SQL-projektet i **Solution Explorer** och välj sedan **Lägg till u-SQL Database referens...**.

    ![Data Lake verktyg för Visual Studio – Lägg till databas projekt referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Konfigurera en databas referens från ett U-SQL Database-projekt i den aktuella lösningen eller i en U-SQL-databasfil.

3. Ange namnet på databasen.

    ![Data Lake verktyg för Visual Studio guiden Lägg till databas projekt referens](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Så här testar du din Azure Data Lake Analyticss kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
