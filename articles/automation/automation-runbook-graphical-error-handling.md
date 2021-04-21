---
title: Hantera fel i Azure Automation grafiska runbooks
description: Den här artikeln beskriver hur du implementerar felhanteringslogik i grafiska runbooks.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 00ed73a1b95558f256caa01ad8eafbefe11b0f32
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830492"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Hantera fel i grafiska runbooks

En viktig designprincip att tänka på för din Azure Automation grafiska runbook är identifiering av problem som runbooken kan uppleva under körning. De här problemen kan omfatta lyckade åtgärder, förväntade feltillstånd och oväntade feltillstånd.

Ofta, om det finns ett icke-avslutande fel som inträffar med en Runbook-aktivitet, hanterar Windows PowerShell aktiviteten genom att bearbeta någon aktivitet som följer, oavsett felet. Det är troligt att felet genererar ett undantag, men nästa aktivitet kan fortfarande få köras.

Din grafiska runbook bör innehålla felhanteringskod för att hantera körningsproblem. Om du vill verifiera utdata från en aktivitet eller hantera ett fel kan du använda en PowerShell-kodaktivitet, definiera villkorslogik för utdatalänken för aktiviteten eller använda en annan metod.

Grafiska Azure Automation-runbooks har förbättrats och fått en funktion för felhantering. Du kan nu omvandla undantag till icke-avslutande fel och skapa fellänkar mellan aktiviteter. Den förbättrade processen gör att din runbook kan fånga upp fel och hantera realiserade eller oväntade förhållanden. 

## <a name="powershell-error-types"></a>PowerShell-feltyper

De typer av PowerShell-fel som kan uppstå under runbook-körningen är avslutande fel och icke-avslutande fel.
 
### <a name="terminating-error"></a>Avslutande fel

Ett avslutande fel är ett allvarligt fel under körning som stoppar en kommando- eller skriptkörning helt. Exempel är icke-existerande cmdlets, syntaxfel som förhindrar att en cmdlet körs och andra allvarliga fel.

### <a name="non-terminating-error"></a>Icke-avslutande fel

Ett icke-avslutande fel är ett icke-allvarligt fel som gör att körningen kan fortsätta trots feltillståndet. Exempel på detta är driftfel, till exempel problem med att filen inte hittades och behörighetsproblem.

## <a name="when-to-use-error-handling"></a>När du ska använda felhantering

Använd felhantering i din runbook när en kritisk aktivitet kastar ett fel eller undantag. Det är viktigt att förhindra att nästa aktivitet i runbooken bearbetar och att hantera felet på rätt sätt. Det är särskilt viktigt att hantera felet när dina runbooks stöder en affärs- eller tjänstdriftsprocess.

## <a name="add-error-links"></a>Lägga till fellänkar

För varje aktivitet som kan generera ett fel kan du lägga till en fellänk som pekar på en annan aktivitet. Målaktiviteten kan vara av valfri typ, inklusive kodaktivitet, anrop av en cmdlet, anrop av en annan runbook och så vidare. Målaktiviteten kan också ha utgående länkar, antingen vanliga länkar eller fellänkar. Med länkarna kan runbooken implementera komplex felhanteringslogik utan att behöva använda en kodaktivitet.

Den rekommenderade praxis är att skapa en dedikerad felhanterings-runbook med vanliga funktioner, men den här praxis är inte obligatorisk. Tänk dig till exempel en runbook som försöker starta en virtuell dator och installera ett program på den. Om den virtuella datorn inte startar korrekt gör den följande:

1. Skickar ett meddelande om det här problemet.
2. Startar en annan runbook som automatiskt skapar en ny virtuell dator i stället.

En lösning är att ha en fellänk i runbooken som pekar på en aktivitet som hanterar steg ett. Runbooken kan till exempel ansluta cmdleten till en aktivitet för steg två, till exempel `Write-Warning` cmdleten [Start-AzAutomationRunbook.](/powershell/module/az.automation/start-azautomationrunbook)

Du kan också generalisera det här beteendet för användning i många runbooks genom att placera dessa två aktiviteter i en separat felhanterings-runbook. Innan den ursprungliga runbooken anropar den här felhanterings-runbooken kan den skapa ett anpassat meddelande från sina data och sedan skicka det som en parameter till felhanterings-runbooken.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Omvandla undantag till icke-avslutande fel

Varje aktivitet i din Runbook har en konfigurationsinställning som omvandlar undantag till icke-avslutande fel. Som standard är denna inställning inaktiverad. Vi rekommenderar att du aktiverar den här inställningen för alla aktiviteter där din runbook hanterar fel. Den här inställningen säkerställer att runbooken hanterar både avslutande och icke-avslutande fel i aktiviteten som icke-avslutande fel med hjälp av en fellänk.  

När du har aktivera konfigurationsinställningen kan runbooken skapa en aktivitet som hanterar felet. Om aktiviteten genererar ett fel följs de utgående fellänkarna. De vanliga länkarna följs inte, även om aktiviteten även genererar regelbundna utdata.<br><br> ![Fellänksexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

I följande exempel hämtar en runbook en variabel som innehåller datornamnet för en virtuell dator. Den försöker sedan starta den virtuella datorn med nästa aktivitet.<br><br> ![Felhanteringsexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

Aktiviteten `Get-AutomationVariable` och cmdleten [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) är konfigurerade för att konvertera undantag till fel. Om det uppstår problem med att hämta variabeln eller starta den virtuella datorn genererar koden fel.<br><br> ![Automation Runbook-felhanteringsaktivitetsinställningar ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) .

Fellänkar flödar från dessa aktiviteter till en enda `error management` kodaktivitet. Den här aktiviteten konfigureras med ett enkelt PowerShell-uttryck som använder nyckelordet för att stoppa bearbetningen, tillsammans med för att hämta meddelandet som `throw` `$Error.Exception.Message` beskriver det aktuella undantaget.<br><br> ![Exempel på felhanteringskod för Automation Runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du löser grafiska runbook-fel finns i [Felsöka runbook-problem.](troubleshoot/runbooks.md)
