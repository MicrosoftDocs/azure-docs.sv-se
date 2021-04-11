---
title: Kör en NHyper-V haveri beredskap-granskning till en sekundär plats med Azure Site Recovery
description: Lär dig hur du kör en DR-granskning för virtuella Hyper-V-datorer i VMM-moln till ett sekundärt lokalt Data Center med hjälp av Azure Site Recovery.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 675190bee674c745494c2378712afa89bd43cbe4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727883"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Köra en DR-granskning för virtuella Hyper-V-datorer till en sekundär plats


Den här artikeln beskriver hur du gör en katastrof återställning (DR) för virtuella Hyper-V-datorer som hanteras i System Center Virtual Machine Manager V-moln (MM) till en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Du kan köra ett redundanstest för att verifiera din strategi för replikering och utföra en katastrof granskning utan data förlust eller avbrott. En redundanstest har ingen påverkan på den pågående replikeringen eller i produktions miljön. 

## <a name="how-do-test-failovers-work"></a>Hur fungerar redundanstest?

Du kör ett redundanstest från den primära platsen till den sekundära platsen. Om du bara vill kontrol lera att en virtuell dator växlar över kan du köra ett redundanstest utan att ange något på den sekundära platsen. Om du vill kontrol lera att redundanstestning fungerar som förväntat måste du konfigurera nätverk och infrastruktur på den sekundära platsen.
- Du kan köra ett redundanstest på en enskild virtuell dator eller på en [återställnings plan](site-recovery-create-recovery-plans.md).
- Du kan köra ett redundanstest utan ett nätverk, med ett befintligt nätverk eller med ett automatiskt skapat nätverk. Mer information om de här alternativen finns i tabellen nedan.
    - Du kan köra ett redundanstest utan ett nätverk. Det här alternativet är användbart om du bara vill kontrol lera att en virtuell dator kunde redundansväxla, men du kan inte verifiera nätverks konfigurationen.
    - Kör redundansväxlingen med ett befintligt nätverk. Vi rekommenderar att du inte använder ett produktions nätverk.
    - Kör redundansväxlingen och låt Site Recovery automatiskt skapa ett test nätverk. I detta fall skapar Site Recovery nätverket automatiskt och rensar det när redundanstestning är klar.
- Du måste välja en återställnings punkt för redundanstest: 
    - **Senaste bearbetning**: det här alternativet Miss lyckas med en virtuell dator till den senaste återställnings punkten som bearbetats av Site Recovery. Med det här alternativet läggs ingen tid på bearbetning av data, så den ger ett lågt mål för återställningstiden.
    - **Senaste program – konsekvent**: det här alternativet växlar över en virtuell dator till den senaste programkonsekventa återställnings punkten som bearbetas av Site Recovery. 
    - **Senaste**: det här alternativet bearbetar först alla data som har skickats till Site Recovery-tjänsten för att skapa en återställnings punkt för varje virtuell dator innan den växlar över till den. Det här alternativet ger lägsta återställnings punkt mål (återställnings punkt mål), eftersom den virtuella datorn som skapades efter redundansväxlingen har alla data som repliker ATS till Site Recovery När redundansväxlingen utlöstes.
    - **Senaste bearbetade multi-VM**: tillgängligt för återställnings planer som innehåller en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer med inställningen aktive rad redundans till den senaste vanliga återställnings punkten med flera virtuella datorer. Andra virtuella datorer växlar över till den senaste bearbetade återställnings punkten.
    - **Senaste app med flera virtuella datorer – konsekvent**: det här alternativet är tillgängligt för återställnings planer med en eller flera virtuella datorer som har konsekvens för flera virtuella datorer aktiverade. Virtuella datorer som ingår i en replikeringsgrupp växlar över till den senaste vanliga programkonsekventa multi-VM-programkonsekventa återställnings punkt. Andra virtuella datorer växlar över till den senaste programkonsekventa återställnings punkten.
    - **Anpassad**: Använd det här alternativet om du vill redundansväxla en specifik virtuell dator till en viss återställnings punkt.



## <a name="prepare-networking"></a>Förbereda nätverk

När du kör ett redundanstest uppmanas du att välja nätverks inställningar för test replik datorer, som sammanfattas i tabellen.

| **Alternativ** | **Information** |
| --- | --- |
| **Ingen** | Den virtuella test datorn skapas på den värd där den virtuella replik datorn finns. Den har inte lagts till i molnet och är inte ansluten till något nätverk.<br/><br/> Du kan ansluta datorn till ett virtuellt dator nätverk när det har skapats.|
| **Använd befintlig** | Den virtuella test datorn skapas på den värd där den virtuella replik datorn finns. Den har inte lagts till i molnet.<br/><br/>Skapa ett virtuellt dator nätverk som är isolerat från produktions nätverket.<br/><br/>Om du använder ett VLAN-baserat nätverk rekommenderar vi att du skapar ett separat logiskt nätverk (som inte används i produktion) i VMM för detta ändamål. Det här logiska nätverket används för att skapa virtuella dator nätverk för redundanstest.<br/><br/>Det logiska nätverket måste vara kopplat till minst ett av nätverkskorten för alla Hyper-V-servrar som är värdar för virtuella datorer.<br/><br/>För logiska VLAN-nätverk ska de nätverks platser som du lägger till i det logiska nätverket isoleras.<br/><br/>Om du använder ett logiskt nätverk för Windows-nätverksvirtualisering skapas automatiskt isolerade virtuella dator nätverk i Azure Site Recovery. |
| **Skapa ett nätverk** | Ett tillfälligt test nätverk skapas automatiskt baserat på den inställning som du anger i det **logiska nätverket** och dess relaterade nätverks platser.<br/><br/> Redundansväxling kontrollerar att virtuella datorer har skapats.<br/><br/> Du bör använda det här alternativet om en återställnings plan använder mer än ett virtuellt dator nätverk.<br/><br/> Om du använder Windows-nätverk för nätverksvirtualisering kan det här alternativet automatiskt skapa virtuella dator nätverk med samma inställningar (undernät och IP-adresspooler) i nätverket på den virtuella replik datorn. Dessa virtuella dator nätverk rensas automatiskt när redundanstestning är klar.<br/><br/> Den virtuella test datorn skapas på den värd där den virtuella replik datorn finns. Den har inte lagts till i molnet.|

### <a name="best-practices"></a>Bästa praxis

- Om du testar ett produktions nätverk orsakar det avbrott i produktions arbets belastningar. Be användarna att inte använda relaterade appar när en haveri beredskap-granskning pågår.

- Test nätverket behöver inte matcha den logiska VMM-nätverks typ som används för redundanstest. Men vissa kombinationer fungerar inte:

     - Om repliken använder DHCP och VLAN-baserad isolering behöver inte det virtuella dator nätverket för repliken en statisk IP-adresspool. Så att det inte går att använda Windows-nätverksvirtualisering för redundanstest eftersom inga adresspooler är tillgängliga. 
        
     - Redundanstest fungerar inte om replik nätverket inte använder någon isolering och test nätverket använder Windows-nätverksvirtualisering. Detta beror på att det inte finns undernät som krävs för att skapa ett Windows-nätverk för nätverksvirtualisering i nätverket.
        
- Vi rekommenderar att du inte använder nätverket som du har valt för nätverks mappning för redundanstest.

- Hur virtuella replik datorer är anslutna till mappade VM-nätverk efter redundansväxlingen beror på hur det virtuella dator nätverket har kon figurer ATS i VMM-konsolen.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>VM-nätverk som kon figurer ATS utan isolering eller VLAN-isolering

Observera följande om ett virtuellt dator nätverk har kon figurer ATS i VMM utan isolering eller VLAN-isolering:

- Om DHCP har definierats för det virtuella dator nätverket ansluts den virtuella replik datorn till VLAN-ID: t via inställningarna som anges för nätverks platsen i det associerade logiska nätverket. Den virtuella datorn tar emot sin IP-adress från den tillgängliga DHCP-servern.
- Du behöver inte definiera en statisk IP-adresspool för det virtuella mål nätverket. Om en statisk IP-adresspool används för det virtuella dator nätverket ansluts den virtuella replik datorn till VLAN-ID: t via inställningarna som anges för nätverks platsen i det associerade logiska nätverket.
- Den virtuella datorn tar emot sin IP-adress från poolen som har definierats för det virtuella dator nätverket. Om en statisk IP-adresspool inte har definierats på det virtuella mål nätverket kommer IP-adressallokering att Miss fördelade. Skapa IP-adresspoolen på både käll-och mål-VMM-servrarna som du ska använda för skydd och återställning.

### <a name="vm-network-with-windows-network-virtualization"></a>VM-nätverk med Windows-nätverksvirtualisering

Observera följande om ett virtuellt dator nätverk har kon figurer ATS i VMM med Windows-nätverksvirtualisering:

- Du bör definiera en statisk pool för det virtuella mål nätverket, oavsett om det virtuella käll nätverket är konfigurerat att använda DHCP eller en statisk IP-adresspool. 
- Om du definierar DHCP fungerar VMM-målservern som en DHCP-server och tillhandahåller en IP-adress från poolen som har definierats för det virtuella mål nätverket.
- Om användningen av en statisk IP-adresspool har definierats för käll servern allokerar VMM-servern en IP-adress från poolen. I båda fallen går det inte att tilldela IP-adresser om en statisk IP-adresspool inte har definierats.



## <a name="prepare-the-infrastructure"></a>Förbered infrastrukturen

Om du bara vill kontrol lera att en virtuell dator kan redundansväxla kan du köra ett redundanstest utan en infrastruktur. Om du vill göra en fullständig DR-Test för att testa redundansväxlingen måste du förbereda infrastrukturen på den sekundära platsen:

- Om du kör ett redundanstest med ett befintligt nätverk förbereder du Active Directory, DHCP och DNS i det nätverket.
- Om du kör ett redundanstest med alternativet för att skapa ett virtuellt dator nätverk automatiskt, måste du lägga till infrastruktur resurser i det automatiskt skapade nätverket innan du kör redundanstest. I en återställnings plan kan du förenkla detta genom att lägga till ett manuellt steg före grupp-1 i återställnings planen som du ska använda för redundanstest. Lägg sedan till infrastruktur resurserna i det automatiskt skapade nätverket innan du kör redundanstest.


### <a name="prepare-dhcp"></a>Förbered DHCP
Om de virtuella datorerna som ingår i redundanstestning använder DHCP, skapar du en test-DHCP-server i det isolerade nätverket i syfte att testa redundans.


### <a name="prepare-active-directory"></a>Förbered Active Directory
Om du vill köra ett redundanstest för program testning behöver du en kopia av produktions Active Directorys miljön i din test miljö. Mer information hittar du i [överväganden för redundanstest för Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Förbered DNS
Förbered en DNS-server för redundanstest enligt följande:

* **DHCP**: om virtuella datorer använder DHCP bör IP-adressen för test-DNS uppdateras på test-DHCP-servern. Om du använder en nätverks typ som är Windows-nätverksvirtualisering fungerar VMM-servern som DHCP-server. IP-adressen för DNS bör därför uppdateras i nätverket för redundanstest. I det här fallet registrerar de virtuella datorerna sig själva på den relevanta DNS-servern.
* **Statisk adress**: om virtuella datorer använder en statisk IP-adress ska IP-adressen för test-DNS-servern uppdateras i nätverket för redundanstest. Du kan behöva uppdatera DNS med IP-adressen för de virtuella test datorerna. Du kan använda följande exempel skript för detta ändamål:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Den här proceduren beskriver hur du kör ett redundanstest för en återställnings plan. Du kan också köra redundansväxlingen för en enskild virtuell dator på fliken **Virtual Machines** .

1. Välj **återställnings planer**  >  *recoveryplan_name*. Klicka på redundanstest för **redundans**  >  .
2. På bladet **redundanstest** anger du hur virtuella repliker ska anslutas till nätverk efter redundanstest.
3. Spåra förlopp för redundansväxling på fliken **jobb** .
4. När redundansväxlingen är klar kontrollerar du att de virtuella datorerna har startats.
5. När du är klar klickar du på **rensning** av redundanstest i återställnings planen. I **Kommentarer** skriver du och sparar eventuella observationer från redundanstestningen. Det här steget tar bort alla virtuella datorer och nätverk som har skapats av Site Recovery under redundanstest. 

![Redundanstest](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Den IP-adress som ges till en virtuell dator under redundanstestning är samma IP-adress som den virtuella datorn tar emot för en planerad eller oplanerad redundansväxling (vilket förutsätter att IP-adressen är tillgänglig i nätverket för redundanstest). Om samma IP-adress inte är tillgänglig i nätverket för redundanstest, får den virtuella datorn en annan IP-adress som är tillgänglig i nätverket för redundanstest.



### <a name="run-a-test-failover-to-a-production-network"></a>Köra ett redundanstest till ett produktions nätverk

Vi rekommenderar att du inte kör ett redundanstest till nätverket för produktions återställnings platsen som du angav under nätverks mappning. Tänk på följande när du behöver validera nätverks anslutningar från slut punkt till slut punkt i en misslyckad virtuell dator:

* Kontrol lera att den primära virtuella datorn är avstängd när du utför redundanstest. Om du inte gör det körs två virtuella datorer med samma identitet i samma nätverk på samma gång. Den situationen kan leda till oönskade konsekvenser.
* Eventuella ändringar som du gör i de virtuella datorerna för redundanstest försvinner när du rensar de virtuella datorerna för redundanstest. Dessa ändringar replikeras inte tillbaka till de primära virtuella datorerna.
* Om du testar detta leder det till stillestånds tid för ditt produktions program. Be användare av programmet att inte använda programmet när DR-granskningen pågår.  


## <a name="next-steps"></a>Nästa steg
När du har kört en DR-granskning kan du [köra en fullständig redundans](site-recovery-failover.md).



