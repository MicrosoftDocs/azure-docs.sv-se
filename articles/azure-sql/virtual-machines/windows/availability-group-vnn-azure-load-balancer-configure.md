---
title: Konfigurera Load Balancer för AG VNN-lyssnare
description: Lär dig hur du konfigurerar en Azure Load Balancer för att dirigera trafik till lyssnaren för virtuella nätverks namn (VNN) för din tillgänglighets grupp med SQL Server på virtuella Azure-datorer för hög tillgänglighet och haveri beredskap (HADR).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d89759438cb625a0e220af10ab6b287096f6390
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359888"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>Konfigurera Load Balancer för AG VNN-lyssnare
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I Azure Virtual Machines använder kluster en belastningsutjämnare för att lagra en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för den virtuella nätverks namn (VNN) lyssnare för Always on-tillgänglighetsgruppen (AG). 

Den här artikeln lär dig hur du konfigurerar en belastningsutjämnare med hjälp av Azure Load Balancer tjänsten. Belastningsutjämnaren dirigerar trafik till din [tillgänglighets grupp (AG)-lyssnare](availability-group-overview.md) med SQL Server på virtuella Azure-datorer för hög tillgänglighet och haveri beredskap (hadr). 

För ett alternativt anslutnings alternativ för kunder som finns på SQL Server 2019 CU8 och senare, bör du överväga en [DNN-lyssnare](availability-group-vnn-azure-load-balancer-configure.md) i stället för förenklad konfiguration och förbättrad redundans.  



## <a name="prerequisites"></a>Förutsättningar

Innan du slutför stegen i den här artikeln bör du redan ha:

- Beslut om att Azure Load Balancer är lämpligt [anslutnings alternativ för din hadr-lösning](hadr-cluster-best-practices.md#connectivity).
- Konfigurerat din [tillgänglighets grupps lyssnare](availability-group-overview.md).
- Den senaste versionen av [PowerShell](/powershell/azure/install-az-ps)har installerats. 


## <a name="create-load-balancer"></a>Skapa en lastbalanserare

Använd [Azure Portal](https://portal.azure.com) för att skapa belastningsutjämnaren:

1. I Azure Portal går du till resurs gruppen som innehåller de virtuella datorerna.

1. Välj **Lägg till**. Sök på Azure Marketplace efter **Load Balancer**. Välj **Load Balancer**.

1. Välj **Skapa**.

1. Konfigurera belastningsutjämnaren med hjälp av följande värden:

   - **Prenumeration**: din Azure-prenumeration.
   - **Resurs grupp**: den resurs grupp som innehåller de virtuella datorerna.
   - **Namn**: ett namn som identifierar belastningsutjämnaren.
   - **Region**: Azure-platsen som innehåller de virtuella datorerna.
   - **Typ**: antingen offentlig eller privat. Det går att få åtkomst till en privat belastningsutjämnare inifrån det virtuella nätverket. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet använder du en offentlig belastningsutjämnare.
   - **SKU**: standard.
   - **Virtuellt nätverk**: samma nätverk som de virtuella datorerna.
   - **IP-adresstilldelning**: statisk. 
   - **Privat IP-adress**: den IP-adress som du har tilldelat till den klustrade nätverks resursen.

   Följande bild visar användar gränssnittet för **belastnings utjämning** :

   ![Konfigurera belastningsutjämnaren](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Konfigurera backend-pool

1. Gå tillbaka till den Azure-resurs grupp som innehåller de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resurs gruppen. Markera belastningsutjämnaren.

1. Välj **backend-pooler** och välj sedan **Lägg till**.

1. Koppla backend-poolen till den tillgänglighets uppsättning som innehåller de virtuella datorerna.

1. Under **mål nätverkets IP-konfigurationer** väljer du **virtuell dator** och väljer de virtuella datorer som ska ingå som klusternoder. Se till att ta med alla virtuella datorer som ska vara värdar för FCI eller tillgänglighets gruppen.

1. Välj **OK** för att skapa backend-poolen.

## <a name="configure-health-probe"></a>Konfigurera hälso avsökning

1. I fönstret belastnings utjämning väljer du **hälso avsökningar**.

1. Välj **Lägg till**.

1. I fönstret **Lägg till hälso avsökning** <span id="probe"></span> anger du följande parametrar för hälso avsökning:

   - **Namn**: ett namn för hälso avsökningen.
   - **Protokoll**: TCP.
   - **Port**: den port som du skapade i brand väggen för hälso avsökningen [när du förbereder den virtuella datorn](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). I den här artikeln använder exemplet TCP-port `59999` .
   - **Intervall**: 5 sekunder.
   - **Tröskelvärde** för fel: 2 efterföljande fel.

1. Välj **OK**.

## <a name="set-load-balancing-rules"></a>Ange regler för belastnings utjämning

1. I fönstret belastnings utjämning väljer du **regler för belastnings utjämning**.

1. Välj **Lägg till**.

1. Ange parametrar för belastnings Utjämnings regler:

   - **Namn**: ett namn för reglerna för belastnings utjämning.
   - **IP-adress för klient** del: IP-adressen för SQL Server skyddas eller AG-lyssnarens klustrade nätverks resurs.
   - **Port**: SQL Server TCP-port. Standard instans porten är 1433.
   - **Backend-port**: samma port som **port** värde när du aktiverar **flytande IP (direkt Server retur)**.
   - **Backend-pool**: det namn på backend-poolen som du konfigurerade tidigare.
   - **Hälso avsökning**: hälso avsökningen som du konfigurerade tidigare.
   - **Persistence för session**: ingen.
   - **Tids gräns för inaktivitet (minuter)**: 4.
   - **Flytande IP (direkt Server retur)**: aktive rad.

1. Välj **OK**.

## <a name="configure-cluster-probe"></a>Konfigurera kluster avsökning

Ange port parametern för kluster avsökningen i PowerShell.

Om du vill ange parametern för kluster avsöknings porten uppdaterar du variablerna i följande skript med värden från din miljö. Ta bort vinkelparenteser ( `<` och `>` ) från skriptet.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

I följande tabell beskrivs de värden som du behöver uppdatera:


|**Värde**|**Beskrivning**|
|---------|---------|
|`Cluster Network Name`| Kluster namnet för Windows Server-redundansklustret för nätverket. I **Klusterhanteraren för växling vid fel**  >  **nätverk** högerklickar du på nätverket och väljer **Egenskaper**. Rätt värde står under **Namn** på fliken **Allmänt**.|
|`AG listener IP Address Resource Name`|Resurs namnet för den SQL Server FCI eller AG-lyssnarens IP-adress. I **Klusterhanteraren för växling vid fel**  >  **roller**, under rollen SQL Server FCI, under **Server namn** högerklickar du på IP-adressresursen och väljer **Egenskaper**. Rätt värde står under **Namn** på fliken **Allmänt**.|
|`ILBIP`|IP-adressen för den interna belastningsutjämnaren (ILB). Den här adressen är konfigurerad i Azure Portal som ILB-frontend-adress. Detta är också den SQL Server FCI IP-adress. Du hittar den i **Klusterhanteraren för växling vid fel** på samma egenskaps sida där du placerade `<AG listener IP Address Resource Name>` .|
|`nnnnn`|Avsöknings porten som du konfigurerade i belastningsutjämnarens hälso avsökning. Alla oanvända TCP-portar är giltiga.|
|Nätmask| Nät masken för kluster parametern. Det måste vara TCP IP-broadcast-adressen: `255.255.255.255` .| 


När du har ställt in kluster avsökningen kan du se alla kluster parametrar i PowerShell. Kör det här skriptet:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Redundanstest


Testa redundansväxlingen för den klustrade resursen för att verifiera kluster funktionen. 


Utför följande steg:

1. Öppna [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) och Anslut till tillgänglighets gruppens lyssnare. 
1. Expandera **Always on tillgänglighets grupp** i **Object Explorer**. 
1. Högerklicka på tillgänglighets gruppen och välj **redundans**. 
1. Följ anvisningarna i guiden för att redundansväxla tillgänglighets gruppen till en sekundär replik. 

Redundansväxlingen lyckas när replikerna växlar roller och båda synkroniseras. 


## <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och Anslut till tillgänglighets gruppens lyssnare.

>[!NOTE]
>Om du behöver kan du [hämta SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du SQL Server HADR-funktioner i Azure i [tillgänglighets grupper](availability-group-overview.md) och [instansen av redundanskluster](failover-cluster-instance-overview.md). Du kan också lära dig [metod tips](hadr-cluster-best-practices.md) för att konfigurera din miljö för hög tillgänglighet och haveri beredskap. 



