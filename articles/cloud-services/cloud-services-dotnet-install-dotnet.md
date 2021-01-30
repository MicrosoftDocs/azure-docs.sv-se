---
title: Installera .NET på Azure Cloud Services (klassiska) roller | Microsoft Docs
description: Den här artikeln beskriver hur du installerar .NET Framework manuellt i webb-och arbets roller för moln tjänsten
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061859"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Installera .NET på Azure Cloud Services (klassiska) roller

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln beskriver hur du installerar versioner av .NET Framework som inte ingår i Azures gäst operativ system. Du kan använda .NET på gäst operativ systemet för att konfigurera webb-och arbets roller för moln tjänsten.

Du kan till exempel installera .NET Framework 4.6.2 på gäst operativ system familj 4, som inte ingår i någon version av .NET Framework 4,6. (Gäst operativ system familjen 5 levereras med .NET Framework 4,6.) Den senaste informationen om Azure-gästens OS-versioner finns i [nyheter om Azure gäst operativ system](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Azure SDK 2,9 innehåller en begränsning för distribution av .NET Framework 4,6 i gäst operativ system familj 4 eller tidigare. En korrigering för begränsningen finns på den [Microsoft docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) webbplatsen.

Om du vill installera .NET på dina webb-och arbets roller inkluderar du .NET-webb installations programmet som en del av ditt moln tjänst projekt. Starta installations programmet som en del av rollens start aktiviteter. 

## <a name="add-the-net-installer-to-your-project"></a>Lägg till .NET-installations programmet i projektet
Om du vill ladda ned webb installations programmet för .NET Framework väljer du den version som du vill installera:

* [.NET Framework 4,8-webb installations program](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [.NET Framework 4.7.2 Web Installer](https://go.microsoft.com/fwlink/?LinkId=863262)
* [.NET Framework 4.6.2 Web Installer](https://www.microsoft.com/download/details.aspx?id=53345)

Så här lägger du till installations programmet för en *webb* roll:
  1. I **Solution Explorer**, under **roller** i ditt moln tjänst projekt, högerklickar du på din *webb* roll och väljer **Lägg till**  >  **ny mapp**. Skapa en mapp med namnet **bin**.
  2. Högerklicka på mappen bin och välj **Lägg till**  >  **befintligt objekt**. Välj .NET-installations programmet och Lägg till det i bin-mappen.
  
Så här lägger du till installations programmet för en *arbets* roll:
* Högerklicka på din *arbets* roll och välj **Lägg till**  >  **befintligt objekt**. Välj .NET-installations programmet och Lägg till det i rollen. 

När filer läggs till på det här sättet i mappen roll innehåll läggs de automatiskt till i moln tjänst paketet. Filerna distribueras sedan till en konsekvent plats på den virtuella datorn. Upprepa den här processen för varje webb-och arbets roll i moln tjänsten så att alla roller har en kopia av installations programmet.

> [!NOTE]
> Du bör installera .NET Framework 4.6.2 på din moln tjänst roll även om ditt program är mål .NET Framework 4,6. Gäst operativ systemet innehåller kunskaps bas [uppdateringen 3098779](https://support.microsoft.com/kb/3098779) och [uppdatering 3097997](https://support.microsoft.com/kb/3097997). Problem kan uppstå när du kör dina .NET-program om .NET Framework 4,6 installeras ovanpå kunskaps bas uppdateringarna. Undvik dessa problem genom att installera .NET Framework 4.6.2 i stället för version 4,6. Mer information finns i [Knowledge Base-artikeln 3118750](https://support.microsoft.com/kb/3118750) och [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Roll innehåll med installationsfiler][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definiera start aktiviteter för dina roller
Du kan använda Start åtgärder för att utföra åtgärder innan en roll startar. Att installera .NET Framework som en del av start aktiviteten säkerställer att ramverket installeras innan någon program kod körs. Mer information om start åtgärder finns i [köra start åtgärder i Azure](cloud-services-startup-tasks.md). 

1. Lägg till följande innehåll i service definition. csdef-filen under **Webrole** -eller **WorkerRole** -noden för alla roller:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Föregående konfiguration kör konsol kommandot `install.cmd` med administratörs behörighet för att installera .NET Framework. Konfigurationen skapar också ett **localStorage** -element med namnet **NETFXInstall**. Start skriptet anger Temp-mappen som ska användas för den här lokala lagrings resursen. 
    
    > [!IMPORTANT]
    > För att säkerställa korrekt installation av ramverket anger du storleken på den här resursen till minst 1 024 MB.
    
    Mer information om start åtgärder finns i [vanliga åtgärder för att starta Azure-Cloud Services](cloud-services-startup-tasks-common.md).

2. Skapa en fil med namnet **install. cmd** och Lägg till följande installations skript i filen.

   Skriptet kontrollerar om den angivna versionen av .NET Framework redan har installerats på datorn genom att fråga registret. Om .NET Framework-versionen inte är installerad öppnas .NET Framework webb installations program. För att hjälpa till att felsöka eventuella problem loggar skriptet all aktivitet till filen startuptasklog – (aktuellt datum och tid). txt som lagras i lokal **InstallLogs** -lagring.
   
   > [!IMPORTANT]
   > Använd en enkel text redigerare, t. ex. Windows Anteckningar, för att skapa install. cmd-filen. Om du använder Visual Studio för att skapa en textfil och ändrar tillägget till. cmd, kan filen fortfarande innehålla ett tecken för UTF-8-byte. Det här märket kan orsaka ett fel när den första raden i skriptet körs. Undvik det här felet genom att göra den första raden i skriptet en REM-instruktion som kan hoppas över av bearbetningen av byte ordern. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Lägg till filen Install. cmd i varje roll genom att använda **Lägg till**  >  **befintligt objekt** i **Solution Explorer** enligt beskrivningen ovan i det här avsnittet. 

    När det här steget har slutförts ska alla roller ha .NET-installations filen och filen Install. cmd.

   ![Roll innehåll med alla filer][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurera diagnostik för att överföra start loggar till Blob Storage
För att under lätta fel sökning av installations problem kan du konfigurera Azure-diagnostik att överföra loggfiler som genereras av start skriptet eller .NET-installations programmet till Azure Blob Storage. Genom att använda den här metoden kan du visa loggarna genom att hämta loggfilerna från Blob Storage i stället för att behöva använda fjärr skrivbord i rollen.


Om du vill konfigurera diagnostik öppnar du filen Diagnostics. wadcfgx och lägger till följande innehåll under noden **kataloger** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Den här XML-koden konfigurerar diagnostik för att överföra filerna i logg katalogen i **NETFXInstall** -resursen till kontot för diagnostik-lagring i BLOB **-behållaren NetFx-install** .

## <a name="deploy-your-cloud-service"></a>Distribuera din moln tjänst
När du distribuerar din moln tjänst installerar start aktiviteterna .NET Framework om de inte redan är installerade. Moln tjänst rollerna är i läget *upptagen* medan ramverket installeras. Om Framework-installationen kräver en omstart kan tjänst rollerna också starta om. 

## <a name="additional-resources"></a>Ytterligare resurser
* [Installera .NET Framework][Installing the .NET Framework]
* [Ta reda på vilka .NET Framework-versioner som är installerade][How to: Determine Which .NET Framework Versions Are Installed]
* [Felsöka .NET Framework installationer][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



