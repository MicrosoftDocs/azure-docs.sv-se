---
title: Skapa och hantera en virtuell Windows-dator i Azure med hjälp av python
description: Lär dig hur du använder python för att skapa och hantera en virtuell Windows-dator i Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: d99f9326b3113742fdc0c14a4ae39d552df47edd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730164"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Skapa och hantera virtuella Windows-datorer i Azure med hjälp av python

En [virtuell Azure-dator](overview.md) (VM) behöver flera stöd för Azure-resurser. Den här artikeln beskriver hur du skapar, hanterar och tar bort VM-resurser med python. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Visual Studio-projekt
> * Installera paket
> * Skapa autentiseringsuppgifter
> * Skapa resurser
> * Utföra hanterings uppgifter
> * Ta bort resurser
> * Kör programmet

Det tar ungefär 20 minuter att utföra dessa steg.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

1. Om du inte redan har gjort det installerar du [Visual Studio](/visualstudio/install/install-visual-studio). Välj **python-utveckling** på sidan arbets belastningar och klicka sedan på **Installera**. I sammanfattningen kan du se att **Python 3 64-bitar (3.6.0)** automatiskt väljs åt dig. Om du redan har installerat Visual Studio kan du lägga till python-arbetsbelastningen med Visual Studio Launcher.
2. När du har installerat och startat Visual Studio klickar du på **fil**  >  **nytt**  >  **projekt**.
3. Klicka på **mallar**  >  **python**  >  **python Application**, ange *myPythonProject* som namn på projektet, Välj platsen för projektet och klicka sedan på **OK**.

## <a name="install-packages"></a>Installera paket

1. I Solution Explorer, under *myPythonProject*, högerklickar du på **python-miljöer** och väljer sedan **Lägg till virtuell miljö**.
2. På skärmen Lägg till virtuell miljö godkänner du standard namnet på *Kuvert*, kontrollerar att *Python 3,6 (64-bit)* har valts för bas tolken och klickar sedan på **skapa**.
3. Högerklicka *på den miljö* miljö som du har skapat, klicka på **Installera python-paket**, ange *Azure* i rutan Sök och tryck sedan på RETUR.

Du bör se i utdata-Fönstren att Azure-paketen har installerats. 

## <a name="create-credentials"></a>Skapa autentiseringsuppgifter

Innan du startar det här steget ska du kontrol lera att du har ett [Active Directory tjänstens huvud namn](../../active-directory/develop/howto-create-service-principal-portal.md). Du bör också registrera program-ID, autentiseringsnyckel och klient-ID som du behöver i ett senare steg.

1. Öppna *myPythonProject.py* -filen som skapades och Lägg sedan till den här koden så att programmet kan köras:

    ```python
    if __name__ == "__main__":
    ```

2. Om du vill importera koden som behövs lägger du till dessa uttryck överst i filen. py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Lägg till variabler efter import-instruktionerna i. py-filen för att ange vanliga värden som används i koden:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Ersätt **prenumerations-ID** med ditt prenumerations-ID.

4. Om du vill skapa de Active Directory autentiseringsuppgifter som du behöver för att göra begär Anden lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Ersätt **program-ID**, **autentiserings nyckel** och **klient-ID** med de värden som du tidigare har samlat in när du skapade ditt Azure Active Directory tjänst huvud namn.

5. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Skapa resurser
 
### <a name="initialize-management-clients"></a>Initiera hanterings klienter

Hanterings klienter behövs för att skapa och hantera resurser med hjälp av python SDK i Azure. Om du vill skapa hanterings klienter lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Skapa den virtuella datorn och de stödda resurserna

Alla resurser måste finnas i en [resurs grupp](../../azure-resource-manager/management/overview.md).

1. Lägg till den här funktionen efter variablerna i. py-filen om du vill skapa en resurs grupp:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Tillgänglighets uppsättningar](tutorial-availability-sets.md) gör det enklare för dig att underhålla de virtuella datorer som används av ditt program.

1. Om du vill skapa en tillgänglighets uppsättning lägger du till den här funktionen efter variablerna i. py-filen:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

En [offentlig IP-adress](../../virtual-network/public-ip-addresses.md) krävs för att kommunicera med den virtuella datorn.

1. Om du vill skapa en offentlig IP-adress för den virtuella datorn lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

En virtuell dator måste finnas i ett undernät för ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md).

1. Om du vill skapa ett virtuellt nätverk lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Lägg till den här funktionen efter variablerna i. py-filen om du vill lägga till ett undernät i det virtuella nätverket:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

En virtuell dator behöver ett nätverks gränssnitt för att kommunicera med det virtuella nätverket.

1. Lägg till den här funktionen efter variablerna i. py-filen om du vill skapa ett nätverks gränssnitt:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Nu när du har skapat alla stöd resurser kan du skapa en virtuell dator.

1. Lägg till den här funktionen efter variablerna i. py-filen om du vill skapa den virtuella datorn:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Den här självstudien skapar en virtuell dator som kör en version av operativ systemet Windows Server. Mer information om hur du väljer andra bilder finns i [navigera och välja avbildningar av virtuella Azure-datorer med Windows PowerShell och Azure CLI](../linux/cli-ps-findimage.md).
    > 
    > 

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Utföra hanterings uppgifter

Under livscykeln för en virtuell dator kan du vilja köra administrativa uppgifter, genom att till exempel starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa kod för att automatisera repetitiva eller komplexa uppgifter.

### <a name="get-information-about-the-vm"></a>Hämta information om den virtuella datorn

1. Om du vill hämta information om den virtuella datorn lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Stoppa den virtuella datorn

Du kan stoppa en virtuell dator och behålla alla inställningar, men fortsätta att debiteras för den, eller så kan du stoppa en virtuell dator och frigöra den. När en virtuell dator frigörs frigörs även alla resurser som är kopplade till den och faktureringen upphör.

1. Om du vill stoppa den virtuella datorn utan att ta bort tilldelningen lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Om du vill frigöra den virtuella datorn ändrar du power_off anrop till den här koden:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Starta den virtuella datorn

1. För att starta den virtuella datorn lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Ändra storlek på den virtuella datorn

Många aspekter av distributionen bör övervägas när du bestämmer dig för en storlek på den virtuella datorn. Mer information finns i [VM-storlekar](../sizes.md).

1. Om du vill ändra storleken på den virtuella datorn lägger du till den här funktionen efter variablerna i. py-filen:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Lägga till en datadisk i en virtuell dator

Virtuella datorer kan ha en eller flera [data diskar](../managed-disks-overview.md) som lagras som virtuella hård diskar.

1. Lägg till den här funktionen efter variablerna i. py-filen om du vill lägga till en datadisk till den virtuella datorn: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Ta bort resurser

Eftersom du debiteras för resurser som används i Azure är det alltid en bra idé att ta bort resurser som inte längre behövs. Om du vill ta bort de virtuella datorerna och alla stödda resurser måste du ta bort resurs gruppen.

1. Om du vill ta bort resurs gruppen och alla resurser lägger du till den här funktionen efter variablerna i. py-filen:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Om du vill anropa funktionen som du tidigare lade till lägger du till den här koden under instruktionen **IF** i slutet av. py-filen:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Spara *myPythonProject.py*.

## <a name="run-the-application"></a>Kör programmet

1. Kör konsol programmet genom att klicka på **Start** i Visual Studio.

2. Tryck på **RETUR** när statusen för varje resurs returneras. I statusinformation bör du se att det finns ett **slutfört** etablerings tillstånd. När du har skapat den virtuella datorn har du möjlighet att ta bort alla resurser som du skapar. Innan du trycker på **RETUR** för att börja ta bort resurser kan det ta några minuter innan du verifierar att de skapats i Azure Portal. Om du har Azure Portal öppen kan du behöva uppdatera bladet för att se nya resurser.  

    Det bör ta ungefär fem minuter för konsol programmet att köras helt från början till slut. Det kan ta flera minuter innan programmet har avslut ATS innan alla resurser och resurs gruppen har tagits bort.


## <a name="next-steps"></a>Nästa steg

- Om det var problem med distributionen, är nästa steg att titta på [Felsöka resursgruppdistribueringar med Azure-portalen](../../azure-resource-manager/templates/deployment-history.md)
- Läs mer om [Azure python-biblioteket](/python/api/overview/azure/)
