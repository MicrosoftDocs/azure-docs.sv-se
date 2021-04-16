---
title: Köra uppgifter under användarkonton
description: Lär dig vilka typer av användarkonton och hur du konfigurerar dem.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 02cad0bff9e76ec5db82c417f2439b12ef088045
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389289"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Köra uppgifter under användarkonton i Batch

> [!NOTE]
> Användarkontona som beskrivs i den här artikeln skiljer sig från användarkonton som används för Remote Desktop Protocol (RDP) eller Secure Shell (SSH), av säkerhetsskäl.
>
> Information om hur du ansluter till en nod som kör den virtuella Linux-datorkonfigurationen via SSH finns i Installera och konfigurera [xrdp för](../virtual-machines/linux/use-remote-desktop.md)att använda fjärrskrivbord med Ubuntu. Information om hur du ansluter till noder som kör Windows via RDP finns i [Ansluta och logga in på en virtuell Azure-dator som kör Windows.](../virtual-machines/windows/connect-logon.md)
>
> Information om hur du ansluter till en nod som kör molntjänstkonfigurationen via RDP finns i [Aktivera Anslutning till fjärrskrivbord för en roll i Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

En aktivitet Azure Batch körs alltid under ett användarkonto. Som standard körs uppgifter under standardanvändarkonton, utan administratörsbehörighet. För vissa scenarier kanske du vill konfigurera det användarkonto under vilket du vill att en uppgift ska köras. Den här artikeln beskriver typerna av användarkonton och hur du konfigurerar dem för ditt scenario.

## <a name="types-of-user-accounts"></a>Typer av användarkonton

Azure Batch två typer av användarkonton för att köra uppgifter:

- **Konton för automatisk användare.** Automatiska användarkonton är inbyggda användarkonton som skapas automatiskt av Batch-tjänsten. Som standard körs uppgifter under ett automatiskt användarkonto. Du kan konfigurera specifikationen för automatisk användare för en uppgift för att ange under vilket automatiskt användarkonto en uppgift ska köras. Med specifikationen för automatisk användare kan du ange höjningsnivå och omfång för det automatiskt användarkonto som ska köra uppgiften.

- **Ett namngivet användarkonto.** Du kan ange ett eller flera namngivna användarkonton för en pool när du skapar poolen. Varje användarkonto skapas på varje nod i poolen. Förutom kontonamnet anger du användarkontolösenordet, upphöjningsnivån och, för Linux-pooler, den privata SSH-nyckeln. När du lägger till en uppgift kan du ange det namngivna användarkonto som aktiviteten ska köras under.

> [!IMPORTANT]
> Batch-tjänsten version 2017-01-01.4.0 införde en icke-brytningsändring som kräver att du uppdaterar koden för att anropa den versionen eller senare. Se [Uppdatera koden till det senaste Batch-klientbiblioteket för snabba](#update-your-code-to-the-latest-batch-client-library) riktlinjer för att uppdatera Batch-koden från en äldre version.

## <a name="user-account-access-to-files-and-directories"></a>Användarkontoåtkomst till filer och kataloger

Både ett automatiskt användarkonto och ett namngivet användarkonto har läs-/skrivåtkomst till aktivitetens arbetskatalog, delade katalog och katalogen med uppgifter med flera instanser. Båda kontotyperna har läsbehörighet till kataloger för start och jobbförberedelse.

Om en aktivitet körs under samma konto som användes för att köra en startaktivitet, har aktiviteten läs- och skrivbehörighet till katalogen startaktivitet. På samma sätt, om en aktivitet körs under samma konto som användes för att köra en jobbförberedelseaktivitet, har aktiviteten läs- och skrivbehörighet till katalogen för jobbförberedelseaktiviteten. Om en aktivitet körs under ett annat konto än startaktiviteten eller jobbförberedelseaktiviteten har aktiviteten endast läsbehörighet till respektive katalog.

Mer information om hur du kommer åt filer och kataloger från en uppgift finns [i Filer och kataloger](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Utökad åtkomst för uppgifter

Användarkontots höjningsnivå anger om en uppgift körs med förhöjd åtkomst. Både ett automatiskt användarkonto och ett namngivet användarkonto kan köras med förhöjd åtkomst. De två alternativen för höjningsnivå är:

- **NonAdmin:** Uppgiften körs som en standardanvändare utan förhöjd åtkomst. Standardnivån för utökade privilegier för ett Batch-användarkonto är alltid **NonAdmin.**
- **Administratör:** Uppgiften körs som en användare med förhöjd behörighet och fungerar med fullständiga administratörsbehörigheter.

## <a name="auto-user-accounts"></a>Konton för automatisk användare

Som standard körs uppgifter i Batch under ett automatiskt användarkonto, som standardanvändare utan utökad åtkomst och med poolomfång. Poolomfång innebär att aktiviteten körs under ett automatiskt användarkonto som är tillgängligt för alla aktiviteter i poolen. Mer information om poolomfång finns i [Köra en uppgift som en automatisk användare med poolomfång.](#run-a-task-as-an-auto-user-with-pool-scope)

Alternativet till poolomfång är uppgiftsomfång. När specifikationen för automatisk användare har konfigurerats för aktivitetsomfång skapar Batch-tjänsten ett automatiskt användarkonto endast för den uppgiften.

Det finns fyra möjliga konfigurationer för specifikationen för automatisk användare, som var och en motsvarar ett unikt automatiskt användarkonto:

- Icke-administratörsåtkomst med uppgiftsomfång
- Administratörsåtkomst (förhöjd) med uppgiftsomfång
- Icke-administratörsåtkomst med poolomfång
- Administratörsåtkomst med poolomfång

> [!IMPORTANT]
> Aktiviteter som körs under uppgiftsomfånget har inte faktisk åtkomst till andra aktiviteter på en nod. En obehörig användare med åtkomst till kontot kan dock komma runt den här begränsningen genom att skicka en uppgift som körs med administratörsbehörighet och som har åtkomst till andra aktivitetskataloger. En obehörig användare kan också använda RDP eller SSH för att ansluta till en nod. Det är viktigt att skydda åtkomsten till dina Batch-kontonycklar för att förhindra ett sådant scenario. Om du misstänker att ditt konto har komprometterats måste du återskapa dina nycklar.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Köra en uppgift som en automatisk användare med förhöjd behörighet

Du kan konfigurera specifikationen för automatisk användare för administratörsbehörighet när du behöver köra en uppgift med förhöjd åtkomst. En startaktivitet kan till exempel behöva förhöjd behörighet för att installera programvara på noden.

> [!NOTE]
> Använd endast förhöjd åtkomst vid behov. Bästa praxis rekommenderar att du beviljar den lägsta behörighet som krävs för att uppnå önskat resultat. Om en startaktivitet till exempel installerar programvara för den aktuella användaren, i stället för för alla användare, kan du undvika att bevilja utökad åtkomst till uppgifter. Du kan konfigurera specifikationen för automatisk användare för poolomfång och icke-administratörsåtkomst för alla aktiviteter som måste köras under samma konto, inklusive startaktiviteten.

Följande kodfragment visar hur du konfigurerar specifikationen för automatisk användare. I exemplen anges höjningsnivån `Admin` till och omfånget till `Task` .

#### <a name="batch-net"></a>.NET för Batch

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```

#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");
```

#### <a name="batch-python"></a>Python för Batch

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Köra en uppgift som en automatisk användare med poolomfång

När en nod etableras skapas två poolomfattande automatiska användarkonton på varje nod i poolen, ett med förhöjd åtkomst och ett utan förhöjd åtkomst. Om du anger omfånget för den automatiska användaren till poolomfång för en viss uppgift körs uppgiften under något av dessa två poolomfattande automatiska användarkonton.

När du anger poolomfång för den automatiska användaren körs alla aktiviteter som körs med administratörsåtkomst under samma automatiska användarkonto för hela poolen. På samma sätt körs uppgifter som körs utan administratörsbehörighet även under ett enda automatiskt användarkonto för hela poolen.

> [!NOTE]
> De två poolomfattande automatiska användarkontona är separata konton. Uppgifter som körs under det administrativa kontot för hela poolen kan inte dela data med uppgifter som körs under standardkontot och vice versa.

Fördelen med att köra under samma automatiskt användarkonto är att aktiviteter kan dela data med andra aktiviteter som körs på samma nod.

Att dela hemligheter mellan aktiviteter är ett scenario där det är användbart att köra aktiviteter under ett av de två poolomfattande automatiska användarkontona. Anta till exempel att en startaktivitet behöver etablera en hemlighet på noden som andra aktiviteter kan använda. Du kan använda Windows Data Protection API (DPAPI), men det kräver administratörsbehörighet. I stället kan du skydda hemligheten på användarnivå. Uppgifter som körs under samma användarkonto kan komma åt hemligheten utan förhöjd åtkomst.

Ett annat scenario där du kanske vill köra uppgifter under ett automatiskt användarkonto med poolomfång är en MPI-filresurs (Message Passing Interface). En MPI-filresurs är användbar när noderna i MPI-aktiviteten behöver arbeta med samma fildata. Huvudnoden skapar en filresurs som de underordnade noderna kan komma åt om de körs under samma automatiska användarkonto.

Följande kodfragment anger omfånget för den automatiska användaren till poolomfånget för en uppgift i Batch .NET. Höjningsnivån utelämnas, så aktiviteten körs under det automatiska användarkontot för hela poolen.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Namngivna användarkonton

Du kan definiera namngivna användarkonton när du skapar en pool. Ett namngivet användarkonto har ett namn och lösenord som du anger. Du kan ange höjningsnivån för ett namngivet användarkonto. För Linux-noder kan du även ange en privat SSH-nyckel.

Ett namngivet användarkonto finns på alla noder i poolen och är tillgängligt för alla aktiviteter som körs på dessa noder. Du kan definiera val annat antal namngivna användare för en pool. När du lägger till en uppgift eller uppgiftssamling kan du ange att aktiviteten ska köras under ett av de namngivna användarkonton som definierats i poolen.

Ett namngivet användarkonto är användbart när du vill köra alla aktiviteter i ett jobb under samma användarkonto, men isolera dem från aktiviteter som körs i andra jobb samtidigt. Du kan till exempel skapa en namngiven användare för varje jobb och köra aktiviteterna för varje jobb under det namngivna användarkontot. Varje jobb kan sedan dela en hemlighet med sina egna uppgifter, men inte med aktiviteter som körs i andra jobb.

Du kan också använda ett namngivet användarkonto för att köra en uppgift som anger behörigheter för externa resurser, till exempel filresurser. Med ett namngivet användarkonto styr du användaridentiteten och kan använda den användaridentiteten för att ange behörigheter.  

Namngivna användarkonton aktiverar lösenordslös SSH mellan Linux-noder. Du kan använda ett namngivet användarkonto med Linux-noder som behöver köra aktiviteter med flera instanser. Varje nod i poolen kan köra aktiviteter under ett användarkonto som definierats för hela poolen. Mer information om aktiviteter med flera instanser finns i [Använda aktiviteter med flera instanser för att köra \- MPI-program.](batch-mpi.md)

### <a name="create-named-user-accounts"></a>Skapa namngivna användarkonton

Om du vill skapa namngivna användarkonton i Batch lägger du till en samling användarkonton i poolen. Följande kodfragment visar hur du skapar namngivna användarkonton i .NET, Java och Python. Dessa kodfragment visar hur du skapar konton med både administratörs- och icke-administratörsnamnet i en pool. Exemplen skapar pooler med hjälp av molntjänstkonfigurationen, men du använder samma metod när du skapar en Windows- eller Linux-pool med hjälp av den virtuella datorkonfigurationen.

#### <a name="batch-net-example-windows"></a>Batch .NET-exempel (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
    nodeAgentSkuId: "batch.node.windows amd64");

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET-exempel (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "Standard_A1",
    virtualMachineConfiguration: virtualMachineConfiguration);
// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-java-example"></a>Batch Java-exempel

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withVirtualMachineConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python-exempel

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Kör en uppgift under ett namngivet användarkonto med förhöjd behörighet

Om du vill köra en uppgift som en upphöjd användare anger du aktivitetens **UserIdentity-egenskap** till ett namngivet användarkonto som skapades med egenskapen **ElevationLevel** inställd på `Admin` .

Det här kodfragmentet anger att aktiviteten ska köras under ett namngivet användarkonto. Det här namngivna användarkontot definierades i poolen när poolen skapades. I det här fallet skapades det namngivna användarkontot med administratörsbehörighet:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uppdatera koden till det senaste Batch-klientbiblioteket

Batch-tjänstens version 2017-01-01.4.0 införde en icke-felande ändring och ersätter den **runElevated-egenskap** som är tillgänglig i tidigare versioner med **egenskapen userIdentity.** Följande tabeller innehåller en enkel mappning som du kan använda för att uppdatera koden från tidigare versioner av klientbiblioteken.

### <a name="batch-net"></a>.NET för Batch

| Om din kod använder...                  | Uppdatera den till...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` har inte angetts | Ingen uppdatering krävs                                                                                               |

### <a name="batch-java"></a>Batch Java

| Om din kod använder...                      | Uppdatera den till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` har inte angetts | Ingen uppdatering krävs                                                                                                                     |

### <a name="batch-python"></a>Python för Batch

| Om din kod använder...                      | Uppdatera den till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` har inte angetts | Ingen uppdatering krävs                                                                                                                                  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer [om Batch-tjänstarbetsflödet och primära](batch-service-workflow-features.md) resurser som pooler, noder, jobb och aktiviteter.
- Lär dig [mer om filer och kataloger](files-and-directories.md) i Azure Batch.
