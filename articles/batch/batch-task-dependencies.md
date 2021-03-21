---
title: Skapa aktivitets beroenden för att köra uppgifter
description: Skapa uppgifter som är beroende av slut för ande av andra uppgifter för att bearbeta MapReduce-format och liknande stor data arbets belastningar i Azure Batch.
ms.topic: how-to
ms.date: 12/28/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: ef05a98fffc3c0684ad0fa29f2f9f039b388f5ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97803943"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Skapa aktivitets beroenden för att köra uppgifter som är beroende av andra aktiviteter

Med aktivitets beroenden för batch, skapar du aktiviteter som är schemalagda för körning på datornoderna när en eller flera överordnade aktiviteter har slutförts. Du kan till exempel skapa ett jobb som återger varje bild ruta i en 3D-film med separata, parallella aktiviteter. Den sista uppgiften – sammanfognings uppgiften--sammanfogar de åter givningar som återges i den fullständiga filmen när alla ramar har Render ATS.

Vissa scenarier där aktivitets beroenden är användbara är:

- MapReduce-arbetsbelastningar i molnet.
- Jobb vars data bearbetnings uppgifter kan uttryckas som ett riktat acykliska diagram (DAG).
- Processer för för åter givning och efter åter givning, där varje aktivitet måste slutföras innan nästa aktivitet kan börja.
- Andra jobb där underordnade aktiviteter är beroende av utdata från överordnade aktiviteter.

Som standard schemaläggs beroende aktiviteter endast för körning efter att den överordnade aktiviteten har slutförts. Du kan också ange en [beroende åtgärd](#dependency-actions)  för att åsidosätta standard beteendet och köra aktiviteter när den överordnade aktiviteten Miss lyckas.

## <a name="task-dependencies-with-batch-net"></a>Aktivitets beroenden med batch .NET

I den här artikeln diskuterar vi hur du konfigurerar aktivitets beroenden med hjälp av [batch .net](/dotnet/api/microsoft.azure.batch) -biblioteket. Först visar vi hur du [aktiverar aktivitets beroenden](#enable-task-dependencies) för dina jobb och visar hur du [konfigurerar en aktivitet med beroenden](#create-dependent-tasks). Vi beskriver också hur du anger en beroende åtgärd för att köra beroende uppgifter om överordnat Miss lyckas. Slutligen diskuterar vi de [beroende scenarier](#dependency-scenarios) som batch stöder.

## <a name="enable-task-dependencies"></a>Aktivera aktivitets beroenden

Om du vill använda aktivitets beroenden i batch-programmet måste du först konfigurera jobbet så att det använder aktivitets beroenden. I batch .NET aktiverar du det på din [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) genom att ställa in dess [UsesTaskDependencies](/dotnet/api/microsoft.azure.batch.cloudjob.usestaskdependencies) -egenskap till `true` :

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

I föregående kodfragment är "metoden batchclient" en instans av klassen [metoden batchclient](/dotnet/api/microsoft.azure.batch.batchclient) .

## <a name="create-dependent-tasks"></a>Skapa beroende uppgifter

Om du vill skapa en uppgift som är beroende av att en eller flera överordnade aktiviteter har slutförts, kan du ange att aktiviteten är beroende av "de andra aktiviteterna. I batch .NET konfigurerar du egenskapen [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) med en instans av klassen [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies) :

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Det här kodfragmentet skapar en beroende uppgift med aktivitets-ID "blommor". Uppgiften "blommor" är beroende av aktiviteterna "regn" och "Sun". Uppgiften "blommor" schemaläggs att köras på en Compute-nod först efter att aktiviteterna "regn" och "Sun" har slutförts.

> [!NOTE]
> Som standard anses en aktivitet vara slutförd när den är i slutfört tillstånd och dess slut kod är `0` . I batch .NET innebär det att ett egenskaps värde för [CloudTask. State](/dotnet/api/microsoft.azure.batch.cloudtask.state) är `Completed` och värdet för egenskapen [TaskExecutionInformation. ExitCode](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.exitcode) för CloudTask `0` . Information om hur du ändrar detta finns i avsnittet [beroende åtgärder](#dependency-actions) .

## <a name="dependency-scenarios"></a>Beroende scenarier

Det finns tre grundläggande scenarier för aktivitets beroenden som du kan använda i Azure Batch: ett-till-ett-till-ett-ett-till-många-och aktivitets-ID-intervall beroende. Dessa tre scenarier kan kombineras för att ge ett fjärde scenario: många-till-många.

| Situationen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Exempel | Exemplet |
|:---:| --- | --- |
|  [En-till-en](#one-to-one) |*aktivitetb* är beroende av *uppgiften* in <p/> *aktivitetb* kommer inte att schemaläggas för körning förrän *uppgiften* i har slutförts |:::image type="content" source="media/batch-task-dependency/01_one_to_one.png" alt-text="Diagram som visar ett beroende scenario för en-till-en-aktivitet."::: |
|  [En-till-många](#one-to-many) |*aktivitetc* är beroende av både *Aktiviteta* och *aktivitetb* <p/> *aktivitetc* kommer inte att schemaläggas för körning förrän båda *aktiviteterna* och *aktivitetb* har slutförts |:::image type="content" source="media/batch-task-dependency/02_one_to_many.png" alt-text="Diagram som visar ett beroende scenario med en-till-många-aktivitet."::: |
|  [Aktivitets-ID-intervall](#task-id-range) |*taskd* är beroende av en mängd aktiviteter <p/> *tasked* kommer inte att schemaläggas för körning förrän uppgifterna med ID *1* till och med *10* har slutförts |:::image type="content" source="media/batch-task-dependency/03_task_id_range.png" alt-text="Diagram som visar aktivitets-ID intervall aktivitets beroende scenario."::: |

> [!TIP]
> Du kan skapa **många-till-många-** relationer, till exempel var aktiviteterna C, D, E och F var beroende av aktiviteterna A och B. Detta är användbart, till exempel i parallella för bearbetnings scenarier där de underordnade uppgifterna är beroende av utdata från flera överordnade aktiviteter.
> 
> I exemplen i det här avsnittet körs en beroende aktivitet bara när de överordnade aktiviteterna har slutförts. Det här beteendet är standard beteendet för en beroende uppgift. Du kan köra en beroende uppgift när en överordnad aktivitet Miss lyckas genom att ange en beroende [åtgärd](#dependency-actions)  för att åsidosätta standard beteendet.

### <a name="one-to-one"></a>En-till-en

I en en-till-en-relation är en uppgift beroende av att en överordnad aktivitet har slutförts. Om du vill skapa beroendet anger du ett enda aktivitets-ID i den statiska metoden [TaskDependencies. OnId](/dotnet/api/microsoft.azure.batch.taskdependencies.onid) när du fyller i egenskapen [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>En-till-många

I en en-till-många-relation är en uppgift beroende av att flera överordnade aktiviteter har slutförts. Skapa beroendet genom att ange en samling aktivitets-ID: n till den statiska metoden [TaskDependencies. OnIds](/dotnet/api/microsoft.azure.batch.taskdependencies.onids) när du fyller i egenskapen [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

### <a name="task-id-range"></a>Aktivitets-ID-intervall

En aktivitet är beroende av ett antal överordnade aktiviteter, beroende på slutförandet av aktiviteter vars ID ligger inom ett intervall.
Om du vill skapa beroendet anger du det första och sista aktivitets-ID: t i intervallet till den statiska metoden [TaskDependencies](/dotnet/api/microsoft.azure.batch.taskdependencies.onidrange) när du fyller i egenskapen [CloudTask. DependsOn](/dotnet/api/microsoft.azure.batch.cloudtask.dependson) .

> [!IMPORTANT]
> När du använder aktivitets-ID-intervall för dina beroenden väljs endast uppgifter med ID: n som representerar heltals värden av intervallet. Intervallet väljer till exempel `1..10` uppgifter `3` och `7` , men inte `5flamingoes` .
>
> Inledande nollor är inte signifikanta vid utvärdering av intervall beroenden, så uppgifter med sträng identifierare `4` , `04` och `004` kommer att vara *inom* intervallet och de kommer att behandlas som `4` en uppgift, så den första som slutföras kommer att uppfylla beroendet.
>
> Varje aktivitet i intervallet måste uppfylla beroendet, antingen genom att den slutförs eller genom att ett fel som har mappats till en [beroende åtgärd](#dependency-actions) angetts som **uppfylls**.

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>Beroende åtgärder

Som standard körs en beroende uppgift eller en uppsättning aktiviteter bara efter att en överordnad aktivitet har slutförts. I vissa fall kanske du vill köra beroende uppgifter även om den överordnade aktiviteten Miss lyckas. Du kan åsidosätta standard beteendet genom att ange en beroende åtgärd.

En beroende åtgärd anger om en beroende uppgift är giltig att köras, baserat på lyckad eller misslyckad aktivitet för den överordnade aktiviteten. Anta till exempel att en beroende uppgift väntar på data från slut för ande av den överordnade aktiviteten. Om den överordnade aktiviteten Miss lyckas kan det hända att den beroende aktiviteten fortfarande kan köras med äldre data. I det här fallet kan en beroende åtgärd ange att den beroende uppgiften är giltig att köras trots att den överordnade aktiviteten Miss lyckas.

En beroende åtgärd baseras på ett avslutnings villkor för den överordnade aktiviteten. Du kan ange en beroende åtgärd för något av följande avslutnings villkor:

- När ett för bearbetnings fel inträffar.
- När ett fil överförings fel inträffar. Om aktiviteten avslutas med en slutkod som har angetts via **exitCodes** eller **exitCodeRanges**, och sedan påträffar ett fil överförings fel, har åtgärden som anges av slut koden företräde.
- När aktiviteten avslutas med en slutkod som definieras av egenskapen **ExitCodes** .
- När aktiviteten avslutas med en slutkod som ligger inom ett intervall som anges av egenskapen **ExitCodeRanges** .
- Standard fallet om aktiviteten avslutas med en slutkod som inte har definierats av **ExitCodes** eller **ExitCodeRanges**, eller om aktiviteten avslutas med ett för bearbetnings fel och egenskapen **PreProcessingError** inte har angetts, eller om aktiviteten Miss lyckas med ett fil överförings fel och **FileUploadError** -egenskapen inte har angetts. 

För .NET, se [ExitConditions](/dotnet/api/microsoft.azure.batch.exitconditions) -klassen för mer information om dessa villkor.

Ange en beroende åtgärd i .NET genom att ange egenskapen [ExitOptions. DependencyAction](/dotnet/api/microsoft.azure.batch.exitoptions.dependencyaction) för avslutnings villkoret till något av följande:

- **Tillfredsställ**: visar att beroende aktiviteter är berättigade att köras om den överordnade aktiviteten avslutas med ett angivet fel.
- **Blockera**: visar att beroende aktiviteter inte är berättigade att köras.

Standardvärdet för egenskapen **DependencyAction** **är för** slutkod 0, och **blockera** för alla andra avslutnings villkor.

I följande kodfragment anges egenskapen **DependencyAction** för en överordnad aktivitet. Om den överordnade aktiviteten avslutas med ett för bearbetnings fel, eller med de angivna fel koderna, blockeras den beroende uppgiften. Om den överordnade aktiviteten avslutas med andra fel än noll, är den beroende uppgiften giltig att köras.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Kodexempel

[TaskDependencies](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) -exempelprojektet på GitHub visar:

- Så här aktiverar du aktivitets beroende för ett jobb.
- Så här skapar du uppgifter som är beroende av andra uppgifter.
- Så här kör du dessa uppgifter i en pool med datornoder.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om funktionen [programpaket](batch-application-packages.md) i batch, som är ett enkelt sätt att distribuera och version av programmen som dina aktiviteter kör på datornoderna.
- Lär dig mer om [fel sökning av jobb och uppgifter](batch-job-task-error-checking.md).
