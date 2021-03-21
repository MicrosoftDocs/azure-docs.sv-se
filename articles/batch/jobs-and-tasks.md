---
title: Jobb och uppgifter i Azure Batch
description: Lär dig mer om jobb och uppgifter och hur de används i ett Azure Batch arbets flöde från en utvecklings synpunkt.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95808593"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Jobb och uppgifter i Azure Batch

I Azure Batch representerar en *uppgift* en beräknings enhet. Ett *jobb* är en samling av dessa uppgifter. Mer information om jobb och uppgifter och hur de används i ett Azure Batch arbets flöde beskrivs nedan.

## <a name="jobs"></a>Jobb

Ett jobb är en samling aktiviteter. Det hanterar hur beräkningen utförs av dess aktiviteter på beräkningsnoderna i en pool.

Ett jobb anger den [pool](nodes-and-pools.md#pools) där arbetet ska köras. Du kan skapa en ny pool för varje jobb eller använda en pool för många jobb. Du kan skapa en pool för varje jobb som är associerat med ett [jobb schema](#scheduled-jobs)eller en pool för alla jobb som är associerade med ett jobb schema.

### <a name="job-priority"></a>Jobbprioritet

Du kan tilldela en valfri jobb prioritet till jobb som du skapar. Batch-tjänsten använder prioritet svärdet för jobbet för att fastställa schemaläggnings ordningen (för alla aktiviteter i jobbet) wtihin varje pool.

Om du vill uppdatera prioriteten för ett jobb anropar du [uppdateringen av egenskaperna för en jobb](/rest/api/batchservice/job/update) åtgärd (batch rest) eller ändrar [CloudJob. Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (batch .net). Prioritets värden sträcker sig från-1000 (lägsta prioritet) till 1000 (högsta prioritet).

I samma pool har jobb med högre prioritet schemaläggnings prioritet framför jobb med lägre prioritet. Aktiviteter med jobb med lägre prioritet som redan körs avbryts inte av aktiviteter i ett jobb med högre prioritet. Jobb med samma prioritets nivå har samma chans att schemaläggas, och sortering av uppgifts körning har inte definierats.

Ett jobb med hög prioritets värde som körs i en pool påverkar inte schemaläggningen av jobb som körs i en separat pool eller i ett annat batch-konto. Jobb prioritet gäller inte för [autopooler](nodes-and-pools.md#autopools), som skapas när jobbet skickas.

### <a name="job-constraints"></a>Jobb begränsningar

Du kan använda jobbegränsningar för att ange vissa begränsningar för dina jobb:

- Du kan ställa in en **högsta wall-clock-tid** så att ett jobb och alla dess aktiviteter avslutas om jobbet körs under längre tid än den angivna wall-clock-tiden.
- Du kan ange det **maximala antalet aktiviteter** som ska göras som en begränsning, inklusive om en aktivitet alltid görs om eller om du inte vill göra ett nytt försök. Om du försöker igen med en aktivitet kommer den att placeras i kö för att köras igen.

### <a name="job-manager-tasks-and-automatic-termination"></a>Jobb Manager-uppgifter och automatisk avslutning

Klientprogrammet kan lägga till aktiviteter till ett jobb eller så kan du ange en [Job Manager-aktivitet](#job-manager-task). En Job Manager-aktivitet innehåller den information som krävs för att skapa de nödvändiga aktiviteterna för ett jobb, där Job Manager-aktiviteten körs på en av beräkningsnoderna i poolen. Job Manager-aktiviteten hanteras specifikt av batch; den placeras i kö så fort jobbet skapas och startas om om det Miss lyckas. En Job Manager-aktivitet krävs för jobb som skapas av ett [jobb schema](#scheduled-jobs), eftersom det är det enda sättet att definiera aktiviteterna innan jobbet instansieras.

Som standard är jobben fortfarande i aktivt läge när alla aktiviteter i jobbet har slutförts. Du kan ändra det här beteendet så att jobbet avbryts automatiskt när alla aktiviteter i jobbet har slutförts. Ange jobbets egenskap **onAllTasksComplete** ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) i batch .net) till `terminatejob` * ' för att automatiskt avsluta jobbet när alla dess aktiviteter är i slutfört tillstånd.

Batch-tjänsten överväger ett jobb *utan aktiviteter för* att alla dess uppgifter ska slutföras. Därför används det här alternativet oftast med en [Job Manager-aktivitet](#job-manager-task). Om du vill använda automatisk avslutning av jobb utan en jobb hanterare, måste du först ställa in det nya jobbets **onAllTasksComplete** -egenskap till `noaction` och sedan ställa in det på `terminatejob` * först när du har lagt till aktiviteter till jobbet.

### <a name="scheduled-jobs"></a>Schemalagda jobb

Med [jobbscheman](/rest/api/batchservice/jobschedule) kan du skapa återkommande jobb i Batch-tjänsten. Ett jobbschema anger när jobb ska köras och innehåller specifikationerna för jobben som ska köras. Du kan ange Schemats varaktighet (hur länge och när schemat tillämpas) och hur ofta jobb skapas under den schemalagda perioden.

## <a name="tasks"></a>Uppgifter

En aktivitet är en beräkningsenhet som associeras med ett jobb. Den körs på en nod. Aktiviteter tilldelas till en nod för körning eller placeras i kö tills en nod blir ledig. Enkelt beskrivet kör en aktivitet ett eller flera program eller skript på en beräkningsnod för att utföra det arbete som du vill ha gjort.

När du skapar en aktivitet kan du ange:

- Uppgiftens **kommandoraden**. Det här är kommandoraden som kör ditt program eller skript på beräkningsnoden.

    Det är viktigt att Observera att kommando raden inte körs under ett gränssnitt. Därför har den inte inbyggt stöd för shell-funktioner som tillägg i [miljövariabler](#environment-settings-for-tasks) (till exempel `PATH`). Om du vill dra nytta av dessa funktioner måste du anropa gränssnittet på kommando raden, t. ex. genom att starta `cmd.exe` på Windows-noder eller `/bin/sh` på Linux:

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Om dina aktiviteter måste köra ett program eller skript som inte finns i nodens `PATH` eller referensmiljövariabler anropar du gränssnittet uttryckligen på aktivitetens kommandorad.
- **Resursfiler** som innehåller de data som ska bearbetas. Dessa filer kopieras automatiskt till noden från Blob Storage i ett Azure Storage-konto innan aktivitetens kommandorad körs. Mer information finns i [Start-aktivitet](#start-task) och [filer och kataloger](files-and-directories.md).
- **Miljövariablerna** som krävs av programmet. Mer information finns i [miljö inställningar för aktiviteter](#environment-settings-for-tasks).
- **Begränsningarna** som aktiviteten ska köras under. Begränsningarna omfattar t.ex. den längsta tid som aktiviteten kan köras, det högsta antal omförsök som ska göras för en aktivitet som misslyckats, samt den längsta tid som filer i aktivitetens arbetskatalog ska bevaras.
- **Programpaket** för distribution till beräkningsnoden som aktiviteten har schemalagts att köra på. Med [programpaket](batch-application-packages.md) blir det lättare att distribuera och hantera versionerna av de program som dina aktiviteter kör. Programpaket på aktivitetsnivå är särskilt användbara i miljöer med delade pooler där olika jobb körs i en pool och där poolen inte tas bort när ett jobb har slutförts. Om jobbet har färre aktiviteter än noder i poolen kan programpaket för aktiviteter minimera dataöverföringen eftersom ditt program bara distribueras till noderna som kör aktiviteterna.
- En **containeravbildning** refererar till Docker Hub eller ett privat register och andra inställningar som behövs för att skapa en Docker-container där uppgifter kan köras på noden. Den här informationen behöver du bara ange om poolen har skapats med en containerkonfiguration.

> [!NOTE]
> Maximal livstid för en uppgift, från den tidpunkt då den läggs till i jobbet tills den slutförs, är 180 dagar. Slutförda uppgifter bevaras i 7 dagar. Data för uppgifter som inte slutförts inom den maximala livstiden är inte tillgängliga.

Förutom de uppgifter som du definierar för att utföra beräkningar på en nod tillhandahålls även flera särskilda aktiviteter av batch-tjänsten:

- [Startaktivitet](#start-task)
- [Job Manager-aktivitet](#job-manager-task)
- [Jobbförberedelse- och jobbpubliceringsaktiviteter](#job-preparation-and-release-tasks)
- [Aktiviteter med flera instanser](#multi-instance-task)
- [Aktivitetsberoenden](#task-dependencies)

### <a name="start-task"></a>Startaktivitet

Genom att associera en startaktivitet med en pool kan du förbereda driftsmiljön för dess noder. Du kan t.ex. installera de program som dina aktiviteter ska köra eller starta bakgrundsprocesser. Start aktiviteten körs varje gång en nod startar, så länge den finns kvar i poolen. Detta inkluderar när noden först läggs till i poolen och när den startas om eller avbildningas om.

En viktig fördel med startaktiviteten är att den kan innehålla all information som krävs för att konfigurera en beräkningsnod och installera de program som krävs för att köra aktiviteterna. Att öka antalet noder i en pool är därför lika enkelt som att ange antalet nya målnoder. Start uppgiften tillhandahåller den information som behövs för batch-tjänsten för att konfigurera de nya noderna och förbereda dem för att acceptera uppgifter.

Som med andra Azure Batch-aktiviteter kan du ange en lista med resursfiler i [Azure Storage](../storage/index.yml), förutom den kommandorad som ska köras. Batchtjänsten kopierar först resursfilerna till noden från Azure Storage och kör sedan kommandoraden. När det gäller en pools startaktivitet innehåller fillistan vanligtvis aktivitetens program och dess beroenden.

Startuppgiften kan dock även innehålla referensdata som ska användas av alla aktiviteter som körs på beräkningsnoden. Till exempel kan start aktivitetens kommando rad utföra en `robocopy` åtgärd för att kopiera programfiler (som har angetts som resursfiler och hämtats till noden) från start aktivitetens [arbets katalog](files-and-directories.md) till den **delade** mappen och sedan köra en MSI-fil eller `setup.exe` .

Normalt bör Batch-tjänsten vänta tills startaktiviteten har slutförts innan noden tilldelas aktiviteter, men detta kan konfigureras vid behov.

Om startaktiviteten misslyckas på en beräkningsnod uppdateras nodens status med feltillståndet och noden är inte tilldelad till någon aktivitet. Startaktiviteten kan misslyckas om det inte går att kopiera aktivitetens resursfiler från lagringen, eller om processen som körs av aktivitetens kommandorad returnerar en slutkod som inte är noll.

Om du lägger till eller uppdaterar startaktiviteten för en befintlig pool måste du starta om dess beräkningsnoder för att startaktiviteten ska tillämpas på noderna.

>[!NOTE]
> Batch begränsar den totala storleken på en startaktivitet. Det här omfattar resursfiler och miljövariabler. Om du vill minska storleken på en startaktivitet finns två metoder:
>
> 1. Du kan använda programpaket för att distribuera program eller data i varje nod i Batch-poolen. Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).
> 2. Du kan manuellt skapa ett zippat arkiv som innehåller dina programfiler. Ladda upp ditt zippade arkiv till Azure Storage som en blob. Ange det zippade arkivet som en resursfil för startaktiviteten. Innan du kör kommandoraden för din startaktivitet packar du upp arkivet från kommandoraden. 
>
>    Du kan packa upp arkivet med valfritt arkiveringsverktyg. Du måste ta med ett verktyg för att packa upp arkivet som en resursfil för startaktiviteten.

### <a name="job-manager-task"></a>Job Manager-aktivitet

Du använder vanligt vis en Job Manager-aktivitet för att styra och/eller övervaka jobb körningen. Till exempel används jobb Manager-aktiviteter ofta för att skapa och skicka uppgifter för ett jobb, bestämma vilka ytterligare aktiviteter som ska köras och fastställa när arbetet är klart.

En Job Manager-aktivitet är dock inte begränsad till dessa aktiviteter. Det är en fullständig fullfjädrade-uppgift som kan utföra alla åtgärder som krävs för jobbet. Den kan till exempel ladda ned en fil som angetts som en parameter, analysera innehållet i filen och skicka fler aktiviteter baserat på innehållet.

En Job Manager-aktivitet startar innan alla andra aktiviteter. Den tillhandahåller följande funktioner:

- Den skickas automatiskt som en aktivitet av Batch-tjänsten när jobbet skapas.
- Den är schemalagd att köras innan andra aktiviteter i ett jobb.
- Dess associerade nod är den sista som tas bort från en pool om poolens storlek minskas.
- Dess avslut kan vara kopplat till avslutet av alla aktiviteter i jobbet.
- En Job Manager-aktivitet får högsta prioritet om den behöver startas om. Om en inaktiv nod inte är tillgänglig kan Batch-tjänsten avsluta någon av de andra aktiviteterna som körs i poolen för att göra plats för Job Manager-aktiviteten så att den kan köras.
- En Job Manager-aktivitet i ett jobb har inte prioritet över aktiviteter i andra jobb. Mellan jobb gäller endast prioriteringar på jobbnivå.

### <a name="job-preparation-and-release-tasks"></a>Jobbförberedelse- och jobbpubliceringsaktiviteter

Batch tillhandahåller jobb förberedelse aktiviteter för konfiguration av för hands jobb och jobb publicerings aktiviteter för underhåll eller rensning av jobb efter jobb.

En jobbförberedelseuppgift körs på alla beräkningsnoder som schemalagts att köra aktiviteter, innan andra jobbuppgifter körs. Du kan till exempel använda en jobb förberedelse aktivitet för att kopiera data som delas av alla aktiviteter, men som är unika för jobbet.

När ett jobb har slutförts körs en jobbpubliceringsuppgift på alla noder i poolen som har kört minst en uppgift. Till exempel kan en jobb publicerings uppgift ta bort data som har kopierats av jobb förberedelse aktiviteten, eller så kan den komprimera och ladda upp diagnostikdata.

Du kan ange en kommandorad som ska köras när aktiviteten anropas både med jobbförberedelse- och jobbpubliceringsaktiviteter. De stöder funktioner som filhämtning, upphöjd körning, anpassade miljövariabler, högsta körningstid, antal omförsök och kvarhållningstid för filer.

Mer information om jobbförberedelse- och jobbpubliceringsaktiviteter finns i [Köra jobbförberedelse- och jobbpubliceringsaktiviteter på Azure Batch-beräkningsnoder](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Aktivitet med flera instanser

En [aktivitet med flera instanser](batch-mpi.md) är en aktivitet som är konfigurerad att köras på mer än en beräkningsnod samtidigt. Med aktiviteter med flera instanser kan du aktivera högpresterande data behandlings scenarier som kräver en grupp Compute-noder som tilldelas tillsammans för att bearbeta en enskild arbets belastning, till exempel MPI (Message Passing Interface).

Detaljerad information om hur du kör MPI-jobb i Batch med hjälp av Batch .NET-biblioteket finns i [Använda aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Aktivitetsberoenden

Med [aktivitetsberoenden](batch-task-dependencies.md) kan du, som namnet antyder, ange att en aktivitet är beroende av att andra aktiviteter slutförs innan den kan köras. Den här funktionen har stöd för situationer där en ”underordnad” aktivitet använder utdata från en ”överordnad” aktivitet, eller där en överordnad aktiviteten utför viss initiering som krävs av en underordnad aktivitet.

Om du vill använda den här funktionen måste du först [Aktivera aktivitets beroenden](batch-task-dependencies.md#enable-task-dependencies
) för batch-jobbet. För varje aktivitet som är beroende av en annan (eller flera andra) anger du sedan de aktiviteter som den aktiviteten är beroende av.

Med aktivitetsberoenden kan du konfigurera scenarier som följande:

- *aktivitetb* är beroende av *taska* (*aktivitetb* kommer inte att börja köras förrän *uppgiften* om har slutförts).
- *aktivitetC* är beroende av både *aktivitetA* och *aktivitetB*.
- *aktivitetD* är beroende av en serie aktiviteter, till exempel aktivitet *1* till och med *10*, innan den kan köras.

Mer information finns i [aktivitets beroenden i Azure Batch](batch-task-dependencies.md) och kod exemplet [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) i [Azure-Batch-samples](https://github.com/Azure-Samples/azure-batch-samples) GitHub-lagringsplatsen.

### <a name="environment-settings-for-tasks"></a>Miljöinställningar för aktiviteter

Varje aktivitet som utförs av Batch-tjänsten har åtkomst till miljövariabler som anges på datornoder. Detta inkluderar miljövariabler som definieras av batch-tjänsten ([tjänstedefinierade](./batch-compute-node-environment-variables.md) och anpassade miljövariabler som du kan definiera för dina aktiviteter). Programmen och skripten som dina aktiviteter utför har åtkomst till dessa miljövariabler under körning.

Du kan ange anpassade miljövariabler på aktivitets- eller jobbnivå genom att fylla i egenskapen för *miljöinställningar* för dessa entiteter. Mer information finns i CloudTask-åtgärden [för att lägga till en aktivitet i ett jobb](/rest/api/batchservice/task/add?)(batch REST API) eller egenskaperna [. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) och [CloudJob. CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) i batch .net.

Klientprogrammet eller tjänsten kan hämta en aktivitets miljövariabler, både tjänstdefinierade och anpassade, med hjälp av åtgärden [Hämta information om en aktivitet](/rest/api/batchservice/task/get) (Batch REST) eller genom att komma åt egenskapen [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (Batch .NET). Processer som körs på en beräkningsnod kan komma åt dessa och andra miljövariabler på noden, till exempel genom att använda vanlig `%VARIABLE_NAME%`- (Windows) eller `$VARIABLE_NAME`-syntax (Linux).

Du hittar en fullständig lista över alla definierade miljövariabler i [Beräkna nodmiljövariabler](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [filer och kataloger](files-and-directories.md).
