---
title: Använd den Service Management-API (python) – funktions guide
description: Lär dig hur du program mässigt utför vanliga Service Management-uppgifter från python.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 02993f2b79e37e5e50c20c4ee07220bcbd36edb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741408"
---
# <a name="use-service-management-from-python"></a>Använda Service Management från python

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här guiden visar hur du program mässigt utför vanliga Service Management-uppgifter från python. **Klassen** -klassen i [Azure SDK för python](https://github.com/Azure/azure-sdk-for-python) stöder programmatisk åtkomst till många av de tjänst hanterings funktioner som finns tillgängliga i [Azure Portal][management-portal]. Du kan använda den här funktionen för att skapa, uppdatera och ta bort moln tjänster, distributioner, data hanterings tjänster och virtuella datorer. Den här funktionen kan vara användbar när du skapar program som behöver programmerings åtkomst till tjänst hantering.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Vad är Service Management?
Azure-Service Management-API ger programmatisk åtkomst till många av de tjänst hanterings funktioner som är tillgängliga via [Azure Portal][management-portal]. Du kan använda Azure SDK för python för att hantera dina moln tjänster och lagrings konton.

Om du vill använda Service Management-API måste du [skapa ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Begrepp
Azure SDK för python radbryter [Service Management-API][svc-mgmt-rest-api], vilket är ett REST API. Alla API-åtgärder utförs via TLS och autentiseras ömsesidigt genom att använda X. 509 v3-certifikat. Hanterings tjänsten kan nås inifrån en tjänst som körs i Azure. Det kan också nås direkt via Internet från alla program som kan skicka en HTTPS-begäran och få ett HTTPS-svar.

## <a name="installation"></a><a name="Installation"> </a>Installation
Alla funktioner som beskrivs i den här artikeln är tillgängliga i `azure-servicemanagement-legacy` paketet, som du kan installera med hjälp av pip. Mer information om installation (till exempel om du inte har arbetat med python) finns i [Installera python och Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Ansluta till Service Management
Om du vill ansluta till Service Management-slutpunkten behöver du ditt Azure-prenumerations-ID och ett giltigt hanterings certifikat. Du kan hämta ditt prenumerations-ID via [Azure Portal][management-portal].

> [!NOTE]
> Nu kan du använda certifikat som skapats med OpenSSL när de körs i Windows. Python-2.7.4 eller senare krävs. Vi rekommenderar att du använder OpenSSL i stället för. pfx, eftersom stöd för. PFX-certifikat förmodligen kommer att tas bort i framtiden.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Hanterings certifikat på Windows/Mac/Linux (OpenSSL)
Du kan använda [openssl](https://www.openssl.org/) för att skapa ett hanterings certifikat. Du måste skapa två certifikat, ett för servern (en `.cer` fil) och ett för klienten (en `.pem` fil). Skapa `.pem` filen genom att köra:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Skapa `.cer` certifikatet genom att köra:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Mer information om Azure-certifikat finns i [Översikt över certifikat för azure Cloud Services](cloud-services-certs-create.md). En fullständig beskrivning av OpenSSL-parametrarna finns i dokumentationen på [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

När du har skapat filerna laddar du upp `.cer` filen till Azure. På fliken **Inställningar** i [Azure Portal][management-portal]väljer du **Ladda upp**. Observera var du sparade `.pem` filen.

När du har fått ditt prenumerations-ID, skapar du ett certifikat och överför `.cer` filen till Azure ansluter du till slut punkten för Azure-hantering. Anslut genom att skicka prenumerations-ID: t och sökvägen till `.pem` filen till **klassen**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

I föregående exempel `sms` är ett **klassen** -objekt. **Klassen** -klassen är den primära klassen som används för att hantera Azure-tjänster.

### <a name="management-certificates-on-windows-makecert"></a>Hanterings certifikat för Windows (MakeCert)
Du kan skapa ett självsignerat hanterings certifikat på datorn med hjälp av `makecert.exe` . Öppna en **Visual Studio** -kommandotolk som **administratör** och Använd följande kommando och Ersätt *AzureCertificate* med det certifikat namn som du vill använda:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

Kommandot skapar `.cer` filen och installerar den i det **personliga** certifikat arkivet. Mer information finns i [Översikt över certifikat för Azure Cloud Services](cloud-services-certs-create.md).

När du har skapat certifikatet laddar du upp `.cer` filen till Azure. På fliken **Inställningar** i [Azure Portal][management-portal]väljer du **Ladda upp**.

När du har fått ditt prenumerations-ID, skapar du ett certifikat och överför `.cer` filen till Azure ansluter du till slut punkten för Azure-hantering. Anslut genom att skicka prenumerations-ID: t och platsen för certifikatet i ditt **personliga** certifikat arkiv till **klassen** (återigen ersätter du *AzureCertificate* med namnet på ditt certifikat).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

I föregående exempel `sms` är ett **klassen** -objekt. **Klassen** -klassen är den primära klassen som används för att hantera Azure-tjänster.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Lista tillgängliga platser
Om du vill visa en lista över de platser som är tillgängliga för värd tjänster använder du metoden **list \_ platser** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

När du skapar en moln tjänst eller lagrings tjänst måste du ange en giltig plats. Metoden för **list \_ platser** returnerar alltid en uppdaterad lista över tillgängliga platser. När detta skrivs är de tillgängliga platserna:

* Europa, västra
* Norra Europa
* Sydostasien
* Asien, östra
* Central US
* USA, norra centrala
* USA, södra centrala
* USA, västra
* East US
* Japan, östra
* Japan, västra
* Brasilien, södra
* Australien, östra
* Australien, sydöstra

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Skapa en moln tjänst
När du skapar ett program och kör det i Azure kallas koden och konfigurationen tillsammans en Azure [-moln tjänst][cloud service]. (Den kallades för en *värdbaserad tjänst* i tidigare versioner av Azure.) Du kan använda metoden **skapa \_ värdbaserad \_ tjänst** för att skapa en ny värdbaserad tjänst. Skapa tjänsten genom att tillhandahålla ett värdbaserat tjänst namn (som måste vara unikt i Azure), en etikett (kodas automatiskt till base64), en beskrivning och en plats.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Du kan visa en lista över alla värdbaserade tjänster för din prenumeration med metoden **list \_ Hosted \_ Services** .

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Om du vill hämta information om en viss värdbaserad tjänst skickar du den värdbaserade tjänstens namn till metoden **get \_ Hosted \_ service \_ Properties** .

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

När du har skapat en moln tjänst distribuerar du din kod till tjänsten med metoden för att **skapa \_ distribution** .

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Ta bort en moln tjänst
Du kan ta bort en moln tjänst genom att skicka tjänst namnet till metoden **Delete \_ Hosted \_ service** .

```python
sms.delete_hosted_service('myhostedservice')
```

Innan du kan ta bort en tjänst måste alla distributioner för tjänsten först tas bort. Mer information finns i [ta bort en distribution](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Ta bort en distribution
Om du vill ta bort en distribution använder du metoden **ta bort \_ distribution** . I följande exempel visas hur du tar bort en distribution med namnet `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Skapa en lagrings tjänst
Med en [lagrings tjänst](../storage/common/storage-account-create.md) får du till gång till Azure- [blobbar](../storage/blobs/storage-quickstart-blobs-python.md), [tabeller](../cosmos-db/table-storage-how-to-use-python.md)och [köer](../storage/queues/storage-python-how-to-use-queue-storage.md). Om du vill skapa en lagrings tjänst behöver du ett namn för tjänsten (mellan 3 och 24 gemena tecken och unik i Azure). Du behöver också en beskrivning, en etikett (upp till 100 tecken, kodas automatiskt till base64) och en plats. I följande exempel visas hur du skapar en lagrings tjänst genom att ange en plats:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

I föregående exempel kan du hämta status för åtgärden **skapa \_ lagrings \_ konto** genom att skicka det resultat som returnerades av **skapa \_ lagrings \_ konto** till metoden **Hämta \_ Åtgärds \_ status** . 

Du kan lista dina lagrings konton och deras egenskaper med metoden **list \_ Storage \_ Accounts** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Ta bort en lagrings tjänst
Om du vill ta bort en lagrings tjänst skickar du namnet på lagrings tjänsten till metoden **ta bort \_ lagrings \_ konto** . Om du tar bort en lagrings tjänst tas alla data som lagras i tjänsten (blobbar, tabeller och köer) bort.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Visa lista över tillgängliga operativ system
Om du vill visa en lista över de operativ system som är tillgängliga för värd tjänster använder du metoden **list \_ operativ \_ system** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Du kan också använda metoden **list \_ Opera ting \_ system \_ familjer** , som grupper operativ systemen efter familj.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Skapa en operativ system avbildning
Om du vill lägga till en operativ system avbildning till avbildnings lagrings platsen använder du metoden **Lägg till \_ operativ system \_ avbildning** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Om du vill visa en lista över de operativ Systems avbildningar som är tillgängliga använder du metoden **list \_ OS- \_ avbildningar** . Den innehåller alla plattforms avbildningar och användar avbildningar.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Ta bort en operativ system avbildning
Om du vill ta bort en användar avbildning använder du metoden **ta bort \_ operativ system \_ avbildning** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Skapa en virtuell dator
Om du vill skapa en virtuell dator måste du först skapa en [moln tjänst](#CreateCloudService). Skapa sedan distributionen av den virtuella datorn med hjälp **av \_ \_ \_ distributions metoden Skapa virtuell dator** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Ta bort en virtuell dator
Om du vill ta bort en virtuell dator tar du först bort distributionen med hjälp av metoden **ta bort \_ distribution** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

Moln tjänsten kan sedan tas bort med hjälp av metoden **ta bort \_ värdbaserad \_ tjänst** .

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Skapa en virtuell dator från en avbildning av en avbildning av en avbildning
För att kunna avbilda en avbildning av en virtuell dator anropar du först **\_ \_ avbildnings metoden avbilda VM** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Kontrol lera att du har hämtat avbildningen genom att använda API: et **lista \_ VM- \_ avbildningar** . Se till att bilden visas i resultaten.

```python
images = sms.list_vm_images()
```

Om du slutligen vill skapa den virtuella datorn med hjälp av avbildningen använder **du \_ \_ \_ distributions metoden Skapa virtuell dator** som tidigare, men den här gången går i vm_image_name i stället.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Mer information om hur du avbildar en virtuell Linux-dator i den klassiska distributions modellen finns i [avbilda en virtuell Linux-dator](/previous-versions/azure/virtual-machines/linux/classic/capture-image-classic).

Mer information om hur du avbildar en virtuell Windows-dator i den klassiska distributions modellen finns i [avbilda en virtuell Windows-dator](/previous-versions/azure/virtual-machines/windows/classic/capture-image-classic).

## <a name="next-steps"></a><a name="What's Next"> </a>Nästa steg
Nu när du har lärt dig grunderna i Service Management kan du komma åt den [fullständiga API-referens dokumentationen för Azure python SDK](https://azure-sdk-for-python.readthedocs.org/) och utföra komplexa uppgifter för att enkelt hantera dina python-program.

Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: /previous-versions/azure/ee460799(v=azure.100)


[cloud service]:/azure/cloud-services/