---
title: Vanliga frågor om haveri beredskap för virtuella Azure-datorer med Azure Site Recovery
description: I den här artikeln besvaras vanliga frågor om haveri beredskap för virtuella Azure-datorer när du använder Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 11/03/2019
ms.topic: conceptual
ms.openlocfilehash: d4fa7348df647cf699ebd55dd6415a79454ab5f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397973"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Vanliga frågor: Azure till Azure-haveriberedskap

Den här artikeln besvarar vanliga frågor om haveri beredskap för virtuella Azure-datorer till en annan Azure-region med hjälp av tjänsten [Azure Site Recovery](site-recovery-overview.md) .

## <a name="general"></a>Allmänt

### <a name="how-is-site-recovery-priced"></a>Hur kostar det att Site Recovery?

Läs om [kostnader](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) för haveri beredskap för virtuella Azure-datorer.

### <a name="how-does-the-free-tier-work"></a>Hur fungerar den kostnads fria nivån?

Varje instans som skyddas med Site Recovery är kostnads fri under de första 31 dagars skyddet. Efter den perioden sammanfattas skyddet för varje instans enligt [pris informationen](https://azure.microsoft.com/pricing/details/site-recovery/). Du kan beräkna kostnader med hjälp av [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator/?service=site-recovery).

### <a name="do-i-incur-other-azure-charges-in-the-first-31-days"></a>Debiteras jag andra Azure-avgifter under de första 31 dagarna?

Ja. Även om Azure Site Recovery är kostnads fritt under de första 31 dagarna i en skyddad instans kan du debiteras för Azure Storage, lagrings transaktioner och data överföringar. En återställd virtuell dator kan också ådra sig Azure Compute-avgifter. G

### <a name="how-do-i-get-started-with-azure-vm-disaster-recovery"></a>Hur gör jag för att kom igång med haveri beredskap för virtuella Azure-datorer?

1. [Förstå](azure-to-azure-architecture.md) Azures arkitektur för haveri beredskap för virtuella datorer.
2. [Granska](azure-to-azure-support-matrix.md) support kraven.
3. [Konfigurera](azure-to-azure-how-to-enable-replication.md) katastrof återställning för virtuella Azure-datorer.
4. [Kör en haveri beredskap](azure-to-azure-tutorial-dr-drill.md) med en redundanstest.
5. [Kör en fullständig redundansväxling](azure-to-azure-tutorial-failover-failback.md) till en sekundär Azure-region.
6. [Växlar tillbaka](azure-to-azure-tutorial-failback.md) från den sekundära regionen till den primära regionen.

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hur garanterar vi kapaciteten i mål regionen?

Site Recovery teamet och Azure Capacity Management-teamet, planera för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanserna skyddas av Site Recovery distribuerar till mål regionen.

## <a name="replication"></a>Replikering

### <a name="can-i-replicate-vms-with-disk-encryption"></a>Kan jag replikera virtuella datorer med disk kryptering?

Ja. Site Recovery stöder haveri beredskap för virtuella datorer som har Azure Disk Encryption (ADE) aktiverade. När du aktiverar replikering kopierar Azure alla nödvändiga disk krypterings nycklar och hemligheter från käll regionen till mål regionen i användar kontexten. Om du inte har de behörigheter som krävs kan din säkerhets administratör använda ett skript för att kopiera nycklar och hemligheter.

- Site Recovery stöder ADE för virtuella Azure-datorer som kör Windows.
- Site Recovery stöder:
    - ADE version 0,1, som har ett schema som kräver Azure Active Directory (Azure AD).
    - ADE version 1,1, som inte kräver Azure AD. För version 1,1 måste virtuella Windows Azure-datorer ha hanterade diskar.
    - [Läs mer](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema). om tilläggs scheman.

[Läs mer](azure-to-azure-how-to-enable-replication-ade-vms.md) om hur du aktiverar replikering för krypterade virtuella datorer.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Kan jag välja ett Automation-konto från en annan resurs grupp?

När du tillåter Site Recovery att hantera uppdateringar för mobilitets tjänst tillägget som körs på replikerade virtuella Azure-datorer, distribuerar den en global Runbook (som används av Azure-tjänster) via ett Azure Automation-konto. Du kan använda Automation-kontot som Site Recovery skapar eller välja att använda ett befintligt Automation-konto. 

I portalen kan du för närvarande bara välja ett Automation-konto i samma resurs grupp som valvet. Du kan välja ett Automation-konto från en annan resurs grupp med hjälp av PowerShell. [Läs mer](azure-to-azure-autoupdate.md#enable-automatic-updates).

### <a name="if-i-use-a-customer-automation-account-thats-not-in-the-vault-resource-group-can-i-delete-the-default-runbook"></a>Kan jag ta bort standard-runbooken om jag använder ett Automation-konto som inte finns i valv resurs gruppen?

Ja, du kan ta bort det om du inte behöver det. 

### <a name="can-i-replicate-vms-to-another-subscription"></a>Kan jag replikera virtuella datorer till en annan prenumeration?

Ja, du kan replikera virtuella Azure-datorer till alla prenumerationer inom samma Azure AD-klient. När du aktiverar haveri beredskap för virtuella datorer är den angivna mål prenumerationen som standard den för den virtuella käll datorn. Du kan ändra mål prenumerationen och andra inställningar (till exempel resurs grupp och virtuellt nätverk), fylls i automatiskt från den valda prenumerationen.

### <a name="can-i-replicate-vms-in-an-availability-zone-to-another-region"></a>Kan jag replikera virtuella datorer i en tillgänglighets zon till en annan region?

Ja, du kan replikera virtuella datorer i tillgänglighets zoner till en annan Azure-region. Den virtuella mål datorn kan distribueras som en enskild instans, i en tillgänglighets uppsättning eller i en tillgänglighets zon om den stöds i mål regionen. 

### <a name="can-i-replicate-non-zone-vms-to-a-zone-within-the-same-region"></a>Kan jag replikera virtuella datorer som inte är zoner till en zon inom samma region? 

Detta stöds inte i portalen. Du kan använda REST API/PowerShell för att göra detta.

### <a name="can-i-replicate-zoned-vms-to-a-different-zone-in-the-same-region"></a>Kan jag replikera virtuella datorer i zonen till en annan zon i samma region?

Support för detta är begränsat till några regioner. [Läs mer](azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

### <a name="can-i-exclude-disks-from-replication"></a>Kan jag utesluta diskar från replikering?

Ja, du kan utesluta diskar när du konfigurerar replikering med PowerShell. [Läs mer](azure-to-azure-exclude-disks.md).

### <a name="can-i-replicate-new-disks-added-to-replicated-vms"></a>Kan jag replikera nya diskar som lagts till i replikerade virtuella datorer?

För replikerade virtuella datorer med hanterade diskar kan du lägga till nya diskar och aktivera replikering för dem. När du lägger till en ny disk visar den replikerade virtuella datorn ett varnings meddelande om att en eller flera diskar på den virtuella datorn är tillgängliga för skydd. 

- Om du aktiverar replikering för de tillagda diskarna försvinner varningen efter den inledande replikeringen.
- Om du inte vill aktivera replikering för disken kan du stänga av varningen.
- Om du växlar över en virtuell dator med tillagda diskar visar replikerings platser de diskar som är tillgängliga för återställning. Om du till exempel lägger till en andra disk till en virtuell dator med en disk, skapas en replikerings punkt som skapats innan du lade till som "1 av 2 diskar".

Site Recovery stöder inte "varm Remove" av diskar från en replikerad virtuell dator. Om du tar bort en virtuell dator disk måste du inaktivera och sedan återaktivera replikering för den virtuella datorn.

### <a name="how-often-can-i-replicate-to-azure"></a>Hur ofta kan jag replikera till Azure?

Replikering är kontinuerlig när du replikerar virtuella Azure-datorer till en annan Azure-region. [Läs mer](./azure-to-azure-architecture.md#replication-process) om hur replikering fungerar.

### <a name="can-i-replicate-virtual-machines-within-a-region"></a>Kan jag replikera virtuella datorer inom en region? 

Du kan inte använda Site Recovery för att replikera diskar inom en region.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Kan jag replikera VM-instanser till alla Azure-regioner?

Du kan replikera och återställa virtuella datorer mellan två regioner i samma geografiska kluster. Geografiska kluster definieras med data svars tid och suveränitet i åtanke. [Läs mer](./azure-to-azure-support-matrix.md#region-support) om region support.

### <a name="does-site-recovery-need-internet-connectivity"></a>Behöver Site Recovery Internet anslutning?

Nej, men virtuella datorer behöver åtkomst till Site Recovery webb adresser och IP-intervall. [Läs mer](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-tiered-across-resource-groups"></a>Kan jag replikera ett program skikt över resurs grupper?

Ja, du kan replikera appen och behålla haveri beredskaps konfigurationen i en separat resurs grupp.

Om till exempel apparna har tre nivåer (program/databas/webb) i olika resurs grupper måste du aktivera replikering tre gånger för att skydda alla nivåer. Site Recovery replikerar de tre nivåerna till tre olika resurs grupper.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>Kan jag flytta lagrings konton över resurs grupper?

Nej, detta stöds inte. Om du av misstag flyttar lagrings konton till en annan resurs grupp och tar bort den ursprungliga resurs gruppen kan du skapa en ny resurs grupp med samma namn som den gamla resurs gruppen och sedan flytta lagrings kontot till resurs gruppen.

## <a name="replication-policy"></a>Replikeringsprincip

### <a name="what-is-a-replication-policy"></a>Vad är en replikeringsprincip?

En replikeringsprincip definierar bevarande historiken för återställnings punkter och frekvensen för programkonsekventa ögonblicks bilder.  Site Recovery skapar en standard princip för replikering enligt följande:

- Behåll återställnings punkter i 24 timmar.
- Ta app-konsekventa ögonblicks bilder var fjärde timme.

[Läs mer](azure-to-azure-how-to-enable-replication.md#customize-target-resources) om replikeringsinställningar.

### <a name="whats-a-crash-consistent-recovery-point"></a>Vad är en krasch-konsekvent återställnings punkt?

En kraschad återställnings punkt innehåller data på disk, som om du hämtade ström sladden från servern under ögonblicks bilden. Det innehåller inte något som fanns i minnet när ögonblicks bilden togs.

Idag kan de flesta appar återställa sig väl från kraschbaserade ögonblicks bilder. En kraschad återställnings punkt är vanligt vis tillräckligt för andra operativ system än databaser, och appar som fil servrar, DHCP-servrar och utskrifts servrar.

Site Recovery skapar automatiskt en kraschad återställnings punkt var femte minut.

### <a name="whats-an-application-consistent-recovery-point"></a>Vad är en programkonsekvent återställnings punkt?

Programkonsekventa återställnings punkter skapas från programkonsekventa ögonblicks bilder. De samlar in samma data som kraschbaserade ögonblicks bilder och samlar in data i minnet och alla pågående transaktioner.

På grund av extra innehåll är programkonsekventa ögonblicks bilder de mest inblandade och ta den längsta. Vi rekommenderar program-konsekventa återställnings punkter för databas operativ system och appar som SQL Server. För Windows använder programkonsekventa ögonblicks bilder tjänsten Volume Shadow Copy (VSS).

### <a name="do-app-consistent-recovery-points-impact-performance"></a>Påverkar app-konsekventa återställnings punkter prestanda?

 Eftersom programkonsekventa återställnings punkter fångar alla data i minnet och processen, om de fångar ofta, kan det påverka prestanda när arbets belastningen redan är upptagen. Vi rekommenderar inte att du fångar för ofta för arbets belastningar som inte är databas. Även för databas arbets belastningar bör en timme vara tillräckligt.

### <a name="whats-the-minimum-frequency-for-generating-app-consistent-recovery-points"></a>Vad är den minsta frekvensen för att generera programkonsekventa återställnings punkter?

Site Recovery kan skapa programkonsekventa återställnings punkter med en minsta frekvens på en timme.

### <a name="can-i-enable-app-consistent-replication-for-linux-vms"></a>Kan jag aktivera programkonsekvent replikering för virtuella Linux-datorer?

Ja. Mobilitets agenten för Linux stöder anpassade skript för program konsekvens. Ett anpassat skript med pre och post-Options används av agenten. [Läs mer](site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

### <a name="how-are-recovery-points-generated-and-saved"></a>Hur genereras och sparas återställnings punkter?

Vi använder ett exempel för att förstå hur Site Recovery genererar återställnings punkter. 

- En replikeringsprincip behåller återställnings punkter i 24 timmar och tar en ögonblicks bild av en app-konsekvent frekvens varje timme.
- Site Recovery skapar en kraschad återställnings punkt var femte minut. Du kan inte ändra den här frekvensen.
- Site Recovery rensar återställnings punkter efter en timme och sparar en poäng per timme.

I den senaste timmen kan du välja mellan 12 kraschbaserade punkter och en programkonsekvent punkt, som du ser i bilden.

   ![Lista över genererade återställnings punkter](./media/azure-to-azure-common-questions/recovery-points.png)

### <a name="how-far-back-can-i-recover"></a>Hur långt tillbaka kan jag återställa?

Den äldsta återställnings punkten som du kan använda är 72 timmar.

### <a name="what-happens-if-site-recovery-cant-generate-recovery-points-for-more-than-24-hours"></a>Vad händer om Site Recovery inte kan generera återställnings punkter i mer än 24 timmar? 

Om du har en replikeringsprincip på 24 timmar och Site Recovery inte kan skapa återställnings punkter i mer än 24 timmar, förblir dina gamla återställnings punkter. Site Recovery ersätter bara den äldsta punkten om den genererar nya punkter. Tills det finns nya återställnings punkter kvarstår alla gamla punkter när du har nått fönstret kvarhållning.

### <a name="can-i-change-the-replication-policy-after-replication-is-enabled"></a>Kan jag ändra replikeringsprincipen när replikering har Aktiver ATS?

Ja. I valvet > **Site Recovery infrastruktur**  >  **principer** för infrastruktur väljer och redigerar du principen. Ändringarna gäller även för befintliga principer.

### <a name="are-all-recovery-points-a-complete-vm-copy"></a>Är alla återställnings punkter en komplett VM-kopia?

Den första återställnings punkten som genereras har en fullständig kopia. Ändrings punkterna för efterföljande återställnings punkter har delta ändringar.

### <a name="do-increases-in-recovery-point-retention-increase-storage-costs"></a>Ökar lagrings kostnaderna i kvarhållning av återställnings punkter?

Ja. Om du till exempel ökar kvarhållning från 24 timmar till 72, sparar Site Recovery återställnings punkter under ytterligare 48 timmar. Den tillagda tiden påverkar lagrings ändringar. Exempel: om en enda återställnings punkt hade delta ändringar på 10 GB, med en kostnad per GB $0,16 per månad, blir ytterligare kostnader $1,60 × 48 per månad.

## <a name="multi-vm-consistency"></a>Konsekvens för flera virtuella datorer

### <a name="what-is-multi-vm-consistency"></a>Vad är konsekvens för flera virtuella datorer?

Konsekvens för flera virtuella datorer säkerställer att återställnings punkter är konsekventa över replikerade virtuella datorer.

- När du aktiverar konsekvens för flera virtuella datorer skapar Site Recovery en replikeringsgrupp för alla datorer med alternativet aktiverat. 
- När du växlar över datorerna i replikeringsgruppen har de delat krasch-konsekventa och programkonsekventa återställnings punkter.

[Lär dig](azure-to-azure-tutorial-enable-replication.md#enable-replication) hur du aktiverar konsekvens för flera virtuella datorer.

### <a name="can-i-fail-over-a-single-vm-in-a-replication-group"></a>Kan jag redundansväxla en enskild virtuell dator i en replikeringsgrupp?

Nej. När du aktiverar konsekvens för flera virtuella datorer, härleder det att en app har ett beroende på alla virtuella datorer i replikeringsgruppen och att en enskild VM-redundans inte är tillåten.

### <a name="how-many-vm-can-i-replicate-together-in-a-group"></a>Hur många virtuella datorer kan jag replikera tillsammans i en grupp?

Du kan replikera 16 virtuella datorer tillsammans i en replikeringsgrupp.

### <a name="when-should-i-enable-multi-vm-consistency"></a>När bör jag aktivera konsekvens för flera virtuella datorer?

Konsekvens för flera virtuella datorer är processor intensivt och det kan påverka arbets Belastningens prestanda. Aktivera endast om virtuella datorer kör samma arbets belastning och du behöver konsekvens på flera datorer. Om du till exempel har två SQL Server instanser och två webb servrar i ett program kan du aktivera konsekvens för flera virtuella datorer för de SQL Server instanserna.

### <a name="can-i-add-a-replicating-vm-to-a-replication-group"></a>Kan jag lägga till en replikerad virtuell dator i en replikeringsgrupp?

När du aktiverar replikering för en virtuell dator kan du lägga till den i en ny replikeringsgrupp eller till en befintlig grupp. Du kan inte lägga till en virtuell dator som redan replikeras till en grupp. 
 
## <a name="failover"></a>Redundans

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hur garanterar vi kapaciteten i mål regionen?

Site Recovery teamet och Azure Capacity Management-teamet, planera för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kan distribueras till mål regionen.

### <a name="is-failover-automatic"></a>Sker redundansväxlingen automatisk?

Den sker inte automatiskt. Du kan starta en redundansväxling med ett enda klick i portalen eller använda  [PowerShell](azure-to-azure-powershell.md) för att utlösa en redundansväxling.

### <a name="can-i-keep-a-public-ip-address-after--failover"></a>Kan jag behålla en offentlig IP-adress efter redundansväxlingen?

Du kan inte behålla den offentliga IP-adressen för en produktions program efter en redundansväxling.

När du hämtar en arbets belastning som en del av redundansväxlingen måste du tilldela en Azure-offentlig IP-adressresurs till den. Resursen måste vara tillgänglig i mål regionen. Du kan tilldela den offentliga Azure-IP-adressresursen manuellt, eller så kan du automatisera den med en återställnings plan. [Lär dig](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan) hur du konfigurerar offentliga IP-adresser efter en redundansväxling.

### <a name="can-i-keep-a-private-ip-address-after-failover"></a>Kan jag behålla en privat IP-adress efter redundansväxlingen?

Ja. Som standard när du aktiverar haveri beredskap för virtuella Azure-datorer skapar Site Recovery mål resurser baserat på käll resurs inställningar. För virtuella Azure-datorer som kon figurer ATS med statiska IP-adresser försöker Site Recovery etablera samma IP-adress för den virtuella mål datorn, om den inte används.
[Läs mer om hur](site-recovery-retain-ip-azure-vm-failover.md) du behåller IP-adresser efter redundansväxling.

### <a name="why-is-a-vm-assigned-a-new-ip-address-after-failover"></a>Varför har en virtuell dator tilldelats en ny IP-adress efter redundansväxlingen?

Site Recovery försöker ange IP-adressen vid redundansväxlingen. Om en annan virtuell dator använder den adressen, Site Recovery ange nästa tillgängliga IP-adress som mål.

[Lär dig mer om](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms) att konfigurera nätverks MAPPNING och IP-adresser för virtuella nätverk.

### <a name="whats-the-latest-recovery-point"></a>Vad är den *senaste* återställnings punkten?

Det *senaste alternativet (lägsta* återställnings punkt) gör följande:

1. Först bearbetar den alla data som har skickats till Site Recovery.
2. När tjänsten bearbetar data skapas en återställnings punkt för varje virtuell dator, innan den virtuella datorn växlar över till den virtuella datorn. Det här alternativet ger lägsta återställnings punkt mål.
3. Den virtuella datorn som skapades efter redundansväxlingen har alla data som repliker ATS till Site Recovery, från När redundansväxlingen utlöstes.

### <a name="do-latest-recovery-points-impact-failover-rto"></a>Påverkar de *senaste* återställnings punkterna redundansväxlingen RTO?

Ja. Site Recovery bearbetar alla väntande data innan de växlar över, så det här alternativet har ett högre återställnings tids mål (RTO) än andra alternativ.

### <a name="whats-the-latest-processed-recovery-option"></a>Vad är det *senaste bearbetade* återställnings alternativet?

Det *senaste bearbetade* alternativet gör följande:

1. Den växlar över alla virtuella datorer till den senaste återställnings punkten som bearbetas av Site Recovery. Det här alternativet ger en låg RTO, eftersom ingen tid ägnas åt att bearbeta obearbetade data.

### <a name="what-if-theres-an-unexpected-outage-in-the-primary-region"></a>Vad händer om det uppstår ett oväntat avbrott i den primära regionen?

Du kan starta redundans. Site Recovery behöver inte anslutning från den primära regionen för att utföra redundansväxlingen.

### <a name="what-is-the-rto-of-a-vm-failover"></a>Vad är RTO för en VM-redundans?

Site Recovery har ett RTO-SLA på [två timmar](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Det mesta av tiden Site Recovery växlar över virtuella datorer på några minuter. Om du vill beräkna RTO granskar du redundansväxlingen, som visar hur lång tid det tog att ta upp en virtuell dator. 

## <a name="recovery-plans"></a>Återställningsplaner

### <a name="whats-a-recovery-plan"></a>Vad är en återställnings plan?

En [återställnings plan](site-recovery-create-recovery-plans.md) i Site Recovery dirigerar redundansväxlingen och återställningen av virtuella datorer. Den hjälper till att göra återställningen konsekvent korrekt, upprepnings bar och automatiserad. Det gör följande:

- Definierar en grupp virtuella datorer som växlar över flera gånger
- Definierar beroenden mellan virtuella datorer, så att programmet blir korrekt.
- Automatiserar återställning med alternativet för anpassade manuella åtgärder för andra uppgifter än VM-redundans. 


### <a name="how-does-sequencing-work"></a>Hur fungerar sekvenseringen?

I en återställnings plan kan du skapa flera grupper med virtuella datorer för sekvenseringen. Grupper växlar över en i taget, så att virtuella datorer som är en del av samma grupp växlar tillsammans. [Läs mer](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hur kan jag hitta RTO i en återställnings plan?

Om du vill kontrol lera RTO i en återställnings plan gör du ett redundanstest för återställnings planen. I **Site Recovery jobb** kontrollerar du varaktigheten för redundanstest. I skärm bilden exempel tog **SAPTestRecoveryPlan** för redundanstest 8 minuter och 59 sekunder.

![Visa jobb som visar varaktigheten för redundanstest för RTO](./media/azure-to-azure-common-questions/recovery-plan-rto.png)

### <a name="can-i-add-automation-runbooks-to-recovery-plans"></a>Kan jag lägga till Automation-runbooks i återställnings planer?

Ja. [Läs mer](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Skydd och återställning efter fel

### <a name="after-failover-are-vms-in-the-secondary-region-protected-automatically"></a>Är virtuella datorer i den sekundära regionen automatiskt skyddade efter redundansväxlingen? 

Nej. När du växlar över virtuella datorer från en region till en annan, startar de virtuella datorerna i mål återställnings regionen i ett oskyddat tillstånd. Om du vill [skydda](./azure-to-azure-how-to-reprotect.md) virtuella datorer i den sekundära regionen aktiverar du replikering tillbaka till den primära regionen.

### <a name="when-i-reprotect-is-all-data-replicated-from-the-secondary-region-to-primary"></a>Är alla data som replikeras från den sekundära regionen till primära när jag skyddar? 

Det beror på. Om den virtuella käll regionen finns, så synkroniseras bara ändringar mellan käll disken och mål disken. Site Recovery jämför diskarna med vad som är olika och överför sedan data. Den här processen tar vanligt vis några timmar. [Läs mer](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-long-does-it-take-fail-back"></a>Hur lång tid tar det att återställa?

Efter återaktivering av återställningen tar återställning efter samma tid det tog att redundansväxla från den primära regionen till en sekundär region.

## <a name="capacity"></a><a name="capacity"></a>Kapacitet

### <a name="how-do-we-ensure-capacity-in-the-target-region"></a>Hur garanterar vi kapaciteten i mål regionen?

Team planen för Site Recovery team och Azure Capacity Management för tillräcklig infrastruktur kapacitet. När du startar en redundansväxling hjälper teamen också till att se till att de virtuella dator instanser som skyddas av Site Recovery kan distribueras till mål regionen.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Fungerar Site Recovery med reserverade instanser?

Ja, du kan köpa [reserverade virtuella Azure-datorer](https://azure.microsoft.com/pricing/reserved-vm-instances/) i Disaster Recovery-regionen och Site Recovery redundansväxlingen använder dem. Ingen ytterligare konfiguration krävs.

## <a name="security"></a>Säkerhet

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Skickas replikeringsdata till Site Recovery-tjänsten?

Nej, Site Recovery inte fångar upp replikerade data och det finns ingen information om vad som körs på dina virtuella datorer. Endast de metadata som behövs för att samordna replikeringen och redundansen skickas till Site Recovery-tjänsten.

Site Recovery är ISO 27001:2013, 27018, HIPAA och DPA Certified. Tjänsten genomgår SOC2 och FedRAMP JAB-utvärderingar.

### <a name="does-site-recovery-encrypt-replication"></a>Krypterar Site Recovery replikering?

Ja, både kryptering och kryptering i [vila i Azure](../storage/common/storage-service-encryption.md) stöds.

## <a name="next-steps"></a>Nästa steg

- [Granska support kraven för Azure-till-Azure](azure-to-azure-support-matrix.md).
- [Konfigurera Azure-till-Azure-replikering](azure-to-azure-tutorial-enable-replication.md).
- Om du har frågor när du har läst den här artikeln kan du publicera dem på [sidan Microsoft Q&en fråga för Azure Recovery Services](/answers/topics/azure-site-recovery.html).
