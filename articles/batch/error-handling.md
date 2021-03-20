---
title: Fel hantering och identifiering i Azure Batch
description: Lär dig mer om fel hantering i batch service-arbetsflöden från en utvecklings synpunkt.
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85964285"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Fel hantering och identifiering i Azure Batch

Ibland kan det vara nödvändigt att hantera både aktivitets-och program felen i din batch-lösning. Den här artikeln innehåller information om typer av fel och hur du löser dem.

## <a name="error-codes"></a>Felkoder

Allmänna typer av fel är:

- Nätverks problem för begär Anden som aldrig har nått batch eller när batch-svaret inte nådde klienten i tid.
- Interna Server fel (standard status kod för 5xx HTTP-svar).
- Begränsning av fel, till exempel 429 eller 503 status kod HTTP-svar med rubriken retry-efter.
- 4xx-fel som AlreadyExists och InvalidOperation. Det innebär att resursen inte är i rätt tillstånd för tillstånds över gången.

Detaljerad information om särskilda felkoder, inklusive felkoder för REST API, batch-tjänst och jobb aktivitet/schemaläggning finns i [batch-status och felkoder](/rest/api/batchservice/batch-status-and-error-codes).

## <a name="application-failures"></a>Programfel

När ett program körs kan det generera diagnostikutdata som du kan använda för att felsöka problem. Som det beskrivs i [filer och kataloger](files-and-directories.md)skriver batch-tjänsten standardutdata och standard fel utdata till `stdout.txt` och `stderr.txt` filer i aktivitets katalogen på Compute-noden.

Du kan ladda ned dessa filer med hjälp av Azure-portalen eller något av Batch-SDK:erna. Du kan till exempel hämta dessa och andra filer för felsökningsändamål genom att använda [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) och [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) i Batch .NET-biblioteket.

## <a name="task-errors"></a>Aktivitets fel

Aktivitets fel hamnar i flera kategorier.

### <a name="pre-processing-errors"></a>För bearbetnings fel

Om det inte går att starta en aktivitet anges ett förbearbetningsfel för aktiviteten.  

För bearbetnings fel kan inträffa om aktivitetens resursfiler har flyttats, att lagrings kontot inte längre är tillgängligt eller på ett annat problem som gjorde att filerna inte kunde kopieras till noden.

### <a name="file-upload-errors"></a>Fil överförings fel

Om filer som anges för en aktivitet inte kan överföras av någon anledning anges ett fil överförings fel för aktiviteten.

Filöverföringsfel kan inträffa om signaturen för delad åtkomst (SAS) som angetts för att få åtkomst till Azure Storage är felaktig eller om den inte ger skrivbehörighet, om lagringskontot inte längre är tillgängligt eller om det uppstår något annat problem som gör att filerna inte kan kopieras från noden.

### <a name="application-errors"></a>Programfel

Processen som definieras av aktivitetens kommandorad kan också misslyckas. Processen misslyckas om en slutkod som inte är noll returneras av processen som körs av aktiviteten (se *Slutkoder för aktiviteter* i nästa avsnitt).

För program fel kan du konfigurera batch för att automatiskt försöka utföra åtgärden igen till ett visst antal gånger.

### <a name="constraint-errors"></a>Begränsnings fel

Du kan definiera ett villkor som anger den högsta körningstiden för ett jobb eller en aktivitet, *maxWallClockTime*. Detta kan vara användbart för att avsluta aktiviteter som har hängt sig.

När den högsta körningstiden överskrids markeras aktiviteten som *slutförd* men slutkoden är `0xC000013A` och fältet *schedulingError* markeras som `{ category:"ServerError", code="TaskEnded"}`.

## <a name="task-exit-codes"></a>Slutkoder för aktiviteter

Som vi nämnt tidigare markerar Batch-tjänsten processen som misslyckad om processen som körs av aktiviteten returnerar en slutkod som inte är noll. När en aktivitet kör en process lägger Batch till processens returkod i aktivitetens slutkodsegenskap.

Observera att en uppgifts slutkod inte bestäms av Batch-tjänsten. En uppgifts slutkod bestäms av processen i sig eller av det operativsystem där processen körs.

## <a name="task-failures-or-interruptions"></a>Uppgifts haverier eller avbrott

Aktiviteter kan ibland misslyckas eller avbrytas. Själva aktivitets programmet kanske inte kan köras, noden som aktiviteten körs på kanske startas om, eller så kan noden tas bort från poolen under en storleks ändring (om poolens princip för avallokering är inställd på att ta bort noder direkt utan att vänta på att aktiviteter ska slutföras). I samtliga fall kan aktiviteten placeras i kö igen av Batch för körning på en annan nod.

Det är också möjligt att ett tillfälligt problem gör att en aktivitet slutar svara eller tar för lång tid att köra. Du kan ange maximalt körningsintervall för en uppgift. Om det maximala körningsintervallet har överskridits avbryter batchtjänsten uppgiftsprogrammet.

## <a name="connect-to-compute-nodes"></a>Anslut till Compute-noder

Du kan utföra ytterligare felsökning genom att logga in till en beräkningsnod via en fjärranslutning. Du kan använda Azure-portalen för att hämta en RDP-fil (Remote Desktop Protocol) för Windows-noder och hämta SSH-anslutningsinformation (Secure Shell) för Linux-noder. Du kan också göra detta med hjälp av batch-API: er, t. ex. med [batch .net](/dotnet/api/microsoft.azure.batch.computenode) eller [batch python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Om du vill ansluta till en nod via RDP eller SSH måste du först skapa en användare på noden. Du kan göra detta genom att använda Azure Portal, [lägga till ett användarkonto till en nod](/rest/api/batchservice/computenode/adduser) med hjälp av Batch REST-API:et, anropa [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode)-metoden i Batch .NET eller genom att anropa [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)-metoden i Batch Python-modulen.

Om du behöver begränsa eller inaktivera RDP- eller SSH-åtkomst till beräkningsnoder kan du läsa mer i [Konfigurera eller inaktivera fjärråtkomst till beräkningsnoder i en Azure Batch-pool](pool-endpoint-configuration.md).

## <a name="troubleshoot-problem-nodes"></a>Felsöka felnoder

Om vissa av dina aktiviteter misslyckas kan Batch-klientprogrammet eller Batch-tjänsten undersöka de misslyckade aktiviteternas metadata för att identifiera en skadad nod. Varje nod i poolen tilldelas ett unikt ID och den nod som en aktivitet körs på tas med i aktivitetens metadata. När du har identifierat en nod som har problem kan du utföra flera åtgärder:

- **Starta om noden** ([rest](/rest/api/batchservice/computenode/reboot)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.reboot)))

    En omstart av noden kan ibland rensa latenta problem, t.ex. processer som har fastnat eller kraschat. Om din pool använder en start uppgift eller om ditt jobb använder en jobb förberedelse uppgift, körs de när noden startas om.
- **Återställ avbildningen av noden** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    Den här åtgärden installerar om operativsystemet på noden. Precis som vid omstarten av en nod körs startaktiviteter och jobbförberedelseaktiviteter när nodens avbildning har återställts.
- **Ta bort noden från poolen** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    Ibland är det nödvändigt att helt ta bort noden från poolen.
- **Inaktivera schemaläggninga av aktiviteter på noden** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    Den här åtgärden kopplar effektivt bort noden så att den inte tilldelas några ytterligare aktiviteter, samtidigt som den kan fortsätta köras och finnas kvar i poolen. På så sätt kan du fortsätta att undersöka orsaken till felen utan att den misslyckade aktivitetens data går förlorade, och utan att noden orsakar ytterligare aktivitetsfel. Du kan till exempel inaktivera schemaläggning på noden och sedan logga in via en fjärranslutning för att granska nodens händelseloggar eller utföra annan felsökning. När du är klar med din undersökning kan du sedan ta tillbaka noden online genom att aktivera schemaläggning ([rest](/rest/api/batchservice/computenode/enablescheduling)  |  [.net](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)eller utföra någon av de andra åtgärderna som beskrivs ovan.

> [!IMPORTANT]
> Med de åtgärder som beskrivs ovan kan youc ange hur aktiviteter som körs på noden hanteras när du utför åtgärden. Om du inaktiverar schemaläggning på en nod med Batch-klientbiblioteket för .NET kan du till exempel ange ett [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption)-uppräkningsvärde för att ange om aktiviteter som körs ska **avslutas**, **placeras i kö igen** för schemaläggning på andra noder eller om de ska slutföras innan åtgärden körs (**TaskCompletion**).

## <a name="retry-after-errors"></a>Försök igen efter fel

Batch-API: erna meddelar dig om det uppstår ett problem. De kan provas igen och alla innehåller en global återförsöks hanterare för detta ändamål. Det är bäst att använda den här inbyggda mekanismen.

Efter ett haveri bör du vänta en stund (flera sekunder mellan återförsök) innan du försöker igen. Om du försöker igen för ofta eller för snabbt, kommer hanteraren för återförsök att begränsas.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer [om att kontrol lera om det finns fel i pooler och noder](batch-pool-node-error-checking.md).
- Lär dig mer [om att söka efter jobb-och aktivitets fel](batch-job-task-error-checking.md).
- Granska listan över [batch-status och felkoder](/rest/api/batchservice/batch-status-and-error-codes).
