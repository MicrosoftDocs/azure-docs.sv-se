---
title: Hitta och använda Azure Marketplace-avbildningar och-planer
description: Använd Azure PowerShell för att hitta och använda utgivare, erbjudande, SKU, version och planera information för VM-avbildningar i Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906275"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Hitta och Använd virtuella Azure Marketplace-avbildningar med Azure PowerShell     

Den här artikeln beskriver hur du använder Azure PowerShell för att hitta VM-avbildningar på Azure Marketplace. Du kan sedan ange en Marketplace-avbildning och planera information när du skapar en virtuell dator.

Du kan också söka efter tillgängliga bilder och erbjudanden med hjälp av [Azure Marketplace](https://azuremarketplace.microsoft.com/) -butik, [Azure Portal](https://portal.azure.com)eller [Azure CLI](../linux/cli-ps-findimage.md). 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>Skapa en virtuell dator från en virtuell hård disk med plan information

Om du har en befintlig virtuell hård disk som har skapats med en Azure Marketplace-avbildning kan du behöva ange inköps plan informationen när du skapar en ny virtuell dator från den virtuella hård disken.

Om du fortfarande har den ursprungliga virtuella datorn eller en annan virtuell dator som har skapats från samma avbildning kan du hämta plan namn, utgivare och produkt information från den med hjälp av Get-AzVM. I det här exemplet får du en virtuell dator med namnet *myVM* i resurs gruppen *myResourceGroup* och sedan visas information om inköps planen.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Om du inte fick plan informationen innan den ursprungliga virtuella datorn togs bort, kan du skicka en [support förfrågan](https://ms.portal.azure.com/#create/Microsoft.Support). De kommer att behöva det virtuella dator namnet, prenumerations-ID och tidsstämpeln för borttagnings åtgärden.

Om du vill skapa en virtuell dator med en virtuell hård disk läser du den här artikeln [skapa en virtuell dator från en specialiserad virtuell hård disk](create-vm-specialized.md) och lägger till i en rad för att lägga till plan informationen i VM-konfigurationen med [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) som liknar följande:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Skapa en ny virtuell dator från en Marketplace-avbildning

Om du redan har information om vilken avbildning som du vill använda kan du skicka den informationen till cmdleten [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) för att lägga till avbildnings information i VM-konfigurationen. Se nästa avsnitt för att söka efter och lista de avbildningar som är tillgängliga i Marketplace.

Vissa betalda avbildningar kräver också att du anger information om inköps plan med hjälp av [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Sedan skickar du VM-konfigurationen tillsammans med de andra konfigurations objekten till- `New-AzVM` cmdleten. Ett detaljerat exempel på hur du använder en VM-konfiguration med PowerShell finns i det här [skriptet](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Om du får ett meddelande om att godkänna villkoren för avbildningen läser du avsnittet [Godkänn villkoren](#accept-the-terms) längre fram i den här artikeln.

## <a name="list-images"></a>Lista bilder

Ett sätt att hitta en avbildning på en plats är att köra cmdletarna [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher), [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)och [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) i ordning:

1. Visa en lista över avbildningsutgivare.
2. Visa en lista över erbjudanden från en viss utgivare.
3. Visa en lista över SKU:er för ett visst erbjudande.

Kör sedan [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) för en vald SKU för att visa en lista över de versioner som ska distribueras.

1. Visa en lista över utgivare:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Fyll i det valda utgivar namnet och lista erbjudandena:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Fyll i det valda erbjudande namnet och lista SKU: er:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Fyll i det valda SKU-namnet och hämta avbildnings versionen:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Från `Get-AzVMImage` kommandots utdata kan du välja en versions avbildning för att distribuera en ny virtuell dator.

I följande exempel visas en fullständig sekvens med kommandon och deras utdata:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Delvisa utdata:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

För *Microsoft Windows Server* -utgivaren:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

Utdata:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

För *Windows Server* -erbjudandet:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Delvisa utdata:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

För *2019-datacenter-* SKU: n:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Nu kan du kombinera vald utgivare, erbjudande, SKU och version till en URN (värden avgränsade med:). Skicka denna URN med `--image` parametern när du skapar en virtuell dator med cmdleten [New-AzVM](/powershell/module/az.compute/new-azvm) . Du kan också ersätta versions numret i URN med "senaste" för att hämta den senaste versionen av avbildningen.

Om du distribuerar en virtuell dator med en Resource Manager-mall ställer du in avbildnings parametrarna individuellt i `imageReference` egenskaperna. Se [mallreferensen](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visa plan egenskaper

Kör cmdleten om du vill visa information om inköps planen för en avbildning `Get-AzVMImage` . Om `PurchasePlan` egenskapen i utdata inte är `null` så har bilden de termer som du behöver acceptera innan du programmerar distributionen.  

Till exempel har *Windows Server 2016 Data Center* -avbildningen inga ytterligare villkor, så `PurchasePlan` informationen är `null` :

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Utdata:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Exemplet nedan visar ett liknande kommando för *data science Virtual Machine-Windows 2016-* avbildningen, som har följande `PurchasePlan` Egenskaper: `name` , `product` och `publisher` . Vissa bilder har också en `promotion code` egenskap. Information om hur du distribuerar den här avbildningen finns i följande avsnitt för att acceptera villkoren och för att aktivera program distribution.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Utdata:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Acceptera villkoren

Om du vill visa licens villkoren använder du cmdleten [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) och överför parametrarna för inköps planen. Utdata innehåller en länk till villkoren för Marketplace-avbildningen och visar om du tidigare har accepterat villkoren. Se till att använda alla gemena bokstäver i parametervärdena.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Utdata:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Använd cmdleten [set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) för att godkänna eller avvisa villkoren. Du behöver bara godkänna villkoren en gång per prenumeration på avbildningen. Se till att använda alla gemena bokstäver i parametervärdena. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Utdata:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>Nästa steg

För att snabbt skapa en virtuell dator med `New-AzVM` cmdleten med hjälp av grundläggande avbildnings information, se [skapa en virtuell Windows-dator med PowerShell](quick-create-powershell.md).

Mer information om hur du använder Azure Marketplace-avbildningar för att skapa anpassade avbildningar i ett delat avbildnings Galleri finns i [tillhandahålla Azure Marketplace inköps plan information när du skapar bilder](../marketplace-images.md).
