---
title: Containerarbetsbelastningar
description: Lär dig hur du kör och skalar appar från behållar avbildningar på Azure Batch. Skapa en pool av datornoder som stöder körning av container aktiviteter.
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843455"
---
# <a name="run-container-applications-on-azure-batch"></a>Kör behållar program på Azure Batch

Med Azure Batch kan du köra och skala ett stort antal batch-databearbetnings jobb på Azure. Batch-aktiviteter kan köras direkt på virtuella datorer (noder) i en batch-pool, men du kan också ställa in en batch-pool för att köra uppgifter i Docker-kompatibla behållare på noderna. Den här artikeln visar hur du skapar en pool med datornoder som stöder körning av behållar aktiviteter och sedan kör behållar aktiviteter i poolen.

I kod exemplen används batch .NET-och python SDK: er. Du kan också använda andra batch-SDK: er och verktyg, inklusive Azure Portal, för att skapa behållar aktiverade batch-pooler och köra behållar aktiviteter.

## <a name="why-use-containers"></a>Varför använda containrar?

Att använda behållare är ett enkelt sätt att köra batch-uppgifter utan att behöva hantera en miljö och beroenden för att köra program. Behållare distribuerar program som enkla, portabla, självpresterande enheter som kan köras i flera olika miljöer. Du kan till exempel bygga och testa en behållare lokalt och sedan ladda upp behållar avbildningen till ett register i Azure eller någon annan stans. Distributions modellen för behållare säkerställer att körnings miljön för ditt program alltid är korrekt installerad och konfigurerad oavsett var du är värd för programmet. Containerbaserade uppgifter i batch kan också dra nytta av funktioner i icke-container-aktiviteter, inklusive programpaket och hantering av resursfiler och utdatafiler.

## <a name="prerequisites"></a>Förutsättningar

Du bör känna till behållar koncept och hur du skapar en batch-pool och ett jobb.

- **SDK-versioner**: batch SDK: er stöder behållar avbildningar från och med följande versioner:
  - Batch REST API version 2017 -09 – 01.6.0
  - Batch .NET SDK-version 8.0.0
  - Batch python SDK version 4,0
  - Batch Java SDK version 3,0
  - Batch Node.js SDK version 3,0

- **Konton**: i din Azure-prenumeration måste du skapa ett batch-konto och eventuellt ett Azure Storage konto.

- **En virtuell dator avbildning som stöds**: behållare stöds bara i pooler som skapats med konfigurationen av den virtuella datorn, från avbildningar som beskrivs i följande avsnitt, "avbildningar som stöds av virtuella datorer". Om du anger en anpassad avbildning, se överväganden i följande avsnitt och kraven i [använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

Tänk på följande begränsningar:

- Batch tillhandahåller RDMA-stöd endast för behållare som körs på Linux-pooler.

- För arbets belastningar i Windows-behållare rekommenderar vi att du väljer en virtuell dator storlek med flera kärnor för poolen.

## <a name="supported-virtual-machine-images"></a>Avbildningar av virtuella datorer som stöds

Använd en av följande Windows-eller Linux-avbildningar som stöds för att skapa en pool med virtuella dator beräknings noder för behållar arbets belastningar. Mer information om Marketplace-avbildningar som är kompatibla med batch finns i [lista över avbildningar av virtuella datorer](batch-linux-nodes.md#list-of-virtual-machine-images).

### <a name="windows-support"></a>Windows-support

Batch stöder Windows Server-avbildningar som har stöd för behållar stöd. Dessa bild-SKU-namn är vanligt vis suffix för `-with-containers` eller `-with-containers-smalldisk` . Dessutom kännetecknar [API: n för att visa alla avbildningar som stöds i batch](batch-linux-nodes.md#list-of-virtual-machine-images) en `DockerCompatible` funktion om avbildningen har stöd för Docker-behållare.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på Windows.

### <a name="linux-support"></a>Linux-support

För för Linux container-arbetsbelastningar stöder batch för närvarande följande Linux-avbildningar som publicerats av Microsoft Azure Batch på Azure Marketplace utan behov av en anpassad avbildning.

#### <a name="vm-sizes-without-rdma"></a>VM-storlekar utan RDMA

- Förläggare `microsoft-azure-batch`
  - Styrelse `centos-container`
  - Styrelse `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>VM-storlekar med RDMA

- Förläggare `microsoft-azure-batch`
  - Styrelse `centos-container-rdma`
  - Styrelse `ubuntu-server-container-rdma`

De här avbildningarna stöds bara för användning i Azure Batch pooler och är avsedd för Docker-behållar körning. De fungerar:

- En förinstallerad Docker-kompatibel [Moby](https://github.com/moby/moby) container runtime

- Förinstallerade NVIDIA GPU-drivrutiner och körning av NVIDIA-behållare för att effektivisera distributionen på virtuella datorer i Azure N-serien

- Förinstallerad/förkonfigurerad avbildning med stöd för InfiniBand RDMA VM-storlekar för avbildningar med suffixet `-rdma` . De här avbildningarna stöder för närvarande inte SR-IOV IB/RDMA VM-storlekar.

Du kan också skapa anpassade avbildningar från virtuella datorer som kör Docker på en av de Linux-distributioner som är kompatibla med batch. Om du väljer att ange en egen anpassad Linux-avbildning kan du läsa anvisningarna i [använda en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).

För Docker-stöd för en anpassad avbildning installerar du [Docker Community Edition (CE)](https://www.docker.com/community-edition) eller [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition).

Ytterligare överväganden för att använda en anpassad Linux-avbildning:

- För att dra nytta av GPU-prestanda för Azure N-seriens storlekar när du använder en anpassad avbildning måste du förinstallera NVIDIA-drivrutiner. Du måste också installera Docker Engine-verktyget för NVIDIA GPU: er, [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker).

- Använd en RDMA-kompatibel VM-storlek för att få åtkomst till Azure RDMA-nätverket. Nödvändiga RDMA-drivrutiner installeras i CentOS HPC-och Ubuntu-avbildningar som stöds av batch. Ytterligare konfiguration kan behövas för att köra MPI-arbetsbelastningar. Se [använda RDMA-kompatibla eller GPU-aktiverade instanser i batch-poolen](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Container konfiguration för batch-pool

Om du vill aktivera en batch-pool för att köra arbets belastningar för behållare måste du ange [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) -inställningar i poolens [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) -objekt. (Den här artikeln innehåller länkar till batch .NET API-referensen. Motsvarande inställningar finns i [batch python](/python/api/overview/azure/batch) -API: et.)

Du kan skapa en container-aktiverad pool med eller utan förhämtade behållar avbildningar, som du ser i följande exempel. Hämtnings processen (eller för hämtning) gör att du kan läsa in behållar avbildningar från antingen Docker Hub eller ett annat behållar register på Internet. Använd ett [Azure Container Registry](../container-registry/container-registry-intro.md) i samma region som batch-kontot för bästa prestanda.

Fördelen med att förHämta behållar avbildningar är att när aktiviteterna först börjar köras behöver de inte vänta på att behållar avbildningen laddas ned. Behållar konfigurationen hämtar behållar avbildningar till de virtuella datorerna när poolen skapas. Aktiviteter som körs på poolen kan sedan referera till listan över behållar avbildningar och körnings alternativ för behållare.

### <a name="pool-without-prefetched-container-images"></a>Pool utan förhämtade behållar avbildningar

Om du vill konfigurera en container-aktiverad pool utan förhämtade behållar avbildningar definierar du `ContainerConfiguration` och `VirtualMachineConfiguration` objekt som visas i följande exempel. I de här exemplen används Ubuntu-servern för avbildningar av Azure Batch behållar pooler från Marketplace.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>För hämtnings avbildningar för container konfiguration

Om du vill hämta behållar avbildningar på poolen lägger du till listan över behållar avbildningar ( `container_image_names` , i python) till `ContainerConfiguration` .

I följande grundläggande python-exempel visas hur du förhämtar en standard Ubuntu behållar avbildning från [Docker Hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

Följande C#-exempel förutsätter att du vill förHämta en TensorFlow-avbildning från [Docker Hub](https://hub.docker.com). Det här exemplet innehåller en start uppgift som körs i den virtuella dator värden på poolens noder. Du kan köra en start aktivitet på värden, till exempel för att montera en fil server som kan nås från behållarna.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>FörHämta avbildningar från ett privat behållar register

Du kan också förHämta behållar avbildningar genom att autentisera till en privat behållares register Server. I följande exempel för över- `ContainerConfiguration` och-objekt för att `VirtualMachineConfiguration` Hämta en privat TensorFlow-avbildning från ett privat Azure Container Registry. Bild referensen är densamma som i föregående exempel.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Behållar inställningar för uppgiften

Om du vill köra en behållar aktivitet på en container-aktiverad pool anger du inställningar för behållare. Inställningarna omfattar de bilder som ska användas, registret och behållar körnings alternativen.

- Använd `ContainerSettings` egenskapen för uppgifts klasserna för att konfigurera inställningar för behållare. De här inställningarna definieras av klassen [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) . Observera att `--rm` alternativet container inte kräver ytterligare ett `--runtime` alternativ eftersom batchen tar hand om det.

- Om du kör uppgifter i behållar avbildningar måste du ange behållar inställningar i [moln aktiviteten](/dotnet/api/microsoft.azure.batch.cloudtask) och [jobb hanterarens uppgift](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) . Men aktiviteten [Starta uppgift](/dotnet/api/microsoft.azure.batch.starttask), [jobb förberedelse](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)och [jobb publicering](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) kräver inte behållar inställningar (det vill säga de kan köras i en behållar kontext eller direkt på noden).

- För Windows måste uppgifter köras med [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) inställt på `admin` . 

- För Linux mappar gruppen användare/grupp behörighet till behållaren. Om åtkomst till en mapp i behållaren kräver administratörs behörighet kan du behöva köra aktiviteten som pool-scope med administratörs utöknings nivå. Detta säkerställer att batch kör aktiviteten som rot i behållar kontexten. Annars kanske inte en användare som inte är administratör har åtkomst till dessa mappar.

- För behållar pooler med GPU-aktiverad maskin vara aktiverar batch automatiskt GPU för container aktiviteter, så du bör inte inkludera `–gpus` argumentet.

### <a name="container-task-command-line"></a>Kommando rad för container aktivitet

När du kör en behållar aktivitet använder batch automatiskt kommandot [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) för att skapa en behållare med hjälp av den avbildning som anges i uppgiften. Batch styr sedan uppgifts körningen i behållaren.

Som med icke-containerns batch-uppgifter anger du en kommando rad för en behållar aktivitet. Eftersom batch automatiskt skapar behållaren, anger kommando raden bara det kommando eller de kommandon som ska köras i behållaren.

Om behållar avbildningen för en batch-aktivitet har kon figurer ATS med ett [Start](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) skript, kan du ange att kommando raden ska använda standard-EntryPoint eller åsidosätta den:

- Om du vill använda behållar avbildningens standard-startpunkt, ställer du in aktivitetens kommando rad på den tomma strängen `""` .

- Om du vill åsidosätta standard start punkten, eller om avbildningen inte har en start punkt, anger du en kommando rad som passar för behållaren, till exempel `/app/myapp` eller `/bin/sh -c python myscript.py` .

Valfria [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) är ytterligare argument som du anger för `docker create` kommandot som batch använder för att skapa och köra behållaren. Om du till exempel vill ange en arbets katalog för behållaren ställer du in `--workdir <directory>` alternativet. Se [Docker Create](https://docs.docker.com/engine/reference/commandline/create/) -referens för ytterligare alternativ.

### <a name="container-task-working-directory"></a>Arbets katalog för container uppgift

En batch container-aktivitet körs i en arbets katalog i den behållare som är mycket lik katalog batchen konfigurerar för en vanlig (icke container) aktivitet. Observera att den här arbets katalogen skiljer sig från [WORKDIR](https://docs.docker.com/engine/reference/builder/#workdir) om den kon figurer ATS i avbildningen eller standard arbets katalogen för behållaren ( `C:\`  på en Windows-behållare eller `/` på en Linux-behållare).

För en batch container-aktivitet:

- Alla kataloger rekursivt under `AZ_BATCH_NODE_ROOT_DIR` på noden värd (roten i Azure Batch kataloger) mappas till behållaren
- Alla aktivitets miljö variabler mappas till behållaren
- Aktivitets arbets katalogen `AZ_BATCH_TASK_WORKING_DIR` på noden anges på samma sätt som för en vanlig uppgift och mappas till behållaren.

Med dessa mappningar kan du arbeta med behållar aktiviteter på ungefär samma sätt som icke-container aktiviteter. Du kan till exempel installera program med hjälp av programpaket, komma åt resursfiler från Azure Storage, använda aktivitets miljö inställningar och spara Uppgiftsutdata när behållaren stoppas.

### <a name="troubleshoot-container-tasks"></a>Felsöka behållar aktiviteter

Om behållar aktiviteten inte körs som förväntat, kan du behöva hämta information om WORKDIR eller ENTRYPOINT-konfigurationen för behållar avbildningen. Om du vill se konfigurationen kör du kommandot [Docker avbildnings inspektion](https://docs.docker.com/engine/reference/commandline/image_inspect/) .

Vid behov kan du justera inställningarna för container uppgiften baserat på avbildningen:

- Ange en absolut sökväg i aktivitetens kommando rad. Om avbildningens standard-ENTRYPOINT används för aktivitetens kommando rad, se till att en absolut sökväg är inställd.
- I aktivitetens körnings alternativ för behållare ändrar du arbets katalogen så att den matchar WORKDIR i avbildningen. Ange till exempel `--workdir /app` .

## <a name="container-task-examples"></a>Exempel på container aktiviteter

Följande python-kodfragment visar en grundläggande kommando rad som körs i en behållare som skapats från en fiktiv avbildning som hämtas från Docker Hub. Här `--rm` tar alternativet container bort behållaren när aktiviteten har slutförts och `--workdir` alternativet anger en arbets katalog. Kommando raden åsidosätter containerns ENTRYPOINT med ett enkelt Shell-kommando som skriver en liten fil till arbets katalogen för aktiviteten på värden.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

I följande C#-exempel visas grundläggande behållar inställningar för en moln aktivitet:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Nästa steg

- För enkel distribution av behållar arbets belastningar på Azure Batch genom [Shipyard-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes), se [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit.
- Information om hur du installerar och använder Docker CE på Linux finns i [Docker](https://docs.docker.com/engine/installation/) -dokumentationen.
- Lär dig hur du [använder en hanterad anpassad avbildning för att skapa en pool med virtuella datorer](batch-custom-images.md).
- Lär dig mer om [Moby-projektet](https://mobyproject.org/), ett ramverk för att skapa behållarebaserade system.
