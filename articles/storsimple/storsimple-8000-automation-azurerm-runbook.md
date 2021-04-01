---
title: Använd Azure Automation Runbook för att hantera StorSimple-enheter
description: Lär dig hur du använder Azure Automation Runbook för att hantera din StorSimple 8000-serie enhet i Azure Portal.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021086"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Använd Azure Automation runbooks för att hantera StorSimple-enheter

I den här artikeln beskrivs hur Azure Automation runbooks används för att hantera din StorSimple 8000-serie enhet i Azure Portal. En exempel-Runbook ingår för att vägleda dig genom stegen för att konfigurera din miljö för att köra denna Runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurera, lägga till och köra Azure Runbook

Det här avsnittet tar ett exempel på Windows PowerShell-skript för StorSimple och beskriver de olika stegen som krävs för att importera skriptet till en Runbook och sedan publicera och köra runbooken.

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar ska du kontrol lera att du har:

* en aktiv Azure-prenumeration som är associerad med din StorSimple Enhetshanteraren-tjänst som registrerats med en StorSimple 8000-serie enhet.

* Windows PowerShell 5,0 installerat på datorn (eller Windows Server-värden för din StorSimple om du använder en).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Skapa Automation Runbook-modul i Windows PowerShell

Utför följande steg för att skapa en Automation-modul för enhets hantering i StorSimple 8000-serien:

1. Starta Windows PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Hämta NUGET CLI](https://www.nuget.org/downloads) under den mapp som skapades i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje nedladdnings länk pekar direkt till en _. exe_ -fil. Tänk på att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.

    Du kan också köra följande kommando för att ladda ned och lagra skriptet i samma mapp som du skapade tidigare.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Hämta det beroende SDK: n.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Hämta skriptet från exemplet GitHub Project.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Skapa en Azure Automation Runbook-modul för enhets hantering i StorSimple 8000-serien. Skriv följande kommandon i Windows PowerShell-fönstret:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Kontrol lera att zip-filen för Automation-modulen har skapats i `C:\scripts\StorSimpleSDKTools` .

    ![Skärm bild som visar StorSimpleSDKTools-mappen med Automation-modulens zip-fil](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Följande utdata visas när Automation-modulen skapas via Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-Runbook

1. Skapa ett Azure kör som Automation-konto i Azure Portal. Det gör du genom att gå till **Azure marketplace > allt** och sedan söka efter **Automation**. Välj **Automation-konton**.

    ![Sök-automatisering](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. På bladet **Lägg till Automation-konto** :

   1. Ange **namnet** på ditt Automation-konto.
   2. Välj den **prenumeration** som är länkad till din StorSimple Enhetshanteraren-tjänst.
   3. Skapa en ny resurs grupp eller Välj en befintlig resurs grupp.
   4. Välj en **plats** (om möjligt är det samma som var tjänsten körs).
   5. Låt standard alternativet **skapa kör som-konto** vara markerat.
   6. Du kan också markera **Fäst på instrument panelen**. Klicka på **Skapa**.

       ![Skärm bild som visar fönstret Lägg till Automation-konto med värden för prenumeration, skapa kör som-konto i Azure och skapa 2](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      När Automation-kontot har skapats får du ett meddelande. Mer information om hur du skapar ett Automation-konto finns i [skapa ett Kör som-konto](../automation/manage-runas-account.md).

3. För att säkerställa att Automation-kontot som skapas kan komma åt StorSimple-Enhetshanteraren tjänsten måste du tilldela lämpliga behörigheter till Automation-kontot. Gå till **åtkomst kontroll** i din StorSimple Enhetshanteraren-tjänst. Klicka på **+ Lägg till** och ange namnet på ditt Azure Automation konto. **Spara** inställningarna.

    ![Add-Permissions-Automation-Account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. I det nyligen skapade kontot går du till **delade resurser > moduler** och klickar på **+ Lägg till modul**.

5. I bladet **Lägg till modul** bläddrar du till platsen för den zippade modulen och väljer och öppnar modulen. Klicka på **OK**.

    ![Skärm bild som visar fönstret Lägg till modul med värdet upload File och OK](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Gå till **process automatisering > Runbooks och klicka på + Lägg till en Runbook**. På bladet **Lägg till Runbook** klickar du på **Importera en befintlig Runbook**. Peka på Windows PowerShell-skriptfilen för Runbook- **filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbooken. Klicka på **Skapa**.

    ![Lägg till modul](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Runbooken läggs till i listan över Runbooks. Välj och klicka på denna Runbook.

    ![Klicka på ny-Runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Redigera runbooken och klicka på **test fönster**. Ange parametrar som namn på din StorSimple-Enhetshanteraren tjänst, namnet på StorSimple-enheten och prenumerationen. **Starta** testet. Rapporten skapas när körningen har slutförts. Mer information finns i så här [testar du en Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Skärm bild som visar testet där du canenter parameter värden och startar ett test](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Granska utdata från runbooken i test fönstret. Stäng fönstret om det är uppfyllt. Klicka på **publicera** och när du uppmanas att bekräfta, bekräfta och publicera runbooken.

    ![Skärm bild som visar fönstret Redigera PowerShell-Runbook som ber dig att fortsätta att publicera runbooken](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Enhetshanteraren-tjänsten för att hantera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).