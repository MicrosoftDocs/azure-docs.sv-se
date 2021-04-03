---
title: Katastrof återställnings arkitektur för VMware VM i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används när du konfigurerar haveri beredskap för lokala virtuella VMware-datorer till Azure med Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 5cf4dc5123040fd2af8efe54153867a8709fe1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652236"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Katastrof återställnings arkitektur för VMware till Azure

Den här artikeln beskriver arkitekturen och processerna som används när du distribuerar haveri beredskap, redundans och återställning av virtuella VMware-datorer mellan en lokal VMware-plats och Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .


## <a name="architectural-components"></a>Arkitekturkomponenter

Följande tabell och grafik ger en övergripande bild av de komponenter som används för VMware haveri beredskap till Azure.

**Komponent** | **Krav** | **Information**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage konto för cache, hanterad disk och Azure-nätverk. | Replikerade data från lokala virtuella datorer lagras i Azure Storage. Virtuella Azure-datorer skapas med replikerade data när du kör en redundansväxling från en lokal plats till Azure. Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Konfigurations Server dator** | En enda lokal dator. Vi rekommenderar att du kör det som en virtuell VMware-dator som kan distribueras från en Hämtad OVF-mall.<br/><br/> Datorn kör alla lokala Site Recovery-komponenter som innehåller konfigurations servern, processervern och huvud mål servern. | **Konfigurations Server**: samordnar kommunikationen mellan både lokalt och Azure och hanterar datareplikering.<br/><br/> **Processerver**: installeras som standard på konfigurations servern. Den tar emot replikeringsdata; optimerar den med cachelagring, komprimering och kryptering; och skickar den till Azure Storage. Processervern installerar också mobilitetstjänsten Azure Site Recovery på de virtuella datorer du vill replikera, samt utför automatisk identifiering av lokala virtuella VMware-datorer. När distributionen växer kan du lägga till ytterligare separata process servrar för att hantera större volymer av replikeringstrafiken.<br/><br/> **Huvud mål server**: installeras som standard på konfigurations servern. Den hanterar replikeringsdata under återställning efter fel från Azure. För stora distributioner kan du lägga till ytterligare en separat huvud mål server för återställning efter fel.
**VMware-servrar** | Virtuella VMware-datorer finns på lokala vSphere ESXi-servrar. Vi rekommenderar att en vCenter-Server hanterar värdarna. | När du Site Recovery distribution lägger du till VMware-servrar i Recovery Services-valvet.
**Replikerade datorer** | Mobilitets tjänsten är installerad på varje virtuell VMware-dator som du replikerar. | Vi rekommenderar att du tillåter automatisk installation från processervern. Alternativt kan du installera tjänsten manuellt eller använda en automatiserad distributions metod, till exempel Configuration Manager.

![Diagram som visar arkitektur relationer för VMware till Azure-replikering.](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>Konfigurera utgående nätverks anslutning

För att Site Recovery ska fungera som förväntat måste du ändra den utgående nätverks anslutningen så att din miljö kan replikeras.

> [!NOTE]
> Site Recovery har inte stöd för en autentiseringsproxy för att styra nätverksanslutningar.

### <a name="outbound-connectivity-for-urls"></a>Utgående anslutning för webbadresser

Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till följande URL: er:

| **Namn**                  | **Kommersiellt**                               | **Myndigheter**                                 | **Beskrivning** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| Replikering               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |

För en fullständig lista över URL: er som ska filtreras för kommunikation mellan lokala Azure Site Recovery infrastruktur och Azure-tjänster, se [avsnittet nätverks krav i artikeln](vmware-azure-deploy-configuration-server.md#prerequisites)krav.

## <a name="replication-process"></a>Replikeringsprocessen

1. När du aktiverar replikering för en virtuell dator börjar den inledande replikeringen till Azure Storage med den angivna replikeringsprincipen. . Tänk på följande:
    - För virtuella VMware-datorer är replikeringen block-level, nästan kontinuerlig, med mobilitets tjänst agenten som körs på den virtuella datorn.
    - Princip inställningar för replikering tillämpas:
        - Återställnings **tröskel**. Den här inställningen påverkar inte replikeringen. Det hjälper till med övervakning. En händelse höjs och eventuellt ett e-postmeddelande som skickas, om den aktuella återställningen överskrider den tröskel gräns som du anger.
        - **Kvarhållning av återställnings punkt**. Den här inställningen anger hur långt bakåt i tiden du vill gå när ett avbrott inträffar. Maximal kvarhållning i Premium Storage är 24 timmar. På standard lagring är det 72 timmar. 
        - **Programkonsekventa ögonblicks bilder**. En programkonsekvent ögonblicks bild kan utföras var 1 till 12 timmar, beroende på ditt programs behov. Ögonblicks bilder är standardiserade Azure Blob-ögonblicksbilder. Mobilitets agenten som körs på en virtuell dator begär en VSS-ögonblicksbild i enlighet med den här inställningen och bok märken som är punkt-i tid som en programkonsekvent punkt i replik data strömmen.

2. Trafiken replikeras till offentliga slut punkter i Azure Storage via Internet. Alternativt kan du använda Azure-ExpressRoute med [Microsoft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Det finns inte stöd för att replikera trafik över ett virtuellt privat nätverk (VPN) från plats till plats från en lokal plats till Azure.
3. Vid inledande replikering ser du till att hela data på datorn vid tidpunkten för att aktivera replikering skickas till Azure. När den inledande replikeringen har slutförts börjar replikeringen av delta ändringar till Azure. Spårade ändringar för en dator skickas till processervern.
4. Kommunikationen sker på följande sätt:

    - Virtuella datorer kommunicerar med den lokala konfigurations servern på port HTTPS 443 inkommande, för hantering av replikering.
    - Konfigurations servern dirigerar replikeringen med Azure via port HTTPS 443 utgående.
    - Virtuella datorer skickar replikeringsdata till processervern (körs på konfigurations serverdatorn) på port HTTPS 9443 inkommande. Den här porten kan ändras.
    - Processervern tar emot replikeringsdata, optimerar och krypterar den och skickar den till Azure Storage via port 443 utgående.
5. Replikeringsdata loggar första marken i ett cache Storage-konto i Azure. Dessa loggar bearbetas och data lagras på en Azure-hanterad disk (kallas för automatisk start disk). Återställnings punkterna skapas på den här disken.

![Diagram över VMware till Azure-replikeringen.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Process för omsynkronisering

1. Vid den första replikeringen eller när delta ändringar överförs kan det uppstå problem med nätverks anslutningen mellan käll datorn för att bearbeta servern eller mellan processervern och Azure. Något av dessa kan leda till problem vid data överföring till Azure tillfälligt.
2. För att undvika problem med data integritet och minimera kostnader för data överföring, Site Recovery Markera en dator för omsynkronisering.
3. En dator kan också markeras för omsynkronisering i situationer som följande för att upprätthålla konsekvensen mellan käll datorn och data som lagras i Azure
    - Om en dator underbörjar tvingas avstängning
    - Om en dator underbörjar konfigurations ändringar som storlek på disken (ändrar storleken på disken från 2 TB till 4 TB)
4. Omsynkronisering skickar endast delta data till Azure. Data överföring mellan lokala och Azure genom att minimeras genom beräkning av data kontroll summor för data mellan käll datorer och data som lagras i Azure.
5. Omsynkronisering av standard är schemalagd att köras automatiskt utanför kontors tid. Om du inte vill vänta på omsynkroniseringen av standardvärdet utanför timmar kan du synkronisera om en virtuell dator manuellt. Det gör du genom att gå till Azure Portal, välja den virtuella datorn > **omsynkronisera**.
6. Om standard omsynkroniseringen Miss lyckas utanför kontors tid och en manuell åtgärd krävs, genereras ett fel på den angivna datorn i Azure Portal. Du kan lösa felet och utlösa omsynkroniseringen manuellt.
7. När omsynkroniseringen har slutförts kommer replikering av delta ändringar att återupptas.

## <a name="replication-policy"></a>Replikeringsprincip 

När du aktiverar Azure VM-replikering skapar Site Recovery en ny replikeringsprincip med de standardinställningar som sammanfattas i tabellen.

**Principinställning** | **Information** | **Standardvärde**
--- | --- | ---
**Kvarhållning av återställnings punkt** | Anger hur länge Site Recovery behåller återställnings punkter | 24 timmar
**Frekvens för programkonsekventa ögonblicks bilder** | Hur ofta Site Recovery tar en programkonsekvent ögonblicks bild. | Var fjärde timme

### <a name="managing-replication-policies"></a>Hantera replikeringsprinciper

Du kan hantera och ändra standardinställningarna för replikeringsprinciper enligt följande:
- Du kan ändra inställningarna när du aktiverar replikering.
- Du kan när som helst skapa en replikeringsprincip och sedan använda den när du aktiverar replikering.

### <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

Om du vill att virtuella datorer ska replikeras tillsammans och har delade krasch-konsekventa och programkonsekventa återställnings punkter vid redundans kan du samla ihop dem i en replikeringsgrupp. Konsekvens för flera virtuella datorer påverkar arbets Belastningens prestanda och bör endast användas för virtuella datorer som kör arbets belastningar som behöver konsekvens på alla datorer. 



## <a name="snapshots-and-recovery-points"></a>Ögonblicksbilder och återställningspunkter

Återställnings punkter skapas från ögonblicks bilder av virtuella dator diskar som tas vid en viss tidpunkt. När du växlar över en virtuell dator använder du en återställnings punkt för att återställa den virtuella datorn på mål platsen.

Vid växling vid fel vill vi vanligt vis se till att den virtuella datorn inte startar utan skada eller data förlust, och att VM-data är konsekventa för operativ systemet och för appar som körs på den virtuella datorn. Detta beror på vilken typ av ögonblicks bilder som har tagits.

Site Recovery tar ögonblicks bilder enligt följande:

1. Site Recovery tar krasch-konsekventa ögonblicks bilder av data som standard och programkonsekventa ögonblicks bilder om du anger en frekvens för dem.
2. Återställnings punkter skapas från ögonblicks bilderna och lagras i enlighet med inställningarna för kvarhållning i replikeringsprincipen.

### <a name="consistency"></a>Konsekvens

I följande tabell beskrivs olika typer av konsekvens.

### <a name="crash-consistent"></a>Krasch-konsekvent

**Beskrivning** | **Information** | **Rekommendation**
--- | --- | ---
En krasch-konsekvent ögonblicks bild fångar upp data som fanns på disken när ögonblicks bilden togs. Det innehåller inte något i minnet.<br/><br/> Den innehåller motsvarigheten till data på disken som kan finnas om den virtuella datorn kraschade eller om ström sladden hämtades från servern vid det ögonblick då ögonblicks bilden togs.<br/><br/> En krasch konsekvens garanterar inte data konsekvens för operativ systemet eller för appar på den virtuella datorn. | Site Recovery skapar kraschbaserade återställnings punkter var femte minut som standard. Den här inställningen kan inte ändras.<br/><br/>  | Idag kan de flesta appar återställa sig väl från kraschbaserade punkter.<br/><br/> Kraschbaserade återställnings punkter är vanligt vis tillräckligt för replikering av operativ system och appar som DHCP-servrar och utskrifts servrar.

### <a name="app-consistent"></a>Program – konsekvent

**Beskrivning** | **Information** | **Rekommendation**
--- | --- | ---
Programkonsekventa återställnings punkter skapas från programkonsekventa ögonblicks bilder.<br/><br/> En programkonsekvent ögonblicks bild innehåller all information i en krasch-konsekvent ögonblicks bild, plus alla data i minnet och transaktioner som pågår. | Programkonsekventa ögonblicks bilder använder tjänsten Volume Shadow Copy (VSS):<br/><br/>   1) Azure Site Recovery använder metoden kopiera endast säkerhets kopiering (VSS_BT_COPY) som inte ändrar säkerhets kopierings tid och sekvensnummer för Microsoft SQLs transaktions logg </br></br> 2) när en ögonblicks bild initieras utför VSS en ko-åtgärd (kopiering vid skrivning) på volymen.<br/><br/>   3) innan den utför Ko informerar VSS varje app på datorn att den behöver tömma sina minnesresidenta data till disk.<br/><br/>   4) VSS tillåter sedan säkerhets kopierings-och haveri beredskap (i det här fallet Site Recovery) att läsa ögonblicks bild data och fortsätta. | Programkonsekventa ögonblicks bilder tas i enlighet med den frekvens som du anger. Den här frekvensen bör alltid vara mindre än du anger för att behålla återställnings punkter. Om du till exempel behåller återställnings punkter med standardinställningen 24 timmar bör du ange frekvensen till mindre än 24 timmar.<br/><br/>De är mer komplexa och tar längre tid än krasch-konsekventa ögonblicks bilder.<br/><br/> De påverkar prestanda för appar som körs på en virtuell dator som är aktive rad för replikering. 

## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

När replikeringen har kon figurer ATS och du kör en haveri beredskap (testa redundans) för att kontrol lera att allt fungerar som förväntat, kan du köra redundans och återställning efter fel när du behöver.

1. Det går inte att köra en enskild dator eller så kan du skapa en återställnings plan för att redundansväxla flera virtuella datorer på samma gång. Fördelen med en återställnings plan i stället för en enskild dators redundans är:
    - Du kan utforma program beroenden genom att inkludera alla virtuella datorer i appen i en enda återställnings plan.
    - Du kan lägga till skript, Azure-Runbooks och pausa för manuella åtgärder.
2. När du har utlöst den inledande redundansväxlingen genomför du den för att börja komma åt arbets belastningen från den virtuella Azure-datorn.
3. När din primära lokala plats är tillgänglig igen, kan du förbereda för att återställa. För att återställa måste du konfigurera en infrastruktur för återställning efter fel, inklusive:

    * **Tillfällig processerver i Azure**: om du vill återställa från Azure konfigurerar du en virtuell Azure-dator så att den fungerar som en processerver för att hantera replikering från Azure. Du kan ta bort den här virtuella datorn när återställningen är klar.
    * **VPN-anslutning**: om du vill återställa behöver du en VPN-anslutning (eller ExpressRoute) från Azure-nätverket till den lokala platsen.
    * **Separat huvud mål server**: som standard hanterar huvud mål servern som installerades med konfigurations servern på den lokala virtuella VMware-datorn återställning efter fel. Om du behöver återställa stora mängder trafik måste du konfigurera en separat lokal huvud mål server för det här ändamålet.
    * **Återställningsprincip**: Om du vill replikera tillbaka till din lokala plats behöver du en återställningsprincip. Den här principen skapas automatiskt när du skapar en replikeringsprincip från lokal plats till Azure.
4. Efter att komponenterna är på plats sker återställning efter fel i tre åtgärder:

    - Steg 1: skydda virtuella datorer i Azure så att de replikeras från Azure tillbaka till lokala virtuella VMware-datorer.
    -  Steg 2: kör en redundansväxling till den lokala platsen.
    - Steg 3: när arbets belastningarna har misslyckats igen återaktiverar du replikeringen för de lokala virtuella datorerna.
    
 

![Diagram över VMware-återställning från Azure.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Nästa steg

Följ [den här självstudien](vmware-azure-tutorial.md) för att aktivera VMware till Azure-replikering.
