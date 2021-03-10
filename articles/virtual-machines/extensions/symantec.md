---
title: Installera Symantec Endpoint Protection på en virtuell Windows-dator i Azure
description: Lär dig hur du installerar och konfigurerar Symantec-Endpoint Protection säkerhets tillägg på en ny eller befintlig virtuell Azure-dator som skapats med den klassiska distributions modellen.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/31/2017
ms.openlocfilehash: 9a25e9eb27111a450f787f4efb3e0d39456fc757
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559655"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Så här installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Den här artikeln visar hur du installerar och konfigurerar Symantec Endpoint Protection-klienten på en befintlig virtuell dator (VM) som kör Windows Server. Den här fullständiga klienten omfattar tjänster som virus-och spionprograms skydd, brand vägg och skydd mot intrång. Klienten installeras som ett säkerhets tillägg med hjälp av VM-agenten.

Om du har en befintlig prenumeration från Symantec för en lokal lösning kan du använda den för att skydda dina virtuella Azure-datorer. Om du inte är en kund än kan du registrera dig för en utvärderings prenumeration. Mer information om den här lösningen finns i [Symantec Endpoint Protection på Microsofts Azure-plattform][Symantec]. Den här sidan innehåller också länkar till licens information och anvisningar om hur du installerar klienten om du redan är Symantec-kund.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installera Symantec Endpoint Protection på en befintlig virtuell dator
Innan du börjar behöver du följande:

* Azure PowerShell-modulen, version 0.8.2 eller senare, på din arbets dator. Du kan kontrol lera vilken version av Azure PowerShell som du har installerat med kommandot **Get-modul Azure | Format-Table version** . Instruktioner och en länk till den senaste versionen finns i [så här installerar och konfigurerar du Azure PowerShell][PS]. Logga in på din Azure-prenumeration med `Add-AzureAccount` .
* VM-agenten som körs på den virtuella Azure-datorn.

Kontrol lera först att VM-agenten redan är installerad på den virtuella datorn. Fyll i namnet på moln tjänsten och namnet på den virtuella datorn och kör sedan följande kommandon på administratörs nivå Azure PowerShell kommando tolken. Ersätt allt inom citat tecken, inklusive < och > tecken.

> [!TIP]
> Om du inte känner till moln tjänsten och virtuella dator namn kör du **Get-AzureVM** för att visa namnen på alla virtuella datorer i din aktuella prenumeration.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Om kommandot **Skriv-Host** visar **True** installeras VM-agenten. Om det visar **falskt** kan du läsa anvisningarna och en länk till hämtningen i Azure-bloggen publicera [VM-agenten och tilläggen – del 2][Agent].

Om den virtuella dator agenten har installerats kör du dessa kommandon för att installera Symantec Endpoint Protection-agenten.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Så här kontrollerar du att Symantecs säkerhets tillägg har installerats och är uppdaterat:

1. Logga in på den virtuella datorn. Instruktioner finns i [så här loggar du in på en virtuell dator som kör Windows Server][Logon].
2. För Windows Server 2008 R2 klickar du på **starta > Symantec Endpoint Protection**. För Windows Server 2012 eller Windows Server 2012 R2 skriver du **Symantec** på Start skärmen och klickar sedan på **Symantec Endpoint Protection**.
3. Från fliken **status** i fönstret **status – Symantec Endpoint Protection** , tillämpa uppdateringar eller starta om vid behov.

## <a name="additional-resources"></a>Ytterligare resurser
[Så här loggar du in på en virtuell dator som kör Windows Server][Logon]

[Azure VM-tillägg och-funktioner][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md
