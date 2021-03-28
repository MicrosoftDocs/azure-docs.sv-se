---
title: Distribuera virtuella datorer på Azure Stack Edge Pro-enhetens GPU via Azure CLI och python
description: Beskriver hur du skapar och hanterar virtuella datorer (VM) på en Azure Stack Edge Pro GPU-enhet med hjälp av Azure CLI och python.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: 38ba5679731b5e52eb06751a625aea9df69c76ca
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643219"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Distribuera virtuella datorer på Azure Stack Edge Pro GPU-enhet med hjälp av Azure CLI och python

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

I den här självstudien beskrivs hur du skapar och hanterar en virtuell dator på din Azure Stack Edge Pro-enhet med Azure Command Line Interface (CLI) och python.

## <a name="vm-deployment-workflow"></a>Arbets flöde för distribution av virtuell dator

Arbets flödet för distributionen illustreras i följande diagram.

![Arbets flöde för distribution av virtuell dator](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Översikten över distributions arbets flödet på hög nivå är följande:

1. Anslut till Azure Resource Manager
2. Skapa en resursgrupp
3. Skapa ett lagringskonto
4. Lägg till BLOB-URI i hosts-filen
5. Installera certifikat
6. Ladda upp en virtuell hårddisk
7. Skapa hanterade diskar från den virtuella hård disken
8. Skapa en avbildning av en virtuell dator från den hanterade avbildnings disken
9. Skapa en virtuell dator med tidigare skapade resurser
10. Skapa ett virtuellt nätverk
11. Skapa en VNIC med VNet-undernätets ID

En detaljerad förklaring av arbets flödes diagrammet finns i [distribuera virtuella datorer på din Azure Stack Edge Pro-enhet med Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md). Information om hur du ansluter till Azure Resource Manager finns i [ansluta till Azure Resource Manager med Azure PowerShell](azure-stack-edge-gpu-connect-resource-manager.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar skapa och hantera en virtuell dator på din Azure Stack Edge Pro-enhet med hjälp av Azure CLI och python måste du kontrol lera att du har slutfört de krav som anges i följande steg:

1. Du har slutfört nätverks inställningarna på din Azure Stack Edge Pro-enhet enligt beskrivningen i [steg 1: konfigurera Azure Stack Edge Pro-enhet](azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Aktiverat ett nätverks gränssnitt för beräkning. Nätverks gränssnittets IP-adress används för att skapa en virtuell växel för VM-distributionen. Följande steg vägleder dig genom processen:

    1. Gå till **Compute**. Välj det nätverks gränssnitt som du ska använda för att skapa en virtuell växel.

        > [!IMPORTANT] 
        > Du kan bara konfigurera en port för beräkning.

    2. Aktivera beräkning i nätverks gränssnittet. Azure Stack Edge Pro skapar och hanterar en virtuell växel som motsvarar det nätverks gränssnittet.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Du har skapat och installerat alla certifikat på din Azure Stack Edge Pro-enhet och i det betrodda arkivet för din klient. Följ proceduren som beskrivs i [steg 2: skapa och installera certifikat](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates).

4. Du har skapat ett Base-64-kodat *. cer* -certifikat (PEM format) för din Azure Stack Edge Pro-enhet. Certifikatet laddas redan upp som en signerings kedja på enheten och installeras i det betrodda rot arkivet på klienten. Det här certifikatet krävs också i *PEM* -format för att python ska fungera på den här klienten.

    Konvertera det här certifikatet till `pem` format med hjälp av `certutil` kommandot. Du måste köra det här kommandot i katalogen som innehåller ditt certifikat.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Följande exempel på kommando användning visas:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Du kommer även att lägga till detta `pem` i python-butiken senare.

5. Du har tilldelat enhetens IP-adress på sidan **nätverk** i det lokala webb gränssnittet för enheten. Lägg till följande IP-adress i:

    - Värd filen på klienten eller,
    - DNS-serverkonfigurationen
    
    > [!IMPORTANT]
    > Vi rekommenderar att du ändrar DNS-serverkonfigurationen för slut punkts namn matchning.

    1. Starta **anteckningar** som administratör (administratörs behörighet krävs för att spara filen) och öppna sedan **hosts** -filen som finns på `C:\Windows\System32\Drivers\etc` .
    
        ![Windows Explorer Hosts-fil](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)
    
    2. Lägg till följande poster i **hosts** -filen och Ersätt med lämpliga värden för din enhet:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Använd följande bild för referens. Spara **värdens** fil.

        ![Hosts-fil i anteckningar](media/azure-stack-edge-gpu-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Hämta python-skriptet](https://aka.ms/ase-vm-python) som används i den här proceduren.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Steg 1: Konfigurera Azure CLI/python på klienten

### <a name="verify-profile-and-install-azure-cli"></a>Verifiera profil och installera Azure CLI

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908&preserve-view=true#azure-resource-manager-api-profiles).-->

1. Installera Azure CLI på klienten. I det här exemplet har Azure CLI-2.0.80 installerats. Kör kommandot för att kontrol lera versionen av Azure CLI `az --version` .

    Följande är exempel på utdata från ovanstående kommando:

    ```output
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Om du inte har Azure CLI laddar du ned och [installerar Azure CLI i Windows](/cli/azure/install-azure-cli-windows). Du kan köra Azure CLI med kommando tolken i Windows eller via Windows PowerShell.

2. Anteckna CLI: s python-plats. Du behöver python-platsen för att fastställa platsen för betrodda rot certifikat Arkiv för Azure CLI.

3. Om du vill köra det exempel skript som används i den här artikeln behöver du följande python-biblioteks versioner:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Kör följande kommando för att installera versionerna:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    Följande exempel på utdata visar installationen av Haikunator:

    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    Följande exempel på utdata visar installationen av pip för `msrestazure` : 
    
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Lita på Azure Stack Edge Pro CA rot certifikat

1. Hitta certifikat platsen på din dator. Platsen kan variera beroende på var du har installerat `az cli` . Kör Windows PowerShell som administratör. Växla till sökvägen där `az cli` installerat python: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    Ange följande kommando för att hämta certifikat platsen:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    Cmdleten returnerar certifikat platsen som visas nedan:  
        
    ```output
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Anteckna den här platsen när du kommer att använda den senare – `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Lita på Azure Stack Edge Pro CA rot certifikat genom att lägga till det i det befintliga python-certifikatet. Du kommer att ange sökvägen till den plats där du sparade PEM-certifikatet tidigare.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    En exempel Sök väg är "C:\VM-scripts\rootteam3device.pem"
    
    Skriv sedan följande serie kommandon i Windows PowerShell:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Anslut till Azure Stack Edge Pro

1. Registrera din Azure Stack Edge Pro-miljö genom att köra `az cloud register` kommandot.

    I vissa fall dirigeras direkt utgående Internet anslutning via en proxy eller brand vägg som tillämpar SSL-avlyssning. I dessa fall kan AZ Cloud register-kommandot inte köras med ett fel, till exempel \" att det inte går att hämta slut punkter från molnet. \" Undvik det här felet genom att ange följande miljövariabler i Windows PowerShell:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Ange miljövariabler för skriptet för Azure Resource Manager slut punkt, plats där resurserna skapas och sökvägen till den plats där den virtuella käll hård disken finns. Platsen för resurserna är fast i alla Azure Stack Edge Pro-enheter och är inställd på `dbelocal` . Du måste också ange adressprefix och en privat IP-adress. Alla följande miljövariabler är värden baserade på dina värden, förutom för `AZURE_RESOURCE_LOCATION` , som ska hårdkodad till `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Registrera din miljö. Använd följande parametrar när du kör AZ Cloud register:

    | Värde | Beskrivning | Exempel |
    | --- | --- | --- |
    | Miljönamn | Namnet på den miljö som du försöker ansluta till | Ange ett namn, till exempel `aze-environ` |
    | Resource Manager-slutpunkt | URL: en är `https://Management.<appliancename><dnsdomain>` . <br> Om du vill hämta den här URL: en går du till sidan **enheter** i enhetens lokala webb gränssnitt. |Till exempel `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Följande exempel visar hur kommandot ovan används:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Ange den aktiva miljön med hjälp av följande kommandon:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Följande exempel visar hur kommandot ovan används:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Logga in på din Azure Stack Edge Pro-miljö med hjälp av `az login` kommandot. Du kan logga in på Azure Stack Edge Pro-miljö antingen som en användare eller som [tjänstens huvud namn](../active-directory/develop/app-objects-and-service-principals.md).

   Följ dessa steg om du vill logga in som en *användare*:

   Du kan antingen ange användar namn och lösen ord direkt i `az login` kommandot eller autentisera med hjälp av en webbläsare. Du måste göra det senare om ditt konto har Multi-Factor Authentication aktiverat.

   Följande exempel visar hur `az login` :
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   När du har använt inloggnings kommandot uppmanas du att ange ett lösen ord. Ange Azure Resource Manager lösen ordet.

   I följande exempel visas utdata för lyckad inloggning efter att du har angett lösen ordet:  
   
   ```output
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Anteckna `id` `tenantId` värdena och som dessa värden motsvarar ditt Azure Resource Manager PRENUMERATIONS-id och Azure Resource Manager klient-ID och kommer att användas i senare steg.
       
   Följande miljövariabler måste anges för att fungera som *tjänstens huvud namn*:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Ditt Azure Resource Manager-klient-ID är hårdkodat. Ditt Azure Resource Manager klient-ID och Azure Resource Manager prenumerations-ID finns både i utdata från `az login` kommandot som du körde tidigare. Den Azure Resource Manager klient hemligheten är det Azure Resource Manager lösen ord som du har angett.

   Mer information finns i [Azure Resource Manager Password](/azure/azure-stack-edge-gpu-set-azure-resource-manager-password).

5. Ändra profilen till version 2019-03-01 – hybrid. Om du vill ändra profil versionen kör du följande kommando:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Följande exempel visar hur `az cloud update` :

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Steg 2: skapa en virtuell dator

Du får ett Python-skript för att skapa en virtuell dator. Beroende på om du är inloggad som användare eller anger som tjänstens huvud namn, tar skriptet med i princip indata och skapar en virtuell dator.

1. Kör python-skriptet från samma katalog där python är installerat.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. När skriptet körs tar det upp till 20-30 minuter att ladda upp den virtuella hård disken. Om du vill visa förloppet för uppladdnings åtgärden kan du använda Azure Storage Explorer eller AzCopy.

    Här är ett exempel på utdata från en lyckad körning av skriptet. Skriptet skapar alla resurser i en resurs grupp, använder resurserna för att skapa en virtuell dator och tar slutligen bort resurs gruppen inklusive alla resurser den har skapat.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Nästa steg

[Vanliga AZ CLI-kommandon för virtuella Linux-datorer](../virtual-machines/linux/cli-manage.md)
