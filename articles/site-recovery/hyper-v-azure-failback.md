---
title: Återställa virtuella Hyper-V-datorer från Azure med Azure Site Recovery
description: Återställa virtuella Hyper-V-datorer till en lokal plats från Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: ramamill
ms.openlocfilehash: 96c3dce17ab78e08b28bb41c0100e51a72a666e7
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110261"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Köra en återställning efter fel för virtuella Hyper-V-datorer

Den här artikeln beskriver hur du återställer virtuella Azure-datorer som har skapats efter redundansväxlingen av virtuella Hyper-V-datorer från en lokal plats till Azure, med [Azure Site Recovery](site-recovery-overview.md).

- Du kan återställa virtuella Hyper-V-datorer från Azure genom att köra en planerad redundansväxling från Azure till den lokala platsen. Om växlings riktningen är från Azure till en lokal, betraktas den som en återställning efter fel.
- Eftersom Azure är en hög tillgänglig miljö och virtuella datorer alltid är tillgängliga, är failback från Azure en planerad aktivitet. Du kan planera för ett litet avbrott så att arbets belastningar kan börja köras lokalt. 
- Planerad återställning stänger av de virtuella datorerna i Azure och laddar ned de senaste ändringarna. Ingen data förlust förväntas.

## <a name="before-you-start"></a>Innan du börjar

1. [Granska typerna av återställning efter fel](failover-failback-overview.md#hyper-v-reprotectionfailback) du kan använda – ursprunglig plats återställning och alternativ plats återställning.
2. Se till att de virtuella Azure-datorerna använder ett lagrings konto och inte hanterade diskar. Det finns inte stöd för återställning efter fel för virtuella Hyper-V-datorer som har redundansväxlats till Azure-datorer med hanterade diskar.
3. Kontrol lera att den lokala Hyper-V-värden (eller System Center VMM-servern om du använder med Site Recovery) körs och är ansluten till Azure. 
4. Se till att redundans och genomförande är slutförda för de virtuella datorerna. Du behöver inte konfigurera några enskilda Site Recoverys komponenter för återställning efter fel för virtuella Hyper-V-datorer från Azure.
5. Tiden som krävs för att slutföra datasynkroniseringen och starta den lokala virtuella datorn beror på ett antal faktorer. För att påskynda hämtningen av data kan du konfigurera Microsoft Recovery Services-agenten att använda fler trådar för att parallellisera nedladdningen. [Läs mer](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Växla tillbaka till den ursprungliga platsen

Om du vill återställa virtuella Hyper-V-datorer i Azure till den ursprungliga lokala virtuella datorn kör du en planerad redundansväxling från Azure till den lokala platsen enligt följande:

1. Välj den virtuella datorn i valvet > **replikerade objekt**. Högerklicka på den virtuella datorn > **planerad redundansväxling**. Om du inte återställer en återställnings plan väljer du plan namnet **och klickar på**  >  **planerad redundansväxling**.
2. I **Bekräfta Planerad redundans** väljer du käll-och mål platserna. Observera växlings riktningen. Om redundansväxlingen från det primära fungerade som förväntat och alla virtuella datorer finns på den sekundära platsen är detta endast för information.
3. I **datasynkronisering** väljer du ett alternativ:
    - **Synkronisera data före redundans (synkronisera ändringar endast)**– med det här alternativet minimeras nedtid för virtuella datorer när den synkroniseras utan att stängas av.
        - **Fas 1**: tar en ögonblicks bild av den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
        - **Fas 2**: stänger av den virtuella Azure-datorn så att inga nya ändringar sker där. Den slutliga uppsättningen delta ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    - **Synkronisera endast data under redundans (fullständig hämtning)**– det här alternativet är snabbare eftersom vi förutsätter att det mesta av disken har ändrats och inte vill ägna tid åt att beräkna kontroll summor. Det här alternativet utför inte några beräkningar av kontroll summa.
        - Den utför en nedladdning av disken. 
        - Vi rekommenderar att du använder det här alternativet om du har kört Azure en stund (en månad eller mer) eller om den lokala virtuella datorn har tagits bort.

4. För VMM, om data kryptering är aktiverat för molnet, i **krypterings nyckel** väljer du det certifikat som utfärdades när du aktiverade data kryptering under installationen av providern på VMM-servern.
5. Starta redundansväxlingen. Du kan följa redundansförloppet på fliken **Jobb**.
6. Om du har valt alternativet för att synkronisera data före redundansväxlingen när den inledande datasynkroniseringen har slutförts och du är redo att stänga av de virtuella datorerna i Azure klickar du på **jobb** > jobb namn > **Slutför redundans**. Det här gör följande:
    - Stänger av Azure-datorn.
    - Överför de senaste ändringarna till den lokala virtuella datorn.
    - Startar den lokala virtuella datorn.
7. Nu kan du logga in på den lokala virtuella datorn för att kontrol lera att den är tillgänglig som förväntat.
8. Den virtuella datorn är i ett väntande tillstånd. Klicka på **genomför** för att genomföra redundansväxlingen.
9. Slutför återställningen genom att klicka på **omvänd replikering** för att starta replikeringen av den lokala virtuella datorn till Azure igen.



## <a name="fail-back-to-an-alternate-location"></a>Växla tillbaka till en alternativ plats 

Växla tillbaka till en alternativ plats enligt följande:

1. Om du konfigurerar ny maskin vara installerar du en [version av Windows som stöds](hyper-v-azure-support-matrix.md#replicated-vms)och Hyper-V-rollen på datorn.
2. Skapa en virtuell nätverks växel med samma namn som du hade på den ursprungliga servern.
3. I   >  **skydds grupp** för skyddade objekt  >  \<ProtectionGroupName>  ->  \<VirtualMachineName> väljer du den virtuella dator som du vill återställa och väljer sedan **planerad redundansväxling**.
4. I **Bekräfta planerad redundansväxling**, väljer du **skapa en lokal virtuell dator om den inte finns**.
5. I **värdnamn** väljer du den nya Hyper-V-värdservern där du vill placera den virtuella datorn.
6. I **datasynkronisering** rekommenderar vi att du väljer alternativet för att synkronisera data före redundansväxlingen. Detta minimerar stillestånds tiden för virtuella datorer när den synkroniseras utan att stänga av dem. Det gör följande:
    - **Fas 1**: tar ögonblicks bilder av den virtuella Azure-datorn och kopierar den till den lokala Hyper-V-värden. Datorn fortsätter att köras i Azure.
    - **Fas 2**: stänger av den virtuella Azure-datorn så att inga nya ändringar sker där. Den slutliga uppsättningen ändringar överförs till den lokala servern och den lokala virtuella datorn startas.
    
7. Klicka på bock markeringen för att påbörja redundansväxlingen (failback).
8. När den första synkroniseringen är klar och du är redo att stänga av den virtuella Azure-datorn klickar du på **jobb**  >  \<planned failover job>  >  **Slutför redundans**. Detta stänger av Azure-datorn, överför de senaste ändringarna till den lokala virtuella datorn och startar den.
9. Du kan logga in på den lokala virtuella datorn för att kontrol lera att allt fungerar som förväntat.
10. Slutför redundansväxlingen genom att klicka på **genomför** . Commit tar bort den virtuella Azure-datorn och dess diskar och förbereder den lokala virtuella datorn så att den skyddas igen.
10. Klicka på **omvänd replikering** för att påbörja replikeringen av den lokala virtuella datorn till Azure. Endast delta ändringar sedan den virtuella datorn stängdes av i Azure kommer att replikeras.

    > [!NOTE]
    > Om du avbryter återställnings jobbet under datasynkroniseringen, är den lokala virtuella datorn i ett skadat tillstånd. Detta beror på att datasynkroniseringen kopierar senaste data från virtuella Azure-diskar till lokala data diskar, och tills synkroniseringen har slutförts kanske disk data inte är i ett konsekvent tillstånd. Om den lokala virtuella datorn startar efter att datasynkroniseringen har avbrutits kanske den inte startar. I det här fallet kör du om redundansväxlingen för att slutföra datasynkroniseringen.


## <a name="next-steps"></a>Nästa steg
När den lokala virtuella datorn replikeras till Azure kan du [köra en annan redundansväxling](site-recovery-failover.md) till Azure efter behov.
