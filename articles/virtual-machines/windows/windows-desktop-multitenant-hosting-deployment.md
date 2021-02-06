---
title: Så här distribuerar du Windows 10 på Azure med värd rättigheter för flera innehavare
description: Lär dig hur du maximerar dina Windows Software Assurance-förmåner för att ta med lokala licenser till Azure med värd rättigheter för flera innehavare.
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: 744f265251e9f58ce91085f0e5dd5d1ad13eec69
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625119"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Så här distribuerar du Windows 10 på Azure med värd rättigheter för flera innehavare 
För kunder med Windows 10 Enterprise E3/E5 per användare eller Windows anslutning för virtuella skriv bord per användare (användar prenumerations licenser eller användar prenumerations licenser), kan du använda värd rättigheterna för flera innehavare för Windows 10 för att ta med Windows 10-licenser till molnet och köra Windows 10 Virtual Machines på Azure utan att betala för en annan licens. Värd rättigheter för flera innehavare är endast tillgängliga för Windows 10 (version 1703 eller senare).

Mer information finns i [värd för flera innehavare för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - För att använda Windows 7, 8,1 och 10 bilder för utveckling eller testning, se [Windows-klient i Azure för utveckling/testnings scenarier](client-images.md)
> - För Windows Server-licensierings förmåner, se [Azure Hybrid Use Benefits för Windows Server-avbildningar](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Prenumerations licenser som är kvalificerade för värd rättigheter för flera innehavare

Med hjälp av [Microsoft administrations Center](https://docs.microsoft.com/microsoft-365/admin/admin-overview/about-the-admin-center?view=o365-worldwide&preserve-view=true)kan du bekräfta om en användare har tilldelats en licens som stöds av Windows 10.

> [!IMPORTANT]
> Användare måste ha en av prenumerations licenserna nedan för att kunna använda Windows 10-avbildningar i Azure. Om du inte har någon av dessa prenumerations licenser kan de köpas via din [moln tjänst partner](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) eller direkt via [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1).

**Berättigade prenumerations licenser:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/A5 
-   Windows 10 Enterprise E3/E5
-   Windows 10-utbildning a3/A5 
-   Windows-VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuera Windows 10-avbildning från Azure Marketplace 
För distributioner av PowerShell-, CLI-och Azure Resource Manager mallar kan du hitta Windows 10-avbildningar med hjälp av `PublisherName: MicrosoftWindowsDesktop` och `Offer: Windows-10` . Uppdatering av Windows 10-version Creator (1809) eller senare stöds för värd rättigheter för flera innehavare. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Mer information om tillgängliga avbildningar finns i [hitta och använda virtuella Azure Marketplace-avbildningar med Azure PowerShell](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Laddar upp Windows 10-VHD till Azure
Observera att om du överför en generaliserad Windows 10-hårddisk har Windows 10 inte inbyggt administratörs konto aktiverat som standard. Om du vill aktivera det inbyggda administratörs kontot inkluderar du följande kommando som en del av det anpassade skript tillägget.

```powershell
Net user <username> /active:yes
```

Följande PowerShell-kodfragment är att markera alla administratörs konton som aktiva, inklusive den inbyggda administratören. Det här exemplet är användbart om det inbyggda administratörs användar namnet är okänt.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Mer information: 
* [Ladda upp VHD till Azure](upload-generalized-managed.md)
* [Förbereda en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuera Windows 10 med värd rättigheter för flera innehavare
Kontrol lera att du har [installerat och konfigurerat de senaste Azure PowerShell](/powershell/azure/). När du har för berett din virtuella hård disk laddar du upp den virtuella hård disken till ditt Azure Storage-konto med `Add-AzVhd` cmdleten på följande sätt:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuera med Azure Resource Manager mall distribution** I dina Resource Manager-mallar kan du ange ytterligare en parameter för `licenseType` . Du kan läsa mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md). När du har laddat upp den virtuella hård disken till Azure redigerar du Resource Manager-mallen för att inkludera licens typen som en del av beräknings leverantören och distribuera mallen som vanligt:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuera via PowerShell** När du distribuerar en virtuell Windows Server-dator via PowerShell har du ytterligare en parameter för `-LicenseType` . När du har laddat upp den virtuella hård disken till Azure skapar du en virtuell dator med hjälp av `New-AzVM` och anger licensierings typen enligt följande:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verifiera att din virtuella dator utnyttjar licens förmånen
När du har distribuerat den virtuella datorn via distributions metoden PowerShell eller Resource Manager, kontrollerar du licens typen med `Get-AzVM` enligt följande:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Utdata liknar följande exempel för Windows 10 med rätt licens typ:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Den här utmatningen kontrasterar med följande virtuella dator som distribuerats utan Azure Hybrid Use Benefit Licensing, till exempel en virtuell dator som distribueras direkt från Azure-galleriet:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Mer information om att ansluta till Azure AD
Azure tillhandahåller alla virtuella Windows-datorer med inbyggt administratörs konto, som inte kan användas för att ansluta till AAD. Exempelvis fungerar inte *inställningar > konto > åtkomst till arbete eller skola > + Connect* . Du måste skapa och logga in som ett andra administratörs konto för att kunna ansluta till Azure AD manuellt. Du kan också konfigurera Azure AD med ett konfigurations paket, använda länken i avsnittet *Nästa steg* för att lära dig mer.

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du [konfigurerar VDA för Windows 10](/windows/deployment/vda-subscription-activation)
- Läs mer om [värd för flera innehavare för Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)
