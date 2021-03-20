---
title: Fördelar med att använda Azure NetApp Files med Oracle Database | Microsoft Docs
description: Beskriver tekniken och ger en prestanda jämförelse mellan Oracle Direct NFS (dNFS) och den traditionella NFS-klienten. Visar fördelarna med att använda dNFS med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91932506"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Fördelar med att använda Azure NetApp Files med Oracle Database

Oracle Direct NFS (dNFS) gör det möjligt att köra högre prestanda än operativ systemets egna NFS-drivrutin. Den här artikeln förklarar tekniken och ger en prestanda jämförelse mellan dNFS och den traditionella NFS-klienten (kernel NFS). Det visar också fördelarna och lätt att använda dNFS med Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Så här fungerar Oracle Direct NFS

I följande sammanfattning förklaras hur Oracle Direct NFS fungerar på hög nivå:

* Oracle Direct NFS kringgår operativ systemets buffert-cache. Data cachelagras bara en gång i användar utrymmet, vilket eliminerar omkostnader för minnes kopior.  

* Den traditionella NFS-klienten använder ett enda nätverks flöde som visas nedan:    

    ![Traditionell NFS-klient med ett enda nätverks flöde](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle Direct NFS förbättrar ytterligare prestanda genom belastnings utjämning av nätverks trafik över flera nätverks flöden. Som testas och visas nedan upprättades 650 distinkta nätverks anslutningar dynamiskt av Oracle Database:  

    ![Förbättra prestanda med Oracle Direct NFS](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Vanliga frågor och svar om Oracle för Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) visar att Oracle-dNFS är en optimerad NFS-klient. Den ger snabb och skalbar åtkomst till NFS-lagring som finns på NAS-lagringsenheter (kan nås via TCP/IP). dNFS är inbyggd i databas kärnan precis som ASM, som främst används med DAS-eller SAN-lagring. Därför *är rikt linjen att använda dNFS när du implementerar NAS-lagring och använder ASM vid implementering av San-lagring.*

dNFS är standard alternativet i Oracle-18c.

dNFS är tillgängligt från och med Oracle Database 11g. Diagrammet nedan jämför dNFS med ursprunglig NFS. När du använder dNFS kan en Oracle-databas som körs på en virtuell Azure-dator köra mer I/O än den ursprungliga NFS-klienten.

![Oracle och Azure NetApp Files jämförelse av dNFS med inbyggd NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Du kan aktivera eller inaktivera dNFS genom att köra två kommandon och starta om databasen.

För att aktivera:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

För att inaktivera:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files kombinerat med Oracle Direct NFS

Du kan förbättra prestanda för Oracle-dNFS med Azure NetApp Files-tjänsten. Tjänsten ger dig total kontroll över dina program prestanda. Det kan uppfylla extremt krävande program. Kombinationen av Oracle dNFS med Azure NetApp Files ger fantastisk nytta av dina arbets belastningar.

## <a name="next-steps"></a>Nästa steg

- [Lösningsarkitekturer med Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Översikt över Oracle-program och Azure-lösningar på Azure](../virtual-machines/workloads/oracle/oracle-overview.md)