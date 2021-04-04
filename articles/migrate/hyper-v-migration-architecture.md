---
title: Hur fungerar Hyper-V-migrering i Azure Migrate?
description: Lär dig mer om Hyper-V-migrering med Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751027"
---
# <a name="how-does-hyper-v-replication-work"></a>Hur fungerar Hyper-V-replikering?

Den här artikeln innehåller en översikt över arkitekturen och processerna som används när du migrerar virtuella Hyper-V-datorer med verktyget för migrering av Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering samt oberoende program varu leverantörer från tredje part (ISV).

## <a name="agentless-migration"></a>Migrering utan agent

Migreringsverktyg för Azure Migrate Server tillhandahåller replikering utan agent för lokala virtuella Hyper-V-datorer, med hjälp av ett arbets flöde för migrering som är optimerat för Hyper-V. Du installerar endast en program varu agent på Hyper-V-värdar eller klusternoder. Inget måste installeras på virtuella Hyper-V-datorer.

## <a name="server-migration-and-azure-site-recovery"></a>Server migrering och Azure Site Recovery

Migrering av Azure Migrate Server är ett verktyg för att migrera lokala arbets belastningar och molnbaserade virtuella datorer till Azure. Site Recovery är ett haveri återställnings verktyg. Verktygen delar några vanliga teknik komponenter som används för datareplikering, men tjänar olika syfte. 


## <a name="architectural-components"></a>Arkitekturkomponenter

![Diagrammet visar ett Hyper-V-nätverk med en H T T P S-datakanal till Microsoft Azure, med information som förklaras i en tabell.](./media/hyper-v-replication-architecture/architecture.png)



**Komponent** | **Distribution** | 
--- | --- 
**Replikeringsprovider** | Microsoft Azure Site Recovery-providern är installerad på Hyper-V-värdar och har registrerats med migreringen av Azure migration Server.<br/> Providern samordnar replikering för virtuella Hyper-V-datorer.
**Recovery Services agent** | Microsoft Azure Recovery Service-agenten hanterar datareplikering. Det fungerar med providern för att replikera data från virtuella Hyper-V-datorer till Azure.<br/> Replikerade data överförs till ett lagrings konto i din Azure-prenumeration. Verktyget Migreringsverktyg bearbetar replikerade data och tillämpar dem på replik diskar i prenumerationen. Replik diskarna används för att skapa virtuella Azure-datorer när du migrerar.

- Komponenter installeras med en enda installations fil som hämtas från Azure Migrate Server-migreringen på portalen.
- Providern och apparaten använder utgående HTTPS-port 443-anslutningar för att kommunicera med Azure Migrate Server-migrering.
- Kommunikation från providern och agenten är säker och krypterad.


## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell Hyper-V-dator börjar den inledande replikeringen.
2. En ögonblicks bild av virtuella Hyper-V-datorer tas.
3. Virtuella hård diskar på den virtuella datorn replikeras en i taget tills alla har kopierats till Azure. Tiden för den inledande replikeringen beror på storleken på den virtuella datorn och nätverks bandbredden.
4. Disk ändringar som sker under den inledande replikeringen spåras med Hyper-V-replikering och lagras i loggfiler (HRL-filer).
    - Loggfilerna finns i samma mapp som diskarna.
    - Varje disk har en associerad HRL-fil som skickas till sekundär lagring.
    - Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den inledande replikeringen är klar tas ögonblicks bilden av den virtuella datorn bort och delta-replikering börjar.
5. Stegvisa disk ändringar spåras i HRL-filer. Loggar med replikering laddas regelbundet upp till ett Azure Storage-konto av Recovery Services-agenten.


## <a name="performance-and-scaling"></a>Prestanda och skalning

Prestanda för Hyper-V-replikering påverkas av faktorer som inkluderar VM-storlek, data ändrings takten (omsättningen) för de virtuella datorerna, tillgängligt utrymme på Hyper-V-värden för logg fils lagring, överförings bandbredd för replikeringsdata och mål lagring i Azure.

- Om du replikerar flera datorer samtidigt använder du [Distributionshanteraren för Azure Site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) för Hyper-V för att optimera replikeringen.
- Planera din Hyper-V-replikering och distribuera replikering över Azure Storage-konton, i enlighet med kapacitet.

### <a name="control-upload-throughput"></a>Styr uppladdnings data flöde

Du kan begränsa mängden bandbredd som används för att ladda upp data till Azure på varje Hyper-V-värd. Var försiktig. Om du ställer in värdena för lågt kommer det att påverka replikeringen negativt och försena migrering.


1. Logga in på Hyper-V-värden eller klusternoden.
2. Kör **C:\Program\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** för att öppna snapin-modulen Windows Azure Backup MMC.
3. Välj **ändra egenskaper** i snapin-modulen.
4. Under **begränsning** väljer du **Aktivera användnings begränsning för Internet bandbredd för säkerhets kopierings åtgärder**. Ange gränserna för arbets tid och ledig tid. Giltiga intervall är mellan 512 och 1 023 Mbit/s.
I

### <a name="influence-upload-efficiency"></a>Påverka uppladdnings effektiviteten

Om du har reserv bandbredd för replikering och vill öka uppladdningar kan du öka antalet trådar som har allokerats för uppladdnings uppgiften enligt följande:

1. Öppna registret med regedit.
2. Navigera till nyckel HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Öka värdet för antalet trådar som används för data uppladdning för varje replikerad virtuell dator. Standardvärdet är 4 och det högsta värdet är 32. 




## <a name="next-steps"></a>Nästa steg

Prova [Hyper-V-migrering](tutorial-migrate-hyper-v.md) med Azure Migrate Server-migrering.
