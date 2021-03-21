---
title: Använda .NET SDK för Microsoft Azure StorSimple Data Manager jobb
description: Lär dig hur du använder .NET SDK i StorSimple Data Manager-tjänsten för att transformera StorSimple enhets data.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b18627d2806662d6d966af95d51873d5623b2393
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015544"
---
# <a name="use-the-net-sdk-to-initiate-data-transformation"></a>Använda .NET SDK till att initiera dataomvandlingen

## <a name="overview"></a>Översikt

Den här artikeln förklarar hur du kan använda funktionen datatransformering i StorSimple Data Manager-tjänsten för att transformera StorSimple enhets data. Transformerade data används sedan av andra Azure-tjänster i molnet.

Du kan starta ett data omvandlings jobb på två sätt:

- Använda .NET SDK
- Använd Azure Automation Runbook
 
  Den här artikeln beskriver hur du skapar ett exempel på ett .NET-konsol program som initierar ett datatransformerings jobb och sedan spårar det för slut för ande. Om du vill veta mer om hur du initierar datatransformering via Automation går du till [använd Azure Automation Runbook för att utlösa data omvandlings jobb](storsimple-data-manager-job-using-automation.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har:
*   En dator som kör:

    - Visual Studio 2012, 2013, 2015 eller 2017.

    - Azure PowerShell. [Ladda ned Azure PowerShell](/powershell/azure/).
*   En korrekt konfigurerad jobb definition i StorSimple Data Manager inom en resurs grupp.
*   Alla obligatoriska DLL-filer. Hämta DLL-filerna från [GitHub-lagringsplatsen](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls).
*   [`Get-ConfigurationParams.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1) skript från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Steg för steg-procedur

Utför följande steg för att använda .NET för att starta ett data omvandlings jobb.

1. Gör så här för att hämta konfigurations parametrarna:
    1. Hämta `Get-ConfigurationParams.ps1` från skriptet för GitHub-lagringsplatsen på `C:\DataTransformation` plats.
    1. Kör `Get-ConfigurationParams.ps1` skriptet från GitHub-lagringsplatsen. Ange följande kommando:

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        Du kan skicka in värden för ActiveDirectoryKey och AppName.

2. Det här skriptet matar ut följande värden:
    * Klient-ID
    * Klient-ID:t
    * Active Directory nyckel (samma som den som anges ovan)
    * Prenumerations-ID:t

        ![Skript utdata för konfigurations parametrar](media/storsimple-data-manager-dotnet-jobs/get-config-parameters.png)

3. Skapa ett C# .NET-konsol program med hjälp av Visual Studio 2012, 2013 eller 2015.

    1. Starta **Visual Studio 2012/2013/2015**.
    1. Välj **Arkiv > Nytt > Projekt**.

        ![Skapa ett projekt 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-7.png)        
    2. Välj **installerade > mallar > Visual C# >-konsol program**.
    3. Ange **DataTransformationApp** som **namn**.
    4. Välj **C:\DataTransformation** för **platsen**.
    6. Klicka på **OK** för att skapa projektet.

        ![Skapa ett projekt 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-1.png)

4. Nu ska du lägga till alla DLL-filer som finns i [mappen dll](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) -filer som **referenser** i projektet som du har skapat. Gör så här för att lägga till DLL-filerna:

   1. Gå till **visa > Solution Explorer** i Visual Studio.
   2. Klicka på pilen till vänster om data omvandling-exempelprojektet. Klicka på **referenser** och högerklicka för att **lägga till referens**.
    
       ![Lägg till dll 1](media/storsimple-data-manager-dotnet-jobs/create-new-project-4.png)

   3. Bläddra till platsen för mappen packages, Välj alla DLL: er och klicka på **Lägg till** och sedan på **OK**.

       ![Lägg till DLL 2](media/storsimple-data-manager-dotnet-jobs/create-new-project-6.png)

5. Lägg till följande **genom att använda** instruktioner till källfilen (Program.cs) i projektet.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```
    
6. Följande kod initierar data omvandlings jobb instansen. Lägg till det i **main-metoden**. Ersätt värdena för de konfigurations parametrar som hämtades tidigare. Anslut värdena för **resurs gruppens namn** och **resourceName**. **ResourceGroupName** är associerad med StorSimple Data Manager som jobb definitionen har kon figurer ATS på. **ResourceName** är namnet på din StorSimple Data Manager-tjänst.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);
    ```
   
7. Ange de parametrar som jobb definitionen måste köras med

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);
    ```

    ELLER

    Om du vill ändra jobb definitions parametrarna under körnings tiden lägger du till följande kod:

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    ```

8. Efter initieringen lägger du till följande kod för att utlösa ett data omvandlings jobb på jobb definitionen. Anslut lämpligt **jobb definitions namn**.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);
    Console.WriteLine("jobid: ", jobId);
    Console.ReadLine();

    ```
    När koden har klistrats in skapar du lösningen. Här är en skärm bild av kodfragmentet för att initiera data omvandlings jobb instansen.

   ![Kodfragment för att initiera data omvandlings jobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet-1.png)

9. Det här jobbet transformerar data som matchar rot katalogen och fil filtren i StorSimple-volymen och placerar dem i den angivna behållaren/fil resursen. När en fil omvandlas läggs ett meddelande till i en lagrings kö (i samma lagrings konto som behållaren/fil resursen) med samma namn som jobb definitionen. Det här meddelandet kan användas som en utlösare för att initiera eventuell bearbetning av filen.

10. När jobbet har utlösts kan du använda följande kod för att spåra jobbet för slut för ande. Det är inte obligatoriskt att lägga till den här koden för jobb körningen.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```
    Här är en skärm bild av hela kod exemplet som används för att utlösa jobbet med .NET.

    ![Fullständigt kod avsnitt för att utlösa ett .NET-jobb](media/storsimple-data-manager-dotnet-jobs/start-dotnet-job-code-snippet.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager användar gränssnitt för att transformera data](storsimple-data-manager-ui.md).