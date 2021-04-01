---
title: Om återställnings planer i Azure Site Recovery
description: Lär dig mer om återställnings planer i Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343922"
---
# <a name="about-recovery-plans"></a>Om återställningsplaner

Den här artikeln innehåller en översikt över återställnings planer i [Azure Site Recovery](site-recovery-overview.md).

En återställnings plan samlar in datorer i återställnings grupper i syfte att redundansväxla. Med en återställnings plan kan du definiera en systematisk återställnings process genom att skapa små oberoende enheter som du kan redundansväxla. En enhet representerar vanligt vis en app i din miljö.

- En återställnings plan definierar hur datorer växlar över och i vilken ordning de startar efter redundansväxlingen.
- Återställnings planer kan användas för både redundans till och återställning efter fel från Azure.
- Upp till 100 skyddade instanser kan läggas till i en återställnings plan.
- Du kan anpassa en plan genom att lägga till order, instruktioner och uppgifter till den.
- När en plan har definierats kan du köra en redundansväxling på den.
- Datorer kan refereras till i flera återställnings planer, där efterföljande planer hoppar över distribution/start av en dator om den tidigare har distribuerats med en annan återställnings plan.



### <a name="why-use-a-recovery-plan"></a>Varför ska jag använda en återställnings plan?

Använd återställnings planer för att:

* Modellera en app runt dess beroenden.
* Automatisera återställnings uppgifter för att minska återställnings tids målet (RTO).
* Kontrol lera att du är för beredd för migrering eller haveri beredskap genom att se till att dina appar ingår i en återställnings plan.
* Kör redundanstest på återställnings planer för att säkerställa att haveri beredskap eller migreringen fungerar som förväntat.


## <a name="model-apps"></a>Modell program 
Du kan planera och skapa en återställnings grupp för att avbilda appar-/regionsspecifika egenskaper. Vi kan till exempel tänka på ett typiskt program på tre nivåer med en SQL Server-Server del, mellanprogram och en webb klient del. Normalt anpassar du återställnings planen så att datorerna på varje nivå startar i rätt ordning efter redundansväxlingen.

- SQL-Dataservern ska starta först, mellanliggande nästa och slutligen webb klient delen.
- Den här start ordningen säkerställer att appen fungerar när den senaste datorn startar.
- Den här ordningen säkerställer att när mellanprogram startar och försöker ansluta till SQL Server nivå körs redan SQL Servers nivån. 
- Den här ordningen hjälper också till att se till att frontend-servern börjar sist, så att slutanvändarna inte ansluter till appens URL innan alla komponenter är igång och appen är redo att ta emot begär Anden.

Om du vill skapa den här ordningen lägger du till grupper i återställnings gruppen och lägger till datorer i grupperna.
- Om order anges används ordningsföljd. Åtgärder körs parallellt på lämpligt sätt för att förbättra program återställnings RTO.
- Datorer i en enda grupp växlar över parallellt.
- Datorer i olika grupper växlar över i grupp ordning, så att endast datorer i grupp 2 startar sin redundans när alla datorer i grupp 1 har redundansväxlats och startat.

    ![Exempel på återställnings plan](./media/recovery-plan-overview/rp.png)

Med den här anpassningen på plats händer följande när du kör en redundansväxling i återställnings planen: 

1. Ett avstängnings steg försöker stänga av de lokala datorerna. Undantaget är om du kör ett redundanstest, i vilket fall den primära platsen fortsätter att köras. 
2. Avstängningen utlöser en parallell redundansväxling av alla datorer i återställnings planen.
3. Redundansväxlingen förbereder virtuella dator diskar med replikerade data.
4. Start grupper körs i ordning och startar datorerna i varje grupp. Först, grupp 1 körs, sedan grupp 2 och slutligen grupp 3. Om det finns fler än en dator i en grupp börjar alla datorer parallellt.


## <a name="automate-tasks-in-recovery-plans"></a>Automatisera uppgifter i återställnings planer

Det kan vara en komplicerad uppgift att återskapa stora program. Manuella steg gör processen fel och den person som kör redundansväxlingen kanske inte är medveten om all app-erna. Du kan använda en återställnings plan för att införa order och automatisera de åtgärder som krävs i varje steg, använda Azure Automation runbooks för redundans till Azure eller skript. För uppgifter som inte kan automatiseras kan du infoga pauser för manuella åtgärder i återställnings planer. Det finns några typer av uppgifter som du kan konfigurera:

* **Aktiviteter på den virtuella Azure-datorn efter redundansväxlingen**: när du växlar över till Azure måste du vanligt vis utföra åtgärder så att du kan ansluta till den virtuella datorn efter redundansväxlingen. Exempel: 
    * Skapa en offentlig IP-adress på den virtuella Azure-datorn.
    * Tilldela en nätverks säkerhets grupp till nätverkskortet på den virtuella Azure-datorn.
    * Lägg till en belastningsutjämnare i en tillgänglighets uppsättning.
* **Aktiviteter i den virtuella datorn efter redundansväxlingen**: dessa aktiviteter omkonfigurerar vanligt vis appen som körs på datorn, så att den fortsätter att fungera korrekt i den nya miljön. Exempel:
    * Ändra databas anslutnings strängen i datorn.
    * Ändra webb Server konfigurationen eller reglerna.


### <a name="run-a-test-failover-on-recovery-plans"></a>Kör ett redundanstest på återställnings planer

Du kan använda en återställnings plan för att utlösa redundanstest. Använd följande metod tips:

- Slutför alltid ett redundanstest på en app innan du kör en fullständig redundansväxling. Med redundanstest kan du kontrol lera om appen kommer upp på återställnings platsen.
- Om du tycker att du har missat något, utlöser du en rensning och kör sedan redundanstestningen igen. 
- Kör ett redundanstest flera gånger tills du är säker på att appen återställs smidigt.
- Eftersom varje app är unik måste du skapa återställnings planer som anpassas för varje program och köra ett redundanstest på var och en.
- Appar och deras beroenden ändras ofta. Se till att återställnings planer är uppdaterade genom att köra ett redundanstest för varje app varje kvartal.

    ![Skärm bild av ett exempel på en test återställnings plan i Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Titta på en video om återställnings plan

Titta på en snabb exempel video som visar en redundansväxling för en återställnings plan för en WordPress-app med två nivåer.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Nästa steg

- [Skapa](site-recovery-create-recovery-plans.md) en återställnings plan.
- [Kör](site-recovery-failover.md) redundans. 
