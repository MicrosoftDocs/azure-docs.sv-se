---
title: Felsöka anslutnings problem mellan virtuella Azure-datorer | Microsoft Docs
description: Lär dig hur du felsöker och löser anslutnings problem som kan uppstå mellan virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5e602f8bbdf6ad92acacac6688326a555a0420b6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042874"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Felsökning av anslutningsproblem mellan virtuella Azure-datorer

Det kan uppstå anslutnings problem mellan virtuella datorer i Azure. Den här artikeln innehåller fel söknings steg som hjälper dig att lösa det här problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptom

En virtuell Azure-dator kan inte ansluta till en annan virtuell Azure-dator.

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrol lera om NÄTVERKSKORTet är felkonfigurerat](#step-1-check-whether-nic-is-misconfigured)
2. [Kontrol lera om nätverks trafiken blockeras av NSG eller UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Kontrol lera om nätverks trafiken blockeras av den virtuella datorns brand vägg](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Kontrol lera om den virtuella datorns app eller tjänst lyssnar på porten](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Kontrol lera om problemet orsakas av SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Kontrol lera om trafik blockeras av ACL: er för den klassiska virtuella datorn](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Kontrol lera om slut punkten har skapats för den klassiska virtuella datorn](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Försök att ansluta till en nätverks resurs för virtuella datorer](#step-8-try-to-connect-to-a-vm-network-share)
9. [Kontrol lera Inter-Vnet anslutningen](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Följ dessa steg för att felsöka problemet. När du har slutfört varje steg kontrollerar du om problemet är löst. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Steg 1: kontrol lera om NÄTVERKSKORTet är felkonfigurerat

Följ stegen i [så här återställer du nätverks gränssnittet för Azure Windows VM](/troubleshoot/azure/virtual-machines/reset-network-interface). 

Om problemet inträffar när du har ändrat nätverks gränssnittet (NIC) följer du dessa steg:

**Virtuella datorer med flera nätverkskort**

1. Lägg till ett nätverkskort.
2. Åtgärda problemen i det felaktiga NÄTVERKSKORTet eller ta bort det felaktiga NÄTVERKSKORTet.  Lägg sedan till NÄTVERKSKORTet igen.

Mer information finns i [lägga till nätverks gränssnitt eller ta bort från virtuella datorer](virtual-network-network-interface-vm.md).

**Virtuell dator med en NIC** 

- [Distribuera om virtuell Windows-dator](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows)
- [Distribuera virtuella Linux-datorer](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-linux)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Steg 2: kontrol lera om nätverks trafiken är blockerad av NSG eller UDR

Använd [Network Watcher IP-flöde verifiera](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG flödes loggning](../network-watcher/network-watcher-nsg-flow-logging-overview.md) för att avgöra om det finns en nätverks säkerhets grupp (NSG) eller User-Defined väg (UDR) som stör trafikflödet.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Steg 3: kontrol lera om nätverks trafiken blockeras av den virtuella datorns brand vägg

Inaktivera brand väggen och testa sedan resultatet. Om problemet är löst kontrollerar du brand Väggs inställningarna och aktiverar sedan brand väggen igen.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Steg 4: kontrol lera om den virtuella datorns app eller tjänst lyssnar på porten

Du kan använda någon av följande metoder för att kontrol lera om den virtuella datorns app eller tjänst lyssnar på porten.

- Kör följande kommandon för att kontrol lera om servern lyssnar på den porten.

**Virtuell Windows-dator**

```console
netstat –ano
```

**Virtuell Linux-dator**

```console
netstat -l
```

- Kör **telnet** -kommandot på den virtuella datorn för att testa porten. Om testet Miss lyckas konfigureras inte programmet eller tjänsten för att lyssna på den porten.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Steg 5: kontrol lera om problemet orsakas av SNAT

I vissa fall placeras den virtuella datorn bakom en belastnings Utjämnings lösning som har ett beroende på resurser utanför Azure. I dessa scenarier kan problemet bero på att det uppstår problem med [SNAT-portar](../load-balancer/load-balancer-outbound-connections.md). Lös problemet genom att skapa en VIP (eller ILPIP för klassisk) för varje virtuell dator som ligger bakom belastningsutjämnaren och skydda med NSG eller ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Steg 6: kontrol lera om trafiken blockeras av ACL: er för den klassiska virtuella datorn

En åtkomst kontrol lista (ACL) ger möjlighet att selektivt tillåta eller neka trafik för en virtuell dator slut punkt. Mer information finns i [Hantera ACL: en på en slut punkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Steg 7: kontrol lera om slut punkten har skapats för den klassiska virtuella datorn

Alla virtuella datorer som du skapar i Azure med hjälp av den klassiska distributions modellen kan kommunicera via en privat nätverks kanal automatiskt med andra virtuella datorer i samma moln tjänst eller virtuella nätverk. Datorer i andra virtuella nätverk kräver dock slut punkter för att dirigera inkommande nätverks trafik till en virtuell dator. Mer information finns i [så här konfigurerar du slut punkter](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Steg 8: Försök ansluta till en nätverks resurs för virtuella datorer

Om du inte kan ansluta till en nätverks resurs för virtuella datorer kan problemet orsakas av otillgängliga nätverkskort på den virtuella datorn. Information om hur du tar bort otillgängliga nätverkskort finns i [ta bort otillgängliga nätverkskort](/troubleshoot/azure/virtual-machines/reset-network-interface#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Steg 9: kontrol lera Inter-Vnet anslutningen

Använd [Network Watcher IP-flöde verifiera](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG flödes loggning](../network-watcher/network-watcher-nsg-flow-logging-overview.md) för att avgöra om det finns en NSG eller UDR som stör trafikflödet. Du kan också kontrol lera Inter-Vnet konfigurationen [här](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.