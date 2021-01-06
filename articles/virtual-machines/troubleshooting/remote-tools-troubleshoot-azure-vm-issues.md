---
title: Använd fjärrverktyg för att felsöka problem med virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om PsExec, PowerShell-skript och andra fjärrverktyg som du kan använda för att felsöka problem med virtuella Azure-datorer utan att använda RDP.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 0681346252f840173d5cd7d4cfe5ef40076f0068
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97912605"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Använd fjärrverktyg för att felsöka problem med Azure VM

När du felsöker problem på en virtuell Azure-dator (VM) kan du ansluta till den virtuella datorn med hjälp av de fjärrverktyg som beskrivs i den här artikeln i stället för att använda Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Seriekonsol

Använd en [serie konsol för Azure Virtual Machines](serial-console-windows.md) för att köra kommandon på den virtuella Azure-datorn.

## <a name="remote-cmd"></a>Fjärr-CMD

Ladda ned [PsExec](/sysinternals/downloads/psexec). Anslut till den virtuella datorn genom att köra följande kommando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Kommandot måste köras på en dator som finns i samma virtuella nätverk.
>* DIP eller värdnamn kan användas för att ersätta \<computer> .
>* Parametern-s ser till att kommandot anropas med hjälp av system kontot (administratörs behörighet).
>* PsExec använder TCP-portarna 135 och 445. Därför måste de två portarna vara öppna i brand väggen.

## <a name="run-command"></a>Kör kommando

Mer information om hur du använder kommando funktionen kör för att köra skript på den virtuella datorn finns i [köra PowerShell-skript i din virtuella Windows-dator med kommandot kör](../windows/run-command.md).

## <a name="custom-script-extension"></a>Anpassat skripttillägg

Du kan använda funktionen för anpassat skript tillägg för att köra ett anpassat skript på den virtuella mål datorn. Om du vill använda den här funktionen måste följande villkor vara uppfyllda:

* Den virtuella datorn är ansluten.
* Azure Virtual Machine-agenten är installerad och fungerar som förväntat på den virtuella datorn.
* Tillägget har inte installerats tidigare på den virtuella datorn.
 
  Tillägget infogar bara skriptet första gången det används. Om du använder den här funktionen senare känner tillägget igen att det redan har använts och inte överför det nya skriptet.

Ladda upp ditt skript till ett lagrings konto och skapa en egen behållare. Kör sedan följande skript i Azure PowerShell på en dator som har anslutning till den virtuella datorn.

### <a name="for-classic-deployment-model-vms"></a>För klassiska virtuella datorer för distributions modell

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>För Azure Resource Manager virtuella datorer

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Fjärr-PowerShell

>[!NOTE]
>TCP-port 5986 (HTTPS) måste vara öppen så att du kan använda det här alternativet.
>
>För Azure Resource Manager virtuella datorer måste du öppna port 5986 på nätverks säkerhets gruppen (NSG). Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port (5986) och en offentlig port. Sedan måste du också öppna den offentliga porten på NSG.

### <a name="set-up-the-client-computer"></a>Konfigurera klient datorn

Om du vill använda PowerShell för att fjärrans luta till den virtuella datorn måste du först konfigurera klient datorn så att anslutningen tillåts. Det gör du genom att lägga till den virtuella datorn i PowerShell-listan över betrodda värdar genom att köra följande kommando, efter vad som är tillämpligt.

Så här lägger du till en virtuell dator i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Så här lägger du till flera virtuella datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Så här lägger du till alla datorer i listan över betrodda värdar:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Aktivera RemotePS på den virtuella datorn

För virtuella datorer som skapats med den klassiska distributions modellen använder du tillägget för anpassat skript för att köra följande skript:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

För Azure Resource Manager virtuella datorer använder du kör-kommandon från portalen för att köra EnableRemotePS-skriptet:

![Kör kommando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Anslut till VM:en

Kör följande kommando baserat på klient datorns plats:

* Utanför det virtuella nätverket eller distributionen

  * Kör följande kommando för en virtuell dator som skapats med den klassiska distributions modellen:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * För en Azure Resource Manager virtuell dator lägger du först till ett DNS-namn till den offentliga IP-adressen. Detaljerade anvisningar finns i [skapa ett fullständigt kvalificerat domän namn i Azure Portal för en virtuell Windows-dator](../create-fqdn.md). Kör sedan följande kommando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* I det virtuella nätverket eller distributionen kör du följande kommando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Om du ställer in flaggan SkipCaCheck kringgås kravet på att importera ett certifikat till den virtuella datorn när du startar sessionen.

Du kan också använda Invoke-Command-cmdlet: en för att köra ett skript på den virtuella datorn via en fjärr anslutning.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Fjärregister

>[!NOTE]
>TCP-port 135 eller 445 måste vara öppen för att det här alternativet ska kunna användas.
>
>För Azure Resource Manager virtuella datorer måste du öppna port 5986 på NSG. Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port 5986 och en offentlig port. Du måste också öppna den offentliga porten på NSG.

1. Öppna Registereditorn (regedit.exe) från en annan virtuell dator i samma virtuella nätverk.

2. Välj **fil**  >  **Anslut nätverks register**.

   ![Registereditorn](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Leta upp den virtuella mål datorn efter **värdnamn** eller **dynamisk IP** (föredra) genom att ange den i rutan **Ange objekt namn att välja** .

   ![Ange rutan objekt namn att markera](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Ange autentiseringsuppgifterna för den virtuella mål datorn.

5. Gör nödvändiga register ändringar.

## <a name="remote-services-console"></a>Konsolen för fjärrtjänster

>[!NOTE]
>TCP-portarna 135 eller 445 måste vara öppna för att det här alternativet ska kunna användas.
>
>För Azure Resource Manager virtuella datorer måste du öppna port 5986 på NSG. Mer information finns i säkerhets grupper. 
>
>För RDFE-VM: ar måste du ha en slut punkt som har en privat port 5986 och en offentlig port. Du måste också öppna den offentliga porten på NSG.

1. Öppna en instans av **Services. msc** från en annan virtuell dator i samma virtuella nätverk.

2. Högerklicka på **tjänster (lokalt)**.

3. Välj **Anslut till en annan dator**.

   ![Fjärrtjänst](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Ange den dynamiska IP-adressen för den virtuella mål datorn.

   ![Inmatad dynamisk IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Gör nödvändiga ändringar i tjänsterna.

## <a name="next-steps"></a>Nästa steg

- Mer information om Enter-PSSession-cmdleten finns i [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1&preserve-view=true).
- Mer information om tillägget för anpassat skript för Windows med hjälp av den klassiska distributions modellen finns i [anpassat skript tillägg för Windows](../extensions/custom-script-windows.md).
- PsExec är en del av [PSTools-sviten](https://download.sysinternals.com/files/PSTools.zip).
- Mer information om PSTools Suite finns i [PSTools](/sysinternals/downloads/pstools).
