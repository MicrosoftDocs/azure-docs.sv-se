---
title: Hantera U-SQL-sammansättningar i en CI/CD-pipeline – Azure Data Lake
description: Lär dig metod tips för att hantera U-SQL C#-sammansättningar i en CI/CD-pipeline med Azure DevOps.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015289"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Metod tips för att hantera U-SQL-sammansättningar i en CI/CD-pipeline

I den här artikeln får du lära dig att hantera käll koden för U-SQL-sammansättningen med det nyligen introducerade U-SQL Database-projektet. Du lär dig också hur du konfigurerar en pipeline för kontinuerlig integrering och distribution (CI/CD) för sammansättnings registrering med hjälp av Azure-DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Använd U-SQL Database-projektet för att hantera käll koden för sammansättningen

[U-SQL Database-projektet](data-lake-analytics-data-lake-tools-develop-usql-database.md) är en projekt typ i Visual Studio som hjälper utvecklare att utveckla, hantera och distribuera sina U-SQL-databaser snabbt och enkelt. Du kan hantera alla U-SQL Database-objekt (förutom autentiseringsuppgifter) med U-SQL Database-projektet.

Använd följande om du vill hantera C#-sammansättningens käll kod och DDL-skript för sammansättnings registrering:

- U-SQL Database-projekt för att hantera U-SQL-skript för sammansättnings registrering.
- Klass bibliotek (för U-SQL-program) för att hantera C#-källkoden och beroenden för användardefinierade operatorer, Functions och aggregator (Katalogentiteter, UDF: er och UDAGs).
- U-SQL Database-projekt för att referera till klass biblioteks projektet.

Ett U-SQL Database-projekt kan referera till ett klass bibliotek (för U-SQL Application)-projekt. Du kan skapa sammansättningar som registrerats i U-SQL-databasen med refererad C#-källkod från det här klass biblioteket (för U-SQL Application)-projekt.

Följ dessa steg om du vill skapa projekt och lägga till referenser.

1. Skapa ett klass bibliotek (för U-SQL Application)-projekt genom att välja **Arkiv**  >  **nytt**  >  **projekt**. Projektet finns under **Azure Data Lake > U-SQL** -noden.

   ![Data Lake verktyg för Visual Studio – skapa C#-klass biblioteks projekt](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Lägg till din användardefinierade C#-kod i klass biblioteket (för U-SQL Application)-projektet.

1. Skapa ett U-SQL-projekt genom att välja **Arkiv**  >  **nytt**  >  **projekt**. Projektet finns under **Azure Data Lake**  >  **U-SQL-** noden.

   ![Data Lake verktyg för Visual Studio – skapa U-SQL Database-projekt](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Lägg till en referens i C#-klass biblioteks projektet för U-SQL Database-projektet.

   ![Data Lake verktyg för Visual Studio – Lägg till referens](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake verktyg för Visual Studio – Lägg till projekt referens för U-SQL-databasen](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Skapa ett sammansättnings skript i U-SQL Database-projektet genom att högerklicka på projektet och välja **Lägg till nytt objekt**.

   ![Data Lake verktyg för Visual Studio – Lägg till sammansättnings skript](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Öppna sammansättnings skriptet i vyn sammansättnings design. Välj den refererade sammansättningen från den nedrullningsbara menyn **skapa sammansättning från referens** .

   ![Data Lake verktyg för Visual Studio – skapa sammansättning från referens](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Lägg till **hanterade beroenden** och **Ytterligare filer**, om det finns några. När du lägger till ytterligare filer använder verktyget den relativa sökvägen för att kontrol lera att det går att hitta sammansättningarna på den lokala datorn och på Build-datorn senare.

**\@ _DeployTempDirectory** i redigerings fönstret längst ned är en fördefinierad variabel som pekar på verktyget för mappen för att bygga utdata. Under mappen för att bygga utdata har varje sammansättning en undermapp med namnet med sammansättnings namnet. Alla DLL-filer och ytterligare filer finns i undermappen.

## <a name="build-a-u-sql-database-project"></a>Bygg ett U-SQL Database-projekt

Build-utdata för ett U-SQL Database-projekt är ett U-SQL-databasens distributions paket. Den har namnet med suffixet `.usqldbpack` . `.usqldbpack`Paketet är en. zip-fil som innehåller alla DDL-instruktioner i ett enda U-SQL-skript i DDL-mappen. Alla inbyggda DLL-filer och ytterligare filer för sammansättningar finns i Temp-mappen.

## <a name="deploy-a-u-sql-database"></a>Distribuera en U-SQL-databas

`.usqldbpack`Paketet kan distribueras till antingen ett lokalt konto eller ett Azure Data Lake Analytics-konto. Använd Visual Studio eller SDK för distribution.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Distribuera en U-SQL-databas i Visual Studio

Du kan distribuera en U-SQL-databas med ett U-SQL Database-projekt eller ett `.usqldbpack` paket i Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Distribuera med hjälp av ett U-SQL Database-projekt

1. Högerklicka på U-SQL Database-projektet och välj sedan **distribuera**.

1. I guiden **distribuera U-SQL Database** väljer du det **ADLA-konto** som du vill distribuera databasen till. Både lokala konton och ADLA-konton stöds.

1. **Databas källan** fylls i automatiskt. Den pekar på. usqldbpack-paketet i projektets mapp för build-utdata.

1. Ange ett namn i **databas namn** för att skapa en databas. Om det redan finns en databas med samma namn i mål Azure Data Lake Analyticss kontot, skapas alla objekt som definieras i databas projektet utan att databasen skapas igen.

1. Om du vill distribuera U-SQL-databasen väljer du **Skicka**. Alla resurser, till exempel sammansättningar och ytterligare filer, överförs. Ett U-SQL-jobb som innehåller alla DDL-uttryck skickas.

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL Database-projekt](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake verktyg för Visual Studio – distribuera U-SQL Database projekt guide](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Distribuera en U-SQL-databas i Azure DevOps

`PackageDeploymentTool.exe` tillhandahåller programmerings-och kommando rads gränssnitt som hjälper till att distribuera U-SQL-databaser. SDK ingår i [U-SQL SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)som finns på `build/runtime/PackageDeploymentTool.exe` .

I Azure DevOps kan du använda en kommando rad uppgift och denna SDK för att konfigurera en automatiserings pipeline för U-SQL Database-uppdateringen. [Lär dig mer om SDK och hur du konfigurerar en CI/CD-pipeline för distribution av U-SQL-databasen](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testa din Azure Data Lake Analyticss kod](data-lake-analytics-cicd-test.md)
- [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
