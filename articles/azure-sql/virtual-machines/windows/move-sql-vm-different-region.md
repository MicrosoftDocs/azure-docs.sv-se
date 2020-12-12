---
title: Flytta en virtuell dator till en annan region (Azure Site Recovery)
description: Lär dig hur du kan migrera din SQL Server virtuella dator från en region till en annan i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360160"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Flytta en SQL Server VM till en annan region inom Azure med Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln lär dig hur du använder Azure Site Recovery för att migrera din SQL Server virtuella dator från en region till en annan i Azure. 

Att flytta en SQL Server VM till en annan region kräver att göra följande:
1. [Förbereder](#prepare-to-move): bekräfta att både käll-SQL Server VM och mål regionen är tillräckligt förberedda för flytten. 
1. [Konfigurera](#configure-azure-site-recovery-vault): om du flyttar din SQL Server VM krävs det att det är ett replikerat objekt i Azure Site Recovery valvet. Du måste lägga till din SQL Server VM i Azure Site Recovery-valvet. 
1. [Testning](#test-move-process): migreringen av SQL Server VM kräver att den inte skickas över från käll regionen till den replikerade mål regionen. För att säkerställa att flytt processen lyckas måste du först testa att SQL Server VM kan redundansväxla till mål regionen. Detta hjälper dig att exponera eventuella problem och undvika dem när du utför den faktiska flytten. 
1. [Flytta](#move-the-sql-server-vm): när redundanstestning har skickats och du vet att du är säker på att du vill migrera din SQL Server VM, kan du utföra flyttningen av den virtuella datorn till mål regionen. 
1. [Rensar](#clean-up-source-resources): om du vill undvika fakturerings avgifter tar du bort SQL Server VM från valvet och eventuella onödiga resurser som finns kvar i resurs gruppen. 

## <a name="verify-prerequisites"></a>Verifiera kraven 

- Bekräfta att det [finns stöd](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)för att flytta från käll regionen till mål regionen.  
- Granska [scenario arkitekturen och-komponenterna](../../../site-recovery/azure-to-azure-architecture.md) samt [support begränsningar och krav](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Verifiera konto behörigheter. Om du har skapat ditt kostnads fria Azure-konto är du administratör för din prenumeration. Om du inte är prenumerations administratör kan du arbeta med administratören för att tilldela de behörigheter som du behöver. Om du vill aktivera replikering för en virtuell dator och kopiera data med hjälp av Azure Site Recovery måste du ha: 
    - Behörigheter för att skapa en virtuell dator. Den inbyggda rollen *virtuell dator deltagare* har följande behörigheter, bland annat: 
        - Behörigheter för att skapa en virtuell dator i den valda resurs gruppen. 
        - Behörigheter för att skapa en virtuell dator i det valda virtuella nätverket. 
        - Behörighet att skriva till det valda lagrings kontot. 
      - Behörigheter för att hantera Azure Site Recovery åtgärder. Rollen *Site Recovery Contributor* har alla behörigheter som krävs för att hantera Site Recovery åtgärder i ett Recovery Services valv.  

## <a name="prepare-to-move"></a>Förbered för att flytta
Förbered både käll SQL Server VM och mål region för flytten. 

### <a name="prepare-the-source-sql-server-vm"></a>Förbered käll SQL Server VM

- Se till att alla de senaste rot certifikaten finns på SQL Server VM som du vill flytta. Om de senaste rot certifikaten inte finns där, kommer säkerhets begränsningar att förhindra att data kopieras till mål regionen. 
- För virtuella Windows-datorer installerar du alla de senaste Windows-uppdateringarna på den virtuella datorn så att alla betrodda rot certifikat finns på datorn. I en frånkopplad miljö följer du standard processen för Windows Update-och certifikat uppdatering för din organisation. 
- För virtuella Linux-datorer följer du rikt linjerna från Linux-distributören för att få de senaste betrodda rot certifikaten och listan över återkallade certifikat på den virtuella datorn. 
- Kontrol lera att du inte använder en autentiseringsprovider för att kontrol lera nätverks anslutningen för de virtuella datorer som du vill flytta. 
- Om den virtuella datorn som du försöker flytta inte har åtkomst till Internet, eller om den använder en brand Väggs-proxy för att kontrol lera utgående åtkomst, kontrollerar du kraven. 
- Identifiera käll nätverks layouten och alla resurser som du använder just nu. Detta omfattar men är inte begränsat till belastningsutjämnare, nätverks säkerhets grupper (NSG: er) och offentliga IP-adresser. 

### <a name="prepare-the-target-region"></a>Förbereda målregionen

- Kontrol lera att din Azure-prenumeration gör att du kan skapa virtuella datorer i mål regionen som används för haveri beredskap. Kontakta supporten och aktivera den kvot som krävs. 
- Kontrol lera att din prenumeration har tillräckligt med resurser för att stödja virtuella datorer med storlek som matchar dina virtuella käll datorer. Om du använder Site Recovery för att kopiera data till målet, Site Recovery välja samma storlek eller den närmast möjliga storleken på den virtuella mål datorn. 
- Se till att du skapar en mål resurs för varje komponent som identifieras i käll nätverkets layout. Det här steget är viktigt för att se till att dina virtuella datorer har alla funktioner och funktioner i mål regionen som du hade i käll regionen. 
    - Azure Site Recovery identifierar och skapar automatiskt ett virtuellt nätverk när du aktiverar replikering för den virtuella käll datorn. Du kan också skapa ett nätverk i förväg och tilldela det till den virtuella datorn i användar flödet för att aktivera replikering. Du måste manuellt skapa andra resurser i mål regionen.
- Information om hur du skapar de vanligaste nätverks resurserna som är relevanta för dig baserat på konfigurationen av den virtuella käll datorn finns i följande dokumentation: 
    - [Nätverkssäkerhetsgrupper](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Lastbalanserare](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Offentlig IP-adress](../../../virtual-network/virtual-network-public-ip-address.md)
    - Ytterligare nätverks komponenter finns i [dokumentationen till nätverket](../../../virtual-network/virtual-networks-overview.md).
- Skapa ett nätverk för icke-produktion manuellt i mål regionen om du vill testa konfigurationen innan du utför den slutliga flyttningen till mål regionen. Vi rekommenderar det här steget eftersom det garanterar minimal interferens med produktions nätverket. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurera Azure Site Recovery valv

Följande steg visar hur du använder Azure Site Recovery för att kopiera data till mål regionen. Skapa Recovery Services-valvet i någon annan region än käll regionen. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Välj att **skapa en resurs** i det övre vänstra hörnet i navigerings fönstret. 
1. Välj **den & hanterings verktyg** och välj sedan **säkerhets kopiering och Site Recovery**. 
1. På fliken **grundläggande** , under **projekt information**, skapar du antingen en ny resurs grupp i mål regionen eller väljer en befintlig resurs grupp i mål regionen. 
1. Under **instans information** anger du ett namn för valvet och väljer sedan mål **regionen** i list rutan. 
1. Välj **Granska + skapa** för att skapa ditt Recovery Services-valv. 
1. Välj **alla tjänster** i det övre vänstra hörnet i navigerings fönstret och i rutan Sök `recovery services` . 
1. Du kan också Välj stjärnan bredvid **Recovery Services valv** för att lägga till den i ditt snabb navigerings fält. 
1. Välj **Recovery Services-valv** och välj sedan det Recovery Services valv som du skapade. 
1. Välj **Replikera** i fönstret **Översikt** . 

   ![Konfigurera replikering](./media/move-sql-vm-different-region/configure-replication.png)

1. Välj **källa** och välj sedan **Azure** som källa. Välj lämpliga värden för de andra List fälten, till exempel platsen för dina virtuella käll datorer. Endast resurs grupper som finns i **käll platsens** region visas i fältet **resurs grupp för källan** . 
1. Välj **virtuella datorer** och välj sedan de virtuella datorer som du vill migrera. Välj **OK** för att spara ditt val av virtuell dator. 
1. Välj **Inställningar** och välj sedan **mål platsen** i list rutan. Detta bör vara den resurs grupp som du för beredde tidigare. 
1. När du har anpassat replikeringen väljer du **skapa mål resurser** för att skapa resurserna på den nya platsen. 
1. När du har skapat en resurs väljer du **Aktivera replikering** för att starta replikeringen av din SQL Server VM från källan till mål regionen.
1. Du kan kontrol lera status för replikeringen genom att gå till återställnings valvet, välja **replikerade objekt** och visa **status** för din SQL Server VM. Status **skyddad** anger att replikeringen har slutförts. 

   ![Kontrollera replikeringsstatus](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testa flyttnings process
Följande steg visar hur du använder Azure Site Recovery för att testa flytt processen. 

1. Navigera till **Recovery Services valvet** i [Azure Portal](https://portal.azure.com) och välj **replikerade objekt**. 
1. Välj den SQL Server VM som du vill flytta, kontrol lera att **hälso tillståndet för replikeringen** visas som **felfri** och välj sedan **testa redundans**. 

   ![Testa redundans för den virtuella datorn](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. På sidan **testa redundans** väljer du den **senaste programkonsekventa** återställnings punkt som ska användas för redundansväxlingen, eftersom det är den enda typen av ögonblicks bild som kan garantera SQL Server data konsekvens. 
1. Välj det virtuella nätverket under **Azure Virtual Network** och välj sedan **OK** för att testa redundansväxlingen. 
   
   >[!IMPORTANT]
   > Vi rekommenderar att du använder ett separat Azure VM-nätverk för redundanstestning. Använd inte det produktions nätverk som konfigurerades när du aktiverade replikeringen och som du vill flytta dina virtuella datorer till slutligen. 

1. Om du vill övervaka förloppet navigerar du till ditt valv, väljer **Site Recovery jobb** under **övervakning** och väljer sedan det **redundanstest** som pågår.

   ![Övervaka test förlopp för redundans](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. När testet är klart navigerar du till **virtuella datorer** i portalen och granskar den nya virtuella datorn. Kontrol lera att SQL Server VM körs, har rätt storlek och är ansluten till rätt nätverk. 
1. Ta bort den virtuella datorn som skapades som en del av testet, eftersom alternativet **redundans** är nedtonat tills test resurserna för redundans har rensats. Navigera tillbaka till valvet, Välj **replikerade objekt**, välj SQL Server VM och välj sedan **Rensa redundanstest**. Spela in och spara alla observationer som är kopplade till testet i avsnittet **anteckningar** och markera kryss rutan bredvid **testningen är klar. Ta bort redundanstest för virtuella datorer**. Välj **OK** för att rensa resurser efter testet. 

   ![Rensa objekt efter ett redundanstest](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Flytta SQL Server VM 
Följande steg visar hur du flyttar SQL Server VM från käll regionen till mål regionen. 

1. Navigera till **Recovery Services** valvet, Välj **replikerade objekt**, Välj den virtuella datorn och välj sedan **redundans**. 

   ![Initiera redundans](./media/move-sql-vm-different-region/initiate-failover.png)

1. Välj den **senaste programkonsekventa** återställnings punkten under **återställnings punkten**. 
1. Markera kryss rutan bredvid Stäng av **datorn innan du påbörjar redundansväxlingen**. Site Recovery försöker stänga av den virtuella käll datorn innan redundansväxlingen utlöses. Redundansväxlingen fortsätter även om avstängningen Miss lyckas. 
1. Välj **OK** för att starta redundansväxlingen.
1. Du kan övervaka redundansväxlingen från sidan **Site Recovery jobb** som du visade när du övervakade redundansväxlingen i föregående avsnitt. 
1. När jobbet har slutförts kontrollerar du att SQL Server VM visas i mål regionen som förväntat. 
1. Navigera tillbaka till valvet, Välj **replikerade objekt**, välj SQL Server VM och välj sedan **genomför** för att slutföra flyttnings processen till mål regionen. Vänta tills commit-jobbet har slutförts. 
1. Registrera din SQL Server VM med SQL IaaS agent-tillägget för att möjliggöra hanterbarheten för **virtuella SQL-datorer** i Azure Portal och funktioner som är kopplade till tillägget. Mer information finns i [registrera SQL Server VM med agent tillägget för SQL-IaaS](sql-agent-extension-manually-register-single-vm.md). 

  > [!WARNING]
  > SQL Server data konsekvens garanteras endast med programkonsekventa ögonblicks bilder. Den **senaste bearbetade** ögonblicks bilden kan inte användas för SQL Server redundans som ögonblicks bilder av krascha ögonblicks bilder kan inte garantera SQL Server data konsekvens. 

## <a name="clean-up-source-resources"></a>Rensa käll resurser
Ta bort SQL Server VM från valvet för att undvika fakturerings avgifter och ta bort onödiga resurser som inte behövs. 

1. Gå tillbaka till **Site Recovery** valvet, Välj **replikerade objekt** och välj SQL Server VM. 
1. Välj **inaktivera replikering**. Välj en orsak till att inaktivera skyddet och välj sedan **OK** för att inaktivera replikering. 

   >[!IMPORTANT]
   > Det är viktigt att du utför det här steget för att undvika att debiteras för Azure Site Recovery replikering. 

1. Om du inte har några planer på att återanvända någon av resurserna i käll regionen, tar du bort alla relevanta nätverks resurser och motsvarande lagrings konton. 


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server vanliga frågor och svar om Windows VM](frequently-asked-questions-faq.md)
* [SQL Server om pris vägledning för Windows VM](pricing-guidance.md)
* [SQL Server en versions information för Windows VM](doc-changes-updates-release-notes.md)