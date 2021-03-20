---
title: Azure Cloud Shell snabb start – PowerShell
description: Lär dig hur du använder PowerShell i din webbläsare med Azure Cloud Shell.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: d4a7f1453ec686cfa16d260101ba81f429ce1da0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89469464"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Snabb start för PowerShell i Azure Cloud Shell

Det här dokumentet beskriver hur du använder PowerShell i Cloud Shell i [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> En [bash i Azure Cloud Shell](quickstart.md) snabb start är också tillgänglig.

## <a name="start-cloud-shell"></a>Starta Cloud Shell

1. Klicka på **Cloud Shell** -knappen i det övre navigerings fältet i Azure Portal

   ![Skärm bild som visar hur du startar Azure Cloud Shell från Azure Portal.](media/quickstart-powershell/shell-icon.png)

2. Välj PowerShell-miljön i list rutan så kommer du att vara i Azure Drive `(Azure:)`

   ![Skärm bild som visar hur du väljer PowerShell-miljö för Azure Cloud Shell.](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Kör PowerShell-kommandon

Kör vanliga PowerShell-kommandon i Cloud Shell, till exempel:

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Navigera i Azure-resurser

 1. Visa en lista över alla dina prenumerationer från `Azure` enheten

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. `cd` till önskad prenumeration

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Visa alla dina Azure-resurser under den aktuella prenumerationen

    Skriv `dir` för att visa en lista över flera vyer för dina Azure-resurser.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources vy

Skriv `dir` under `AllResources` katalog för att se dina Azure-resurser.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>Utforska resurs grupper

 Du kan gå till `ResourceGroups` katalogen och inuti en speciell resurs grupp för att hitta virtuella datorer.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> Du kan se att det går `dir` att visa objekten mycket snabbare när du skriver Cloud Shell.
> Detta beror på att de underordnade objekten cachelagras i minnet för en bättre användar upplevelse.
Du kan dock alltid använda `dir -Force` för att hämta nya data.

### <a name="navigate-storage-resources"></a>Navigera till lagrings resurser

Genom att ange i `StorageAccounts` katalogen kan du enkelt navigera bland dina lagrings resurser

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

Med anslutnings strängen kan du använda följande kommando för att montera Azure Files-resursen.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

Mer information finns i [montera en Azure Files resurs och få åtkomst till resursen i Windows][azmount].

Du kan också navigera i katalogerna under Azure Files resursen på följande sätt:

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>Interagera med virtuella datorer

Du kan hitta alla virtuella datorer under den aktuella prenumerationen via `VirtualMachines` katalog.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Anropa PowerShell-skript över fjärranslutna virtuella datorer

 > [!WARNING]
 > Se [Felsöka fjärrhantering av virtuella Azure-datorer](troubleshooting.md#troubleshooting-remote-management-of-azure-vms).

  Förutsatt att du har en virtuell dator, MyVM1, ska vi använda `Invoke-AzVMCommand` för att anropa ett PowerShell-skript block på fjärrdatorn.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  Du kan också navigera till VirtualMachines-katalogen först och köra `Invoke-AzVMCommand` enligt följande.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Logga in interaktivt på en fjärran sluten dator

Du kan använda `Enter-AzVM` för att interaktivt logga in på en virtuell dator som körs i Azure.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

Du kan också navigera till `VirtualMachines` katalogen först och köra `Enter-AzVM` enligt följande

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>Identifiera webbappar

Genom att ange i `WebApps` katalogen kan du enkelt navigera bland dina Web Apps-resurser

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

Om du vill autentisera till servrar eller virtuella datorer med SSH genererar du det offentliga privata nyckel paret i Cloud Shell och publicerar den offentliga nyckeln på `authorized_keys` fjärrdatorn, till exempel `/home/user/.ssh/authorized_keys` .

> [!NOTE]
> Du kan skapa privata SSH-offentliga nycklar med `ssh-keygen` och publicera dem `$env:USERPROFILE\.ssh` i Cloud Shell.

### <a name="using-ssh"></a>Använda SSH

Följ anvisningarna [här](../virtual-machines/linux/quick-create-powershell.md) för att skapa en ny VM-konfiguration med hjälp av Azure PowerShell-cmdletar.
Innan du ansluter till `New-AzVM` för att starta distributionen lägger du till en offentlig SSH-nyckel i VM-konfigurationen.
Den nyskapade virtuella datorn innehåller den offentliga nyckeln på `~\.ssh\authorized_keys` platsen och aktiverar därmed en kostnads fri SSH-session med autentiseringsuppgifter till den virtuella datorn.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>Visa lista över tillgängliga kommandon

Under `Azure` enhet skriver `Get-AzCommand` du för att hämta Sammanhangs beroende Azure-kommandon.

Alternativt kan du alltid använda `Get-Command *az* -Module Az.*` för att ta reda på tillgängliga Azure-kommandon.

## <a name="install-custom-modules"></a>Installera anpassade moduler

Du kan köra `Install-Module` för att installera moduler från [PowerShell-galleriet][gallery].

## <a name="get-help"></a>Get-Help

Skriv `Get-Help` för att hämta information om PowerShell i Azure Cloud Shell.

```azurepowershell-interactive
Get-Help
```

För ett speciellt kommando kan du fortfarande `Get-Help` följa med en cmdlet.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Använd Azure Files för att lagra dina data

Du kan skapa ett skript, t. ex. `helloworld.ps1` och spara det i `clouddrive` för att använda det i en Shell-session.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

Nästa gång du använder PowerShell i Cloud Shell, `helloworld.ps1` finns filen under `$HOME\clouddrive` katalogen som monterar din Azure Files-resurs.

## <a name="use-custom-profile"></a>Använd anpassad profil

Du kan anpassa din PowerShell-miljö genom att skapa PowerShell-profil (er) – `profile.ps1` (eller `Microsoft.PowerShell_profile.ps1` ).
Spara den under `$profile.CurrentUserAllHosts` (eller `$profile.CurrentUserAllHosts` ), så att den kan läsas in i varje PowerShell i Cloud Shell-sessionen.

Information om hur du skapar en profil finns i [om profiler][profile].

## <a name="use-git"></a>Använd git

Om du vill klona en git-lagrings platsen i Cloud Shell måste du skapa en [personlig åtkomsttoken][githubtoken] och använda den som användar namn. När du har din token ska du klona databasen på följande sätt:

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>Lämna gränssnittet

Skriv `exit` för att avsluta sessionen.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: /powershell/module/microsoft.powershell.core/about/about_profiles
[azmount]: ../storage/files/storage-how-to-use-files-windows.md
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/