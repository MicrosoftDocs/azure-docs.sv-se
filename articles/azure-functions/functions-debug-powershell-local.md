---
title: Felsöka PowerShell Azure Functions lokalt
description: Lär dig att felsöka PowerShell-funktioner när du kör lokalt.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422968"
---
# <a name="debug-powershell-azure-functions-locally"></a>Felsöka PowerShell Azure Functions lokalt

Med Azure Functions kan du utveckla dina funktioner som PowerShell-skript.

Du kan felsöka PowerShell-funktioner lokalt på samma sätt som du gör med PowerShell-skript med hjälp av följande standard utvecklings verktyg:

* [Visual Studio Code](https://code.visualstudio.com/): Microsofts kostnads fria och lätta text redigerare med öppen källkod med PowerShell-tillägget som ger en fullständig PowerShell-utvecklings upplevelse.
* En PowerShell-konsol: Felsök med samma kommandon som du använder för att felsöka andra PowerShell-processer.

[Azure Functions Core tools](functions-run-local.md) stöder lokal fel sökning av Azure Functions, inklusive PowerShell-funktioner.

## <a name="example-function-app"></a>Exempel på Function-app

Function-appen som används i den här artikeln har en enda HTTP-utlöst funktion och har följande filer:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Den här funktions appen liknar den som du får när du slutför snabb starten av [PowerShell](./create-first-function-vs-code-powershell.md).

Funktions koden i `run.ps1` ser ut som följande skript:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Ange kopplings punkt

Om du vill felsöka en PowerShell-funktion måste funktionen stoppa för att fel söknings programmet ska kopplas. `Wait-Debugger`Cmdleten slutar att köras och väntar på fel sökning.

>[!NOTE]
>När du använder PowerShell 7 behöver du inte lägga till `Wait-Debugger` anropet i din kod.

Allt du behöver göra är att lägga till ett anrop till `Wait-Debugger` cmdleten strax ovanför `if` instruktionen, enligt följande:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Fel sökning börjar vid `if` instruktionen. 

Med `Wait-Debugger` på plats kan du nu felsöka funktionerna med hjälp av antingen Visual Studio Code eller en PowerShell-konsol.

## <a name="debug-in-visual-studio-code"></a>Fel sökning i Visual Studio Code

Om du vill felsöka PowerShell-funktionerna i Visual Studio Code måste du ha följande installerat:

* [PowerShell-tillägg för Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions-tillägg för Visual Studio Code](./create-first-function-cli-powershell.md)
* [PowerShell Core 6,2 eller högre](/powershell/scripting/install/installing-powershell-core-on-windows)

När du har installerat dessa beroenden läser du in ett befintligt PowerShell Functions-projekt eller [skapar ditt första PowerShell Functions-projekt](./create-first-function-vs-code-powershell.md).

>[!NOTE]
> Om projektet inte har de konfigurationsfiler som behövs uppmanas du att lägga till dem.

### <a name="set-the-powershell-version"></a>Ange PowerShell-version

PowerShell-kärnan installeras sida vid sida med Windows PowerShell. Ange PowerShell Core som PowerShell-version som ska användas med PowerShell-tillägget för Visual Studio Code.

1. Tryck på F1 för att Visa kommandots lastpall och Sök sedan efter `Session` .

1. Välj **PowerShell: Visa session-menyn**.

1. Om den **aktuella sessionen** inte är **PowerShell Core 6** väljer **du växla till: PowerShell Core 6**.

När du har en PowerShell-fil öppen visas den version som visas i grönt längst ned till höger i fönstret. Om du väljer den här texten visas även session-menyn. Mer information finns i [välja en version av PowerShell som ska användas med tillägget](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Starta Function-appen

Kontrol lera att `Wait-Debugger` har angetts i den funktion där du vill koppla fel sökaren.  När `Wait-Debugger` du har lagt till kan du felsöka din Function-app med Visual Studio Code.

Välj **fel söknings** fönstret och sedan **koppla till PowerShell-funktionen**.

![fel sökare](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Du kan också trycka på F5 för att starta fel sökningen.

Åtgärden starta fel sökning utför följande aktiviteter:

* Körs `func extensions install` i terminalen för att installera Azure Functions tillägg som krävs av din Function-app.
* Körs `func host start` i terminalen för att starta Function-appen i functions-värden.
* Koppla PowerShell-felsökaren till PowerShell-körnings utrymme i functions-körningen.

>[!NOTE]
> Du måste se till att PSWorkerInProcConcurrencyUpperBound är inställt på 1 för att säkerställa att fel söknings upplevelsen i Visual Studio Code fungerar korrekt. Det här är standardinställningen.

När din Function-App körs behöver du en separat PowerShell-konsol för att anropa funktionen HTTP-utlöst.

I det här fallet är PowerShell-konsolen klienten. `Invoke-RestMethod`Används för att utlösa funktionen.

Kör följande kommando i en PowerShell-konsol:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Du märker att ett svar inte direkt returneras. Det beror `Wait-Debugger` på att du har bifogat fel söknings programmet och PowerShell-körningen gick in i avbrotts läge så snart det kunde. Detta beror på BreakAll- [konceptet](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), som beskrivs senare. När du har tryckt på `continue` knappen bryts fel sökaren nu på linjen direkt efter `Wait-Debugger` .

I det här läget är fel söknings programmet ansluten och du kan utföra alla vanliga fel söknings åtgärder. Mer information om hur du använder fel sökning i Visual Studio Code finns i [den officiella dokumentationen](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

När du har gått vidare och fullständigt anropar skriptet ser du att:

* PowerShell-konsolen som gjorde `Invoke-RestMethod` returnerade ett resultat
* Den integrerade PowerShell-konsolen i Visual Studio Code väntar på att ett skript ska köras

Senare när du anropar samma funktion bryts fel söknings verktyget i PowerShell-tillägget direkt efter `Wait-Debugger` .

## <a name="debugging-in-a-powershell-console"></a>Fel sökning i en PowerShell-konsol

>[!NOTE]
> Det här avsnittet förutsätter att du har läst [Azure Functions Core tools-dokument](functions-run-local.md) och vet hur du använder `func host start` kommandot för att starta din Function-app.

Öppna en konsol, `cd` i katalogen för din Function-app, och kör följande kommando:

```sh
func host start
```

Med Function-appen igång och på `Wait-Debugger` plats kan du ansluta till processen. Du behöver två fler PowerShell-konsoler.

En av konsolerna fungerar som-klienten. Härifrån kan du anropa `Invoke-RestMethod` funktionen. Du kan till exempel köra följande kommando:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Du ser att det inte returnerar något svar, vilket är ett resultat av `Wait-Debugger` . PowerShell-körnings utrymme väntar nu på att ett fel söknings program ska bifogas. Nu är det dags att bifoga.

Kör följande kommando i den andra PowerShell-konsolen:

```powershell
Get-PSHostProcessInfo
```

Den här cmdleten returnerar en tabell som ser ut som följande utdata:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Anteckna `ProcessId` för objektet i tabellen med `ProcessName` som `dotnet` . Den här processen är din Function-app.

Kör sedan följande kodfragment:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

När den har startats bryts fel sökaren och ser ut ungefär så här:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

Nu är du stoppad vid en Bryt punkt i [PowerShell-felsökaren](/powershell/module/microsoft.powershell.core/about/about_debuggers). Härifrån kan du utföra alla vanliga fel söknings åtgärder, stega, stega i, fortsätta, avsluta och andra. Om du vill se en fullständig uppsättning fel söknings kommandon som är tillgängliga i-konsolen kan du köra `h` `?` kommandona eller.

Du kan också ange Bryt punkter på den här nivån med `Set-PSBreakpoint` cmdleten.

När du fortsätter och har anropat skriptet fullständigt ser du att:

* PowerShell-konsolen där du körde `Invoke-RestMethod` har nu returnerat ett resultat.
* PowerShell-konsolen där du körde `Debug-Runspace` väntar på att ett skript ska köras.

Du kan anropa samma funktion igen (med `Invoke-RestMethod` t. ex.) och fel söknings verktyget bryts direkt efter `Wait-Debugger` kommandot.

## <a name="considerations-for-debugging"></a>Att tänka på vid fel sökning

Tänk på följande när du felsöker din funktions kod.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` kan orsaka att fel söknings programmet avbryts på oväntad plats

PowerShell-tillägget använder `Debug-Runspace` , vilket i sin tur använder PowerShell- `BreakAll` funktionen. Den här funktionen instruerar PowerShell att stoppa vid det första kommandot som körs. Med det här beteendet får du möjlighet att ställa in Bryt punkter i fel söknings körnings utrymme.

Azure Functions runtime kör några kommandon innan skriptet anropas `run.ps1` , så det är möjligt att fel söknings programmet slutar att fungera i `Microsoft.Azure.Functions.PowerShellWorker.psm1` eller `Microsoft.Azure.Functions.PowerShellWorker.psd1` .

Om den här rasten inträffar kör du `continue` eller- `c` kommandot för att hoppa över den här Bryt punkten. Sedan stoppar du den förväntade Bryt punkten.

## <a name="troubleshooting"></a>Felsökning

Om du har problem under fel sökningen bör du kontrol lera följande:

| Markera | Action |
|------|------|
| Kör `func --version` från terminalen. Om du får ett fel som `func` inte går att hitta, kan det saknas kärn verktyg (func.exe) från den lokala `path` variabeln.| [Installera om kärn verktyg](functions-run-local.md#v2).|  
| I Visual Studio Code måste standard terminalen ha åtkomst till func.exe. Kontrol lera att du inte använder en standard-Terminal som inte har några installerade verktyg, till exempel Windows-undersystemet för Linux (WSL).  | Ange standard gränssnittet i Visual Studio Code till antingen PowerShell 7 (rekommenderas) eller Windows PowerShell 5,1.|
  

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utvecklar funktioner med hjälp av PowerShell finns i [Azure Functions PowerShell Developer Guide](functions-reference-powershell.md).
