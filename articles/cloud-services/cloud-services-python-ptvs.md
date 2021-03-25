---
title: Kom igång med python och Azure Cloud Services (klassisk) | Microsoft Docs
description: Översikt över hur du använder Python Tools för Visual Studio för att skapa Azure-molntjänster, inklusive webb- och arbetsroller.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2822f719928515efc70eeed3d7c182e347627418
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045526"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Webb- och arbetsroller för Python med Python Tools för Visual Studio

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

I den här artikeln ges en översikt över hur du använder webb- och arbetsroller för Python med hjälp av [Python Tools för Visual Studio][Python Tools for Visual Studio]. Lär dig hur du använder Visual Studio för att skapa och distribuera en grundläggande molntjänst som använder Python.

## <a name="prerequisites"></a>Förutsättningar
* [Visual Studio 2013, 2015 eller 2017](https://www.visualstudio.com/)
* [Python Tools för Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK Tools för VS 2013][Azure SDK Tools for VS 2013] eller  
[Azure SDK Tools för VS 2015][Azure SDK Tools for VS 2015] eller  
[Azure SDK Tools för VS 2017][Azure SDK Tools for VS 2017]
* [Python 2,7 32-bitars][Python 2.7 32-bit] eller [python 3,8 32-bit][Python 3.8 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Vad är webb- och arbetsroller för Python?
Azure har tre beräkningsmodeller för att köra program: [funktionen Web Apps i Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] och [Azure Cloud Services][execution model-cloud services]. Alla tre modeller stöder Python. Cloud Services, där webb- och arbetsroller ingår, tillhandahåller *plattform som en tjänst (PaaS)*. I en molntjänst tillhandahåller en webbroll en dedikerad IIS-webbserver (Internet Information Services) som fungerar som värd för frontend-webbprogram, medan en arbetsroll kan köra asynkrona, tidskrävande eller beständiga uppgifter oberoende av användarinteraktion eller indata.

Mer information finns i [Vad är en molntjänst?].

> [!NOTE]
> *Vill du skapa en enkel webbplats?*
> Om ditt scenario bara är en enkel webbplats bör du överväga att använda den förenklade funktionen Web Apps i Azure App Service. Det är lätt att uppgradera till en molntjänst efter hand som webbplatsen växer och dina behov förändras. På [Python Developer Center](https://azure.microsoft.com/develop/python/) hittar du artiklar om utvecklingen av funktionen Web Apps i Azure App Service.
> <br />
> 
> 

## <a name="project-creation"></a>Skapa projekt
I Visual Studio kan du välja **Azure Cloud Service** i dialogrutan **Nytt projekt** under **Python**.

![Dialogrutan Nytt projekt](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Du kan skapa nya webb- och arbetsroller i Azure Cloud Services-guiden.

![Dialogrutan för Azure Cloud Services](./media/cloud-services-python-ptvs/new-service-wizard.png)

Mallen för arbetsroller innehåller formaterad exempelkod för anslutning till ett Azure-lagringskonto eller till Azure Service Bus.

![Molntjänstlösning](./media/cloud-services-python-ptvs/worker.png)

Du kan lägga till webb- eller arbetsroller till en befintlig molntjänst när som helst.  Du kan välja att lägga till befintliga projekt i din lösning eller skapa nya.

![Kommandot Lägg till roll](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Din molntjänst kan innehålla roller som implementeras på olika språk.  Exempelvis kan en Python-webbroll implementeras med hjälp av Django, med Python, eller med C#-arbetsroller.  Du kan enkelt kommunicera mellan dina roller med hjälp av Service Bus-köer eller lagringsköer.

## <a name="install-python-on-the-cloud-service"></a>Installera Python i molntjänsten
> [!WARNING]
> Installationsskripten som installeras med Visual Studio (när den här artikeln uppdaterades senast) fungerar inte. Det här avsnittet beskriver en lösning.
> 
> 

Huvudproblemet med installationsskripten är att de inte installerar Python. Definiera först två [startaktiviteter](cloud-services-startup-tasks.md) i filen [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). Den första aktiviteten (**PrepPython.ps1**) hämtar och installerar Python-körningen. Den andra aktiviteten (**PipInstaller.ps1**) kör pip för att installera alla beroenden som du kan ha.

Följande skript har skrivits för python 3,8. Om du vill använda version 2.x av Python, ställer du in **PYTHON2**-variabelfilen till **på** för de båda startaktiviteterna och för körningsaktiviteten: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

Variablerna **PYTHON2** och **PYPATH** måste läggas till arbetsstartsaktiviteten. **PYPATH**-variabeln används bara om **PYTHON2**-variabeln anges till **på**.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Exempel ServiceDefinition.csdef
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Skapa sedan filerna **PrepPython.ps1** och **PipInstaller.ps1** i din rolls **. / bin**-mapp.

#### <a name="preppythonps1"></a>PrepPython.ps1
Det här skriptet installerar Python. Om **PYTHON2** -miljövariabeln är inställd på **på**, är python 2,7 installerat, annars installeras python 3,8.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.8.8/python-3.8.8-amd64.exe"
        $outFile = "${env:TEMP}\python-3.8.8-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.18/python-2.7.18.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.18.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Det här skriptet ringer upp pip och installerar alla beroenden i **requirements.txt**-filen. Om **PYTHON2** -miljövariabeln är inställd på **på**, används python 2,7, annars används python 3,8.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Ändra LaunchWorker.ps1
> [!NOTE]
> Vid ett **arbetsroll** projekt krävs filen **LauncherWorker.ps1** för att köra startfilen. Vid ett **webbroll** projekt definieras startfilen istället i egenskaperna för projektet.
> 
> 

**bin\LaunchWorker.ps1** skapades ursprungligen för att göra mycket förberedande arbete men det fungerar inte riktigt som tänkt. Ersätt innehållet i filen med följande skript.

Det här skriptet anropar **worker.py**-filen från python-projektet. Om **PYTHON2** -miljövariabeln är inställd på **på**, används python 2,7, annars används python 3,8.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Visual Studio-mallarna ska ha skapat en **ps.cmd**-fil i **. / bin**-mappen. Detta gränssnittsskript anropar PowerShell-omslutningsskripten ovan och tillhandahåller loggning baserat på namnet på den anropade PowerShell-omslutningen. Detta är vad som bör finnas i den, om filen inte skapades. 

```cmd
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Lokal körning
Om du definierar ditt molntjänstprojekt som startprojektet och trycker på F5 körs molntjänsten i den lokala Azure-emulatorn.

Även om PTVS har stöd för att starta i emulatorn så går det inte att felsöka (till exempel brytpunkter).

Om du vill felsöka dina webb- och arbetsroller kan du konfigurera rollprojektet som startprojektet och felsöka det i stället.  Du kan också ange flera startprojekt.  Högerklicka på lösningen och välj **Ange startprojekt**.

![Egenskaper för lösningens startprojekt](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Publicera till Azure
När du vill publicera högerklickar du på molntjänstprojektet i lösningen och väljer **Publicera**.

![Inloggning för publicering i Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Följ guiden. Aktivera Fjärrskrivbord vid behov. Fjärrskrivbord är användbart när du behöver felsöka något.

När du har konfigurerat inställningarna klickar du på **Publicera**.

Förloppet visas i utdatafönstret. Därefter visas fönstret med Microsoft Azure-aktivitetsloggen.

![Fönstret med Microsoft Azure-aktivitetsloggen](./media/cloud-services-python-ptvs/publish-activity-log.png)

Distributionen tar flera minuter. När den är klar körs dina webb- och/eller arbetsroller i Azure!

### <a name="investigate-logs"></a>Undersöka loggar
När den virtuella molntjänstdatorn startar och installerar Python, kan du titta på loggarna för att hitta eventuella felmeddelanden. Dessa loggar finns i mappen **C:\Resources\Directory \\ {Role} \LogFiles** . **PrepPython.err.txt** har minst ett fel från när skriptet försöker identifiera om Python är installerat och **PipInstaller.err.txt** kan klaga över en inaktuell version av pip.

## <a name="next-steps"></a>Nästa steg
Mer detaljerad information om hur du arbetar med webb- och arbetsroller i Python Tools för Visual Studio finns i dokumentationen till PTVS:

* [Molntjänstprojekt][Cloud Service Projects]

Mer information om hur du använder Azure-tjänster från dina webb- och arbetsroller, t.ex. Azure Storage eller Azure Service Bus, finns i följande artiklar:

* [Blob-tjänst][Blob Service]
* [Tabelltjänst][Table Service]
* [Kötjänst][Queue Service]
* [Service Bus köer][Service Bus Queues]
* [Service Bus ämnen][Service Bus Topics]

<!--Link references-->

[Vad är en molntjänst?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: /visualstudio/python/
[Cloud Service Projects]: /visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.8 32-bit]: https://www.python.org/downloads/