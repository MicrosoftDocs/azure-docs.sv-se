---
title: Vägledning och metodtips
description: Upptäck de bästa metoderna och vägledningen för att säkerhetskopiera molnet och den lokala arbets belastningen till molnet
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 7b65556d8dd9b5b12e8da25055f6e39732c83afd
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258769"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Säkerhetskopiera molnet och lokala arbets belastningar till molnet

## <a name="introduction"></a>Introduktion

Azure Backup kan skydda dina data till gångar i Azure via en enkel, säker och kostnads effektiv lösning som kräver en Zero-infrastruktur. Det har Azure's inbyggd data skydds lösning för en mängd olika arbets belastningar. Den hjälper till att skydda dina verksamhets kritiska arbets belastningar som körs i molnet och säkerställer att dina säkerhets kopior alltid är tillgängliga och hanteras i stor skala över hela reserv fastigheten.

### <a name="intended-audience"></a>Målgrupp

Den primära mål gruppen för den här artikeln är IT-administratörer och program administratörer och implementeringar av stora och medel stora organisationer, som vill lära sig mer om funktionerna i Azures inbyggda data skydds teknik, Azure Backup och hur man effektivt implementerar lösningar som bättre skyddar dina distributioner. Artikeln förutsätter att du är bekant med Core Azure-teknik, data skydds koncept och hur du arbetar med en lösning för säkerhets kopiering. De rikt linjer som beskrivs i den här artikeln gör det lättare att utforma säkerhets kopierings lösningen på Azure med etablerade mönster och undvika kända fall GRO par.

### <a name="how-this-article-is-organized"></a>Hur den här artikeln organiseras

Även om det är enkelt att börja skydda infrastruktur och program på Azure, kan du påskynda tiden till värde när du ser till att de underliggande Azure-resurserna är korrekt konfigurerade och används optimalt. Den här artikeln beskriver en kort översikt över design överväganden och rikt linjer för att konfigurera din Azure Backup-distribution optimalt. Den undersöker kärn komponenterna (till exempel Recovery Services valv, säkerhets kopierings principer) och koncept (till exempel styrning) och hur du kan tänka på dem och deras funktioner med länkar till detaljerad produkt dokumentation.

## <a name="architecture"></a>Arkitektur

![Azure Backup-arkitektur](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Arbetsbelastningar

Azure Backup aktiverar data skydd för olika arbets belastningar (lokalt och i molnet). Det är en säker och tillförlitlig inbyggd data skydds funktion i Azure. Det kan enkelt skala sitt skydd mellan flera arbets belastningar utan hanterings kostnader. Det finns flera Automation-kanaler även för att aktivera detta (via PowerShell-, CLI-, Azure Resource Manager mallar och REST API: er.)

* **Skalbar, hållbar och säker lagring –** Azure Backup använder tillförlitlig blob-lagring med inbyggda funktioner för säkerhet och hög tillgänglighet. Du kan välja LRS-, GRS-eller RA-GRS-lagringar för dina säkerhets kopierings data.  

* **Intern arbets belastnings integrering –** Azure Backup tillhandahåller inbyggd integrering med Azure-arbetsbelastningar (virtuella datorer, SAP HANA, SQL på virtuella Azure-datorer och till och med Azure Files) utan att du behöver hantera automatisering eller infrastruktur för att distribuera agenter, skriva nya skript eller tillhandahålla lagring.

### <a name="data-plane"></a>Dataplanet

* **Automatiserad lagrings hantering** – Azure Backup automatiserar etablering och hantering av lagrings konton för säkerhetskopierade data så att de kan skalas när säkerhets kopierings data växer.

* **Skydd mot skadlig borttagning –** Skydda mot oavsiktliga och skadliga försök att ta bort säkerhets kopior via mjuk borttagning av säkerhets kopior. De borttagna säkerhetskopierade data lagras i 14 dagar utan kostnad och kan återställas från det här läget.

* **Säkra krypterade säkerhets kopieringar –** Azure Backup ser till att dina säkerhets kopierings data lagras på ett säkert sätt och använder inbyggda säkerhetsfunktioner i Azure-plattformen som Azure RBAC och kryptering.

* **Hantering av säkerhets kopierings data –** Azure Backup rensar automatiskt äldre säkerhetskopierade data för att följa bevarande principerna. Du kan också lagra dina data från användnings lagring till valv lagring.

### <a name="management-plane"></a>Hanteringsplanet

* **Åtkomst kontroll** – valv (Recovery Services-och säkerhets kopierings valv) tillhandahåller hanterings funktioner och är tillgängliga via Azure Portal, säkerhets kopierings Center, valv instrument paneler, SDK, CLI och till och med REST-API: er. Det är också en Azure RBAC-begränsning, som ger dig möjlighet att begränsa åtkomsten till säkerhets kopior enbart till behöriga säkerhets kopierings administratörer.

* **Princip hantering** – Azure Backup principer i varje valv definierar när säkerhets kopiorna ska utlösas och hur länge de behöver behållas. Du kan också hantera dessa principer och tillämpa dem på flera objekt.

* **Övervakning och rapportering** – Azure Backup integreras med Log Analytics och ger möjlighet att se rapporter via arbets böcker också.

* **Ögonblicks bilds hantering** – Azure Backup tar ögonblicks bilder för vissa Azure Native-arbetsbelastningar (VM: ar och Azure Files) hanterar dessa ögonblicks bilder och snabb återställning från dem. Det här alternativet minskar drastiskt tiden för att återställa data till den ursprungliga lagringen.

## <a name="vault-considerations"></a>Valv överväganden

Azure Backup använder valv (Recovery Services-och säkerhets kopierings valv) för att dirigera och hantera säkerhets kopior. Det använder också valv för att lagra säkerhetskopierade data. Effektiv valv design hjälper organisationer att skapa en struktur för att organisera och hantera säkerhets kopierings till gångar i Azure som stöd för dina affärs prioriteringar. Tänk på följande rikt linjer när du skapar ett valv:  

### <a name="align-to-subscription-design-strategy"></a>Anpassa till prenumerationens design strategi

Eftersom valvet är begränsat till en prenumeration kan du anpassa din valv design så att den passar prenumerationens design strategi, till exempel *strategi för program kategori* där prenumerationer åtskiljs baserat på specifika program eller tjänster eller på linjerna i Application archetypes. Mer information finns i den här [artikeln](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Ett eller flera valv

Du kan använda ett enda valv eller flera valv för att organisera och hantera säkerhets kopieringen. Läs igenom följande riktlinjer:

* Om dina arbets belastningar hanteras av en enda prenumeration och en enda resurs kan du använda ett enda valv för att övervaka och hantera reserv fastigheten.

* Om dina arbets belastningar sprids över prenumerationer kan du skapa flera valv, en eller flera per prenumeration.
  * Med Backup Center kan du ha en enda fönster ruta för att hantera alla uppgifter som är relaterade till säkerhets kopiering. [Mer information finns här]().
  * Du kan anpassa dina vyer med mallar för arbets böcker. Backup Explorer är en sådan mall för virtuella Azure-datorer. [Mer information finns här](monitor-azure-backup-with-backup-explorer.md).
  * Om du behöver konsekvent princip över valv kan du använda Azure policy för att sprida säkerhets kopierings principen över flera valv. Du kan skriva en anpassad [Azure policy definition](../governance/policy/concepts/definition-structure.md) som använder ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) -resultatet för att sprida en säkerhets kopierings princip över flera valv. Du kan också [tilldela](../governance/policy/assign-policy-portal.md) denna Azure policy-definition till ett visst omfång (prenumerations-eller rg), så att den distribuerar en "säkerhets kopierings princip"-resurs till alla Recovery Services-valv i omfånget för Azure policy tilldelningen. Inställningarna för säkerhets kopierings principen (t. ex. säkerhets kopierings frekvens, kvarhållning och så vidare) ska anges av användaren som parametrar i Azure Policy tilldelningen.

* När organisationens utrymme växer kan du vilja flytta arbets belastningar mellan prenumerationer av följande orsaker: justera efter säkerhets kopierings princip, konsolidera valv, kompromissa med lägre redundans för att spara pengar (flytta från GRS till LRS).  Azure Backup stöder flytt av ett Recovery Services valv i Azure-prenumerationer eller till en annan resurs grupp inom samma prenumeration. [Mer information finns här](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Granska standardinställningarna

Granska standardinställningarna för typ och säkerhets inställningar för lagringsprovider för att uppfylla dina krav innan du konfigurerar säkerhets kopior i valvet.

* *Typen av lagringsprovider* är som standard inställd på Geo-REDUNDANT (GRS). När du har konfigurerat säkerhets kopieringen inaktive ras alternativet att ändra. Följ [dessa](backup-create-rs-vault.md#set-storage-redundancy) steg om du vill granska och ändra inställningarna.

* *Mjuk borttagning* är aktiverat som standard på nyligen skapade valv för att skydda säkerhets kopierings data från oavsiktliga eller skadliga borttagningar. Följ [dessa](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) steg om du vill granska och ändra inställningarna.

* Med *återställning mellan regioner* kan du återställa virtuella Azure-datorer i en sekundär region, som är en Azure-kopplad region. Med det här alternativet kan du utföra granskningar för att uppfylla kraven på granskning eller efterlevnad och för att återställa den virtuella datorn eller disken om det finns en katastrof i den primära regionen. CRR är en valbar funktion för alla GRS-valv. [Mer information finns här](backup-create-rs-vault.md#set-cross-region-restore).

* Innan du slutfört din valv design går du igenom [valvets support mat ris](backup-support-matrix.md#vault-support) för att förstå de faktorer som kan påverka eller begränsa dina design val.

## <a name="backup-policy-considerations"></a>Säkerhets kopierings princip överväganden

Azure Backup princip har två komponenter: *schema* (när säkerhets kopiering ska ske) och *kvarhållning* (hur länge säkerhets kopieringen ska behållas). Du kan definiera principen baserat på vilken typ av data som säkerhets kopie ras, RTO/återställnings krav, operativa eller reglerade krav och arbets belastnings typ (till exempel virtuell dator, databas, filer). [Mer information finns här](backup-architecture.md#backup-policy-essentials).

Tänk på följande när du skapar en säkerhets kopierings princip:

### <a name="schedule-considerations"></a>Schema överväganden

* Överväg att gruppera virtuella datorer som kräver samma schemalagda start tid, frekvens och inställningar för kvarhållning inom en enda princip.

* Se till att den schemalagda start tiden för säkerhets kopiering är under en produktions program tid som inte är hög.

* Om du vill distribuera säkerhets kopierings trafik bör du överväga att säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen och se till att tiderna inte överlappar varandra.

### <a name="retention-considerations"></a>Överväganden vid bevarande

* Kortsiktig kvarhållning kan vara "minuter" eller "daglig". Kvarhållning för "veckovis", "månatliga" eller "årliga" säkerhets kopierings platser kallas långsiktig kvarhållning.

* Långsiktig kvarhållning:
  * Planerat (krav på efterlevnad) – om du vet i förväg att data krävs år från den aktuella tiden ska du använda långsiktig kvarhållning.
  * Oplanerat (krav på begäran) – om du inte vet i förväg kan du använda den på begäran med angivna inställningar för kvarhållning (dessa inställningar för anpassad kvarhållning påverkas inte av princip inställningar).

* Säkerhets kopiering på begäran med anpassad kvarhållning – om du behöver göra en säkerhets kopia som inte har schemalagts via säkerhets kopierings policyn kan du använda en säkerhets kopiering på begäran. Detta kan vara användbart för att säkerhetskopiera som inte passar din schemalagda säkerhets kopiering eller för att ta detaljerad säkerhets kopia (till exempel flera IaaS VM-säkerhetskopieringar per dag sedan schemalagd säkerhets kopiering tillåter endast en säkerhets kopiering per dag). Det är viktigt att Observera att bevarande principen som definierats i den schemalagda principen inte gäller för säkerhets kopiering på begäran.

### <a name="optimize-backup-policy"></a>Optimera säkerhets kopierings princip

* När dina affärs behov ändras kan du behöva förlänga eller minska Retentions tiden. När du gör det kan du förvänta följande:  
  * Om kvarhållning är utökad, markeras befintliga återställnings punkter i enlighet med den nya principen.
  * Om kvarhållning minskas markeras återställnings punkter för rensning i nästa rensnings jobb och tas sedan bort.
  * De senaste reglerna för kvarhållning gäller för alla kvarhållning punkter (exklusive kvarhållning på begäran). Så om kvarhållningsperioden är utökat (till exempel 100 dagar) och när säkerhets kopieringen utförs, följt av minskning av kvarhållning (till exempel från 100 dagar till sju dagar), kommer alla säkerhetskopierade data att behållas enligt den senaste angivna kvarhållningsperioden (det vill säga 7 dagar).

* Azure Backup ger dig flexibiliteten att *sluta skydda och hantera dina säkerhets kopior*:
  * *Stoppa skyddet och behåll säkerhets kopierings data*. Om du tar bort eller inaktiverar data källan (VM, program), men behöver spara data för granskning eller efterlevnad, kan du använda det här alternativet för att stoppa alla framtida säkerhets kopierings jobb från att skydda din data källa och behålla de återställnings punkter som har säkerhetskopierats. Du kan sedan återställa eller återuppta skyddet av virtuella datorer.
  * *Stoppa skyddet och ta bort säkerhetskopierade data*. Med det här alternativet stoppas alla framtida säkerhets kopierings jobb från att skydda din virtuella dator och ta bort alla återställnings punkter. Du kommer inte att kunna återställa den virtuella datorn eller använda alternativet återuppta säkerhets kopiering.

  * Om du återupptar skyddet (av en data källa som har stoppats med kvarhållna data) kommer reglerna för kvarhållning att gälla. Eventuella utgångna återställnings punkter tas bort (vid den schemalagda tiden).

* Innan du slutför princip designen är det viktigt att vara medveten om följande faktorer som kan påverka dina design alternativ.
  * En säkerhets kopierings princip är begränsad till ett valv.
  * Det finns en gräns för antalet objekt per princip (till exempel 100 VM: ar). För att skala kan du skapa dubbla principer med samma eller olika scheman.
  * Du kan inte ta bort vissa återställnings punkter selektivt.
  * Du kan inte helt inaktivera den schemalagda säkerhets kopieringen och hålla data källan i skyddat tillstånd. Den minst frekventa säkerhets kopiering som du kan konfigurera med principen är att ha en veckovis schemalagd säkerhets kopiering. Ett alternativ är att stoppa skyddet med kvarhållna data och att aktivera skydd varje gången du vill göra en säkerhets kopia, ta en säkerhets kopia på begäran och sedan inaktivera skyddet, men behålla säkerhetskopierade data. [Mer information finns här](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Säkerhetsöverväganden

För att hjälpa dig att skydda dina säkerhets kopierings data och uppfylla verksamhetens behov, ger Azure Backup konfidentialitet, integritet och tillgänglighets garantier mot avsiktlig attack och missbruk av värdefulla data och system. Tänk på följande säkerhets rikt linjer för din Azure Backup-lösning:

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

* Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering, uppdelning av uppgifter i teamet och ger endast åtkomst till de användare som krävs för att utföra sina jobb. [Mer information finns här](backup-rbac-rs-vault.md).

* Azure Backup innehåller tre inbyggda roller för att kontrol lera säkerhets kopierings hanterings åtgärder: säkerhets kopierings deltagare, operatörer och läsare. [Mer information finns här](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup har flera säkerhets kontroller som är inbyggda i tjänsten för att förhindra, identifiera och reagera på säkerhets risker (Läs mer)

* De lagrings konton som används av Recovery Services-valven är isolerade och kan inte användas av användare i något skadligt syfte. Åtkomst tillåts endast via Azure Backup hanterings åtgärder, till exempel Restore.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Kryptering av data vid överföring och i vila

Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

* I Azure skyddas data i överföring mellan Azure Storage och valvet av HTTPS. Dessa data finns kvar i Azure-nätverket.

* Säkerhetskopierade data krypteras automatiskt med Microsoft-hanterade nycklar. Du kan också använda egna nycklar, även kallade [kund hanterade nycklar](encryption-at-rest-with-cmk.md).

* Azure Backup stöder säkerhets kopiering och återställning av virtuella Azure-datorer som har sina operativ system/data diskar krypterade med Azure Disk Encryption (ADE). [Mer information finns här](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Skydd av säkerhets kopierings data från oavsiktliga borttagningar

Azure Backup innehåller säkerhetsfunktioner som hjälper dig att skydda säkerhets kopierings data även efter borttagning. Om en användare tar bort säkerhets kopian (från en virtuell dator, SQL Server databas, Azure-filresurs, SAP HANA databas) med mjuk borttagning, bevaras säkerhets kopierings data under 14 ytterligare dagar, vilket gör det möjligt att återställa säkerhets kopierings objekt utan data förlust. De ytterligare 14 dagars kvarhållning av säkerhets kopierings data i läget "mjuk borttagning" kostar inga kostnader för dig. [Mer information finns här](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Övervakning och aviseringar för misstänkt aktivitet

Azure Backup innehåller inbyggda övervaknings-och aviserings funktioner för att visa och konfigurera åtgärder för händelser relaterade till Azure Backup. [Mer information finns här](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Säkerhetsfunktioner som hjälper dig att skydda hybrid säkerhets kopieringar

Azure Backup tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att säkerhetskopiera och återställa filer, mappar och volym eller system tillstånd från en lokal dator till Azure. MARS innehåller nu säkerhetsfunktioner: en lösen fras som ska krypteras innan den laddas upp och dekrypteras efter nedladdning från Azure Backup, behålls borttagna säkerhets kopierings data under ytterligare 14 dagar från borttagnings datumet och kritisk åtgärd (t. ex. att ändra en lösen fras) kan bara utföras av användare som har giltiga autentiseringsuppgifter för Azure. [Mer information finns här](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Nätverksöverväganden

Azure Backup måste flytta data från din arbets belastning till Recovery Services-valvet. Azure Backup tillhandahåller flera funktioner för att skydda säkerhets kopierings data från att exponeras oavsiktligt (till exempel en man-in-the-Middle-attack i nätverket). Läs igenom följande riktlinjer:

### <a name="internet-connectivity"></a>Internetanslutning

* Virtuell *Azure-säkerhetskopiering* – all nödvändig kommunikation och data överföring mellan lagrings platsen och Azure Backup tjänsten sker i Azure-nätverket utan att du behöver komma åt ditt virtuella nätverk. Säkerhets kopiering av virtuella Azure-datorer i skyddade nätverk kräver inte att du tillåter åtkomst till några IP-adresser eller FQDN.

* *SAP HANA databaser på den virtuella Azure-datorn SQL Server databaser på Azure VM* – kräver anslutning till Azure Backup tjänst, Azure Storage och Azure Active Directory. Detta kan uppnås med hjälp av privata slut punkter eller genom att tillåta åtkomst till de offentliga IP-adresser eller FQDN: er som krävs. Att inte tillåta korrekt anslutning till de nödvändiga Azure-tjänsterna kan leda till fel i åtgärder som databas identifiering, konfigurering av säkerhets kopiering, säkerhets kopiering och återställning av data. En fullständig nätverks vägledning när du använder NSG-taggar, Azure-brandväggen och HTTP-proxy finns i dessa [SQL](backup-sql-server-database-azure-vms.md#establish-network-connectivity) -och [SAP HANA](./backup-azure-sap-hana-database.md#establish-network-connectivity) -artiklar.

* *Hybrid* -mars-agenten (Microsoft Azure Recovery Services) kräver nätverks åtkomst för alla kritiska åtgärder – installera, konfigurera, säkerhetskopiera och Återställ. MARS-agenten kan ansluta till Azure Backup tjänsten via [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) med hjälp av offentlig peering (tillgänglig för gamla kretsar) och Microsoft-peering, med hjälp av [privata slut punkter](install-mars-agent.md#private-endpoints) eller via [proxy/brand vägg med lämpliga åtkomst kontroller](install-mars-agent.md#verify-internet-access).

### <a name="private-endpoints-for-azure-backup"></a>Privata slut punkter för Azure Backup

Den [privata Azure-slutpunkten](../private-link/private-endpoint-overview.md) är ett nätverks gränssnitt som ansluter privat och säkert till en tjänst som drivs av en privat Azure-länk. Med Azure Backup kan du säkerhetskopiera och återställa data på ett säkert sätt från dina Recovery Services-valv med privata slut punkter.

* När du aktiverar privata slut punkter för valvet används de endast för säkerhets kopiering och återställning av SQL-och SAP HANA-arbetsbelastningar i en Azure VM-och MARS agent-säkerhetskopiering.  Du kan också använda valvet för säkerhets kopiering av andra arbets belastningar även (de kräver inte privata slut punkter). Förutom säkerhets kopian av SQL och SAP HANA arbets belastningar och säkerhets kopiering med MARS-agenten används även privata slut punkter för att utföra fil återställning när det gäller Azure VM-säkerhetskopiering. [Mer information finns här](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory stöder för närvarande inte privata slut punkter. Därför måste IP-adresser och FQDN: er som krävs för Azure Active Directory beviljas utgående åtkomst från det skyddade nätverket när säkerhets kopiering av databaser i virtuella Azure-datorer och säkerhets kopiering utförs med MARS-agenten. Du kan också använda NSG-Taggar och Azure Firewall-taggar för att tillåta åtkomst till Azure AD, efter vad som är tillämpligt. Läs mer om [kraven här](./private-endpoints.md#before-you-start).

## <a name="governance-considerations"></a>Saker att tänka på i samband med styrning

Styrning i Azure implementeras främst med [Azure policy](../governance/policy/overview.md) och [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). Med [Azure policy](../governance/policy/overview.md) kan du skapa, tilldela och hantera princip definitioner för att tillämpa regler för dina resurser. Den här funktionen håller resurserna i överensstämmelse med företagets standarder. Med [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kan du spåra moln användning och utgifter för dina Azure-resurser och andra moln leverantörer. Följande verktyg, till exempel Azure- [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) och [Azure Advisor](../advisor/advisor-overview.md)  , spelar också en viktig roll i kostnads hanterings processen.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup stöder två nyckel scenarier via inbyggda Azure Policy

* Se till att nyligen skapade affärs kritiska datorer säkerhets kopie ras automatiskt med rätt bevarande inställningar. Azure Backup tillhandahåller en inbyggd princip (med Azure Policy) som kan tilldelas till alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resurs grupp. När den här principen tilldelas ett angivet omfång konfigureras alla nya virtuella datorer i det omfånget automatiskt för säkerhets kopiering till ett befintligt valv på samma plats och i en prenumeration. Användaren kan ange valvet och den bevarande princip som de säkerhetskopierade virtuella datorerna ska associeras med. [Mer information finns här](backup-azure-auto-enable-backup.md).

* Se till att nyligen skapade valv har aktive rad diagnostik för att stödja rapporter. Ofta kan det vara en besvärlig uppgift att lägga till en diagnostisk inställning manuellt per valv. Dessutom måste ett nytt valv som skapats ha aktiverat diagnostikinställningar, så att du kan visa rapporter för det här valvet. För att förenkla skapandet av diagnostikinställningar i skala (med Log Analytics som mål) är Azure Backup en inbyggd Azure Policy. Den här principen lägger till en LA-diagnostisk inställning för alla valv i varje prenumeration eller resurs grupp. I följande avsnitt finns anvisningar om hur du använder den här principen. [Mer information finns här](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup kostnads överväganden

Den Azure Backup tjänstens funktioner ger flexibiliteten att effektivt hantera dina kostnader och fortfarande uppfylla din BCDR (verksamhets kontinuitet och haveri beredskap). Läs igenom följande riktlinjer:

* Använd pris Kalkylatorn för att utvärdera och optimera kostnader genom att justera olika Laffont. [Läs mer här](azure-backup-pricing.md)

* Backup Explorer: Använd säkerhets kopierings Utforskaren eller säkerhets kopierings rapporter för att förstå och optimera lagring av säkerhets kopior, stoppa säkerhets kopieringar för icke-kritiska arbets belastningar eller borttagna virtuella datorer Du kan få en sammanställd vy över hela din egendom från ett säkerhets kopierings perspektiv. Detta inkluderar inte bara information om dina säkerhets kopierings objekt, utan även resurser som inte har kon figurer ATS för säkerhets kopiering. Detta garanterar att du aldrig saknar att skydda viktiga data i din växande egendom och att dina säkerhets kopior är optimerade för icke-kritiska arbets belastningar eller borttagna arbets belastningar.

* Optimera säkerhets kopierings princip
  * Optimera inställningarna för schema och kvarhållning baserat på arbets Belastningens archetypes (till exempel verksamhets kritisk, icke-kritisk)
  * Optimera inställningarna för kvarhållning för omedelbar återställning
  * Välj rätt typ av säkerhets kopiering för att uppfylla kraven, samtidigt som de säkerhets kopierings typer som stöds (fullständig, stegvis, logg, differentiell) av arbets belastningen i Azure Backup beaktas.

* Selektiv säkerhets kopierings diskar: exkludera disk (förhands gransknings funktion) ger ett effektivt och kostnads effektivt alternativ för att säkerhetskopiera viktiga data selektivt. Säkerhetskopiera till exempel bara en disk när du inte vill säkerhetskopiera resten av diskarna som är anslutna till en virtuell dator. Detta är också användbart när du har flera säkerhets kopierings lösningar. Till exempel när du säkerhetskopierar dina databaser eller data med en lösning för säkerhets kopiering av arbets belastning (SQL Server databas i Azure VM backup) och du vill använda säkerhets kopiering av Azure VM-nivå för valda diskar.

* Azure Backup tar ögonblicks bilder av virtuella Azure-datorer och lagrar dem tillsammans med diskarna för att förbättra skapandet av återställnings punkter och påskynda återställnings åtgärder. Detta kallas för omedelbar återställning. Som standard behålls ögonblicks bilder av omedelbar återställning i två dagar. Med den här funktionen kan du utföra en återställning från dessa ögonblicks bilder genom att skära tillbaka återställnings tiderna. Det minskar den tid som krävs för att transformera och kopiera data tillbaka från valvet. Därför kan du se lagrings kostnader som motsvarar ögonblicks bilder som tas under den här perioden. [Mer information finns här](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup valvets typ av lagrings replikering är som standard inställd på Geo-redundant (GRS). Det här alternativet kan inte ändras efter att objekten skyddats. Geo-redundant lagring (GRS) ger en högre nivå av data hållbarhet än lokalt redundant lagring (LRS), vilket gör det möjligt att välja att använda återställning och kostnader över flera regioner. Granska kompromisserna mellan lägre kostnader och högre data hållbarhet och Bestäm vad som passar bäst för ditt scenario. [Läs mer här](backup-create-rs-vault.md#set-storage-redundancy)

* Om du skyddar både arbets belastningen som körs i en virtuell dator och den virtuella datorn kan du kontrol lera om det här dubbla skyddet behövs.

## <a name="monitoring-and-alerting-considerations"></a>Överväganden vid övervakning och avisering

Som säkerhets kopierings användare eller administratör bör du kunna övervaka alla säkerhets kopierings lösningar och få meddelanden om viktiga scenarier. Det här avsnittet innehåller information om de övervaknings-och aviserings funktioner som tillhandahålls av Azure Backups tjänsten.

### <a name="monitoring"></a>Övervakning

* Azure Backup tillhandahåller inbyggd **jobb övervakning** för åtgärder som att konfigurera säkerhets kopiering, säkerhets kopiering, återställning, borttagning av säkerhets kopior och så vidare. Detta är begränsat till valvet och idealiskt för övervakning av ett enda valv. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Om du behöver övervaka operativa aktiviteter i skala ger **Backup Explorer** en sammanställd vy över hela reserv fastigheten, vilket möjliggör detaljerad analys och fel sökning. Det är en inbyggd Azure Monitor arbets bok som ger en enkel, central plats som hjälper dig att övervaka operativa aktiviteter över hela reserv fastigheten på Azure, som omfattar klienter, platser, prenumerationer, resurs grupper och valv. [Mer information finns här](monitor-azure-backup-with-backup-explorer.md).
  * Använd den för att identifiera resurser som inte har kon figurer ATS för säkerhets kopiering och se till att du inte någonsin kommer att skydda viktiga data i din växande egendom.
  * Instrument panelen tillhandahåller operativa aktiviteter under de senaste sju dagarna (max). Om du behöver behålla dessa data kan du exportera dem som en Excel-fil och spara dem.
  * Om du är en Azure Lighthouse-användare kan du Visa information över flera klienter, vilket möjliggör en mer begränsande övervakning.

* Om du behöver behålla och Visa operativa aktiviteter för långsiktigt använder du **rapporter**. Ett vanligt krav för säkerhets kopierings administratörer är att få insikter om säkerhets kopieringar baserat på data som omfattar en längre tids period. Användnings fall för sådan lösning är:
  * Allokering och Prognosticering av moln lagring förbrukat.
  * Granskning av säkerhets kopiering och återställning.
  * Identifiera viktiga trender på olika nivåer av granularitet.

* Dessutom ger
  * Du kan skicka data (till exempel jobb, principer och så vidare) till arbets ytan **Log Analytics** . Detta aktiverar funktionerna i Azure Monitor loggar för att möjliggöra korrelation av data med andra övervaknings data som samlas in av Azure Monitor, konsolidera logg poster från flera Azure-prenumerationer och-klienter till en enda plats för analys, använda logg frågor för att utföra komplexa analyser och få djupgående insikter om logg poster. [Mer information finns här](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace).
  * Du kan skicka data till Händelsehubben för att skicka poster utanför Azure, till exempel en SIEM (säkerhets information och händelse hantering) eller en annan Log Analytics-lösning. [Mer information finns här](../azure-monitor/platform/activity-log.md#send-to-azure-event-hubs).
  * Du kan skicka data till ett Azure Storage-konto om du vill behålla dina loggdata längre än 90 dagar för granskning, statisk analys eller säkerhets kopiering. Om du bara behöver behålla dina händelser i 90 dagar eller mindre, behöver du inte skapa arkiv till ett lagrings konto, eftersom aktivitets logg händelser lagras i Azure-plattformen i 90 dagar. [Läs mer](../azure-monitor/platform/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Aviseringar

* Aviseringar är i första hand ett sätt att komma fram till att vidta relevanta åtgärder. Avsnittet om säkerhets kopierings aviseringar visar aviseringar som genererats av tjänsten Azure Backup.

* Azure Backup innehåller en inbyggd **aviserings funktion för aviseringar** via e-post för fel, varningar och kritiska åtgärder. Du kan ange enskilda e-postadresser eller distributions listor som ska meddelas när en avisering genereras. Du kan också välja om du vill bli meddelad om varje enskild avisering eller gruppera dem i en Tim sammandrag och sedan få ett meddelande.
  * De här aviseringarna definieras av tjänsten och ger stöd för begränsade scenarier – säkerhets kopierings-/återställnings fel, stoppa skyddet med Behåll data/stoppa skydd med ta bort data och så vidare. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Om en destruktiv åtgärd, till exempel stoppa skydd med ta bort data, görs, aktive ras en avisering och ett e-postmeddelande skickas till prenumerations ägare, administratörer och medadministratörer även om meddelanden **inte** har kon figurer ats för Recovery Services valvet.
  * Vissa arbets belastningar kan generera höga frekvenser av haverier (till exempel SQL Server var 15: e minut). För att förhindra att aviseringar blir överbelastade för varje förekomst av händelsen, konsol IDE ras aviseringarna. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * De inbyggda aviseringarna kan inte anpassas och är begränsade till e-postmeddelanden som definierats i Azure Portal.

* Om du behöver **skapa anpassade aviseringar** (till exempel aviseringar om lyckade jobb) använder du Log Analytics. I Azure Monitor kan du skapa egna aviseringar i en Log Analytics arbets yta. Hybrid arbets belastningar (DPM/MABS) kan också skicka data till LA och använda LA för att tillhandahålla vanliga aviseringar över arbets belastningar som stöds av Azure Backup.

* Du kan också få aviseringar via inbyggda Recovery Services valv **aktivitets loggar**. Det stöder dock begränsade scenarier och är inte lämpligt för åtgärder som schemalagd säkerhets kopiering, som justeras bättre med resurs loggar än med aktivitets loggar. Om du vill veta mer om de här begränsningarna och hur du kan använda Log Analytics arbets yta för övervakning och avisering i skala för alla arbets belastningar som skyddas av Azure Backup, se den här [artikeln](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du läser följande artiklar som start punkter för att använda Azure Backup:

* [Översikt över Azure Backup](backup-overview.md)
* [Vanliga frågor och svar](backup-azure-backup-faq.md)
