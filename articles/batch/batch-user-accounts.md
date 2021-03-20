---
title: Kör uppgifter under användar konton
description: Lär dig olika typer av användar konton och hur du konfigurerar dem.
ms.topic: how-to
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: cce374e7d7ffb513bed882b048ea54bcbad81b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719367"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Kör uppgifter under användar konton i batch

> [!NOTE]
> Användar kontona som beskrivs i den här artikeln skiljer sig från användar konton som används för Remote Desktop Protocol (RDP) eller Secure Shell (SSH) av säkerhets skäl.
>
> Om du vill ansluta till en nod som kör konfigurationen för virtuella Linux-datorer via SSH, se [Använd fjärr skrivbord till en virtuell Linux-dator i Azure](../virtual-machines/linux/use-remote-desktop.md). Information om hur du ansluter till noder som kör Windows via RDP finns i [ansluta till en virtuell Windows Server-dator](../virtual-machines/windows/connect-logon.md).<br /><br />
> Om du vill ansluta till en nod som kör moln tjänst konfigurationen via RDP, se [aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

En aktivitet i Azure Batch körs alltid under ett användar konto. Som standard körs aktiviteter under standard användar konton utan administratörs behörighet. I vissa fall kanske du vill konfigurera det användar konto som du vill att en aktivitet ska köras under. I den här artikeln beskrivs olika typer av användar konton och hur du konfigurerar dem för ditt scenario.

## <a name="types-of-user-accounts"></a>Typer av användar konton

Azure Batch tillhandahåller två typer av användar konton för att köra aktiviteter:

- **Konton för automatisk användare.** Automatiska användar konton är inbyggda användar konton som skapas automatiskt av batch-tjänsten. Som standard körs aktiviteter under ett automatiskt användar konto. Du kan konfigurera Auto-User-specifikationen för en uppgift som anger under vilket automatiskt användar konto en aktivitet ska köras. Med specifikationen Auto-User kan du ange höjnings nivå och omfånget för det automatiska användar konto som ska köra uppgiften.

- **Ett namngivet användar konto.** Du kan ange ett eller flera namngivna användar konton för en pool när du skapar poolen. Varje användar konto skapas på varje nod i poolen. Förutom konto namnet anger du användar kontots lösen ord, höjnings nivå och, för Linux-pooler, den privata SSH-nyckeln. När du lägger till en aktivitet kan du ange det namngivna användar konto som aktiviteten ska köras under.

> [!IMPORTANT]
> Batch service-versionen 2017 01-01.4.0 innehåller en avbrytande ändring som kräver att du uppdaterar koden för att anropa den versionen. Om du migrerar kod från en äldre version av batch, Observera att egenskapen **runElevated** inte längre stöds i REST API-eller batch-klientens bibliotek. Använd den nya **userIdentity** -egenskapen för en uppgift för att ange höjnings nivå. Mer information om hur du uppdaterar din batch-kod finns i [Uppdatera din kod till det senaste batch-klientcertifikatet](#update-your-code-to-the-latest-batch-client-library) om du använder ett av klient biblioteken.

## <a name="user-account-access-to-files-and-directories"></a>Användar konto åtkomst till filer och kataloger

Både ett automatiskt användar konto och ett namngivet användar konto har Läs-/Skriv behörighet till aktivitetens arbets katalog, delade katalog och flera instanser i arbets katalogen. Båda typerna av konton har Läs behörighet till start-och jobb förberedelse kataloger.

Om en aktivitet körs under samma konto som användes för att köra en start uppgift, har aktiviteten Läs-och Skriv behörighet till Start aktivitets katalogen. På samma sätt, om en aktivitet körs under samma konto som användes för att köra en jobb förberedelse uppgift, har aktiviteten Läs-och Skriv behörighet till jobb förberedelsens aktivitets katalog. Om en aktivitet körs under ett annat konto än uppgiften starta uppgift eller jobb förberedelse, har aktiviteten bara Läs behörighet till respektive katalog.

Mer information om hur du kommer åt filer och kataloger från en aktivitet finns i [filer och kataloger](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Utökad åtkomst för uppgifter

Användar kontots höjnings nivå anger om en aktivitet körs med utökad åtkomst. Både ett automatiskt användar konto och ett namngivet användar konto kan köras med utökad åtkomst. De två alternativen för höjnings nivån är:

- Inte **administratör:** Aktiviteten körs som en standard användare utan utökad åtkomst. Standard höjnings nivån för ett batch-användarkonto är alltid inte **administratör**.
- **Administratör:** Aktiviteten körs som en användare med utökad åtkomst och fungerar med fullständiga administratörs behörigheter.

## <a name="auto-user-accounts"></a>Konton för automatisk användare

Som standard körs aktiviteter i batch under ett automatiskt användar konto, som standard användare utan utökad åtkomst och med pool-scope. Pool-scope innebär att aktiviteten körs under ett automatiskt användar konto som är tillgängligt för alla aktiviteter i poolen. Mer information om pool-scope finns i [köra en aktivitet som en automatisk användare med pool-scope](#run-a-task-as-an-auto-user-with-pool-scope).

Alternativet till pool-scope är uppgifts omfattning. När den automatiska användar specifikationen har kon figurer ATS för uppgifts omfattning skapar batch-tjänsten ett automatiskt användar konto för den uppgiften.

Det finns fyra möjliga konfigurationer för den automatiska användar specifikationen, som var och en motsvarar ett unikt konto för automatisk användare:

- Icke-admin-åtkomst med uppgifts omfattning
- Administratör (utökad åtkomst) med uppgifts omfattning
- Icke-admin-åtkomst med pool-scope
- Administratörs åtkomst med pool-scope

> [!IMPORTANT]
> Aktiviteter som körs under uppgifts omfattning har inte faktiskt åtkomst till andra aktiviteter på en nod. En obehörig användare som har åtkomst till kontot kan dock kringgå den här begränsningen genom att skicka in en uppgift som körs med administratörs behörighet och som har åtkomst till andra aktivitets kataloger. En obehörig användare kan också använda RDP eller SSH för att ansluta till en nod. Det är viktigt att skydda åtkomsten till dina batch-konto nycklar för att förhindra ett sådant scenario. Om du misstänker att ditt konto har komprometterats kan du se till att återskapa dina nycklar.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Köra en aktivitet som en automatisk användare med utökad åtkomst

Du kan konfigurera specifikationen för automatisk användare för administratörs behörighet när du behöver köra en aktivitet med utökad åtkomst. En start-aktivitet kan till exempel behöva utökad åtkomst för att installera program vara på noden.

> [!NOTE]
> Använd endast utökad åtkomst vid behov. Bästa praxis rekommenderar att du ger den minsta behörighet som krävs för att uppnå önskat resultat. Till exempel, om en start aktivitet installerar program vara för den aktuella användaren, i stället för för alla användare, kan du undvika att ge utökad åtkomst till uppgifter. Du kan konfigurera den automatiska användar specifikationen för pool-scope och icke-administratörs åtkomst för alla aktiviteter som behöver köras under samma konto, inklusive start uppgiften.

Följande kodfragment visar hur du konfigurerar den automatiska användar specifikationen. I exemplen anges höjnings nivån till `Admin` och omfånget till `Task` .

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Köra en uppgift som en automatisk användare med pool-scope

När en nod har allokerats skapas två pooliska automatiska användar konton på varje nod i poolen, en med utökad åtkomst och en utan utökad åtkomst. Att ställa in den automatiska användarens omfång till pool-omfånget för en uppgift kör uppgiften under ett av dessa två konton för automatisk användar grupp.

När du anger poolens omfång för den automatiska användaren, körs alla aktiviteter som körs med administratörs åtkomst under samma pool-brett automatiskt användar konto. På samma sätt körs aktiviteter som körs utan administratörs behörighet även under ett enda pool-konto för automatisk användar åtkomst.

> [!NOTE]
> De två automatiskt använda poolerna för användar konton är separata konton. Aktiviteter som körs under det administrativa kontot för hela poolen kan inte dela data med aktiviteter som körs under standard kontot och vice versa.

Fördelen med att köra med samma automatiskt användar konto är att aktiviteter kan dela data med andra aktiviteter som körs på samma nod.

Att dela hemligheter mellan uppgifter är ett scenario där aktiviteter som körs under ett av de två automatiska användar kontona i poolen är användbara. Anta till exempel att en start aktivitet måste tillhandahålla en hemlighet till den nod som andra aktiviteter kan använda. Du kan använda Windows Data Protection API (DPAPI), men det kräver administratörs behörighet. I stället kan du skydda hemligheten på användar nivå. Aktiviteter som körs under samma användar konto kan komma åt hemligheten utan utökad åtkomst.

Ett annat scenario där du kanske vill köra uppgifter under ett automatiskt användar konto med pool-scope är en fil resurs för Message Passing Interface (MPI). En MPI fil resurs är användbar när noderna i MPI-aktiviteten behöver arbeta med samma fildata. Head-noden skapar en fil resurs som de underordnade noderna kan komma åt om de körs under samma automatiskt användar konto.

Följande kodfragment ställer in den automatiska användarens omfång till pool-omfång för en uppgift i batch .NET. Höjnings nivån utelämnas, så aktiviteten körs under standardpoolen för hela det automatiska användar kontot.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Namngivna användar konton

Du kan definiera namngivna användar konton när du skapar en pool. Ett namngivet användar konto har ett namn och ett lösen ord som du anger. Du kan ange höjnings nivå för ett namngivet användar konto. För Linux-noder kan du också ange en privat SSH-nyckel.

Det finns ett namngivet användar konto på alla noder i poolen och är tillgängligt för alla aktiviteter som körs på noderna. Du kan definiera ett valfritt antal namngivna användare för en pool. När du lägger till en aktivitet eller uppgifts samling kan du ange att aktiviteten ska köras under ett av de namngivna användar konton som definierats i poolen.

Ett namngivet användar konto är användbart när du vill köra alla aktiviteter i ett jobb under samma användar konto, men isolera dem från aktiviteter som körs i andra jobb på samma gång. Du kan till exempel skapa en namngiven användare för varje jobb och köra varje jobbs aktiviteter under det namngivna användar kontot. Varje jobb kan sedan dela en hemlighet med sina egna uppgifter, men inte med aktiviteter som körs i andra jobb.

Du kan också använda ett namngivet användar konto om du vill köra en uppgift som anger behörigheter för externa resurser, till exempel fil resurser. Med ett namngivet användar konto styr du användar identiteten och kan använda den användar identiteten för att ange behörigheter.  

Namngivna användar konton möjliggör lösen ords lös SSH mellan Linux-noder. Du kan använda ett namngivet användar konto med Linux-noder som behöver köra aktiviteter med flera instanser. Varje nod i poolen kan köra aktiviteter under ett användar konto som definierats i hela poolen. Mer information om aktiviteter med flera instanser finns i [använda aktiviteter med flera \- instanser för att köra MPI-program](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Skapa namngivna användar konton

Om du vill skapa namngivna användar konton i batch lägger du till en samling användar konton i poolen. Följande kodfragment visar hur du skapar namngivna användar konton i .NET, Java och python. De här kodfragmenten visar hur du skapar både admin-och icke-admin-namngivna konton på en pool. I exemplen skapas pooler med hjälp av moln tjänst konfigurationen, men du använder samma metod när du skapar en Windows-eller Linux-pool med den virtuella dator konfigurationen.

#### <a name="batch-net-example-windows"></a>Batch .NET-exempel (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

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

#### <a name="batch-java-example"></a>Java-exempel för batch

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch python-exempel

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Kör en aktivitet under ett namngivet användar konto med utökad åtkomst

Om du vill köra en aktivitet som en upphöjd användare anger du aktivitetens **UserIdentity** -egenskap till ett namngivet användar konto som skapades med egenskapen **ElevationLevel** inställd på `Admin` .

Det här kodfragmentet anger att aktiviteten ska köras under ett namngivet användar konto. Det här namngivna användar kontot definierades i poolen när poolen skapades. I det här fallet skapades det namngivna användar kontot med administratörs behörighet:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uppdatera koden till det senaste batch-klientprogrammet

Batch-tjänstens version 2017 01-01.4.0 inför en brytande ändring, och ersätter **runElevated** -egenskapen som är tillgänglig i tidigare versioner med **userIdentity** -egenskapen. Följande tabeller innehåller en enkel mappning som du kan använda för att uppdatera din kod från tidigare versioner av klient biblioteken.

### <a name="batch-net"></a>.NET för Batch

| Om koden använder...                  | Uppdatera det till...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` inte angivet | Ingen uppdatering krävs                                                                                               |

### <a name="batch-java"></a>Batch Java

| Om koden använder...                      | Uppdatera det till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` inte angivet | Ingen uppdatering krävs                                                                                                                     |

### <a name="batch-python"></a>Python för Batch

| Om koden använder...                      | Uppdatera det till...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, där <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` inte angivet | Ingen uppdatering krävs                                                                                                                                  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig mer om [filer och kataloger](files-and-directories.md) i Azure Batch.
