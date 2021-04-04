---
title: Kör U-SQL-jobb lokalt – Azure Data Lake U-SQL SDK
description: Lär dig hur du kör och testar U-SQL-jobb lokalt med hjälp av kommando raden och programmerings gränssnitt på din lokala arbets Station.
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215543"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Köra och testa U-SQL med Azure Data Lake U-SQL SDK

När du utvecklar U-SQL-skript är det vanligt att köra och testa U-SQL-skript lokalt innan du skickar det till molnet. Azure Data Lake tillhandahåller ett NuGet-paket med namnet Azure Data Lake U-SQL SDK för det här scenariot, genom vilket du enkelt kan skala U-SQL-körning och-test. Det är också möjligt att integrera U-SQL-testet med CI-systemet (kontinuerlig integrering) för att automatisera kompilering och testning.

Om du bryr dig om hur du manuellt lokalt kör och felsöker U-SQL-skript med GUI-verktyg kan du använda Azure Data Lake verktyg för Visual Studio. Du kan läsa mer [här](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Installera Azure Data Lake U-SQL SDK

Du kan hämta Azure Data Lake U-SQL SDK [här](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) på NuGet.org. Och innan du använder det måste du kontrol lera att du har beroenden enligt följande.

### <a name="dependencies"></a>Beroenden

Data Lake U-SQL SDK kräver följande beroenden:

- [Microsoft .NET Framework 4,6 eller senare](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 och Windows SDK 10.0.10240.0 eller senare (som kallas CppSDK i den här artikeln). Det finns två sätt att hämta CppSDK:

  - Installera [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Du har en \Windows Kits\10-mapp under mappen program – till exempel C:\Program Files (x86) \Windows Kits\10\. Du hittar också Windows 10 SDK-versionen under \Windows Kits\10\Lib. Om du inte ser dessa mappar måste du installera om Visual Studio och se till att välja Windows 10 SDK under installationen. Om du har installerat med Visual Studio hittar du den lokala U-SQL-kompilatorn automatiskt.

    ![Data Lake verktyg för Visual Studio Local – kör Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Installera [data Lake verktyg för Visual Studio](https://aka.ms/adltoolsvs). Du hittar de förpaketerade Visual C++-och Windows SDK-filerna på `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    I det här fallet kan inte den lokala U-SQL-kompileraren hitta beroenden automatiskt. Du måste ange sökvägen till CppSDK. Du kan antingen kopiera filerna till en annan plats eller använda den som den är.

## <a name="understand-basic-concepts"></a>Förstå grundläggande begrepp

### <a name="data-root"></a>Data rot

Mappen data-root är en "lokal lagring" för det lokala beräknings kontot. Det motsvarar Azure Data Lake Store kontot för ett Data Lake Analytics konto. Att växla till en annan data rot Katalog är precis som att växla till ett annat lagrings konto. Om du vill komma åt vanliga delade data med olika data rot kataloger måste du använda absoluta sökvägar i skripten. Du kan också skapa fil system symboliska länkar (till exempel **MKLINK** på NTFS) under mappen dataroot för att peka på delade data.

Mappen data-root används för att:

- Lagra lokala metadata, inklusive databaser, tabeller, tabell värdes funktioner (TVFs) och sammansättningar.
- Leta upp de indata och utdata som har definierats som relativa sökvägar i U-SQL. Genom att använda relativa sökvägar blir det enklare att distribuera dina U-SQL-projekt till Azure.

### <a name="file-path-in-u-sql"></a>Fil Sök väg i U-SQL

Du kan använda både en relativ sökväg och en lokal absolut sökväg i U-SQL-skript. Den relativa sökvägen är relativ i förhållande till den angivna sökvägen för data rot katalogen. Vi rekommenderar att du använder "/" som Sök vägs avgränsare för att göra skripten kompatibla med Server sidan. Här följer några exempel på relativa sökvägar och deras motsvarande absoluta sökvägar. I de här exemplen är C:\LocalRunDataRoot mappen data-root.

|Relativ sökväg|Absolut sökväg|
|-------------|-------------|
|/ABC/DEF/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|ABC/DEF/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/ABC/DEF/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Arbets katalog

När U-SQL-skriptet körs lokalt skapas en arbets katalog under kompileringen under aktuell katalog som körs. Förutom kompileringen av utdata, kommer de nödvändiga runtime-filerna för lokal körning att skugg kopie ras till den här arbets katalogen. Rotmappen för arbets katalogen heter "ScopeWorkDir" och filerna i arbets katalogen ser ut så här:

|Katalog/fil|Katalog/fil|Katalog/fil|Definition|Beskrivning|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-sträng för körnings version|Skugg kopia av runtime-filer som behövs för lokal körning|
| |Script_66AE4909AA0ED06C| |Skript namn + hash-sträng för skript Sök väg|Att kompilera utdata och loggning av körnings steg|
| | |\_script \_ . områdesgränsrouter|Kompilatorns utdata|Algebra-fil|
| | |\_ScopeCodeGen \_ . *|Kompilatorns utdata|Genererad hanterad kod|
| | |\_ScopeCodeGenEngine \_ . *|Kompilatorns utdata|Genererad ursprunglig kod|
| | |refererade sammansättningar|Sammansättnings referens|Refererade Assembly-filer|
| | |deployed_resources|Resurs distribution|Filer för resurs distribution|
| | |xxxxxxxx. xxx [1.. n] \_ \* . *|Körnings logg|Logg för körnings steg|

## <a name="use-the-sdk-from-the-command-line"></a>Använda SDK från kommando raden

### <a name="command-line-interface-of-the-helper-application"></a>Kommando rads gränssnitt för hjälp programmet

Under SDK-directory\build\runtime är LocalRunHelper.exe det kommando rads program som innehåller gränssnitt till de flesta av de vanligaste funktionerna för lokal körning. Observera att både kommandot och argument växlarna är Skift läges känsliga. Så här anropar du det:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Kör LocalRunHelper.exe utan argument eller med **hjälp av hjälp** växeln för att visa hjälp informationen:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

I hjälp informationen:

- **Visar** kommandots namn.  
- **Obligatoriskt argument** visar argument som måste anges.  
- **Valfria argument** visar argument som är valfria, med standardvärden.  Valfria booleska argument har inte parametrar, och deras utseende innebär negativ till standardvärdet.

### <a name="return-value-and-logging"></a>Retur värde och loggning

Hjälp programmet returnerar **0** för att lyckas och **-1** för fel. Som standard skickar hjälparen alla meddelanden till den aktuella konsolen. De flesta kommandon stöder dock **meddelandet-meddelande path_to_log_file** valfritt argument som omdirigerar utdata till en loggfil.

### <a name="environment-variable-configuring"></a>Konfigurera miljö variabel

Lokal U-SQL-körning behöver en angiven data rot som ett lokalt lagrings konto, samt en angiven CppSDK-sökväg för beroenden. Du kan båda ange argumentet i kommando rads-eller ange en miljö variabel för dem.

- Ange miljövariabeln **SCOPE_CPP_SDK** .

  Om du får Microsoft Visual C++ och Windows SDK genom att installera Data Lake verktyg för Visual Studio, kontrollerar du att du har följande mapp:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Definiera en ny miljö variabel som kallas **SCOPE_CPP_SDK** att peka på den här katalogen. Eller kopiera mappen till den andra platsen och ange **SCOPE_CPP_SDK** som.

  Förutom att ställa in miljövariabeln kan du ange argumentet **-CppSDK** när du använder kommando raden. Med det här argumentet skrivs din standard miljö variabel över CppSDK.

- Ange miljövariabeln **LOCALRUN_DATAROOT** .

  Definiera en ny miljö variabel som kallas **LOCALRUN_DATAROOT** som pekar på data roten.

  Förutom att ställa in miljövariabeln kan du ange argumentet **-DataRoot** med data rot Sök vägen när du använder en kommando rad. Det här argumentet skriver över din standard miljö variabel för data rot. Du måste lägga till det här argumentet till alla kommando rader som du kör så att du kan skriva över standard miljön för data rot miljön för alla åtgärder.

### <a name="sdk-command-line-usage-samples"></a>Exempel på kommando rads användning för SDK

#### <a name="compile-and-run"></a>Kompilera och köra

**Kör** -kommandot används för att kompilera skriptet och köra sedan kompilerade resultat. Dess kommando rads argument är en kombination av dem från att **kompilera** och **köra**.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Följande är valfria argument för **körning**:

|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|-CodeBehind|Falskt|Skriptet har. cs-kod bakom|
|-CppSDK| |CppSDK-katalog|
|– DataRoot| DataRoot miljö variabel|DataRoot för lokal körning, standard till miljövariabeln LOCALRUN_DATAROOT|
|– Meddelande| |Dumpa meddelanden i konsolen till en fil|
|– Parallell|1|Kör planen med den angivna parallellitet|
|-Referenser| |Lista över sökvägar till extra referens sammansättningar eller datafiler med kod bakom, avgränsade med '; '|
|-UdoRedirect|Falskt|Generera Udo Assembly Redirect config|
|-UseDatabase|master|Databas som ska användas för kod bakom tillfällig sammansättnings registrering|
|– Utförlig|Falskt|Visa detaljerade utdata från körning|
|-WorkDir|Aktuell katalog|Katalog för användning och utdata av kompilerare|
|-RunScopeCEP|0|ScopeCEP-läge som ska användas|
|-ScopeCEPTempPath|temp|Temp-sökväg som ska användas för strömmande data|
|-OptFlags| |Kommaavgränsad lista över optimerings flaggor|

Här är ett exempel:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Förutom att kombinera **kompilera** och **köra** kan du kompilera och köra kompilerade körbara filer separat.

#### <a name="compile-a-u-sql-script"></a>Kompilera ett U-SQL-skript

**Compile** -kommandot används för att kompilera ett U-SQL-skript till körbara filer.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Följande är valfria argument för **kompilering**:

|Argument|Beskrivning|
|--------|-----------|
| -CodeBehind [standardvärdet ' false ']|Skriptet har. cs-kod bakom|
| -CppSDK [standardvärde ' ']|CppSDK-katalog|
| -DataRoot [standardvärdet ' DataRoot Environment Variable ']|DataRoot för lokal körning, standard till miljövariabeln LOCALRUN_DATAROOT|
| -Meddelande [Standardvärde]|Dumpa meddelanden i konsolen till en fil|
| -Referenser [standardvärde ' ']|Lista över sökvägar till extra referens sammansättningar eller datafiler med kod bakom, avgränsade med '; '|
| -Lite [standardvärde ' false ']|Ytlig kompilering|
| -UdoRedirect [standardvärdet ' false ']|Generera Udo Assembly Redirect config|
| -UseDatabase [standardvärde ' Master ']|Databas som ska användas för kod bakom tillfällig sammansättnings registrering|
| -WorkDir [standardvärde ' aktuell katalog ']|Katalog för användning och utdata av kompilerare|
| -RunScopeCEP [standardvärde ' 0 ']|ScopeCEP-läge som ska användas|
| -ScopeCEPTempPath [standardvärdet ' Temp ']|Temp-sökväg som ska användas för strömmande data|
| -OptFlags [standardvärde ' ']|Kommaavgränsad lista över optimerings flaggor|

Här följer några exempel på användning.

Kompilera ett U-SQL-skript:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Kompilera ett U-SQL-skript och ange mappen data rot. Observera att det här kommer att skriva över variabeln uppsättnings miljö.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Kompilera ett U-SQL-skript och ange en arbets katalog, referens sammansättning och databas:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Köra kompilerade resultat

Kommandot **execute** används för att köra kompilerade resultat.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Följande är valfria argument för att **köra**:

|Argument|Standardvärde|Beskrivning|
|--------|-------------|-----------|
|– DataRoot | '' |Data rot för metadata-körning. Den används som standard i **LOCALRUN_DATAROOT** -miljövariabeln.|
|– Meddelande | '' |Dumpa meddelanden i-konsolen till en fil.|
|– Parallell | 81.1 |Indikator för att köra de genererade lokala körnings stegen med den angivna parallell nivån.|
|– Utförlig | ! |Indikator för att visa detaljerade utdata från körnings miljön.|

Här är ett exempel på användning:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Använda SDK med programmerings gränssnitt

Programmerings gränssnitten finns i LocalRunHelper.exe. Du kan använda dem för att integrera funktionerna i U-SQL SDK och C#-test ramverket för att skala ditt lokala U-SQL-skript. I den här artikeln ska jag använda standard C#-test projekt för att visa hur du använder dessa gränssnitt för att testa ditt U-SQL-skript.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Steg 1: skapa ett test projekt och konfiguration av C#-enhet

- Skapa ett C#-test projekt via fil > nytt > projekt > Visual C# > test > unit test projekt.
- Lägg till LocalRunHelper.exe som referens för projektet. LocalRunHelper.exe finns på \build\runtime\LocalRunHelper.exe i NuGet-paketet.

   ![Lägg till referens för Azure Data Lake U-SQL SDK](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK stöder **bara** x64-miljö, se till att ställa in build Platform Target som x64. Du kan ställa in detta genom projekt egenskap > bygga > plattforms mål.

   ![Azure Data Lake U-SQL SDK konfigurera x64-projekt](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Se till att ange din test miljö som x64. I Visual Studio kan du ställa in det genom test > test inställningar > standard processor arkitektur > x64.

   ![Azure Data Lake U-SQL SDK konfigurera x64 test miljö](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Se till att kopiera alla beroende filer under NugetPackage\build\runtime\ till projekt arbets katalog, vanligt vis under ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Steg 2: skapa ett U-SQL-skript test fall

Nedan visas exempel koden för U-SQL script-test. För testning måste du förbereda skript, indatafiler och förväntade filer.

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Programmerings gränssnitt i LocalRunHelper.exe

LocalRunHelper.exe tillhandahåller programmerings gränssnitt för lokal U-SQL-kompilering, körning osv. Gränssnitten visas på följande sätt.

### <a name="constructor"></a>Konstruktor

offentlig LocalRunHelper ([system. IO. TextWriter messageOutput = null])

|Parameter|Typ|Beskrivning|
|---------|----|-----------|
|messageOutput|System. IO. TextWriter|för utgående meddelanden anger du null för att använda konsolen|

### <a name="properties"></a>Egenskaper

|Egenskap|Typ|Beskrivning|
|--------|----|-----------|
|AlgebraPath|sträng|Sökvägen till algebra-filen (algebra-filen är en av resultatet av kompileringen)|
|CodeBehindReferences|sträng|Om skriptet har ytterligare kod bakom referenser anger du Sök vägarna avgränsade med semikolon (;)|
|CppSdkDir|sträng|CppSDK-katalog|
|CurrentDir|sträng|Aktuell katalog|
|DataRoot|sträng|Sökväg för data rot|
|DebuggerMailPath|sträng|Sökvägen till fel söknings fack|
|GenerateUdoRedirect|boolesk|Om vi vill generera en åsidosättning av omdirigering av sammansättnings inläsning|
|HasCodeBehind|boolesk|Om skriptet har kod bakom|
|InputDir|sträng|Katalog för indata|
|MessagePath|sträng|Sökväg till meddelande dumpnings fil|
|OutputDir|sträng|Katalog för utdata|
|Parallellitet|int|Parallellitet för att köra algebra|
|ParentPid|int|PID för den överordnade tjänst övervakaren som ska avslutas, ange till 0 eller negativ för att ignorera|
|ResultPath|sträng|Sökväg till dump-fil|
|RuntimeDir|sträng|Körnings katalog|
|ScriptPath|sträng|Var du hittar skriptet|
|Grundbuffrade|boolesk|Ytlig kompilering eller inte|
|TempDir|sträng|Tillfällig katalog|
|UseDataBase|sträng|Ange databasen som ska användas för kod bakom tillfällig sammansättnings registrering, Master som standard|
|WorkDir|sträng|Prioriterad arbets katalog|

### <a name="method"></a>Metod

|Metod|Beskrivning|Returrelaterade|Parameter|
|------|-----------|------|---------|
|offentlig bool-DoCompile ()|Kompilera U-SQL-skriptet|Sant vid lyckad| |
|offentlig bool-DoExec ()|Kör det kompilerade resultatet|Sant vid lyckad| |
|offentlig bool-DoRun ()|Kör U-SQL-skriptet (kompilera och kör)|Sant vid lyckad| |
|offentlig bool-IsValidRuntimeDir (sträng väg)|Kontrol lera om den angivna sökvägen är giltig runtime-sökväg|Sant för giltig|Sökvägen till körnings katalogen|

## <a name="faq-about-common-issue"></a>Vanliga frågor och svar om vanliga problem

### <a name="error-1"></a>Fel 1

E_CSC_SYSTEM_INTERNAL: internt fel! Det gick inte att läsa in filen eller sammansättningen ScopeEngineManaged.dll eller ett av dess beroenden. Det gick inte att hitta den angivna modulen.

Kontrollera följande:

- Se till att du har x64-miljö. Versions mål plattformen och test miljön bör vara x64, se **steg 1: skapa C#-enhets test projekt och konfiguration** ovan.
- Se till att du har kopierat alla beroende filer under NugetPackage\build\runtime\ till projektets arbets katalog.

## <a name="next-steps"></a>Nästa steg

- Information om U-SQL finns i [Kom igång med U-SQL-språk i Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Information om hur du loggar diagnostikinformation finns i [komma åt diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
- Om du vill se en mer komplex fråga, se [analysera webbplats loggar med Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Om du vill visa jobb information, se [Använd jobb webbläsare och jobb för Azure Data Lake Analytics jobb](data-lake-analytics-data-lake-tools-view-jobs.md).
- Om du vill använda körnings vyn för hörn visas [i Använd vyn hörn körning i data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
