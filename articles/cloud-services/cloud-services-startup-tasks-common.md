---
title: Vanliga start uppgifter för Cloud Services (klassisk) | Microsoft Docs
description: Innehåller några exempel på vanliga start åtgärder som du kanske vill utföra i din webb roll eller arbets roll för Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f55b225e615a3e7a5fbcf56b405054883d3b5413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741204"
---
# <a name="common-cloud-service-classic-startup-tasks"></a>Vanliga start uppgifter för moln tjänst (klassisk)

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln innehåller några exempel på vanliga start uppgifter som du kanske vill utföra i din moln tjänst. Du kan använda Start åtgärder för att utföra åtgärder innan en roll startar. Åtgärder som du kanske vill utföra är att installera en komponent, registrera COM-komponenter, ange register nycklar eller starta en tids krävande process. 

Se [den här artikeln](cloud-services-startup-tasks.md) för att förstå hur start aktiviteter fungerar och hur du skapar de poster som definierar en start uppgift.

> [!NOTE]
> Start aktiviteter kan inte tillämpas på Virtual Machines, endast för webb-och arbets roller i moln tjänster.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definiera miljövariabler innan en roll startas
Om du behöver miljövariabler som definierats för en viss aktivitet, använder du [miljö] elementet i [aktivitets] elementet.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Variabler kan också använda ett [giltigt Azure XPath-värde](cloud-services-role-config-xpath.md) för att referera till något om distributionen. I stället för att använda `value` attributet definierar du ett underordnat [RoleInstanceValue] -element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurera IIS-start med AppCmd.exe
Kommando rads verktyget [AppCmd.exe](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj635852(v=ws.11)) kan användas för att hantera IIS-inställningar vid start på Azure. *AppCmd.exe* ger bekväm kommando rads åtkomst till konfigurations inställningar för användning i Start åtgärder på Azure. Med hjälp av *AppCmd.exe* kan webbplats inställningar läggas till, ändras eller tas bort för program och platser.

Det finns dock några saker att se för när du använder *AppCmd.exe* som en start uppgift:

* Start aktiviteter kan köras mer än en gång mellan omstarter. Till exempel när en roll återanvänds.
* Om en *AppCmd.exe* åtgärd utförs mer än en gång kan det generera ett fel. Försök till exempel att lägga till ett avsnitt till *Web.config* två gånger kan generera ett fel.
* Start aktiviteter fungerar inte om de returnerar en slutkod som inte är noll eller **errorlevel**. Till exempel när *AppCmd.exe* genererar ett fel.

Det är en bra idé att kontrol lera **errorlevel** när du har anropat *AppCmd.exe*, vilket är enkelt att göra om du omsluter anropet till *AppCmd.exe* med en *. cmd* -fil. Om du identifierar ett känt **errorlevel** -svar kan du ignorera det eller skicka tillbaka det.

ERRORLEVEL som returneras av *AppCmd.exe* visas i filen WinError. h och kan också visas på [MSDN](/windows/desktop/Debug/system-error-codes--0-499-).

### <a name="example-of-managing-the-error-level"></a>Exempel på hur du hanterar fel nivån
Det här exemplet lägger till ett komprimerings avsnitt och en komprimerings post för JSON till *Web.config* -filen, med fel hantering och loggning.

De relevanta avsnitten i filen [service definition. csdef] visas här, som inkluderar inställning av [ExecutionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task) -attributet för `elevated` att ge *AppCmd.exe* tillräckliga behörigheter för att ändra inställningarna i *Web.config* -filen:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Kommando filen *startup. cmd* använder *AppCmd.exe* för att lägga till ett komprimerings avsnitt och en komprimerings post för JSON till *Web.config* -filen. Den förväntade **errorlevel** på 183 är inställd på noll med VERIFY.EXE kommando rads program. Oväntade errorlevels loggas till StartupErrorLog.txt.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Lägg till brand Väggs regler
I Azure finns det effektiva två brand väggar. Den första brand väggen kontrollerar anslutningar mellan den virtuella datorn och den utanför världen. Den här brand väggen styrs av [slut punkts] elementet i filen [service definition. csdef] .

Den andra brand väggen kontrollerar anslutningar mellan den virtuella datorn och processerna i den virtuella datorn. Den här brand väggen kan styras av `netsh advfirewall firewall` kommando rads verktyget.

Azure skapar brand Väggs regler för de processer som startas i dina roller. När du exempelvis startar en tjänst eller ett program skapar Azure automatiskt de nödvändiga brand Väggs reglerna för att tillåta tjänsten att kommunicera med Internet. Men om du skapar en tjänst som startas av en process utanför din roll (som en COM+-tjänst eller en schemalagd schemalagd aktivitet) måste du skapa en brand Väggs regel manuellt för att tillåta åtkomst till tjänsten. Dessa brand Väggs regler kan skapas med hjälp av en start åtgärd.

En [Start åtgärd som] skapar en brand Väggs regel måste ha en [ExecutionContext]med **utökade privilegier**. Lägg till följande start åtgärd i filen [service definition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Om du vill lägga till brand Väggs regeln måste du använda lämpliga `netsh advfirewall firewall` kommandon i Start kommando filen. I det här exemplet kräver start åtgärden säkerhet och kryptering för TCP-port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blockera en speciell IP-adress
Du kan begränsa en Azure-webbrolls åtkomst till en uppsättning angivna IP-adresser genom att ändra din IIS- **web.config** fil. Du måste också använda en kommando fil som låser upp avsnittet **ipSecurity** i **ApplicationHost.config** -filen.

Om du vill låsa upp avsnittet **ipSecurity** i **ApplicationHost.config** -filen skapar du en kommando fil som körs vid roll start. Skapa en mapp på rot nivån för din webbroll som heter **Start** och skapa sedan en kommando fil som heter **startup. cmd** i den här mappen. Lägg till den här filen i Visual Studio-projektet och ange att egenskaperna ska **kopieras alltid** för att säkerställa att de ingår i paketet.

Lägg till följande start åtgärd i filen [service definition. csdef] .

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Lägg till det här kommandot i filen **startup. cmd** :

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Den här uppgiften gör att **Start-. cmd** -konfigurationsfilen körs varje gång webb rollen initieras, vilket säkerställer att det nödvändiga **ipSecurity** -avsnittet är upplåst.

Slutligen ändrar du [avsnittet system. webserver](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) till din webbrolls **web.config** -fil för att lägga till en lista över IP-adresser som beviljas åtkomst, som du ser i följande exempel:

Det här exempel på konfigurationen **tillåter** att alla IP-adresser får åtkomst till servern, förutom de två definierade

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Den här exempel konfigurationen **nekar** alla IP-adresser från att komma åt servern, förutom de två definierade.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Skapa en start åtgärd för PowerShell
Windows PowerShell-skript kan inte anropas direkt från filen [service definition. csdef] , men de kan anropas i en start kommando fil.

PowerShell (som standard) kör inte osignerade skript. Om du inte signerar skriptet måste du konfigurera PowerShell att köra osignerade skript. Om du vill köra osignerade skript måste **ExecutionPolicy** vara inställt på **obegränsad**. Den **ExecutionPolicy** -inställning som du använder baseras på Windows PowerShell-versionen.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Om du använder ett gäst operativ system som kör PowerShell 2,0 eller 1,0 kan du tvinga version 2 att köras, och om den inte är tillgänglig, använder du version 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Skapa filer i lokal lagring från en start aktivitet
Du kan använda en lokal lagrings resurs för att lagra filer som skapats av en start åtgärd som du kan komma åt senare av ditt program.

Om du vill skapa den lokala lagrings resursen lägger du till en [LocalResources] -sektion i filen [service definition. csdef] och lägger sedan till [localStorage] -underordnade elementet. Ge den lokala lagrings resursen ett unikt namn och lämplig storlek för start aktiviteten.

Om du vill använda en lokal lagrings resurs i Start aktiviteten måste du skapa en miljö variabel för att referera till den lokala lagrings resursens plats. Sedan kan start aktiviteten och programmet läsa och skriva filer till den lokala lagrings resursen.

De relevanta avsnitten i filen **service definition. csdef** visas här:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Som exempel använder den här **Start. cmd** -konfigurationsfilen **PathToStartupStorage** -miljövariabeln för att skapa filen **MyTest.txt** på den lokala lagrings platsen.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Du kan komma åt den lokala lagringsmappen från Azure SDK med hjälp av metoden [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) .

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Kör i emulatorn eller molnet
Du kan låta din start åtgärd utföra olika steg när den körs i molnet jämfört med när den finns i beräknings emulatorn. Till exempel kanske du vill använda en ny kopia av dina SQL-data endast när du kör i emulatorn. Eller så kanske du vill göra vissa prestanda optimeringar för molnet som du inte behöver göra när du kör i emulatorn.

Den här möjligheten att utföra olika åtgärder i beräknings emulatorn och molnet kan åstadkommas genom att skapa en miljö variabel i filen [service definition. csdef] . Sedan testar du miljövariabeln för ett värde i Start aktiviteten.

Lägg till [variabeln] / [RoleInstanceValue] -elementet och skapa ett XPath-värde för att skapa miljövariabeln `/RoleEnvironment/Deployment/@emulated` . Värdet för miljövariabeln **% ComputeEmulatorRunning%** är `true` när det körs på Compute-emulatorn och `false` när det körs i molnet.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Aktiviteten kan nu kontrol lera miljövariabeln **% ComputeEmulatorRunning%** för att utföra olika åtgärder baserat på om rollen körs i molnet eller emulatorn. Här är ett cmd-Shell-skript som söker efter miljövariabeln.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Identifiera att aktiviteten redan har körts
Rollen kan återanvändas utan en omstart som gör att dina start aktiviteter körs igen. Det finns ingen flagga som indikerar att en aktivitet redan har körts på den virtuella värddatorn. Det kan finnas vissa uppgifter där det inte spelar någon roll att de körs flera gånger. Du kan dock stöta på en situation där du behöver förhindra att en aktivitet körs mer än en gång.

Det enklaste sättet att identifiera att en aktivitet redan har körts är att skapa en fil i mappen **% Temp%** när uppgiften lyckas och leta efter den i början av uppgiften. Här är ett exempel på ett cmd Shell-skript som gör det åt dig.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Metod tips för aktiviteter
Här följer några metod tips som du bör följa när du konfigurerar aktiviteter för din webb-eller arbets roll.

### <a name="always-log-startup-activities"></a>Logga alltid start aktiviteter
Visual Studio tillhandahåller inte en fel sökare för att stega igenom kommandofiler, så det är bra att hämta så mycket information som möjligt för batch-filernas funktion. Loggning av utdata från kommandofiler, både **STDOUT** och **stderr**, kan ge dig viktig information när du försöker felsöka och åtgärda kommandofiler. Om du vill logga både **STDOUT** och **stderr** till StartupLog.txt-filen i katalogen som refereras till av miljövariabeln **% Temp%** , lägger du till texten `>>  "%TEMP%\\StartupLog.txt" 2>&1` i slutet av de rader som du vill logga. Om du till exempel vill köra setup.exe i katalogen **% PathToApp1Install%** : `"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1`

För att förenkla XML-koden kan du skapa en *kommando* fil för omslutning som anropar alla dina start uppgifter tillsammans med loggning och ser till att varje underordnad aktivitet delar samma miljövariabler.

Det kan vara irriterande om du använder `>> "%TEMP%\StartupLog.txt" 2>&1` i slutet av varje start åtgärd. Du kan framtvinga loggning av uppgifter genom att skapa en omslutning som hanterar loggningen åt dig. Den här omslutningen anropar den riktiga kommando filen som du vill köra. Utdata från mål kommando filen omdirigeras till *Startuplog.txts* filen.

I följande exempel visas hur du omdirigerar alla utdata från en start kommando fil. I det här exemplet skapar filen ServerDefinition. csdef en start aktivitet som anropar *logwrap. cmd*. *logwrap. cmd* anropar *Startup2. cmd*, omdirigerar alla utdata till **% Temp% \\StartupLog.txt**.

Service definition. cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap. cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2. cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Exempel på utdata i **StartupLog.txt** -filen:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> **StartupLog.txt** -filen finns i mappen *C:\Resources\temp \\ {Role Identifier} \RoleTemp* .
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Ställ in executionContext korrekt för start åtgärder
Ange privilegier för start åtgärden på lämpligt sätt. Ibland måste start aktiviteter köras med utökade privilegier även om rollen körs med normal behörighet.

Attributet [ExecutionContext][] anger behörighets nivån för start aktiviteten. Med `executionContext="limited"` innebär det att start aktiviteten har samma behörighets nivå som rollen. Med `executionContext="elevated"` innebär det att start aktiviteten har administratörs behörighet, vilket gör att start aktiviteten kan utföra administratörs åtgärder utan att ge administratörs behörighet till din roll.

Ett exempel på en start aktivitet som kräver förhöjd behörighet är en start åtgärd som använder **AppCmd.exe** för att konfigurera IIS. **AppCmd.exe** kräver `executionContext="elevated"` .

### <a name="use-the-appropriate-tasktype"></a>Använd lämplig taskType
[TaskType][-attributet bestämmer] hur start aktiviteten körs. Det finns tre värden: **enkel**, **bakgrund** och **förgrund**. Bakgrunden och förgrunds aktiviteterna startas asynkront och sedan körs de enkla uppgifterna synkront en i taget.

Med **enkla** start uppgifter kan du ange i vilken ordning aktiviteterna ska köras i den ordning som uppgifterna visas i listan i filen service definition. csdef. Om en **enkel** aktivitet slutar med en slutkod som inte är noll stoppas start proceduren och rollen startar inte.

Skillnaden mellan start **aktiviteter och start** aktiviteter i **förgrunden** är att **förgrunds** aktiviteterna håller rollen igång tills **förgrunds** aktiviteten slutar. Det innebär också att om **förgrunds** aktiviteten låser sig eller kraschar, kommer rollen inte att återvinna förrän **förgrunds** aktiviteten tvingas stängas. Därför rekommenderas **bakgrunds** aktiviteter för asynkrona start aktiviteter om du inte behöver den funktionen i **förgrunds** aktiviteten.

### <a name="end-batch-files-with-exit-b-0"></a>Avsluta batch-filer med avsluta/B 0
Rollen kommer bara att starta om **errorlevel** från var och en av dina enkla start aktiviteter är noll. Alla program ställer inte in **errorlevel** (slutkod) korrekt, så kommando filen bör avslutas med ett `EXIT /B 0` om allting kördes korrekt.

Ett saknas `EXIT /B 0` i slutet av en start kommando fil är en vanlig orsak till roller som inte startar.

> [!NOTE]
> Jag har lagt märke till att kapslade batch-filer ibland slutar att svara när `/B` parametern används. Du kanske vill se till att det här problemet inte inträffar om en annan kommando fil anropar den aktuella kommando filen, till exempel om du använder [logg omslutningen](#always-log-startup-activities). Du kan utelämna `/B` parametern i det här fallet.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Förväntar dig att start aktiviteter körs mer än en gång
Alla roll återkallning inkluderar inte en omstart, men alla roll återkallning inkluderar körning av alla start aktiviteter. Det innebär att start aktiviteter måste kunna köras flera gånger mellan omstarter utan problem. Detta beskrivs i [föregående avsnitt](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Använd lokal lagring för att lagra filer som måste nås i rollen
Om du vill kopiera eller skapa en fil under start aktiviteten som sedan är tillgänglig för din roll, måste filen placeras i lokal lagring. Se [föregående avsnitt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Nästa steg
Granska moln [tjänst modellen och-paketet](cloud-services-model-and-package.md)

Lär dig mer om hur [aktiviteter](cloud-services-startup-tasks.md) fungerar.

[Skapa och distribuera](cloud-services-how-to-create-deploy-portal.md) ditt moln tjänst paket.

[Service definition. csdef]: cloud-services-model-and-package.md#csdef
[Uppgift]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Runtime]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Miljö]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variabel]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)
[Slutpunkter]: /previous-versions/azure/reference/gg557552(v=azure.100)#Endpoints
[LocalStorage]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalStorage
[LocalResources]: /previous-versions/azure/reference/gg557552(v=azure.100)#LocalResources
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue