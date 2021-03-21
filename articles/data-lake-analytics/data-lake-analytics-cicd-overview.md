---
title: Så här konfigurerar du en CI/CD-pipeline för Azure Data Lake Analytics
description: Lär dig hur du konfigurerar kontinuerlig integrering och kontinuerlig distribution för Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015272"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Så här konfigurerar du en CI/CD-pipeline för Azure Data Lake Analytics  

I den här artikeln får du lära dig hur du konfigurerar en pipeline för kontinuerlig integrering och distribution (CI/CD) för U-SQL-jobb och U-SQL-databaser.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Använd CI/CD för U-SQL-jobb

Azure Data Lake Tools för Visual Studio tillhandahåller U-SQL-projektfilen som hjälper dig att organisera U-SQL-skript. Genom att använda U-SQL-projektet för att hantera din U-SQL-kod blir det enklare att snabbt använda CI/CD-scenarier.

## <a name="build-a-u-sql-project"></a>Bygg ett U-SQL-projekt

Ett U-SQL-projekt kan byggas med Microsoft build Engine (MSBuild) genom att skicka motsvarande parametrar. Följ stegen i den här artikeln för att konfigurera en build-process för ett U-SQL-projekt.

### <a name="project-migration"></a>Projekt migrering

Kontrol lera att du har den senaste versionen av U-SQL-projektet innan du konfigurerar en build-uppgift för ett U-SQL-projekt. Öppna U-SQL-projektfilen i redigeraren och kontrol lera att du har dessa import objekt:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Om inte, har du två alternativ för att migrera projektet:

- Alternativ 1: ändra det gamla import objekt till föregående.
- Alternativ 2: öppna det gamla projektet i Azure Data Lake verktyg för Visual Studio. Använd en senare version än 2.3.3000.0. Den gamla projekt mal len uppgraderas automatiskt till den senaste versionen. Nya projekt som skapats med versioner nyare än 2.3.3000.0 använder den nya mallen.

### <a name="get-nuget"></a>Hämta NuGet

MSBuild tillhandahåller inget inbyggt stöd för U-SQL-projekt. För att få det här stödet måste du lägga till en referens för din lösning till paketet [Microsoft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet som lägger till den språk tjänst som krävs.

Om du vill lägga till NuGet-paket referensen högerklickar du på lösningen i Visual Studio Solution Explorer och väljer **Hantera NuGet-paket**. Du kan också lägga till en fil `packages.config` som heter i Solution-mappen och lägga till följande innehåll i den:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>Hantera U-SQL Database-referenser

U-SQL-skript i ett U-SQL-projekt kan ha frågeuttryck för U-SQL Database-objekt. I så fall måste du referera till motsvarande U-SQL Database-projekt som innehåller objekt definitionen innan du skapar U-SQL-projektet. Ett exempel är när du frågar en U-SQL-tabell eller en referens till en sammansättning.

Läs mer om [U-SQL Database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> DROP-instruktionen kan orsaka en oavsiktlig borttagning. Om du vill aktivera DROP-instruktionen måste du uttryckligen ange MSBuild-argumenten. **AllowDropStatement** kommer att aktivera icke-datarelaterad Drop-åtgärd, t. ex. drop Assembly och Drop Table Value-funktion. **AllowDataDropStatement** kommer att aktivera data relaterad Drop-åtgärd, t. ex. drop Table och Drop schema. Du måste aktivera AllowDropStatement innan du använder AllowDataDropStatement.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Bygg ett U-SQL-projekt med kommando raden för MSBuild

Migrera först projektet och hämta NuGet-paketet. Anropa sedan standard kommando raden för MSBuild med följande ytterligare argument för att bygga ditt U-SQL-projekt:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

Argumenten och värdena är följande:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Den här parametern refererar till NuGet-paketets installations Sök väg för språk tjänsten U-SQL.
- **USQLTargetType = merge eller SyntaxCheck**:

  - **Sammanfoga**. I sammanslagnings läget kompileras kod bakomliggande filer. Exempel är **. cs**-, **. py**-och **. r** -filer. Det resulterande användardefinierade kod biblioteket infogas i U-SQL-skriptet. Exempel är en dll-binärfil, python eller R-kod.

  - **SyntaxCheck**. SyntaxCheck-läget sammanfogar först kod-bakom filer till U-SQL-skriptet. Sedan kompileras U-SQL-skriptet för att verifiera din kod.

- **DataRoot = \<DataRoot path>**. DataRoot krävs bara för SyntaxCheck-läge. När skriptet bygger med SyntaxCheck-läge, kontrollerar MSBuild referenserna till databas objekt i skriptet. Innan du skapar konfigurerar du en matchande lokal miljö som innehåller de refererade objekten från U-SQL-databasen i DataRoot-mappen för build-datorn. Du kan också hantera dessa databas beroenden genom att [referera till ett U-SQL Database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild kontrollerar endast databas objekts referenser, inte filer.

- **EnableDeployment = True** eller **false**. EnableDeployment anger om det ska vara tillåtet att distribuera refererade U-SQL-databaser under skapande processen. Om du refererar till ett U-SQL Database-projekt och använder databas objekt i ditt U-SQL-skript anger du den här parametern till **True**.

### <a name="continuous-integration-through-azure-pipelines"></a>Kontinuerlig integrering via Azure-pipeliner

Förutom kommando raden kan du också använda Visual Studio-versionen eller en MSBuild-uppgift för att bygga U-SQL-projekt i Azure-pipelines. Om du vill konfigurera en pipeline för bygge, se till att lägga till två aktiviteter i build-pipeline: en NuGet Restore-uppgift och en MSBuild-uppgift.

![MSBuild-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Lägg till en NuGet Restore-uppgift för att hämta det NuGet-paket som innehåller lösningar som innehåller `Azure.DataLake.USQL.SDK` , så att MSBuild kan hitta U-SQL-språkmålen. Ange **Avancerad**  >  **mål katalog** till `$(Build.SourcesDirectory)/packages` om du vill använda exemplet med MSBuild-argument direkt i steg 2.

   ![NuGet Restore-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ange MSBuild-argument i Visual Studio build-verktyg eller i en MSBuild-uppgift som du ser i följande exempel. Eller så kan du definiera variabler för dessa argument i pipeline för Azure pipelines build.

   ![Definiera CI/CD MSBuild-variabler för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Bygg utdata för U-SQL-projekt

När du har kört en version skapas alla skript i U-SQL-projektet och skickas till en zip-fil med namnet `USQLProjectName.usqlpack` . Mappstrukturen i ditt projekt behålls i det zippade build-resultatet.

> [!NOTE]
> Bakomliggande filer för varje U-SQL-skript kommer att slås samman som ett infogat uttryck till skriptets build-utdata.

## <a name="test-u-sql-scripts"></a>Testa U-SQL-skript

Azure Data Lake tillhandahåller test projekt för U-SQL-skript och C#-UDO/UDAG/UDF:

- Lär dig hur du [lägger till test ärenden för U-SQL-skript och utökad C#-kod](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Lär dig hur du [Kör test ärenden i Azure-pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Distribuera ett U-SQL-jobb

När du har kontrollerat kod genom build-och test processen kan du skicka U-SQL-jobb direkt från Azure-pipelinen via en Azure PowerShell aktivitet. Du kan också distribuera skriptet till Azure Data Lake Store eller Azure Blob Storage och [köra de schemalagda jobben via Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Skicka U-SQL-jobb via Azure-pipeliner

Build-utdata från U-SQL-projektet är en zip-fil med namnet **USQLProjectName. usqlpack**. Zip-filen innehåller alla U-SQL-skript i projektet. Du kan använda [Azure PowerShell uppgiften](/azure/devops/pipelines/tasks/deploy/azure-powershell) i pipelines med följande exempel på PowerShell-skript för att skicka U-SQL-jobb direkt från Azure-pipeliner.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> Kommandona: `Submit-AzDataLakeAnalyticsJob` och `Wait-AzDataLakeAnalyticsJob` är båda Azure PowerShell-cmdletar för Azure Data Lake Analytics i Azure Resource Manager Framework. Du behöver en arbets station med Azure PowerShell installerad. Du kan referera till [kommando listan](/powershell/module/Az.DataLakeAnalytics) för fler kommandon och exempel.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Distribuera U-SQL-jobb via Azure Data Factory

Du kan skicka U-SQL-jobb direkt från Azure-pipeliner. Du kan också ladda upp de skapade skripten till Azure Data Lake Store eller Azure Blob Storage och [köra de schemalagda jobben via Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

Använd [Azure PowerShell uppgiften](/azure/devops/pipelines/tasks/deploy/azure-powershell) i Azure-pipeliner med följande PowerShell-skript för att överföra U-SQL-skript till ett Azure Data Lake Store-konto:

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD för en U-SQL-databas

Azure Data Lake Tools för Visual Studio innehåller Project-mallar för U-SQL-databasen som hjälper dig att utveckla, hantera och distribuera U-SQL-databaser. Läs mer om ett [U-SQL Database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Bygg U-SQL Database-projekt

### <a name="get-the-nuget-package"></a>Hämta NuGet-paketet

MSBuild tillhandahåller inget inbyggt stöd för U-SQL Database-projekt. För att få den här möjligheten måste du lägga till en referens för din lösning till paketet [Microsoft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet som lägger till den språk tjänst som krävs.

Om du vill lägga till NuGet-paket referensen högerklickar du på lösningen i Visual Studio Solution Explorer. Välj **Hantera NuGet-paket**. Sök sedan efter och installera NuGet-paketet. Du kan också lägga till en fil som heter **packages.config** i mappen Solution och lägga till följande innehåll i den:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Bygg U-SQL ett databas projekt med kommando raden för MSBuild

Om du vill bygga ditt U-SQL Database-projekt anropar du standard-kommando raden i MSBuild och skickar U-SQL SDK NuGet-paket referensen som ett ytterligare argument. Se följande exempel: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Argumentet `USQLSDKPath=<U-SQL Nuget package>\build\runtime` refererar till installations Sök vägen för NuGet-paketet för U-SQL-språktjänsten.

### <a name="continuous-integration-with-azure-pipelines"></a>Kontinuerlig integrering med Azure Pipelines

Förutom kommando raden kan du använda Visual Studio build eller en MSBuild-uppgift för att bygga U-SQL Database-projekt i Azure-pipelines. Om du vill konfigurera en bygge-uppgift, se till att lägga till två aktiviteter i build-pipeline: en NuGet Restore-uppgift och en MSBuild-uppgift.

   ![CI/CD MSBuild-uppgift för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Lägg till en NuGet Restore-uppgift för att hämta det NuGet-paket som omfattas av lösningen, vilket innefattar `Azure.DataLake.USQL.SDK` , så att MSBuild kan hitta U-SQL-språkmålen. Ange **Avancerad**  >  **mål katalog** till `$(Build.SourcesDirectory)/packages` om du vill använda exemplet med MSBuild-argument direkt i steg 2.

   ![CI/CD NuGet-aktivitet för ett U-SQL-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Ange MSBuild-argument i Visual Studio build-verktyg eller i en MSBuild-uppgift som du ser i följande exempel. Eller så kan du definiera variabler för dessa argument i pipeline för Azure pipelines build.

   ![Definiera CI/CD MSBuild-variabler för ett U-SQL Database-projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>U-SQL Database Project build-utdata

Build-utdata för ett U-SQL Database-projekt är ett U-SQL-databas distributions paket med namnet med suffixet `.usqldbpack` . `.usqldbpack`Paketet är en zip-fil som innehåller alla DDL-instruktioner i ett enda U-SQL-skript i en DDL-mapp. Den innehåller alla **DLL** -filer och ytterligare filer för sammansättning i en Temp-mapp.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Testa tabell värdes funktioner och lagrade procedurer

Att lägga till test fall för tabell värdes funktioner och lagrade procedurer direkt stöds inte för närvarande. Som en lösning kan du skapa ett U-SQL-projekt som har U-SQL-skript som anropar dessa funktioner och skriver test fall för dem. Utför följande steg för att ställa in test fall för tabell värdes funktioner och lagrade procedurer som definierats i U-SQL Database-projektet:

1. Skapa ett U-SQL-projekt för test syfte och skriv U-SQL-skript som anropar tabell värdes funktionerna och de lagrade procedurerna.

1. Lägg till en databas referens i U-SQL-projektet. Om du vill hämta tabell värdes funktionen och definitionen för den lagrade proceduren måste du referera till det databas projekt som innehåller DDL-instruktionen. Läs mer om [databas referenser](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Lägg till test ärenden för U-SQL-skript som anropar tabell värdes funktioner och lagrade procedurer. Lär dig hur du [lägger till test ärenden för U-SQL-skript](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Distribuera U-SQL-databas via Azure-pipeliner

`PackageDeploymentTool.exe` tillhandahåller programmerings-och kommando rads gränssnitt som hjälper till att distribuera distributions paket för U-SQL-databasen, **. usqldbpack**. SDK ingår i [U-SQL SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), som finns på **build/runtime/PackageDeploymentTool.exe**. Med hjälp av `PackageDeploymentTool.exe` kan du distribuera U-SQL-databaser till både Azure Data Lake Analytics och lokala konton.

> [!NOTE]
>
> Stöd för PowerShell-kommandorad och Azure pipelines-support för att lansera en U-SQL Database-distribution är för närvarande väntande.
>

Utför följande steg för att konfigurera en databas distributions uppgift i Azure-pipelines:

1. Lägg till en PowerShell-skriptfil i en build-eller release-pipeline och kör följande PowerShell-skript. Med den här aktiviteten kan du hämta Azure SDK-beroenden för `PackageDeploymentTool.exe` och `PackageDeploymentTool.exe` . Du kan ange parametrarna **-AzureSDK** och **-DBDeploymentTool** för att läsa in beroenden och distributions verktyget till vissa mappar. Skicka **AzureSDK-** sökvägen till `PackageDeploymentTool.exe` som parametern **-AzureSDKPath** i steg 2.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Lägg till en **kommando rad aktivitet** i en pipeline för build eller release och fyll i skriptet genom att anropa `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` finns under den definierade **$DBDeploymentTool** mappen. Exempel skriptet ser ut så här: 

   - Distribuera en U-SQL-databas lokalt:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Använd interaktivt autentiseringsläge för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Använd **hemlig** autentisering för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Använd **certifikat** -autentisering för att distribuera en U-SQL-databas till ett Azure Data Lake Analytics-konto:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe parameter beskrivningar

#### <a name="common-parameters"></a>Vanliga parametrar

| Parameter | Beskrivning | Standardvärde | Obligatorisk |
|---------|-----------|-------------|--------|
|Paket|Sökvägen till U-SQL-databasens distributions paket som ska distribueras.|null|true|
|Databas|Det databas namn som ska distribueras till eller skapas.|master|falskt|
|LogFile|Sökvägen till filen för loggning. Standard ut för standard ut (konsol).|null|falskt|
|Loggnivå|Loggnings nivå: utförlig, normal, varning eller fel.|LogLevel. normal|falskt|

#### <a name="parameter-for-local-deployment"></a>Parameter för lokal distribution

|Parameter|Beskrivning|Standardvärde|Obligatorisk|
|---------|-----------|-------------|--------|
|DataRoot|Sökvägen till den lokala rotmappen för data.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parametrar för Azure Data Lake Analytics distribution

|Parameter|Beskrivning|Standardvärde|Obligatorisk|
|---------|-----------|-------------|--------|
|Konto|Anger vilket Azure Data Lake Analytics-konto som ska distribueras efter konto namn.|null|true|
|ResourceGroup|Namnet på Azure-resurs gruppen för Azure Data Lake Analytics kontot.|null|true|
|SubscriptionId|ID för Azure-prenumerationen för Azure Data Lake Analytics-kontot.|null|true|
|Klientorganisation|Klient namnet är Azure Active Directory (Azure AD)-domän namnet. Hitta den på sidan prenumerations hantering i Azure Portal.|null|true|
|AzureSDKPath|Sökvägen för att söka efter beroende sammansättningar i Azure SDK.|null|true|
|Interaktiv|Huruvida interaktivt läge ska användas för autentisering.|falskt|falskt|
|ClientId|Det program-ID för Azure AD som krävs för icke-interaktiv autentisering.|null|Krävs för icke-interaktiv autentisering.|
|Hemlighet|Hemligheten eller lösen ordet för icke-interaktiv autentisering. Den bör endast användas i en betrodd och säker miljö.|null|Krävs för icke-interaktiv autentisering eller Använd SecreteFile.|
|SecreteFile|Filen sparar hemligheten eller lösen ordet för icke-interaktiv autentisering. Se till att hålla det läsbart för den aktuella användaren.|null|Krävs för icke-interaktiv autentisering eller Använd hemlighet.|
|Certifikat fil|Filen sparar X. 509-certifiering för icke-interaktiv autentisering. Standardvärdet är att använda autentisering med klient hemlighet.|null|falskt|
| JobPrefix | Prefixet för databas distribution av ett U-SQL DDL-jobb. | Deploy_ + DateTime. nu | falskt |

## <a name="next-steps"></a>Nästa steg

- [Så här testar du din Azure Data Lake Analyticss kod](data-lake-analytics-cicd-test.md).
- [Kör U-SQL-skriptet på den lokala datorn](data-lake-analytics-data-lake-tools-local-run.md).
- [Använd u-SQL Database-projekt för att utveckla U-SQL-databasen](data-lake-analytics-data-lake-tools-develop-usql-database.md).