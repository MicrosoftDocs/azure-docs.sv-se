---
title: Använda Azure Data Lake Tools för Visual Studio Code
description: Lär dig hur du använder Azure Data Lake Tools för Visual Studio Code för att skapa, testa och köra U-SQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: e1d74795fd25019e205f4b7b1d2bac1b67107e2d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878586"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Använda Azure Data Lake Tools för Visual Studio Code

I den här artikeln får du lära dig hur du kan använda Azure Data Lake Tools för Visual Studio Code (VS Code) för att skapa, testa och köra U-SQL-skript. Informationen tas också upp i följande video:

[![Videospelare: Azure Data Lake-verktyg för VS Code](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>Förutsättningar

Azure Data Lake Tools för VS Code stöder Windows, Linux och macOS. Lokal U-SQL-körning och lokal felsökning fungerar bara i Windows.

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

För macOS och Linux:

- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>Installera Azure Data Lake Tools

När du har installerat förutsättningarna kan du installera Azure Data Lake Tools för VS Code.

### <a name="to-install-azure-data-lake-tools"></a>Så här installerar du Azure Data Lake Tools

1. Öppna Visual Studio Code.
2. Välj **Tillägg** i den vänstra rutan. Ange **Azure Data Lake Tools** i sökrutan.
3. Välj **Installera** bredvid **Azure Data Lake Tools**.

   ![Val för att installera Data Lake Tools](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   Efter några sekunder ändras knappen **Installera** till Läs **in på nytt.**
4. Välj **Läs in** på nytt för att aktivera Azure Data Lake **Tools-tillägget.**
5. Välj **Läs in fönster igen** för att bekräfta. Du kan se **Azure Data Lake-verktyg** i **fönstret** Tillägg.

## <a name="activate-azure-data-lake-tools"></a>Aktivera Azure Data Lake-verktyg

Skapa en .usql-fil eller öppna en befintlig .usql-fil för att aktivera tillägget.

## <a name="work-with-u-sql"></a>Arbeta med U-SQL

Om du vill arbeta med U-SQL måste du öppna antingen en U-SQL-fil eller en mapp.

### <a name="to-open-the-sample-script"></a>Öppna exempelskriptet

Öppna kommandopaletten (Ctrl+Skift+P) och ange **ADL: Öppna exempelskriptet**. En annan instans av det här exemplet öppnas. Du kan också redigera, konfigurera och skicka ett skript på den här instansen.

### <a name="to-open-a-folder-for-your-u-sql-project"></a>Så här öppnar du en mapp för ditt U-SQL-projekt

1. Från Visual Studio Code väljer du **menyn** Arkiv och sedan **Öppna mapp.**
2. Ange en mapp och välj sedan **Välj mapp.**
3. Välj **menyn** Arkiv och välj sedan **Nytt.** Filen Untitled-1 läggs till i projektet.
4. Ange följande kod i filen Untitled-1:

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   UTDATA @departments     TILL "/Output/departments.csv" USING Outputters.Csv();

    Skriptet skapar en departments.csv med vissa data som ingår i mappen /output.

5. Spara filen som **myUSQL.usql i den** öppna mappen.

### <a name="to-compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Compile Script**. Kompileringsresultaten visas i **fönstret Utdata.** Du kan också högerklicka på en skriptfil och sedan välja **ADL: Kompilera skript för att** kompilera ett U-SQL-jobb. Kompileringsresultatet visas i **fönstret Utdata.**

### <a name="to-submit-a-u-sql-script"></a>Skicka ett U-SQL-skript

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Submit Job (ADL: Skicka jobb).** Du kan också högerklicka på en skriptfil och sedan välja **ADL: Submit Job (ADL: Skicka jobb).**

När du har skickat ett U-SQL-jobb visas inskickade loggar i **fönstret Utdata** i VS Code. Jobbvyn visas i den högra rutan. Om överföringen lyckas visas även jobb-URL:en. Du kan öppna jobb-URL:en i en webbläsare för att spåra jobbstatusen i realtid.

På jobbvyns **sammanfattningsflik** kan du se jobbinformationen. Huvudfunktionerna är att skicka om ett skript, duplicera ett skript och öppna det i portalen. På fliken DATA i **jobbvyn** kan du referera till indatafiler, utdatafiler och resursfiler. Filer kan laddas ned till den lokala datorn.

![Fliken Sammanfattning i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![Fliken Data i jobbvyn](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>Ange standardkontexten

Du kan ange standardkontexten för att tillämpa den här inställningen på alla skriptfiler om du inte har angett parametrar för filer individuellt.

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Ange standardkontext**. Eller högerklicka på skriptredigeraren och välj **ADL: Ange standardkontext.**
3. Välj det konto, den databas och det schema som du vill använda. Inställningen sparas i xxx_settings.jskonfigurationsfilen.

   ![Konto, databas och schema som angetts som standardkontext](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>Ange skriptparametrar

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Ange skriptparametrar**.
3. Den xxx_settings.jsfilen öppnas med följande egenskaper:

   - **account**: Ett Azure Data Lake Analytics under din Azure-prenumeration som behövs för att kompilera och köra U-SQL-jobb. Du måste konfigurera datorkontot innan du kompilerar och kör U-SQL-jobb.
   - **database**: En databas under ditt konto. Standardvärdet är **master**.
   - **schema**: Ett schema under din databas. Standardvärdet är **dbo**.
   - **optionalSettings:**
        - **priority**: Prioritetsintervallet är mellan 1 och 1 000, med 1 som högsta prioritet. Standardvärdet är **1000**.
        - **degreeOfParallelism:** Parallellitetsintervallet är mellan 1 och 150. Standardvärdet är den maximala parallellitet som tillåts i ditt Azure Data Lake Analytics konto.

   ![Innehållet i JSON-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> När du har sparat konfigurationen visas konto-, databas- och schemainformationen i statusfältet i det nedre vänstra hörnet av motsvarande .usql-fil om du inte har en standardkontext konfigurerad.

### <a name="to-set-git-ignore"></a>Så här ställer du in Git ignore

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Ange Git Ignore**.

   - Om du inte har en **.gitIgnore-fil** i arbetsmappen för VS Code skapas en fil med namnet **.gitIgnore** i mappen . Fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) läggs till i filen som standard. Du kan göra fler uppdateringar om det behövs.
   - Om du redan har en **.gitIgnore-fil** i arbetsmappen för VS Code lägger verktyget till fyra objekt (**usqlCodeBehindReference**, **usqlCodeBehindGenerated**, **.cache**, **obj**) i **din .gitIgnore-fil** om de fyra objekten inte inkluderades i filen.

   ![Objekt i .gitIgnore-filen](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>Arbeta med filer som ligger bakom kod: C Sharp, Python och R

Azure Data Lake Tools stöder flera anpassade koder. Instruktioner finns i [Utveckla U-SQL med Python, R och C Sharp](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)för Azure Data Lake Analytics i VS Code .

## <a name="work-with-assemblies"></a>Arbeta med sammansättningar

Information om hur du utvecklar sammansättningar finns i [Utveckla U-SQL-sammansättningar för Azure Data Lake Analytics jobb]().

Du kan använda Data Lake Tools för att registrera anpassade kodsammansättningar i Data Lake Analytics katalog.

### <a name="to-register-an-assembly"></a>Registrera en sammansättning

Du kan registrera sammansättningen via kommandot **ADL: Register Assembly (ADL: Registrera sammansättning)** eller **ADL: Register Assembly (Advanced).**

### <a name="to-register-through-the-adl-register-assembly-command"></a>Så här registrerar du dig via kommandot ADL: Registrera sammansättning

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Registrera sammansättning**.
3. Ange den lokala sammansättningssökvägen.
4. Välj ett Data Lake Analytics konto.
5. Välj en databas.

Portalen öppnas i en webbläsare och visar processen för sammansättningsregistrering.  

Ett enklare sätt att utlösa **kommandot ADL: Registrera** sammansättning är att högerklicka på DLL-filen i Utforskaren.

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>Så här registrerar du dig via kommandot ADL: Register Assembly (Advanced) (Registrera sammansättning (avancerat)

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.
2. Ange **ADL: Registrera sammansättning (avancerat).**
3. Ange den lokala sammansättningssökvägen.
4. JSON-filen visas. Granska och redigera sammansättningsberoenden och resursparametrar om det behövs. Instruktioner visas i fönstret **Utdata.** Om du vill fortsätta med sammansättningsregistreringen sparar du JSON-filen (Ctrl+S).

   ![JSON-fil med sammansättningsberoenden och resursparametrar](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Tools anger automatiskt om DLL-filen har några sammansättningsberoenden. Beroendena visas i JSON-filen när de har identifierats.
>- Du kan ladda upp DINA DLL-resurser (till exempel .txt, .png och .csv) som en del av sammansättningsregistreringen.

Ett annat sätt att utlösa **kommandot ADL: Register Assembly (Avancerat)** är att högerklicka på DLL-filen i Utforskaren.

Följande U-SQL-kod visar hur du anropar en sammansättning. I exemplet är sammansättningsnamnet *test*.

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>Använda lokal U-SQL-körning och lokal felsökning för Windows-användare

Lokal U-SQL-körning testar dina lokala data och verifierar skriptet lokalt innan koden publiceras till Data Lake Analytics. Du kan använda den lokala felsökningsfunktionen för att slutföra följande uppgifter innan koden skickas till Data Lake Analytics:

- Felsök din C#-kod bakom.
- Stega igenom koden.
- Verifiera skriptet lokalt.

Funktionen för lokal körning och lokal felsökning fungerar bara i Windows-miljöer och stöds inte på macOS- och Linux-baserade operativsystem.

Anvisningar om lokal körning och lokal felsökning finns i [U-SQL local run and local debug with Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md).

## <a name="connect-to-azure"></a>Anslut till Azure

Innan du kan kompilera och köra U-SQL-skript i Data Lake Analytics måste du ansluta till ditt Azure-konto.

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>Så här ansluter du till Azure med hjälp av ett kommando

1. Välj Ctrl+Skift+P för att öppna kommandopaletten.

2. Ange **ADL: Logga in**. Inloggningsinformationen visas i det nedre högra högra.

   ![Ange inloggningskommandot](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![Meddelande om inloggning och autentisering](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. Välj **Kopiera & Öppna för** att öppna [inloggningens webbsida.](https://aka.ms/devicelogin) Klistra in koden i rutan och välj sedan **Fortsätt.**

    ![Inloggningssida](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. Följ anvisningarna för att logga in från webbsidan. När du är ansluten visas ditt Azure-kontonamn i statusfältet i det nedre vänstra hörnet i VS Code-fönstret.

> [!NOTE]
>
> - Data Lake Tools loggar automatiskt in dig nästa gång du inte loggar ut.
> - Om ditt konto har två aktiverade faktorer rekommenderar vi att du använder telefonautentisering i stället för att använda en PIN-kod.

Logga ut genom att ange kommandot **ADL: Logga ut.**

### <a name="to-connect-to-azure-from-the-explorer"></a>Så här ansluter du till Azure från utforskaren

Expandera **AZURE DATALAKE,** välj **Logga in** på Azure och följ sedan steg 3 och steg 4 i Ansluta till Azure med hjälp [av kommandot](#sign-in-by-command).

!["Logga in på Azure" i utforskaren](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

Du kan inte logga ut från utforskaren. Information om hur du loggar ut [finns i Ansluta till Azure med hjälp av kommandot](#sign-in-by-command).

## <a name="create-an-extraction-script"></a>Skapa ett extraheringsskript

Du kan skapa ett extraheringsskript för .csv-, .tsv- och .txt-filer med hjälp av kommandot **ADL: Create EXTRACT Script** eller från Azure Data Lake Explorer.

### <a name="to-create-an-extraction-script-by-using-a-command"></a>Så här skapar du ett extraheringsskript med hjälp av ett kommando

1. Välj Ctrl+Skift+P för att öppna kommandopaletten och ange **ADL: Skapa EXTRAHERINGsskript.**
2. Ange den fullständiga sökvägen för Azure Storage fil och välj Retur.
3. Välj ett konto.
4. För en .txt-fil väljer du en avgränsare för att extrahera filen.

![Process för att skapa ett extraheringsskript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

Extraheringsskriptet genereras baserat på dina poster. Välj ett av de två alternativen för ett skript som inte kan identifiera kolumnerna. Annars genereras bara ett skript.

![Resultatet av att skapa ett extraheringsskript](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>Så här skapar du ett extraheringsskript från utforskaren

Ett annat sätt att skapa extraheringsskriptet är via snabbmenyn på .csv-, .tsv- eller .txt-filen i Azure Data Lake Store eller Azure Blob Storage.

![Kommandot "Create EXTRACT Script" (Skapa EXTRAHERA skript) från snabbmenyn](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>Nästa steg

- [Utveckla U-SQL med Python, R och C Sharp för Azure Data Lake Analytics i VS Code](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Lokal U-SQL-körning och lokal felsökning med Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
- [Självstudie: Komma igång med Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [Självstudie: Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
