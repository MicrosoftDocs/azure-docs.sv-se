---
title: Skydda virtuella Azure-datorer till den primära regionen med Azure Site Recovery | Microsoft Docs
description: Beskriver hur du skyddar virtuella Azure-datorer efter en redundansväxling, den sekundära till den primära regionen med hjälp av Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 3b9edab6e908b4506a92c78aa8f3f53277b9c17b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91360879"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Återaktivering av skydd redundansväxlade virtuella Azure-datorer till den primära regionen

När du [växlar över](site-recovery-failover.md) virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md), startar de virtuella datorerna i den sekundära regionen i **oskyddat** tillstånd. Om du vill återställa de virtuella datorerna till den primära regionen gör du följande:

1. Skydda de virtuella datorerna i den sekundära regionen så att de börjar replikera till den primära regionen.
1. När skyddet har slutförts och de virtuella datorerna replikeras, kan du redundansväxla från den sekundära till den primära regionen.

## <a name="prerequisites"></a>Förutsättningar

- Den virtuella datorns redundans från den primära till den sekundära regionen måste allokeras.
- Den primära mål platsen måste vara tillgänglig och du bör kunna komma åt eller skapa resurser i den regionen.

## <a name="reprotect-a-vm"></a>Återaktivera skydd för en virtuell dator

1. I **valv**  >  **replikerade objekt** högerklickar du på den misslyckade virtuella datorn och väljer **sedan skydda igen**. Skydds riktningen bör visas från sekundär till primär.

   ![Skärm bild som visar en virtuell dator med en snabb meny med ny skydd valt.](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Granska resurs grupp, nätverk, lagring och tillgänglighets uppsättningar. Klicka sedan på **OK**. Om det finns några resurser som marker ATS som nya, skapas de som en del av återställnings processen.
1. Återskydds jobbet dirigerar mål platsen med den senaste informationen. När jobbet har slutförts sker delta-replikering. Sedan kan du växla tillbaka till den primära platsen. Du kan välja det lagrings konto eller det nätverk som du vill använda vid återaktivering med hjälp av alternativet Anpassa.

   ![Anpassa alternativ](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Anpassa inställningarna för att skydda på nytt

Du kan anpassa följande egenskaper för den virtuella mål datorn under skyddet.

![Anpassa](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Egenskap |Kommentarer  |
|---------|---------|
|Målresursgrupp | Ändra mål resurs gruppen som den virtuella datorn skapas i. Som en del av återskyddet tas den virtuella mål datorn bort. Du kan välja en ny resurs grupp under vilken du vill skapa den virtuella datorn efter redundansväxlingen. |
|Virtuellt mål nätverk | Det går inte att ändra mål nätverket under återskydds jobbet. Om du vill ändra nätverket gör du om nätverks mappningen. |
|Mål lagring (sekundär virtuell dator använder inte hanterade diskar) | Du kan ändra det lagrings konto som används av den virtuella datorn efter en redundansväxling. |
|Hanterade replik diskar (sekundär virtuell dator använder Managed Disks) | Site Recovery skapar en replik som Managed disks i den primära regionen för att spegla den sekundära virtuella datorns hanterade diskar. |
|Cachelagring | Du kan ange ett cache Storage-konto som ska användas under replikeringen. Ett nytt lagrings konto för cachen skapas som standard, om det inte redan finns. |
|Tillgänglighetsuppsättning | Om den virtuella datorn i den sekundära regionen är en del av en tillgänglighets uppsättning kan du välja en tillgänglighets uppsättning för den virtuella mål datorn i den primära regionen. Som standard försöker Site Recovery hitta den befintliga tillgänglighets uppsättningen i den primära regionen och använda den. Under anpassningen kan du ange en ny tillgänglighets uppsättning. |

### <a name="what-happens-during-reprotection"></a>Vad händer under skyddet?

Som standard inträffar följande:

1. Ett lagrings konto för cachen skapas i den region där den felande virtuella datorn körs.
1. Om mål lagrings kontot (det ursprungliga lagrings kontot i den primära regionen) inte finns, skapas ett nytt. Det tilldelade lagrings konto namnet är namnet på det lagrings konto som används av den sekundära virtuella datorn, suffixet med `asr` .
1. Om den virtuella datorn använder hanterade diskar skapas replik hanterade diskar i den primära regionen för att lagra data som replikeras från diskarna för den sekundära virtuella datorn.
1. Om mål tillgänglighets uppsättningen inte finns, skapas en ny som en del av återskydds jobbet vid behov. Om du har anpassat skydds inställningarna används den valda uppsättningen.

När du utlöser ett skydds jobb och den virtuella mål datorn finns inträffar följande:

1. Den virtuella mål sidan är avstängd om den körs.
1. Om den virtuella datorn använder hanterade diskar skapas en kopia av den ursprungliga disken med ett `-ASRReplica` suffix. De ursprungliga diskarna tas bort. `-ASRReplica`Kopiorna används för replikering.
1. Om den virtuella datorn använder ohanterade diskar, kopplas data diskarna för den virtuella mål datorn bort och används för replikering. En kopia av OS-disken skapas och kopplas till den virtuella datorn. Den ursprungliga OS-disken är frånkopplad och används för replikering.
1. Endast ändringar mellan käll disken och mål disken synkroniseras. Skillnaderna beräknas genom att jämföra både diskarna och sedan överföras. Se nedan för att hitta den beräknade tiden för att slutföra återskyddet.
1. När synkroniseringen är klar börjar delta-replikeringen och en återställnings punkt skapas i linje med replikeringsprincipen.

När du utlöser ett skydds jobb och den virtuella mål datorn och diskarna inte finns inträffar följande:

1. Om den virtuella datorn använder hanterade diskar skapas replik diskar med `-ASRReplica` suffix. `-ASRReplica`Kopiorna används för replikering.
1. Om den virtuella datorn använder ohanterade diskar skapas replik diskar på mål lagrings kontot.
1. Hela diskarna kopieras från den misslyckade över-regionen till den nya mål regionen.
1. När synkroniseringen är klar börjar delta-replikeringen och en återställnings punkt skapas i linje med replikeringsprincipen.

#### <a name="estimated-time-to-do-the-reprotection"></a>Beräknad tid för att göra omskydden

I de flesta fall replikerar Azure Site Recovery inte fullständiga data till käll regionen.
Följande villkor avgör hur mycket data som replikeras:

1. Om den virtuella käll datorn tas bort, skadas eller inte är tillgänglig av någon anledning, till exempel en resurs grupp ändring/ta bort, sker en fullständig inledande replikering eftersom det inte finns några tillgängliga data i käll regionen som ska användas.
1. Om de virtuella käll datorerna är tillgängliga, beräknas bara differentiella genom att jämföra diskarna och sedan överföra dem. Kontrol lera tabellen nedan för att få den uppskattade tiden.

|Exempel situation | Åtgången tid för att skydda |
|---|---|
|Käll regionen har 1 virtuell dator med 1 TB standard disk.<br/>Endast 127 GB data används och resten av disken är tom.<br/>Disk typen är standard med 60 Mbit/s-genomflöde.<br/>Ingen data ändring efter redundans.| Ungefärlig tid: 60-90 minuter.<br/> Vid återskydd fyller Site Recovery kontroll summan för alla data. Detta fungerar på 45MBps, så den totala tiden det tar är 127 GB/45 MBps, ungefär 45 minuter.<br/>En del omkostnader krävs för att Site Recovery ska autoskalas, ungefär 20-30 minuter. |
|Käll regionen har 1 virtuell dator med 1 TB standard disk.<br/>Endast 127 GB data används och resten av disken är tom.<br/>Disk typen är standard med 60 Mbit/s-genomflöde.<br/>45 GB data ändringar efter redundans.| Ungefärlig tid: 2,5 – 3 timmar.<br/> Vid återskydd fyller Site Recovery kontroll summan för alla data. Detta fungerar på 45MBps, så den totala tiden det tar är 127 GB/45 MBps, ungefär 45 minuter.<br/>Överföringshastigheten är cirka 16% av genomflödet eller 9,6 MBps. Därför överförs tiden för att tillämpa ändringar på 45 GB som är 45 GB/9,6 MBps, ungefär 80 minuter.<br/>En del omkostnader krävs för att Site Recovery ska autoskalas, ungefär 20-30 minuter. |
|Käll regionen har 1 virtuell dator med 1 TB standard disk.<br/>Endast 20 GB data används och resten av disken är tom.<br/>Disk typen är standard med 60 Mbit/s-genomflöde.<br/>De inledande data på disken omedelbart efter redundansväxlingen var 15 GB. Det fanns 5 GB data ändring efter redundansväxlingen. Det totala antalet fyllda data är därför 20 GB.| Ungefärlig tid: 1 – 1,5 timmar.<br/>Eftersom de data som är ifyllda på disken är mindre än 10% av disk storleken utför vi en fullständig inledande replikering.<br/> Överföringshastigheten är cirka 16% av genomflödet eller 9,6 MBps. Därför kan överförings tiden för att tillämpa ändringar på 20 GB som är 20 GB/9,6 MBps, ungefär 36 minuter.<br/>En del omkostnader krävs för att Site Recovery ska autoskalas, ungefär 20-30 minuter. |
|Käll regionen har 1 virtuell dator med 1 TB Premium-disk.<br/>Endast 127 GB data används och resten av disken är tom.<br/>Disk typen är Premium med 200 Mbit/s genom strömning.<br/>Ingen data ändring efter redundans.| Ungefärlig tid: 2 timmar.<br/>Vid återskydd fyller Site Recovery kontroll summan för alla data. Detta fungerar på 25MBps (upp till 16% av disk data flödet), så den totala tiden det tar är 127 GB/25 MBps, ungefär 87 minuter.<br/>En del omkostnader krävs för att Site Recovery ska autoskalas, ungefär 20-30 minuter. |
|Käll regionen har 1 virtuell dator med 1 TB Premium-disk.<br/>Endast 127 GB data används och resten av disken är tom.<br/>Disk typen är Premium med 200 Mbit/s genom strömning.<br/>45 GB data ändringar efter redundans.| Ungefärlig tid: 2,5 – 3 timmar.<br/>Vid återskydd fyller Site Recovery kontroll summan för alla data. Detta fungerar på 25MBps (upp till 16% av disk data flödet), så den totala tiden det tar är 127 GB/25 MBps, ungefär 87 minuter.</br>Överföringshastigheten är cirka 16% av genomflödet eller 32MBps. Därför överförs tiden för att tillämpa ändringar på 45 GB som är 45 GB/32 Mbit/s, ungefär 24 minuter.<br/>En del omkostnader krävs för att Site Recovery ska autoskalas, ungefär 20-30 minuter. |
|Käll regionen har 1 virtuell dator med 1 TB Premium-disk.<br/>Endast 20 GB data används och resten av disken är tom.<br/>Disk typen är Premium med 200 Mbit/s genom strömning.<br/>De inledande data på disken omedelbart efter redundansväxlingen var 15 GB. Det fanns 5 GB data ändring efter redundansväxlingen. Totalt antal fyllda data är därför 20 GB| Ungefärlig tid: 30-45 minuter.<br/>Eftersom de data som är ifyllda på disken är mindre än 10% av disk storleken utför vi en fullständig inledande replikering.<br/>Överföringshastigheten är cirka 16% av genomflödet eller 32MBps. Därför ska överförings tiden för att tillämpa ändringar på 20 GB som är 20 GB/32 MBps, ungefär 11 minuter.<br/>Det krävs en del omkostnader för Site Recovery för automatisk skalning, ungefär 20-30 minuter |

När den virtuella datorn skyddas igen efter att ha växlat tillbaka till den primära regionen (dvs. om den virtuella datorn skyddas från primär region till DR-region) tas den virtuella mål datorn och de associerade NIC: erna bort.

När den virtuella datorn skyddas på nytt från DR-regionen till den primära regionen tar vi inte bort den primära virtuella datorn erstwhile och tillhör ande NIC (n).

## <a name="next-steps"></a>Nästa steg

När den virtuella datorn är skyddad kan du starta en redundansväxling. Redundansväxlingen stänger den virtuella datorn i den sekundära regionen och skapar och startar den virtuella datorn i den primära regionen med korta stillestånd under den här processen. Vi rekommenderar att du väljer lämplig tid för den här processen och att du kör ett redundanstest innan du påbörjar en fullständig redundans till den primära platsen. [Läs mer](site-recovery-failover.md) om Azure Site Recovery redundans.
