---
title: Ta bort servrar och inaktivera skydd | Microsoft Docs
description: Den här artikeln beskriver hur du avregistrerar servrar från ett Site Recovery valv och inaktiverar skyddet för virtuella datorer och fysiska servrar.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: 1356deabd13db1dd2f29ac1b1f088db2120353fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738862"
---
# <a name="remove-servers-and-disable-protection"></a>Ta bort servrar och inaktivera skydd

Den här artikeln beskriver hur du avregistrerar servrar från ett Recovery Services valv och hur du inaktiverar skyddet för datorer som skyddas av Site Recovery.


## <a name="unregister-a--configuration-server"></a>Avregistrera en konfigurations Server

Om du replikerar virtuella VMware-datorer eller Windows/Linux fysiska servrar till Azure, kan du avregistrera en frånkopplad konfigurations Server från ett valv på följande sätt:

1. [Inaktivera skydd av virtuella datorer](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Ta bort eller ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) principer för replikering.
3. [Ta bort konfigurations servern](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Avregistrera en VMM-Server

1. Stoppa replikeringen av virtuella datorer i moln på den VMM-server som du vill ta bort.
2. Ta bort eventuella nätverks mappningar som används av moln på den VMM-server som du vill ta bort. I **Site Recovery infrastruktur**  >  för nätverks mappning i **System Center VMM**  >  högerklickar du på nätverks mappningen > **ta bort**.
3. Anteckna ID för VMM-servern.
4. Ta bort associationen mellan replikeringsprinciper från molnen på den VMM-server som du vill ta bort.    >  Dubbelklicka på den associerade principen i Site Recovery infrastruktur **för principer för System Center VMM**-  >   **replikering**. Högerklicka på molnet > ta **bort koppling**.
5. Ta bort VMM-servern eller den aktiva noden. I **Site Recovery infrastruktur**  >  **för System Center VMM**  >  **VMM-servrar** högerklickar du på servern > **ta bort**.
6. Om VMM-servern är i frånkopplat tillstånd laddar du ned och kör [rensnings skriptet](/samples/browse/?redirectedfrom=TechNet-Gallery) på VMM-servern. Öppna PowerShell med alternativet **Kör som administratör** för att ändra körnings principen för standard-(LocalMachine)-omfånget. I skriptet anger du ID: t för den VMM-server som du vill ta bort. Skriptet tar bort registrerings-och moln länknings information från servern.
5. Kör rensnings skriptet på valfri sekundär VMM-Server.
6. Kör rensnings skriptet på alla andra passiva VMM-klusternoder där providern är installerad.
7. Avinstallera providern manuellt på VMM-servern. Om du har ett kluster tar du bort det från alla noder.
8. Om dina virtuella datorer replikeras till Azure måste du avinstallera Microsoft Recovery Services-agenten från Hyper-V-värdar i de borttagna molnen.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Avregistrera en Hyper-V-värd på en Hyper-V-plats

Hyper-V-värdar som inte hanteras av VMM samlas in i en Hyper-V-plats. Ta bort en värd på en Hyper-V-plats enligt följande:

1. Inaktivera replikering för virtuella Hyper-V-datorer som finns på värden.
2. Ta bort associationen mellan principer och Hyper-V-platsen.   >  Dubbelklicka på den associerade principen i Site Recovery infrastruktur **för Hyper-V-platser**  >   . Högerklicka på platsen > ta **bort koppling**.
3. Ta bort Hyper-V-värdar. I **Site Recovery infrastruktur**  >  **för Hyper-v-platser**  >  **Hyper-v-värdar** högerklickar du på servern > **ta bort**.
4. Ta bort Hyper-V-platsen när alla värdar har tagits bort från den. I **Site Recovery infrastruktur**  >  **för Hyper-v-platser**  >  **Hyper-v-platser** högerklickar du på platsen > **ta bort**.
5. Om Hyper-V-värden är i **frånkopplat** tillstånd kör du följande skript på varje Hyper-v-värd som du har tagit bort. Skriptet rensar inställningarna på servern och avregistrerar den från valvet.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Inaktivera skydd för en virtuell VMware-dator eller fysisk server (VMware till Azure)

1. I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
2. Välj något av följande alternativ på sidan **inaktivera replikering** :
    - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet tar bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfiguration på konfigurations servern har rensats och Site Recovery fakturering för den här skyddade servern har stoppats. Observera att det här alternativet endast kan användas när konfigurations servern är i anslutet tillstånd.
    - **Ta bort** – det här alternativet ska endast användas om käll miljön tas bort eller inte går att komma åt (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Det **går inte** att rensa konfigurations konfigurationen på konfigurations servern. 

> [!NOTE]
> I båda alternativen mobilitets tjänsten kommer inte att avinstalleras från de skyddade servrarna. du måste avinstallera den manuellt. Om du planerar att skydda servern igen med samma konfigurations Server kan du hoppa över avinstallationen av mobilitets tjänsten.

> [!NOTE]
> Om du redan har växlat över en virtuell dator och den körs i Azure, Tänk på att inaktivera skydd inte tar bort/påverkar den felande virtuella datorn.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Inaktivera skydd för en virtuell Azure-dator (Azure till Azure)

-  I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
> [!NOTE]
> mobilitets tjänsten kommer inte att avinstalleras från de skyddade servrarna. du måste avinstallera den manuellt. Om du planerar att skydda servern igen kan du hoppa över avinstallationen av mobilitets tjänsten.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Inaktivera skydd för en virtuell Hyper-V-dator (Hyper-V till Azure)

> [!NOTE]
> Använd den här proceduren om du replikerar virtuella Hyper-V-datorer till Azure utan en VMM-Server. Om du replikerar dina virtuella datorer med hjälp av scenariot **System Center VMM till Azure** , följer du anvisningarna inaktivera skydd för en virtuell Hyper-V-dator som replikeras med System Center VMM till Azure-scenariot

1. I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
2. I **inaktivera replikering** kan du välja följande alternativ:
   - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet tar bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfiguration på den lokala virtuella datorn kommer att rensas och Site Recovery faktureringen för den här skyddade servern stoppas.
   - **Ta bort** – det här alternativet ska endast användas om käll miljön tas bort eller inte går att komma åt (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Det **går inte** att rensa konfigurationen på den lokala virtuella datorn. 

    > [!NOTE]
    > Om du väljer alternativet **ta bort** kör du följande uppsättning skript för att rensa replikeringsinställningarna på den lokala Hyper-V-servern.

    > [!NOTE]
    > Om du redan har växlat över en virtuell dator och den körs i Azure, Tänk på att inaktivera skydd inte tar bort/påverkar den felande virtuella datorn.

1. Ta bort replikering för den virtuella datorn på käll-Hyper-V-värdservern. Ersätt SQLVM1 med namnet på den virtuella datorn och kör skriptet från en administrativ PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Inaktivera skydd för en virtuell Hyper-V-dator som replikeras till Azure med scenariot System Center VMM till Azure

1. I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
2. I **inaktivera replikering** väljer du något av följande alternativ:

   - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet tar bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfiguration på den lokala virtuella datorn har rensats och Site Recovery fakturering för den här skyddade servern har stoppats.
   - **Ta bort** – det här alternativet ska endast användas om käll miljön tas bort eller inte går att komma åt (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Det **går inte** att rensa konfigurationen på den lokala virtuella datorn. 

     > [!NOTE]
     > Om du väljer alternativet **ta bort** TUN följande skript för att rensa replikeringsinställningarna för den lokala VMM-servern.
3. Kör det här skriptet på käll-VMM-servern med PowerShell (administratörs behörighet krävs) från VMM-konsolen. Ersätt plats hållaren **SQLVM1** med namnet på den virtuella datorn.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Stegen ovan rensar replikeringsinställningarna på VMM-servern. Kör det här skriptet för att stoppa replikeringen för den virtuella datorn som körs på Hyper-V-värdservern. Ersätt SQLVM1 med namnet på din virtuella dator och host01.contoso.com med namnet på Hyper-V-värdservern.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Inaktivera skydd för en virtuell Hyper-V-dator som replikeras till en sekundär VMM-server med System Center VMM till VMM-scenario

1. I **skyddade objekt**  >  **replikerade objekt** högerklickar du på datorn > **inaktiverar replikering**.
2. I **inaktivera replikering** väljer du något av följande alternativ:

   - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet tar bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfiguration på den lokala virtuella datorn har rensats och Site Recovery fakturering för den här skyddade servern har stoppats.
   - **Ta bort** – det här alternativet ska endast användas om käll miljön tas bort eller inte går att komma åt (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Det **går inte** att rensa konfigurationen på den lokala virtuella datorn. Kör följande uppsättning skript för att rensa replikeringsinställningarna för lokala virtuella datorer.
     > [!NOTE]
     > Om du väljer alternativet **ta bort** TUN följande skript för att rensa replikeringsinställningarna för den lokala VMM-servern.

3. Kör det här skriptet på käll-VMM-servern med PowerShell (administratörs behörighet krävs) från VMM-konsolen. Ersätt plats hållaren **SQLVM1** med namnet på den virtuella datorn.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. På den sekundära VMM-servern kör du det här skriptet för att rensa inställningarna för den sekundära virtuella datorn:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. Uppdatera de virtuella datorerna på Hyper-V-värdservern på den sekundära VMM-servern så att den sekundära virtuella datorn identifieras igen i VMM-konsolen.
6. Stegen ovan rensar replikeringsinställningarna på VMM-servern. Om du vill stoppa replikeringen för den virtuella datorn kör du följande skript och de primära och sekundära virtuella datorerna. Ersätt SQLVM1 med namnet på den virtuella datorn.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```