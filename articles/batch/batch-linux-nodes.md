---
title: Kör Linux på virtuella datorers datornoder
description: Lär dig hur du bearbetar parallella beräknings arbets belastningar i pooler för virtuella Linux-datorer i Azure Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98683708"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Etablera Linux Compute-noder i batch-pooler

Du kan använda Azure Batch för att köra parallella beräknings arbets belastningar på både virtuella Linux-och Windows-datorer. Den här artikeln beskriver hur du skapar pooler för Linux Compute-noder i batch-tjänsten med hjälp av både [batchen python](https://pypi.python.org/pypi/azure-batch) och [batch .net](/dotnet/api/microsoft.azure.batch) -klient bibliotek. 

## <a name="virtual-machine-configuration"></a>Konfiguration av virtuell dator

När du skapar en pool med datornoder i batch har du två alternativ för att välja Node-storlek och operativ system: Cloud Services konfiguration och konfiguration av virtuell dator. [Konfigurations pooler för virtuella datorer](nodes-and-pools.md#virtual-machine-configuration) består av virtuella Azure-datorer, som kan skapas från antingen Linux-eller Windows-avbildningar. När du skapar en pool med konfiguration av virtuell dator anger du en [tillgänglig storlek](../virtual-machines/sizes.md)på en Compute-nod, avbildnings referensen för den virtuella datorn som ska installeras på noderna och batch Node agent-SKU: n (ett program som körs på varje nod och ger ett gränssnitt mellan noden och batch-tjänsten).

### <a name="virtual-machine-image-reference"></a>Referens för avbildning av virtuell dator

Batch-tjänsten använder [skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/overview.md) för att tillhandahålla Compute-noder i konfigurationen för den virtuella datorn. Du kan ange en avbildning från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)eller [använda galleriet för delad avbildning för att förbereda en anpassad avbildning](batch-sig-images.md).

När du skapar en avbildnings referens för en virtuell dator måste du ange följande egenskaper:

| **Bild referens egenskap** | **Exempel** |
| --- | --- |
| Publisher |Canonical |
| Erbjudande |UbuntuServer |
| SKU |18,04 – LTS |
| Version |senaste |

> [!TIP]
> Du kan lära dig mer om de här egenskaperna och hur du anger Marketplace-avbildningar i [hitta virtuella Linux-avbildningar på Azure Marketplace med Azure CLI](../virtual-machines/linux/cli-ps-findimage.md). Observera att vissa Marketplace-avbildningar inte är kompatibla med batch.

### <a name="list-of-virtual-machine-images"></a>Lista över avbildningar av virtuella datorer

Alla Marketplace-avbildningar är inte kompatibla med de för närvarande tillgängliga batch Node-agenterna. Om du vill visa en lista över virtuella Marketplace-avbildningar som stöds för batch-tjänsten och deras motsvarande Node agent-SKU: er, använder [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (batch .net) eller motsvarande API i ett annat språk-SDK.

### <a name="node-agent-sku"></a>Node agent-SKU

[Batch Node agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) är ett program som körs på varje nod i poolen och tillhandahåller kommando-och-Control-gränssnittet mellan noden och batch-tjänsten. Det finns olika implementeringar av Node-agenten, som kallas SKU: er, för olika operativ system. När du skapar en konfiguration för virtuell dator är det i princip att du först ange avbildnings referensen för den virtuella datorn och sedan ange vilken Node-agent som ska installeras på avbildningen. Normalt är varje nod-agent-SKU kompatibel med flera avbildningar av virtuella datorer. Här följer några exempel på Node agent-SKU: er:

- batch. Node. Ubuntu 18,04
- batch. Node. CentOS 7
- batch. Node. Windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Skapa en Linux-pool: batch python

Följande kodfragment visar ett exempel på hur du använder [Microsoft Azure Batch klient bibliotek för python](https://pypi.python.org/pypi/azure-batch) för att skapa en pool med Ubuntu-serverns beräknings noder. Mer information om batch python-modulen finns i [referens dokumentationen](/python/api/overview/azure/batch).

Det här kodfragmentet skapar en [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explicit och anger var och en av dess egenskaper (utgivare, erbjudande, SKU, version). I produktions kod rekommenderar vi dock att du använder metoden [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) för att välja bland kombinationerna tillgängliga avbildning och Node agent-SKU vid körning.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Som tidigare nämnts rekommenderar vi att du använder [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) -metoden för att dynamiskt välja bland avbildnings kombinationerna för just nu stödED Node agent/Marketplace (i stället för att skapa en [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) explicit). Följande python-kodfragment visar hur du använder den här metoden.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Skapa en Linux-pool: batch .NET

Följande kodfragment visar ett exempel på hur du använder [batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -klient biblioteket för att skapa en pool med Ubuntu-serverns Compute-noder. Läs [referens dokumentationen](/dotnet/api/microsoft.azure.batch)om du vill ha mer information om batch .net.

Följande kodfragment använder metoden [PoolOperations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) för att välja från en lista över aktuella Marketplace-avbildningar och SKU-kombinationer för Node agent. Den här tekniken rekommenderas, eftersom listan över kombinationer som stöds kan ändras från tid till tid. Oftast läggs de kombinationer som stöds till.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Även om det tidigare kodfragmentet använder metoden [PoolOperations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) för att dynamiskt lista och välja från avbildningar som stöds och välj bland SKU-kombinationer som stöds (rekommenderas), kan du också konfigurera en [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) explicit:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Ansluta till Linux-noder med SSH

Under utvecklingen eller under fel sökningen kanske du måste logga in på noderna i poolen. Till skillnad från Windows Compute-noder kan du inte använda Remote Desktop Protocol (RDP) för att ansluta till Linux-noder. I stället möjliggör batch-tjänsten SSH-åtkomst på varje nod för fjärr anslutning.

Följande python-kodfragment skapar en användare på varje nod i en pool, vilket krävs för fjärr anslutning. Den skriver sedan ut SSH-anslutningen (Secure Shell) för varje nod.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Den här koden kommer att ha utdata som liknar följande exempel. I det här fallet innehåller poolen fyra Linux-noder.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

I stället för ett lösen ord kan du ange en offentlig SSH-nyckel när du skapar en användare på en nod. Använd **ssh_public_key** -parametern på [COMPUTENODEUSER](/python/api/azure-batch/azure.batch.models.computenodeuser)i python SDK. Använd egenskapen [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) i .net.

## <a name="pricing"></a>Priser

Azure Batch bygger på Azure Cloud Services-och Azure Virtual Machines-teknik. Själva batch-tjänsten erbjuds utan kostnad, vilket innebär att du bara debiteras för beräknings resurserna (och tillhör ande kostnader som ingår) som dina batch-lösningar använder. När du väljer **konfiguration för virtuell dator** debiteras du baserat på [Virtual Machines pris](https://azure.microsoft.com/pricing/details/virtual-machines/) strukturen.

Om du distribuerar program till dina batch-noder med hjälp av [programpaket](batch-application-packages.md), debiteras du också för de Azure Storage-resurser som dina program paket använder.

## <a name="next-steps"></a>Nästa steg

- Utforska [python-kod exemplen](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) i [Azure-Batch-samples GitHub-lagringsplatsen](https://github.com/Azure/azure-batch-samples) för att se hur du utför vanliga batch-åtgärder, t. ex. pool, jobb och aktivitets skapande. [Readme-filen](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) som medföljer python-exemplen innehåller information om hur du installerar de nödvändiga paketen.
- Lär dig mer om att använda [virtuella datorer med låg prioritet](batch-low-pri-vms.md) med batch.
