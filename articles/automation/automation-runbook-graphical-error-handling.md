---
title: Hantera fel i Azure Automation grafiska runbooks
description: Den här artikeln beskriver hur du implementerar fel hanterings logik i grafiska runbooks.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 24c7aaf08b4d22706bee8f37025b12a656ceaff5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98895908"
---
# <a name="handle-errors-in-graphical-runbooks"></a>Hantera fel i grafiska runbooks

En viktig design princip för din Azure Automation grafiska Runbook är identifieringen av problem som kan uppstå under körningen av runbooken. De här problemen kan omfatta lyckade åtgärder, förväntade feltillstånd och oväntade feltillstånd.

Ofta, om det finns ett icke-avslutande fel som inträffar med en Runbook-aktivitet, hanterar Windows PowerShell aktiviteten genom att bearbeta alla aktiviteter som följer, oavsett fel. Det är troligt att felet genererar ett undantag, men nästa aktivitet kan fortfarande få köras.

Din grafiska Runbook ska innehålla fel hanterings kod för att hantera körnings problem. Om du vill verifiera utdata för en aktivitet eller hantera ett fel kan du använda en PowerShell-kod aktivitet, definiera villkorlig logik för aktivitetens utgående länk eller tillämpa en annan metod.

Grafiska Azure Automation-runbooks har förbättrats och fått en funktion för felhantering. Du kan nu omvandla undantag till icke-avslutande fel och skapa fellänkar mellan aktiviteter. Den förbättrade processen gör det möjligt för din Runbook att fånga fel och hantera realiserade eller oväntade villkor. 

## <a name="powershell-error-types"></a>PowerShell-feltyper

De typer av PowerShell-fel som kan uppstå under körningen av Runbook avslutar fel och icke-avslutande fel.
 
### <a name="terminating-error"></a>Avslutar fel

Ett avslutande fel är ett allvarligt fel under körningen som avbryter ett kommando-eller skript körning helt. Exempel inkluderar obefintliga cmdlets, syntaxfel som förhindrar att en cmdlet körs och andra allvarliga fel.

### <a name="non-terminating-error"></a>Icke-avslutande fel

Ett icke-avslutande fel är ett icke-allvarligt fel som gör att körningen kan fortsätta trots fel tillståndet. Exempel på detta är drift fel, t. ex. att filen inte hittades fel-och behörighets problem.

## <a name="when-to-use-error-handling"></a>När du ska använda felhantering

Använd fel hantering i din Runbook när en kritisk aktivitet genererar ett fel eller ett undantag. Det är viktigt att förhindra nästa aktivitet i runbooken från att bearbeta och hantera felet på lämpligt sätt. Att hantera felet är särskilt viktigt när dina runbooks stöder en affärs-eller tjänst åtgärds process.

## <a name="add-error-links"></a>Lägg till fel länkar

För varje aktivitet som kan producera ett fel kan du lägga till en fellänk som pekar på någon annan aktivitet. Mål aktiviteten kan vara av valfri typ, inklusive kod aktivitet, anrop av en cmdlet, anrop av en annan Runbook och så vidare. Mål aktiviteten kan också ha utgående länkar, antingen vanliga eller fel länkar. Länkarna tillåter att runbooken implementerar komplex fel hanterings logik utan att behöva använda en kod aktivitet.

Den rekommenderade metoden är att skapa en dedikerad fel hanterings-Runbook med vanliga funktioner, men den här metoden är inte obligatorisk. Överväg till exempel en Runbook som försöker starta en virtuell dator och installera ett program på den. Om den virtuella datorn inte startar på rätt sätt:

1. Skickar ett meddelande om det här problemet.
2. Startar en annan Runbook som automatiskt etablerar en ny virtuell dator i stället.

En lösning är att ha en fellänk i Runbook som pekar på en aktivitet som hanterar steg ett. Runbooken kan till exempel ansluta `Write-Warning` cmdleten till en aktivitet för steg två, till exempel cmdleten [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) .

Du kan också generalisera detta beteende för användning i många runbooks genom att placera dessa två aktiviteter i en separat Runbook för fel hantering. Innan den ursprungliga runbooken anropar den här fel hanterings runbooken kan den skapa ett anpassat meddelande från dess data och sedan skicka det som en parameter till den fel hanterings-runbooken.

## <a name="turn-exceptions-into-non-terminating-errors"></a>Omvandla undantag till icke-avslutande fel

Varje aktivitet i din Runbook har en konfigurations inställning som inaktiverar undantag till icke-avslutande fel. Som standard är denna inställning inaktiverad. Vi rekommenderar att du aktiverar den här inställningen för alla aktiviteter där Runbook hanterar fel. Den här inställningen säkerställer att Runbook hanterar både avslutande och icke-avslutande fel i aktiviteten som icke-avslutande fel, med hjälp av en fel länk.  

När du har aktiverat konfigurations inställningen måste din Runbook skapa en aktivitet som hanterar felet. Om aktiviteten genererar ett fel följs länkarna för utgående fel. De vanliga länkarna följs inte, även om aktiviteten även genererar vanliga utdata.<br><br> ![Fellänksexempel för Automation Runbook](media/automation-runbook-graphical-error-handling/error-link-example.png)

I följande exempel hämtar en Runbook en variabel som innehåller dator namnet på en virtuell dator. Den försöker sedan starta den virtuella datorn med nästa aktivitet.<br><br> ![Exempel på fel hantering av Automation-Runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable`Aktiviteten och cmdleten [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) har kon figurer ATS för att konvertera undantag till fel. Om det uppstår problem med att hämta variabeln eller att starta den virtuella datorn genererar koden fel.<br><br> ![Fel hanterings aktivitets inställningar för Automation Runbook ](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png) .

Fel länkar flödar från dessa aktiviteter till en enskild `error management` kod aktivitet. Den här aktiviteten har kon figurer ATS med ett enkelt PowerShell-uttryck som använder `throw` nyckelordet för att stoppa bearbetningen, tillsammans med `$Error.Exception.Message` för att hämta det meddelande som beskriver det aktuella undantaget.<br><br> ![Fel hanterings kod exempel för Automation Runbook](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>Nästa steg

* Information om hur du löser grafiska Runbook-fel finns i [Felsöka Runbook-problem](troubleshoot/runbooks.md).
