---
title: Skapa en virtuell dator i DevTest Labs med Azure PowerShell
description: Lär dig hur du använder Azure DevTest Labs för att skapa och hantera virtuella datorer med Azure PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499262"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Skapa en virtuell dator med DevTest Labs med Azure PowerShell
Den här artikeln visar hur du skapar en virtuell dator i Azure DevTest Labs med hjälp av Azure PowerShell. Du kan använda PowerShell-skript för att automatisera skapandet av virtuella datorer i ett labb i Azure DevTest Labs. 

## <a name="prerequisites"></a>Krav
Innan du börjar:

- [Skapa ett labb](devtest-lab-create-lab.md) om du inte vill använda ett befintligt labb för att testa skriptet eller kommandona i den här artikeln. 
- [Installera Azure PowerShell](/powershell/azure/install-az-ps) eller Använd Azure Cloud Shell som är integrerat i Azure Portal. 

## <a name="powershell-script"></a>PowerShell-skript
Exempel skriptet i det här avsnittet använder cmdleten [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) .  Denna cmdlet tar Labbets resurs-ID, namnet på den åtgärd som ska utföras ( `createEnvironment` ) och de parametrar som krävs för att utföra åtgärden. Parametrarna finns i en hash-tabell som innehåller alla egenskaper för den virtuella datorns beskrivning. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Egenskaperna för den virtuella datorn i skriptet ovan gör att vi kan skapa en virtuell dator med Windows Server 2016 Data Center som operativ system. För varje typ av virtuell dator är dessa egenskaper något annorlunda. Avsnittet [definiera virtuell dator](#define-virtual-machine) visar hur du avgör vilka egenskaper som ska användas i det här skriptet.

Följande kommando innehåller ett exempel på hur du kör skriptet som sparats i ett fil namn: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definiera virtuell dator
I det här avsnittet visas hur du hämtar egenskaperna som är speciella för en typ av virtuell dator som du vill skapa. 

### <a name="use-azure-portal"></a>Använda Azure-portalen
Du kan skapa en Azure Resource Manager-mall när du skapar en virtuell dator i Azure Portal. Du behöver inte slutföra processen med att skapa den virtuella datorn. Du följer bara anvisningarna tills du ser mallen. Detta är det bästa sättet att få den nödvändiga JSON-beskrivningen om du inte redan har skapat en virtuell labb dator. 

1. Navigera till [Azure Portal](https://portal.azure.com).
2. Välj **alla tjänster** i den vänstra navigerings menyn.
3. Sök efter och välj **DevTest Labs** i listan över tjänster. 
4. På sidan **DevTest Labs** väljer du ditt labb i listan med labb.
5. På Start sidan för ditt labb väljer du **+ Lägg till** i verktygsfältet. 
6. Välj en **bas avbildning** för den virtuella datorn. 
7. Välj **automatiserings alternativ** längst ned på sidan ovanför knappen **Skicka** . 
8. Du ser **Azure Resource Manager-mallen** för att skapa den virtuella datorn. 
9. JSON-segmentet i avsnittet **Resources** har definitionen för den avbildnings typ som du valde tidigare. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

I det här exemplet får du se hur du får en definition av en Azure-avbildning på marknaden. Du kan få en definition av en anpassad avbildning, en formel eller en miljö på samma sätt. Lägg till eventuella artefakter som krävs för den virtuella datorn och ange avancerade inställningar som krävs. När du har angett värden för de obligatoriska fälten, och eventuella valfria fält, innan du väljer knappen **automatiserings alternativ** .

### <a name="use-azure-rest-api"></a>Använd Azure REST API
Följande procedur ger dig anvisningar om hur du hämtar egenskaper för en avbildning med hjälp av REST API: de här stegen fungerar bara för en befintlig virtuell dator i ett labb. 

1. Gå till sidan [Virtual Machines-lista](/rest/api/dtl/virtualmachines/list) och välj knappen **prova** . 
2. Välj din **Azure-prenumeration**.
3. Ange **resurs gruppen för labbet**.
4. Ange **namnet på labbet**. 
5. Välj **Kör**.
6. Du ser **egenskaperna för den avbildning** som är baserad på vilken den virtuella datorn skapades. 

## <a name="set-expiration-date"></a>Ange förfallo datum
I scenarier som utbildning, demonstrationer och test versioner kanske du vill skapa virtuella datorer och ta bort dem automatiskt efter en fast varaktighet så att du inte debiteras onödiga kostnader. Du kan ange ett förfallo datum för en virtuell dator när du skapar den med PowerShell, som du ser i avsnittet exempel på [PowerShell-skript](#powershell-script) .

Här är ett exempel på ett PowerShell-skript som anger förfallo datum för alla befintliga virtuella datorer i ett labb:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Nästa steg
Se följande innehåll: [Azure PowerShell dokumentation för Azure DevTest Labs](/powershell/module/az.devtestlabs/)
