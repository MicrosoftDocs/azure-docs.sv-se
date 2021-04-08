---
title: Skapa Azure Web-och Worker-roller för PHP
description: En guide för att skapa PHP Web-och Worker-roller i en moln tjänst i Azure och konfigurera PHP-körningsmiljön.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 0b02662658b2199a13f117ad95c6402f9b2974f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95519854"
---
# <a name="create-php-web-and-worker-roles"></a>Skapa PHP webb- och arbetsroller

## <a name="overview"></a>Översikt

I den här guiden får du lära dig hur du skapar PHP Web-eller Worker-roller i en Windows-utvecklings miljö, väljer en speciell version av PHP från "inbyggda" versioner, ändrar PHP-konfigurationen, aktiverar tillägg och slutligen distribuerar till Azure. Det beskriver också hur du konfigurerar en webb-eller arbets roll för att använda en PHP-körning (med anpassad konfiguration och tillägg) som du anger.

Azure tillhandahåller tre beräknings modeller för att köra program: Azure App Service, Azure Virtual Machines och Azure Cloud Services. Alla tre modeller har stöd för PHP. Cloud Services, som innehåller webb-och arbets roller, tillhandahåller *PaaS (Platform as a Service)*. I en moln tjänst tillhandahåller en webbroll en dedikerad Internet Information Services (IIS)-webb server som värd för frontend-webbprogram. En arbets roll kan köra asynkrona, långvariga eller beständig uppgifter oberoende av användar interaktion eller indata.

Mer information om de här alternativen finns i avsnittet [Compute hosting-alternativ som tillhandahålls av Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Hämta Azure SDK för PHP

[Azure SDK för php](https://github.com/Azure/azure-sdk-for-php) består av flera komponenter. Den här artikeln kommer att använda två av dem: Azure PowerShell och Azure-emulatorer. Dessa två komponenter kan installeras via installations programmet för Microsoft-webbplattform. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

## <a name="create-a-cloud-services-project"></a>Skapa ett Cloud Services-projekt

Det första steget i att skapa en PHP Web-eller Worker-roll är att skapa ett Azure Service-projekt. ett Azure Service-projekt fungerar som en logisk behållare för webb-och arbets roller och innehåller projektets [tjänst definitions-(. csdef)] och [tjänst konfigurations fil (. cscfg)] .

Om du vill skapa ett nytt Azure-tjänst projekt kör du Azure PowerShell som administratör och kör följande kommando:

```powershell
PS C:\>New-AzureServiceProject myProject
```

Det här kommandot skapar en ny katalog ( `myProject` ) som du kan lägga till webb-och arbets roller för.

## <a name="add-php-web-or-worker-roles"></a>Lägg till PHP Web-eller Worker-roller

Om du vill lägga till en PHP-webbroll i ett projekt kör du följande kommando i projektets rot Katalog:

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

Använd följande kommando för en arbets roll:

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName`Parametern är valfri. Om den utelämnas skapas roll namnet automatiskt. Den första webb rollen som skapas är `WebRole1` , den andra är `WebRole2` , och så vidare. Den första arbets rollen som skapas är `WorkerRole1` , den andra är `WorkerRole2` , och så vidare.
>
>

## <a name="use-your-own-php-runtime"></a>Använd din egen PHP-körning

I vissa fall, i stället för att välja en inbyggd PHP-körning och konfigurera den enligt beskrivningen ovan, kanske du vill ange en egen PHP-körning. Du kan till exempel använda samma PHP-körning i en webb-eller arbets roll som du använder i din utvecklings miljö. Detta gör det enklare att se till att programmet inte ändrar beteendet i produktions miljön.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurera en webbroll att använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en webbroll för att använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-webbroll enligt beskrivningen ovan i det här avsnittet.
2. Skapa en `php` mapp i `bin` mappen som finns i webbrollens rot Katalog och Lägg sedan till php-körningsmiljön (alla binärfiler, konfigurationsfiler, undermappar osv.) i `php` mappen.
3. VALFRITT Om din PHP-körning använder [Microsoft-drivrutinerna för php för SQL Server][sqlsrv drivers]måste du konfigurera din webbroll för att installera [SQL Server Native Client 2012][sql native client] när den har tillhandahållits. Det gör du genom att lägga till [ installations programmet försqlncli.msi x64] i `bin` mappen i webbrollens rot Katalog. Start skriptet som beskrivs i nästa steg kommer tyst att köra installations programmet när rollen är etablerad. Om din PHP-körning inte använder Microsoft-drivrutinerna för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definiera en start åtgärd som konfigurerar [Internet Information Services (IIS)][iis.net] att använda din php-körning för att hantera begär Anden för `.php` sidor. Det gör du genom att öppna `setup_web.cmd` filen (i `bin` filen för webbrollens rot Katalog) i en text redigerare och ersätta innehållet med följande skript:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Lägg till dina programfiler till din webb Rolls rot Katalog. Detta är webb serverns rot Katalog.
6. Publicera ditt program enligt beskrivningen i avsnittet [publicera ditt program](#publish-your-application) nedan.

> [!NOTE]
> `download.ps1`Skriptet (i `bin` mappen i webbrollens rot Katalog) kan tas bort när du följer stegen som beskrivs ovan för att använda din egen php-körning.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurera en arbets roll för att använda din egen PHP-körning

Följ dessa steg om du vill konfigurera en arbets roll för att använda en PHP-körning som du anger:

1. Skapa ett Azure Service-projekt och Lägg till en PHP-arbetsroll enligt beskrivningen ovan.
2. Skapa en `php` mapp i arbets rollens rot Katalog och Lägg sedan till php-körningsmiljön (alla binärfiler, konfigurationsfiler, undermappar osv.) i `php` mappen.
3. VALFRITT Om din PHP-körning använder [Microsoft-drivrutiner för php för SQL Server][sqlsrv drivers]måste du konfigurera arbets rollen för att installera [SQL Server Native Client 2012][sql native client] när den är etablerad. Det gör du genom att lägga till [ installations programmet försqlncli.msi x64] i arbets rollens rot Katalog. Start skriptet som beskrivs i nästa steg kommer tyst att köra installations programmet när rollen är etablerad. Om din PHP-körning inte använder Microsoft-drivrutinerna för PHP för SQL Server, kan du ta bort följande rad från skriptet som visas i nästa steg:

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. Definiera en start åtgärd som lägger till din `php.exe` körbara fil i miljövariabeln för arbets rollens sökväg när rollen är etablerad. Det gör du genom att öppna `setup_worker.cmd` filen (i arbets rollens rot Katalog) i en text redigerare och ersätta dess innehåll med följande skript:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Lägg till dina programfiler i arbets rollens rot Katalog.
6. Publicera ditt program enligt beskrivningen i avsnittet [publicera ditt program](#publish-your-application) nedan.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Kör ditt program i beräknings-och lagrings emulatorerna

Azure-emulatorer tillhandahåller en lokal miljö där du kan testa ditt Azure-program innan du distribuerar det till molnet. Det finns vissa skillnader mellan emulatorerna och Azure-miljön. För att förstå detta bättre, se [använd Azure Storage emulatorn för utveckling och testning](storage/common/storage-use-emulator.md).

Observera att du måste ha PHP installerat lokalt för att kunna använda Compute-emulatorn. Compute-emulatorn använder den lokala PHP-installationen för att köra programmet.

Kör projektet i emulatorn genom att köra följande kommando från projektets rot Katalog:

```powershell
PS C:\MyProject> Start-AzureEmulator
```

Du bör se utdata som liknar följande: 

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

Du kan se programmet som körs i emulatorn genom att öppna en webbläsare och bläddra till den lokala adressen som visas i utdata ( `http://127.0.0.1:81` i exempel resultatet ovan).

Kör följande kommando för att stoppa emulatorn:

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>Publicera programmet

Om du vill publicera ditt program måste du först importera dina publicerings inställningar med hjälp av cmdleten [import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) . Sedan kan du publicera programmet med hjälp av cmdleten [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) . Information om hur du loggar in finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [php Developer Center](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[tjänst definition (. csdef)]: /previous-versions/azure/reference/ee758711(v=azure.100)
[tjänst konfiguration (. cscfg)]: /previous-versions/azure/reference/ee758710(v=azure.100)
[iis.net]: https://www.iis.net/
[sql native client]: /sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64-installations program]: https://go.microsoft.com/fwlink/?LinkID=239648