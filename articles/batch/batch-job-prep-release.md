---
title: Skapa uppgifter för att förbereda & slutföra jobb på datornoder
description: Använd förberedande uppgifter på jobb nivå för att minimera data överföringen till Azure Batch Compute-noder och släpp uppgifter för noden rensa vid jobb slut för ande.
ms.topic: how-to
ms.date: 02/17/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5b1084cfdd5995b7983badcdce71460f7bdec3d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88919462"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Kör jobb förberedelse-och jobb publicerings aktiviteter i batch Compute-noder

 Ett Azure Batch jobb kräver ofta en viss typ av installation innan dess aktiviteter utförs och underhåll av jobb när dess aktiviteter har slutförts. Du kan behöva hämta vanliga uppgifter från uppgifts indata till dina datornoder eller ladda upp Uppgiftsutdata till Azure Storage när jobbet har slutförts. Du kan använda **jobb förberedelse** -och **jobb publicerings** aktiviteter för att utföra dessa åtgärder.

## <a name="what-are-job-preparation-and-release-tasks"></a>Vad är jobb förberedelse-och publicerings uppgifter?
Innan jobbet körs körs jobb förberedelse aktiviteten på alla Compute-noder som schemalagts för att köra minst en aktivitet. När jobbet har slutförts körs jobb publicerings aktiviteten på varje nod i poolen som utförde minst en aktivitet. Precis som med vanliga batch-uppgifter kan du ange en kommando rad som ska anropas när en jobb förberedelse-eller publicerings uppgift körs.

Jobb förberedelse-och publicerings aktiviteter erbjuder välkända batch-funktioner, till exempel fil hämtning ([resursfiler][net_job_prep_resourcefiles]), förhöjd körning, anpassade miljövariabler, maximal körnings tid, antal försök och kvarhållning av filer.

I följande avsnitt får du lära dig hur du använder de [aktivitets typerna jobpreparationtask][net_job_prep] -och [JobReleaseTask][net_job_release] -klasser som finns i [batch .net][api_net] -biblioteket.

> [!TIP]
> Jobb förberedelse-och publicerings aktiviteter är särskilt användbara i miljöer med delade pooler, där en pool av datornoder behålls mellan jobb körningar och används av många jobb.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>När du ska använda jobb förberedelse-och publicerings uppgifter
Jobb förberedelse-och jobb publicerings aktiviteter passar bra för följande situationer:

**Hämta vanliga uppgifts data**

Batch-jobb kräver ofta en gemensam uppsättning data som indata för jobbets aktiviteter. För dagliga risk analys beräkningar är till exempel marknads data projektspecifika, men gemensamma för alla aktiviteter i jobbet. Dessa marknads data, ofta flera gigabyte i storlek, bör bara hämtas till varje Compute-nod så att alla aktiviteter som körs på noden kan använda den. Använd en **jobb förberedelse uppgift** för att ladda ned dessa data till varje nod innan jobbets övriga aktiviteter körs.

**Ta bort jobb-och Uppgiftsutdata**

I en "delad pool"-miljö, där en Pools datornoder inte inaktive ras mellan jobb, kan du behöva ta bort jobb data mellan körningar. Du kan behöva spara disk utrymme på noderna eller uppfylla organisationens säkerhets principer. Använd en **jobb publicerings aktivitet** för att ta bort data som hämtades av en jobb förberedelse aktivitet eller genererades under aktivitets körningen.

**Kvarhållning av loggar**

Du kanske vill behålla en kopia av loggfiler som aktiviteterna genererar, eller så kanske du vill att krasch dum par filer som kan genereras av misslyckade program. Använd en **jobb publicerings aktivitet** i sådana fall för att komprimera och överföra dessa data till ett [Azure Storage][azure_storage] -konto.

> [!TIP]
> Ett annat sätt att spara loggar och andra jobb-och Uppgiftsutdata är att använda biblioteket för [Azure batch fil konventioner](batch-task-output.md) .
>
>

## <a name="job-preparation-task"></a>Uppgift för jobb förberedelse


Innan du utför ett jobbs aktiviteter kör batch jobb förberedelse aktiviteten på varje beräknings nod som schemalagts för att köra en aktivitet. Som standard väntar batch för jobb förberedelse aktiviteten så att den slutförs innan aktiviteter som är schemalagda att köras på noden körs. Du kan dock konfigurera tjänsten att inte vänta. Om noden startas om körs jobb förberedelse åtgärden igen. Du kan också inaktivera det här beteendet. Om du har ett jobb med en jobb förberedelse aktivitet och en Job Manager-aktivitet har kon figurer ATS, körs jobb förberedelse aktiviteten innan jobb hanterarens aktivitet, precis som för alla andra aktiviteter. Jobb förberedelse aktiviteten körs alltid först.

Jobb förberedelse aktiviteten körs bara på noder som är schemalagda att köra en aktivitet. Detta förhindrar onödig körning av en förberedande uppgift om en nod inte är tilldelad en aktivitet. Detta kan inträffa när antalet aktiviteter för ett jobb är mindre än antalet noder i en pool. Den gäller även när [samtidiga uppgifts körningar](batch-parallel-node-tasks.md) är aktiverat, vilket innebär att vissa noder är inaktiva om antalet aktiviteter är lägre än de totala möjliga samtidiga aktiviteterna. Genom att inte köra jobb förberedelse aktiviteten på inaktiva noder kan du spendera mindre pengar på avgifterna för data överföring.

> [!NOTE]
> [Aktivitets typerna jobpreparationtask][net_job_prep_cloudjob] skiljer sig från [CloudPool. StartTask][pool_starttask] i som aktivitets typerna jobpreparationtask körs i början av varje jobb, medan StartTask körs endast när en Compute Node först ansluter en pool eller startar om.
>


>## <a name="job-release-task"></a>Jobb publicerings aktivitet

När ett jobb har marker ATS som slutfört körs jobb publicerings aktiviteten på varje nod i poolen som utförde minst en aktivitet. Du markerar ett jobb som slutfört genom att utfärda en avslutnings förfrågan. Batch-tjänsten ställer sedan in jobbets tillstånd till att *Avsluta*, avslutar alla aktiva eller pågående aktiviteter som är kopplade till jobbet och Kör jobb publicerings aktiviteten. Jobbet flyttas sedan till *slutfört* tillstånd.

> [!NOTE]
> Jobb borttagning kör även jobb publicerings aktiviteten. Men om ett jobb redan har avslut ATS körs inte publicerings aktiviteten en andra gång om jobbet tas bort senare.

Jobb lanserings aktiviteter kan köras i högst 15 minuter innan de avslutas av batch-tjänsten. Mer information finns i [referens dokumentationen för REST API](/rest/api/batchservice/job/add#jobreleasetask).
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Förberedelse av jobb och publicerings uppgifter med batch .NET
Om du vill använda en uppgift för jobb förberedelse tilldelar du ett [aktivitets typerna jobpreparationtask][net_job_prep] -objekt till ditt jobbs egenskap [CloudJob. aktivitets typerna jobpreparationtask][net_job_prep_cloudjob] . På samma sätt initierar du en [JobReleaseTask][net_job_release] och tilldelar den till jobbets egenskap [CloudJob. JobReleaseTask][net_job_prep_cloudjob] för att ange jobbets versions aktivitet.

I det här kodfragmentet `myBatchClient` är en instans av [metoden batchclient][net_batch_client]och `myPool` är en befintlig pool i batch-kontot.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Som tidigare nämnts körs versions uppgiften när ett jobb avbryts eller tas bort. Avsluta ett jobb med [JobOperations. TerminateJobAsync][net_job_terminate]. Ta bort ett jobb med [JobOperations. DeleteJobAsync][net_job_delete]. Du kan vanligt vis avsluta eller ta bort ett jobb när dess aktiviteter har slutförts, eller när en tids gräns som du har definierat har uppnåtts.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Kod exempel på GitHub
Om du vill se jobb förberedelse-och publicerings aktiviteter i praktiken kan du kolla [JobPrepRelease][job_prep_release_sample] -exempelprojektet på GitHub. Det här konsol programmet gör följande:

1. Skapar en pool med två noder.
2. Skapar ett jobb med jobb förberedelse-, versions-och standard uppgifter.
3. Kör uppgiften jobb Preparation, som först skriver Node-ID: t till en textfil i nodens delade katalog.
4. Kör en aktivitet på varje nod som skriver dess aktivitets-ID till samma textfil.
5. När alla aktiviteter har slutförts (eller om tids gränsen har nåtts) skriver ut innehållet i varje nods textfil till-konsolen.
6. När jobbet har slutförts kör jobb publicerings aktiviteten för att ta bort filen från noden.
7. Skriver ut avslutnings koderna för jobb förberedelse-och publicerings aktiviteterna för varje nod som de utförde.
8. Pausar körningen för att tillåta bekräftelse av jobb och/eller borttagning av pooler.

Utdata från exempel programmet ser ut ungefär så här:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> På grund av variabel skapande och start tid för noder i en ny pool (vissa noder är klara för aktiviteter innan andra) kan du se olika utdata. I synnerhet, eftersom uppgifterna slutförs snabbt, kan en av poolens noder köra alla jobbets aktiviteter. Om detta inträffar ser du att aktiviteterna förberedelse-och publicerings aktiviteter inte finns för den nod som körde inga aktiviteter.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Granska jobb förberedelse-och publicerings uppgifter i Azure Portal
När du kör exempel programmet kan du använda [Azure Portal][portal] för att visa egenskaperna för jobbet och dess aktiviteter, eller till och med Ladda ned den delade text filen som ändras av jobbets aktiviteter.

Skärm bilden nedan visar **bladet förberedande uppgifter** i Azure Portal efter en körning av exempel programmet. Navigera till egenskaperna för *JobPrepReleaseSampleJob* när dina aktiviteter har slutförts (men innan du tar bort jobbet och poolen) och klickar på **förberedelse aktiviteter** eller **versions aktiviteter** för att visa deras egenskaper.

![Egenskaper för jobb förberedelse i Azure Portal][1]

## <a name="next-steps"></a>Nästa steg
### <a name="application-packages"></a>Programpaket
Förutom uppgiften för jobb förberedelse kan du också använda funktionen [programpaket](batch-application-packages.md) i batch för att förbereda datornoder för uppgifts körning. Den här funktionen är särskilt användbar för att distribuera program som inte kräver att ett installations program körs, program som innehåller många (100 +) filer eller program som kräver en strikt versions kontroll.

### <a name="installing-applications-and-staging-data"></a>Installera program och mellanlagrings data
Det här MSDN forum-inlägget ger en översikt över flera metoder för att förbereda dina noder för att köra aktiviteter:

[Installera program och mellanlagrings data i batch Compute-noder][forum_post]

Det finns flera tekniker som du kan använda för att distribuera program och data till Compute-noder. Azure Batch

[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_prep_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_job_prep_resourcefiles]: /dotnet/api/microsoft.azure.batch.jobpreparationtask
[net_job_delete]: /previous-versions/azure/mt281411(v=azure.100)
[net_job_terminate]: /previous-versions/azure/mt188985(v=azure.100)
[net_job_release]: /dotnet/api/microsoft.azure.batch.jobreleasetask
[net_job_release_cloudjob]: /dotnet/api/microsoft.azure.batch.cloudjob
[pool_starttask]: /dotnet/api/microsoft.azure.batch.cloudpool

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
