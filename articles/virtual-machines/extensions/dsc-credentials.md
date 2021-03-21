---
title: Skicka autentiseringsuppgifter till Azure med önskad tillstånds konfiguration
description: Lär dig hur du på ett säkert sätt skickar autentiseringsuppgifter till virtuella Azure-datorer med hjälp av PowerShell Desired State Configuration (DSC).
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560089"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Skicka autentiseringsuppgifter till Azure DSCExtension-hanteraren

Den här artikeln beskriver DSC-tillägget (Desired State Configuration) för Azure. En översikt över DSC-tilläggs hanteraren finns i [Introduktion till tilläggs hanteraren för Azure Desired State Configuration](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Skicka in autentiseringsuppgifter

Som en del av konfigurations processen kan du behöva konfigurera användar konton, komma åt tjänster eller installera ett program i en användar kontext. Du måste ange autentiseringsuppgifter för att kunna utföra dessa åtgärder.

Du kan använda DSC för att konfigurera parametriserade konfigurationer. I en parametriserad konfiguration överförs autentiseringsuppgifterna till konfigurationen och lagras på ett säkert sätt i MOF-filer. Azure Extension-hanteraren fören klar hanteringen av autentiseringsuppgifter genom att tillhandahålla automatisk hantering av certifikat.

Följande DSC-konfigurations skript skapar ett lokalt användar konto med det angivna lösen ordet:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Det är viktigt att inkludera **Node localhost** som en del av konfigurationen. Tilläggs hanteraren söker särskilt efter **nodens localhost** -instruktion. Om den här instruktionen saknas fungerar följande steg inte. Det är också viktigt att inkludera typecast **[PsCredential]**. Den här typen utlöser tillägget för att kryptera autentiseringsuppgifterna.

Publicera skriptet till Azure Blob Storage:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Ange Azure DSC-tillägget och ange autentiseringsuppgifterna:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Hur en autentiseringsuppgift skyddas

Om du kör den här koden uppmanas du att ange autentiseringsuppgifter. När autentiseringsuppgiften har angetts lagras den kortfattat i minnet. När autentiseringsuppgiften har publicerats med cmdleten **set-AzVMDscExtension** överförs AUTENTISERINGSUPPGIFTEN via https till den virtuella datorn. I den virtuella datorn lagrar Azure autentiseringsuppgifterna som krypterats på disken med hjälp av det lokala VM-certifikatet. Autentiseringsuppgifterna dekrypteras i minnet och sedan krypteras det igen för att skicka det till DSC.

Den här processen skiljer sig [från att använda säkra konfigurationer utan tilläggs hanterare](/powershell/scripting/dsc/pull-server/securemof). Azure-miljön ger dig ett sätt att överföra konfigurations data på ett säkert sätt via certifikat. När du använder DSC-tilläggs hanteraren behöver du inte ange **$CertificatePath** eller en **$CertificateID** /  **$Thumbprint** post i **ConfigurationData**.

## <a name="next-steps"></a>Nästa steg

- Få en [Introduktion till tilläggs hanteraren för Azure DSC](dsc-overview.md).
- Granska [Azure Resource Manager-mallen för DSC-tillägget](dsc-template.md).
- Mer information om PowerShell DSC finns i [PowerShell-dokumentations centret](/powershell/scripting/dsc/overview/overview).
- Om du vill ha fler funktioner som du kan hantera med hjälp av PowerShell DSC kan du bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)för fler DSC-resurser.