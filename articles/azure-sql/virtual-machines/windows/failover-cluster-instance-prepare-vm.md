---
title: Förbereda virtuella datorer för en FCI
description: Förbered dina virtuella Azure-datorer så att de kan använda dem med en FCI (failover Cluster instance) och SQL Server på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 10f01fd5943928eda1f1e4518f30c8e3ccf56b46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737803"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Förbereda virtuella datorer för en FCI (SQL Server på virtuella Azure-datorer)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du förbereder virtuella Azure-datorer (VM) att använda dem med en SQL Server-FCI (failover Cluster instance). Konfigurations inställningarna varierar beroende på FCI lagrings lösning, så kontrol lera att du väljer rätt konfiguration så att den passar din miljö och ditt företag. 

Mer information finns i Översikt över [FCI med SQL Server på Azure VM](failover-cluster-instance-overview.md) och [kluster metod tips](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Förutsättningar 

- En Microsoft Azure-prenumeration. Kom igång [kostnads fritt](https://azure.microsoft.com/free/). 
- En Windows-domän på virtuella Azure-datorer eller ett lokalt Data Center som utökats till Azure med länkning av virtuella nätverk.
- Ett konto som har behörighet att skapa objekt på virtuella Azure-datorer och i Active Directory.
- Ett virtuellt Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för dessa komponenter:
   - Både virtuella datorer
   - IP-adressen för Windows-redundansklustret
   - En IP-adress för varje FCI
- DNS konfigurerat på Azure-nätverket och pekar på domän kontrol Lanterna.

## <a name="choose-an-fci-storage-option"></a>Välj ett FCI lagrings alternativ

Konfigurations inställningarna för den virtuella datorn varierar beroende på vilket lagrings alternativ du planerar att använda för din SQL Server-redundanskluster. Innan du förbereder den virtuella datorn granskar du de [tillgängliga lagrings alternativen för FCI](failover-cluster-instance-overview.md#storage) och väljer det alternativ som passar din miljö och dina affärs behov bäst. Välj sedan noggrant lämpliga konfigurations alternativ för virtuella datorer i den här artikeln baserat på ditt lagrings val. 

## <a name="configure-vm-availability"></a>Konfigurera tillgänglighet för virtuell dator 

Funktionen kluster för växling vid fel kräver att virtuella datorer placeras i en [tillgänglighets uppsättning](../../../virtual-machines/linux/tutorial-availability-sets.md) eller i en [tillgänglighets zon](../../../availability-zones/az-overview.md#availability-zones). Om du väljer tillgänglighets uppsättningar kan du använda [närhets placerings grupper](../../../virtual-machines/co-location.md#proximity-placement-groups) för att hitta de virtuella datorerna närmare. I själva verket är närhets grupper nödvändiga för att använda Azure-delade diskar. 

Välj noga alternativet tillgänglighet för virtuell dator som matchar den avsedda kluster konfigurationen: 

- **Azure delade diskar**: tillgänglighets alternativet varierar om du använder Premium SSD eller UltraDisk:
   - Premium SSD: [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) i olika fel-/uppdaterings domäner för Premium-SSD placeras inuti en [närhets placerings grupp](../../../virtual-machines/windows/proximity-placement-groups-portal.md).
   - Ultra disk: [tillgänglighets zon](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) men de virtuella datorerna måste placeras i samma tillgänglighets zon som minskar klustrets tillgänglighet till 99,9%. 
- **Premium-fil resurser**: [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) eller [tillgänglighets zon](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Lagringsdirigering**: [tillgänglighets uppsättning](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Du kan inte ange eller ändra tillgänglighets uppsättningen när du har skapat en virtuell dator.

## <a name="create-the-virtual-machines"></a>Skapa de virtuella datorerna

När du har konfigurerat din VM-tillgänglighet är du redo att skapa virtuella datorer. Du kan välja att använda en Azure Marketplace-avbildning som inte har SQL Server redan är installerad på den. Men om du väljer en avbildning för SQL Server på virtuella Azure-datorer måste du avinstallera SQL Server från den virtuella datorn innan du konfigurerar instansen av redundanskluster. 

### <a name="considerations"></a>Överväganden

På ett gäst kluster för virtuella Azure-datorer rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster för en Azure IaaS-dator. Även om klustrets verifieringsrapport utfärdar en varning om att noderna endast kan nås i ett enda nätverk, kan varningen ignoreras för redundanskluster på virtuella Azure IaaS-gästdatorer.

Placera båda virtuella datorerna:

- Om du använder tillgänglighets uppsättningar i samma Azure-resurs grupp som din tillgänglighets uppsättning.
- I samma virtuella nätverk som domänkontrollanten eller i ett virtuellt nätverk som har lämplig anslutning till domänkontrollanten.
- I ett undernät som har tillräckligt med IP-adressutrymme för både virtuella datorer och alla skyddas som du kan använda i klustret.
- I Azures tillgänglighets uppsättning eller tillgänglighets zon.

Du kan skapa en virtuell Azure-dator med hjälp av en avbildning [med](sql-vm-create-portal-quickstart.md) eller [utan](../../../virtual-machines/windows/quick-create-portal.md) SQL Server förinstallerad till den. Om du väljer SQL Server avbildningen måste du avinstallera SQL Server instansen manuellt innan du installerar kluster instansen för växling vid fel. 


## <a name="uninstall-sql-server"></a>Avinstallera SQL Server

Som en del av processen för att skapa FCI installerar du SQL Server som en klustrad instans i redundansklustret. *Om du har distribuerat en virtuell dator med en Azure Marketplace-avbildning utan SQL Server kan du hoppa över det här steget.* Om du har distribuerat en avbildning med SQL Server förinstallerad måste du avregistrera SQL Server VM från SQL IaaS agent-tillägget och sedan avinstallera SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Avregistrera från SQL IaaS agent-tillägget

SQL Server VM avbildningar från Azure Marketplace registreras automatiskt med SQL IaaS agent-tillägget. Innan du avinstallerar den förinstallerade SQL Server-instansen måste du först [avregistrera varje SQL Server VM från SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Avinstallera SQL Server

När du har avregistrerat dig från tillägget kan du avinstallera SQL Server. Följ de här stegen på varje virtuell dator: 

1. Anslut till den virtuella datorn med RDP.

   När du först ansluter till en virtuell dator med hjälp av RDP frågar en fråga om du vill att datorn ska kunna identifieras i nätverket. Välj **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna av virtuella datorer tar du bort SQL Server-instansen:

   1. I **program och funktioner** högerklickar du på **Microsoft SQL Server 201_ (64-bitars)** och väljer **Avinstallera/ändra**.
   1. Välj **Ta bort**.
   1. Välj standard instansen.
   1. Ta bort alla funktioner under **databas motor tjänster**. Ta inte bort något under **Delade funktioner**. Du ser något som liknar följande skärm bild:

      ![Välja funktioner](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. Välj **Nästa** och välj sedan **ta bort**.
   1. När instansen har tagits bort startar du om den virtuella datorn. 

## <a name="open-the-firewall"></a>Öppna brand väggen 

På varje virtuell dator öppnar du Windows-brandväggens TCP-port som SQL Server använder. Som standard är detta port 1433. Men du kan ändra SQL Server port på en distribution av en virtuell Azure-dator, så öppna porten som SQL Server använder i din miljö. Den här porten öppnas automatiskt på SQL Server avbildningar som distribueras från Azure Marketplace. 

Om du använder en [belastningsutjämnare](failover-cluster-instance-vnn-azure-load-balancer-configure.md)måste du också öppna porten som hälso avsökningen använder. Som standard är detta port 59999. Men det kan vara vilken TCP-port som helst som du anger när du skapar belastningsutjämnaren. 

Den här tabellen innehåller information om de portar som du kan behöva öppna, beroende på din FCI-konfiguration: 

   | Syfte | Port | Kommentarer
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Normal port för standard instanser av SQL Server. Om du använde en avbildning från galleriet öppnas porten automatiskt. </br> </br> **Används av**: alla FCI-konfigurationer. |
   | Hälsoavsökning | TCP 59999 | Alla öppna TCP-portar. Konfigurera belastnings utjämningens [hälso avsökning](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) och klustret för att använda den här porten. </br> </br> **Används av**: FCI med Load Balancer. |
   | Filresurs | UDP 445 | Port som fil resurs tjänsten använder. </br> </br> **Används av**: FCI med Premium-filresurs. |

## <a name="join-the-domain"></a>Anslut till domänen

Du måste också ansluta dina virtuella datorer till domänen. Du kan göra detta med hjälp av en [snabb starts mall](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Granska lagrings konfiguration

Virtuella datorer som skapats från Azure Marketplace levereras med bifogad lagring. Om du planerar att konfigurera FCI-lagring med hjälp av Premium-filresurser eller Azure delade diskar kan du ta bort den bifogade lagringen för att spara pengar eftersom lokal lagring inte används för instansen av redundanskluster. Det är dock möjligt att använda den bifogade lagringen för Lagringsdirigering FCI-lösningar, så att du kan ta bort dem i det här fallet. Granska din FCI lagrings lösning för att avgöra om borttagning av bifogad lagring är optimalt för att spara kostnader. 


## <a name="next-steps"></a>Nästa steg

Nu när du har för berett din virtuella dator miljö är du redo att konfigurera din instans av redundansklustret. 

Välj någon av följande guider för att konfigurera den FCI-miljö som passar din verksamhet: 
- [Konfigurera FCI med Azure delade diskar](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Konfigurera FCI med en Premium-filresurs](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Konfigurera FCI med Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

Mer information finns i Översikt över [FCI med SQL Server på virtuella Azure-datorer](failover-cluster-instance-overview.md) och [hadr-konfigurationer som stöds](hadr-cluster-best-practices.md). 

Mer information finns i: 
- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)