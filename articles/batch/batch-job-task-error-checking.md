---
title: Söka efter jobb- och uppgiftsfel
description: Lär dig mer om fel att söka efter och fel sökning av jobb och uppgifter.
author: mscurrell
ms.topic: how-to
ms.date: 11/23/2020
ms.author: markscu
ms.openlocfilehash: d8cf3b5e28d4455e00e0bdcbae2063771d3e8acd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95736807"
---
# <a name="job-and-task-error-checking"></a>Fel kontroll av jobb och aktivitet

Det finns olika fel som kan uppstå när du lägger till jobb och uppgifter. Det är enkelt att identifiera felen för dessa åtgärder eftersom eventuella problem returneras direkt av API, CLI eller gränssnitt. Det finns dock också problem som kan uppstå senare, när jobb och aktiviteter schemaläggs och körs.

Den här artikeln beskriver de fel som kan uppstå efter att jobb och uppgifter har skickats och hur du kan söka efter och hantera dem.

## <a name="jobs"></a>Jobb

Ett jobb är en gruppering av en eller flera aktiviteter, med de uppgifter som faktiskt anger vilka kommando rader som ska köras.

När du lägger till ett jobb kan du ange följande parametrar som kan påverka hur jobbet kan gå sönder:

- [Jobb begränsningar](/rest/api/batchservice/job/add#jobconstraints)
  - Du `maxWallClockTime` kan också ange en egenskap för att ange den maximala tid som ett jobb kan vara aktivt eller körs. Om det överskrids avbryts jobbet med `terminateReason` egenskapen som angetts i [executionInfo](/rest/api/batchservice/job/get#cloudjob) för jobbet.
- [Uppgift för jobb förberedelse](/rest/api/batchservice/job/add#jobpreparationtask)
  - Om det här alternativet anges körs en jobb förberedelse aktivitet första gången en aktivitet körs för ett jobb på en nod. Uppgiften att förbereda jobbet kan Miss klar, vilket leder till att aktiviteten inte körs och att jobbet inte slutförs.
- [Jobb publicerings aktivitet](/rest/api/batchservice/job/add#jobreleasetask)
  - En jobb publicerings aktivitet kan bara anges om en jobb förberedelse aktivitet har kon figurer ATS. När ett jobb avslutas körs jobb publicerings aktiviteten på varje pool där en jobb förberedelse aktivitet kördes. Det går inte att utföra en jobb publicerings uppgift, men jobbet kommer fortfarande att flyttas till ett `completed` tillstånd.

### <a name="job-properties"></a>Jobb egenskaper

Följande jobb egenskaper bör kontrol leras för fel:

- '[executionInfo](/rest/api/batchservice/job/get#jobexecutioninformation)':
  - `terminateReason`Egenskapen kan ha värden som anger att, som `maxWallClockTime` anges i jobb begränsningarna, överskreds och att jobbet avbröts. Den kan också anges för att indikera att en uppgift misslyckades om jobb `onTaskFailure` egenskapen har ställts in korrekt.
  - Egenskapen [schedulingError](/rest/api/batchservice/job/get#jobschedulingerror) anges om det har uppstått ett schemaläggnings fel.

### <a name="job-preparation-tasks"></a>Jobb förberedelse aktiviteter

Om en jobb förberedelse uppgift har angetts för ett jobb körs en instans av den aktiviteten första gången en aktivitet för jobbet körs på en nod. Jobb förberedelse aktiviteten som kon figurer ATS för jobbet kan ses som en uppgiftsmall, med att flera uppgifts instanser för jobb förberedelse körs, upp till antalet noder i en pool.

Uppgifts instanserna för jobb förberedelsen bör kontrol leras för att avgöra om det finns fel:

- När en jobb förberedelse åtgärd körs, kommer aktiviteten som utlöste aktiviteten att flyttas till ett [tillstånd](/rest/api/batchservice/task/get#taskstate) av `preparing` . om uppgiften förberedande jobb Miss lyckas återgår den Utlös ande aktiviteten till `active` statusen och körs inte.
- Alla instanser av uppgiften för jobb förberedelse som har körts kan hämtas från jobbet med hjälp av API: et för [förberedelse och publicering av uppgifts status](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) . Som med alla uppgifter finns [körnings information](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) som är tillgänglig med egenskaper som `failureInfo` , `exitCode` och `result` .
- Om jobb förberedelse aktiviteterna Miss lyckas körs inte jobb uppgifter som utlöses, jobbet kommer inte att slutföras och kommer att fastna. Poolen kan komma att användas om det inte finns några andra jobb med aktiviteter som kan schemaläggas.

### <a name="job-release-tasks"></a>Jobb publicerings aktiviteter

Om en jobb publicerings uppgift har angetts för ett jobb, och när ett jobb avslutas, körs en instans av jobb publicerings aktiviteten på varje adresspool där jobb förberedelse aktiviteten kördes. Aktivitets instanserna för jobb lansering bör kontrol leras för att avgöra om det finns fel:

- Alla instanser av jobb publicerings aktiviteten som körs kan hämtas från jobbet med hjälp av [aktivitets status för förberedelse och publicering](/rest/api/batchservice/job/listpreparationandreleasetaskstatus)av API-lista. Som med alla uppgifter finns [körnings information](/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) som är tillgänglig med egenskaper som `failureInfo` , `exitCode` och `result` .
- Om en eller flera jobb lanserings aktiviteter Miss Miss kan jobbet fortfarande avslutas och flyttas till ett `completed` tillstånd.

## <a name="tasks"></a>Uppgifter

Jobb aktiviteter kan inte utföras av flera orsaker:

- Aktivitetens kommando rad Miss lyckas, returnerar en slutkod som inte är noll.
- `resourceFiles`En aktivitet har angetts, men det uppstod ett problem med att en eller flera filer inte laddades ned.
- `outputFiles`En aktivitet har angetts, men det uppstod ett problem med att en eller flera filer inte överfördes.
- Den förflutna tiden för aktiviteten, som anges av `maxWallClockTime` egenskapen i aktivitets [begränsningarna](/rest/api/batchservice/task/add#taskconstraints), överskreds.

I samtliga fall måste följande egenskaper kontrol leras för fel och information om felen:

- Egenskapen tasks [executionInfo](/rest/api/batchservice/task/get#taskexecutioninformation) innehåller flera egenskaper som innehåller information om ett fel. [resultatet](/rest/api/batchservice/task/get#taskexecutionresult) visar om uppgiften misslyckades av någon anledning, med `exitCode` och `failureInfo` ger mer information om fel.
- Aktiviteten kommer alltid att flyttas till `completed` [tillstånd](/rest/api/batchservice/task/get#taskstate), oberoende av om den lyckades eller misslyckades.

Effekten av aktivitets haverier på jobbet och eventuella aktivitets beroenden måste beaktas. Egenskapen [exitConditions](/rest/api/batchservice/task/add#exitconditions) kan anges för en uppgift för att konfigurera en åtgärd för beroenden och för jobbet.

- För-beroenden kontrollerar [DependencyAction](/rest/api/batchservice/task/add#dependencyaction) om aktiviteterna som är beroende av den misslyckade aktiviteten är blockerade eller körs.
- För jobbet styr [JobAction](/rest/api/batchservice/task/add#jobaction) om den misslyckade aktiviteten leder till jobbet som inaktive ras, avslutas eller lämnas oförändrat.

### <a name="task-command-line-failures"></a>Åtgärds kommando rads problem

När aktivitetens kommando rad körs skrivs utdata till `stderr.txt` och `stdout.txt` . Dessutom kan programmet skriva till programspecifika loggfiler.

Om noden pool där en aktivitet har körts fortfarande finns, kan loggfilerna hämtas och visas. Till exempel visar Azure Portal listor och kan visa loggfiler för en aktivitet eller en pool-nod. Flera API: er tillåter också att aktivitetsvyer visas och hämtas, till exempel [Hämta från uppgift](/rest/api/batchservice/file/getfromtask).

Eftersom pooler och pooler ofta är tillfälliga, och noderna läggs till och tas bort kontinuerligt, rekommenderar vi att du sparar loggfiler. Med [Uppgiftsutdata](./batch-task-output-files.md) kan du enkelt spara loggfiler till Azure Storage.

Kommando raderna som körs av aktiviteter på datornoderna körs inte under ett gränssnitt, så de kan inte internt dra nytta av Shell-funktioner som t. ex. miljö variabel expansion. Om du vill dra nytta av dessa funktioner måste du [anropa gränssnittet på kommando raden](batch-compute-node-environment-variables.md#command-line-expansion-of-environment-variables).

### <a name="output-file-failures"></a>Utdatafel

Vid varje fil uppladdning skriver batch två loggfiler till Compute-noden `fileuploadout.txt` och `fileuploaderr.txt` . Du kan kontrol lera loggfilerna för att lära dig mer om ett speciellt haveri. I de fall där fil överföringen aldrig har gjorts, till exempel eftersom själva uppgiften inte kunde köras, finns inte dessa loggfiler.  

## <a name="next-steps"></a>Nästa steg

- Kontrol lera att ditt program implementerar omfattande fel kontroll. Det kan vara viktigt att snabbt identifiera och diagnostisera problem.
- Läs mer om [jobb och uppgifter](jobs-and-tasks.md).