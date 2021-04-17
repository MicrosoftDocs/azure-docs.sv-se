---
title: Hög tillgänglighet och haveriberedskap för Oracle på BareMetal
description: Lär dig mer om hög tillgänglighet och haveriberedskap för Oracle på Azure BareMetal-infrastruktur.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590348"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Hög tillgänglighet och haveriberedskap för Oracle på BareMetal

I den här artikeln tittar vi på grunderna för hög tillgänglighet och haveriberedskap. Sedan introducerar vi hur du kan uppnå hög tillgänglighet och haveriberedskap i en Oracle-miljö i BareMetal-infrastrukturen.

## <a name="high-availability-vs-disaster-recovery"></a>Hög tillgänglighet jämfört med haveriberedskap

Både hög tillgänglighet och haveriberedskap ger täckning, men från olika typer av fel. De använder olika funktioner och alternativ för Oracle Database.

Hög tillgänglighet gör att ett system kan lösa flera fel utan att programmets användarupplevelse påverkas. Vanliga egenskaper hos ett system med hög tillgänglig omfattar:

- Redundant maskinvara som inte har någon enskild felpunkt.
- Automatisk återställning från icke-kritiska fel, till exempel misslyckade diskenheter eller felaktiga nätverkskablar.
- Möjligheten att rulla maskin- och programvaruändringar utan någon märkbar inverkan på bearbetningen.
- Uppfyller eller överskrider målen för återställningstid (RTO) och mål för återställningspunkt (RPO).

Den vanligaste funktionen i Oracle som används för hög tillgänglighet är [Oracle Real Application Clusters (RAC).](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)

Haveriberedskap skyddar dig mot oåterkalleliga lokaliserade fel som skulle skada din primära strategi för hög tillgänglighet. I Oracle-ekosystemet tillhandahålls det via databasreplikering, även kallat [Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Nästa steg

Läs mer om funktioner med hög tillgänglighet för Oracle:

> [!div class="nextstepaction"]
> [Funktioner för hög tillgänglighet för Oracle på BareMetal-infrastruktur](high-availability-features.md)
