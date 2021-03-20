---
title: Katastrof återställnings arkitektur för Hyper-V i Azure Site Recovery
description: Den här artikeln innehåller en översikt över komponenter och arkitektur som används för att distribuera haveri beredskap för lokala virtuella Hyper-V-datorer (utan VMM) till Azure med tjänsten Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c5025b83619b505728bfdf5c4e1ccc81d3bb225e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654769"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Katastrof återställnings arkitektur för Hyper-V till Azure


Den här artikeln beskriver arkitekturen och processerna som används när du replikerar, växlar över och återställer virtuella Hyper-V-datorer mellan lokala Hyper-V-värdar och Azure med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

Hyper-V-värdar kan också hanteras i System Center Virtual Machine Manager (VMM) privata moln.



## <a name="architectural-components---hyper-v-without-vmm"></a>Arkitektur komponenter – Hyper-V utan VMM

Följande tabell och grafik ger en övergripande bild av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar inte hanteras av VMM.

**Komponent** | **Krav** | **Information**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala VM-arbetsbelastningar lagras i lagrings kontot. Virtuella Azure-datorer skapas med replikerade arbets belastnings data när redundans från den lokala platsen inträffar.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**Hyper-V** | Under Site Recovery distributionen samlar du in Hyper-V-värdar och-kluster på Hyper-V-platser. Du installerar Azure Site Recovery Provider och Recovery Services agent på varje fristående Hyper-V-värd eller på varje Hyper-V-klusternod. | Providern samordnar replikeringen med Site Recovery-tjänsten via Internet. Recovery Services-agenten hanterar datareplikeringen.<br/><br/> Kommunikation från både providern och agenten är säker och krypterad. Replikerade data i Azure-lagring krypteras också.
**Hyper-V:s virtuella datorer** | En eller flera virtuella datorer som körs på Hyper-V. | Inget måste uttryckligen installeras på virtuella datorer.


**Arkitektur för Hyper-V till Azure (utan VMM)**

![Diagram över lokal Hyper-V-plats till Azure-arkitektur utan VMM.](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Arkitektur komponenter – Hyper-V med VMM

Följande tabell och grafik ger en övergripande bild av de komponenter som används för Hyper-V-replikering till Azure, när Hyper-V-värdar hanteras i VMM-moln.

**Komponent** | **Krav** | **Information**
--- | --- | ---
**Azure** | En Azure-prenumeration, Azure Storage-konto och Azure-nätverk. | Replikerade data från lokala VM-arbetsbelastningar lagras i lagrings kontot. Virtuella Azure-datorer skapas med replikerade data när redundansväxlingen från din lokala plats sker.<br/><br/> Virtuella Azure-datorer ansluter till det virtuella Azure-nätverket när de skapas.
**VMM-server** | VMM-servern har ett eller flera moln som innehåller Hyper-V-värdar. | Du installerar Site Recovery-providern på VMM-servern, för att samordna replikering med Site Recovery och registrera servern i Recovery Services-valvet.
**Hyper-V-värd** | En eller flera Hyper-V-värdar eller Hyper-V-kluster som hanteras av VMM. |  Du installerar Recovery Services-agenten på varje Hyper-V-värd eller klusternod.
**Hyper-V:s virtuella datorer** | En eller flera virtuella datorer som körs på en Hyper-V-värdserver. | Du behöver inte installera något på de virtuella datorerna.
**Nätverk** | Logiska och virtuella datornätverk som konfigurerats på VMM-servern. Det virtuella dator nätverket bör vara kopplat till ett logiskt nätverk som är associerat med molnet. | Virtuella dator nätverk mappas till virtuella Azure-nätverk. När virtuella Azure-datorer skapas efter en redundansväxling läggs de till i det Azure-nätverk som är mappat till det virtuella dator nätverket.

**Arkitektur för Hyper-V till Azure (med VMM)**

![Diagram över en lokal Hyper-V-plats till Azure-arkitekturen med VMM.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

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


## <a name="replication-process"></a>Replikeringsprocessen

![Diagram som visar replikeringen av Hyper-V till Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Process för replikering och återställning**


### <a name="enable-protection"></a>Aktivera skydd

1. När du har aktiverat skydd för en virtuell Hyper-V-dator i Azure Portal eller lokalt startar **Aktivera skydd**.
2. Jobbet kontrollerar att datorn uppfyller kraven och anropar sedan metoden [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice) som konfigurerar replikering med de inställningar som du har konfigurerat.
3. Jobbet startar den initiala replikeringen genom att aktivera metoden [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice) för att initiera en fullständig VM-replikering och skicka de virtuella datorernas diskar till Azure.
4. Du kan övervaka jobbet på fliken **jobb** .      ![Skärm bild av listan jobb på fliken jobb. ](media/hyper-v-azure-architecture/image1.png) Skärm ![ bild av skärmen Aktivera skydd med mer information.](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Inledande datareplikering

1. När den inledande replikeringen utlöses tas en ögonblicks bild av en [ögonblicks bild av virtuell dator i Hyper-V](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) .
2. Virtuella hård diskar på den virtuella datorn replikeras en i taget tills alla har kopierats till Azure. Detta kan ta en stund, beroende på storleken på den virtuella datorn och nätverks bandbredden. [Lär dig hur](https://support.microsoft.com/kb/3056159) du ökar nätverks bandbredden.
3. Om disk ändringar sker medan den inledande replikeringen pågår, spårar spårningen för Hyper-V-replikering ändringarna som Hyper-V-replikeringsinställningar (. HRL). Dessa loggfiler finns i samma mapp som diskarna. Varje disk har en associerad. HRL-fil som skickas till sekundär lagring. Ögonblicksbilden och loggfilerna använder diskresurser när den inledande replikeringen pågår.
4. När den initiala replikeringen är klar tas VM-ögonblicksbilden bort.
5. Diskförändringarna (delta) i loggen synkroniseras och sammanfogas till den överordnade disken.


### <a name="finalize-protection-process"></a>Slutför skydds process

1. När den inledande replikeringen har slutförts körs jobbet **Slutför skydd på den virtuella datorn** . Den konfigurerar nätverks-och andra inställningar efter replikering, så att den virtuella datorn skyddas.
2. I det här skedet kan du kontrol lera inställningarna för virtuella datorer för att kontrol lera att de är redo för redundans. Du kan köra en granskning av haveri beredskap (redundanstest) för den virtuella datorn för att kontrol lera att den växlar över som förväntat. 


## <a name="delta-replication"></a>Deltareplikering

1. Efter den inledande replikeringen börjar delta-replikering, i enlighet med replikeringsprincipen.
2. Spåraren för Hyper-V-replikering spårar ändringar till en virtuell hård disk som. HRL-filer. Varje disk som är konfigurerad för replikering har en associerad .hrl-fil.
3. Loggen skickas till kundens lagrings konto. När en logg överförs till Azure spåras ändringarna på den primära disken i en annan loggfil i samma mapp.
4. Under inledande och delta-replikering kan du övervaka den virtuella datorn i Azure Portal.

### <a name="resynchronization-process"></a>Process för omsynkronisering

1. Om deltareplikeringen misslyckas och om en fullständig replikering skulle bli för tids- och bandbreddskrävande så markeras en virtuell dator för omsynkronisering.
    - Om HRL-filerna till exempel når 50 % av diskstorleken markeras den virtuella datorn för omsynkronisering.
    -  Omsynkronisering av standard är schemalagd att köras automatiskt utanför kontors tid.
1.  Omsynkronisering skickar endast delta data.
    - Den minimerar mängden data som skickas genom att beräkna kontroll summor för käll-och mål-VM.
    - Den använder en algoritm för fast block-block där käll-och målattribut är indelade i fasta segment.
    - Kontroll summor för varje segment genereras. Dessa jämförs för att avgöra vilka block från källan som måste tillämpas på målet.
2. När omsynkroniseringen är klar ska den normala deltareplikeringen återupptas.
3. Om du inte vill vänta på omsynkroniseringen av standardvärdet utanför timmar kan du synkronisera om en virtuell dator manuellt. Till exempel om ett avbrott inträffar. Det gör du genom att välja den virtuella datorn > **omsynkronisering** i Azure Portal.

    ![Skärm bild som visar omsynkroniserings-alternativet.](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Försök att bearbeta igen

Om det uppstår ett replikeringsfel finns det en inbyggd funktion som gör ett nytt försök. Återförsök klassificeras enligt beskrivningen i tabellen.

**Kategori** | **Information**
--- | ---
**Oåterkalleliga fel** | Inga nya försök görs. Status för den virtuella datorn är **kritisk**, och administratören måste ingripa.<br/><br/> Exempel på dessa fel är en bruten virtuell hård disk kedja, ett ogiltigt tillstånd för den virtuella replik datorn, autentiseringsfel, auktoriseringsfel och den virtuella datorn kunde inte hitta fel (för fristående Hyper-V-servrar).
**Återkalleliga fel** | Återförsök sker varje replikeringsintervall, med ett exponentiellt undantagsläge som ökar återförsöksintervallet från det första försökets start med 1, 2, 4, 8 och 10 minuter. Om felet kvarstår försöker du var 30: e minut. Exempel på sådana problem är nätverks fel, låga diskfel och låga minnes förhållanden.



## <a name="failover-and-failback-process"></a>Processen för redundans och återställning efter fel

1. Du kan köra en planerad eller oplanerad redundansväxling från lokala Hyper-V virtuella datorer till Azure. Om du kör en planerad redundansväxling stängs de virtuella källdatorerna av för att säkerställa att inga data går förlorade. Kör en oplanerad redundansväxling om den primära platsen inte är tillgänglig.
2. Du kan redundansväxla en enskild dator eller skapa återställningsplaner för att samordna redundans för flera datorer.
3. Du kör en redundansväxling. När det första steget i redundansväxlingen har slutförts bör du kunna se de skapade virtuella replik datorerna i Azure. Du kan tilldela en offentlig IP-adress till datorn om det behövs.
4. Sedan genomför du redundansväxlingen och startar åtkomst till arbets belastningen från den virtuella repliken Azure-datorn.

När din lokala infrastruktur är igång igen kan du växla tillbaka. Återställning efter fel sker i tre steg:

1. Starta en planerad redundansväxling från Azure till den lokala platsen:
    - **Minimera nedtid**: om du använder det här alternativet Site Recovery synkroniserar data före redundans. Den söker efter ändrade data block och laddar ned dem till den lokala platsen, medan den virtuella Azure-datorn fortsätter att köras, vilket minimerar stillestånds tiden. När du anger att redundansväxlingen ska slutföras manuellt stängs den virtuella Azure-datorn, eventuella ändringar i ändringarna kopieras och redundansväxlingen startar.
    - **Fullständig nedladdning**: med dessa alternativ data synkroniseras under redundansväxlingen. Med det här alternativet hämtas hela disken. Det är snabbare eftersom inga kontroll summor beräknas, men det finns mer stillestånds tid. Använd det här alternativet om du har kört replikeringen av virtuella Azure-datorer under en tid, eller om den lokala virtuella datorn har tagits bort.
    - **Skapa virtuell dator**: du kan välja att växla tillbaka till samma virtuella dator eller till en annan virtuell dator. Du kan ange att Site Recovery ska skapa den virtuella datorn om den inte redan finns.

2. När den första synkroniseringen är klar väljer du att slutföra redundansväxlingen. När den är klar kan du logga in på den lokala virtuella datorn för att kontrol lera att allt fungerar som förväntat. I Azure Portal kan du se att de virtuella Azure-datorerna har stoppats.
3.  Sedan genomför du redundansväxlingen för att slutföra och startar åtkomst till arbets belastningen från den lokala virtuella datorn igen.
4. När arbets belastningarna har återställts aktiverar du omvänd replikering så att de lokala virtuella datorerna replikeras till Azure igen.



## <a name="next-steps"></a>Nästa steg


Följ [den här självstudien](tutorial-prepare-azure.md) för att komma igång med Hyper-V till Azure-replikering.


