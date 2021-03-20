---
title: Använd Azure Automation grafisk Runbook SDK (för hands version)
description: Den här artikeln beskriver hur du använder Azure Automation grafisk Runbook SDK (för hands version).
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83835044"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>Använd Azure Automation grafisk Runbook SDK (för hands version)

[Grafiska runbooks](automation-graphical-authoring-intro.md) hjälper till att hantera komplexiteten i den underliggande Windows PowerShell-eller PowerShell-arbetsflödes koden. Microsoft Azure Automation graphicing graphicing SDK gör det möjligt för utvecklare att skapa och redigera grafiska runbooks för användning med Azure Automation. I den här artikeln beskrivs de grundläggande stegen för att skapa en grafisk Runbook från din kod.

## <a name="prerequisites"></a>Förutsättningar

Importera `Orchestrator.GraphRunbook.Model.dll` paketet genom att hämta [SDK: n](https://www.microsoft.com/download/details.aspx?id=50734).

## <a name="create-a-runbook-object-instance"></a>Skapa en instans av en Runbook-objekt

Referera till `Orchestrator.GraphRunbook.Model` sammansättningen och skapa en instans av `Orchestrator.GraphRunbook.Model.GraphRunbook` klassen:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Lägg till Runbook-parametrar

Instansiera `Orchestrator.GraphRunbook.Model.Parameter` objekt och Lägg till dem i runbooken:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Lägg till aktiviteter och länkar

Instansiera aktiviteter av lämpliga typer och Lägg till dem i runbooken:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Aktiviteter implementeras av följande klasser i `Orchestrator.GraphRunbook.Model` namn området.

|Klass  |Aktivitet  |
|---------|---------|
|CommandActivity     | Anropar ett PowerShell-kommando (cmdlet, Function osv.).        |
|InvokeRunbookActivity     | Anropar en annan Runbook infogad.        |
|JunctionActivity     | Väntar på att alla inkommande grenar ska slutföras.        |
|WorkflowScriptActivity     | Kör ett block med PowerShell-eller PowerShell-arbetsflöde (beroende på typ av Runbook) i kontexten för runbooken. Detta är ett kraftfullt verktyg, men du kan inte överanvända det: gränssnittet visar det här skript blocket som text. körnings motorn hanterar det angivna blocket som en svart ruta och gör inga försök att analysera innehållet, förutom en grundläggande syntaxkontroll. Om du bara behöver anropa ett enda PowerShell-kommando föredrar du CommandActivity.        |

> [!NOTE]
> Härled inte dina egna aktiviteter från de angivna klasserna. Azure Automation kan inte använda Runbooks med anpassade aktivitets typer.

Du måste ange `CommandActivity` och `InvokeRunbookActivity` parametrar som värde beskrivningar, inte direkta värden. Värde beskrivningar anger hur faktiska parameter värden ska skapas. Följande värde beskrivningar finns för närvarande:


|Script  |Definition  |
|---------|---------|
|ConstantValueDescriptor     | Refererar till ett hårdkodat konstant värde.        |
|RunbookParameterValueDescriptor     | Refererar till en Runbook-parameter efter namn.        |
|ActivityOutputValueDescriptor     | Refererar till en överordnad aktivitets utmatning, så att en aktivitet kan "Prenumerera" på data som skapats av en annan aktivitet.        |
|AutomationVariableValueDescriptor     | Refererar till en Automation-variabel till gång efter namn.         |
|AutomationCredentialValueDescriptor     | Refererar till till gång till ett Automation-certifikat efter namn.        |
|AutomationConnectionValueDescriptor     | Refererar till en Automation-anslutningssträng efter namn.        |
|PowerShellExpressionValueDescriptor     | Anger ett PowerShell-uttryck (fritt format) som ska utvärderas precis innan aktiviteten anropas.  <br/>Detta är ett kraftfullt verktyg, men du kan inte överanvända det: det här uttrycket visas som text i användar gränssnittet. körnings motorn hanterar det angivna blocket som en svart ruta och gör inga försök att analysera innehållet, förutom en grundläggande syntaxkontroll. Om möjligt bör du föredra mer exakta värde beskrivningar.      |

> [!NOTE]
> Härled inte dina egna värde beskrivningar från de angivna klasserna. Azure Automation kan inte använda Runbooks med anpassade värde beskrivnings typer.

Instansiera länkar som ansluter aktiviteter och Lägg till dem i runbooken:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Spara runbooken till en fil

Använd `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` för att serialisera en Runbook till en sträng:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Du kan spara den här strängen i en fil med fil namns tillägget **. graphrunbook** . Motsvarande Runbook kan importeras till Azure Automation.
Det serialiserade formatet kan ändras i framtida versioner av `Orchestrator.GraphRunbook.Model.dll` . Vi lovar bakåtkompatibilitet: alla Runbook-versioner som serialiseras med en äldre version av `Orchestrator.GraphRunbook.Model.dll` kan avserialiseras med en senare version. Forward-kompatibilitet är inte garanterad: en Runbook som serialiseras med en nyare version kan inte deserialiseras av äldre versioner.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Redigera grafiska runbooks i Azure Automation](automation-graphical-authoring-intro.md).