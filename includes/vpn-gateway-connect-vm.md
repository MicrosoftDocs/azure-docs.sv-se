---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5d631d8492ed1869bdc244e2cc90595183892822
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105222"
---
Du kan ansluta till en virtuell dator som är distribuerad till ditt VNet genom att skapa en anslutning till fjärrskrivbord till den virtuella datorn. Det bästa sättet att först kontrollera att du kan ansluta till den virtuella datorn är att ansluta via dess privata IP-adress snarare än datornamnet. På så sätt kan du testa om du kan ansluta, men inte om namnmatchningen är korrekt konfigurerad.

1. Leta upp den privata IP-adressen. Du kan hitta privata IP-adressen för en virtuell dator genom att antingen granska egenskaperna för den virtuella datorn i Azure Portal eller med hjälp av PowerShell.

   * Azure Portal – Leta upp din första virtuella dator på Azure Portal. Visa egenskaperna för den virtuella datorn. Den privata IP-adressen är angiven.

   * PowerShell – Använd exemplet för att visa en lista över virtuella datorer och privata IP-adresser från dina resursgrupper. Du behöver inte ändra exemplet innan du använder det.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Kontrollera att du är ansluten till ditt VNet med en punkt-till-plats-anslutning via VPN.
1. Öppna **anslutning till fjärrskrivbord** genom att skriva "RDP" eller "anslutning till fjärrskrivbord" i sökrutan i aktivitets fältet och välj sedan anslutning till fjärrskrivbord. Du kan även öppna Anslutning till fjärrskrivbord med hjälp av kommandot ”mstsc” i PowerShell. 
1. I fjärrskrivbordsanslutningen anger du den virtuella datorns privata IP-adress. Du kan klicka på "Visa alternativ" för att justera inställningar och sedan ansluta.

**Felsöka en anslutning**

Om du har problem med att ansluta till en virtuell dator via VPN-anslutningen kan du kontrollera följande:

* Kontrollera att VPN-anslutningen har genomförts.

* Kontrollera att du ansluter till den virtuella datorns privata IP-adress.

* Om du kan ansluta till den virtuella datorn med hjälp av den privata IP-adressen, men inte med namnet på datorn, kontrollerar du att du har konfigurerat DNS korrekt. Mer information om hur namnmatchningen fungerar för virtuella datorer finns i [Namnmatchning för virtuella datorer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

* Mer information finns i [Felsöka fjärrskrivbordsanslutningar till en virtuell dator](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).