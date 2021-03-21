---
title: Så här testar du din Azure Data Lake Analyticss kod
description: Lär dig hur du lägger till test ärenden för U-SQL och utökad C#-kod för Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: 7310c67ef20a4134d4f613ea969c96802958bf62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015238"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Testa din Azure Data Lake Analyticss kod

Azure Data Lake tillhandahåller [U-SQL-](data-lake-analytics-u-sql-get-started.md) språket. U-SQL kombinerar deklarativ SQL med tvingande C# för att bearbeta data i valfri skala. I det här dokumentet får du lära dig hur du skapar test ärenden för U-SQL och utökad C#-kod (User-Defined User-UDO).

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

U-SQL-skriptet kompileras och optimeras för körbar kod som körs i Azure eller på den lokala datorn. Processen för kompilering och optimering behandlar hela U-SQL-skriptet som helhet. Du kan inte göra ett traditionellt enhets test för varje instruktion. Genom att använda U-SQL-testsdk: n och den lokala körnings-SDK: n kan du dock testa skript nivå.

### <a name="create-test-cases-for-u-sql-script"></a>Skapa test fall för U-SQL-skript

Med Azure Data Lake verktyg för Visual Studio kan du skapa test fall i U-SQL-skript.

1. Högerklicka på ett U-SQL-skript i Solution Explorer och välj sedan **skapa enhets test**.

1. Skapa ett nytt testprojekt eller infoga test fallet i ett befintligt test projekt.

   ![Data Lake verktyg för Visual Studio – skapa en U-SQL test projekt konfiguration](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Hantera test data källan

När du testar U-SQL-skript måste du testa indatafiler. Om du vill hantera test data i **Solution Explorer** högerklickar du på U-SQL-projektet och väljer **Egenskaper**. Du kan ange en källa i **test data källan**.

![Data Lake verktyg för Visual Studio – konfigurera data källa för projekt test](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

När du anropar `Initialize()` gränssnittet i U-SQL test SDK skapas en tillfällig lokal datarotmapp under arbets katalogen i testprojektet. Alla filer och mappar i mappen test data källa kopieras till den tillfälliga lokala datarotmappen innan du kör test fallen i U-SQL-skriptet. Du kan lägga till fler mappar för test data källor genom att dela sökvägen för testdata-mappen med ett semikolon.

### <a name="manage-the-database-environment-for-testing"></a>Hantera databas miljön för testning

Om dina U-SQL-skript använder eller frågar med U-SQL Database-objekt måste du initiera databas miljön innan du kör U-SQL-testfall. Den här metoden kan vara nödvändig när du anropar lagrade procedurer. `Initialize()`Gränssnittet i u-SQL test SDK hjälper dig att distribuera alla databaser som U-SQL-projektet refererar till i den tillfälliga lokala datarotmappen i test projektets arbets katalog.

Mer information om hur du hanterar projekt referenser i U-SQL-databasen för ett U-SQL-projekt finns i [referera till ett u-SQL Database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Verifiera test resultat

`Run()`Gränssnittet returnerar ett jobb körnings resultat. *0* betyder lyckades och *1* innebär ett haveri. Du kan också använda C#-kontroll funktioner för att verifiera utdata.

### <a name="run-test-cases-in-visual-studio"></a>Köra test ärenden i Visual Studio

Ett U-SQL script test-projekt skapas ovanpå ett C#-test ramverk för enheter. När du har skapat projektet väljer du **testa**  >  **Windows**  >  **test Utforskaren**. Du kan köra test ärenden från **test Utforskaren**. Du kan också högerklicka på. cs-filen i enhets testet och välja **Kör tester**.

## <a name="test-c-udos"></a>Testa C# Katalogentiteter

### <a name="create-test-cases-for-c-udos"></a>Skapa test fall för C# Katalogentiteter

Du kan använda C# enhets test ramverk för att testa dina användardefinierade C#-operatörer (Katalogentiteter). När du testar Katalogentiteter måste du förbereda motsvarande **IRowset** -objekt som indata.

Det finns två sätt att skapa ett **IRowset** -objekt:

- Hämta data från en fil för att skapa **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Använd data från en data samling för att skapa **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Verifiera test resultat

När du har anropat UDO-funktionerna kan du kontrol lera resultatet med hjälp av kontrollen schema och rad uppsättnings värde med hjälp av C#-funktioner. Du kan lägga till ett **U-SQL C# Udo unit test-projekt** i din lösning. Det gör du genom att välja **fil > nytt > projekt** i Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Köra test ärenden i Visual Studio

När du har skapat projektet väljer du **testa**  >  **Windows**  >  **test Utforskaren**. Du kan köra test ärenden från **test Utforskaren**. Du kan också högerklicka på. cs-filen i enhets testet och välja **Kör tester**.

## <a name="run-test-cases-in-azure-pipelines"></a>Köra test ärenden i Azure-pipelines<a name="run-test-cases-in-azure-devops"></a>

Test projekt för både **U-SQL-skript** och **c# Udo-test** projekt ärver c#-enhets test projekt. [Test aktiviteten Visual Studio](/azure/devops/pipelines/test/getting-started-with-continuous-testing) i Azure-pipelines kan köra dessa test fall.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Kör U-SQL-testfall i Azure-pipeline

För ett U-SQL-test, se till att du läser in `CPPSDK` på bygg datorn och överför sedan `CPPSDK` sökvägen till `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` .

#### <a name="what-is-cppsdk"></a>Vad är CPPSDK?

CPPSDK är ett paket som innehåller Microsoft Visual C++ 14 och Windows SDK 10.0.10240.0. Det här paketet innehåller den miljö som behövs för U-SQL-körningsmiljön. Du kan hämta det här paketet under installations mappen Azure Data Lake Tools för Visual Studio:

- För Visual Studio 2015 är det under `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- För Visual Studio 2017 är det under `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- För Visual Studio 2019 är det under `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Förbereda CPPSDK i Azures pipeline build-agent

Det vanligaste sättet att förbereda CPPSDK-beroendet i Azure-pipeliner är följande:

1. Zip-mappen som innehåller CPPSDK-biblioteken.

1. Checka in. zip-filen i käll kontroll systemet. Zip-filen säkerställer att du checkar in alla bibliotek i mappen CPPSDK så att filerna inte ignoreras på grund av en `.gitignore` fil.

1. Zippa upp zip-filen i build-pipeline.

1. Peka på `USqlScriptTestRunner` den här unzippade mappen på Build-datorn.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Kör C# UDO test väskor i Azure-pipelines

För ett C# UDO-test, se till att referera till följande sammansättningar, vilka krävs för Katalogentiteter.

- Microsoft. Analytics. Interfaces
- Microsoft. Analytics. types
- Microsoft. Analytics. UnitTest

Om du hänvisar till dem via [NuGet-paketet Microsoft. Azure. DataLake. USQL. Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)kontrollerar du att du lägger till en NuGet Restore-uppgift i din build-pipeline.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera CI/CD-pipeline för Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Kör U-SQL-skript på din lokala dator](data-lake-analytics-data-lake-tools-local-run.md)
- [Använd U-SQL Database-projekt för att utveckla U-SQL-databas](data-lake-analytics-data-lake-tools-develop-usql-database.md)