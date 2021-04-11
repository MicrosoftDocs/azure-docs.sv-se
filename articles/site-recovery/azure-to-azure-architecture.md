---
title: Katastrof återställnings arkitektur för Azure till Azure i Azure Site Recovery
description: Översikt över arkitekturen som används när du konfigurerar haveri beredskap mellan Azure-regioner för virtuella Azure-datorer med hjälp av tjänsten Azure Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.openlocfilehash: 38bf9d41f81a76c4263952a94b5526984db0705f
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580932"
---
# <a name="azure-to-azure-disaster-recovery-architecture"></a>Haveriberedskapsarkitektur för Azure till Azure


I den här artikeln beskrivs arkitektur, komponenter och processer som används när du distribuerar haveri beredskap för virtuella Azure-datorer (VM: ar) med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) . När haveri beredskap har kon figurer ATS replikeras de virtuella Azure-datorerna kontinuerligt till en annan mål region. Om ett avbrott inträffar kan du växla över virtuella datorer till den sekundära regionen och komma åt dem därifrån. När allt körs normalt igen kan du växla tillbaka och fortsätta att arbeta på den primära platsen.



## <a name="architectural-components"></a>Arkitekturkomponenter

De komponenter som ingår i haveri beredskap för virtuella Azure-datorer sammanfattas i följande tabell.

**Komponent** | **Krav**
--- | ---
**Virtuella datorer i käll regionen** | En av flera virtuella Azure-datorer i en [käll region som stöds](azure-to-azure-support-matrix.md#region-support).<br/><br/> Virtuella datorer kan köra alla [operativ system som stöds](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).
**VM-lagring för källa** | Virtuella Azure-datorer kan hanteras eller låta icke-hanterade diskar spridas över lagrings konton.<br/><br/>[Läs mer om](azure-to-azure-support-matrix.md#replicated-machines---storage) Azure Storage som stöds.
**Virtuella käll dator nätverk** | Virtuella datorer kan finnas i ett eller flera undernät i ett virtuellt nätverk (VNet) i käll regionen. [Lär dig mer](azure-to-azure-support-matrix.md#replicated-machines---networking) om nätverks krav.
**Lagrings konto för cache** | Du behöver ett cache Storage-konto i käll nätverket. Under replikeringen lagras VM-ändringar i cachen innan de skickas till mål lagringen.  Cache Storage-konton måste vara standard.<br/><br/> Användningen av en cache garanterar minimal påverkan på produktions program som körs på en virtuell dator.<br/><br/> [Läs mer](azure-to-azure-support-matrix.md#cache-storage) om cache Storage-krav. 
**Mål resurser** | Mål resurserna används vid replikering och när en redundansväxling sker. Site Recovery kan konfigurera mål resurs som standard, eller så kan du skapa/anpassa dem.<br/><br/> I mål regionen kontrollerar du att du kan skapa virtuella datorer och att din prenumeration har tillräckligt med resurser för att stödja VM-storlekar som kommer att behövas i mål regionen. 

![Diagram över käll-och mål replikering.](./media/concepts-azure-to-azure-architecture/enable-replication-step-1-v2.png)

## <a name="target-resources"></a>Mål resurser

När du aktiverar replikering för en virtuell dator ger Site Recovery dig möjlighet att skapa mål resurser automatiskt. 

**Målresurs** | **Standardinställning**
--- | ---
**Mål prenumeration** | Samma som käll prenumerationen.
**Målresursgrupp** | Resurs gruppen som de virtuella datorerna tillhör efter redundansväxlingen.<br/><br/> Det kan finnas i alla Azure-regioner utom käll regionen.<br/><br/> Site Recovery skapar en ny resurs grupp i mål regionen, med ett "ASR"-suffix.<br/><br/>
**Mål-VNet** | Det virtuella nätverk (VNet) i vilket replikerade virtuella datorer finns efter en redundansväxling. En nätverks mappning skapas mellan käll-och mål nätverk och vice versa.<br/><br/> Site Recovery skapar ett nytt VNet och undernät med suffixet "ASR".
**Mål lagrings konto** |  Om den virtuella datorn inte använder en hanterad disk är detta det lagrings konto som data replikeras till.<br/><br/> Site Recovery skapar ett nytt lagrings konto i mål regionen för att spegla käll lagrings kontot.
**Replik Managed disks** | Om den virtuella datorn använder en hanterad disk, är detta de hanterade diskar som data replikeras till.<br/><br/> Site Recovery skapar en replik som Managed disks i lagrings regionen för att spegla källan.
**Tillgänglighets uppsättningar för mål** |  Tillgänglighets uppsättning där virtuella datorer replikeras efter redundansväxlingen.<br/><br/> Site Recovery skapar en tillgänglighets uppsättning i mål regionen med suffixet "ASR" för virtuella datorer som finns i en tillgänglighets uppsättning på käll platsen. Om det finns en tillgänglighets uppsättning används den och en ny inte skapas.
**Mål tillgänglighets zoner** | Om mål regionen har stöd för tillgänglighets zoner tilldelar Site Recovery samma zon nummer som används i käll regionen.

### <a name="managing-target-resources"></a>Hantera mål resurser

Du kan hantera mål resurser på följande sätt:

- Du kan ändra mål inställningarna när du aktiverar replikering.
- Du kan ändra mål inställningarna när replikeringen redan fungerar. Observera att standard-SKU: n för den virtuella mål regionen är samma som SKU: n för den virtuella käll datorn (eller nästa tillgängliga SKU som är mest tillgängligt i jämförelse med den virtuella käll datorn SKU). På samma sätt som för andra resurser, till exempel mål resurs grupp, målnamn och andra, kan den virtuella datorns SKU: n också uppdateras efter att replikeringen pågår. En resurs som inte kan uppdateras är tillgänglighets typ (enskild instans, uppsättning eller zon). Om du vill ändra den här inställningen måste du inaktivera replikering, ändra inställningen och sedan aktivera igen. 


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

## <a name="replication-process"></a>Replikeringsprocessen

När du aktiverar replikering för en virtuell Azure-dator händer följande:

1. Tillägget Site Recovery Mobility Service installeras automatiskt på den virtuella datorn.
2. Tillägget registrerar den virtuella datorn med Site Recovery.
3. Kontinuerlig replikering börjar för den virtuella datorn.  Disk skrivningar överförs direkt till cache-lagrings kontot på käll platsen.
4. Site Recovery bearbetar data i cachen och skickar dem till mål lagrings kontot, eller till replik Managed disks.
5. När data har bearbetats genereras kraschbaserade återställnings punkter var femte minut. Programkonsekventa återställnings punkter genereras enligt den inställning som anges i replikeringsprincipen.

![Diagram som visar den replikerade processen, steg 2.](./media/concepts-azure-to-azure-architecture/enable-replication-step-2-v2.png)

**Replikeringsprocessen**

## <a name="connectivity-requirements"></a>Anslutnings krav

 De virtuella Azure-datorerna som du replikerar behöver utgående anslutning. Site Recovery behöver aldrig inkommande anslutning till den virtuella datorn. 

### <a name="outbound-connectivity-urls"></a>Utgående anslutning (URL: er)

Om utgående åtkomst för virtuella datorer styrs med URL: er, Tillåt dessa URL: er.

| **Namn**                  | **Kommersiellt**                               | **Myndigheter**                                 | **Beskrivning** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Gör att data kan skrivas från den virtuella datorn till cachelagringskontot i källregionen. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Tillhandahåller auktorisering och autentisering för Site Recovery-tjänstens webbadresser. |
| Replikering               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`     | Låter den virtuella datorn kommunicera med Site Recovery-tjänsten. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Låter den virtuella datorn skriva övervaknings- och diagnostikdata för Site Recovery. |
| Key Vault                 | `*.vault.azure.net`                        | `*.vault.usgovcloudapi.net`                  | Tillåter åtkomst att aktivera replikering för ADE-aktiverade virtuella datorer via portalen |
| Azure Automation          | `*.automation.ext.azure.com`               | `*.azure-automation.us`                      | Gör det möjligt att aktivera automatisk uppgradering av mobilitets agenten för ett replikerat objekt via portalen |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Utgående anslutning för IP-adressintervall

Tillåt de här adresserna för att kontrol lera utgående anslutningar för virtuella datorer med IP-adresser.
Observera att information om nätverks anslutningens krav finns i [nätverks White Paper](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) 

#### <a name="source-region-rules"></a>Käll regions regler

**Regel** |  **Information** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar lagrings konton i käll regionen | Lagrings.\<region-name>
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Active Directory (Azure AD)  | AzureActiveDirectory
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Event Hub i mål regionen. | EventsHub.\<region-name>
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Site Recovery  | AzureSiteRecovery
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Key Vault (detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portalen) | AzureKeyVault
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Automation Controller (detta krävs endast för att aktivera automatisk uppgradering av mobilitets agenten för ett replikerat objekt via portalen) | GuestAndHybridManagement

#### <a name="target-region-rules"></a>Mål regions regler

**Regel** |  **Information** | **Tjänsttagg**
--- | --- | --- 
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar lagrings konton i mål regionen | Lagrings.\<region-name>
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure AD  | AzureActiveDirectory
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Event Hub i käll regionen. | EventsHub.\<region-name>
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Site Recovery  | AzureSiteRecovery
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Key Vault (detta krävs endast för att aktivera replikering av ADE-aktiverade virtuella datorer via portalen) | AzureKeyVault
Tillåt HTTPS utgående: port 443 | Tillåt intervall som motsvarar Azure Automation Controller (detta krävs endast för att aktivera automatisk uppgradering av mobilitets agenten för ett replikerat objekt via portalen) | GuestAndHybridManagement


#### <a name="control-access-with-nsg-rules"></a>Kontrol lera åtkomst med NSG-regler

Observera följande krav om du styr VM-anslutningen genom att filtrera nätverks trafik till och från Azure-nätverk/undernät med [NSG-regler](../virtual-network/network-security-groups-overview.md):

- NSG-regler för Azure-webbregionen ska tillåta utgående åtkomst för replikeringstrafik.
- Vi rekommenderar att du skapar regler i en test miljö innan du sätter dem i produktion.
- Använd [service Taggar](../virtual-network/network-security-groups-overview.md#service-tags) i stället för att tillåta enskilda IP-adresser.
    - Service taggar representerar en grupp IP-adressprefix som samlats in för att minimera komplexiteten när du skapar säkerhets regler.
    - Microsoft uppdaterar automatiskt tjänst etiketter över tid. 
 
Läs mer om [utgående anslutning](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) för Site Recovery och att [kontrol lera anslutningen till NSG: er](concepts-network-security-group-with-site-recovery.md).


### <a name="connectivity-for-multi-vm-consistency"></a>Anslutning för konsekvens för flera virtuella datorer

Om du aktiverar konsekvens för flera virtuella datorer kommunicerar datorer i replikeringsgruppen med varandra på port 20004.
- Se till att det inte finns någon brandvägg som blockerar den interna kommunikationen mellan de virtuella datorerna på port 20004.
- Om du vill att virtuella datorer med Linux ska vara med i en replikeringsgrupp måste du se till att du manuellt öppnar för utgående trafik på port 20004 enligt riktlinjerna för den specifika Linux-versionen.




## <a name="failover-process"></a>Redundansväxling

När du initierar en redundansväxling skapas de virtuella datorerna i mål resurs gruppen, målets virtuella nätverk, mål under nätet och målets tillgänglighets uppsättning. Under en redundansväxling kan du använda alla återställnings punkter.

![Diagram som visar redundansväxlingen med käll-och mål miljöer.](./media/concepts-azure-to-azure-architecture/failover-v2.png)

## <a name="next-steps"></a>Nästa steg

[Replikera snabbt](azure-to-azure-quickstart.md) en virtuell Azure-dator till en sekundär region.