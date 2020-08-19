---
title: PowerShell – skapa avbildningar av virtuella datorer med Packer
description: Lär dig hur du använder Packer och PowerShell för att skapa avbildningar av virtuella datorer i Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: 16f2bc2cc22fa38ece78b4a07298235abd7d629d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587097"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: använda Packer för att skapa avbildningar av virtuella datorer i Azure
Varje virtuell dator (VM) i Azure skapas från en avbildning som definierar Windows-distribution och operativ system version. Avbildningar kan omfatta förinstallerade program och konfigurationer. Azure Marketplace innehåller många första och tredje parts avbildningar för de flesta vanliga operativ system och program miljöer, eller så kan du skapa egna anpassade avbildningar som är anpassade efter dina behov. Den här artikeln beskriver [hur du använder verktyget med](https://www.packer.io/) öppen källkod för att definiera och skapa anpassade avbildningar i Azure.

Den här artikeln testades senast 8/5/2020 med [Packer](https://www.packer.io/docs/install) version 1.6.1.

> [!NOTE]
> Nu har Azure en tjänst, Azure Image Builder (för hands version), för att definiera och skapa egna anpassade avbildningar. Azure Image Builder bygger på Packer, så du kan till och med använda dina befintliga Packers Shell-Provisioning-skript med det. Information om hur du kommer igång med Azure Image Builder finns i [skapa en virtuell Windows-dator med Azure Image Builder](image-builder.md).

## <a name="create-azure-resource-group"></a>Skapa Azure-resurs grupp
Under Bygg processen skapar Packern tillfälliga Azure-resurser när den skapar den virtuella käll datorn. Om du vill avbilda den virtuella käll datorn för användning som en avbildning måste du definiera en resurs grupp. Utdata från paketets Bygg process lagras i den här resurs gruppen.

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myPackerGroup* på platsen för *öster* :

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Skapa Azure-autentiseringsuppgifter
Packer autentiseras med Azure med hjälp av ett huvud namn för tjänsten. Ett huvud namn för Azure-tjänsten är en säkerhets identitet som du kan använda med appar, tjänster och automatiserings verktyg som Packer. Du styr och definierar behörigheterna för vilka åtgärder som tjänstens huvud namn kan utföra i Azure.

Skapa ett huvud namn för tjänsten med [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Värdet för `-DisplayName` måste vara unikt. Ersätt med ditt eget värde om det behövs.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Sedan skriver du in lösen ordet och program-ID: t.

```powershell
$plainPassword
$sp.ApplicationId
```


För att autentisera till Azure måste du också skaffa dina Azure-klient-och prenumerations-ID: n med [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Definiera Packer-mall
För att skapa avbildningar skapar du en mall som en JSON-fil. I mallen definierar du skapare och provisioner som utför den faktiska Bygg processen. Packer har ett [verktyg för Azure](https://www.packer.io/docs/builders/azure.html) som gör att du kan definiera Azure-resurser, till exempel de autentiseringsuppgifter för tjänstens huvud namn som skapades i föregående steg.

Skapa en fil med namnet *windows.jspå* och klistra in följande innehåll. Ange dina egna värden för följande:

| Parameter                           | Var du kan hämta |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Visa tjänstens huvud namns-ID med `$sp.applicationId` |
| *client_secret*                     | Visa det automatiskt genererade lösen ordet med `$plainPassword` |
| *tenant_id*                         | Utdata från `$sub.TenantId` kommando |
| *subscription_id*                   | Utdata från `$sub.SubscriptionId` kommando |
| *managed_image_resource_group_name* | Namnet på den resurs grupp som du skapade i det första steget |
| *managed_image_name*                | Namn för den hanterade disk avbildning som skapas |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Den här mallen skapar en virtuell Windows Server 2016-dator, installerar IIS och generaliserar sedan den virtuella datorn med Sysprep. IIS-installationen visar hur du kan använda PowerShell-etableringen för att köra ytterligare kommandon. Den sista paket avbildningen innehåller sedan den nödvändiga installationen och konfigurationen av program varan.

Gäst agenten i Windows ingår i Sysprep-processen. Agenten måste vara helt installerad innan den virtuella datorn kan sysprep'ed. För att säkerställa att detta är sant måste alla agent tjänster köras innan du kör sysprep.exe. Föregående JSON-kodfragment visar ett sätt att göra detta i PowerShell-etableringen. Det här kodfragmentet krävs bara om den virtuella datorn har kon figurer ATS för att installera agenten, vilket är standardinställningen.


## <a name="build-packer-image"></a>Avbildning av bygg paket
Om du inte redan har installerat Packer på den lokala datorn [följer du installations anvisningarna för installations](https://learn.hashicorp.com/packer/getting-started/install)guiden.

Bygg avbildningen genom att öppna en cmd-prompt och ange din Packer-mallfil enligt följande:

```
./packer build windows.json
```

Ett exempel på utdata från föregående kommandon är följande:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Det tar några minuter för packare att skapa den virtuella datorn, köra provisionen och rensa distributionen.


## <a name="create-a-vm-from-the-packer-image"></a>Skapa en virtuell dator från paket avbildningen
Nu kan du skapa en virtuell dator från avbildningen med [New-AzVM](/powershell/module/az.compute/new-azvm). De stödda nätverks resurserna skapas om de inte redan finns. När du uppmanas till det anger du ett administratörs användar namn och lösen ord som ska skapas på den virtuella datorn. I följande exempel skapas en virtuell dator med namnet *myVM* från *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Om du vill skapa virtuella datorer i en annan resurs grupp eller region än din Packer-avbildning anger du bild-ID i stället för avbildnings namn. Du kan hämta avbildnings-ID: t med [Get-AzImage](/powershell/module/az.compute/get-azimage).

Det tar några minuter att skapa den virtuella datorn från din Packer-avbildning.


## <a name="test-vm-and-webserver"></a>Testa virtuell dator och webb server
Hämta den offentliga IP-adressen för den virtuella datorn med [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIP* som skapades tidigare:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Om du vill se din virtuella dator, som innehåller IIS-installationen från Packer-etableringen, anger du den offentliga IP-adressen i en webbläsare i praktiken.

![Standardwebbplatsen i IIS](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Nästa steg
Du kan också använda befintliga paket med Provisioning-skript med [Azure Image Builder](image-builder.md).
